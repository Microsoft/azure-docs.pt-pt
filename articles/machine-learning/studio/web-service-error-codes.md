---
title: Códigos de erro REST API - Azure Machine Learning Studio (clássico) / Microsoft Docs
description: Estes códigos de erro podem ser devolvidos por uma operação num serviço web Azure Machine Learning.
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
ms.openlocfilehash: 9e25f2fbc10eb07cc71f2a7bd34247c0191f61b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79217764"
---
# <a name="azure-machine-learning-studio-classic-rest-api-error-codes"></a>Azure Machine Learning Studio (clássico) CÓDIGOs de erro REST API

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]
 
Os seguintes códigos de erro podem ser devolvidos por uma operação num serviço web Azure Machine Learning Studio (clássico).
 
## <a name="badargument-http-status-code-400"></a>BadArgument (código de estado HTTP 400)
 
Argumento inválido fornecido.
 
Esta classe de erros significa que um argumento fornecido em algum lugar era inválido. Esta pode ser uma credencial ou localização do armazenamento Azure para algo passado para o serviço web. Por favor, olhe para o campo de erro "código" na secção "detalhes" para diagnosticar qual argumento específico era inválido.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| BadParameterValue | O valor do parâmetro fornecido não satisfaz a regra do parâmetro no parâmetro |
| BadSubscriptionId | O Id de subscrição que é usado para pontuar não é o presente no recurso |
| BadVersionCall | O parâmetro de versão inválida foi {0}passado durante a chamada da API: . Verifique a página de ajuda da API para passar a versão correta e tente novamente. |
| LotJobInputsNotEspecificado | Os seguintes contributos necessários não foram especificados {0}com o pedido: . Certifique-se de que todos os dados de entrada estão especificados e tente novamente. |
| LotJobInputsTooManyEspecificado | O pedido especificou mais inputs do que o definido no serviço. Lista de contributos aceites): {0}. Certifique-se de que todos os dados de entrada estão especificados corretamente e tente novamente. |
| BlobNameTooLong | O caminho de armazenamento de bolhas azure {0}previsto para a saída de diagnóstico é demasiado longo: . Encurte o caminho e tente de novo. |
| BlobNotFound | Incapaz de aceder à bolha Azure fornecida - {0}.  Mensagem de erro {1}azure: . |
| Contentorisvazio | Não foi fornecido nenhum nome de contentor de armazenamento Azure. Forneça um nome de recipiente válido e tente novamente. |
| ContentorSegmentInválido | Nome do recipiente inválido. Forneça um nome de recipiente válido e tente novamente. |
| Validação de contentoresFalhada | A validação do recipiente blob falhou com este erro: {0}. |
| DataTypeNotSupported | Tipo de dados não suportados fornecidos. Forneça os tipos de dados válidos e tente novamente. |
| DuplicateInputInBatchCall | O pedido do lote é inválido. Não é possível especificar a entrada única e múltipla ao mesmo tempo. Retire um destes itens do pedido e tente novamente. |
| ExpiraçãoTimeInThePast | O prazo de validade previsto {0}é no passado: . Forneça um futuro tempo de validade na UTC e tente novamente. Para nunca expirar, dedute o tempo de validade para NULL. |
| Definições incompletas | As definições de diagnóstico estão incompletas. |
| InputBlobRelativeLocationInválido | Não foi fornecido nenhum nome de bolha de armazenamento Azure. Forneça um nome de bolha válido e tente novamente. |
| InvalidBlob | Especificação de bolha inválida {0}para a bolha: . Verifique se a cadeia de ligação / caminho relativo ou especificação token SAS estão corretas e tente novamente. |
| InvalidBlobConnectionString | A cadeia de ligação especificada para uma das bolhas {0}de entrada/saída inválidas: . Por favor, corrija isto e tente de novo. |
| Extensão InvalidBlob | A referência blob: {0} tem uma extensão de ficheiro inválida ou em falta. As extensões de ficheirosuportadas para{1}este tipo de saída são: " ". |
| Nomes inválidos de inputs | Nomes de entrada de serviço inválidos {0}especificados no pedido: . Por favor, mapeie os dados de entrada para as inputs de serviço corretas e tente novamente. |
| InvalidOutputOverrideName | Nome de substituição {0}de saída inválido: . O serviço não tem um nó de saída com este nome. Por favor, passe um nome de nó de saída correto para anular (a sensibilidade do caso aplica-se). |
| InvalidQueryParameter | Parâmetro de consulta inválido '{0}'. {1} |
| Informação missingInputBlob | Informação de blob de armazenamento azure desaparecida. Forneça uma corda de ligação válida e um caminho relativo ou URI e tente novamente. |
| Emprego desaparecido | Nenhum trabalho que a Id forneceu. Um trabalho é devolvido quando um trabalho foi submetido pela primeira vez. Verifique se o id do trabalho está correto e tente novamente. |
| Chaves desaparecidas | Não são fornecidas chaves ou uma chave primária ou secundária. |
| Pacote de Modelos Desaparecidos | Nenhum pacote de modelo id ou pacote de modelo fornecido. Forneça um pacote de modelo válido Id ou pacote de modelo e tente novamente. |
| FaltaoutputOverrideSpecification | Falta-lhe a especificação de bolha {0}para a sobreposição da saída . Por favor, especifique uma localização de bolha válida com o pedido ou remova a especificação de saída se não for desejada qualquer sobreposição de localização. |
| Input de Pedido de Falta | O serviço web espera uma entrada, mas não foi fornecida nenhuma entrada. Certifique-se de que as inputs válidas são fornecidas com base nas portas de entrada publicadas no modelo e tente novamente. |
| Faltade Parâmetros Globais Exigidos | Nem todos os parâmetros de serviço web necessários fornecidos. Verifique se os parâmetros esperados para os módulos estão corretos e tente novamente. |
| Deslocações por excesso de saída em falta | Ao chamar um ponto final de serviço encriptado é obrigatório passar em sobreposições de saída para todas as saídas do serviço. Falta ndo substituições neste momento para estas saídas:{0} |
| MissingWebServiceGroupId | Nenhum grupo de serviço web Fornecido. Forneça um grupo de serviço web válido Id e tente novamente. |
| MissingWebServiceId | Nenhum serviço web que o ID forneceu. Forneça um id de serviço web válido e tente novamente. |
| Pacote de serviços web service desaparecido | Nenhum pacote de serviço web fornecido. Forneça um pacote de serviço web válido e tente novamente. |
| MissingWorkspaceId | Não forneceu nenhum espaço de trabalho. Forneça um id de espaço de trabalho válido e tente novamente. |
| ModelConfigurationInválido | Configuração do modelo inválido no pacote do modelo. Certifique-se de que a configuração do modelo contém definição de ponto final de saída, ponto final de erro std e ponto final e tentar novamente. |
| ModeloPackageIdInvalid | Pacote de modelo inválido Id. Verifique se o pacote de modelo Id está correto e tente novamente. |
| RequestBodyInvalid | Nenhum organismo de pedido fornecido ou erro na desserialização do organismo de pedido. |
| RequestisEmpty | Nenhum pedido fornecido. Forneça um pedido válido e tente novamente. |
| Parâmetro inesperado | Parâmetros inesperados fornecidos. Verifique se todos os nomes dos parâmetros estão corretamente escritos, apenas os parâmetros esperados são passados e tente novamente. |
| Erro Desconhecido | Uma margem de erro desconhecida. |
| UserParameterInválido | {0} |
| Requisitos de pedido de consões da WebServiceConcurrentInválido | Não é possível alterar {0} os requisitos simultâneos de pedidos para o serviço web. |
| WebServiceIdInvalid | Id de serviço web inválido fornecido. O id do serviço web deve ser um guia válido. |
| Requisito de Pedido de CorrenteSToSManyConcurrent | Não é possível definir {0}o requisito de pedido simultâneo para mais do que . |
| WebServiceTypeInvalid | Tipo de serviço web inválido fornecido. Verifique se o tipo de serviço web válido está correto e tente novamente. Tipos de serviço {0}web válidos: . |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (código de estado HTTP 400)
 
