---
title: Códigos de erro de REST API - Azure Machine Learning Studio | Documentos da Microsoft
description: Esses códigos de erro poderá ser retornados por uma operação num serviço web Azure Machine Learning.
keywords: ''
services: machine-learning
documentationcenter: ''
author: xiaoharper
ms.custom: seodec18
ms.author: amlstudiodocs
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: 8e91d0cd68997dee9bb00cceeaa6b697f6644ee5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58100960"
---
# <a name="azure-machine-learning-studio-rest-api-error-codes"></a>Códigos de erro de API de REST do Azure Machine Learning Studio
 
Os seguintes códigos de erro poderá ser retornados por uma operação num serviço web Azure Machine Learning Studio.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (código de estado HTTP 400)
 
Argumento inválido fornecido.
 
Essa classe de erros significa um argumento em algum lugar fornecido era inválido. Isto pode ser uma credencial ou a localização de armazenamento do Azure para algo passado para o serviço web. Veja o campo de "código" Erro na secção "Detalhes" para diagnosticar o argumento específico era inválido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| BadParameterValue | O valor do parâmetro fornecido não satisfaz a regra de parâmetro no parâmetro |
| BadSubscriptionId | O Id que é utilizado para pontuação da subscrição não é a presente no recurso |
| BadVersionCall | Parâmetro de versão inválido foi passado durante a chamada de API: {0}. Verifique a página de ajuda da API para transmitir a versão correta e tente novamente. |
| BatchJobInputsNotSpecified | Os seguintes necessários input(s) não foram especificadas com o pedido: {0}. Certifique-se de todos os dados de entrada é especificado e tente novamente. |
| BatchJobInputsTooManySpecified | O pedido especificado entradas mais do que definido no serviço. Lista de input(s) aceites: {0}. Certifique-se de todos os dados de entrada está corretamente especificada e tente novamente. |
| BlobNameTooLong | Caminho de armazenamento de Blobs do Azure fornecido para a saída de diagnóstico é demasiado longa: {0}. Encurte o caminho e tente novamente. |
| BlobNotFound | Não é possível aceder a BLOBs do Azure fornecida - {0}.  Mensagem de erro do Azure: {1}. |
| ContainerIsEmpty | Foi fornecido nenhum nome de contentor de armazenamento do Azure. Forneça um nome de contentor válido e tente novamente. |
| ContainerSegmentInvalid | Nome de contentor inválido. Forneça um nome de contentor válido e tente novamente. |
| ContainerValidationFailed | Falha com este erro de validação do contentor de BLOBs: {0}. |
| DataTypeNotSupported | Foi fornecido um tipo de dados não suportado. Forneça o tipo ou tipos de dados válido e tente novamente. |
| DuplicateInputInBatchCall | O pedido de batch é inválido. Não é possível especificar a entrada de uma ou várias ao mesmo tempo. Remova um desses itens do pedido e tente novamente. |
| ExpiryTimeInThePast | Hora de expiração indicada está no passado: {0}. Forneça uma hora de expiração futura em UTC e tente novamente. Para nunca expirar, definir a hora de expiração como NULL. |
| IncompleteSettings | As definições de diagnóstico estão incompletas. |
| InputBlobRelativeLocationInvalid | Nenhum nome de blob de armazenamento do Azure fornecida. Forneça um nome de blob válido e tente novamente. |
| InvalidBlob | Especificação de blob inválido para o blob: {0}. Verifique se essa cadeia de ligação / caminho relativo ou especificação do token SAS está correta e tente novamente. |
| InvalidBlobConnectionString | A cadeia de ligação especificada para um dos blobs de entrada/saída no inválido: {0}. Corrija isso e tente novamente. |
| InvalidBlobExtension | A referência de blob: {0} tem uma extensão de ficheiro inválido ou está em falta. Extensões de ficheiro é suportado para este tipo de saída são: "{1}". |
| InvalidInputNames | Nome (s) especificada no pedido de entrada de serviço inválido: {0}. . Mapear os dados de entrada para as entradas de serviço correto e tente novamente. |
| InvalidOutputOverrideName | Nome de substituição de saída inválido: {0}. O serviço não tem um nó de saída com este nome. Transmita um nome de nó de saída corretos para substituir (aplicável de maiúsculas e minúsculas). |
| InvalidQueryParameter | Parâmetro de consulta inválida '{0}'. {1} |
| MissingInputBlobInformation | Não dispõe de informações de blob de armazenamento do Azure. Forneça uma cadeia de ligação válida e o caminho relativo ou um URI e tente novamente. |
| MissingJobId | Nenhuma tarefa Id fornecido. Uma tarefa de Id é retornado quando uma tarefa foi submetida pela primeira vez. Verifique se que o Id da tarefa está correto e tente novamente. |
| MissingKeys | Não existem chaves fornecidos ou um dos principal ou a chave secundária não for fornecido. |
| MissingModelPackage | Nenhum Id de pacote do modelo ou o pacote de modelo fornecido. Forneça um Id de pacote de modelo válido ou pacote de modelo e tente novamente. |
| MissingOutputOverrideSpecification | O pedido não tem a especificação de BLOBs para substituição de saída {0}. Especifique uma localização de blob válido com o pedido ou remova a especificação de saída, se for o pretendido sem substituição de localização. |
| MissingRequestInput | O web service espera uma entrada, mas nenhuma entrada foi fornecida. Certifique-se entradas válidas são fornecidas com base nas portas de entrada publicadas no modelo e tente novamente. |
| MissingRequiredGlobalParameters | Nem todos necessário o parâmetro ou parâmetros a web do serviço fornecidos. Verifique se o parâmetro ou parâmetros esperado para os módulos estão correto e tente novamente. |
| MissingRequiredOutputOverrides | Ao chamar um ponto de extremidade de serviço encriptada é obrigatório para passar na saída substituições para saídas do serviço. Substituições em falta neste momento para essas saídas: {0} |
| MissingWebServiceGroupId | Nenhum grupo de serviço web Id fornecido. Forneça um Id do grupo de serviço de web válido e tente novamente. |
| MissingWebServiceId | Nenhum serviço da web Id fornecido. Forneça um Id de serviço web válido e tente novamente. |
| MissingWebServicePackage | Nenhum pacote de serviço web fornecida. Forneça um pacote de serviço web válido e tente novamente. |
| MissingWorkspaceId | Nenhuma área de trabalho Id fornecido. Forneça uma Id de área de trabalho válida e tente novamente. |
| ModelConfigurationInvalid | Configuração de modelo inválido no pacote de modelo. Certifique-se de que a configuração de modelo contém a definição de ponto de saída, ponto de extremidade de erro e padrão, e std Descubra ponto final e tente novamente. |
| ModelPackageIdInvalid | Pacote de modelo inválido de ID. Certifique-se de que o Id do pacote de modelo está correto e tente novamente. |
| RequestBodyInvalid | Nenhum corpo de pedido fornecido ou um erro ao anular serialização do corpo do pedido. |
| RequestIsEmpty | Nenhum pedido fornecido. Forneça uma solicitação válida e tente novamente. |
| UnexpectedParameter | Inesperado parâmetros fornecidos. Certifique-se de que todos os nomes de parâmetro são escritos corretamente, apenas os parâmetros esperados são passados e tente novamente. |
| UnknownError | Erro desconhecido. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Não é possível alterar os requisitos de pedidos simultâneos para {0} serviço web. |
| WebServiceIdInvalid | Id de serviço web inválido fornecido. Id de serviço Web deve ser um guid válido. |
| WebServiceTooManyConcurrentRequestRequirement | Não é possível definir o requisito de pedido simultâneo mais do que {0}. |
| WebServiceTypeInvalid | Tipo de serviço web inválido fornecido. Verifique se que o tipo de serviço web válido está correto e tente novamente. Tipos de serviço da web válido: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (código de estado HTTP 400)
 
