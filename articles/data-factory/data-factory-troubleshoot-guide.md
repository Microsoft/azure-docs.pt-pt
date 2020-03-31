---
title: Troubleshoot Azure Data Factory [ Fábrica de Dados De Saca do Azure ] Microsoft Docs
description: Saiba como resolver as atividades de controlo externo na Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065386"
---
# <a name="troubleshoot-azure-data-factory"></a>Fábrica de Dados Azure

Este artigo explora métodos comuns de resolução de problemas para atividades de controlo externo na Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Atividade de conector e cópia

Para problemas de conector, por exemplo, encontrar erros utilizando a atividade de cópia, consulte [os Conectores](connector-troubleshoot-guide.md)da Fábrica de Dados Troubleshoot Azure .
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Código de erro: 3200

- **Mensagem**: Erro 403.

- **Causa:**`The Databricks access token has expired.`

- **Recomendação**: Por padrão, o token de acesso dos Tijolos de Dados Azure é válido por 90 dias. Crie um novo símbolo e atualize o serviço ligado.


### <a name="error-code--3201"></a>Código de erro: 3201

- **Mensagem:**`Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa:**`Bad authoring: Notebook path not specified correctly.`

- **Recomendação**: Especifique o caminho do caderno na atividade dos Databricks.

<br/>  

- **Mensagem:**`Cluster... does not exist.`

- **Causa:**`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recomendação**: Verifique se o cluster Databricks existe.

<br/>  

