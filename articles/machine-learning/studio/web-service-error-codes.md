---
title: Códigos de erro da API REST-Azure Machine Learning Studio (clássico) | Microsoft Docs
description: Esses códigos de erro podem ser retornados por uma operação em um serviço Web Azure Machine Learning.
keywords: ''
services: machine-learning
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 15e73740f5d932d0fa4cfb9a56c9fe7b155555cc
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73468156"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Códigos de erro da API REST do Azure Machine Learning Studio (clássico)
 
Os códigos de erro a seguir podem ser retornados por uma operação em um serviço Web Azure Machine Learning Studio (clássico).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (código de status HTTP 400)
 
Argumento inválido fornecido.
 
Essa classe de erros significa que um argumento fornecido em algum lugar era inválido. Isso pode ser uma credencial ou local do armazenamento do Azure para algo passado para o serviço Web. Consulte o campo "código" do erro na seção "detalhes" para diagnosticar qual argumento específico era inválido.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| BadParameterValue | O valor do parâmetro fornecido não satisfaz a regra de parâmetro no parâmetro |
| BadSubscriptionId | A ID da assinatura que é usada para pontuar não é a presente no recurso |
| BadVersionCall | Foi passado um parâmetro de versão inválido durante a chamada à API: {0}. Verifique a página de ajuda da API para passar a versão correta e tente novamente. |
| BatchJobInputsNotSpecified | As entradas necessárias a seguir não foram especificadas com a solicitação: {0}. Verifique se todos os dados de entrada estão especificados e tente novamente. |
| BatchJobInputsTooManySpecified | A solicitação especificou mais entradas do que o definido no serviço. Lista de entradas aceitas: {0}. Verifique se todos os dados de entrada estão especificados corretamente e tente novamente. |
| BlobNameTooLong | O caminho do armazenamento de BLOBs do Azure fornecido para a saída de diagnóstico é muito longo: {0}. Encurte o caminho e tente novamente. |
| BlobNotFound | Não é possível acessar o blob do Azure fornecido-{0}.  Mensagem de erro do Azure: {1}. |
| ContainerIsEmpty | Nenhum nome de contêiner de armazenamento do Azure foi fornecido. Forneça um nome de contêiner válido e tente novamente. |
| ContainerSegmentInvalid | Nome de contêiner inválido. Forneça um nome de contêiner válido e tente novamente. |
| ContainerValidationFailed | Falha na validação do contêiner de BLOBs com este erro: {0}. |
| DataTypeNotSupported | Tipo de dados sem suporte fornecido. Forneça os tipos de dados válidos e tente novamente. |
| DuplicateInputInBatchCall | A solicitação em lote é inválida. Não é possível especificar uma entrada única e várias entradas ao mesmo tempo. Remova um desses itens da solicitação e tente novamente. |
| ExpiryTimeInThePast | O tempo de expiração fornecido está no passado: {0}. Forneça um tempo de expiração futuro em UTC e tente novamente. Para nunca expirar, defina o tempo de expiração como nulo. |
| IncompleteSettings | As configurações de diagnóstico estão incompletas. |
| InputBlobRelativeLocationInvalid | Nenhum nome de blob de armazenamento do Azure fornecido. Forneça um nome de blob válido e tente novamente. |
| InvalidBlob | Especificação de blob inválida para blob: {0}. Verifique se a cadeia de conexão/caminho relativo ou especificação de token SAS está correta e tente novamente. |
| InvalidBlobConnectionString | A cadeia de conexão especificada para um dos BLOBs de entrada/saída é inválida: {0}. Corrija isso e tente novamente. |
| InvalidBlobExtension | A referência de blob: {0} tem uma extensão de arquivo inválida ou ausente. As extensões de arquivo com suporte para esse tipo de saída são: "{1}". |
| InvalidInputNames | Nomes de entrada de serviço inválidos especificados na solicitação: {0}. Mapeie os dados de entrada para as entradas de serviço corretas e tente novamente. |
| InvalidOutputOverrideName | Nome de substituição de saída inválido: {0}. O serviço não tem um nó de saída com esse nome. Passe um nome de nó de saída correto para substituição (a diferenciação de maiúsculas e minúsculas se aplica). |
| InvalidQueryParameter | Parâmetro de consulta '{0}' inválido. {1} |
| MissingInputBlobInformation | Informações do blob de armazenamento do Azure ausentes. Forneça uma cadeia de conexão válida e um caminho relativo ou URI e tente novamente. |
| MissingJobId | Nenhuma ID de trabalho fornecida. Uma ID de trabalho é retornada quando um trabalho foi enviado pela primeira vez. Verifique se a ID do trabalho está correta e tente novamente. |
| MissingKeys | Nenhuma chave fornecida ou uma das chaves primárias ou secundárias não foi fornecida. |
| MissingModelPackage | Nenhuma ID de pacote de modelo ou pacote de modelo fornecido. Forneça uma ID de pacote de modelo ou pacote de modelo válido e tente novamente. |
| MissingOutputOverrideSpecification | A solicitação não tem a especificação de BLOB para a substituição de saída {0}. Especifique um local de blob válido com a solicitação ou remova a especificação de saída se nenhuma substituição de local for desejada. |
| MissingRequestInput | O serviço Web espera uma entrada, mas nenhuma entrada foi fornecida. Verifique se as entradas válidas são fornecidas com base nas portas de entrada publicadas no modelo e tente novamente. |
| MissingRequiredGlobalParameters | Nem todos os parâmetros de serviço Web necessários foram fornecidos. Verifique se os parâmetros esperados para os módulos estão corretos e tente novamente. |
| MissingRequiredOutputOverrides | Ao chamar um ponto de extremidade de serviço criptografado, é obrigatório transmitir substituições de saída para todas as saídas do serviço. Substituições ausentes neste momento para estas saídas: {0} |
| MissingWebServiceGroupId | Nenhuma ID de grupo de serviço Web fornecida. Forneça uma ID de grupo de serviço Web válida e tente novamente. |
| MissingWebServiceId | Nenhuma ID de serviço Web fornecida. Forneça uma ID de serviço da Web válida e tente novamente. |
| MissingWebServicePackage | Nenhum pacote de serviço Web fornecido. Forneça um pacote de serviço Web válido e tente novamente. |
| MissingWorkspaceId | Nenhuma ID de espaço de trabalho fornecida. Forneça uma ID de espaço de trabalho válida e tente novamente. |
| ModelConfigurationInvalid | Configuração de modelo inválida no pacote de modelo. Certifique-se de que a configuração de modelo contenha definição de ponto de extremidade de saída, ponto de extremidade de erro std e ponto de extremidade de padrão e tente novamente. |
| ModelPackageIdInvalid | ID de pacote de modelo inválida. Verifique se a ID do pacote de modelo está correta e tente novamente. |
| RequestBodyInvalid | Nenhum corpo de solicitação fornecido ou erro ao desserializar o corpo da solicitação. |
| RequestIsEmpty | Nenhuma solicitação fornecida. Forneça uma solicitação válida e tente novamente. |
| UnexpectedParameter | Parâmetros inesperados fornecidos. Verifique se todos os nomes de parâmetro estão escritos corretamente, se apenas os parâmetros esperados são passados e tente novamente. |
| UnknownError | Erro desconhecido. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Não é possível alterar os requisitos de solicitações simultâneas para o serviço Web {0}. |
| WebServiceIdInvalid | ID de serviço Web inválida fornecida. A ID do serviço Web deve ser um GUID válido. |
| WebServiceTooManyConcurrentRequestRequirement | Não é possível definir o requisito de solicitação simultânea para mais de {0}. |
| WebServiceTypeInvalid | Tipo de serviço da Web inválido fornecido. Verifique se o tipo de serviço Web válido está correto e tente novamente. Tipos de serviço Web válidos: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (código de status HTTP 400)
 
