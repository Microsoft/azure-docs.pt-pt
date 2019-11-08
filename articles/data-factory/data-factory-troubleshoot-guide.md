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
ms.openlocfilehash: 0026aa377a58f6b766a400860692a35440deb962
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748353"
---
# <a name="troubleshoot-azure-data-factory"></a>Solucionar problemas Azure Data Factory

Este artigo explora métodos comuns de solução de problemas para atividades de controle externo no Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Conector e atividade de cópia

Para problemas de conector, por exemplo, encontrar erro ao usar a atividade de cópia, consulte [solucionar problemas de conectores Azure data Factory](connector-troubleshoot-guide.md).
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Código de erro: 3200

- **Mensagem**: erro 403.

- **Causa**: `The Databricks access token has expired.`

- **Recomendação**: por padrão, o token de acesso Azure Databricks é válido por 90 dias. Crie um novo token e atualize o serviço vinculado.


### <a name="error-code--3201"></a>Código de erro: 3201

- **Mensagem**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa**: `Bad authoring: Notebook path not specified correctly.`

- **Recomendação**: especifique o caminho do bloco de anotações na atividade do databricks.

<br/>    
              
- **Mensagem**: `Cluster   ... does not exist.`

- **Causa**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recomendação**: Verifique se o cluster do databricks existe.

<br/>  

- **Mensagem**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Causa**: `Bad authoring.`

- **Recomendação**: especifique caminhos absolutos para esquemas de endereçamento de espaço de trabalho ou `dbfs:/folder/subfolder/foo.py` para arquivos armazenados no sistema de arquivos do databricks.

<br/>  

- **Mensagem**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Causa**: `Bad authoring.`

