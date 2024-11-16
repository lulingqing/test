package com.example.demo.config;

import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

import javax.persistence.EntityManagerFactory;
import javax.sql.DataSource;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.batch.core.configuration.annotation.EnableBatchProcessing;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.Lazy;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.transaction.PlatformTransactionManager;
import org.springframework.transaction.annotation.EnableTransactionManagement;
import org.springframework.orm.jpa.JpaTransactionManager;

@Configuration
@EnableAutoConfiguration
@EnableTransactionManagement
@EnableBatchProcessing(modular = true)
public class BatchMainConfiguration {
	private static final Logger logger = LoggerFactory.getLogger(BatchMainConfiguration.class);
	
	@Bean
	protected String systemEnv() {
		String env = System.getenv("SYSTEM_ENV");
		logger.info("SYSTEM_ENV-[" + env + "]");
		return env;
	}
	
	@Bean
	protected String businessDate() {
		LocalDateTime dateTime = LocalDateTime.now();
		DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd");
		return dateTime.format(formatter);
	}
	
	@Bean
	protected String businessDateTime() {
		LocalDateTime dateTime = LocalDateTime.now();
		DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd_HHmm");
		return dateTime.format(formatter);
	}
	
	@Bean
	@Lazy
	public MyBatchConfigurer myBatchConfigurer() {
		return new MyBatchConfigurer();
	}
	
	@Bean
	public JdbcTemplate jdbcTemplate(DataSource dataSource) {
		return new JdbcTemplate(dataSource);
	}
	
	@Bean
	public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
		JpaTransactionManager jpaTransactionManager = new JpaTransactionManager();
		jpaTransactionManager.setEntityManagerFactory(null);
		return jpaTransactionManager;
	}
}