Argumento de usuário inválido fornecido.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| InputMismatchError | Os dados de entrada não correspondem ao esquema de porta de entrada. |
| InputParseError | Falha na análise do vetor de entrada.  Verifique se o vetor de entrada tem o número correto de colunas e tipos de dados.  Detalhes adicionais: {0}. |
| MissingRequiredGlobalParameters | Os parâmetros esperados pelo serviço Web estão ausentes. Verifique se todos os parâmetros necessários esperados pelo serviço Web estão corretos e tente novamente. |
| UnexpectedParameter | Verifique se apenas os parâmetros necessários esperados pelo serviço Web foram passados e tente novamente. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (código de status HTTP 400)
 
A solicitação é inválida no contexto atual.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| CannotStartJob | O trabalho não pode ser iniciado porque está no estado {0}. |
| IncompatibleModel | O modelo é incompatível com a versão de solicitação. A versão de solicitação dá suporte apenas a modelos de saída de DataTable únicos. |
| MultipleInputsNotAllowed | O modelo não permite várias entradas. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (código de status HTTP 400)
 
A execução do módulo encontrou um erro de biblioteca interno.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (código de status HTTP 400)
 
A execução do módulo encontrou um erro.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (código de status HTTP 400)
 
Pacote de serviço Web inválido. Verifique se o pacote de serviço Web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| FormatError | O pacote do serviço Web está malformado. Detalhes: {0} |
| RuntimesError | O grafo do pacote de serviço Web é inválido. Detalhes: {0} |
| ValidationError | O grafo do pacote de serviço Web é inválido. Detalhes: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Não autorizado (código de status HTTP 401)
 
