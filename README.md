	@Override
	protected JobExplorer createJobExplorer() throws Exception {
		FileJobExplorerFactoryBean jobExplorerFactory = new FileJobExplorerFactoryBean(jobRepositoryFactory);
		jobExplorerFactory.afterPropertiesSet();
		return jobExplorerFactory.getObject();
	}
