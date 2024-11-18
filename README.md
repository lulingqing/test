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

 package com.example.demo.jobstep.writer;

import java.util.Map;

import org.springframework.batch.item.KeyValueItemWriter;
import org.springframework.util.Assert;

import com.example.demo.bean.OutputFileItem;

import jakarta.annotation.PostConstruct;

public class OutputFileMapWriter extends KeyValueItemWriter<String, OutputFileItem> {

	public Map<String, OutputFileItem> outputFileMap;
	
	public void setOutputFileMap(Map<String, OutputFileItem> outputFileMap) {
		this.outputFileMap = outputFileMap;
	}
	
	@Override
	protected void writeKeyValue(String key, OutputFileItem value) {
		if(delete) {
			outputFileMap.remove(key);
		} else {
			outputFileMap.put(key, value);
		}
	}

	@Override
	@PostConstruct
	protected void init() {
		Assert.notNull(outputFileMap, "A outputFileMap is required.");
	}

}
