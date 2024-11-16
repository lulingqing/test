package com.example.demo.log.dao;

import org.springframework.batch.core.StepExecution;
import org.springframework.batch.core.repository.dao.MapStepExecutionDao;

public class FileStepExecutionDao extends MapStepExecutionDao{
	
	public void saveStepExecution(StepExecution stepExecution) {
		super.saveStepExecution(stepExecution);
	}

}
