package com.example.demo.config;

import javax.sql.DataSource;
import org.springframework.batch.core.configuration.annotation.DefaultBatchConfigurer;
import org.springframework.batch.core.explore.JobExplorer;
import org.springframework.batch.core.repository.JobRepository;
import com.example.demo.log.dao.FileJobRepositoryFactoryBean;
import com.example.demo.log.dao.FileJobExplorerFactoryBean;

public class MyBatchConfigurer extends DefaultBatchConfigurer{

	public MyBatchConfigurer() {
		//Doing nothing
	}
	
	public MyBatchConfigurer(DataSource dataSource) {
		super(dataSource);
	}
	
	private FileJobRepositoryFactoryBean jobRepositoryFactory;
	
	@Override
	protected JobRepository createJobRepository() throws Exception {
		jobRepositoryFactory = new FileJobRepositoryFactoryBean(super.getTransactionManager());
		jobRepositoryFactory.afterPropertiesSet();
		return jobRepositoryFactory.getObject();
	}
	
	@Override
	protected JobExplorer createJobExplorer() throws Exception {
		FileJobExplorerFactoryBean jobExplorerFactory = new FileJobExplorerFactoryBean(jobRepositoryFactory);
		jobExplorerFactory.afterPropertiesSet();
		return jobExplorerFactory.getObject();
	}
	
}
