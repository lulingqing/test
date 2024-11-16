package com.example.demo.log.dao;

import org.springframework.batch.core.explore.JobExplorer;
import org.springframework.batch.core.explore.support.AbstractJobExplorerFactoryBean;
import org.springframework.batch.core.explore.support.SimpleJobExplorer;
import org.springframework.batch.core.repository.dao.ExecutionContextDao;
import org.springframework.batch.core.repository.dao.JobExecutionDao;
import org.springframework.batch.core.repository.dao.JobInstanceDao;
import org.springframework.batch.core.repository.dao.StepExecutionDao;
import org.springframework.beans.factory.InitializingBean;
import org.springframework.util.Assert;

public class FileJobExplorerFactoryBean extends AbstractJobExplorerFactoryBean implements InitializingBean {

	private FileJobRepositoryFactoryBean repositoryFactory;

	public FileJobExplorerFactoryBean() {
		// Doing nothing
	}
	
	public FileJobExplorerFactoryBean(FileJobRepositoryFactoryBean repositoryFactory) {
		this.repositoryFactory = repositoryFactory;
	}
	
	public void setRepositoryFactory(FileJobRepositoryFactoryBean repositoryFactory) {
		this.repositoryFactory = repositoryFactory;
	}
	
	
 
	@Override
	public JobExplorer getObject() throws Exception {
		return new SimpleJobExplorer(createJobInstanceDao(), createJobExecutionDao(), 
					createStepExecutionDao(), createExecutionContextDao());
	}

	@Override
	public void afterPropertiesSet() throws Exception {
		Assert.state(repositoryFactory != null, "A FileJobRepsotiryBean must be provided.");
		repositoryFactory.afterPropertiesSet();
	}

	@Override
	protected JobInstanceDao createJobInstanceDao() throws Exception {
		return repositoryFactory.getJobInstanceDao();
	}

	@Override
	protected JobExecutionDao createJobExecutionDao() throws Exception {
		return repositoryFactory.getJobExecutionDao();
	}

	@Override
	protected StepExecutionDao createStepExecutionDao() throws Exception {
		return repositoryFactory.getStepExecutionDao();
	}

	@Override
	protected ExecutionContextDao createExecutionContextDao() throws Exception {
		return repositoryFactory.getExecutionContextDao();
	}
}
