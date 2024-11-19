package com.example.demo.jobstep.validator;

import org.springframework.batch.item.validator.ValidationException;
import org.springframework.batch.item.validator.Validator;

import com.example.demo.bean.InputCsvBean;

public class InputCsvFileValidator implements Validator<InputCsvBean> {

	@Override
	public void validate(InputCsvBean value) throws ValidationException {
		if(value.getId().equals("000")) {
			value.setOutput(false);
		}
	}

}
