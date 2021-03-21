---
title: Resolução de problemas da orquestração e desencadeamentos de gasodutos na Fábrica de Dados do Azure
description: Utilize diferentes métodos para resolver problemas de desencadeamento de gasodutos na Azure Data Factory.
author: ssabat
ms.service: data-factory
ms.date: 03/13/2021
ms.topic: troubleshooting
ms.author: susabat
ms.reviewer: susabat
ms.openlocfilehash: f5039e5a49da202b2dbfa20e56639365ed597c79
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462002"
---
# <a name="troubleshoot-pipeline-orchestration-and-triggers-in-azure-data-factory"></a>Resolução de problemas da orquestração e desencadeamentos de gasodutos na Fábrica de Dados do Azure

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Uma execução de pipeline do Azure Data Factory define uma instância de uma execução de pipeline. Por exemplo, digamos que tem um oleoduto que funciona às 8:00, 9:00 e 10:00. Neste caso, há três oleodutos separados. Cada execução de pipeline tem um ID de execução de pipeline exclusivo. Um ID de execução é um identificador globalmente único (GUID) que define esse gasoduto em particular.

Normalmente, as execuções de pipeline são instanciadas pela transmissão de argumentos a parâmetros que são definidos no pipeline. Pode executar uma tubagem manualmente ou utilizando um gatilho. Consulte [a execução do Pipeline e os gatilhos na Azure Data Factory](concepts-pipeline-execution-triggers.md) para obter mais detalhes.

## <a name="common-issues-causes-and-solutions"></a>Questões comuns, causas e soluções

### <a name="an-azure-functions-app-pipeline-throws-an-error-with-private-endpoint-connectivity"></a>Um pipeline de aplicativoS Azure Funs lança um erro com conectividade de ponto final privado
 
Tem data factory e uma aplicação de função Azure em execução num ponto final privado. Está a tentar executar um oleoduto que interage com a aplicação de função. Já tentou três métodos diferentes, mas um retorna o erro "Mau Pedido", e os outros dois métodos devolvem "103 Error Forbidden".

**Motivo**

A Data Factory não suporta atualmente um conector de ponto final privado para aplicações de funções. A Azure Functions rejeita chamadas porque está configurado para permitir apenas ligações a partir de um link privado.

**Resolução**

Crie um ponto final **PrivateLinkService** e forneça o DNS da sua aplicação de função.

### <a name="a-pipeline-run-is-canceled-but-the-monitor-still-shows-progress-status"></a>Uma corrida de gasoduto é cancelada, mas o monitor ainda mostra o estado de progresso

**Motivo**

Quando se cancela uma corrida ao gasoduto, a monitorização do gasoduto mostra frequentemente o estado de progresso. Isto acontece por causa de um problema de cache do navegador. Também pode não ter os filtros de monitorização corretos.

**Resolução**

Refresque o navegador e aplique os filtros de monitorização corretos.
 
### <a name="you-see-a-delimitedtextmorecolumnsthandefined-error-when-copying-a-pipeline"></a>Você vê um erro "DelimitedTextMoreColumnsThanDefined" ao copiar um oleoduto
 
 **Motivo**
 
Se uma pasta que está a copiar contiver ficheiros com esquemas diferentes, tais como número variável de colunas, delimiters diferentes, definições de carvão de cotação ou algum problema de dados, o gasoduto Data Factory pode lançar este erro:

`
Operation on target Copy_sks  failed: Failure happened on 'Sink' side.
ErrorCode=DelimitedTextMoreColumnsThanDefined,
'Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException,
Message=Error found when processing 'Csv/Tsv Format Text' source '0_2020_11_09_11_43_32.avro' with row number 53: found more columns than expected column count 27.,
Source=Microsoft.DataTransfer.Common,'
`

**Resolução**

Selecione a opção **Cópia Binária** enquanto cria a atividade Copy. Desta forma, para cópias a granel ou migração dos seus dados de um lago de dados para outro, a Data Factory não abrirá os ficheiros para ler o esquema. Em vez disso, a Data Factory tratará cada ficheiro como binário e copiá-lo-á para o outro local.

### <a name="a-pipeline-run-fails-when-you-reach-the-capacity-limit-of-the-integration-runtime-for-data-flow"></a>Um gasoduto falha quando se atinge o limite de capacidade do tempo de execução da integração para o fluxo de dados

**Emissão**

Mensagem de erro:

`
Type=Microsoft.DataTransfer.Execution.Core.ExecutionException,Message=There are substantial concurrent MappingDataflow executions which is causing failures due to throttling under Integration Runtime 'AutoResolveIntegrationRuntime'.
`

**Motivo**

