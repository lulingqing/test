package com.example.demo.log.dao;

import org.springframework.batch.core.repository.dao.ExecutionContextDao;
import org.springframework.batch.core.repository.dao.JobExecutionDao;
import org.springframework.batch.core.repository.dao.JobInstanceDao;
import org.springframework.batch.core.repository.dao.StepExecutionDao;
import org.springframework.batch.core.repository.support.AbstractJobRepositoryFactoryBean;
import org.springframework.batch.support.transaction.ResourcelessTransactionManager;
import org.springframework.transaction.PlatformTransactionManager;

public class FileJobRepositoryFactoryBean extends AbstractJobRepositoryFactoryBean{

	private FileJobExecutionDao jobExecutionDao;
	private FileJobInstanceDao jobInstanceDao;
	private FileStepExecutionDao stepExecutionDao;
	private FileExecutionContextDao executionContextDao;
	
	public FileJobRepositoryFactoryBean() {
		this(new ResourcelessTransactionManager());
	}
	
	public FileJobRepositoryFactoryBean(PlatformTransactionManager transactionManager) {
		setTransactionManager(transactionManager);
	}

	public JobExecutionDao getJobExecutionDao() {
		return jobExecutionDao;
	}
	
	public JobInstanceDao getJobInstanceDao() {
		return jobInstanceDao;
	}
	
	public StepExecutionDao getStepExecutionDao() {
		return stepExecutionDao;
	}
	
	public ExecutionContextDao getExecutionContextDao() {
		return executionContextDao;
	}
	
	public void clear() {
		jobInstanceDao.clear();
		jobExecutionDao.clear();
		stepExecutionDao.clear();
		executionContextDao.clear();
	}
	
	@Override
	protected JobInstanceDao createJobInstanceDao() throws Exception {
		jobInstanceDao = new FileJobInstanceDao();
		return jobInstanceDao;
	}

	@Override
	protected JobExecutionDao createJobExecutionDao() throws Exception {
		jobExecutionDao = new FileJobExecutionDao();
		return jobExecutionDao;
	}

	@Override
	protected StepExecutionDao createStepExecutionDao() throws Exception {
		stepExecutionDao = new FileStepExecutionDao();
		return stepExecutionDao;
	}

	@Override
	protected ExecutionContextDao createExecutionContextDao() throws Exception {
		executionContextDao = new FileExecutionContextDao();
		return executionContextDao;
	}

}
