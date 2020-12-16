---
title: Resolução de problemas da orquestração do gasoduto e dos gatilhos na ADF
description: Utilize diferentes métodos para resolver problemas de desencadeamento de gasodutos em ADF
author: ssabat
ms.service: data-factory
ms.date: 12/15/2020
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: ed3728513820da9f4ef85d44cac983dc09c3fc7d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/15/2020
ms.locfileid: "97521850"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-adf"></a>Resolução de problemas da orquestração do gasoduto e dos gatilhos na ADF

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma execução de pipeline do Azure Data Factory define uma instância de uma execução de pipeline. Por exemplo, tem um oleoduto que executa às 8:00, 9:00 e 10:00 da manhã. Neste caso, existem três percursos separados do gasoduto ou do gasoduto. Cada execução de pipeline tem um ID de execução de pipeline exclusivo. Um ID de execução é um GUID (Globalmente Unique Identifier) que define esse gasoduto em particular.

Normalmente, as execuções de pipeline são instanciadas pela transmissão de argumentos a parâmetros que são definidos no pipeline. Pode executar um pipeline manualmente ou com um acionador. Consulte a [execução do Pipeline e os gatilhos na Azure Data Factory](concepts-pipeline-execution-triggers.md) para obter mais detalhes.

## <a name="common-issues-causes-and-solutions"></a>Questões comuns, causas e soluções

### <a name="pipeline-with-azure-function-throws-error-with-private-end-point-connectivity"></a>Pipeline com Função Azure lança erro com conectividade de ponto final privado
 
#### <a name="issue"></a>Problema
Para algum contexto, tem a ADF e a Azure Function App a funcionar em um ponto final privado. Está a tentar que funcione um oleoduto que interage com a App de Função Azure. Já experimentou três métodos diferentes, mas um retorna `Bad Request` erro, os outros dois métodos `103 Error Forbidden` regressam.

#### <a name="cause"></a>Causa 
A ADF não suporta atualmente um conector de ponto final privado para a App de Função Azure. E esta deve ser a razão pela qual a Azure Function App está rejeitando as chamadas, uma vez que seria configurada para permitir apenas ligações a partir de um Link Privado.

#### <a name="resolution"></a>Resolução
Pode criar um Ponto Final Privado do tipo **PrivateLinkService** e fornecer o DNS da sua aplicação de função, e a ligação deve funcionar.

### <a name="pipeline-run-is-killed-but-the-monitor-still-shows-progress-status"></a>O gasoduto está morto, mas o monitor ainda mostra o estado de progresso

#### <a name="issue"></a>Problema
Muitas vezes, quando se mata uma conduta, a monitorização do gasoduto ainda mostra o estado de progresso. Isto acontece devido ao problema de cache no navegador e você não está tendo filtros certos para monitorização.

#### <a name="resolution"></a>Resolução
Refresque o navegador e aplique filtros certos para monitorização.
 
### <a name="copy-pipeline-failure--found-more-columns-than-expected-column-count-delimitedtextmorecolumnsthandefined"></a>Falha do Pipeline copy – encontrou mais colunas do que a contagem de colunas esperada (DelimitedTextMoreColumnsThanDefined)

#### <a name="issue"></a>Problema  
Se os ficheiros de uma determinada pasta que está a copiar contiverem ficheiros com esquemas diferentes, como número variável de colunas, delimiters diferentes, definições de carvão de cotação ou algum problema de dados, o gasoduto ADF acabará por funcionar neste erro:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

#### <a name="resolution"></a>Resolução
Selecione a opção "Cópia Binária" enquanto cria a atividade de Dados de Cópia. Desta forma, para copiar a granel ou migrar os seus dados de um Lago de Dados para outro, com opção **binária,** a ADF não abrirá os ficheiros para ler esquemas, mas apenas tratará cada ficheiro como binário e copiá-losá para o outro local.

### <a name="pipeline-run-fails-when-capacity-limit-of-integration-runtime-is-reached"></a>O gasoduto falha quando se atinge o limite de capacidade do tempo de execução da integração

#### <a name="issue"></a>Problema
Mensagem de erro:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

O erro indica a limitação do tempo de execução por integração, que atualmente é de 50. Consulte [os Limites](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#version-2) para mais detalhes.

Se executar uma grande quantidade de fluxo de dados usando o mesmo tempo de integração ao mesmo tempo, pode causar este tipo de erro.

#### <a name="resolution"></a>Resolução 
- Separe estes oleodutos por um tempo diferente de disparo para executar.
- Crie um novo tempo de integração e divida estes oleodutos através de vários tempos de integração.

### <a name="how-to-monitor-pipeline-failures-on-regular-interval"></a>Como monitorizar falhas do gasoduto em intervalos regulares

#### <a name="issue"></a>Problema
Há muitas vezes a necessidade de monitorizar os gasodutos ADF em intervalos, digamos, 5 minutos. Pode consultar e filtrar o gasoduto a partir de uma fábrica de dados utilizando o ponto final. 

#### <a name="recommendation"></a>Recomendação
1. Crie uma App Azure Logic para consultar todos os oleodutos falhados a cada 5 minutos.
2. Em seguida, pode reportar incidentes ao nosso sistema de bilhética de acordo com [a QueryByFactory.](https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory)

#### <a name="reference"></a>Referência
- [Notificações de envio externo da ADF](https://www.mssqltips.com/sqlservertip/5962/send-notifications-from-an-azure-data-factory-pipeline--part-2/)

### <a name="how-to-handle-activity-level-errors-and-failures-in-pipelines"></a>Como lidar com erros e falhas ao nível da atividade em gasodutos

#### <a name="issue"></a>Problema
A orquestração Azure Data Factory permite a lógica condicional e permite ao utilizador seguir caminhos diferentes com base nos resultados de uma atividade anterior. Permite quatro caminhos condicional: "Após o Sucesso (passe padrão)", "Após O Fracasso", "Após Conclusão", e "Após O Salto". É permitido utilizar diferentes caminhos.

A Azure Data Factory define o sucesso e o fracasso do gasoduto da seguinte forma:

- Avalie o resultado de todas as atividades ao nível da folha. Se uma atividade de folha foi ignorada, avaliamos a sua atividade principal.
- O resultado do gasoduto é bem sucedido se e somente se todas as folhas forem bem sucedidas.

#### <a name="recommendation"></a>Recomendação
- Implementar controlos de nível de atividade na sequência [de como lidar com falhas e erros do gasoduto](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459).
- Utilize a Azure Logic App para monitorizar os gasodutos em intervalos regulares após [consulta por DataFactory]( https://docs.microsoft.com/rest/api/datafactory/pipelineruns/querybyfactory).

## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)