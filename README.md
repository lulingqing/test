package com.example.demo.jobstep.writer;

import java.util.Map;

import org.springframework.batch.item.KeyValueItemWriter;
import org.springframework.util.Assert;

import com.example.demo.bean.OutputFileItem;

import jakarta.annotation.PostConstruct;

public class OutputFileMapWriter extends KeyValueItemWriter<String, OutputFileItem> {

	public Map<String, OutputFileItem> outputFileMap;
	
	public void setOutputFileMap(Map<String, OutputFileItem> outputFileMap) {
		this.outputFileMap = outputFileMap;
	}
	
	@Override
	protected void writeKeyValue(String key, OutputFileItem value) {
		if(delete) {
			outputFileMap.remove(key);
		} else {
			outputFileMap.put(key, value);
		}
	}

	@Override
	@PostConstruct
	protected void init() {
		Assert.notNull(outputFileMap, "A outputFileMap is required.");
	}

}
