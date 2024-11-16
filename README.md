package com.example.demo.bean;

import java.io.Serializable;

public class TableManyKey implements Serializable {

	private static final long serialVersionUID = 1549743109103150741L;
	
	public TableManyKey() {
		//Doing nothing
	}
	
	public String getNewId() {
		return newId;
	}
	public void setNewId(String newId) {
		this.newId = newId;
	}

	public String getId() {
		return id;
	}

	public void setId(String id) {
		this.id = id;
	}

	private String newId;
	private String id;
	
	@Override
	public int hashCode() {
		final int PRIME=31;
		int result = 1;
		result = PRIME * result + ((newId == null) ? 0 : newId.hashCode());
		result = PRIME * result + ((id == null) ? 0 : id.hashCode());
		return result;
	}
	
	@Override
	public boolean equals(Object obj) {
		if(this == obj) 
			return true;
		if(obj == null) 
			return false;
		if(getClass() != obj.getClass()) 
			return false;
		
		final TableManyKey other = (TableManyKey) obj;
		
		if(newId == null) {
			if(other.newId != null)
				return false;
		} else if(!newId.equals(other.newId))
			return false;
		
		if(id == null) {
			if(other.id != null)
				return false;
		} else if(!id.equals(other.id))
			return false;
		
		return true;
			
	}

}


	@Bean
	@Lazy
	@StepScope
	public JpaItemWriter<OutputTableItem> outputTableItemWriter() {
		return new JpaItemWriterBuilder<OutputTableItem>()
				.entityManagerFactory(entityManagerFactory)
				.build();
	}
