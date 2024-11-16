package com.example.demo.log.dao;

import org.springframework.batch.core.JobInstance;
import org.springframework.batch.core.JobParameters;
import org.springframework.batch.core.repository.dao.MapJobInstanceDao;

public class FileJobInstanceDao extends MapJobInstanceDao{
	
	public JobInstance createJobInstance(String jobName, JobParameters jobParameters) {
		JobInstance jobInstance = super.createJobInstance(jobName, jobParameters);
		FileDaoLoggerSingleton.getInstance().logInfo(jobInstance.toString());
		return jobInstance;
	}

}