Argumento de utilizador inválido fornecido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| InputMismatchError | Dados de entrada não corresponde ao esquema de porta de entrada. |
| InputParseError | Análise do vetor de entrada falhou.  Certifique-se de que o vetor de entrada tem o número correto de colunas e tipos de dados.  Detalhes adicionais: {0}. |
| MissingRequiredGlobalParameters | Parâmetro (s) esperado pelo serviço web está em falta. Verifique se todos os parâmetros necessários esperados pelo serviço web estão corretos e tente novamente. |
| UnexpectedParameter | Certifique-se de que apenas os parâmetros necessários esperados pelo serviço da web são passados e tente novamente. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>Operaçãoinválida (código de estado HTTP 400)
 
O pedido é inválido no contexto atual.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| CannotStartJob | Não é possível iniciar a tarefa porque está a ser {0} estado. |
| IncompatibleModel | O modelo é incompatível com a versão do pedido. A versão de solicitação suporta apenas a modelos de saída do datatable único. |
| MultipleInputsNotAllowed | O modelo não permite várias entradas. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (código de estado HTTP 400)
 
Execução do módulo encontrou um erro de biblioteca interna.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (código de estado HTTP 400)
 
Execução do módulo encontrou um erro.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (código de estado HTTP 400)
 
Pacote de serviço web inválido. Verifique se o pacote de serviço da web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| FormatError | O pacote de serviço web tem um formato incorreto. Detalhes: {0} |
| RuntimesError | O gráfico de pacote de serviço web é inválido. Detalhes: {0} |
| ValidationError | O gráfico de pacote de serviço web é inválido. Detalhes: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Não autorizado (código de estado HTTP 401)
 
