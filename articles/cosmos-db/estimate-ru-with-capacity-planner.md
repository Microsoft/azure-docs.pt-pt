---
title: Estimativa de custos usando o planejador de capacidades DB da Azure Cosmos
description: O planejador de capacidades DB da Azure Cosmos permite-lhe estimar a produção (RU/s) necessária e o custo para a sua carga de trabalho. Este artigo descreve como usar a nova versão do planejador de capacidade para estimar a produção e o custo necessários.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: 017aeaaa24e8ac2d493d0de81a7abb655bae5611
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342084"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Estimativa RU/s usando o planejador de capacidades DB Azure Cosmos
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Configurar as suas bases de dados e contentores Azure Cosmos com a quantidade certa de produção prevista, ou [Unidades de Pedido (RU/s)](request-units.md), pois a sua carga de trabalho é essencial para otimizar o custo e o desempenho. Este artigo descreve como usar o planejador de [capacidades](https://cosmos.azure.com/capacitycalculator/) DB da Azure Cosmos para obter uma estimativa do RU/s necessário e do custo da sua carga de trabalho. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Como estimar a produção e o custo com o planejador de capacidades da Azure Cosmos DB

O planificador de capacidade pode ser utilizado em dois modos.

|**Modo**  |**Descrição**  |
|---------|---------|
|Básico|Fornece uma estimativa rápida e de custos de alto nível. Este modo pressupõe as definições padrão de Azure Cosmos DB para a política de indexação, consistência e outros parâmetros. <br/><br/>Utilize o modo básico para uma estimativa rápida e de alto nível quando estiver a avaliar uma carga de trabalho potencial para funcionar no Azure Cosmos DB.|
|Avançado|Fornece uma estimativa de custos e RU mais detalhado, com a capacidade de afinar definições adicionais — política de indexação, nível de consistência e outros parâmetros que afetam o custo e a produção. <br/><br/>Utilize o modo avançado quando estimar RU/s para um novo projeto ou quer uma estimativa mais detalhada. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Estimativa de produção e custos previstos através do modo básico
Para obter uma estimativa rápida da sua carga de trabalho utilizando o modo básico, navegue até ao [planejador de capacidades](https://cosmos.azure.com/capacitycalculator/). Introduza os seguintes parâmetros com base na sua carga de trabalho: 

|**Entrada**  |**Descrição**  |
|---------|---------|
|Número de regiões|AZure Cosmos DB está disponível em todas as regiões do Azure. Selecione o número de regiões necessárias para a sua carga de trabalho. Pode associar várias regiões à sua conta Cosmos. Consulte [a distribuição global](distribute-data-globally.md) em Azure Cosmos DB para mais detalhes.|
|Várias regiões escrevem|Se ativar [as gravações multi-regiões,](distribute-data-globally.md#key-benefits-of-global-distribution)a sua aplicação pode ler e escrever para qualquer região do Azure. Se desativar as gravações multi-regiões, a sua aplicação pode escrever dados para uma única região. <br/><br/> Ativar as gravações multi-regiões se espera ter uma carga de trabalho ativa que requer baixas escritas de latência em diferentes regiões. Por exemplo, uma carga de trabalho IOT que escreve dados para a base de dados em volumes elevados em diferentes regiões. <br/><br/> A multi-região escreve que garante 99,999% de leitura e disponibilidade de escrita. As escritas multi-regiões requerem mais produção quando comparadas com as regiões de escrita única. Para saber mais, veja [como as RUs são diferentes para o artigo de regiões de escrita única e múltipla.](optimize-cost-regions.md)|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB numa única região.|
|Tamanho do item|O tamanho estimado do item de dados (por exemplo, documento), que varia de 1 KB a 2 MB. |
|Leituras/seg por região|Número de leituras esperadas por segundo. |
|Escritos/seg por região|Número de escritos esperado por segundo. |

Depois de preencher os dados necessários, **selecione Calcular**. O **separador Estimativa de Custos** mostra o custo total de armazenamento e produção a provisionada. Pode expandir o link 'Detalhes do **Espetáculo'** neste separador para obter a desagregação da produção necessária para pedidos de leitura e escrita. Sempre que alterar o valor de qualquer campo, **selecione Calcular** para re-calcular o custo estimado. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/basic-mode.png" alt-text="Modo básico de planejador de capacidade":::

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Estimativa de produção e custos previstos através do modo avançado

O modo avançado permite-lhe fornecer mais configurações que impactem a estimativa RU/s. Para utilizar esta opção, navegue até ao [planificador de capacidade](https://cosmos.azure.com/capacitycalculator/) e inscreva-se na ferramenta com uma conta que utiliza para o Azure. A opção de inscrição está disponível no canto direito. 

Depois de iniciar sedutada, pode ver campos adicionais em comparação com os campos em modo básico. Introduza os parâmetros adicionais com base na sua carga de trabalho. 

|**Entrada**  |**Descrição**  |
|---------|---------|
|API|Azure Cosmos DB é um serviço multi-modelo e multi-API. Para novas cargas de trabalho, selecione API SQL (Core). |
|Número de regiões|AZure Cosmos DB está disponível em todas as regiões do Azure. Selecione o número de regiões necessárias para a sua carga de trabalho. Pode associar várias regiões à sua conta Cosmos. Consulte [a distribuição global](distribute-data-globally.md) em Azure Cosmos DB para mais detalhes.|
|Várias regiões escrevem|Se ativar [as gravações multi-regiões,](distribute-data-globally.md#key-benefits-of-global-distribution)a sua aplicação pode ler e escrever para qualquer região do Azure. Se desativar as gravações multi-regiões, a sua aplicação pode escrever dados para uma única região. <br/><br/> Ativar as gravações multi-regiões se espera ter uma carga de trabalho ativa que requer baixas escritas de latência em diferentes regiões. Por exemplo, uma carga de trabalho IOT que escreve dados para a base de dados em volumes elevados em diferentes regiões. <br/><br/> A multi-região escreve que garante 99,999% de leitura e disponibilidade de escrita. As escritas multi-regiões requerem mais produção quando comparadas com as regiões de escrita única. Para saber mais, veja [como as RUs são diferentes para o artigo de regiões de escrita única e múltipla.](optimize-cost-regions.md)|
|Consistência padrão|O Azure Cosmos DB suporta 5 níveis de consistência, para permitir que os desenvolvedores equilibrem a troca entre a consistência, a disponibilidade e as trocas de latência. Para saber mais, consulte o artigo [níveis de consistência.](consistency-levels.md) <br/><br/> Por padrão, a Azure Cosmos DB usa a consistência da sessão, o que garante a capacidade de ler as suas próprias escritas numa sessão. <br/><br/> A escolha de uma estagnação forte ou limitada exigirá o dobro dos RU/s necessários para leituras, quando comparados com a sessão, prefixo consistente e eventual consistência. A forte coerência com as escritas multi-regiões não é suportada e automaticamente por defeito às escreveções de uma região única com forte consistência. |
|Política de indexação|Por padrão, a Azure Cosmos DB [indexa todas as propriedades](index-policy.md) em todos os itens para consultas flexíveis e eficientes (mapas para a política de indexação **automática).** <br/><br/> Se **escolher,** nenhuma das propriedades está indexada. Isto resulta na menor taxa ru para escritas. Selecione **a** política se espera apenas fazer [leituras de pontos](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?preserve-view=true&view=azure-dotnet) (análises de valor chave) e/ou escritas, e sem consultas. <br/><br/> A política de indexação personalizada permite-lhe incluir ou excluir propriedades específicas do índice para menor produção e armazenamento de escrita. Para saber mais, consulte [a política de indexação](index-overview.md) e os artigos [de política de indexação de amostras.](how-to-manage-indexing-policy.md#indexing-policy-examples)|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB numa única região.|
|Modo de carga de trabalho|**Selecione Steady** se o seu volume de carga de trabalho for constante. <br/><br/> Selecione **Variável** se o seu volume de carga de trabalho mudar ao longo do tempo.  Por exemplo, durante um dia ou um mês específico. <br/><br/> As seguintes definições estão disponíveis se escolher a opção de carga de trabalho variável:<ul><li>Percentagem de tempo no pico: Percentagem de tempo num mês em que a sua carga de trabalho requer pico (mais alto) de produção. <br/><br/> Por exemplo, se tiver uma carga de trabalho elevada durante as 9h às 18h, então a percentagem de tempo no pico é: 45 horas no pico / 730 horas/mês = ~6%.<br/><br/></li><li>Leituras/seg por região no pico - Número de leituras esperadas por segundo no pico.</li><li>Writes/sec por região no pico – Número de escritos esperados por segundo no pico.</li><li>Leituras/seg por região fora do pico - Número de leituras esperadas por segundo durante o pico off.</li><li>Writes/sec por região fora do pico - Número de writes esperados por segundo durante o pico off.</li></ul>Com intervalos de pico e de pico, pode otimizar o seu custo [aumentando programáticamente a sua produção provisitada para](set-throughput.md#update-throughput-on-a-database-or-a-container) cima e para baixo em conformidade.|
|Tamanho do item|O tamanho do item de dados (por exemplo, documento), que varia de 1 KB a 2 MB. <br/><br/>Também pode enviar o documento **da amostra (JSON)** para uma estimativa mais precisa.<br/><br/>Se a sua carga de trabalho tiver vários tipos de itens (com diferentes conteúdos JSON) no mesmo recipiente, pode carregar vários documentos JSON e obter a estimativa. Utilize o botão **de artigo novo** adicionar vários documentos JSON de amostra múltipla.|

Também pode utilizar o botão **'Estimativa de Poupança'** para descarregar um ficheiro CSV que contenha a estimativa atual. 

:::image type="content" source="./media/estimate-ru-with-capacity-planner/advanced-mode.png" alt-text="Modo avançado de planejador de capacidade":::

Os preços indicados no planificador de capacidades da Azure Cosmos são estimativas baseadas nas taxas de preços públicos para produção e armazenamento. Todos os preços são mostrados em dólares americanos. Consulte a [página de preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para ver todas as tarifas por região.  

## <a name="estimating-throughput-for-queries"></a>Estimativa da produção para consultas

A calculadora de capacidade azure Cosmos assume leituras de ponto (uma leitura de um único item, por exemplo, documento, por ID e valor chave de partição) e escreve para a carga de trabalho. Para estimar a produção necessária para consultas, execute a sua consulta num conjunto de dados representativos num recipiente cosmos e [obtenha a taxa RU](find-request-unit-charge.md). Multiplique a carga RU pelo número de consultas que prevê correr por segundo para obter o total RU/s necessário. 

Por exemplo, se a sua carga de trabalho necessitar de uma consulta, ``SELECT * FROM c WHERE c.id = 'Alice'`` que é executada 100 vezes por segundo, e a carga RU da consulta é de 10 RUs, você precisará de 100 consultas / seg * 10 RU /consulta = 1000 RU/s no total para servir estes pedidos. Adicione estes RU/s ao RU/s necessário para quaisquer leituras ou escritos que ocorram na sua carga de trabalho.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [o modelo de preços da Azure Cosmos DB.](how-pricing-works.md)
* Crie uma nova [conta Cosmos, base de dados e contentor.](create-cosmosdb-resources-portal.md)
* Saiba como [otimizar o custo de produção a provisionado.](optimize-cost-throughput.md)
* Saiba como [otimizar o custo com capacidade reservada.](cosmos-db-reserved-capacity.md)