A solicitação não está autorizada a acessar o recurso.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| AdminRequestUnauthorized | Não autorizado |
| ManagementRequestUnauthorized | Não autorizado |
| ScoreRequestUnauthorized | Credenciais inválidas fornecidas. |
 
## <a name="notfound-http-status-code-404"></a>Não encontrado (código de status HTTP 404)
 
Recurso não encontrado.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| ModelPackageNotFound | Pacote de modelo não encontrado. Verifique se a ID do pacote de modelo está correta e tente novamente. |
| WebServiceIdNotFoundInWorkspace | O serviço Web neste espaço de trabalho não foi encontrado. Há uma incompatibilidade entre webserviceid e workspaceid. Verifique se o serviço Web fornecido faz parte do espaço de trabalho e tente novamente. |
| WebServiceNotFound | Serviço Web não encontrado. Verifique se a ID do serviço Web está correta e tente novamente. |
| WorkspaceNotFound | Espaço de trabalho não encontrado. Verifique se a ID do espaço de trabalho está correta e tente novamente. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (código de status HTTP 408)
 
A operação não pôde ser concluída dentro do tempo permitido.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| RequestCanceled | A solicitação foi cancelada pelo cliente. |
| ScoreRequestTimeout | A solicitação de execução atingiu o tempo limite. |
 
## <a name="conflict-http-status-code-409"></a>Conflito (código de status HTTP 409)
 
O recurso já existe.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nome de parâmetro de saída inválido. Tente usar o módulo editor de metadados para renomear as colunas e tente novamente. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (código de status HTTP 413)
 
O modelo excedeu a cota de memória atribuída a ele.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| OutOfMemoryLimit | O modelo consumiu mais memória do que foi apropriado para ele. A memória máxima permitida para o modelo é {0} MB. Verifique se há problemas no seu modelo. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (código de status HTTP 500)
 
A execução encontrou um erro interno.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | O processo de contêiner falhou com erro do sistema |
| ContainerProcessTerminatedWithUnknownError | O processo de contêiner falhou com erro desconhecido |
| ContainerValidationFailed | Falha na validação do contêiner de BLOBs com este erro: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nenhum argumento fornecido. Verifique se os argumentos válidos são passados e tente novamente. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | ID da porta ={0} tem um tipo de dados sem suporte: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Falha na geração de Swagger, detalhes: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Código de status de trabalho desconhecido {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, detalhes: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (código de status HTTP 500)
 
A execução encontrou um erro interno. Sistema com pouca memória. Tente novamente.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (código de status HTTP 500)
 
Pacote de modelo inválido. Verifique se o pacote de modelo fornecido está correto e tente novamente.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (código de status HTTP 500)
 
Pacote de serviço Web inválido. Verifique se o pacote da Web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| ModuleError | O grafo do pacote de serviço Web é inválido. Detalhes: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (código de status HTTP 503)
 
A solicitação não pode ser executada porque os contêineres estão sendo inicializados.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (código de status HTTP 503)
 
O serviço está temporariamente indisponível.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| NoMoreResources | Não há recursos disponíveis para solicitação. |
| RequestThrottled | A solicitação foi limitada por {0} ponto de extremidade. A simultaneidade máxima para o ponto de extremidade é {1}. |
| TooManyConcurrentRequests | Muitas solicitações simultâneas enviadas. |
| TooManyHostsBeingInitialized | Muitos hosts sendo inicializados ao mesmo tempo. Considere a limitação/repetição. |
| TooManyHostsBeingInitializedPerModel | Muitos hosts sendo inicializados ao mesmo tempo. Considere a limitação/repetição. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (código de status HTTP 504)
 
A operação não pôde ser concluída dentro do tempo permitido.
 
| Código de erro | Mensagem do usuário |
| ---------- |--------------|
| BackendInitializationTimeout | A inicialização do serviço Web não pôde ser concluída dentro do tempo permitido. |
| BackendScoreTimeout | A execução da solicitação do serviço Web não pôde ser concluída dentro do tempo permitido. |
 
