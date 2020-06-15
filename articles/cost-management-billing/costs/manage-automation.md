---
title: Gerir os custos do Azure com a automatização
description: Este artigo explica como pode gerir os custos do Azure com a automatização.
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449836"
---
# <a name="manage-costs-with-automation"></a>Gerir os custos com a automatização

Pode utilizar a automatização do Cost Management para criar um conjunto personalizado de soluções para obter e gerir os dados dos custos. Este artigo aborda cenários comuns de automatização do Cost Management e as opções disponíveis em função da sua situação. Se quiser programar com APIs, apresentamos exemplos de pedidos à API comuns para ajudar a acelerar o processo de programação.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizar a obtenção de dados de custos para análise offline

Poderá ter de transferir os dados de custos do Azure para os combinar com outros conjuntos de dados. Ou poderá ter de integrar os dados de custos nos seus próprios sistemas. Existem várias opções disponíveis dependendo da quantidade de dados envolvida. Em qualquer dos casos, tem de ter permissões no âmbito adequado do Cost Management para utilizar as APIs e as ferramentas. Para obter mais informações, veja [Atribuir acesso aos dados](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data).

## <a name="suggestions-for-handling-large-datasets"></a>Sugestões para processar conjuntos de dados grandes

Se a sua organização tiver uma forte presença do Azure em muitos recursos ou subscrições, terá uma grande quantidade de dados de detalhes de utilização. O Excel muitas vezes não consegue carregar ficheiros tão grandes. Nesta situação, recomendamos as seguintes opções:

**Power BI**

O Power BI é utilizado para ingerir e processar grandes quantidades de dados. Se for cliente do Contrato Enterprise, poderá utilizar a aplicação de modelo do Power BI para analisar os custos da sua conta de faturação. O relatório contém as principais vistas utilizadas pelos clientes. Para obter mais informações, veja [Analisar os custos do Azure com a aplicação de modelo do Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).

**Conector de dados do Power BI**

Se quiser analisar os dados diariamente, recomendamos que utilize o [conector de dados do Power BI](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management) para obter os dados para a análise detalhada. Qualquer relatório que criar será mantido atualizado pelo conector à medida que são acumulados mais custos.

**Exportações do Cost Management**

