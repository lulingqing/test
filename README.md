package com.example.demo.bean;

public interface IItemBean {
	boolean isOutput();
}

package com.example.demo.bean;

public class AbstractItemBean implements IItemBean {

	private boolean isOutput = true;
	
	public AbstractItemBean( ) {
		//Doing nothing
	}
	
	@Override
	public boolean isOutput() {
		return isOutput;
	}

	public void setOutput(boolean isOutput) {
		this.isOutput = isOutput;
	}
	
	protected String normalizeValue(String value) {
		if(value == null || value.isEmpty()) {
			return null;
		}
		return value;
	}
}




package com.example.demo.config;

import java.io.File;
import java.nio.charset.Charset;

import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.configuration.annotation.JobBuilderFactory;
import org.springframework.batch.core.configuration.annotation.StepBuilderFactory;
import org.springframework.batch.core.configuration.annotation.StepScope;
import org.springframework.batch.item.database.JdbcCursorItemReader;
import org.springframework.batch.item.database.JpaItemWriter;
import org.springframework.batch.item.file.DefaultBufferedReaderFactory;
import org.springframework.batch.item.file.FlatFileItemReader;
import org.springframework.batch.item.file.FlatFileItemWriter;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Qualifier;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.autoconfigure.condition.ConditionalOnBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Lazy;
import org.springframework.core.io.FileSystemResource;
import org.springframework.batch.item.file.builder.FlatFileItemReaderBuilder;
import com.example.demo.bean.InputCsvBean;
import com.example.demo.bean.InputDatBean;
import com.example.demo.bean.OutputFileItem;
import com.example.demo.bean.OutputTableItem;
import com.example.demo.globalbean.GlobalBean;
import com.example.demo.header.ReportHeaderCallback;
import com.example.demo.jobstep.mapper.CsvFileMapper;
import com.example.demo.jobstep.mapper.DatFileMapper;
import com.example.demo.jobstep.processor.CsvFileItemProcessor;
import com.example.demo.jobstep.tokenizer.FixedByteLengthLineTokenizer;
import com.example.demo.jobstep.writer.OutputFileMapWriter;

import org.springframework.batch.item.file.builder.FlatFileItemWriterBuilder;
import org.springframework.batch.item.file.mapping.DefaultLineMapper;
import org.springframework.batch.item.database.builder.JpaItemWriterBuilder;
import org.springframework.batch.item.database.builder.JdbcCursorItemReaderBuilder;
import com.example.demo.jobstep.mapper.OutputTableMapper;
import com.example.demo.converter.OutputFileMapConverter;

@Configuration
@ConditionalOnBean(name="jobNameMyBatchJob")
public class Batch01MyBatchConfiguration {
	private static final Logger logger = LoggerFactory.getLogger(Batch01MyBatchConfiguration.class);
	
	@Autowired
	private GlobalBean globalBean;
	
	@Value("${input.folder}")
	private String inputFolder;
	
	@Value("${output.folder}")
	private String outputFolder;
	
	@Value("${backup.folder}")
	private String backupFolder;
	
	@Value("${log.folder}")
	private String logFolder;
	
	@Value("${input.csv.file}")
	private String inputCsvFile;
	
	@Value("${input.dat.file}")
	private String inputDatFile;
	
	@Value("${output.dat.file")
	private String outputFile;
	
	@Autowired
	@Qualifier("entityManagerFactory")
	EntityManagerFactory entityManagerFactory;

	@Bean
	@Lazy
	public Job myBatchJob(JobBuilderFactory jobBuilderFactory) {
		logger.info("myBatchJob Start!");
		
		return jobBuilderFactory.get("myBatchJob")
				.start(csvFileIPOStep(null))
				.build();
	}
	
	@Bean
	@Lazy
	public Step csvFileIPOStep(StepBuilderFactory stepBuilderFactory) {
		logger.info("csvFileIPOStep");
		
		return stepBuilderFactory.get("csvFileIPOStep")
				.<InputCsvBean,OutputFileItem>chunk(100)
				.reader(inputCsvFileReader())
				.processor(csvFileItemProcessor())
				.writer(outputFileItem())
				.build();
	}
	
