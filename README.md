package com.example.demo.log.dao;

import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.StepExecution;
import org.springframework.batch.core.repository.dao.MapExecutionContextDao;

public class FileExecutionContextDao extends MapExecutionContextDao{

	public void updateExecutionContext(StepExecution stepExecution) {
		super.updateExecutionContext(stepExecution);
		FileDaoLoggerSingleton.getInstance().logInfo(stepExecution);
	}
	
	public void updateExecutionContext(JobExecution jobExecution) {
		super.updateExecutionContext(jobExecution);
		FileDaoLoggerSingleton.getInstance().logInfo(jobExecution);
	}
	
}
