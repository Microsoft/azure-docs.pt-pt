---
title: Estimar os custos utilizando o planejador de capacidade saca-do-bD da Azure Cosmos
description: O planificador de capacidade síbdo Azure Cosmos permite-lhe estimar o custo necessário e o custo para a sua carga de trabalho. Este artigo descreve como usar a nova versão do planificador de capacidade para estimar a entrada e o custo necessários.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dech
ms.openlocfilehash: f10ace47f774e31b586f7736f5fb8e5dfea0c948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68707633"
---
# <a name="estimate-rus-using-the-azure-cosmos-db-capacity-planner"></a>Estimar RU/s usando o planejador de capacidade sbS Do Cosmos Azure

Configurar as suas bases de dados e contentores Azure Cosmos com a quantidade certa de entrada aprovisionada, ou Unidades de [Pedido (RU/s),](request-units.md)para a sua carga de trabalho é essencial para otimizar o custo e o desempenho. Este artigo descreve como usar o [planejador](https://cosmos.azure.com/capacitycalculator/) de capacidade saqueador de capacidade sbs Do Azure Cosmos para obter uma estimativa do RU/s necessário e do custo da sua carga de trabalho. 

## <a name="how-to-estimate-throughput-and-cost-with-azure-cosmos-db-capacity-planner"></a>Como estimar a entrada e o custo com o planejador de capacidade seleções da Azure Cosmos DB

O planificador de capacidade pode ser usado em dois modos.

|**Modo**  |**Descrição**  |
|---------|---------|
|Básico|Fornece uma estimativa rápida e de custos e ru's de alto nível. Este modo pressupõe as definições padrão de Azure Cosmos DB para indexação da política, consistência e outros parâmetros. <br/><br/>Utilize o modo básico para uma estimativa rápida e de alto nível quando estiver a avaliar uma potencial carga de trabalho para funcionar no Azure Cosmos DB.|
|Avançado|Fornece uma estimativa de custos e RU/s mais detalhada, com a capacidade de afinar configurações adicionais — política de indexação, nível de consistência e outros parâmetros que afetam o custo e a potência. <br/><br/>Utilize o modo avançado quando estiver a estimar ru/s para um novo projeto ou queira uma estimativa mais detalhada. |


## <a name="estimate-provisioned-throughput-and-cost-using-basic-mode"></a>Estimativa de rendimento e custos previstos utilizando o modo básico
Para obter uma estimativa rápida da sua carga de trabalho utilizando o modo básico, navegue até ao [planificador](https://cosmos.azure.com/capacitycalculator/)de capacidade . Introduza nos seguintes parâmetros com base na sua carga de trabalho: 

|**Entrada**  |**Descrição**  |
|---------|---------|
|Número de regiões|Azure Cosmos DB está disponível em todas as regiões de Azure. Selecione o número de regiões necessárias para a sua carga de trabalho. Pode associar várias regiões à sua conta Cosmos. Consulte a [distribuição global](distribute-data-globally.md) no Azure Cosmos DB para mais detalhes.|
|Escreve várias regiões|Se permitir [a escrita em várias regiões,](distribute-data-globally.md#key-benefits-of-global-distribution)a sua aplicação pode ler e escrever para qualquer região do Azure. Se desativar as escritas multi-regiões, a sua aplicação pode escrever dados para uma única região. <br/><br/> Enable multi-region escreve se você espera ter uma carga de trabalho ativa ativa que requer baixa latência escrita em diferentes regiões. Por exemplo, uma carga de trabalho da IOT que escreve dados para a base de dados em volumes elevados em diferentes regiões. <br/><br/> A multi-região escreve que garante 99,999% de leitura e de disponibilidade de escrita. Os escritos multi-regiões requerem mais entrada quando comparados com as regiões de escrita única. Para saber mais, veja como as RUs são diferentes para o artigo de [regiões únicas e múltiplas.](optimize-cost-regions.md)|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB numa única região.|
|Tamanho do item|A dimensão estimada do item de dados (por exemplo, documento), que varia entre 1 KB e 2 MB. |
|Leituras/sec por região|Número de leituras esperadas por segundo. |
|Escritos/seg por região|Número de escritos esperados por segundo. |

Depois de preencher os detalhes necessários, **selecione Calcular**. O separador Estimativa de **Custos** mostra o custo total para armazenamento e produção provisionada. Pode expandir o link **Show Details** neste separador para obter a repartição da entrada necessária para ler e escrever pedidos. Cada vez que alterar o valor de qualquer campo, selecione **Calcular** para recalcular o custo estimado. 

![Modo básico de planejador de capacidade](./media/estimate-ru-with-capacity-planner/basic-mode.png)

## <a name="estimate-provisioned-throughput-and-cost-using-advanced-mode"></a>Estimativa de entrada e custo provisionados utilizando modo avançado

O modo avançado permite-lhe fornecer mais definições que impactam a estimativa ru/s. Para utilizar esta opção, navegue para o planejador de [capacidades](https://cosmos.azure.com/capacitycalculator/) e inscreva-se na ferramenta com uma conta que utiliza para o Azure. A opção de inscrição está disponível no canto direito. 

Depois de iniciar sessão, pode ver campos adicionais em comparação com os campos em modo básico. Introduza os parâmetros adicionais com base na sua carga de trabalho. 

|**Entrada**  |**Descrição**  |
|---------|---------|
|API|Azure Cosmos DB é um serviço multi-modelo e multi-API. Para novas cargas de trabalho, selecione API SQL (Core). |
|Número de regiões|Azure Cosmos DB está disponível em todas as regiões de Azure. Selecione o número de regiões necessárias para a sua carga de trabalho. Pode associar várias regiões à sua conta Cosmos. Consulte a [distribuição global](distribute-data-globally.md) no Azure Cosmos DB para mais detalhes.|
|Escreve várias regiões|Se permitir [a escrita em várias regiões,](distribute-data-globally.md#key-benefits-of-global-distribution)a sua aplicação pode ler e escrever para qualquer região do Azure. Se desativar as escritas multi-regiões, a sua aplicação pode escrever dados para uma única região. <br/><br/> Enable multi-region escreve se você espera ter uma carga de trabalho ativa ativa que requer baixa latência escrita em diferentes regiões. Por exemplo, uma carga de trabalho da IOT que escreve dados para a base de dados em volumes elevados em diferentes regiões. <br/><br/> A multi-região escreve que garante 99,999% de leitura e de disponibilidade de escrita. Os escritos multi-regiões requerem mais entrada quando comparados com as regiões de escrita única. Para saber mais, veja como as RUs são diferentes para o artigo de [regiões únicas e múltiplas.](optimize-cost-regions.md)|
|Consistência por defeito|A Azure Cosmos DB suporta 5 níveis de consistência, para permitir aos desenvolvedores equilibrar a compensação entre consistência, disponibilidade e trocas de latência. Para saber mais, consulte o artigo sobre os níveis de [consistência.](consistency-levels.md) <br/><br/> Por padrão, o Azure Cosmos DB utiliza a consistência da sessão, o que garante a capacidade de ler os seus próprios escritos numa sessão. <br/><br/> Escolher estande forte ou limitada exigirá o dobro do RU/s necessário para leituras, quando comparado com a sessão, prefixo consistente e eventual consistência. A forte coerência com as escritas multi-regiões não é suportada e irá automaticamente faltar aos escritos de uma região única com forte consistência. |
|Política de indexação|Por padrão, o Azure Cosmos DB [indexa todas as propriedades](index-policy.md) em todos os itens para consultas flexíveis e eficientes (mapas para a política de indexação **automática).** <br/><br/> Se **optar,** nenhuma das propriedades está indexada. Isto resulta na menor carga de RU para escritos. Selecione a política **de exclusão** se espera apenas fazer leituras de [pontos](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) (procurações de valor chave) e/ou escreve, e sem consultas. <br/><br/> A política de indexação personalizada permite-lhe incluir ou excluir propriedades específicas do índice para uma menor sobrevisão e armazenamento de escrita. Para saber mais, consulte a [política de indexação](index-overview.md) e os artigos de política de [indexação](how-to-manage-indexing-policy.md#indexing-policy-examples) de amostras.|
|Total de dados armazenados (por região)|Total de dados estimados armazenados em GB numa única região.|
|Modo de carga de trabalho|Selecione **Estável** se o seu volume de carga de trabalho for constante. <br/><br/> Selecione **Variável** se o volume de carga de trabalho mudar ao longo do tempo.  Por exemplo, durante um dia específico ou um mês. <br/><br/> As seguintes definições estão disponíveis se escolher a opção de carga de trabalho variável:<ul><li>Percentagem de tempo no pico: Percentagem de tempo num mês em que a sua carga de trabalho requer pico (maior) de entrada. <br/><br/> Por exemplo, se tiver uma carga de trabalho que tenha alta atividade durante as 9h às 18h horas úteis, então a percentagem de tempo no pico é: 45 horas no pico / 730 horas /mês = ~6%.<br/><br/></li><li>Leituras/sec por região no pico - Número de leituras esperadas por segundo no pico.</li><li>Escreve/seg por região no pico – Número de escritos esperados por segundo no pico.</li><li>Leituras/sec por região fora do pico – Número de leituras esperadas por segundo durante o pico.</li><li>Escreve/seg por região fora do pico – Número de escritos esperados por segundo durante o pico off.</li></ul>Com intervalos de pico e de pico, pode otimizar o seu custo [escalando programáticamente a sua entrada prevista](set-throughput.md#update-throughput-on-a-database-or-a-container) para cima e para baixo em conformidade.|
|Tamanho do item|O tamanho do item de dados (por exemplo, documento), que varia entre 1 KB e 2 MB. <br/><br/>Também pode carregar o documento **da amostra (JSON)** para uma estimativa mais precisa.<br/><br/>Se a sua carga de trabalho tiver vários tipos de itens (com diferentes conteúdos JSON) no mesmo recipiente, pode carregar vários documentos da JSON e obter a estimativa. Utilize o botão **adicionar novo item** para adicionar vários documentos JSON de amostra.|

Também pode utilizar o botão **'Estimativa de Guardar'** para descarregar um ficheiro CSV contendo a estimativa atual. 

![Modo avançado de planejador de capacidade](./media/estimate-ru-with-capacity-planner/advanced-mode.png)

Os preços apresentados no planificador de capacidade saca-do-bD do Azure Cosmos são estimativas baseadas nas taxas de preços públicas para o resultado e armazenamento. Todos os preços são mostrados em dólares americanos. Consulte a página de [preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) para ver todas as tarifas por região.  

## <a name="estimating-throughput-for-queries"></a>Estimativa de entrada para consultas

A calculadora de capacidade da Azure Cosmos assume que se lê (uma leitura de um único item, por exemplo, documento, por ID e valor-chave de partição) e escreve para a carga de trabalho. Para estimar a entrada necessária para consultas, execute a sua consulta num conjunto de dados representativo num recipiente Cosmos e [obtenha a carga RU](find-request-unit-charge.md). Multiplique a carga ru pelo número de consultas que antecipa executar por segundo para obter o total de RU/s necessário. 

Por exemplo, se a sua carga ``SELECT * FROM c WHERE c.id = 'Alice'`` de trabalho requer uma consulta, que é executada 100 vezes por segundo, e a carga RU da consulta é de 10 RUs, você precisará de 100 consultas / sec * 10 RU / consulta = 1000 RU/s no total para servir estes pedidos. Adicione estes RU/s aos RU/s necessários para qualquer leitura ou escrita que aconteça na sua carga de trabalho.

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o modelo de [preços da Azure Cosmos DB.](how-pricing-works.md)
* Crie uma nova [conta Cosmos, base de dados e contentor.](create-cosmosdb-resources-portal.md)
* Aprenda a otimizar o [custo de entrada provisionado.](optimize-cost-throughput.md)
* Aprenda a [otimizar o custo com capacidade reservada.](cosmos-db-reserved-capacity.md)

