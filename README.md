package com.example.demo.log.dao;

import javax.annotation.concurrent.ThreadSafe;
import org.slf4j.*;
import org.springframework.batch.core.JobExecution;
import org.springframework.batch.core.StepExecution;

@ThreadSafe
public class FileDaoLoggerSingleton {
	private static final Logger logger = LoggerFactory.getLogger(FileDaoLoggerSingleton.class);
	private static FileDaoLoggerSingleton singleton = null;
	
	public FileDaoLoggerSingleton() {
		// nothing
	}
	
	public static synchronized FileDaoLoggerSingleton getInstance() {
		if(singleton == null) {
			singleton = new FileDaoLoggerSingleton();
		}
		return singleton;
	}
	
	public void logInfo(JobExecution jobExecution) {
		StringBuilder info = new StringBuilder();
		info.append("JobName=[%s],");
		info.append("Status=[%s]");
		
		String logInfo = String.format(info.toString(), 
				normalize(jobExecution.getJobInstance().getJobName()),
				normalize(jobExecution.getStatus()));
		
		logger.info("-------------------------------------------------------------");
		logger.info(logInfo);
	}
	
	public void logInfo(StepExecution stepExecution) {
		// Logをきれいにするため、処理件数が全て0である場合、件数出力を省略する
		long readCount, processSkipCount,writeCount;
		readCount = stepExecution.getReadCount();
		processSkipCount = stepExecution.getProcessSkipCount();
		writeCount = stepExecution.getWriteCount();
		
		StringBuilder info = new StringBuilder();
		String logInfo = null;
		if(readCount == 0 && processSkipCount == 0 && writeCount == 0) {
			info.append("Id=[%s],");
			info.append("StepName=[%s]");
			
			logInfo = String.format(info.toString(), 
					normalize(stepExecution.getId().toString()),
					normalize(stepExecution.getStepName()));
		} else {
			info.append("Id=[%s],");
			info.append("StepName=[%s],");
			info.append("readCount=[%s],");
			info.append("processSkipCount=[%s],");
			info.append("writeCount=[%s]");
			
			logInfo = String.format(info.toString(),
					normalize(stepExecution.getId().toString()),
					normalize(stepExecution.getStepName()),
					normalize(readCount),
					normalize(processSkipCount),
					normalize(writeCount));
		}
		
		logger.info(logInfo);	
	}
	
	public void logInfo(String msg) {
		logger.info(msg);
	}
	
	public void info(String format, Object...arguments) {
		logger.info(format, arguments);
	}
	
	public void logDebug(String msg) {
		logger.info(msg);
	}
	
	public void logError(String msg) {
		logger.info(msg);
	}
	
	public void logWarn(String msg) {
		logger.info(msg);
	}
		
	private String normalize(Object object) {
		if(object == null) {
			return "NULL";
		}
		return object.toString();
	}
}