- **Mensagem:**`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Especifique os caminhos absolutos `dbfs:/folder/subfolder/foo.py` para os esquemas de endereçamento do espaço de trabalho ou para os ficheiros armazenados no Sistema de Ficheiros databricks.

<br/>  

- **Mensagem:**`{0} LinkedService should have domain and accessToken as required properties.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Verificar a definição de [serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Mensagem:**`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Verificar a definição de [serviço vinculado](compute-linked-services.md#azure-databricks-linked-service).

<br/>  

- **Mensagem:**`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Consulte a mensagem de erro.

<br/>

### <a name="error-code--3202"></a>Código de erro: 3202

- **Mensagem:**`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Causa:**`Too many Databricks runs in an hour.`

- **Recomendação**: Verifique todos os gasodutos que utilizam este espaço de trabalho databricks para a sua taxa de criação de emprego.  Se os oleodutos lançados forem demasiados Databricks em conjunto, emigra alguns oleodutos para um novo espaço de trabalho.

<br/>  

- **Mensagem:**`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa:**`Authoring error: No value provided for the parameter.`

- **Recomendação**: Inspecione o gasoduto JSON e certifique-se de que todos os parâmetros do caderno BaseParameters especificam um valor não vazio.

<br/>  

- **Mensagem** `User: `: SimpleUserContext{userId=...,user@company.comnome= , orgId=...}` is not   authorized to access cluster.`

- **Causa**: O utilizador que gerou o token de acesso não está autorizado a aceder ao cluster Databricks especificado no serviço ligado.

- **Recomendação**: Certifique-se de que o utilizador tem as permissões necessárias no espaço de trabalho.


### <a name="error-code--3203"></a>Código de erro: 3203

- **Mensagem:**`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa:** O aglomerado foi encerrado. Para aglomerados interativos, esta pode ser uma condição de corrida.

- **Recomendação**: A melhor maneira de evitar este erro é utilizar grupos de trabalho.


### <a name="error-code--3204"></a>Código de erro: 3204

- **Mensagem:**`Job execution failed.`

- **Causa**: As mensagens de erro indicam vários problemas, tais como um estado de cluster inesperado ou uma atividade específica. Na maioria das vezes, nenhuma mensagem de erro aparece.

- **Recomendação**: N/A
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A tabela seguinte aplica-se à U-SQL.
      
### <a name="error-code--2709"></a>Código de erro: 2709

- **Mensagem:**`The access token is from the wrong tenant.`

- **Causa**: Incorreto Azure Ative Directory (Azure AD) inquilino.

- **Recomendação**: Incorreto Azure Ative Directory (Azure AD) inquilino.

<br/>

- **Mensagem:**`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Causa**: Este erro é causado por estrangulamento no Data Lake Analytics.

- **Recomendação**: Reduzir o número de postos de trabalho submetidos ao Data Lake Analytics alterando os gatilhos da Fábrica de Dados e configurações de condivisões nas atividades. Ou aumentar os limites da Análise do Lago de Dados.

<br/>  

- **Mensagem:**`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**: Este erro é causado por estrangulamento no Data Lake Analytics.

- **Recomendação**: Reduzir o número de postos de trabalho submetidos ao Data Lake Analytics alterando os gatilhos da Fábrica de Dados e configurações de condivisões nas atividades. Ou aumentar os limites da Análise do Lago de Dados.


### <a name="error-code--2705"></a>Código de erro: 2705

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: O diretor de serviço ou certificado não tem acesso ao ficheiro armazenado.

- **Recomendação**: Certifique-se de que o principal ou certificado de serviço que o utilizador fornece para os trabalhos de Data Lake Analytics tem acesso à conta Data Lake Analytics e à instância padrão de armazenamento de data lake da pasta raiz.


### <a name="error-code--2711"></a>Código de erro: 2711

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: O diretor de serviço ou certificado não tem acesso ao ficheiro armazenado.

- **Recomendação**: Certifique-se de que o principal ou certificado de serviço que o utilizador fornece para os trabalhos de Data Lake Analytics tem acesso à conta Data Lake Analytics e à instância padrão de armazenamento de data lake da pasta raiz.

<br/>  

- **Mensagem:**`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa**: O caminho para o ficheiro U-SQL está errado, ou as credenciais de serviço ligadas não têm acesso.

- **Recomendação**: Verifique o caminho e as credenciais fornecidas no serviço vinculado.


### <a name="error-code--2704"></a>Código de erro: 2704

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa**: O diretor de serviço ou certificado não tem acesso ao ficheiro armazenado.

- **Recomendação**: Certifique-se de que o principal ou certificado de serviço que o utilizador fornece para os trabalhos de Data Lake Analytics tem acesso à conta Data Lake Analytics e à instância padrão de armazenamento de data lake da pasta raiz.


### <a name="error-code--2707"></a>Código de erro: 2707

- **Mensagem:**`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Causa**: A conta Data Lake Analytics no serviço ligado está errada.

- **Recomendação**: Verifique se a conta certa é fornecida.


### <a name="error-code--2703"></a>Código de erro: 2703

- **Mensagem:**`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Causa:** O erro é da Data Lake Analytics.

- **Recomendação**: Um erro como o exemplo significa que o trabalho foi submetido ao Data Lake Analytics, e o guião lá falhou. Investigue em Data Lake Analytics. No portal, vá à conta Data Lake Analytics e procure o trabalho utilizando o ID de execução da atividade da Fábrica de Dados (não o ID de execução do gasoduto). O trabalho lá fornece mais informações sobre o erro e irá ajudá-lo a resolver problemas. Se a resolução não for clara, contacte a equipa de suporte do Data Lake Analytics e forneça o URL de trabalho, que inclui o nome da sua conta e o ID do trabalho.
          

## <a name="azure-functions"></a>Funções do Azure

### <a name="error-code--3602"></a>Código de erro: 3602

- **Mensagem:**`Invalid HttpMethod: '%method;'.`

- **Causa**: O método http especificado na carga útil da atividade não é suportado pela Atividade de Função Azure.

- **Recomendação**: Os métodos http que são suportados são PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.


### <a name="error-code--3603"></a>Código de erro: 3603

- **Mensagem:**`Response Content is not a valid JObject.`

- **Causa**: Função Azure que foi chamada não devolveu uma Carga Útil JSON na resposta. A atividade da função ADF Azure apenas suporta o conteúdo de resposta JSON.

- **Recomendação**: Função Update Azure para devolver uma carga útil válida da JSON, por exemplo,\"\"uma\"função\"C# pode devolver (ActionResult)novo OkObjectResult ("{ Id: 123 }");


### <a name="error-code--3606"></a>Código de erro: 3606

- **Mensagem**: Chave de função de função Azure em falta.

- **Causa:** A definição de atividade da função Azure não está completa.

- **Recomendação**: Por favor, verifique se a definição jSON da atividade do Inserção AzureFunction tem uma propriedade denominada 'functionKey'.


### <a name="error-code--3607"></a>Código de erro: 3607

- **Mensagem:**`Azure function activity missing function name.`

- **Causa:** A definição de atividade da função Azure não está completa.

- **Recomendação:** Por favor, verifique se a definição jSON da atividade do Inserção AzureFunction tem uma propriedade denominada 'functionName'.


### <a name="error-code--3608"></a>Código de erro: 3608

- **Mensagem:**`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa**: Os detalhes da função Azure na definição de atividade podem estar incorretos.

- **Recomendação**: Fixar os detalhes da função azure e voltar a tentar novamente.


### <a name="error-code--3609"></a>Código de erro: 3609

- **Mensagem:**`Azure function activity missing functionAppUrl.`

- **Causa:** A definição de atividade da função Azure não está completa.

- **Recomendação**: Por favor, verifique se a definição jSON da atividade do Inserção AzureFunction tem propriedade denominada 'functionAppUrl'.


### <a name="error-code--3610"></a>Código de erro: 3610

- **Mensagem:**`There was an error while calling endpoint.`

- **Causa:** O URL da função pode estar incorreto.

- **Recomendação**: Certifique-se de que o valor do 'functionAppUrl' na atividade JSON está correto e tente novamente.


### <a name="error-code--3611"></a>Código de erro: 3611

- **Mensagem:**`Azure function activity missing Method in JSON.`

- **Causa:** A definição de atividade da função Azure não está completa.

- **Recomendação**: Por favor, verifique se a definição jSON da atividade do Inserção AzureFunction tem propriedade denominada "método".


### <a name="error-code--3612"></a>Código de erro: 3612

- **Mensagem:**`Azure function activity missing LinkedService definition in JSON.`

- **Causa:** A definição de atividade da função Azure não está completa.

- **Recomendação**: Verifique se a definição jSON da atividade do Inserção AzureFunction tem detalhes de serviço ligados.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Código de erro: 4101

- **Mensagem:**`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa**: Mau formato ou definição em falta de propriedade '%propertyName;'.

- **Recomendação:** Verifique se a atividade '%activityName;' tem propriedade '%propertyName;' definida com dados corretos.


### <a name="error-code--4110"></a>Código de erro: 4110

- **Mensagem:**`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa**: A definição de atividade do AzureMLExecutePipeline não está completa.

- **Recomendação**: Verifique se a inserção Da definição JSON da atividade do AzureMLExecutePipeline tem detalhes de serviço ligados.


### <a name="error-code--4111"></a>Código de erro: 4111

- **Mensagem:**`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa**: Definição de atividade incorreta.

- **Recomendação**: Verifique se a definição json da atividade do Input AzureMLExecutePipeline tem corretas as definições de serviço ligados.


### <a name="error-code--4112"></a>Código de erro: 4112

- **Mensagem:**`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa**: Mau formato ou definição em falta de propriedade '%propertyName;'.

- **Recomendação**: Verifique se o serviço ligado tem propriedade '%propertyName;' definido com dados corretos.


### <a name="error-code--4121"></a>Código de erro: 4121

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial utilizada para aceder ao Azure Machine Learning expirou.

- **Recomendação**: Verifique se a credencial é válida e novamente


### <a name="error-code--4122"></a>Código de erro: 4122

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial fornecida no Serviço Ligado à Aprendizagem automática Azure é inválida ou não tem autorização para a operação.

- **Recomendação**: Verifique se a credencial no Linked Service é válida e tem permissão para aceder ao Azure Machine Learning.


### <a name="error-code--4123"></a>Código de erro: 4123

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: As propriedades da atividade, tais como o gasodutoParâmetros, são inválidas para o gasoduto Azure ML.

- **Recomendação**: Verifique o valor das propriedades de atividade para corresponder à carga útil esperada do gasoduto Azure ML publicado especificado no Linked Service.


### <a name="error-code--4124"></a>Código de erro: 4124

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: O ponto final do gasoduto Azure ML publicado não existe.

- **Recomendação**: Verifique se o ponto final do gasoduto Azure Machine Learning publicado especificado no Serviço Ligado existe no Azure Machine Learning.


### <a name="error-code--4125"></a>Código de erro: 4125

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** Erro do servidor na Aprendizagem automática de Azure.

- **Recomendação**: Por favor, tente mais tarde. Contacte a equipa azure de machine learning para obter ajuda se o problema se mantiver.


### <a name="error-code--4126"></a>Código de erro: 4126

- **Mensagem:**`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Causa:** O gasoduto Azure ML falhou.

- **Recomendação**: Por favor, verifique se o Azure Machine Learning está a procurar mais registos de erros e fixe o gasoduto ML.



## <a name="common"></a>Common

### <a name="error-code--2103"></a>Código de erro: 2103

- **Mensagem:**`Please provide value for the required property '%propertyName;'.`

- **Causa**: O valor do imóvel não foi fornecido, no entanto é exigido no cenário.

- **Recomendação**: Forneça o valor da mensagem e tente novamente.


### <a name="error-code--2104"></a>Código de erro: 2104

- **Mensagem:**`The type of the property '%propertyName;' is incorrect.`

- **Causa**: O tipo de propriedade fornecida não é como esperado.

- **Recomendação**: Por favor, corrija o tipo de propriedade e tente novamente.


### <a name="error-code--2105"></a>Código de erro: 2105

- **Mensagem:**`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa**: O valor para o imóvel é inválido ou não tem o formato esperado.

- **Recomendação**: Consulte a documentação para o imóvel e certifique-se de que o valor fornecido tem formato e tipo esperados.


### <a name="error-code--2106"></a>Código de erro: 2106

- **Mensagem:**`The storage connection string is invalid. %errorMessage;`

- **Causa**: A cadeia de ligação para o armazenamento é inválida ou tem um formato incorreto.

- **Recomendação**: Por favor, vá ao portal Azure, encontre o seu armazenamento, copie a cadeia de ligação e a pasta no seu serviço ligado e tente novamente.


### <a name="error-code--2108"></a>Código de erro: 2108

- **Mensagem:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: O pedido falhou devido a um problema subjacente, como conectividade de rede, falha dNS, validação de certificado de servidor ou tempo limite.

- **Recomendação**: Utilize o Violinista/Carteiro para validar o pedido.


### <a name="error-code--2110"></a>Código de erro: 2110

- **Mensagem:**`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa**: O serviço vinculado especificado na atividade estava errado.

- **Recomendação**: Certifique-se de que o tipo de serviço ligado é um dos tipos suportados para a atividade. Por exemplo, para as atividades de HDI, o tipo de serviço ligado pode ser HDInsight ou HDInsightOnDemand.


### <a name="error-code--2111"></a>Código de erro: 2111

- **Mensagem:**`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa**: O tipo de propriedade fornecida não é como esperado.

- **Recomendação**: Por favor, corrija o tipo de propriedade e tente novamente.


### <a name="error-code--2112"></a>Código de erro: 2112

- **Mensagem:**`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa**: O tipo de nuvem não é suportado ou não foi possível determinar para armazenamento a partir do EndpointSuffix.

- **Recomendação**: Utilize o armazenamento noutra nuvem e tente novamente.


### <a name="error-code--2128"></a>Código de erro: 2128

- **Mensagem:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Conectividade da rede, falha dNS, validação do certificado do servidor ou intervalo.

- **Recomendação**: Validar que o ponto final que está a tentar atingir está a responder aos pedidos. Pode utilizar ferramentas como o Fiddler/Carteiro.



## <a name="custom"></a>Personalizado

A tabela seguinte aplica-se ao Lote Azure.
      
### <a name="error-code--2500"></a>Código de erro: 2500

- **Mensagem:**`Hit unexpected exception and execution failed.`

- **Causa:**`Can't launch command, or the program returned an error code.`

- **Recomendação**: Certifique-se de que o ficheiro executável existe. Se o programa começou, *certifique-se de que stdout.txt* e *stderr.txt* foram enviados para a conta de armazenamento. É uma boa prática emitir registos copiosos no seu código para depuração.


### <a name="error-code--2501"></a>Código de erro: 2501

- **Mensagem:**`Cannot access user batch account; please check batch account settings.`

- **Causa**: Chave de acesso ao lote incorreto ou nome da piscina.

- **Recomendação**: Verifique o nome da piscina e a chave de acesso ao lote no serviço ligado.


### <a name="error-code--2502"></a>Código de erro: 2502

- **Mensagem:**`Cannot access user storage account; please check storage account settings.`

- **Causa**: Nome ou chave de acesso incorreto da conta de armazenamento.

- **Recomendação**: Verifique o nome da conta de armazenamento e a chave de acesso no serviço ligado.


### <a name="error-code--2504"></a>Código de erro: 2504

- **Mensagem:**`Operation returned an invalid status code 'BadRequest'.`

- **Causa**: Demasiados ficheiros na pastaCaminho da atividade personalizada. O tamanho total dos recursosOs Ficheiros não podem ser superiores a 32.768 caracteres.

- **Recomendação**: Remova ficheiros desnecessários. Ou zip-los e adicionar um comando desfecho para extraí-los. Por exemplo, usar`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Código de erro: 2505

- **Mensagem:**`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa**: As atividades personalizadas suportam apenas contas de armazenamento que usam uma chave de acesso.

- **Recomendação**: Consulte a descrição do erro.


### <a name="error-code--2507"></a>Código de erro: 2507

- **Mensagem:**`The folder path does not exist or is empty: ...`

- **Causa:** Não há ficheiros na conta de armazenamento no caminho especificado.

- **Recomendação**: O caminho da pasta deve conter os ficheiros executáveis que pretende executar.


### <a name="error-code--2508"></a>Código de erro: 2508

- **Mensagem:**`There are duplicate files in the resource folder.`

- **Causa**: Vários ficheiros com o mesmo nome estão em diferentes subpastas da pastaPath.

- **Recomendação**: As atividades personalizadas achatam a estrutura da pasta sob pastaCaminho.  Se precisar de preservar a estrutura da pasta, feche os ficheiros e extrai-os no Lote Azure utilizando um comando desfecho. Por exemplo, usar`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Código de erro: 2509

- **Mensagem:**`Batch   url ... is invalid; it must be in Uri format.`

- **Causa:** Os URLs de lote devem ser semelhantes aos`https://mybatchaccount.eastus.batch.azure.com`

- **Recomendação**: Consulte a descrição do erro.


### <a name="error-code--2510"></a>Código de erro: 2510

- **Mensagem:**`An   error occurred while sending the request.`

- **Causa:** O URL do lote é inválido.

- **Recomendação**: Verifique o URL do lote.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Código de erro: 200

- **Mensagem:**`Unexpected error happened: '%error;'.`

- **Causa:** Há um problema de serviço interno.

- **Recomendação**: Contacte o suporte da ADF para obter mais assistência.


### <a name="error-code--201"></a>Código de erro: 201

- **Mensagem:**`JobType %jobType; is not found.`

- **Causa**: Existe um novo tipo de trabalho que não é suportado pela ADF.

- **Recomendação**: Contacte a equipa de apoio da ADF para obter mais assistência.


### <a name="error-code--202"></a>Código de erro: 202

- **Mensagem:**`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que correu mal.

- **Recomendação**: A mensagem de erro deve ajudar a resolver o problema. Se não houver informações suficientes, contacte o suporte da ADF para obter mais ajuda.


### <a name="error-code--203"></a>Código de erro: 203

- **Mensagem:**`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que correu mal.

- **Recomendação**: A mensagem de erro deve ajudar a resolver o problema. Se não houver informações suficientes, contacte o suporte da ADF para obter mais ajuda.


### <a name="error-code--204"></a>Código de erro: 204

- **Mensagem:**`The resumption token is missing for runId '%runId;'.`

- **Causa:** Há um problema de serviço interno.

- **Recomendação**: Contacte o suporte da ADF para obter mais assistência.


### <a name="error-code--205"></a>Código de erro: 205

- **Mensagem:**`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Causa**: Houve um erro ao criar o cluster de IDE sobre procura.

- **Recomendação**: Contacte o suporte da ADF para obter mais assistência.


### <a name="error-code--206"></a>Código de erro: 206

- **Mensagem:**`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Causa:** Houve um problema interno com o serviço que causou isto.

- **Recomendação**: Esta pode ser uma questão transitória. Por favor, tente novamente o seu trabalho, e se o problema persistir, contacte o apoio da ADF para obter mais assistência.


### <a name="error-code--207"></a>Código de erro: 207

- **Mensagem:**`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Causa**: Houve um erro interno ao tentar determinar a região a partir da conta de armazenamento primário.

- **Recomendação**: Tente outro armazenamento. Caso esta não seja uma solução aceitável, contacte a equipa de apoio da ADF para obter mais assistência.


### <a name="error-code--208"></a>Código de erro: 208

- **Mensagem:**`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Causa**: Houve um erro interno ao tentar ler o Diretor de Serviço ou instantaneamente a autenticação MSI.

- **Recomendação**: Por favor, considere fornecer um Diretor de Serviço que tenha permissões para criar um cluster HDInsight na subscrição fornecida e tente novamente. No caso de esta não for uma solução aceitável, contacte a equipa de apoio da ADF para obter mais assistência.


### <a name="error-code--2300"></a>Código de erro: 2300

- **Mensagem:**`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'O nome remoto não pôde ser resolvido.', isto pode significar que o cluster URI fornecido é inválido.


- **Recomendação**: Certifique-se de que o cluster não foi eliminado e que o URI fornecido está correto. Ao abrir o URI num browser, deverá ver o Ambari UI. Se o cluster estiver numa rede virtual, o URI deve ser o URI privado. Para abri-lo, use um VM que faça parte da mesma rede virtual. Para mais informações, consulte [isto.](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services)
                  

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'Uma tarefa foi cancelada.', isto significa que a submissão do trabalho foi cronometrada.

- **Recomendação**: O problema pode ser a conectividade Geral HDInsight ou a conectividade da rede. Primeiro confirme que o HDInsight Ambari UI está disponível em qualquer navegador. Confirme que as suas credenciais ainda são válidas. Se estiver a utilizar o tempo de execução integrado auto-hospedado (IR), certifique-se de que o faz a partir do VM ou da máquina onde o IR auto-hospedado está instalado. Em seguida, tente submeter o trabalho de volta à Data Factory. Se ainda falhar, contacte a equipa da Data Factory para obter apoio.

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a 'O administrador do utilizador está bloqueado em Ambari' ou 'Não autorizado: O nome ou palavra-passe do utilizador Ambari está errado', isto significa que as credenciais para hDInsight estão incorretas ou expiraram.

- **Recomendação**: Corrija as credenciais e reimplante o serviço ligado. Primeiro certifique-se de que as credenciais funcionam no HDInsight abrindo o cluster URI em qualquer navegador e tentando iniciar sessão. Se as credenciais não funcionarem, pode redefini-las a partir do portal Azure.

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a '502 - O servidor Web recebeu uma resposta inválida enquanto atuava como um gateway ou servidor proxy', este erro é devolvido pelo serviço HDInsight.


- **Recomendação**: Veja através da documentação de https://hdinsight.github.io/ambari/ambari-ui-502-error.htmlresolução de problemas do Azure HDInsight, por exemplo, https://hdinsight.github.io/spark/spark-thriftserver-errors.html. https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502
                  

<br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante a "Incapaz de servir o pedido de emprego de apresentação, uma vez que o serviço templeton está ocupado com muitos pedidos de emprego de envio" ou "Queue root.joblauncher já tem 500 pedidos, não pode aceitar a submissão de candidaturas", isto significa que estão a ser submetidos ao HDInsight demasiados postos de trabalho ao mesmo tempo.

- **Recomendação**: Considere limitar o número de postos de trabalho simultâneos submetidos ao HDInsight. Consulte a moeda da atividade da Data Factory se os postos de trabalho estiverem a ser submetidos pela mesma atividade. Mude os gatilhos para que as condutas de gasodutos simultâneos se espalhem ao longo do tempo. Consulte a documentação do HDInsight para ajustar templeton.parallellism.job.submit como o erro sugere.


### <a name="error-code--2301"></a>Código de erro: 2301

- **Mensagem:**`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa:** Cluster ou serviço HDInsight tem problemas.


- **Recomendação**: Este erro ocorre quando a ADF não obtém resposta do cluster HDInsight ao tentar obter o estado do trabalho em funcionamento. Pode ser causa de problemas no próprio cluster, ou o serviço HDInsight pode ter uma falha. Consulte a documentação de resolução de problemas da HDInsight em https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide, ou contacte o seu suporte para obter mais assistência.
                


### <a name="error-code--2302"></a>Código de erro: 2302

- **Mensagem:**`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa**: O trabalho foi submetido ao cluster DeI e falhou lá.

- **Recomendação**: Siga o link de registos de fios na saída de execução de atividade e procure os erros na saída de HDI. Contacte a equipa HDInsight para obter suporte, se necessário.


### <a name="error-code--2303"></a>Código de erro: 2303

- **Mensagem:**`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa**: O trabalho foi submetido ao cluster DeI e falhou lá.

- **Recomendação**: Siga o link de registos de fios na saída de execução de atividade e procure os erros na saída de HDI. Tente novamente ou contacte a equipa HDInsight para obter apoio, se necessário.


### <a name="error-code--2304"></a>Código de erro: 2304

- **Mensagem:**`MSI authentication is not supported on storages for HDI activities.`

- **Causa**: Os serviços ligados ao armazenamento utilizados no serviço ligado ao HDI ou na atividade de HDI estão configurados com autenticação MSI que não é suportada.

- **Recomendação**: Por favor, forneça cordas de ligação completas para contas de armazenamento utilizadas no serviço ligado ao HDI ou na atividade hDI.


### <a name="error-code--2305"></a>Código de erro: 2305

- **Mensagem:**`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa**: As informações de ligação para o cluster HDI estão incorretas, o utilizador desde que não tenha permissões para realizar a ação necessária, ou o serviço HDInsight teve problemas em responder aos pedidos da ADF.

- **Recomendação**: Certifique-se de que a informação do utilizador está correta. Verifique também que o Ambari UI para o cluster HDI pode ser aberto num browser a partir do VM onde o IR está instalado em caso de IR auto-hospedado, ou pode ser aberto a partir de qualquer máquina no caso do Azure IR.


### <a name="error-code--2306"></a>Código de erro: 2306

- **Mensagem:**`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: O json fornecido para a ação do guião é inválido.


- **Recomendação**: A mensagem de erro deve ajudar a identificar o problema. Por favor, corrija a configuração json e tente novamente. Procure https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service mais informações.
                


### <a name="error-code--2310"></a>Código de erro: 2310

- **Mensagem:**`Failed to submit Spark job. Error: '%message;'`

- **Causa**: A DF tentou criar um lote num cluster de faíscas usando API Livy (livy/lote), mas recebeu um erro.

- **Recomendação**: Siga a mensagem de erro para corrigir o problema. Se não houver informações suficientes para o resolver, contacte a equipa de HDI e forneça-lhes o ID do lote e o ID de trabalho que podem ser encontrados na execução de atividade Output na página de Monitorização ADF.


### <a name="error-code--2312"></a>Código de erro: 2312

- **Mensagem:**`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa**: O trabalho falhou no cluster HDInsight Spark.

- **Recomendação**: Siga os links na página de monitorização da execução da atividade na página de monitorização ADF para resolver o problema da execução no cluster HDInsight Spark. Contacte a equipa de suporte da HDInsight para obter mais assistência.


### <a name="error-code--2313"></a>Código de erro: 2313

- **Mensagem:**`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa**: O lote foi eliminado no cluster HDInsight Spark.

- **Recomendação**: Lotes de resolução de problemas no cluster HDInsight Spark. Contacte o suporte HDInsight para obter mais assistência. 


### <a name="error-code--2328"></a>Código de erro: 2328

- **Mensagem:**`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa:**`The error message should show the details of what went wrong.`

- **Recomendação**: A mensagem de erro deve ajudar a resolver o problema.


### <a name="error-code--2329"></a>Código de erro: 2329

- **Mensagem:**`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que correu mal.

- **Recomendação**: A mensagem de erro deve ajudar a resolver o problema.


### <a name="error-code--2331"></a>Código de erro: 2331

- **Mensagem:**`The file path should not be null or empty.`

- **Causa:** O caminho de arquivo fornecido está vazio.

- **Recomendação**: Por favor, forneça um caminho para um ficheiro que exista.


### <a name="error-code--2340"></a>Código de erro: 2340

- **Mensagem:**`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa**: O serviço ligado HDInsightOnDemand não suporta a execução através de IV auto-hosted.

- **Recomendação**: Por favor, selecione um IR Azure e tente novamente.


### <a name="error-code--2341"></a>Código de erro: 2341

- **Mensagem:**`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa**: O URL fornecido não está em formato correto.

- **Recomendação**: Por favor, fixe o URL do cluster e tente novamente.


### <a name="error-code--2342"></a>Código de erro: 2342

- **Mensagem:**`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa**: As credenciais fornecidas estão erradas para o cluster ou houve uma configuração de rede ou problema de ligação ou o IR está a ter problemas de ligação ao cluster.

- **Recomendação:**  
      1. Verifique se as credenciais estão corretas abrindo o Ambari UI do cluster HDInsight num browser.
      2. Se o cluster estiver em VNet e o IR auto-hospedado estiver a ser utilizado, o URL HDI deve ser o URL privado em VNets, o que significa que deve ter 'int' após o nome do cluster. Por exemplo, "devehttps://mycluster.azurehdinsight.net/serhttps://mycluster-int.azurehdinsight.net/mudado para " ".
      2. Se o cluster estiver em VNet, o IR auto-hospedado está a ser utilizado, e o URL privado foi usado, e a ligação ainda falhou, então o VM onde o IR está instalado tinha problemas de ligação ao HDI. Ligue-se ao VM onde o IR está instalado e abra o Ambari UI num browser. Use o URL privado para o cluster. Esta ligação deve funcionar a partir do navegador. Se não o fizer, contacte a equipa de suporte da HDInsight para obter mais assistência.
      3. Se o IR auto-hospedado não estiver a ser utilizado, então o cluster HDI deve ser acessível publicamente. Abra o Ambari UI num navegador e certifique-se de que se abre. Se houver algum problema com o cluster ou os serviços nele, contacte a equipa de suporte da HDInsight para obter assistência.
      Assim, em geral, o URL do cluster HDI utilizado no serviço ligado ao ADF deve ser acessível para ADF IR (auto-hospedado ou Azure) para que a ligação de teste passe e para as corridas para funcionar. Isto pode ser facilmente verificado abrindo esse URL a partir de um navegador, quer a partir de VM quer de qualquer máquina pública.
    


### <a name="error-code--2343"></a>Código de erro: 2343

- **Mensagem:**`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Causa**: Ou o nome do utilizador ou a palavra-passe estão vazios.

- **Recomendação**: Forneça credenciais corretas para ligar ao IDE e tentar novamente.


### <a name="error-code--2345"></a>Código de erro: 2345

- **Mensagem:**`Failed to read the content of the hive script. Error: '%message;'`

- **Causa**: O ficheiro script não existe ou a ADF não pode ligar-se à localização do script.

- **Recomendação**: Verifique se o script existe e o serviço associado ligado tem credenciais adequadas para a ligação.


### <a name="error-code--2346"></a>Código de erro: 2346

- **Mensagem:**`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa:** A Df tentou estabelecer uma ligação ODBC ao cluster HDI e falhou com erro.

- **Recomendação**: A mensagem de erro e o código de erro devem ajudar a resolver os erros de ligação oDBC. Caso não sejam suficientes para resolver o problema, contacte a equipa Azure HDInsight para obter apoio.


### <a name="error-code--2347"></a>Código de erro: 2347

- **Mensagem:**`Hive execution through ODBC failed with error message '%message;'.`

- **Causa**: ADF submeteu o script da colmeia para execução ao cluster HDI através da ligação ODBC e o script falhou no HDI.

- **Recomendação**: A execução do script da colmeia falhou no cluster hDI e a mensagem de erro e código de erro devem ajudar na resolução de problemas. Caso não sejam suficientes para resolver o problema, contacte a equipa Azure HDInsight para obter apoio.


### <a name="error-code--2348"></a>Código de erro: 2348

- **Mensagem:**`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa**: As propriedades do serviço ligado ao armazenamento não estão corretamente definidas.

- **Recomendação**: Apenas as cordas de ligação completas são suportadas no principal serviço ligado ao armazenamento para atividades de IDE. Por favor, certifique-se de que não está a usar auth MSI ou aplicações.


### <a name="error-code--2350"></a>Código de erro: 2350

- **Mensagem:**`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa**: As credenciais fornecidas para se ligarem ao armazenamento onde os ficheiros devem ser localizados estão incorretas ou os ficheiros não existem lá.

- **Recomendação**: Este erro ocorre quando a ADF faz etapas de preparação para as atividades de IDE. Tenta copiar ficheiros para o armazenamento principal antes de submeter o trabalho ao HDI. Certifique-se de que os ficheiros existem no local fornecido, a ligação de armazenamento está correta. As atividades de IDE da ADF não suportam a autenticação MSI em contas de armazenamento relacionadas com atividades de IDE, por isso certifique-se de que esses serviços ligados têm chaves completas ou estão a utilizar o Cofre chave Azure.


### <a name="error-code--2351"></a>Código de erro: 2351

- **Mensagem:**`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa**: O ficheiro não existe no caminho especificado.

- **Recomendação**: Verifique se o ficheiro existe realmente, e o serviço ligado com informações de ligação que apontam para este ficheiro tem credenciais corretas.


### <a name="error-code--2352"></a>Código de erro: 2352

- **Mensagem:**`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa**: As propriedades do serviço ligados ao armazenamento de ficheiros não estão corretamente definidas.

- **Recomendação**: Certifique-se de que as propriedades do serviço ligado ao armazenamento de ficheiros estão corretamente configuradas.


### <a name="error-code--2353"></a>Código de erro: 2353

- **Mensagem:**`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa**: As propriedades do serviço de armazenamento de scripts não estão corretamente definidas.

- **Recomendação**: Certifique-se de que as propriedades do serviço ligado ao armazenamento do script estão corretamente configuradas.


### <a name="error-code--2354"></a>Código de erro: 2354

- **Mensagem:**`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa**: O tipo de serviço ligado ao armazenamento não é suportado pela atividade.

- **Recomendação**: Certifique-se de que o serviço ligado selecionado tem um dos tipos suportados para a atividade. As atividades da HDI suportam serviços ligados ao AzureBlobStorage e ao AzureBlobFSStorage.


### <a name="error-code--2355"></a>Código de erro: 2355

- **Mensagem:**`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa**: O fornecido para comandoAmbiente está incorreto.

- **Recomendação:**  
      Certifique-se de que o \"valor\"fornecido \"é semelhante ao: comandoAmbiente : [ variableName=variableValue\" ] E cada variável aparece na lista apenas uma vez.
    


### <a name="error-code--2356"></a>Código de erro: 2356

- **Mensagem:**`The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa**: A variável foi fornecida duas vezes no comandoAmbiente .

- **Recomendação:**  
      Certifique-se de que o \"valor\"fornecido \"é semelhante ao: comandoAmbiente : [ variableName=variableValue\" ] E cada variável aparece na lista apenas uma vez.
    


### <a name="error-code--2357"></a>Código de erro: 2357

- **Mensagem:**`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa**: As credenciais fornecidas estão incorretas.

- **Recomendação**: Verifique as informações de ligação no serviço ligado à ADLS Gen 1 e certifique-se de que a ligação de teste é bem sucedida.


### <a name="error-code--2358"></a>Código de erro: 2358

- **Mensagem:**`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa**: O valor fornecido para a propriedade necessária 'TimeToLive' tem um formato inválido. 

- **Recomendação**: Por favor atualize o valor para estar na gama sugerida e tente novamente.


### <a name="error-code--2359"></a>Código de erro: 2359

- **Mensagem:**`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa**: O valor fornecido para as 'funções' da propriedade é inválido.

- **Recomendação**: Por favor atualize o valor para ser uma das sugestões e tente novamente.


### <a name="error-code--2360"></a>Código de erro: 2360

- **Mensagem:**`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa**: A cadeia de ligação fornecida para o HCatalogLinkedService é inválida.

- **Recomendação**: Por favor atualize o valor para uma cadeia de ligação SQL Azure correta e tente novamente.


### <a name="error-code--2361"></a>Código de erro: 2361

- **Mensagem:**`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa**: A criação do cluster falhou e a ADF não teve um erro de volta do serviço HDInsight.

- **Recomendação**: Abra o portal Azure e tente encontrar o recurso HDI com o nome fornecido e verifique o estado de provisionamento. Contacte a equipa de suporte HDInsight para obter mais assistência.


### <a name="error-code--2362"></a>Código de erro: 2362

- **Mensagem:**`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa**: O armazenamento adicional fornecido não foi armazenamento Azure Blob.

- **Recomendação**: Forneça a conta de armazenamento Azure Blob como um armazenamento adicional para o HDInsight no serviço ligado à procura.



## <a name="web-activity"></a>Atividade Web

### <a name="error-code--2128"></a>Código de erro: 2128

- **Mensagem:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Conectividade da rede, falha dNS, validação do certificado do servidor ou intervalo.

- **Recomendação**: Validar que o ponto final que está a tentar atingir está a responder aos pedidos. Pode utilizar ferramentas como o Fiddler/Carteiro.


### <a name="error-code--2108"></a>Código de erro: 2108

- **Mensagem:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: O pedido falhou devido a um problema subjacente, como conectividade de rede, falha dNS, validação de certificado de servidor ou tempo limite.

- **Recomendação**: Utilize o Violinista/Carteiro para validar o pedido.
<br>


#### <a name="more-details"></a>Mais detalhes
Para utilizar o Fiddler para criar uma sessão HTTP da aplicação web monitorizada:

1. Descarregue, instale e abra [o Fiddler.](https://www.telerik.com/download/fiddler)

1. Se a sua aplicação web utilizar HTTPS, vá às **Ferramentas** > **Opções** > de Violino**HTTPS**. Selecione **Capture HTTPS CONNECTs** e **Decrypt HTTPS tráfego**.

   ![Opções de violino](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se a sua aplicação utilizar certificados TLS/SSL, adicione o certificado Fiddler ao seu dispositivo. Vá a **Ferramentas** > **Opções** > de Violino**HTTPS** > **Ações** > **Exporte Root Certificate para Desktop**.

1. Desligue a captura indo para **file** > **capture Traffic**. Ou prima **F12**.

1. Limpe a cache do seu navegador para que todos os itens em cache sejam removidos e devem ser descarregados novamente.

1. Criar um pedido:

   1. Selecione o separador **Compositor.**

   1. Delineie o método HTTP e o URL.
   
   1. Adicione cabeçalhos e um corpo de pedido, se necessário.

   1. Selecione **Executar**.

1. Volte a ligar a captura de tráfego e complete a transação problemática na sua página.

1. Vá para **File** > **Save** > All**Sessions**.

Para mais informações, consulte [Começar com o Violinista.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolução de problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de funcionalidade sinuosos da Fábrica de Dados](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Fórum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Stack Overflow forum para fábrica de dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informações do Twitter sobre data factory](https://twitter.com/hashtag/DataFactory)


            
