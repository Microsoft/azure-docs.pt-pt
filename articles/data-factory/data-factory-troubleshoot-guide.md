---
title: Resolução de problemas Azure Data Factory | Microsoft Docs
description: Saiba como resolver as atividades de controlo externo na Azure Data Factory.
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382808"
---
# <a name="troubleshoot-azure-data-factory"></a>Resolver Problemas do Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Este artigo explora métodos comuns de resolução de problemas para atividades de controlo externo na Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Atividade de cópia e conector

Para problemas de conector, tais como um erro de encontro utilizando a atividade da cópia, consulte os [Conectores da Fábrica de Dados de Resolução de Problemas](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Código de erro: 3200

- **Mensagem**: Erro 403.

- **Causa:**`The Databricks access token has expired.`

- **Recomendação**: Por padrão, o token de acesso Azure Databricks é válido por 90 dias. Crie um novo token e atualize o serviço ligado.

### <a name="error-code-3201"></a>Código de erro: 3201

- **Mensagem:**`Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa:**`Bad authoring: Notebook path not specified correctly.`

- **Recomendação**: Especifique o percurso do caderno na atividade databricks.

<br/> 

- **Mensagem:**`Cluster... does not exist.`

- **Causa:**`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Recomendação**: Verifique se existe o cluster Databricks.

<br/> 

- **Mensagem:**`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Especifique caminhos absolutos para esquemas de endereçamento do espaço de trabalho ou `dbfs:/folder/subfolder/foo.py` para ficheiros armazenados no Sistema de Ficheiros databricks (DFS).

<br/> 

- **Mensagem:**`{0} LinkedService should have domain and accessToken as required properties.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Verifique a [definição de serviço ligado](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Mensagem:**`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Verifique a [definição de serviço ligado](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Mensagem:**`Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Causa:**`Bad authoring.`

- **Recomendação**: Consulte a mensagem de erro.

<br/>

### <a name="error-code-3202"></a>Código de erro: 3202

- **Mensagem:**`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Causa:**`Too many Databricks runs in an hour.`

- **Recomendação**: Verifique todos os oleodutos que utilizam este espaço de trabalho Databricks para a sua taxa de criação de emprego. Se os oleodutos lançados forem demasiados Databricks em conjunto, migrar alguns oleodutos para um novo espaço de trabalho.

<br/> 

- **Mensagem:**`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa:**`Authoring error: No value provided for the parameter.`

- **Recomendação**: Inspecione o gasoduto JSON e certifique-se de que todos os parâmetros do caderno baseParameters especificam um valor não estimado.

<br/> 

- **Mensagem**: `User: ` SimpleUserContext{userId=..., nome= user@company.com , orgId=...}` is not authorized to access cluster.`

- **Causa**: O utilizador que gerou o token de acesso não está autorizado a aceder ao cluster Databricks especificado no serviço ligado.

- **Recomendação**: Certifique-se de que o utilizador tem as permissões necessárias no espaço de trabalho.

### <a name="error-code-3203"></a>Código de erro: 3203

- **Mensagem:**`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa:** O aglomerado foi encerrado. Para agrupamentos interativos, esta questão pode ser uma condição de raça.

- **Recomendação**: Para evitar este erro, utilize clusters de trabalho.

### <a name="error-code-3204"></a>Código de erro: 3204

- **Mensagem:**`Job execution failed.`

- **Causa:** As mensagens de erro indicam vários problemas, tais como um estado de agrupamento inesperado ou uma atividade específica. Muitas vezes, não aparece nenhuma mensagem de erro.

- **Recomendação**: N/A

### <a name="error-code-3208"></a>Código de erro: 3208

- **Mensagem:**`An error occurred while sending the request.`

- **Causa**: A ligação de rede ao serviço Databricks foi interrompida.

- **Recomendação**: Se estiver a utilizar um tempo de integração auto-organizado, certifique-se de que a ligação da rede é fiável a partir dos nós de tempo de integração. Se estiver a utilizar o tempo de integração do Azure, o retraimento geralmente funciona.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A tabela a seguir aplica-se ao U-SQL.
 
### <a name="error-code-2709"></a>Código de erro: 2709

- **Mensagem:**`The access token is from the wrong tenant.`

- **Causa:** Incorreto Azure Ative Directory (Azure AD) inquilino.

- **Recomendação**: Inquilino incorreto do Azure Ative Directory (Azure AD).

<br/>

- **Mensagem:**`We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Causa:** Este erro é causado por estrangulamento no Data Lake Analytics.

- **Recomendação**: Reduzir o número de postos de trabalho submetidos à Data Lake Analytics. Ou altera os gatilhos da Data Factory e as definições de concordância nas atividades, ou aumenta os limites da Data Lake Analytics.

<br/> 

- **Mensagem:**`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa:** Este erro é causado por estrangulamento no Data Lake Analytics.

- **Recomendação**: Reduzir o número de postos de trabalho submetidos à Data Lake Analytics. Ou altera os gatilhos da Data Factory e as definições de concordância nas atividades, ou aumenta os limites da Data Lake Analytics.

### <a name="error-code-2705"></a>Código de erro: 2705

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa**: O principal ou certificado de serviço não tem acesso ao ficheiro armazenado.

- **Recomendação**: Verifique se o principal ou certificado de serviço que o utilizador fornece para os empregos do Data Lake Analytics tem acesso à conta Data Lake Analytics e à instância de armazenamento de data lake padrão da pasta raiz.

### <a name="error-code-2711"></a>Código de erro: 2711

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa**: O principal ou certificado de serviço não tem acesso ao ficheiro armazenado.

- **Recomendação**: Verifique se o principal ou certificado de serviço que o utilizador fornece para os empregos do Data Lake Analytics tem acesso à conta Data Lake Analytics e à instância de armazenamento de data lake padrão da pasta raiz.

<br/> 

- **Mensagem:**`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa:** O caminho para o ficheiro U-SQL está errado, ou as credenciais de serviço ligadas não têm acesso.

- **Recomendação**: Verifique o percurso e as credenciais fornecidas no serviço ligado.

### <a name="error-code-2704"></a>Código de erro: 2704

- **Mensagem:**`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa**: O principal ou certificado de serviço não tem acesso ao ficheiro armazenado.

- **Recomendação**: Verifique se o principal ou certificado de serviço que o utilizador fornece para os empregos do Data Lake Analytics tem acesso à conta Data Lake Analytics e à instância de armazenamento de data lake padrão da pasta raiz.

### <a name="error-code-2707"></a>Código de erro: 2707

- **Mensagem:**`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Causa**: A conta Data Lake Analytics no serviço ligado está errada.

- **Recomendação**: Verifique se a conta certa é fornecida.

### <a name="error-code-2703"></a>Código de erro: 2703

- **Mensagem:**`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Causa:** O erro é da Data Lake Analytics.

- **Recomendação**: O trabalho foi submetido ao Data Lake Analytics, e o guião ali, ambos falharam. Investigue no Data Lake Analytics. No portal, vá à conta Data Lake Analytics e procure o trabalho utilizando o ID de execução da atividade da Data Factory (não utilize o ID de funcionação do pipeline). O trabalho lá fornece mais informações sobre o erro, e vai ajudá-lo a resolver problemas.

   Se a resolução não for clara, contacte a equipa de apoio do Data Lake Analytics e forneça o trabalho de Localizador de Recursos Universais (URL), que inclui o nome da sua conta e o ID do trabalho.
 
## <a name="azure-functions"></a>Funções do Azure

### <a name="error-code-3602"></a>Código de erro: 3602

- **Mensagem:**`Invalid HttpMethod: '%method;'.`

- **Causa**: O Httpmethod especificado na carga útil da atividade não é suportado pela Atividade de Função Azure.

- **Recomendação**: Os httpmethods suportados são: PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.

### <a name="error-code-3603"></a>Código de erro: 3603

- **Mensagem:**`Response Content is not a valid JObject.`

- **Causa**: A função Azure que foi chamada não devolveu uma carga JSON na resposta. A atividade de função Azure Data Factory (ADF) Azure suporta apenas o conteúdo de resposta JSON.

- **Recomendação**: Atualizar a função Azure para devolver uma carga útil válida do JSON, tal como uma função C# pode voltar `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Código de erro: 3606

- **Mensagem**: Azure função função faltando tecla de função.

- **Causa**: A definição de atividade da função Azure não está completa.

- **Recomendação**: Verifique se a atividade de função Azure de entrada JSON tem uma propriedade chamada `functionKey` .

### <a name="error-code-3607"></a>Código de erro: 3607

- **Mensagem:**`Azure function activity missing function name.`

- **Causa**: A definição de atividade da função Azure não está completa.

- **Recomendação**: Verifique se a atividade de função Azure de entrada JSON tem uma propriedade chamada `functionName` .

### <a name="error-code-3608"></a>Código de erro: 3608

- **Mensagem:**`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa**: Os detalhes da função Azure na definição de atividade podem estar incorretos.

- **Recomendação**: Corrija os detalhes da função Azure e tente novamente.

### <a name="error-code-3609"></a>Código de erro: 3609

- **Mensagem:**`Azure function activity missing functionAppUrl.`

- **Causa**: A definição de atividade da função Azure não está completa.

- **Recomendação**: Verifique se a atividade de função Azure de entrada JSON tem uma propriedade chamada `functionAppUrl` .

### <a name="error-code-3610"></a>Código de erro: 3610

- **Mensagem:**`There was an error while calling endpoint.`

- **Causa:** O URL de função pode estar incorreto.

- **Recomendação**: Verifique se o valor para `functionAppUrl` a atividade JSON está correto e tente novamente.

### <a name="error-code-3611"></a>Código de erro: 3611

- **Mensagem:**`Azure function activity missing Method in JSON.`

- **Causa**: A definição de atividade da função Azure não está completa.

- **Recomendação**: Verifique se a atividade de função Azure de entrada JSON tem uma propriedade chamada `method` .

### <a name="error-code-3612"></a>Código de erro: 3612

- **Mensagem:**`Azure function activity missing LinkedService definition in JSON.`

- **Causa**: A definição de atividade da função Azure não está completa.

- **Recomendação**: Verifique se a função de entrada Azure atividade JSON definiu detalhes de serviço ligados.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Código de erro: 4101

- **Mensagem:**`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa:** Mau formato ou falta de definição de `%propertyName;` propriedade.

- **Recomendação**: Verifique se a atividade `%activityName;` tem a propriedade definida com `%propertyName;` dados corretos.

### <a name="error-code-4110"></a>Código de erro: 4110

- **Mensagem:**`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa**: A definição de atividade AzureMLExecutePipeline não está completa.

- **Recomendação**: Verifique se a entrada AzureMLExecutePipeline atividade JSON definiu detalhes de serviço corretamente ligados.

### <a name="error-code-4111"></a>Código de erro: 4111

- **Mensagem:**`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa:** Definição de atividade incorreta.

- **Recomendação**: Verifique se a entrada AzureMLExecutePipeline atividade JSON definiu detalhes de serviço corretamente ligados.

### <a name="error-code-4112"></a>Código de erro: 4112

- **Mensagem:**`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa:** Mau formato ou definição em falta de propriedade '%propertyName;'.

- **Recomendação**: Verifique se o serviço ligado tem a propriedade `%propertyName;` definida com dados corretos.

### <a name="error-code-4121"></a>Código de erro: 4121

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial utilizada para aceder ao Azure Machine Learning expirou.

- **Recomendação**: Verifique se a credencial é válida e redando.

### <a name="error-code-4122"></a>Código de erro: 4122

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: A credencial fornecida no Serviço Ligado à Aprendizagem de Máquinas Azure é inválida ou não tem autorização para a operação.

- **Recomendação**: Verifique se a credencial no Serviço Ligado é válida e tem permissão para aceder ao Azure Machine Learning.

### <a name="error-code-4123"></a>Código de erro: 4123

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: As propriedades da atividade tal como `pipelineParameters` são inválidas para o gasoduto Azure Machine Learning (ML).

- **Recomendação**: Verifique se o valor das propriedades de atividade corresponde à carga útil esperada do gasoduto Azure ML publicado especificado no Linked Service.

### <a name="error-code-4124"></a>Código de erro: 4124

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa**: O ponto final do gasoduto Azure ML publicado não existe.

- **Recomendação**: Verifique se o ponto final do gasoduto Azure Machine Learning publicado especificado no Serviço Ligado existe no Azure Machine Learning.

### <a name="error-code-4125"></a>Código de erro: 4125

- **Mensagem:**`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** Existe um erro do servidor no Azure Machine Learning.

- **Recomendação**: Retry mais tarde. Contacte a equipa de Machine Learning do Azure para obter ajuda se o problema continuar.

### <a name="error-code-4126"></a>Código de erro: 4126

- **Mensagem:**`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Causa**: O gasoduto Azure ML falhou.

- **Recomendação**: Verifique a aprendizagem automática Azure para obter mais registos de erros e, em seguida, fixe o gasoduto ML.

## <a name="common"></a>Common

### <a name="error-code-2103"></a>Código de erro: 2103

- **Mensagem:**`Please provide value for the required property '%propertyName;'.`

- **Causa**: O valor exigido para o imóvel não foi fornecido.

- **Recomendação**: Forneça o valor da mensagem e tente novamente.

### <a name="error-code-2104"></a>Código de erro: 2104

- **Mensagem:**`The type of the property '%propertyName;' is incorrect.`

- **Causa:** O tipo de propriedade fornecido não é correto.

- **Recomendação**: Fixar o tipo de imóvel e tentar novamente.

### <a name="error-code-2105"></a>Código de erro: 2105

- **Mensagem:**`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa:** O valor do imóvel é inválido ou não está no formato esperado.

- **Recomendação**: Consulte a documentação do imóvel e verifique se o valor fornecido inclui o formato e o tipo corretos.

### <a name="error-code-2106"></a>Código de erro: 2106

- **Mensagem:**`The storage connection string is invalid. %errorMessage;`

- **Causa**: O fio de ligação para o armazenamento é inválido ou tem um formato incorreto.

- **Recomendação**: Vá ao portal Azure e encontre o seu armazenamento, em seguida, copie e cole o fio de ligação no seu serviço ligado e tente novamente.

### <a name="error-code-2108"></a>Código de erro: 2108

- **Mensagem:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: O pedido falhou devido a um problema subjacente, como conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: Utilize o Violinista/Carteiro para validar o pedido.

### <a name="error-code-2110"></a>Código de erro: 2110

- **Mensagem:**`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa**: O serviço ligado especificado na atividade está incorreto.

- **Recomendação**: Verifique se o tipo de serviço ligado é um dos tipos suportados para a atividade. Por exemplo, o tipo de serviço ligado para atividades HDI pode ser HDInsight ou HDInsightOnDemand.

### <a name="error-code-2111"></a>Código de erro: 2111

- **Mensagem:**`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa:** O tipo de propriedade fornecida não está correta.

- **Recomendação**: Fixar o tipo de propriedade e tentar novamente.

### <a name="error-code-2112"></a>Código de erro: 2112

- **Mensagem:**`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa**: O tipo de nuvem não é suportado ou não pode ser determinado para armazenamento a partir do sufixo EndpointS.

- **Recomendação:** Utilize o armazenamento noutra nuvem e tente novamente.

### <a name="error-code-2128"></a>Código de erro: 2128

- **Mensagem:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa:** Conectividade de rede, falha de DNS, validação de certificado do servidor ou tempo limite.

- **Recomendação**: Valide que o ponto final que está a tentar atingir está a responder aos pedidos. Pode usar ferramentas como Fiddler/Carteiro.

## <a name="custom"></a>Personalizado

A tabela a seguir aplica-se ao Azure Batch.
 
### <a name="error-code-2500"></a>Código de erro: 2500

- **Mensagem:**`Hit unexpected exception and execution failed.`

- **Causa:**`Can't launch command, or the program returned an error code.`

- **Recomendação**: Certifique-se de que o ficheiro executável existe. Se o programa começar, verifique se *stdout.txt* e *stderr.txt* foram enviados para a conta de armazenamento. É uma boa prática incluir registos no seu código para depurar.

### <a name="error-code-2501"></a>Código de erro: 2501

- **Mensagem:**`Cannot access user batch account; please check batch account settings.`

- **Causa:** Chave de acesso incorreto do lote ou nome da piscina.

- **Recomendação**: Verifique o nome da piscina e a chave de acesso do Lote no serviço ligado.

### <a name="error-code-2502"></a>Código de erro: 2502

- **Mensagem:**`Cannot access user storage account; please check storage account settings.`

- **Causa:** Nome da conta de armazenamento incorreto ou chave de acesso.

- **Recomendação**: Verifique o nome da conta de armazenamento e a chave de acesso no serviço ligado.

### <a name="error-code-2504"></a>Código de erro: 2504

- **Mensagem:**`Operation returned an invalid status code 'BadRequest'.`

- **Causa:** Demasiados ficheiros na `folderPath` atividade personalizada. O tamanho total não `resourceFiles` pode ser mais de 32.768 caracteres.

- **Recomendação:** Remova ficheiros desnecessários ou feche-os e adicione um comando de unzip para os extrair.
   
   Por exemplo, usar `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Código de erro: 2505

- **Mensagem:**`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa:** As atividades personalizadas suportam apenas contas de armazenamento que utilizem uma chave de acesso.

- **Recomendação**: Consulte a descrição do erro.

### <a name="error-code-2507"></a>Código de erro: 2507

- **Mensagem:**`The folder path does not exist or is empty: ...`

- **Causa**: Não há ficheiros na conta de armazenamento no caminho especificado.

- **Recomendação**: O caminho da pasta deve conter os ficheiros executáveis que pretende executar.

### <a name="error-code-2508"></a>Código de erro: 2508

- **Mensagem:**`There are duplicate files in the resource folder.`

- **Causa:** Vários ficheiros com o mesmo nome estão em diferentes subpastas de pastasPath.

- **Recomendação**: Atividades personalizadas achatam a estrutura da pasta em pastaPata. Se necessitar de preservar a estrutura da pasta, feche os ficheiros e extraia-os em Azure Batch utilizando um comando unzip.
   
   Por exemplo, usar `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Código de erro: 2509

- **Mensagem:**`Batch url ... is invalid; it must be in Uri format.`

- **Causa:** Os URLs de lote devem ser semelhantes a `https://mybatchaccount.eastus.batch.azure.com`

- **Recomendação**: Consulte a descrição do erro.

### <a name="error-code-2510"></a>Código de erro: 2510

- **Mensagem:**`An error occurred while sending the request.`

- **Causa**: A URL do lote é inválida.

- **Recomendação**: Verifique o URL do lote.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Código de erro: 206

- **Mensagem:**`The batch ID for Spark job is invalid. Please retry your job.`

- **Causa**: Houve um problema interno com o serviço que causou este erro.

- **Recomendação:** Esta questão pode ser transitória. Recandidutar o seu trabalho depois de algum tempo.

### <a name="error-code-207"></a>Código de erro: 207

- **Mensagem:**`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Causa:** Houve um erro interno ao tentar determinar a região a partir da conta de armazenamento primário.

- **Recomendação:** Tente outro armazenamento. 

### <a name="error-code-208"></a>Código de erro: 208

- **Mensagem:**`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Causa**: Houve um erro interno ao tentar ler o Diretor de Serviço ou instantaneamente a autenticação MSI.

- **Recomendação**: Considere fornecer um principal de serviço, que tenha permissões para criar um cluster HDInsight na subscrição fornecida e tente novamente. Verifique se [as identidades de gestão estão corretamente configurada](../hdinsight/hdinsight-managed-identities.md).

### <a name="error-code-2300"></a>Código de erro: 2300

- **Mensagem:**`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Causa**: A mensagem de erro contém uma mensagem semelhante a `The remote name could not be resolved.` . O cluster fornecido URI pode ser inválido.

- **Recomendação**: Verifique se o cluster não foi eliminado e que o URI fornecido está correto. Quando abrir o URI num browser, deverá ver o Ambari UI. Se o cluster estiver numa rede virtual, o URI deve ser o URI privado. Para abri-la, utilize uma Máquina Virtual (VM) que faça parte da mesma rede virtual.

   Para mais informações, consulte [diretamente os serviços da Apache Hadoop.](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services)
 
 </br>

- **Causa**: Se a mensagem de erro contiver uma mensagem semelhante `A task was canceled.` a, a submissão do trabalho foi cronometrada.

- **Recomendação**: O problema pode ser a conectividade geral hdInsight ou a conectividade da rede. Primeiro confirme que o HDInsight Ambari UI está disponível em qualquer navegador. Então verifique se as suas credenciais ainda são válidas.
   
   Se estiver a utilizar um tempo de execução integrado auto-hospedado (IR), execute este passo a partir do VM ou da máquina onde o IR auto-hospedado está instalado. Então tente submeter o trabalho da Data Factory novamente.

   Para mais informações, leia [Ambari Web UI.](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui)

 </br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante `User admin is locked out in Ambari` `Unauthorized: Ambari user name or password is incorrect` ou, as credenciais para HDInsight estão incorretas ou expiraram.

- **Recomendação**: Corrija as credenciais e reimplante o serviço ligado. Verifique primeiro se as credenciais funcionam no HDInsight abrindo o cluster URI em qualquer navegador e tentando iniciar sposição. Se as credenciais não funcionarem, pode repor-as a partir do portal Azure.

   Para o cluster ESP, reinicie a palavra-passe através da [redefinição da palavra-passe de autosserviço](../active-directory/user-help/active-directory-passwords-update-your-own-password.md).

 </br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante `502 - Web server received an invalid response while acting as a gateway or proxy server` a , este erro é devolvido pelo serviço HDInsight.

- **Recomendação**: Um erro 502 ocorre frequentemente quando o seu processo de Servidor Ambari foi desligado. Pode reiniciar os Serviços Ambari reiniciando o nó de cabeça.

    1. Ligue-se a um dos seus nós em HDInsight utilizando SSH.
    1. Identifique o seu anfitrião de nó de cabeça ativo executando `ping headnodehost` .
    1. Ligue-se ao nó de cabeça ativo enquanto o Ambari Server se encontra no nó de cabeça ativo utilizando SSH. 
    1. Reinicie o nó de cabeça ativo.

       Para obter mais informações, procure na documentação de resolução de problemas do Azure HDInsight. Por exemplo:

       * [Ambari UI 502 error](https://hdinsight.github.io/ambari/ambari-ui-502-error.html) (Erro de 502 de IU do Ambari)
       * [RpcTimeoutException para o servidor Apache Spark thrift](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Resolução de problemas de erros de gateway em Application Gateway](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Causa**: Quando a mensagem de erro contém uma mensagem semelhante `Unable to service the submit job request as templeton service is busy with too many submit job requests` ou , `Queue root.joblauncher already has 500 applications, cannot accept submission of application` muitos trabalhos estão a ser submetidos ao HDInsight ao mesmo tempo.

- **Recomendação**: Limitar o número de postos de trabalho simultâneos submetidos à HDInsight. Consulte a condência da atividade da Data Factory se os postos de trabalho estiverem a ser submetidos pela mesma atividade. Mude os gatilhos para que o gasoduto simultâneo se espalhe ao longo do tempo.

   Consulte a [documentação HDInsight](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) para ajustar `templeton.parallellism.job.submit` como o erro sugere.

### <a name="error-code-2301"></a>Código de erro: 2301

- **Mensagem:**`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa**: O cluster ou serviço HDInsight tem problemas.

- **Recomendação**: Este erro ocorre quando a ADF não recebe uma resposta do cluster HDInsight ao tentar solicitar o estado do trabalho em execução. Este problema pode estar no próprio cluster, ou o serviço HDInsight pode ter uma paragem.

   Consulte a documentação de resolução de problemas da HDInsight https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide ou contacte o seu suporte para obter mais assistência.

### <a name="error-code-2302"></a>Código de erro: 2302

- **Mensagem:**`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa**: O trabalho foi submetido ao cluster HDI e falhou lá.

- **Recomendação**: 

 1. Verifique a Ambari UI:
    1. Certifique-se de que todos os serviços ainda estão em funcionamento.
    1. A partir da UI Ambari, verifique a secção de alerta no seu painel de instrumentos.
       1. Para obter mais informações sobre alertas e resoluções para alertas, consulte [Gerir e Monitorizar um Cluster.](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)
    1. Reveja a sua memória DE YARN. Se a sua memória de YARN for alta, o processamento dos seus trabalhos pode ser adiado. Se não tiver recursos suficientes para acomodar a sua aplicação/emprego Spark, dimensione o cluster para garantir que o cluster tem memória e núcleos suficientes. 
 1. Fazer um teste de amostra.
    1. Se fizer o mesmo trabalho no backend HDInsight, verifique se conseguiu. Para exemplos de amostras, consulte [executar os exemplos mapReduce incluídos no HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Se o trabalho ainda falhar no HDInsight, verifique os registos e informações da aplicação, que fornecer para Suporte:
    1. Verifique se o trabalho foi submetido à YARN. Se o trabalho não foi submetido a fios, `--master yarn` use.
    1. Se a aplicação terminar a execução, recolha a hora de início e o fim da Aplicação YARN. Se a aplicação não tiver concluído a execução, recolha a hora de início/hora de lançamento.
    1. Verifique e recolha o registo de candidaturas com `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Verifique e recolha os registos do Gestor de Recursos de Fios sob o `/var/log/hadoop-yarn/yarn` diretório.
    1. Se estes passos não forem suficientes para resolver o problema, contacte a equipa Azure HDInsight para obter apoio e forneça os registos e os horários acima referidos.

### <a name="error-code-2303"></a>Código de erro: 2303

- **Mensagem:**`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa**: O trabalho foi submetido ao cluster HDI e falhou lá.

- **Recomendação**: 

 1. Verifique a Ambari UI:
    1. Certifique-se de que todos os serviços ainda estão em funcionamento.
    1. A partir da UI Ambari, verifique a secção de alerta no seu painel de instrumentos.
       1. Para obter mais informações sobre alertas e resoluções para alertas, consulte [Gerir e Monitorizar um Cluster.](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)
    1. Reveja a sua memória DE YARN. Se a sua memória de YARN for alta, o processamento dos seus trabalhos pode ser adiado. Se não tiver recursos suficientes para acomodar a sua aplicação/emprego Spark, dimensione o cluster para garantir que o cluster tem memória e núcleos suficientes. 
 1. Fazer um teste de amostra.
    1. Se fizer o mesmo trabalho no backend HDInsight, verifique se conseguiu. Para exemplos de amostras, consulte [executar os exemplos mapReduce incluídos no HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Se o trabalho ainda falhar no HDInsight, verifique os registos e informações da aplicação, que fornecer para Suporte:
    1. Verifique se o trabalho foi submetido à YARN. Se o trabalho não foi submetido a fios, `--master yarn` use.
    1. Se a aplicação terminar a execução, recolha a hora de início e o fim da Aplicação YARN. Se a aplicação não tiver concluído a execução, recolha a hora de início/hora de lançamento.
    1. Verifique e recolha o registo de candidaturas com `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Verifique e recolha os registos do Gestor de Recursos de Fios sob o `/var/log/hadoop-yarn/yarn` diretório.
    1. Se estes passos não forem suficientes para resolver o problema, contacte a equipa Azure HDInsight para obter apoio e forneça os registos e os horários acima referidos.

### <a name="error-code-2304"></a>Código de erro: 2304

- **Mensagem:**`MSI authentication is not supported on storages for HDI activities.`

- **Causa**: Os serviços ligados ao armazenamento utilizados no serviço ligado a HDInsight (HDI) ou na atividade HDI, são configurados com uma autenticação MSI que não é suportada.

- **Recomendação**: Fornecer cadeias de ligação completas para contas de armazenamento utilizadas no serviço ligado ao HDI ou na atividade HDI.

### <a name="error-code-2305"></a>Código de erro: 2305

- **Mensagem:**`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa**: As informações de ligação para o cluster HDI estão incorretas, o utilizador fornecido não tem permissões para executar a ação necessária, ou o serviço HDInsight tem problemas de resposta aos pedidos da ADF.

- **Recomendação**: Verifique se as informações do utilizador estão corretas e que o UI Ambari para o cluster HDI pode ser aberto num browser a partir do VM onde o IR está instalado (para um IR auto-hospedado), ou pode ser aberto a partir de qualquer máquina (para Azure IR).

### <a name="error-code-2306"></a>Código de erro: 2306

- **Mensagem:**`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: O JSON previsto para a ação do script é inválido.

- **Recomendação**: A mensagem de erro deve ajudar a identificar o problema. Corrija a configuração do json e tente novamente.

   Consulte [o serviço ligado a pedido da Azure HDInsight para](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) obter mais informações.

### <a name="error-code-2310"></a>Código de erro: 2310

- **Mensagem:**`Failed to submit Spark job. Error: '%message;'`

- **Causa**: A ADF tentou criar um lote num cluster spark usando Livy API (livy/lote), mas recebeu um erro.

- **Recomendação**: Siga a mensagem de erro para corrigir o problema. Se não houver informações suficientes para o resolver, contacte a equipa do HDI e forneça-lhes o ID do lote e o ID do trabalho, que pode ser encontrado na atividade executada output na página de monitorização ADF. Para resolver mais, recolha o registo completo do lote.

   Para obter mais informações sobre como recolher o registo completo, consulte [obter o registo completo de um lote.](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)

### <a name="error-code-2312"></a>Código de erro: 2312

- **Mensagem:**`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa:** O trabalho falhou no cluster HDInsight Spark.

- **Recomendação**: Siga os links da atividade executada Output na página de monitorização ADF para resolver problemas na execução no cluster HDInsight Spark. Contacte a equipa de apoio da HDInsight para obter mais assistência.

   Para obter mais informações sobre como recolher o registo completo, consulte [obter o registo completo de um lote.](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)

### <a name="error-code-2313"></a>Código de erro: 2313

- **Mensagem:**`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa**: O lote foi eliminado no cluster HDInsight Spark.

- **Recomendação**: Lotes de resolução de problemas no cluster HDInsight Spark. Contacte o suporte hdInsight para obter mais assistência. 

   Para obter mais informações sobre como recolher o registo completo, consulte [obter o registo completo de uma função de lote](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job), e partilhe o registo completo com o suporte hdInsight para mais assistência.

### <a name="error-code-2328"></a>Código de erro: 2328

- **Mensagem:**`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que correu mal.

- **Recomendação**: A mensagem de erro deve ajudar a resolver o problema.

### <a name="error-code-2329"></a>Código de erro: 2329

- **Mensagem:**`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: A mensagem de erro deve mostrar os detalhes do que correu mal.

- **Recomendação**: A mensagem de erro deve ajudar a resolver o problema.

### <a name="error-code-2331"></a>Código de erro: 2331

- **Mensagem:**`The file path should not be null or empty.`

- **Causa**: O caminho do ficheiro fornecido está vazio.

- **Recomendação**: Providenciar um caminho para um ficheiro que exista.

### <a name="error-code-2340"></a>Código de erro: 2340

- **Mensagem:**`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa**: O serviço ligado a HDInsightOnDemand não suporta a execução através do SelfHosted IR.

- **Recomendação**: Selecione um Azure IR e tente novamente.

### <a name="error-code-2341"></a>Código de erro: 2341

- **Mensagem:**`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa:** O URL fornecido não está no formato correto.

- **Recomendação**: Fixe o URL do cluster e tente novamente.

### <a name="error-code-2342"></a>Código de erro: 2342

- **Mensagem:**`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa:** Ou as credenciais fornecidas estão erradas para o cluster, ou houve um problema de configuração ou ligação de rede, ou o IR está a ter problemas de ligação ao cluster.

- **Recomendação**: 
    1. Verifique se as credenciais estão corretas abrindo o Ambari UI do cluster HDInsight num browser.
    1. Se o cluster estiver na Rede Virtual (VNet) e estiver a ser utilizado um IR auto-hospedado, o URL HDI deve ser o URL privado em VNets, e deve ter 'int' listado após o nome do cluster.
    
       Por exemplo, mudar `https://mycluster.azurehdinsight.net/` para `https://mycluster-int.azurehdinsight.net/` . Note o `-int` `mycluster` depois, mas antes `.azurehdinsight.net`
    1. Se o cluster estiver no VNet, o IR auto-alojado está a ser utilizado, e o URL privado foi usado, e no entanto a ligação ainda falhou, então o VM onde o IR é instalado teve problemas de ligação ao HDI. 
    
       Ligue-se ao VM onde o IR está instalado e abra o UI Ambari num browser. Utilize o URL privado para o cluster. Esta ligação deve funcionar a partir do navegador. Se não o fizer, contacte a equipa de apoio da HDInsight para mais assistência.
    1. Se o IR auto-alojado não estiver a ser utilizado, então o cluster HDI deve ser acessível publicamente. Abra a UI Ambari num browser e verifique se abre. Se houver algum problema com o cluster ou os serviços nele, contacte a equipa de apoio da HDInsight para obter assistência.

       O URL do cluster HDI utilizado no serviço ligado a ADF deve estar acessível para a ADF IR (auto-hospedada ou Azure) para que a ligação de teste passe e para as corridas ao trabalho. Este estado pode ser verificado abrindo o URL a partir de um navegador, quer a partir de VM, quer de qualquer máquina pública.

### <a name="error-code-2343"></a>Código de erro: 2343

- **Mensagem:**`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Causa:** O nome de utilizador ou a palavra-passe estão vazios.

- **Recomendação**: Forneça as credenciais corretas para ligar ao HDI e tentar novamente.

### <a name="error-code-2345"></a>Código de erro: 2345

- **Mensagem:**`Failed to read the content of the hive script. Error: '%message;'`

- **Causa:** O ficheiro de script não existe ou a ADF não conseguiu ligar-se à localização do script.

- **Recomendação**: Verifique se o script existe e que o serviço associado tem as credenciais adequadas para uma ligação.

### <a name="error-code-2346"></a>Código de erro: 2346

- **Mensagem:**`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa**: A ADF tentou estabelecer uma ligação de conectividade de base de dados aberta (ODBC) ao cluster HDI, falhando com um erro.

- **Recomendação**: 

   1. Confirme que configura corretamente a sua ligação de conectividade ODBC/Java Database (JDBC).
      1. Para o JDBC, se estiver a utilizar a mesma rede virtual, pode obter esta ligação a partir de:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Para garantir que tem o JDBC correto configurado, consulte [a Colmeia Query Apache através do controlador JDBC em HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Para a Base de Dados Aberta (ODB), consulte [Tutorial: Consulta Apache Hive com ODBC e PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) para garantir que tem a configuração correta. 
   1. Verifique se o Hiveserver2, a Hive Metastore e a Hiveserver2 Interactive estão ativas e a funcionar. 
   1. Verifique a interface de utilizador Ambari (UI):
      1. Certifique-se de que todos os serviços ainda estão em funcionamento.
      1. A partir da UI Ambari, verifique a secção de alerta no seu painel de instrumentos.
         1. Para obter mais informações sobre alertas e resoluções para alertas, consulte [Gerir e Monitorizar um Cluster. ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)
   1. Se estes passos não forem suficientes para resolver o problema, contacte a equipa Azure HDInsight.

### <a name="error-code-2347"></a>Código de erro: 2347

- **Mensagem:**`Hive execution through ODBC failed with error message '%message;'.`

- **Causa**: A ADF submeteu o script da colmeia para execução ao cluster HDI através da ligação ODBC, e o script falhou no HDI.

- **Recomendação**: 

   1. Confirme que configura corretamente a sua ligação de conectividade ODBC/Java Database (JDBC).
      1. Para o JDBC, se estiver a utilizar a mesma rede virtual, pode obter esta ligação a partir de:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Para garantir que tem o JDBC correto configurado, consulte [a Colmeia Query Apache através do controlador JDBC em HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Para a Base de Dados Aberta (ODB), consulte [Tutorial: Consulta Apache Hive com ODBC e PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) para garantir que tem a configuração correta. 
   1. Verifique se o Hiveserver2, a Hive Metastore e a Hiveserver2 Interactive estão ativas e a funcionar. 
   1. Verifique a interface de utilizador Ambari (UI):
      1. Certifique-se de que todos os serviços ainda estão em funcionamento.
      1. A partir da UI Ambari, verifique a secção de alerta no seu painel de instrumentos.
         1. Para obter mais informações sobre alertas e resoluções para alertas, consulte [Gerir e Monitorizar um Cluster. ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)
   1. Se estes passos não forem suficientes para resolver o problema, contacte a equipa Azure HDInsight.

### <a name="error-code-2348"></a>Código de erro: 2348

- **Mensagem:**`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa**: As propriedades de serviço ligadas ao armazenamento não estão corretamente definidas.

- **Recomendação**: Apenas as cordas de ligação completa são suportadas no serviço principal ligado ao armazenamento para as atividades de HDI. Verifique se não está a utilizar autorizações ou pedidos de MSI.

### <a name="error-code-2350"></a>Código de erro: 2350

- **Mensagem:**`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa**: As credenciais fornecidas para ligar ao armazenamento onde os ficheiros devem ser localizados estão incorretas ou os ficheiros não existem lá.

- **Recomendação**: Este erro ocorre quando a ADF se prepara para as atividades de HDI e tenta copiar ficheiros para o armazenamento principal antes de submeter o trabalho ao HDI. Verifique se existem ficheiros no local fornecido e se a ligação de armazenamento está correta. Como as atividades do ADF HDI não suportam a autenticação do MSI em contas de armazenamento relacionadas com as atividades de HDI, verifique se esses serviços ligados têm chaves completas ou estão a utilizar o Cofre da Chave Azure.

### <a name="error-code-2351"></a>Código de erro: 2351

- **Mensagem:**`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa:** O ficheiro não existe no caminho especificado.

- **Recomendação**: Verifique se o ficheiro realmente existe e que o serviço ligado com informações de ligação que apontam para este ficheiro tem as credenciais corretas.

### <a name="error-code-2352"></a>Código de erro: 2352

- **Mensagem:**`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa**: As propriedades de serviço ligadas ao armazenamento de ficheiros não estão corretamente definidas.

- **Recomendação**: Verifique se as propriedades do serviço ligado ao armazenamento de ficheiros estão devidamente configuradas.

### <a name="error-code-2353"></a>Código de erro: 2353

- **Mensagem:**`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa**: As propriedades de serviço ligadas ao armazenamento do script não estão definidas corretamente.

- **Recomendação**: Verifique se as propriedades do serviço ligado ao armazenamento do script estão devidamente configuradas.

### <a name="error-code-2354"></a>Código de erro: 2354

- **Mensagem:**`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa**: O tipo de serviço ligado ao armazenamento não é suportado pela atividade.

- **Recomendação**: Verifique se o serviço vinculado selecionado tem um dos tipos suportados para a atividade. As atividades da HDI apoiam os serviços ligados AzureBlobStorage e AzureBlobFSStorage.

   Para mais informações, leia [Compare as opções de armazenamento para utilização com clusters Azure HDInsight](../hdinsight/hdinsight-hadoop-compare-storage-options.md)

### <a name="error-code-2355"></a>Código de erro: 2355

- **Mensagem:**`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa**: O valor fornecido `commandEnvironment` é incorreto.

- **Recomendação**: Verifique se o valor fornecido é semelhante a:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Verifique também se cada variável aparece na lista apenas uma vez.

### <a name="error-code-2356"></a>Código de erro: 2356

- **Mensagem:**`The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa**: O valor fornecido `commandEnvironment` é incorreto.

- **Recomendação**: Verifique se o valor fornecido é semelhante a:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Verifique também se cada variável aparece na lista apenas uma vez.

### <a name="error-code-2357"></a>Código de erro: 2357

- **Mensagem:**`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa**: As credenciais fornecidas estão incorretas.

- **Recomendação**: Verifique se as informações de ligação na ADLS Gen 1 ligadas ao serviço e verifique se a ligação de teste é bem sucedida.

### <a name="error-code-2358"></a>Código de erro: 2358

- **Mensagem:**`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa**: O valor fornecido para a propriedade requerida `TimeToLive` tem um formato inválido. 

- **Recomendação**: Atualizar o valor para o intervalo sugerido e tentar novamente.

### <a name="error-code-2359"></a>Código de erro: 2359

- **Mensagem:**`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa**: O valor fornecido para o `roles` imóvel é inválido.

- **Recomendação**: Atualizar o valor para ser uma das sugestões e tentar novamente.

### <a name="error-code-2360"></a>Código de erro: 2360

- **Mensagem:**`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa**: A cadeia de ligação fornecida para o `HCatalogLinkedService` é inválida.

- **Recomendação**: Atualize o valor para uma cadeia de ligação Azure SQL correta e tente novamente.

### <a name="error-code-2361"></a>Código de erro: 2361

- **Mensagem:**`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa**: A criação do cluster falhou e a ADF não recuperou um erro do serviço HDInsight.

- **Recomendação**: Abra o portal Azure e tente encontrar o recurso HDI com o nome fornecido e, em seguida, verifique o estado de provisionamento. Contacte a equipa de apoio da HDInsight para obter mais assistência.

### <a name="error-code-2362"></a>Código de erro: 2362

- **Mensagem:**`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa**: O armazenamento adicional fornecido não era o armazenamento Azure Blob.

- **Recomendação**: Forneça uma conta de armazenamento Azure Blob como um armazenamento adicional para o serviço ligado a pedido hdInsight.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>Erro SSL quando o serviço ligado a ADF utilizando o cluster HDInsight ESP

- **Mensagem:**`Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **Causa**: O problema está provavelmente relacionado com a System Trust Store.

- **Resolução**: Pode navegar para o caminho **Microsoft Integration Runtime\4.0\Shared\ODBC Drivers\Microsoft Hive ODBC Driver\lib** e abrir DriverConfiguration64.exe para alterar a definição.

    ![Descheck Use System Trust Store](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Atividade Web

### <a name="error-code-2128"></a>Código de erro: 2128

- **Mensagem:**`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Este problema deve-se à conectividade da Rede, a uma falha de DNS, a uma validação de certificado de servidor ou a um intervalo.

- **Recomendação**: Valide que o ponto final que está a tentar atingir está a responder aos pedidos. Pode utilizar ferramentas como **Violinista/Carteiro.**

### <a name="error-code-2108"></a>Código de erro: 2108

- **Mensagem:**`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: O pedido falhou devido a um problema subjacente, como conectividade de rede, falha de DNS, validação de certificado de servidor ou tempo limite.

- **Recomendação**: Utilize o Violinista/Carteiro para validar o pedido.

#### <a name="more-details"></a>Mais detalhes
Utilizar **o Fiddler** para criar uma sessão HTTP da aplicação web monitorizada:

1. Descarregue, instale e abra [o Violinista.](https://www.telerik.com/download/fiddler)

1. Se a sua aplicação web utilizar HTTPS, aceda a  >  **Opções de Violino de**  >  **Ferramentas HTTPS**.

   1. No separador HTTPS, selecione ambos **os CONNECTs de captura** e **desencriptar o tráfego HTTPS**.

      ![Opções de violino](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se a sua aplicação utilizar certificados TLS/SSL, adicione o certificado Fiddler ao seu dispositivo.

   Ir para: **Opções** de Violino de  >    >  **Ferramentas HTTPS**  >  **Actions**  >  **Export Root Certificate to Desktop**.

1. Desligue a captura indo para o   >  **Tráfego de Captura de Ficheiros**. Ou prima **F12**.

1. Limpe a cache do seu navegador para que todos os itens em cache sejam removidos e devem ser descarregados novamente.

1. Criar um pedido:

1. Selecione o **separador Compositor.**

   1. Desa estada o método HTTP e URL.
 
   1. Se necessário, adicione cabeçalhos e um corpo de pedido.

   1. Selecione **Execute** (Executar).

1. Ligue a captura de tráfego novamente e complete a transação problemática na sua página.

1. Ir para: **Arquivar**  >  **Guardar**  >  **todas as sessões**.

Para mais informações, consulte [Começar com o Violinista.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)

## <a name="general"></a>Geral

### <a name="activity-stuck-issue"></a>Problema de atividade preso

Quando observa que a atividade está a correr muito mais tempo do que o normal, sem progressos, pode estar presa. Pode tentar cancelá-lo e tentar novamente para ver se ajuda. Se for uma atividade de cópia, pode aprender sobre a monitorização de desempenho e resolução de problemas do desempenho da atividade da cópia de resolução de [problemas;](copy-activity-performance-troubleshooting.md) se for um fluxo de dados, aprenda com [o mapeamento de dados flui desempenho](concepts-data-flow-performance.md) e guia de afinação.

### <a name="payload-is-too-large"></a>A carga é muito grande.

**Mensagem de erro:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**Causa:** A carga útil para cada execução de atividade inclui a configuração de atividade, as configurações de conjuntos de dados associados e as configurações de serviços ligados, se houver, e uma pequena porção de propriedades do sistema geradas por tipo de atividade. O limite deste tamanho de carga útil é de 896 KB, tal como mencionado na secção [de limites da Data Factory.](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits)

**Recomendação:** Você atinge este limite provavelmente porque você passa em um ou mais valores de parâmetros grandes de saída de atividade a montante ou externo, especialmente se você passar dados reais através de atividades no fluxo de controle. Verifique se consegue reduzir o tamanho dos grandes valores de parâmetros ou afinar a lógica do pipeline para evitar passar esses valores através das atividades e manuseá-lo dentro da atividade.

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

* [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow Forum para a Fábrica de Dados](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
* [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
