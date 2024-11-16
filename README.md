package org.springframework.batch.item.file.builder;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;
import java.util.Locale;
import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.batch.item.file.FlatFileFooterCallback;
import org.springframework.batch.item.file.FlatFileHeaderCallback;
import org.springframework.batch.item.file.FlatFileItemWriter;
import org.springframework.batch.item.file.transform.BeanWrapperFieldExtractor;
import org.springframework.batch.item.file.transform.DelimitedLineAggregator;
import org.springframework.batch.item.file.transform.FieldExtractor;
import org.springframework.batch.item.file.transform.FormatterLineAggregator;
import org.springframework.batch.item.file.transform.LineAggregator;
import org.springframework.core.io.Resource;
import org.springframework.util.Assert;

public class FlatFileItemWriterBuilder<T> {
	protected Log logger = LogFactory.getLog(this.getClass());
	private Resource resource;
	private boolean forceSync = false;
	private String lineSeparator;
	private LineAggregator<T> lineAggregator;
	private String encoding;
	private boolean shouldDeleteIfExists;
	private boolean append;
	private boolean shouldDeleteIfEmpty;
	private FlatFileHeaderCallback headerCallback;
	private FlatFileFooterCallback footerCallback;
	private boolean transactional;
	private boolean saveState;
	private String name;
	private DelimitedBuilder<T> delimitedBuilder;
	private FormattedBuilder<T> formattedBuilder;

	public FlatFileItemWriterBuilder() {
		this.lineSeparator = FlatFileItemWriter.DEFAULT_LINE_SEPARATOR;
		this.encoding = "UTF-8";
		this.shouldDeleteIfExists = true;
		this.append = false;
		this.shouldDeleteIfEmpty = false;
		this.transactional = true;
		this.saveState = true;
	}

	public FlatFileItemWriterBuilder<T> saveState(boolean saveState) {
		this.saveState = saveState;
		return this;
	}

	public FlatFileItemWriterBuilder<T> name(String name) {
		this.name = name;
		return this;
	}

	public FlatFileItemWriterBuilder<T> resource(Resource resource) {
		this.resource = resource;
		return this;
	}

	public FlatFileItemWriterBuilder<T> forceSync(boolean forceSync) {
		this.forceSync = forceSync;
		return this;
	}

	public FlatFileItemWriterBuilder<T> lineSeparator(String lineSeparator) {
		this.lineSeparator = lineSeparator;
		return this;
	}

	public FlatFileItemWriterBuilder<T> lineAggregator(LineAggregator<T> lineAggregator) {
		this.lineAggregator = lineAggregator;
		return this;
	}

	public FlatFileItemWriterBuilder<T> encoding(String encoding) {
		this.encoding = encoding;
		return this;
	}

	public FlatFileItemWriterBuilder<T> shouldDeleteIfEmpty(boolean shouldDelete) {
		this.shouldDeleteIfEmpty = shouldDelete;
		return this;
	}

	public FlatFileItemWriterBuilder<T> shouldDeleteIfExists(boolean shouldDelete) {
		this.shouldDeleteIfExists = shouldDelete;
		return this;
	}

	public FlatFileItemWriterBuilder<T> append(boolean append) {
		this.append = append;
		return this;
	}

	public FlatFileItemWriterBuilder<T> headerCallback(FlatFileHeaderCallback callback) {
		this.headerCallback = callback;
		return this;
	}

	public FlatFileItemWriterBuilder<T> footerCallback(FlatFileFooterCallback callback) {
		this.footerCallback = callback;
		return this;
	}

	public FlatFileItemWriterBuilder<T> transactional(boolean transactional) {
		this.transactional = transactional;
		return this;
	}

	public DelimitedBuilder<T> delimited() {
		this.delimitedBuilder = new DelimitedBuilder(this);
		return this.delimitedBuilder;
	}

	public FormattedBuilder<T> formatted() {
		this.formattedBuilder = new FormattedBuilder(this);
		return this.formattedBuilder;
	}

	public FlatFileItemWriter<T> build() {
		Assert.isTrue(this.lineAggregator != null || this.delimitedBuilder != null || this.formattedBuilder != null,
				"A LineAggregator or a DelimitedBuilder or a FormattedBuilder is required");
		if (this.saveState) {
			Assert.hasText(this.name, "A name is required when saveState is true");
		}

		if (this.resource == null) {
			this.logger.debug(
					"The resource is null. This is only a valid scenario when injecting it later as in when using the MultiResourceItemWriter");
		}

		FlatFileItemWriter<T> writer = new FlatFileItemWriter();
		writer.setName(this.name);
		writer.setAppendAllowed(this.append);
		writer.setEncoding(this.encoding);
		writer.setFooterCallback(this.footerCallback);
		writer.setForceSync(this.forceSync);
		writer.setHeaderCallback(this.headerCallback);
		if (this.lineAggregator == null) {
			Assert.state(this.delimitedBuilder == null || this.formattedBuilder == null,
					"Either a DelimitedLineAggregator or a FormatterLineAggregator should be provided, but not both");
			if (this.delimitedBuilder != null) {
				this.lineAggregator = this.delimitedBuilder.build();
			} else {
				this.lineAggregator = this.formattedBuilder.build();
			}
		}

		writer.setLineAggregator(this.lineAggregator);
		writer.setLineSeparator(this.lineSeparator);
		writer.setResource(this.resource);
		writer.setSaveState(this.saveState);
		writer.setShouldDeleteIfEmpty(this.shouldDeleteIfEmpty);
		writer.setShouldDeleteIfExists(this.shouldDeleteIfExists);
		writer.setTransactional(this.transactional);
		return writer;
	}