	@Bean
	@Lazy
	@StepScope
	public FlatFileItemReader<InputCsvBean> inputCsvFileReader() {
		logger.info("inputCsvFileReader Start!");
		
		String filePath = inputFolder + File.separator + inputCsvFile;
		
		return new FlatFileItemReaderBuilder<InputCsvBean>()
				.name("inputCsvFileReader")
				.resource(new FileSystemResource(filePath))
				.delimited()
				.delimiter(",")
				.names(InputCsvBean.getTitiles())
				.fieldSetMapper(new CsvFileMapper())
				.encoding(Charset.availableCharsets().get("Shift_JIS").name())
				.linesToSkip(1)
				.strict(true)
				.build();
	}
	
	@Bean
	@Lazy
	@StepScope
	public CsvFileItemProcessor csvFileItemProcessor() {
		logger.info("csvFileItemProcessor start");
		
		return new CsvFileItemProcessor();
	}
	
	@Bean
	@Lazy
	@StepScope
	public FlatFileItemWriter<OutputFileItem> outputFileItem() {
		logger.info("outputFileItem Start!");
		
		String filePath = outputFolder + File.separator + outputFile;
		
		return new FlatFileItemWriterBuilder<OutputFileItem>()
				.name("outputFileItem")
				.resource(new FileSystemResource(filePath))
				.delimited()
				.delimiter("|")
				.names(OutputFileItem.getTitiles())
				.append(false)
				.headerCallback(new ReportHeaderCallback(OutputFileItem.getTitiles()))
				.shouldDeleteIfEmpty(true)
				.lineSeparator("\n")
				.build();
	}
	
	@Bean
	@Lazy
	@StepScope
	public FlatFileItemReader<InputDatBean> inputDatFileReader() {
		logger.info("inputDatFileReader Start!");
		
		String filePath = inputFolder + File.separator + inputDatFile;
		
		FixedByteLengthLineTokenizer tokenizer = new FixedByteLengthLineTokenizer(InputDatBean.getRanges(),
				Charset.availableCharsets().get("Shift_JIS"));
		tokenizer.setNames(InputDatBean.getTitiles());
		
		DefaultLineMapper<InputDatBean> lineMapper = new DefaultLineMapper<>();
		lineMapper.setLineTokenizer(tokenizer);
		lineMapper.setFieldSetMapper(new DatFileMapper());
		
		return new FlatFileItemReaderBuilder<InputDatBean>()
					.name("inputDatFileReader")
					.bufferedReaderFactory(new DefaultBufferedReaderFactory())
					.resource(new FileSystemResource(filePath))
					.lineMapper(lineMapper)
					.encoding(Charset.availableCharsets().get("Shift_JIS").name())
					.strict(true)
					.build();
	}
	
	@Bean
	@Lazy
	@StepScope
	public JpaItemWriter<OutputTableItem> outputTableItemWriter() {
		return new JpaItemWriterBuilder<OutputTableItem>()
				.entityManagerFactory(entityManagerFactory)
				.build();
	}
	
	@Bean
	@Lazy
	@StepScope
	public JdbcCursorItemReader<OutputTableItem> outputJdbcCursorItemReader(DataSource datasource, String businessDate) {
		StringBuilder sqlString = new StringBuilder();
		
		sqlString.append("Select * FROM MY_BATCH_TABLE ");
		sqlString.append("WHERE getDate() < '");
		sqlString.append(businessDate);
		sqlString.append("'");
		
		return new JdbcCursorItemReaderBuilder<OutputTableItem>()
				.dataSource(datasource)
				.name("outputJdbcCursorItemReader")
				.sql(sqlString.toString())
				.rowMapper(new OutputTableMapper())
				.build();
	}
	
	@Bean
	@Lazy
	@StepScope
	public OutputFileMapWriter outputFileMapWriter() {
		OutputFileMapWriter writer = new OutputFileMapWriter();
		writer.setDelete(false);
		writer.setItemKeyMapper(new OutputFileMapConverter());
		writer.setOutputFileMap(globalBean.getOutputFileMap());
		
		return writer;
	}
	
	
}
