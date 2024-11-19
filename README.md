package com.example.demo.converter;

import org.springframework.core.convert.converter.Converter;
import com.example.demo.bean.OutputFileItem;

public class OutputFileMapConverter implements Converter<OutputFileItem, String>{

	@Override
	public String convert(OutputFileItem source) {
		return source.getId();
	}

}