	public static class DelimitedBuilder<T> {
		private FlatFileItemWriterBuilder<T> parent;
		private List<String> names = new ArrayList();
		private String delimiter = ",";
		private FieldExtractor<T> fieldExtractor;

		protected DelimitedBuilder(FlatFileItemWriterBuilder<T> parent) {
			this.parent = parent;
		}

		public DelimitedBuilder<T> delimiter(String delimiter) {
			this.delimiter = delimiter;
			return this;
		}

		public FlatFileItemWriterBuilder<T> names(String... names) {
			this.names.addAll(Arrays.asList(names));
			return this.parent;
		}

		public FlatFileItemWriterBuilder<T> fieldExtractor(FieldExtractor<T> fieldExtractor) {
			this.fieldExtractor = fieldExtractor;
			return this.parent;
		}

		public DelimitedLineAggregator<T> build() {
			Assert.isTrue(this.names != null && !this.names.isEmpty() || this.fieldExtractor != null,
					"A list of field names or a field extractor is required");
			DelimitedLineAggregator<T> delimitedLineAggregator = new DelimitedLineAggregator();
			if (this.delimiter != null) {
				delimitedLineAggregator.setDelimiter(this.delimiter);
			}

			if (this.fieldExtractor == null) {
				BeanWrapperFieldExtractor<T> beanWrapperFieldExtractor = new BeanWrapperFieldExtractor();
				beanWrapperFieldExtractor.setNames((String[]) this.names.toArray(new String[this.names.size()]));

				try {
					beanWrapperFieldExtractor.afterPropertiesSet();
				} catch (Exception var4) {
					throw new IllegalStateException("Unable to initialize DelimitedLineAggregator", var4);
				}

				this.fieldExtractor = beanWrapperFieldExtractor;
			}

			delimitedLineAggregator.setFieldExtractor(this.fieldExtractor);
			return delimitedLineAggregator;
		}
	}

	public static class FormattedBuilder<T> {
		private FlatFileItemWriterBuilder<T> parent;
		private String format;
		private Locale locale = Locale.getDefault();
		private int maximumLength = 0;
		private int minimumLength = 0;
		private FieldExtractor<T> fieldExtractor;
		private List<String> names = new ArrayList();

		protected FormattedBuilder(FlatFileItemWriterBuilder<T> parent) {
			this.parent = parent;
		}

		public FormattedBuilder<T> format(String format) {
			this.format = format;
			return this;
		}

		public FormattedBuilder<T> locale(Locale locale) {
			this.locale = locale;
			return this;
		}

		public FormattedBuilder<T> minimumLength(int minimumLength) {
			this.minimumLength = minimumLength;
			return this;
		}

		public FormattedBuilder<T> maximumLength(int maximumLength) {
			this.maximumLength = maximumLength;
			return this;
		}

		public FlatFileItemWriterBuilder<T> fieldExtractor(FieldExtractor<T> fieldExtractor) {
			this.fieldExtractor = fieldExtractor;
			return this.parent;
		}

		public FlatFileItemWriterBuilder<T> names(String... names) {
			this.names.addAll(Arrays.asList(names));
			return this.parent;
		}

		public FormatterLineAggregator<T> build() {
			Assert.notNull(this.format, "A format is required");
			Assert.isTrue(this.names != null && !this.names.isEmpty() || this.fieldExtractor != null,
					"A list of field names or a field extractor is required");
			FormatterLineAggregator<T> formatterLineAggregator = new FormatterLineAggregator();
			formatterLineAggregator.setFormat(this.format);
			formatterLineAggregator.setLocale(this.locale);
			formatterLineAggregator.setMinimumLength(this.minimumLength);
			formatterLineAggregator.setMaximumLength(this.maximumLength);
			if (this.fieldExtractor == null) {
				BeanWrapperFieldExtractor<T> beanWrapperFieldExtractor = new BeanWrapperFieldExtractor();
				beanWrapperFieldExtractor.setNames((String[]) this.names.toArray(new String[this.names.size()]));

				try {
					beanWrapperFieldExtractor.afterPropertiesSet();
				} catch (Exception var4) {
					throw new IllegalStateException("Unable to initialize FormatterLineAggregator", var4);
				}

				this.fieldExtractor = beanWrapperFieldExtractor;
			}

			formatterLineAggregator.setFieldExtractor(this.fieldExtractor);
			return formatterLineAggregator;
		}
	}
}