Pode não precisar de analisar os dados diariamente. Se assim for, pondere utilizar a funcionalidade [Exportações](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) do Cost Management para agendar exportações de dados para uma Conta de armazenamento do Azure. Em seguida, pode carregar os dados no Power BI, se necessário, ou analisá-los no Excel se o ficheiro for suficientemente pequeno. As exportações estão disponíveis no portal do Azure ou pode configurá-las com a [API de Exportações](https://docs.microsoft.com/rest/api/cost-management/exports).

**API de Detalhes de Utilização**

Pondere utilizar a [API de Detalhes de Utilização](https://docs.microsoft.com/rest/api/consumption/usageDetails) se tiver um conjunto de dados de custos pequeno. Se tiver uma grande quantidade de dados de custos, deverá pedir a mais pequena quantidade possível de dados de utilização para um determinado período. Para o fazer, especifique um intervalo de tempo pequeno ou utilize um filtro no pedido. Por exemplo, num cenário em que são necessários três anos de dados de custos, a API funciona melhor quando faz várias chamadas para intervalos de tempo diferentes em vez do que com uma única chamada. A partir daí, pode carregar os dados no Excel para uma análise mais detalhada.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizar a obtenção com a API de Detalhes de Utilização

A [API de Detalhes de Utilização ](https://docs.microsoft.com/rest/api/consumption/usageDetails) proporciona uma forma fácil de obter dados de custos não processados nem agregados que correspondam à sua fatura do Azure. A API é útil quando a sua organização precisa de uma solução de obtenção de dados através de programação. Pondere utilizar a API se quiser analisar conjuntos de dados de custos mais pequenos. No entanto, caso tenha conjuntos de dados maiores, deve utilizar as outras soluções identificadas anteriormente. Os dados dos Detalhes de Utilização são disponibilizados por medidor e por dia. São utilizados no cálculo da fatura mensal. A versão de disponibilidade geral destas APIs é `2019-10-01`. Utilize a `2019-04-01-preview` para aceder à versão de pré-visualização para reserva e compras do Azure Marketplace com as APIs.

### <a name="usage-details-api-suggestions"></a>Sugestões da API de Detalhes de Utilização

**Agendamento do pedido**

Recomendamos que _não faça mais do que um pedido_ à API de Detalhes de Utilização por dia. Para obter mais informações sobre a frequência com que os dados de custos são atualizados e como se processa o arredondamento, veja [Compreender os dados do Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule).

**Estabelecer âmbitos de nível superior sem filtragem**

Utilize a API para obter todos os dados de que precisa no âmbito mais alto disponível. Aguarde até que todos os dados necessários sejam ingeridos antes de utilizar a filtragem, o agrupamento ou a análise agregada. A API está otimizada especificamente para proporcionar grandes quantidades de dados de custos não processados nem agregados. Para obter mais informações sobre os âmbitos disponíveis no Cost Management, veja [Compreender e trabalhar com âmbitos](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes). Assim que tiver transferido os dados necessários para um âmbito, utilize o Excel para analisar melhor os dados com filtros e tabelas dinâmicas.

## <a name="example-usage-details-api-requests"></a>Exemplo de pedidos à API de Detalhes de Utilização

Os exemplos seguintes de pedidos são utilizados pelos clientes da Microsoft para lidar com cenários comuns que poderá encontrar.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Obter os Detalhes de Utilização de um âmbito para um intervalo de datas específico

Os dados devolvidos pelo pedido correspondem à data em que o sistema de faturação recebeu a utilização. Poderão incluir custos de várias faturas.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>Obter detalhes de custos amortizados

Se precisar que os custos reais mostrem as compras à medida que são acumulados, altere a *métrica* para `ActualCost` no pedido seguinte. Para utilizar custos amortizados e reais, tem de utilizar a versão `2019-04-01-preview`. A versão atual da API funciona da mesma maneira que a versão `2019-10-01`, com exceção do novo atributo tipo/métrica e os nomes de propriedade alterados. Se tiver um Contrato de Cliente Microsoft, os filtros são `startDate` e `endDate` no exemplo seguinte.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Obter conjuntos de dados de custos de grande tamanho de forma recorrente com as Exportações

A funcionalidade Exportações é uma solução que permite agendar as capturas de dados de custos de forma regular. É a forma recomendada para obter dados de custos não agregados para as organizações cujos ficheiros de utilização podem ser demasiado grandes para chamar e transferir dados de forma fiável com a API de Detalhes de Utilização. Os dados são colocados numa Conta de armazenamento do Azure à sua escolha. A partir daí, pode carregá-los nos seus próprios sistemas e analisá-los conforme necessário pelas suas equipas. Para configurar as exportações no portal do Azure, veja [Exportar dados](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data).

## <a name="data-latency-and-rate-limits"></a>Latência de dados e limites de velocidade

Recomendamos que as APIs não sejam chamadas mais do que uma vez por dia. Os dados do Cost Management são atualizados a cada quatro horas à medida que são recebidos os novos dados de utilização dos fornecedores de recursos do Azure. Chamar com maior frequência não permite obter dados adicionais. Pelo contrário, criará uma carga maior. Para saber mais sobre a frequência com que os dados são alterados e como se processa a latência dos dados, veja [Compreender os dados do Cost Management](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Código de erro 429 – O número de chamadas excedeu os limites

Para proporcionar uma experiência consistente a todos os subscritores do Cost Management, as APIs do Cost Management têm limites. Quando atingir o limite, recebe o código de estado HTTP `429: Too many requests`. Os limites de débito atuais das nossas APIs são os seguintes:

- 30 chamadas por minuto – a contar por âmbito, por utilizador ou aplicação.
- 200 chamadas por minuto – a contar por inquilino, por utilizador ou aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Analisar os custos do Azure com a aplicação de modelo do Power BI](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app).
- [Criar e gerir dados exportados](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data) com as Exportações.
- Saiba mais sobre a [API de Detalhes de Utilização](https://docs.microsoft.com/rest/api/consumption/usageDetails).