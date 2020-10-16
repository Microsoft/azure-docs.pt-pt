---
title: 'ML Studio (clássico): CÓDIGOS de erro REST API - Azure'
description: Estes códigos de erro podem ser devolvidos através de uma operação num serviço web Azure Machine Learning.
keywords: ''
services: machine-learning
author: likebupt
ms.author: keli19
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.subservice: studio
ms.topic: reference
ms.date: 11/16/2016
ms.openlocfilehash: c48c59db2d9b830367276d39a82bc7fc8fdc34d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91357255"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (clássico) REST API Error Codes

**APLICA-SE A:** ![ Aplica-se a. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (clássico) ![ Não se aplica a.](../../../includes/media/aml-applies-to-skus/no.png)[ Aprendizagem de Máquinas Azure](../compare-azure-ml-to-studio-classic.md)  


Os seguintes códigos de erro podem ser devolvidos através de uma operação num serviço web Azure Machine Learning Studio (clássico).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (código de estado HTTP 400)
 
Argumento inválido fornecido.
 
Esta classe de erros significa que um argumento fornecido em algum lugar era inválido. Isto pode ser uma credencial ou localização do armazenamento Azure para algo passado para o serviço web. Por favor, consulte o campo "código" de erro na secção "detalhes" para diagnosticar qual argumento específico era inválido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| BadParameterValue | O valor do parâmetro fornecido não satisfaz a regra do parâmetro no parâmetro |
| BadSubscriptionId | O Id de subscrição que é usado para pontuar não é o presente no recurso |
| BadVersionCall | O parâmetro da versão inválida foi aprovado durante a chamada da API: {0} . Consulte a página de ajuda da API para passar a versão correta e tente novamente. |
| BatchJobInputsNotSpecified | As seguintes entradas requeridas não foram especificadas com o pedido: {0} . Certifique-se de que todos os dados de entrada estão especificados e tente novamente. |
| BatchJobInputsTooManySpecified | O pedido especificou mais entradas do que definidas no serviço. Lista de entradas aceites: {0} . Certifique-se de que todos os dados de entrada estão corretamente especificados e tente novamente. |
| BlobNameTooLong | O caminho de armazenamento do blob azul previsto para a saída de diagnóstico é demasiado longo: {0} . Encurtar o caminho e tentar de novo. |
| BlobNotFound | Incapaz de aceder à bolha Azure fornecida - {0} .  Mensagem de erro azul: {1} . |
| ContentoresEmptia | Não foi fornecido nenhum nome do contentor de armazenamento Azure. Forneça um nome de recipiente válido e tente novamente. |
| ContainerSegmentInvalid | Nome do recipiente inválido. Forneça um nome de recipiente válido e tente novamente. |
| ContainerValidationFailed | A validação do contentor blob falhou com este erro: {0} . |
| DataTypeNotSupporto | Tipo de dados não suportado fornecido. Forneça os tipos de dados válidos e tente novamente. |
| DuplicateInputInBatchCall | O pedido do lote é inválido. Não é possível especificar a entrada única e múltipla ao mesmo tempo. Retire um destes itens do pedido e tente novamente. |
| ExpiraçãoTimeInThePast | O prazo de validade previsto é no passado: {0} . Forneça um tempo de validade futuro na UTC e tente novamente. Para nunca expirar, desloque o prazo de validade para NU. |
| Incompletas | As definições de diagnóstico estão incompletas. |
| InputBlobRelativeLocationInvalid | Não é fornecido nenhum nome de bolha de armazenamento Azure. Forneça um nome de bolha válido e tente novamente. |
| Inválido | Especificação de bolha inválida para bolha: {0} . Verifique se o fio de ligação / caminho relativo ou especificação de símbolo SAS está correto e tente novamente. |
| InvalidBlobConnectionString | O fio de ligação especificado para uma das bolhas de entrada/saída inválidas: {0} . Por favor, corrija isto e tente de novo. |
| InvalidBlobExtension | A referência blob: {0} tem uma extensão de ficheiro inválida ou em falta. As extensões de ficheiros suportadas para este tipo de saída são: " {1} " |
| Inválidos InputNames | Nomes de entrada de serviço inválidos especificados no pedido: {0} . Por favor, mapeear os dados de entrada para as entradas de serviço corretas e tente novamente. |
| InvalidOutputOverrideName | Nome de substituição de saída inválida: {0} . O serviço não tem um nó de saída com este nome. Por favor, passe um nome de nó de saída correto para substituir (aplica-se sensibilidade ao caso). |
| InvalidQueryParameter | Parâmetro de consulta inválido {0} '. {1} |
| FaltainputInbInformation | Informação de blob de armazenamento de Azure desaparecida. Forneça uma cadeia de ligação válida e um caminho relativo ou URI e tente novamente. |
| Faltando o Trabalho | Nenhum trabalho que me seja fornecido. Um id de trabalho é devolvido quando um trabalho foi submetido pela primeira vez. Verifique se o id do trabalho está correto e tente novamente. |
| Mãos Desaparecidas | Não são fornecidas chaves fornecidas ou uma das teclas primárias ou secundárias. |
| Pacote deModel Missing | Não é fornecido o pacote de modelos Id ou pacote de modelos. Forneça um pacote de modelo válido Id ou pacote de modelo e tente novamente. |
| Falta deoutputEspecificação | O pedido não é a especificação blob para a sobreposição de saída {0} . Por favor, especifique uma localização de bolha válida com o pedido, ou remova a especificação de saída se não for desejada qualquer sobreposição de localização. |
| DesaparecidoRequestInput | O serviço web espera uma entrada, mas não foi fornecida nenhuma entrada. Certifique-se de que as entradas válidas são fornecidas com base nas portas de entrada publicadas no modelo e tente novamente. |
| Paraquímetros DesaparecidosRequiredGlobal | Nem todos os parâmetros de serviço web necessários fornecidos. Verifique se os parâmetros esperados para os módulos estão corretos e tente novamente. |
| MissingRequiredOutputOverrides | Ao chamar um ponto final de serviço encriptado é obrigatório passar em sobreposições de saída para todas as saídas do serviço. Faltando sobreposições neste momento para estas saídas: {0} |
| DesaparecidoWebServiceGroupId | Nenhum id do grupo de serviço web fornecido. Forneça um Id de grupo de serviço web válido e tente novamente. |
| DesaparecidoWebServiceId | Nenhum id de serviço web fornecido. Forneça um ID de serviço web válido e tente novamente. |
| Falta deWebServicePackage | Nenhum pacote de serviço web fornecido. Forneça um pacote de serviço web válido e tente novamente. |
| Faltando Espaço-Ad | Nenhuma identificação de espaço de trabalho fornecida. Forneça um id de espaço de trabalho válido e tente novamente. |
| Configuração modeloInvalid | Configuração do modelo inválida no pacote de modelos. Certifique-se de que a configuração do modelo contém definição de ponto final de saída, ponto final de erro de std e ponto final e tente novamente. |
| ModelPackageIdInvalid | Id do pacote de modelo inválido. Verifique se o pacote de modelo id está correto e tente novamente. |
| RequestBodyInvalid | Nenhum organismo de pedido fornecido ou erro na deserização do organismo de pedido. |
| PedidoIsEmpty | Nenhum pedido fornecido. Forneça um pedido válido e tente novamente. |
| InesperadoParameter | Parâmetros inesperados fornecidos. Verifique se todos os nomes dos parâmetros estão corretamente escritos, apenas os parâmetros esperados são passados e tente novamente. |
| DesconhecidoError | Uma margem de erro desconhecida. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Não é possível alterar os requisitos de pedidos simultâneos para {0} o serviço web. |
| WebServiceIdInvalid | Id de serviço web inválido fornecido. O id do serviço web deve ser um guia válido. |
| WebServiceTooManyConcurrentRequestRequirement | Não é possível definir o pedido simultâneo para mais de {0} . |
| WebServiceTypeInvalid | Tipo de serviço web inválido fornecido. Verifique se o tipo de serviço web válido está correto e tente novamente. Tipos de serviço web válidos: {0} . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (código de estado HTTP 400)
 
Argumento inválido do utilizador fornecido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| InputMismatchError | Os dados de entrada não correspondem ao esquema da porta de entrada. |
| InputParseError | A análise do vetor de entrada falhou.  Verifique se o vetor de entrada tem o número correto de colunas e tipos de dados.  Detalhes adicionais: {0} . |
| Paraquímetros DesaparecidosRequiredGlobal | Faltam parâmetros esperados pelo serviço web. Verifique todos os parâmetros necessários esperados pelo serviço web e esteja correto e tente novamente. |
| InesperadoParameter | Verifique apenas os parâmetros necessários esperados pelo serviço web e tente novamente. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InválidaOperação (código de estado HTTP 400)
 
O pedido é inválido no contexto atual.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| Não é o Início de Trabalho | O trabalho não pode ser iniciado porque está no {0} estado. |
| Modelo incompatível | O modelo é incompatível com a versão de pedido. A versão request suporta apenas modelos de saída de dados únicos. |
| MultipleInputsNotAllowed | O modelo não permite múltiplas entradas. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (código de estado HTTP 400)
 
A execução do módulo encontrou um erro interno da biblioteca.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>MóduloExecutionError (código de estado HTTP 400)
 
A execução do módulo encontrou um erro.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (código de estado HTTP 400)
 
Pacote de serviço web inválido. Verifique se o pacote de serviço web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| FormatError | O pacote de serviço web está mal formado. Detalhes: {0} |
| RuntimesError | O gráfico do pacote de serviço web é inválido. Detalhes: {0} |
| ValidaçãoError | O gráfico do pacote de serviço web é inválido. Detalhes: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Não autorizado (código de estado HTTP 401)
 
O pedido não é autorizado a aceder a recursos.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| AdminRequestUnauthorized | Não autorizado |
| GestãoRequestunauthorizada | Não autorizado |
| PontuaçãoRequestUnauthorized | Credenciais inválidas fornecidas. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (código de estado HTTP 404)
 
Recurso não encontrado.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModelPackageNotFound | Pacote modelo não encontrado. Verifique se o id da embalagem do modelo está correto e tente novamente. |
| WebServiceIdNotFoundInWorkspace | Serviço web sob este espaço de trabalho não encontrado. Existe um desfasamento entre o webServiceId e o workspaceId. Verifique se o serviço web fornecido faz parte do espaço de trabalho e tente novamente. |
| WebServiceNotFound | Serviço web não encontrado. Verifique se o ID do serviço web está correto e tente novamente. |
| Espaço de trabalhoNotFound | Espaço de trabalho não encontrado. Verifique se o Id do espaço de trabalho está correto e tente novamente. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (código de estado HTTP 408)
 
A operação não pôde ser concluída dentro do prazo permitido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| PedidoCanceled | O pedido foi cancelado pelo cliente. |
| ScoreRequestTimeout | Pedido de execução cronometrado. |
 
## <a name="conflict-http-status-code-409"></a>Conflito (código de estado HTTP 409)
 
O recurso já existe.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| ModeloOutputAdataMismatch | Nome do parâmetro de saída inválido. Tente utilizar o módulo editor de metadados para mudar o nome das colunas e tente novamente. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (código de estado HTTP 413)
 
O modelo tinha excedido a quota de memória que lhe foi atribuída.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| OutOfMemoryLimit | O modelo consumiu mais memória do que se apropriava. A memória máxima permitida para o modelo é {0} MB. Por favor, verifique se o seu modelo tem problemas. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (código de estado HTTP 500)
 
A execução encontrou um erro interno.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMis configurado |  |
| ContainerProcessprocessadoWithSystemError | O processo do contentor colidiu com erro do sistema |
| ContainerProcessprocessadoWithUnknownError | O processo do contentor despenhou-se com um erro desconhecido |
| ContainerValidationFailed | A validação do contentor blob falhou com este erro: {0} . |
| DeleteWebServiceResourceFailed |  |
| ExcepçãoDeserializaçãoError |  |
| FailedGettingApiDocument |  |
| Falha na armazenagem doWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| FaltandoTaskInstance | Sem argumentos. Verifique se os argumentos válidos são aprovados e tente novamente. |
| ModelPackageInvalid |  |
| MóduloExecuçãoFailed |  |
| MóduloLoadFailed |  |
| MóduloObjectCloneFailed |  |
| SaídaConversionFailed |  |
| PortDataTypeNotSupportou | O id {0} port= tem um tipo de dados não suportado: {1} . |
| Redução de recursos |  |
| RecursosLoadFailed |  |
| ServiceUrisNotFound |  |
| Elegê-lo | A geração Swagger falhou, detalhes: {0} |
| InesperadoSScoreStatus |  |
| DesconhecidaBackendErrorResponse |  |
| DesconhecidoError |  |
| DesconhecidoJobStatusCode | Código de estado de trabalho {0} desconhecido. |
| DesconhecidoModuleError |  |
| AtualizaçãoWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage, Detalhes: {0} |
| Direito de AutoritizaçãoFailed |  |
| TrabalhadorUnalizável |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (código de estado HTTP 500)
 
A execução encontrou um erro interno. Sistema com pouca memória. Tente novamente.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (código de estado HTTP 500)
 
Pacote modelo inválido. Verifique se a embalagem do modelo fornecida está correta e tente novamente.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (código de estado HTTP 500)
 
Pacote de serviço web inválido. Verifique se a embalagem web fornecida está correta e tente novamente.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| MóduloError | O gráfico do pacote de serviço web é inválido. Detalhes: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InicializaçãoContainers (código de estado HTTP 503)
 
O pedido não pode ser executado à medida que os contentores estão a ser inicializados.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiçoUn disponível (código de estado HTTP 503)
 
O serviço está temporariamente indisponível.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| NoMoreResources | Não há recursos disponíveis para pedido. |
| RequestThrottled | O pedido foi acelerado para {0} o ponto final. A concordância máxima para o ponto final é {1} . |
| TooManyConcurrentRequests | Muitos pedidos simultâneos enviados. |
| TooManyHostsBeingInitializado | Demasiados anfitriões a serem inicializados ao mesmo tempo. Considere estrangular/tentar novamente. |
| TooManyHostsBeingInitializedPerModel | Demasiados anfitriões a serem inicializados ao mesmo tempo. Considere estrangular/tentar novamente. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (código de estado HTTP 504)
 
A operação não pôde ser concluída dentro do prazo permitido.
 
| Código de erro | Mensagem de utilizador |
| ---------- |--------------|
| BackendInitializationTimeout | A inicialização do serviço web não pôde ser concluída dentro do tempo permitido. |
| BackendScoreTimeout | O pedido de serviço web de execução não pôde ser concluído dentro do tempo permitido. |
 