Pedido não está autorizado a recursos de acesso.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| AdminRequestUnauthorized | Não autorizado |
| ManagementRequestUnauthorized | Não autorizado |
| ScoreRequestUnauthorized | Foram fornecidas credenciais inválidas. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (código de estado HTTP 404)
 
Recurso não encontrado.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModelPackageNotFound | Pacote de modelo não encontrado. Verifique se que o Id do pacote de modelo está correto e tente novamente. |
| WebServiceIdNotFoundInWorkspace | Serviço Web sob esta área de trabalho não foi encontrado. Existe um erro de correspondência entre o webServiceId e o workspaceId. Certifique-se de que o serviço web fornecido faz parte da área de trabalho e tente novamente. |
| WebServiceNotFound | Serviço Web não foi encontrado. Verifique se que o Id de serviço web está correto e tente novamente. |
| WorkspaceNotFound | A área de trabalho não foi encontrada. Verifique se que o Id de área de trabalho está correta e tente novamente. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (código de estado HTTP 408)
 
Não foi possível concluir a operação no tempo permitido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| RequestCanceled | O pedido foi cancelado pelo cliente. |
| ScoreRequestTimeout | Pedido de execução excedeu o tempo limite. |
 
## <a name="conflict-http-status-code-409"></a>Conflito (código de estado HTTP 409)
 
Já existe um recurso.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nome do parâmetro de saída inválido. Tente utilizar o módulo de editor de metadados para mudar o nome de colunas e tente novamente. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (código de estado HTTP 413)
 
O modelo tinha excedeu a quota de memória atribuída à mesma.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| OutOfMemoryLimit | O modelo de consumir mais memória do que foi posse para ele. Memória máxima permitida para o modelo é {0} MB. Verifique o seu modelo para problemas. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (código de estado HTTP 500)
 
A execução encontrou um erro interno.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | O processo de contentor falhado com erro de sistema |
| ContainerProcessTerminatedWithUnknownError | O processo de contentor falhado com erro desconhecido |
| ContainerValidationFailed | Falha com este erro de validação do contentor de BLOBs: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Nenhum argumento fornecido. Certifique-se de que os argumentos válidos são passados e tente novamente. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | Id da porta ={0} tem um tipo de dados não suportado: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | Falha na geração do swagger, detalhes: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Código de estado de tarefa desconhecido {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, detalhes: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (código de estado HTTP 500)
 
A execução encontrou um erro interno. Sistema de memória insuficiente. Tente novamente.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (código de estado HTTP 500)
 
Pacote de modelo inválido. Verifique se o pacote de modelo fornecido está correto e tente novamente.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (código de estado HTTP 500)
 
Pacote de serviço web inválido. Verifique se o pacote de web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModuleError | O gráfico de pacote de serviço web é inválido. Detalhes: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (código de estado HTTP 503)
 
O pedido não é possível executar como os contentores estão a ser inicializados.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (código de estado HTTP 503)
 
Serviço está temporariamente indisponível.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| NoMoreResources | Não existem recursos disponíveis para o pedido. |
| RequestThrottled | O pedido foi limitado para {0} ponto final. A concorrência máxima para o ponto final é {1}. |
| TooManyConcurrentRequests | Demasiados pedidos simultâneos enviados. |
| TooManyHostsBeingInitialized | Demasiados anfitriões que está a ser inicializados ao mesmo tempo. Considere a limitação / repetir a operação. |
| TooManyHostsBeingInitializedPerModel | Demasiados anfitriões que está a ser inicializados ao mesmo tempo. Considere a limitação / repetir a operação. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (código de estado HTTP 504)
 
Não foi possível concluir a operação no tempo permitido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| BackendInitializationTimeout | Não foi possível concluir a inicialização do serviço web no tempo permitido. |
| BackendScoreTimeout | Não foi possível concluir a execução de pedido de serviço web no tempo permitido. |
 
