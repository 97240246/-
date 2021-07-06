## 1.指定testng.xml的路径
```shell
mvn clean test -Dsurefire.suiteXmlFiles=src/main/resources/testng.xml
```

## 2.生成allure报告

- 生成在线html，ctrl+c终止后会无法访问：

  ```
  mvn io.qameta.allure:allure-maven:serve
  ```

- 生成本地html文件

  ```shell
  allure generate --clean target/allure-results 
  ```

  