package com.example.demo.log.dao;

import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.repository.dao.MapJobExecutionDao;

public class FileJobExecutionDao extends MapJobExecutionDao {
	
	public void saveJobExecution(JobExecution jobExecution) {
		super.saveJobExecution(jobExecution);
	}
	
	public void updateJobExecution(JobExecution jobExecution) {
		super.updateJobExecution(jobExecution);
	}
}