Argumento de utilizador inválido fornecido.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| InputMismatchError | Os dados de entrada não correspondem ao esquema da porta de entrada. |
| InputParseError | A análise do vetor de entrada falhou.  Verifique se o vetor de entrada tem o número correto de colunas e tipos de dados.  Detalhes adicionais: {0}. |
| Faltade Parâmetros Globais Exigidos | Os parâmetros esperados pelo serviço web estão em falta. Verifique se todos os parâmetros necessários esperados pelo serviço web estão corretos e tente novamente. |
| Parâmetro inesperado | Verifique se apenas os parâmetros necessários esperados pelo serviço web são passados e tente novamente. |
| UserParameterInválido | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>Operação Inválida (código de estado HTTP 400)
 
O pedido é inválido no contexto atual.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| Não Pode Começar Job | O trabalho não pode ser {0} iniciado porque está no estado. |
| Modelo Incompatível | O modelo é incompatível com a versão de pedido. A versão de pedido suporta apenas modelos de saída simples datatable. |
| MúltiplosInputsNotAllowed | O modelo não permite várias inputs. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (código de estado HTTP 400)
 
A execução do módulo encontrou um erro interno da biblioteca.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>MóduloExecutionError (código de estado HTTP 400)
 
A execução do módulo encontrou um erro.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (código de estado HTTP 400)
 
Pacote de serviço web inválido. Verifique se o pacote de serviço web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| Erro de formato | O pacote de serviço web está mal formado. Detalhes:{0} |
| RuntimesError | O gráfico do pacote de serviço web é inválido. Detalhes:{0} |
| Erro de validação | O gráfico do pacote de serviço web é inválido. Detalhes:{0} |
 
## <a name="unauthorized-http-status-code-401"></a>Não autorizado (código de estado HTTP 401)
 
O pedido não é autorizado a aceder a recursos.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| AdminRequestUnauthorized | Não autorizado |
| Pedido de GestãoNão Autorizado | Não autorizado |
| Pedido de pontuaçãoNão autorizado | Credenciais inválidas fornecidas. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (código de estado HTTP 404)
 