- **Recomendação**: Verifique a [definição do serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Mensagem**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa**: `Bad authoring.`

- **Recomendação**: Verifique a [definição do serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Mensagem**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Causa**: `Bad authoring.`

- **Recomendação**: consulte a mensagem de erro.

<br/>

### <a name="error-code--3202"></a>Código de erro: 3202

- **Mensagem**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Causa**: `Too many Databricks runs in an hour.`

- **Recomendação**: marque todos os pipelines que usam este espaço de trabalho do databricks para sua taxa de criação de trabalho.  Se os pipelines inicializados em excesso de databricks forem executados na agregação, migre alguns pipelines para um novo espaço de trabalho.

<br/>  

- **Mensagem**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa**: `Authoring error: No value provided for the parameter.`

- **Recomendação**: Inspecione o pipeline JSON e verifique se todos os parâmetros no notebook baseparameters especificam um valor não vazio.

<br/>  

- **Mensagem**: `User: `SimpleUserContext {userid =..., nome =user@company.com, orgId =...}` is not   authorized to access cluster.`

- **Causa**: o usuário que gerou o token de acesso não tem permissão para acessar o cluster do databricks especificado no serviço vinculado.

- **Recomendação**: Verifique se o usuário tem as permissões necessárias no espaço de trabalho.


### <a name="error-code--3203"></a>Código de erro: 3203

- **Mensagem**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa**: o cluster foi encerrado. Para clusters interativos, isso pode ser uma condição de corrida.

- **Recomendação**: a melhor maneira de evitar esse erro é usar clusters de trabalho.


### <a name="error-code--3204"></a>Código de erro: 3204

- **Mensagem**: `Job execution failed.`

- **Causa**: as mensagens de erro indicam vários problemas, como um estado de cluster inesperado ou uma atividade específica. Geralmente, nenhuma mensagem de erro é exibida.

- **Recomendação**: N/A
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A tabela a seguir se aplica ao U-SQL.
      
### <a name="error-code--2709"></a>Código de erro: 2709

- **Mensagem**: `The access token is from the wrong tenant.`

- **Causa**: locatário incorreto do Azure Active Directory (AD do Azure).

- **Recomendação**: locatário incorreto do Azure Active Directory (AD do Azure).

<br/>

- **Mensagem**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Causa**: esse erro é causado pela limitação no data Lake Analytics.

- **Recomendação**: Reduza o número de trabalhos enviados para data Lake Analytics alterando os gatilhos data Factory e as configurações de simultaneidade em atividades. Ou aumente os limites no Data Lake Analytics.

<br/>  

- **Mensagem**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**: esse erro é causado pela limitação no data Lake Analytics.

- **Recomendação**: Reduza o número de trabalhos enviados para data Lake Analytics alterando os gatilhos data Factory e as configurações de simultaneidade em atividades. Ou aumente os limites no Data Lake Analytics.


### <a name="error-code--2705"></a>Código de erro: 2705

- **Mensagem**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: a entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário fornece para data Lake Analytics trabalhos tem acesso à conta de data Lake Analytics e à instância de data Lake Storage padrão da pasta raiz.


### <a name="error-code--2711"></a>Código de erro: 2711

- **Mensagem**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: a entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário fornece para data Lake Analytics trabalhos tem acesso à conta de data Lake Analytics e à instância de data Lake Storage padrão da pasta raiz.

<br/>  

- **Mensagem**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa**: o caminho para o arquivo U-SQL está errado ou as credenciais do serviço vinculado não têm acesso.

- **Recomendação**: Verifique o caminho e as credenciais fornecidas no serviço vinculado.


### <a name="error-code--2704"></a>Código de erro: 2704

- **Mensagem**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: a entidade de serviço ou o certificado não tem acesso ao arquivo no armazenamento.

- **Recomendação**: Verifique se a entidade de serviço ou o certificado que o usuário fornece para data Lake Analytics trabalhos tem acesso à conta de data Lake Analytics e à instância de data Lake Storage padrão da pasta raiz.


### <a name="error-code--2707"></a>Código de erro: 2707

- **Mensagem**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Causa**: a conta de data Lake Analytics no serviço vinculado está incorreta.

- **Recomendação**: Verifique se a conta correta foi fornecida.


### <a name="error-code--2703"></a>Código de erro: 2703

- **Mensagem**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Causa**: o erro é de data Lake Analytics.

- **Recomendação**: um erro como o exemplo significa que o trabalho foi enviado para data Lake Analytics e o script falhou. Investigue no Data Lake Analytics. No portal, vá para a conta de Data Lake Analytics e procure o trabalho usando a ID de execução de atividade de Data Factory (não a ID de execução de pipeline). O trabalho fornece mais informações sobre o erro e irá ajudá-lo a solucionar problemas. Se a resolução não estiver clara, entre em contato com a equipe de suporte do Data Lake Analytics e forneça a URL do trabalho, que inclui o nome da conta e a ID do trabalho.
          

## <a name="azure-functions"></a>Funções do Azure

### <a name="error-code--3602"></a>Código de erro: 3602

- **Mensagem**: `Invalid HttpMethod: '%method;'.`

- **Causa**: o método HTTP especificado na carga de atividade não é suportado pela atividade de funções do Azure.

- **Recomendação**: os métodos http que têm suporte são PUT, post, obter, excluir, opções, cabeçalho e rastreamento.


### <a name="error-code--3603"></a>Código de erro: 3603

- **Mensagem**: `Response Content is not a valid JObject.`

- **Causa**: a função do Azure que foi chamada não retornou uma carga JSON na resposta. A atividade de função do Azure AAD dá suporte apenas ao conteúdo de resposta JSON.

- **Recomendação**: Atualize o Azure function para retornar uma carga JSON válida, por C# exemplo, uma função pode retornar (ActionResult) New OkObjectResult ("{\"Id\":\"123\"}");


### <a name="error-code--3606"></a>Código de erro: 3606

- **Mensagem**: chave de função ausente na atividade de função do Azure.

- **Causa**: a definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' functionKey '.


### <a name="error-code--3607"></a>Código de erro: 3607

- **Mensagem**: `Azure function activity missing function name.`

- **Causa**: a definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' nomedafunção '.


### <a name="error-code--3608"></a>Código de erro: 3608

- **Mensagem**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa**: os detalhes da função do Azure na definição da atividade podem estar incorretos.

- **Recomendação**: corrija os detalhes da função do Azure e tente novamente.


### <a name="error-code--3609"></a>Código de erro: 3609

- **Mensagem**: `Azure function activity missing functionAppUrl.`

- **Causa**: a definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' functionAppUrl '.


### <a name="error-code--3610"></a>Código de erro: 3610

- **Mensagem**: `There was an error while calling endpoint.`

- **Causa**: a URL da função pode estar incorreta.

- **Recomendação**: Verifique se o valor de ' functionAppUrl ' na atividade JSON está correto e tente novamente.


### <a name="error-code--3611"></a>Código de erro: 3611

- **Mensagem**: `Azure function activity missing Method in JSON.`

- **Causa**: a definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem a propriedade chamada ' method '.


### <a name="error-code--3612"></a>Código de erro: 3612

- **Mensagem**: `Azure function activity missing LinkedService definition in JSON.`

- **Causa**: a definição de atividade da função do Azure não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureFunction de entrada tem detalhes do serviço vinculado.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Código de erro: 4101

- **Mensagem**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa**: formato inadequado ou definição ausente da propriedade '% PropertyName; '.

- **Recomendação**: Verifique se a atividade '% ActivityName; ' tem a propriedade '% PropertyName; ' definida com os dados corretos.


### <a name="error-code--4110"></a>Código de erro: 4110

- **Mensagem**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa**: a definição da atividade AzureMLExecutePipeline não está completa.

- **Recomendação**: Verifique se a definição de JSON da atividade AzureMLExecutePipeline de entrada tem detalhes do serviço vinculado.


### <a name="error-code--4111"></a>Código de erro: 4111

- **Mensagem**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa**: definição de atividade incorreta.

- **Recomendação**: Verifique se a definição de JSON de atividade de AzureMLExecutePipeline de entrada tem detalhes de serviço vinculado corretos.


### <a name="error-code--4112"></a>Código de erro: 4112

- **Mensagem**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa**: formato inadequado ou definição ausente da propriedade '% PropertyName; '.

- **Recomendação**: Verifique se o serviço vinculado tem a propriedade '% PropertyName; ' definida com os dados corretos.


### <a name="error-code--4121"></a>Código de erro: 4121

- **Mensagem**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Causa**: a credencial usada para acessar o serviço do Azure ml expirou.

- **Recomendação**: Verifique se a credencial é válida e tente novamente


### <a name="error-code--4122"></a>Código de erro: 4122

- **Mensagem**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Causa**: a credencial fornecida no serviço vinculado do serviço do Azure ml é inválida ou não tem permissão para a operação.

- **Recomendação**: Verifique se a credencial no serviço vinculado é válida e tem permissão para acessar o serviço do Azure ml.


### <a name="error-code--4123"></a>Código de erro: 4123

- **Mensagem**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Causa**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Recomendação**: Verifique o valor das propriedades de atividade para corresponder a carga esperada do pipeline do ml do Azure publicado especificado no serviço vinculado.


### <a name="error-code--4124"></a>Código de erro: 4124

- **Mensagem**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Causa**: o ponto de extremidade de pipeline do Azure ml publicado não existe.

- **Recomendação**: Verifique se o ponto de extremidade do pipeline do Azure ml publicado especificado no serviço vinculado existe no serviço do Azure ml.


### <a name="error-code--4125"></a>Código de erro: 4125

- **Mensagem**: `Request sent to Azure ML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure ML Service: '%externalMessage;'.`

- **Causa**: erro de servidor no serviço do Azure ml.

- **Recomendação**: tente novamente mais tarde. Contate a equipe de serviço do Azure ML para obter ajuda se o problema permanecer.


### <a name="error-code--4126"></a>Código de erro: 4126

- **Mensagem**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure ML Service for more error logs.`

- **Causa**: falha na execução do pipeline do Azure ml.

- **Recomendação**: Verifique o serviço de ml do Azure para obter mais logs de erros e corrija o pipeline ml



## <a name="common"></a>Common

### <a name="error-code--2103"></a>Código de erro: 2103

- **Mensagem**: `Please provide value for the required property '%propertyName;'.`

- **Causa**: o valor da propriedade não foi fornecido, no entanto, é necessário no cenário.

- **Recomendação**: forneça o valor da mensagem e tente novamente.


### <a name="error-code--2104"></a>Código de erro: 2104

- **Mensagem**: `The type of the property '%propertyName;' is incorrect.`

- **Causa**: o tipo da propriedade fornecida não é o esperado.

- **Recomendação**: corrija o tipo da propriedade e tente novamente.


### <a name="error-code--2105"></a>Código de erro: 2105

- **Mensagem**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa**: o valor da propriedade é inválido ou não tem o formato esperado.

- **Recomendação**: Procure a propriedade na documentação e verifique se o valor fornecido tem o formato e o tipo esperados.


### <a name="error-code--2106"></a>Código de erro: 2106

- **Mensagem**: `The storage connection string is invalid. %errorMessage;`

- **Causa**: a cadeia de conexão para o armazenamento é inválida ou tem um formato incorreto.

- **Recomendação**: Vá para o portal do Azure, localize o armazenamento, copie a cadeia de conexão e cole o serviço vinculado e tente novamente.


### <a name="error-code--2108"></a>Código de erro: 2108

- **Mensagem**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: falha na solicitação devido a um problema subjacente, como conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: Use Fiddler/postmaster para validar a solicitação.


### <a name="error-code--2110"></a>Código de erro: 2110

- **Mensagem**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa**: o serviço vinculado especificado na atividade estava errado.

- **Recomendação**: Verifique se o tipo de serviço vinculado é um dos tipos com suporte para a atividade. Por exemplo, para atividades HDI, o tipo de serviço vinculado pode ser HDInsight ou HDInsightOnDemand.


### <a name="error-code--2111"></a>Código de erro: 2111

- **Mensagem**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa**: o tipo da propriedade fornecida não é o esperado.

- **Recomendação**: corrija o tipo da propriedade e tente novamente.


### <a name="error-code--2112"></a>Código de erro: 2112

- **Mensagem**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa**: o tipo de nuvem não tem suporte ou não pôde ser determinado para armazenamento do EndpointSuffix.

- **Recomendação**: Use o armazenamento em outra nuvem e tente novamente.


### <a name="error-code--2128"></a>Código de erro: 2128

- **Mensagem**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: valide se o ponto de extremidade que você está tentando atingir está respondendo às solicitações. Você pode usar ferramentas como Fiddler/postmaster.



## <a name="custom"></a>Personalizado

A tabela a seguir se aplica ao lote do Azure.
      
### <a name="error-code--2500"></a>Código de erro: 2500

- **Mensagem**: `Hit unexpected exception and execution failed.`

- **Causa**: `Can't launch command, or the program returned an error code.`

- **Recomendação**: Verifique se o arquivo executável existe. Se o programa foi iniciado, certifique-se de que *stdout. txt* e *stderr. txt* foram carregados para a conta de armazenamento. É uma boa prática emitir logs de grandes em seu código para depuração.


### <a name="error-code--2501"></a>Código de erro: 2501

- **Mensagem**: `Cannot access user batch account; please check batch account settings.`

- **Causa**: nome de pool ou chave de acesso do lote incorreto.

- **Recomendação**: Verifique o nome do pool e a chave de acesso do lote no serviço vinculado.


### <a name="error-code--2502"></a>Código de erro: 2502

- **Mensagem**: `Cannot access user storage account; please check storage account settings.`

- **Causa**: nome de conta de armazenamento ou chave de acesso incorreto.

- **Recomendação**: Verifique o nome da conta de armazenamento e a chave de acesso no serviço vinculado.


### <a name="error-code--2504"></a>Código de erro: 2504

- **Mensagem**: `Operation returned an invalid status code 'BadRequest'.`

- **Causa**: muitos arquivos no FolderPath da atividade personalizada. O tamanho total de resourceFiles não pode ter mais de 32.768 caracteres.

- **Recomendação**: remover arquivos desnecessários. Ou compactá-los e adicionar um comando de descompactação para extraí-los. Por exemplo, use `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Código de erro: 2505

- **Mensagem**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa**: as atividades personalizadas dão suporte apenas a contas de armazenamento que usam uma tecla de acesso.

- **Recomendação**: consulte a descrição do erro.


### <a name="error-code--2507"></a>Código de erro: 2507

- **Mensagem**: `The folder path does not exist or is empty: ....`

- **Causa**: nenhum arquivo está na conta de armazenamento no caminho especificado.

- **Recomendação**: o caminho da pasta deve conter os arquivos executáveis que você deseja executar.


### <a name="error-code--2508"></a>Código de erro: 2508

- **Mensagem**: `There are duplicate files in the resource folder.`

- **Causa**: vários arquivos com o mesmo nome estão em subpastas diferentes de FolderPath.

- **Recomendação**: estrutura de pastas achatada de atividades personalizadas em FolderPath.  Se você precisar preservar a estrutura de pastas, compacte os arquivos e extraia-os no lote do Azure usando um comando unzip. Por exemplo, use `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Código de erro: 2509

- **Mensagem**: `Batch   url ... is invalid; it must be in Uri format.`

- **Causa**: as URLs de lote devem ser semelhantes a `https://mybatchaccount.eastus.batch.azure.com`

- **Recomendação**: consulte a descrição do erro.


### <a name="error-code--2510"></a>Código de erro: 2510

- **Mensagem**: `An   error occurred while sending the request.`

- **Causa**: a URL do lote é inválida.

- **Recomendação**: Verifique a URL do lote.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Código de erro: 200

- **Mensagem**: `Unexpected error happened: '%error;'.`

- **Causa**: há um problema de serviço interno.

- **Recomendação**: entre em contato com o suporte do ADF para obter mais assistência.


### <a name="error-code--201"></a>Código de erro: 201

- **Mensagem**: `JobType %jobType; is not found.`

- **Causa**: há um novo tipo de trabalho que não é suportado pelo ADF.

- **Recomendação**: entre em contato com a equipe de suporte do ADF para obter mais assistência.


### <a name="error-code--202"></a>Código de erro: 202

- **Mensagem**: `Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: a mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: a mensagem de erro deve ajudar a solucionar o problema. Se não houver informações suficientes, entre em contato com o suporte do ADF para obter mais ajuda.


### <a name="error-code--203"></a>Código de erro: 203

- **Mensagem**: `Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: a mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: a mensagem de erro deve ajudar a solucionar o problema. Se não houver informações suficientes, entre em contato com o suporte do ADF para obter mais ajuda.


### <a name="error-code--204"></a>Código de erro: 204

- **Mensagem**: `The resumption token is missing for runId '%runId;'.`

- **Causa**: há um problema de serviço interno.

- **Recomendação**: entre em contato com o suporte do ADF para obter mais assistência.


### <a name="error-code--205"></a>Código de erro: 205

- **Mensagem**: `Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Causa**: erro ao criar o cluster HDI on Demand.

- **Recomendação**: entre em contato com o suporte do ADF para obter mais assistência.


### <a name="error-code--206"></a>Código de erro: 206

- **Mensagem**: `The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Causa**: houve um problema interno com o serviço que causou isso.

- **Recomendação**: isso pode ser um problema transitório. Repita o trabalho e, se o problema persistir, entre em contato com o suporte do ADF para obter mais assistência.


### <a name="error-code--207"></a>Código de erro: 207

- **Mensagem**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Causa**: ocorreu um erro interno ao tentar determinar a região da conta de armazenamento primária.

- **Recomendação**: tente outro armazenamento. Caso essa não seja uma solução aceitável, entre em contato com a equipe de suporte do ADF para obter mais assistência.


### <a name="error-code--208"></a>Código de erro: 208

- **Mensagem**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Causa**: ocorreu um erro interno ao tentar ler a entidade de serviço ou instanciar a autenticação msi.

- **Recomendação**: Considere fornecer uma entidade de serviço que tenha permissões para criar um cluster HDInsight na assinatura fornecida e tente novamente. Caso essa não seja uma solução aceitável, entre em contato com a equipe de suporte do ADF para obter mais assistência.


### <a name="error-code--2300"></a>Código de erro: 2300

- **Mensagem**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Causa**: quando a mensagem de erro contém uma mensagem semelhante a ' o nome remoto não pôde ser resolvido. ', isso pode significar que o URI de cluster fornecido é inválido.


- **Recomendação**: Verifique se o cluster não foi excluído e se o URI fornecido está correto. Ao abrir o URI em um navegador, você deverá ver a interface do usuário do amAmbari. Se o cluster estiver em uma rede virtual, o URI deverá ser o URI privado. Para abri-lo, use uma VM que faça parte da mesma rede virtual. Para obter mais informações, consulte [isso](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Causa**: quando A mensagem de erro contém uma mensagem semelhante A ' uma tarefa foi cancelada. ', isso significa que o envio do trabalho atingiu o tempo limite.

- **Recomendação**: o problema pode ser a conectividade geral do HDInsight ou a conectividade de rede. Primeiro, confirme se a interface do usuário do HDInsight Ambari está disponível em qualquer navegador. Confirme se suas credenciais ainda são válidas. Se você estiver usando o IR (tempo de execução integrado) auto-hospedado, certifique-se de fazer isso na VM ou computador em que o IR auto-hospedado está instalado. Em seguida, tente enviar o trabalho de Data Factory novamente. Se ainda falhar, contate a equipe de Data Factory para obter suporte.

<br>

- **Causa**: quando a mensagem de erro contém uma mensagem semelhante a ' o administrador do usuário está bloqueado em Ambari ' ou ' não autorizado: o nome de usuário do Ambari ou a senha está incorreta ', isso significa que as credenciais do HDInsight estão incorretas ou expiraram.

- **Recomendação**: corrija as credenciais e reimplante o serviço vinculado. Primeiro, verifique se as credenciais funcionam no HDInsight abrindo o URI do cluster em qualquer navegador e tentando entrar. Se as credenciais não funcionarem, você poderá redefini-las do portal do Azure.

<br>

- **Causa**: quando a mensagem de erro contém uma mensagem semelhante a ' 502-o servidor Web recebeu uma resposta inválida ao atuar como um gateway ou servidor proxy ', esse erro é retornado pelo serviço do HDInsight.


- **Recomendação**: consulte a documentação de solução de problemas do Azure HDInsight, por exemplo, https://hdinsight.github.io/ambari/ambari-ui-502-error.html, https://hdinsight.github.io/spark/spark-thriftserver-errors.htmlhttps://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502.
                  

<br>

- **Causa**: quando a mensagem de erro contém uma mensagem semelhante a ' não é possível atender à solicitação de envio de trabalho, pois o serviço Templeton está ocupado com muitas solicitações de trabalho de envio ' ou ' raiz da fila. joblauncher já tem 500 aplicativos, não é possível aceitar o envio de aplicativo ', isso significa que muitos trabalhos estão sendo enviados ao HDInsight ao mesmo tempo.

- **Recomendação**: considere limitar o número de trabalhos simultâneos enviados ao HDInsight. Consulte Data Factory a simultaneidade de atividade se os trabalhos estiverem sendo enviados pela mesma atividade. Altere os gatilhos para que as execuções de pipeline simultâneas sejam distribuídas com o passar do tempo. Consulte a documentação do HDInsight para ajustar Templeton. parallellism. Job. Submit conforme o erro sugere.


### <a name="error-code--2301"></a>Código de erro: 2301

- **Mensagem**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa**: o serviço ou cluster HDInsight tem problemas.


- **Recomendação**: esse erro ocorre quando o ADF não obtém resposta do cluster HDInsight ao tentar obter o status do trabalho em execução. Pode ser a causa de problemas no próprio cluster ou o serviço HDInsight pode ter uma interrupção. Consulte a documentação de solução de problemas do HDInsight em https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideou entre em contato com o suporte para obter assistência adicional.
                


### <a name="error-code--2302"></a>Código de erro: 2302

- **Mensagem**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa**: o trabalho foi enviado ao cluster HDI e falhou.

- **Recomendação**: Siga o link yarn logs na saída da execução da atividade e procure os erros na saída do HDI. Contate a equipe do HDInsight para obter suporte, se necessário.


### <a name="error-code--2303"></a>Código de erro: 2303

- **Mensagem**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa**: o trabalho foi enviado ao cluster HDI e falhou.

- **Recomendação**: Siga o link yarn logs na saída da execução da atividade e procure os erros na saída do HDI. Tente novamente ou contate a equipe do HDInsight para obter suporte, se necessário.


### <a name="error-code--2304"></a>Código de erro: 2304

- **Mensagem**: `MSI authentication is not supported on storages for HDI activities.`

- **Causa**: os serviços vinculados de armazenamento usados no serviço vinculado HDI ou na atividade HDI são configurados com a autenticação MSI que não tem suporte.

- **Recomendação**: forneça cadeias de conexão completas para contas de armazenamento usadas no serviço vinculado HDI ou na atividade HDI.


### <a name="error-code--2305"></a>Código de erro: 2305

- **Mensagem**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa**: as informações de conexão para o cluster HDI estão incorretas, o usuário fornecido não tem permissões para executar a ação necessária ou o serviço HDInsight teve problemas de resposta a solicitações do ADF.

- **Recomendação**: Verifique se as informações do usuário estão corretas. Verifique também se a interface do usuário do amAmbari para o cluster HDI pode ser aberta em um navegador da VM em que o IR está instalado no caso do IR auto-hospedado ou pode ser aberto em qualquer computador no caso Azure IR.


### <a name="error-code--2306"></a>Código de erro: 2306

- **Mensagem**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: o JSON fornecido para a ação de script é inválido.


- **Recomendação**: a mensagem de erro deve ajudar a identificar o problema. Corrija a configuração JSON e tente novamente. Verifique https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service para obter mais informações.
                


### <a name="error-code--2310"></a>Código de erro: 2310

- **Mensagem**: `Failed to submit Spark job. Error: '%message;'`

- **Causa**: o ADF tentou criar um lote em um cluster Spark usando a API Livy (Livy/batch), mas recebeu um erro.

- **Recomendação**: siga a mensagem de erro para corrigir o problema. Se não houver informações suficientes para que elas sejam resolvidas, entre em contato com a equipe do HDI e forneça a ID do lote e a ID do trabalho que podem ser encontrados na página de monitoramento de execução da atividade na saída do ADF.


### <a name="error-code--2312"></a>Código de erro: 2312

- **Mensagem**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa**: falha no trabalho no cluster HDInsight Spark.

- **Recomendação**: siga os links na página de monitoramento de execução da atividade no ADF para solucionar problemas de execução no cluster Spark do HDInsight. Entre em contato com a equipe de suporte do HDInsight para obter assistência adicional.


### <a name="error-code--2313"></a>Código de erro: 2313

- **Mensagem**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa**: o lote foi excluído no cluster HDInsight Spark.

- **Recomendação**: solucionar problemas de lotes no cluster HDInsight Spark. Contate o suporte do HDInsight para obter mais assistência. 


### <a name="error-code--2328"></a>Código de erro: 2328

- **Mensagem**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: `The error message should show the details of what went wrong.`

- **Recomendação**: a mensagem de erro deve ajudar a solucionar o problema.


### <a name="error-code--2329"></a>Código de erro: 2329

- **Mensagem**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: a mensagem de erro deve mostrar os detalhes do que deu errado.

- **Recomendação**: a mensagem de erro deve ajudar a solucionar o problema.


### <a name="error-code--2331"></a>Código de erro: 2331

- **Mensagem**: `The file path should not be null or empty.`

- **Causa**: o caminho de arquivo fornecido está vazio.

- **Recomendação**: forneça um caminho para um arquivo existente.


### <a name="error-code--2340"></a>Código de erro: 2340

- **Mensagem**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa**: o serviço vinculado HDInsightOnDemand não dá suporte à execução via SelfHosted ir.

- **Recomendação**: selecione um Azure ir e tente novamente.


### <a name="error-code--2341"></a>Código de erro: 2341

- **Mensagem**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa**: a URL fornecida não está no formato correto.

- **Recomendação**: corrija a URL do cluster e tente novamente.


### <a name="error-code--2342"></a>Código de erro: 2342

- **Mensagem**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa**: as credenciais fornecidas são incorretas para o cluster ou houve uma configuração de rede ou um problema de conexão ou o ir está tendo problemas para se conectar ao cluster.

- **Recomendação**:  
      1. Verifique se as credenciais estão corretas abrindo a interface do usuário do Ambari do cluster HDInsight em um navegador.
      2. Se o cluster estiver em VNet e o IR auto-hospedado estiver sendo usado, a URL HDI deverá ser a URL privada em VNets, o que significa que ele deve ter '-int ' após o nome do cluster. Por exemplo, "https://mycluster.azurehdinsight.net/" deve ser alterado para "https://mycluster-int.azurehdinsight.net/".
      2. Se o cluster estiver na VNet, o IR auto-hospedado está sendo usado e a URL privada foi usada e a conexão ainda falhou, a VM em que o IR está instalado teve problemas ao se conectar ao HDI. Conecte-se à VM em que o IR está instalado e abra a interface do usuário do Ambari em um navegador. Use a URL privada para o cluster. Essa conexão deve funcionar no navegador. Caso contrário, entre em contato com a equipe de suporte do HDInsight para obter mais assistência.
      3. Se o IR auto-hospedado não estiver sendo usado, o cluster HDI deverá ser acessível publicamente. Abra a interface do usuário do amAmbari em um navegador e verifique se ele é aberto. Se houver algum problema com o cluster ou com os serviços, entre em contato com a equipe de suporte do HDInsight para obter assistência.
      Portanto, em geral, a URL do cluster HDI usada no serviço vinculado do ADF deve ser acessível para o IR do ADF (auto-hospedado ou Azure) para que a conexão de teste passe e para que as execuções funcionem. Isso pode ser facilmente verificado abrindo a URL em um navegador, seja da VM ou de qualquer máquina pública.
    


### <a name="error-code--2343"></a>Código de erro: 2343

- **Mensagem**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Causa**: o nome de usuário ou a senha estão vazios.

- **Recomendação**: forneça as credenciais corretas para se conectar ao HDI e tente novamente.


### <a name="error-code--2345"></a>Código de erro: 2345

- **Mensagem**: `Failed to read the content of the hive script. Error: '%message;'`

- **Causa**: o arquivo de script não existe ou o ADF não pôde se conectar ao local do script.

- **Recomendação**: Verifique se o script existe e se o serviço vinculado associado tem as credenciais adequadas para a conexão.


### <a name="error-code--2346"></a>Código de erro: 2346

- **Mensagem**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa**: o ADF tentou estabelecer uma conexão ODBC com o cluster HDI e falhou com erro.

- **Recomendação**: a mensagem de erro e o código de erro devem ajudar a solucionar os erros de conexão ODBC. Caso não sejam suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight para obter suporte.


### <a name="error-code--2347"></a>Código de erro: 2347

- **Mensagem**: `Hive execution through ODBC failed with error message '%message;'.`

- **Causa**: o ADF enviou o script do hive para execução para o cluster HDI via conexão ODBC e o script falhou em HDI.

- **Recomendação**: a execução do script do hive falhou no cluster HDI e a mensagem de erro e o código de erro devem ajudar na solução de problemas. Caso não sejam suficientes para resolver o problema, entre em contato com a equipe do Azure HDInsight para obter suporte.


### <a name="error-code--2348"></a>Código de erro: 2348

- **Mensagem**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa**: as propriedades do serviço vinculado de armazenamento não estão definidas corretamente.

- **Recomendação**: há suporte apenas para cadeias de conexão completas no serviço vinculado de armazenamento principal para atividades HDI. Certifique-se de que você não está usando a autenticação ou os aplicativos do MSI.


### <a name="error-code--2350"></a>Código de erro: 2350

- **Mensagem**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa**: as credenciais fornecidas para se conectar ao armazenamento onde os arquivos devem estar localizados estão incorretas ou os arquivos não existem.

- **Recomendação**: esse erro ocorre quando o ADF faz etapas de preparação para atividades HDI. Ele tenta copiar arquivos para o armazenamento principal antes de enviar o trabalho para HDI. Verifique se os arquivos existem no local fornecido, se a conexão de armazenamento está correta. As atividades do ADF HDI não dão suporte à autenticação MSI em contas de armazenamento relacionadas às atividades do HDI, portanto, certifique-se de que esses serviços vinculados tenham chaves completas ou estejam usando Azure Key Vault.


### <a name="error-code--2351"></a>Código de erro: 2351

- **Mensagem**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa**: o arquivo não existe no caminho especificado.

- **Recomendação**: Verifique se o arquivo realmente existe e se o serviço vinculado com informações de conexão apontando para esse arquivo tem as credenciais corretas.


### <a name="error-code--2352"></a>Código de erro: 2352

- **Mensagem**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa**: as propriedades do serviço vinculado do armazenamento de arquivos não estão definidas corretamente.

- **Recomendação**: Verifique se as propriedades do serviço vinculado de armazenamento de arquivos estão configuradas corretamente.


### <a name="error-code--2353"></a>Código de erro: 2353

- **Mensagem**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa**: as propriedades do serviço vinculado de armazenamento de script não estão definidas corretamente.

- **Recomendação**: Verifique se as propriedades do serviço vinculado de armazenamento de script estão configuradas corretamente.


### <a name="error-code--2354"></a>Código de erro: 2354

- **Mensagem**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa**: o tipo de serviço vinculado de armazenamento não é suportado pela atividade.

- **Recomendação**: Verifique se o serviço vinculado selecionado tem um dos tipos com suporte para a atividade. As atividades HDI dão suporte aos serviços vinculados AzureBlobStorage e AzureBlobFSStorage.


### <a name="error-code--2355"></a>Código de erro: 2355

- **Mensagem**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa**: o fornecido para commandEnvironment está incorreto.

- **Recomendação**:  
      Certifique-se de que o valor fornecido é semelhante a: \"commandEnvironment\": [\"VariableName = variableValue\"] e cada variável aparece na lista apenas uma vez.
    


### <a name="error-code--2356"></a>Código de erro: 2356

- **Mensagem**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa**: a variável foi fornecida duas vezes no commandEnvironment.

- **Recomendação**:  
      Certifique-se de que o valor fornecido é semelhante a: \"commandEnvironment\": [\"VariableName = variableValue\"] e cada variável aparece na lista apenas uma vez.
    


### <a name="error-code--2357"></a>Código de erro: 2357

- **Mensagem**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa**: as credenciais fornecidas estão incorretas.

- **Recomendação**: Verifique as informações de conexão no serviço vinculado do ADLS Gen 1 e certifique-se de que a conexão de teste tenha sucesso.


### <a name="error-code--2358"></a>Código de erro: 2358

- **Mensagem**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa**: o valor fornecido para a propriedade necessária ' TimeToLive ' tem um formato inválido. 

- **Recomendação**: Atualize o valor para estar no intervalo sugerido e tente novamente.


### <a name="error-code--2359"></a>Código de erro: 2359

- **Mensagem**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa**: o valor fornecido para a propriedade ' Roles ' é inválido.

- **Recomendação**: Atualize o valor para ser uma das sugestões e tente novamente.


### <a name="error-code--2360"></a>Código de erro: 2360

- **Mensagem**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa**: a cadeia de conexão fornecida para o HCatalogLinkedService é inválida.

- **Recomendação**: Atualize o valor para uma cadeia de conexão do Azure SQL correta e tente novamente.


### <a name="error-code--2361"></a>Código de erro: 2361

- **Mensagem**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa**: falha na criação do cluster e o ADF não retornou um erro do serviço HDInsight.

- **Recomendação**: Abra o portal do Azure e tente localizar o recurso HDI com o nome fornecido e verifique o status de provisionamento. Contate a equipe de suporte do HDInsight para obter mais assistência.


### <a name="error-code--2362"></a>Código de erro: 2362

- **Mensagem**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa**: o armazenamento adicional fornecido não era o armazenamento de BLOBs do Azure.

- **Recomendação**: forneça a conta de armazenamento de BLOBs do Azure como um armazenamento adicional para o serviço vinculado sob demanda do HDInsight.



## <a name="web-activity"></a>Atividade Web

### <a name="error-code--2128"></a>Código de erro: 2128

- **Mensagem**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: valide se o ponto de extremidade que você está tentando atingir está respondendo às solicitações. Você pode usar ferramentas como Fiddler/postmaster.


### <a name="error-code--2108"></a>Código de erro: 2108

- **Mensagem**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: falha na solicitação devido a um problema subjacente, como conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: Use Fiddler/postmaster para validar a solicitação.
<br>


#### <a name="more-details"></a>Mais detalhes
Para usar o Fiddler para criar uma sessão HTTP do aplicativo Web monitorado:

1. Baixe, instale e abra o [Fiddler](https://www.telerik.com/download/fiddler).

1. Se seu aplicativo Web usar HTTPS, vá para **ferramentas** > **Opções Fiddler** > **https**. Selecione **capturar conexões HTTPS** e **descriptografar tráfego HTTPS**.

![Opções do Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se seu aplicativo usar certificados SSL, adicione o certificado Fiddler ao seu dispositivo. Acesse **ferramentas** > **Opções Fiddler** > **ações** **https** >  > **Exportar certificado raiz para área de trabalho**.

1. Desative a captura acessando **arquivo** > **capturar tráfego**. Ou pressione **F12**.

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

*  [Blogue do Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory solicitações de recursos](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow Fórum para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)


            
