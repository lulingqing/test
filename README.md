package com.example.demo.jobstep.mapper;

import org.springframework.batch.item.file.mapping.FieldSetMapper;
import org.springframework.batch.item.file.transform.FieldSet;
import org.springframework.validation.BindException;
import com.example.demo.bean.InputCsvBean;

public class CsvFileMapper implements FieldSetMapper<InputCsvBean> {

	@Override
	public InputCsvBean mapFieldSet(FieldSet fieldSet) throws BindException {
		
		InputCsvBean itemBean = new InputCsvBean();
		
		itemBean.setId(fieldSet.readString(InputCsvBean.ConstantValueList.ID));
		itemBean.setName(fieldSet.readString(InputCsvBean.ConstantValueList.NAME));
		itemBean.setAddress(fieldSet.readString(InputCsvBean.ConstantValueList.ADDRESS));
		itemBean.setTelephone(fieldSet.readString(InputCsvBean.ConstantValueList.TELEPHONE));
		
		return itemBean;
	}

}
