package com.example.demo.jobstep.processor;

import org.springframework.batch.item.ItemProcessor;

import com.example.demo.bean.IItemBean;

public abstract class AbstractItemProcessor<I extends IItemBean, O extends IItemBean> implements ItemProcessor<I, O> {

	public AbstractItemProcessor() {
		//Doing nothing
	}
	
	@Override
	public O process(I item) throws Exception {
		if(!item.isOutput()) return null;
		return coreProcess(item);
	}
	
	protected abstract O coreProcess(I item);

}


package com.example.demo.jobstep.processor;

import com.example.demo.bean.InputCsvBean;
import com.example.demo.bean.OutputFileItem;

public class CsvFileItemProcessor extends AbstractItemProcessor<InputCsvBean, OutputFileItem>{

	@Override
	protected OutputFileItem coreProcess(InputCsvBean item) {
		
		OutputFileItem outItem = new OutputFileItem();
		outItem.setId(item.getId());
		outItem.setName(item.getName());
		outItem.setAddress(item.getAddress());
		outItem.setTelephone(item.getTelephone());
		outItem.setChinese(null);
		outItem.setMath(null);
		outItem.setEnglish(null);
		
		return outItem;
	}

}
	@Bean
	public PlatformTransactionManager transactionManager(EntityManagerFactory entityManagerFactory) {
		JpaTransactionManager jpaTransactionManager = new JpaTransactionManager();
		jpaTransactionManager.setEntityManagerFactory(null);
		return jpaTransactionManager;
	}