Atingiste o limite de capacidade do tempo de integração. Você pode estar executando uma grande quantidade de fluxo de dados usando o mesmo tempo de integração ao mesmo tempo. Consulte [limites de subscrição e serviço da Azure, quotas e constrangimentos](../azure-resource-manager/management/azure-subscription-service-limits.md#version-2) para mais detalhes.

**Resolução**
 
- Executar os seus oleodutos em diferentes momentos de disparo.
- Crie um novo tempo de integração e divida os seus oleodutos através de vários tempos de integração.

### <a name="how-to-perform-activity-level-errors-and-failures-in-pipelines"></a>Como executar erros e falhas a nível de atividade em gasodutos

**Motivo**

A orquestração Azure Data Factory permite a lógica condicional e permite que os utilizadores tomem caminhos diferentes com base no resultado de uma atividade anterior. Permite quatro caminhos condicional: **Após o Sucesso** (passe padrão), **Após O Fracasso,** **Após A Conclusão,** e **Após O Salto**. 

A Azure Data Factory avalia o resultado de todas as atividades ao nível das folhas. Os resultados do gasoduto só são bem sucedidos se todas as folhas forem bem sucedidas. Se uma atividade de folha foi ignorada, avaliamos a sua atividade principal. 

**Resolução**

* Implementar controlos ao nível da atividade seguindo [como lidar com falhas e erros](https://techcommunity.microsoft.com/t5/azure-data-factory/understanding-pipeline-failures-and-error-handling/ba-p/1630459)do gasoduto .
* Utilize aplicações lógicas Azure para monitorizar os gasodutos em intervalos regulares após [consulta por fábrica](/rest/api/datafactory/pipelineruns/querybyfactory).
* [Pipeline monitor visual](https://docs.microsoft.com/azure/data-factory/monitor-visually)

### <a name="how-to-monitor-pipeline-failures-in-regular-intervals"></a>Como monitorizar falhas de gasodutos em intervalos regulares

**Motivo**

Pode ser necessário monitorizar os oleodutos falhados da Data Factory em intervalos, digamos, 5 minutos. Pode consultar e filtrar o gasoduto a partir de uma fábrica de dados utilizando o ponto final. 

**Resolução**
* Pode configurar uma aplicação lógica Azure para consultar todos os oleodutos falhados a cada 5 minutos, conforme descrito na [Consulta By Factory](/rest/api/datafactory/pipelineruns/querybyfactory). Então, pode reportar incidentes ao seu sistema de bilhética.
* [Pipeline monitor visual](https://docs.microsoft.com/azure/data-factory/monitor-visually)

### <a name="degree-of-parallelism--increase-does-not-result-in-higher-throughput"></a>O grau de aumento do paralelismo não resulta numa maior produção

**Motivo** 

O grau de paralelismo em *ForEach* é, na verdade, o grau máximo de paralelismo. Não podemos garantir um número específico de execuções que ocorram ao mesmo tempo, mas este parâmetro garantirá que nunca ultrapassamos o valor estabelecido. Deve ver isto como um limite, para ser alavancado ao controlar o acesso simultâneo às suas fontes e pias.

Factos Conhecidos sobre *ForEach*
 * Foreach tem uma propriedade chamada contagem de lote(n) onde o valor padrão é 20 e o máximo é de 50.
 * A contagem de lotes, n, é usada para construir n filas. Mais tarde discutiremos alguns detalhes sobre a forma como estas filas são construídas.
 * Cada fila corre sequencialmente, mas você pode ter várias filas correndo em paralelo.
 * As filas são pré-criadas. Isto significa que não há reequilíbrio das filas durante o tempo de funcionamento.
 * A qualquer momento, tem no máximo um item sendo processado por fila. Isto significa que, no máximo, n itens estão a ser processados a qualquer momento.
 * O tempo total de processamento forifível é igual ao tempo de processamento da fila mais longa. Isto significa que a atividade do foreach depende da forma como as filas são construídas.
 
**Resolução**

 * Não deve utilizar a atividade *SetVariable* dentro *de cada um* que funciona em paralelo.
 * Tendo em conta a forma como as filas são construídas, o cliente pode melhorar o desempenho do foreach, definindo *múltiplas falhas em* que cada proa terá itens com tempo de processamento semelhante. Isto assegurará que os longos prazos sejam processados paralelamente de forma bastante sequencial.

 ### <a name="pipeline-status-is-queued-or-stuck-for-a-long-time"></a>O estado do gasoduto está na fila ou preso por muito tempo
 
 **Motivo**
 
 Isto pode acontecer por várias razões como bater limites de concordância, falhas de serviço, falhas de rede e assim por diante.
 
 **Resolução**
 
* Limite de Concordância: Se o seu gasoduto tiver uma política de concordância, verifique se não existem antigos gasodutos em curso. A concordância máxima permitida na Fábrica de Dados Azure é de 10 oleodutos. 
* Limites de monitorização: Vá à tela de autoria da ADF, selecione o seu oleoduto e determine se tem uma propriedade de conuncy atribuída a ele. Se acontecer, vá à vista de monitorização, e certifique-se de que não há nada nos últimos 45 dias que esteja em andamento. Se houver algo em andamento, pode cancelá-lo e o novo oleoduto deve começar.
* Questões Transitórias: É possível que a sua execução tenha sido afetada por um problema transitório de rede, falhas de credenciais, falhas de serviços, etc.  Se isso acontecer, a Azure Data Factory tem um processo de recuperação interna que monitoriza todas as corridas e as inicia quando percebe que algo correu mal. Este processo acontece a cada hora, por isso, se a sua corrida ficar presa por mais de uma hora, crie um caso de apoio.
 
### <a name="longer-start-up-times-for-activities-in-adf-copy-and-data-flow"></a>Tempos de arranque mais longos para atividades em ADF Copy e Data Flow

**Motivo**

Isto pode acontecer se não tiver implementado tempo para viver recurso para Data Flow ou SHIR otimizado.

**Resolução**

* Se cada atividade de cópia estiver a demorar até 2 minutos a ser iniciada e o problema ocorrer principalmente numa associação VNet (vs. Azure IR), poderá tratar-se de um problema de desempenho da cópia. Para rever os passos de resolução de problemas, vá para [Copy Performance Improvement.](https://docs.microsoft.com/azure/data-factory/copy-activity-performance-troubleshooting)
* Pode utilizar o tempo para viver a funcionalidade para diminuir o tempo de arranque do cluster para atividades de fluxo de dados. Por favor, reveja [o tempo de execução da integração do fluxo de dados.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity#data-flow-integration-runtime)

 ### <a name="hitting-capacity-issues-in-shirself-hosted-integration-runtime"></a>Problemas de capacidade de ataque em SHIR (Tempo de Execução de Integração Auto-Hospedada)
 
 **Motivo**
 
Isto pode acontecer se não tiver aumentado o SHIR de acordo com a sua carga de trabalho.

**Resolução**

* Se encontrar um problema de capacidade da SHIR, atualize o VM para aumentar o nó para equilibrar as atividades. Se receber uma mensagem de erro sobre uma falha ou erro geral auto-hospedado, uma atualização ira auto-hospedada ou problemas de conectividade IR auto-hospedados, que podem gerar uma longa fila, vá para [o tempo de integração auto-hospedado.](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-troubleshoot-guide)

### <a name="error-messages-due-to-long-queues-for-adf-copy-and-data-flow"></a>Mensagens de erro devido a longas filas para cópia aDF e fluxo de dados

**Motivo**

Mensagens de erro relacionadas com a fila longa podem aparecer por várias razões. 

**Resolução**
* Se receber uma mensagem de erro de qualquer fonte ou destino através de conectores, que podem gerar uma longa fila, aceda ao [Guia de Resolução de Problemas do Conector.](https://docs.microsoft.com/azure/data-factory/connector-troubleshoot-guide)
* Se receber uma mensagem de erro sobre o Fluxo de Dados de Mapeamento, que pode gerar uma longa fila, vá ao [Guia de Resolução de Problemas dos Fluxos de Dados.](https://docs.microsoft.com/azure/data-factory/data-flow-troubleshoot-guide)
* Se receber uma mensagem de erro sobre outras atividades, como Databricks, atividades personalizadas ou HDI, que podem gerar uma longa fila, vá ao [Guia de Resolução de Problemas de Atividade.](https://docs.microsoft.com/azure/data-factory/data-factory-troubleshoot-guide)
* Se receber uma mensagem de erro sobre a execução de pacotes SSIS, que podem gerar uma longa fila, vá ao [Guia de Resolução de Problemas de Execução de Problemas de Execução de Pacotes Azure-SSIS](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-ssis-activity-faq) e [ao Guia de Resolução de Problemas de Gestão de Problemas de Integração.](https://docs.microsoft.com/azure/data-factory/ssis-integration-runtime-management-troubleshoot)


## <a name="next-steps"></a>Passos seguintes

Para obter mais ajuda para resolver problemas, experimente estes recursos:

*  [Blog da Fábrica de Dados](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Pedidos de recursos da Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Vídeos do Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&Uma página de perguntas](/answers/topics/azure-data-factory.html)
*  [Informações do Twitter sobre a Data Factory](https://twitter.com/hashtag/DataFactory)