Recurso não encontrado.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| ModeloPacoteNão Encontrado | Modelo pacote não encontrado. Verifique se o pacote de modelos Id está correto e tente novamente. |
| WebServiceIdNotFoundInWorkspace | Serviço web sob este espaço de trabalho não encontrado. Existe um desfasamento entre o webServiceId e o workspaceId. Verifique se o serviço web fornecido faz parte do espaço de trabalho e tente novamente. |
| WebServiceNotFound | Serviço web não encontrado. Verifique se o id do serviço web está correto e tente novamente. |
| Espaço de trabalhoNotFound | Espaço de trabalho não encontrado. Verifique se o id do espaço de trabalho está correto e tente novamente. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (código de estado HTTP 408)
 
A operação não pôde ser concluída no prazo permitido.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| Pedido Cancelado | O pedido foi cancelado pelo cliente. |
| Timeout de Pedido de Pontuação | Pedido de execução cronometrado. |
 
## <a name="conflict-http-status-code-409"></a>Conflito (código de estado HTTP 409)
 
O recurso já existe.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Nome do parâmetro de saída inválido. Tente utilizar o módulo de editor de metadados para mudar o nome das colunas e tentar novamente. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>Violação de MemoryQuota (código de estado HTTP 413)
 
O modelo tinha excedido a quota de memória que lhe foi atribuída.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| Limite de memória fora | O modelo consumia mais memória do que o apropriado para ele. A memória máxima permitida {0} para o modelo é MB. Por favor, verifique se o seu modelo tem problemas. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (código de estado HTTP 500)
 
A execução encontrou um erro interno.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMconfigurado |  |
| Processo de contentorTerminadoComerro do Sistema | O processo do contentor colidiu com o erro do sistema |
| Processo de contentorterminado Com Erro Desconhecido | O processo do contentor despenhou-se com um erro desconhecido. |
| Validação de contentoresFalhada | A validação do recipiente blob falhou com este erro: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExcepçãoErro deserialização |  |
| FailedGettingApiDocument |  |
| ArmazenetWebService falhado |  |
| Configuração de Memória Inválida | Configuração de Memória Inválida, ConfigValue:{0} |
| Configuração invalidResourceCache |  |
| Configuração de Downloadde Recursos Inválidos |  |
| InvalidWebServiceResources |  |
| Faltade tarefas | Não há argumentos fornecidos. Verifique se os argumentos válidos são aprovados e tente novamente. |
| ModeloPacoteInválido |  |
| MóduloExecuçãoFalhado |  |
| MóduloLoadFailed |  |
| MóduloObjectCloneFailed |  |
| Conversão de saídaFalhada |  |
| PortDataTypeNotSupported | O id{0} do porto= tem {1}um tipo de dados não suportado: . |
| Download de Recursos |  |
| Carga de recursos Falhada |  |
| ServiceUrisNotFound |  |
| Geração Swagger | A geração Swagger falhou, detalhes:{0} |
| Estatuto de Pontuação Inesperada |  |
| Resposta desconhecida backendErrorResponse |  |
| Erro Desconhecido |  |
| Código de Estatuto de Emprego Desconhecido | Código {0}de estado de trabalho desconhecido. |
| Erro de Módulo desconhecido |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| Pacote WebServiceInválido | InvalidWebServicePackage, Detalhes:{0} |
| Autorização de TrabalhadorEs Falhada |  |
| Trabalhador Inalcançável |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (código de estado HTTP 500)
 
A execução encontrou um erro interno. Sistema com pouca memória. Tente novamente.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (código de estado HTTP 500)
 
Pacote de modelo inválido. Verifique se a embalagem do modelo fornecida está correta e tente novamente.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (código de estado HTTP 500)
 
Pacote de serviço web inválido. Verifique se o pacote web fornecido está correto e tente novamente.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| MóduloError | O gráfico do pacote de serviço web é inválido. Detalhes:{0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>Recipientes de Inicialização (código de estado HTTP 503)
 
O pedido não pode ser executado porque os contentores estão a ser inicializados.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiçoIndisponível (código de estado HTTP 503)
 
O serviço está temporariamente indisponível.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| NoMoreResources | Não há recursos disponíveis para pedido. |
| RequestThrottled | O pedido foi {0} acelerado para o ponto final. A moeda máxima para o {1}ponto final é . |
| Pedidos demasiado constuais | Demasiados pedidos simultâneos enviados. |
| TooManyHostsBeingInitializado | Demasiados anfitriões a serem inicializados ao mesmo tempo. Considere estrangulamento/retentativa. |
| TooManyHostsBeingInitializedPerModel | Demasiados anfitriões a serem inicializados ao mesmo tempo. Considere estrangulamento/retentativa. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (código de estado HTTP 504)
 
A operação não pôde ser concluída no prazo permitido.
 
| Código de erro | Mensagem do utilizador |
| ---------- |--------------|
| BackendInicializationTimeout | A inicialização do serviço web não poderia ser concluída dentro do tempo permitido. |
| BackendScoreTimeout | A execução do pedido de serviço web não poderia ser concluída dentro do tempo permitido. |
 
