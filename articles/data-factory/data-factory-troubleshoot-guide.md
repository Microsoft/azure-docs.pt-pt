---
title: Solucionar problemas Azure Data Factory | Microsoft Docs
description: Saiba como solucionar problemas de atividades de controle externo no Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091984"
---
# <a name="troubleshoot-azure-data-factory"></a>Solucionar problemas Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para atividades de controle externo no Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Conector e atividade de cópia

Para problemas de conector, por exemplo, encontrar erro ao usar a atividade de cópia, consulte [solucionar problemas de conectores Azure data Factory](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Código de erro:  3200

- **Mensagem**: Erro 403.

- **Causa**:`The Databricks access token has expired.`

- **Recomendação**: Por padrão, o token de acesso Azure Databricks é válido por 90 dias. Crie um novo token e atualize o serviço vinculado.


### <a name="error-code--3201"></a>Código de erro:  3201

- **Mensagem**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa**:`Bad authoring: Notebook path not specified correctly.`

- **Recomendação**: Especifique o caminho do bloco de anotações na atividade databricks.

<br/>

- **Mensagem**:`Cluster   ... does not exist.`

- **Causa**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recomendação**: Verifique se o cluster do databricks existe.

<br/>

- **Mensagem**:`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Causa**:`Bad authoring.`

- **Recomendação**: Especifique caminhos absolutos para esquemas de endereçamento de espaço `dbfs:/folder/subfolder/foo.py` de trabalho ou para arquivos armazenados no sistema de arquivos do databricks.

<br/>

- **Mensagem**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Causa**:`Bad authoring.`

- **Recomendação**: Verifique a [definição do serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Mensagem**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa**:`Bad authoring.`

- **Recomendação**: Verifique a [definição do serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Mensagem**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Causa**:`Bad authoring.`

- **Recomendação**: Consulte a mensagem de erro. 

<br/>

### <a name="error-code--3202"></a>Código de erro:  3202

- **Mensagem**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Causa**:`Too many Databricks runs in an hour.`

- **Recomendação**: Verifique todos os pipelines que usam este espaço de trabalho do databricks para sua taxa de criação de trabalho.  Se os pipelines inicializados em excesso de databricks forem executados na agregação, migre alguns pipelines para um novo espaço de trabalho.

<br/>

- **Mensagem**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa**:`Authoring error: No value provided for the parameter.`

- **Recomendação**: Inspecione o pipeline JSON e verifique se todos os parâmetros no notebook baseparameters especificam um valor não vazio.

<br/>

- **Mensagem**: `User: `SimpleUserContext {userid =..., nome =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Motivo**: O usuário que gerou o token de acesso não tem permissão para acessar o cluster do databricks especificado no serviço vinculado.

- **Recomendação**: Verifique se o usuário tem as permissões necessárias no espaço de trabalho.


### <a name="error-code--3203"></a>Código de erro:  3203

- **Mensagem**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Motivo**: O cluster foi encerrado. Para clusters interativos, isso pode ser uma condição de corrida.

- **Recomendação**: A melhor maneira de evitar esse erro é usar clusters de trabalho.


### <a name="error-code--3204"></a>Código de erro:  3204

- **Mensagem**:`Job execution failed.`

- **Motivo**:  Mensagens de erro indicam vários problemas, como um estado de cluster inesperado ou uma atividade específica. Geralmente, nenhuma mensagem de erro é exibida. 

- **Recomendação**: N/A


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A tabela a seguir se aplica ao U-SQL.


### <a name="error-code--2709"></a>Código de erro:  2709

- **Mensagem**:`The access token is from the wrong tenant.`

- **Motivo**:  Locatário incorreto do Azure Active Directory (AD do Azure).

- **Recomendação**: Locatário incorreto do Azure Active Directory (AD do Azure).

<br/>

- **Mensagem**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Motivo**:  Esse erro é causado pela limitação no Data Lake Analytics.

- **Recomendação**: Reduza o número de trabalhos enviados para Data Lake Analytics alterando gatilhos de Data Factory e configurações de simultaneidade em atividades. Ou aumente os limites no Data Lake Analytics.

<br/>

- **Mensagem**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Motivo**:  Esse erro é causado pela limitação no Data Lake Analytics. 

- **Recomendação**: Reduza o número de trabalhos enviados para Data Lake Analytics alterando gatilhos de Data Factory e configurações de simultaneidade em atividades. Ou aumente os limites no Data Lake Analytics.


### <a name="error-code--2705"></a>Código de erro:  2705

- **Mensagem**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Motivo**:  A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário fornece para Data Lake Analytics trabalhos tem acesso à conta de Data Lake Analytics e à instância de Data Lake Storage padrão da pasta raiz.


### <a name="error-code--2711"></a>Código de erro:  2711

- **Mensagem**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Motivo**:  A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário fornece para Data Lake Analytics trabalhos tem acesso à conta de Data Lake Analytics e à instância de Data Lake Storage padrão da pasta raiz.

<br/>

- **Mensagem**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Motivo**:  O caminho para o arquivo U-SQL está errado ou as credenciais do serviço vinculado não têm acesso.

- **Recomendação**: Verifique o caminho e as credenciais fornecidas no serviço vinculado.


### <a name="error-code--2704"></a>Código de erro:  2704

- **Mensagem**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Motivo**:  A entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário fornece para Data Lake Analytics trabalhos tem acesso à conta de Data Lake Analytics e à instância de Data Lake Storage padrão da pasta raiz.


### <a name="error-code--2707"></a>Código de erro:  2707

- **Mensagem**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Motivo**:  A conta de Data Lake Analytics no serviço vinculado está incorreta.

- **Recomendação**: Verifique se a conta certa é fornecida.


### <a name="error-code--2703"></a>Código de erro:  2703

- **Mensagem**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Motivo**:  O erro é de Data Lake Analytics. 

- **Recomendação**: Um erro como o exemplo significa que o trabalho foi enviado para Data Lake Analytics, e o script falhou. Investigue no Data Lake Analytics. No portal, vá para a conta de Data Lake Analytics e procure o trabalho usando a ID de execução de atividade de Data Factory (não a ID de execução de pipeline). O trabalho fornece mais informações sobre o erro e irá ajudá-lo a solucionar problemas. Se a resolução não estiver clara, entre em contato com a equipe de suporte do Data Lake Analytics e forneça a URL do trabalho, que inclui o nome da conta e a ID do trabalho.



## <a name="azure-functions"></a>Funções do Azure

### <a name="error-code--3602"></a>Código de erro:  3602

- **Mensagem**:`Invalid HttpMethod: {method}.`

- **Motivo**: O método HTTP especificado na carga de atividade não tem suporte da atividade de funções do Azure. 

- **Recomendação**: Os métodos http com suporte são PUT, POST, GET, DELETE, opções, HEAD e TRACE.


### <a name="error-code--3603"></a>Código de erro:  3603

- **Mensagem**:`Response content is not a valid JObject.`

- **Motivo**: A função do Azure que foi chamada não retornou uma carga JSON na resposta. A atividade de funções do Azure no Data Factory dá suporte apenas ao conteúdo de resposta JSON. 

- **Recomendação**: Atualize a função do Azure para retornar uma carga JSON válida. Por exemplo, uma C# função pode retornar `(ActionResult)new<OkObjectResult("{` \"a\"ID\":\"123`}");`.


### <a name="error-code--3606"></a>Código de erro:  3606

- **Mensagem**:`Azure function activity missing function key.`

- **Motivo**: A definição de atividade da função do Azure não está completa. 

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' functionKey '.


### <a name="error-code--3607"></a>Código de erro:  3607

- **Mensagem**:`Azure function activity missing function name.`

- **Motivo**: A definição de atividade da função do Azure não está completa. 

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' nomedafunção '.


### <a name="error-code--3608"></a>Código de erro:  3608

- **Mensagem**:`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Motivo**: Os detalhes da função do Azure na definição da atividade podem estar incorretos. 

- **Recomendação**: Corrija os detalhes da função do Azure e tente novamente.


### <a name="error-code--3609"></a>Código de erro:  3609

- **Mensagem**:`Azure function activity missing functionAppUrl.` 

- **Motivo**: A definição de atividade da função do Azure não está completa. 

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' functionAppUrl '.


### <a name="error-code--3610"></a>Código de erro:  3610

- **Mensagem**:`There was an error while calling endpoint.`

- **Motivo**: A URL da função pode estar incorreta.

- **Recomendação**: Certifique-se de que o valor de ' functionAppUrl ' na atividade JSON esteja correto e tente novamente.


### <a name="error-code--3611"></a>Código de erro:  3611

- **Mensagem**:`Azure function activity missing Method in JSON.` 

- **Motivo**: A definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' method '.


### <a name="error-code--3612"></a>Código de erro:  3612

- **Mensagem**:`Azure function activity missing LinkedService definition in JSON.`

- **Motivo**: A definição de atividade da função do Azure pode não estar concluída.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem detalhes do serviço vinculado.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Código de erro:  4101

- **Mensagem**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Motivo**: Formato inadequado ou definição ausente de uma propriedade.

- **Recomendação**:  Verifique se a atividade está definida com os dados corretos.


### <a name="error-code--4110"></a>Código de erro:  4110

- **Mensagem**: A atividade AzureMLExecutePipeline não tem a definição de LinkedService em JSON.

- **Motivo**: A definição da atividade AzureMLExecutePipeline não está completa.

- **Recomendação**:  Verifique se a definição de JSON da atividade AzureMLExecutePipeline de entrada tem detalhes do serviço vinculado.


### <a name="error-code--4111"></a>Código de erro:  4111

- **Mensagem**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Motivo**: Definição de atividade incorreta.

- **Recomendação**:  Verifique se a definição de JSON de atividade AzureMLExecutePipeline de entrada tem detalhes de serviço vinculado corretos.


### <a name="error-code--4112"></a>Código de erro:  4112

- **Mensagem**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Motivo**: Formato inadequado ou definição ausente de uma propriedade.

- **Recomendação**:  Verifique se a definição de serviço vinculado tem dados corretos.


### <a name="error-code--4121"></a>Código de erro:  4121

- **Mensagem**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: A credencial usada para acessar o serviço do Azure ML expirou.

- **Recomendação**:  Verifique se a credencial é válida e tente novamente


### <a name="error-code--4122"></a>Código de erro:  4122

- **Mensagem**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: A credencial fornecida no serviço vinculado do serviço AzureML é inválida ou não tem permissão para a operação.

- **Recomendação**:  Verifique se a credencial no serviço vinculado é válida e tem permissão para acessar o serviço do AzureML.


### <a name="error-code--4123"></a>Código de erro:  4123

- **Mensagem**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Causa**:`Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Recomendação**:  Verifique o valor das propriedades da atividade para corresponder à carga esperada do pipeline do Azure ML publicado especificado no serviço vinculado.


### <a name="error-code--4124"></a>Código de erro:  4124

- **Mensagem**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: O ponto de extremidade de pipeline do Azure ML publicado não existe.

- **Recomendação**:  Verifique se o ponto de extremidade do pipeline do Azure ML publicado especificado no serviço vinculado existe no serviço do Azure ML.


### <a name="error-code--4125"></a>Código de erro:  4125

- **Mensagem**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: Erro de servidor no serviço do Azure ML.

- **Recomendação**:  Tente novamente mais tarde. Contate a equipe de serviço do Azure ML para obter ajuda se o problema permanecer.


### <a name="error-code--4126"></a>Código de erro:  4126

- **Mensagem**:`AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Motivo**: Falha na execução do pipeline do AzureML.

- **Recomendação**:  Verifique o AzureMLService para obter mais registros de erros e corrija o pipeline ML


## <a name="custom"></a>Personalizar

A tabela a seguir se aplica ao lote do Azure.


### <a name="error-code--2500"></a>Código de erro:  2500

- **Mensagem**:`Hit unexpected exception and execution failed.`

- **Causa**:`Can't launch command, or the program returned an error code.`

- **Recomendação**:  Verifique se o arquivo executável existe. Se o programa foi iniciado, certifique-se de que *stdout. txt* e *stderr. txt* foram carregados para a conta de armazenamento. É uma boa prática emitir logs de grandes em seu código para depuração.


### <a name="error-code--2501"></a>Código de erro:  2501

- **Mensagem**:`Cannot access user batch account; please check batch account settings.`

- **Motivo**: Chave de acesso do lote ou nome do pool incorreto.

- **Recomendação**: Verifique o nome do pool e a chave de acesso do lote no serviço vinculado.


### <a name="error-code--2502"></a>Código de erro:  2502

- **Mensagem**:`Cannot access user storage account; please check storage account settings.`

- **Motivo**: Nome da conta de armazenamento ou chave de acesso incorreto.

- **Recomendação**: Verifique o nome da conta de armazenamento e a chave de acesso no serviço vinculado.


### <a name="error-code--2504"></a>Código de erro:  2504

- **Mensagem**:`Operation returned an invalid status code 'BadRequest'.` 

- **Motivo**: Muitos arquivos no folderPath da atividade personalizada. O tamanho total de resourceFiles não pode ter mais de 32.768 caracteres.

- **Recomendação**: Remover arquivos desnecessários. Ou compactá-los e adicionar um comando de descompactação para extraí-los. Por exemplo, use`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Código de erro:  2505

- **Mensagem**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Motivo**: As atividades personalizadas dão suporte apenas a contas de armazenamento que usam uma tecla de acesso.

- **Recomendação**: Consulte a descrição do erro.


### <a name="error-code--2507"></a>Código de erro:  2507

- **Mensagem**:`The folder path does not exist or is empty: ....`

- **Motivo**: Nenhum arquivo está na conta de armazenamento no caminho especificado.

- **Recomendação**: O caminho da pasta deve conter os arquivos executáveis que você deseja executar.


### <a name="error-code--2508"></a>Código de erro:  2508

- **Mensagem**:`There are duplicate files in the resource folder.`

- **Motivo**: Vários arquivos com o mesmo nome estão em subpastas diferentes de folderPath.

- **Recomendação**: Estrutura de pastas achatada de atividades personalizadas em folderPath.  Se você precisar preservar a estrutura de pastas, compacte os arquivos e extraia-os no lote do Azure usando um comando unzip. Por exemplo, use`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Código de erro:  2509

- **Mensagem**:`Batch   url ... is invalid; it must be in Uri format.` 

- **Motivo**: As URLs do lote devem ser semelhantes a`https://mybatchaccount.eastus.batch.azure.com`

- **Recomendação**: Consulte a descrição do erro.


### <a name="error-code--2510"></a>Código de erro:  2510

- **Mensagem**:`An   error occurred while sending the request.`

- **Motivo**: A URL do lote é inválida. 

- **Recomendação**: Verifique a URL do lote.


## <a name="hdinsight"></a>HDInsight

A tabela a seguir aplica-se ao Spark, Hive, MapReduce, Pig e streaming do Hadoop.


### <a name="error-code--2300"></a>Código de erro:  2300

- **Mensagem**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Motivo**: O URI de cluster fornecido é inválido. 

- **Recomendação**:  Verifique se o cluster não foi excluído e se o URI fornecido está correto. Ao abrir o URI em um navegador, você deverá ver a interface do usuário do amAmbari. Se o cluster estiver em uma rede virtual, o URI deverá ser o URI privado. Para abri-lo, use uma VM que faça parte da mesma rede virtual. Para obter mais informações, consulte [conectar-se diretamente a serviços de Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Mensagem**:`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Motivo**: O envio do trabalho atingiu o tempo limite. 

- **Recomendação**: O problema pode ser a conectividade geral do HDInsight ou a conectividade de rede. Primeiro, confirme se a interface do usuário do HDInsight Ambari está disponível em qualquer navegador. Confirme se suas credenciais ainda são válidas. Se você estiver usando o IR (tempo de execução integrado) auto-hospedado, certifique-se de fazer isso na VM ou computador em que o IR auto-hospedado está instalado. Em seguida, tente enviar o trabalho de Data Factory novamente. Se ainda falhar, contate a equipe de Data Factory para obter suporte.


- **Mensagem**:`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Motivo**: As credenciais do HDInsight estão incorretas ou expiradas.

- **Recomendação**: Corrija as credenciais e reimplante o serviço vinculado. Primeiro, verifique se as credenciais funcionam no HDInsight abrindo o URI do cluster em qualquer navegador e tentando entrar. Se as credenciais não funcionarem, você poderá redefini-las do portal do Azure.

<br/>

- **Mensagem**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Motivo**: Este erro é do HDInsight.

- **Recomendação**: Esse erro é do cluster HDInsight. Para obter mais informações, consulte [erro do Ambari UI 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 erros ao se conectar ao servidor spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 erros ao se conectar ao servidor Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [solucionar erros de gateway inválidos no gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Mensagem**:`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Motivo**: Muitos trabalhos estão sendo enviados ao HDInsight ao mesmo tempo.

- **Recomendação**: Considere limitar o número de trabalhos simultâneos enviados ao HDInsight. Consulte Data Factory a simultaneidade de atividade se os trabalhos estiverem sendo enviados pela mesma atividade. Altere os gatilhos para que as execuções de pipeline simultâneas sejam distribuídas com o passar do tempo. Consulte a documentação do HDInsight para `templeton.parallellism.job.submit` ajustar conforme o erro sugere.


### <a name="error-code--2303"></a>Código de erro:  2303

- **Mensagem**:`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Motivo**: O trabalho foi enviado para o HDInsight e falhou no HDInsight.

- **Recomendação**: O trabalho foi enviado para o HDInsight com êxito. Ele falhou no cluster. Abra o trabalho e os logs na interface do usuário do HDInsight Ambari ou abra o arquivo do armazenamento, como sugere a mensagem de erro. O arquivo mostra os detalhes do erro.


### <a name="error-code--2310"></a>Código de erro:  2310

- **Mensagem**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Motivo**: O URI de cluster fornecido é inválido. 

- **Recomendação**:  Verifique se o cluster não foi excluído e se o URI fornecido está correto. Ao abrir o URI em um navegador, você deverá ver a interface do usuário do amAmbari. Se o cluster estiver em uma rede virtual, o URI deverá ser o URI privado. Para abri-lo, use uma VM que faça parte da mesma rede virtual. Para obter mais informações, consulte [conectar-se diretamente a serviços de Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Mensagem**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Motivo**: Este erro é do HDInsight.

- **Recomendação**: Esse erro é do cluster HDInsight. Para obter mais informações, consulte [erro do Ambari UI 502](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 erros ao se conectar ao servidor spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 erros ao se conectar ao servidor Spark Thrift](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [solucionar erros de gateway inválidos no gateway de aplicativo](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Mensagem**:`java.lang.NullPointerException`

- **Motivo**: Esse erro ocorre quando o trabalho é enviado a um cluster Spark. 

- **Recomendação**: Essa exceção é proveniente do HDInsight. Ele oculta o problema real. Entre em contato com a equipe do HDInsight para obter suporte. Forneça o nome do cluster e o intervalo de tempo de execução da atividade.


### <a name="error-code--2347"></a>Código de erro:  2347

- **Mensagem**:`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Motivo**: O trabalho foi enviado para o HDInsight e falhou no HDInsight.

- **Recomendação**: O trabalho foi enviado para o HDInsight com êxito. Ele falhou no cluster. Abra o trabalho e os logs na interface do usuário do HDInsight Ambari ou abra o arquivo do armazenamento, como sugere a mensagem de erro. O arquivo mostra os detalhes do erro.


### <a name="error-code--2328"></a>Código de erro:  2328

- **Mensagem**:`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Motivo**: Esse erro ocorre no HDInsight sob demanda.

- **Recomendação**: Esse erro é proveniente do serviço do HDInsight quando o provisionamento do HDInsight falha. Entre em contato com a equipe do HDInsight e forneça o nome do cluster sob demanda.



## <a name="web-activity"></a>Atividade Web

### <a name="error-code--2108"></a>Código de erro:  2108

- **Mensagem**:`Invalid HttpMethod: '...'.`

- **Motivo**: A atividade da Web não dá suporte ao método HTTP especificado na carga da atividade.

- **Recomendação**:  Os métodos HTTP com suporte são PUT, POST, GET e DELETE.

<br/>

- **Mensagem**:`Invalid Server Error 500.`

- **Motivo**: Erro interno no ponto de extremidade.

- **Recomendação**:  Use o Fiddler ou o postmaster para verificar a funcionalidade na URL.

<br/>

- **Mensagem**:`Unauthorized 401.`

- **Motivo**: Autenticação válida ausente na solicitação.

- **Recomendação**:  O token pode ter expirado. Forneça um método de autenticação válido. Use o Fiddler ou o postmaster para verificar a funcionalidade na URL.

<br/>

- **Mensagem**:`Forbidden 403.`

- **Motivo**: Permissões necessárias ausentes.

- **Recomendação**:  Verifique as permissões de usuário no recurso acessado. Use o Fiddler ou o postmaster para verificar a funcionalidade na URL.

<br/>

- **Mensagem**:`Bad Request 400.`

- **Motivo**: Solicitação HTTP inválida.

- **Recomendação**:   Verifique a URL, o verbo e o corpo da solicitação. Use o Fiddler ou o postmaster para validar a solicitação.

<br/>

- **Mensagem**:`Not found 404.` 

- **Motivo**: O recurso não foi encontrado.   

- **Recomendação**:  Use o Fiddler ou o postmaster para validar a solicitação.

<br/>

- **Mensagem**:`Service unavailable.`

- **Motivo**: O serviço não está disponível.

- **Recomendação**:  Use o Fiddler ou o postmaster para validar a solicitação.

<br/>

- **Mensagem**:`Unsupported Media Type.`

- **Motivo**: O tipo de conteúdo não corresponde ao corpo da atividade da Web.

- **Recomendação**:  Especifique o tipo de conteúdo que corresponde ao formato da carga. Use o Fiddler ou o postmaster para validar a solicitação.

<br/>

- **Mensagem**:`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Motivo**: O recurso não está disponível. 

- **Recomendação**:  Use o Fiddler ou o postmaster para verificar o ponto de extremidade.

<br/>

- **Mensagem**:`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Motivo**: Um método de atividade da Web incorreto foi especificado na solicitação.

- **Recomendação**:  Use o Fiddler ou o postmaster para verificar o ponto de extremidade.

<br/>

- **Mensagem**:`invalid_payload`

- **Motivo**: O corpo da atividade da Web está incorreto.

- **Recomendação**:  Use o Fiddler ou o postmaster para verificar o ponto de extremidade.


### <a name="error-code--2208"></a>Código de erro:  2208

- **Mensagem**:`Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Motivo**: O serviço de destino retornou o status de falha.

- **Recomendação**:  Use Fiddler/postmaster para validar a solicitação.


### <a name="error-code--2308"></a>Código de erro:  2308

- **Mensagem**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Motivo**: Pode haver várias razões para esse erro, como conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**:  Use Fiddler/postmaster para validar a solicitação.


Para usar o Fiddler para criar uma sessão HTTP do aplicativo Web monitorado:

1. Baixe, instale e abra o [Fiddler](https://www.telerik.com/download/fiddler).

1. Se seu aplicativo Web usar HTTPS, vá para **ferramentas** > **Opções** > do Fiddler**https**. Selecione **capturar conexões HTTPS** e **descriptografar tráfego HTTPS**. 
   
   ![Opções do Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se seu aplicativo usar certificados SSL, adicione o certificado Fiddler ao seu dispositivo. Acesse **ferramentas** > **Fiddler opções** > **https** **ações** **Exportar certificado raiz para área de trabalho.**  >  > 

1. Desative a captura indo para o**tráfego de captura**de **arquivo** > . Ou pressione **F12**.

1. Limpe o cache do navegador para que todos os itens em cache sejam removidos e precisem ser baixados novamente.

1. Criar uma solicitação: 

   a. Selecione a guia **compositor** .

   b. Defina o método e a URL HTTP.

   c. Adicione cabeçalhos e um corpo de solicitação, se necessário.

   d. Selecione **Executar**.

9. Ative a captura de tráfego novamente e conclua a transação problemática em sua página.

10. Vá para **arquivo** > **salvar** > **todas as sessões**.

Para obter mais informações, consulte [introdução ao Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para solução de problemas, Experimente estes recursos:

*  [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)



