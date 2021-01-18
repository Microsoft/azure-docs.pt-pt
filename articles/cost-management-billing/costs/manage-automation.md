---
title: Gerir os custos do Azure com a automatização
description: Este artigo explica como pode gerir os custos do Azure com a automatização.
author: bandersmsft
ms.author: banders
ms.date: 01/06/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 02215bace693ac5ac36f9fc29758215d45b23eb1
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98051790"
---
# <a name="manage-costs-with-automation"></a>Gerir os custos com a automatização

Pode utilizar a automatização do Cost Management para criar um conjunto personalizado de soluções para obter e gerir os dados dos custos. Este artigo aborda cenários comuns de automatização do Cost Management e as opções disponíveis em função da sua situação. Se quiser programar com APIs, apresentamos exemplos de pedidos à API comuns para ajudar a acelerar o processo de programação.

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>Automatizar a obtenção de dados de custos para análise offline

Poderá ter de transferir os dados de custos do Azure para os combinar com outros conjuntos de dados. Ou poderá ter de integrar os dados de custos nos seus próprios sistemas. Existem várias opções disponíveis dependendo da quantidade de dados envolvida. Em qualquer dos casos, tem de ter permissões no âmbito adequado do Cost Management para utilizar as APIs e as ferramentas. Para obter mais informações, veja [Atribuir acesso aos dados](./assign-access-acm-data.md).

## <a name="suggestions-for-handling-large-datasets"></a>Sugestões para processar conjuntos de dados grandes

Se a sua organização tiver uma forte presença do Azure em muitos recursos ou subscrições, terá uma grande quantidade de dados de detalhes de utilização. O Excel muitas vezes não consegue carregar ficheiros tão grandes. Nesta situação, recomendamos as seguintes opções:

**Power BI**

O Power BI é utilizado para ingerir e processar grandes quantidades de dados. Se for cliente do Contrato Enterprise, poderá utilizar a aplicação de modelo do Power BI para analisar os custos da sua conta de faturação. O relatório contém as principais vistas utilizadas pelos clientes. Para obter mais informações, veja [Analisar os custos do Azure com a aplicação de modelo do Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).

**Conector de dados do Power BI**

Se quiser analisar os dados diariamente, recomendamos que utilize o [conector de dados do Power BI](/power-bi/connect-data/desktop-connect-azure-cost-management) para obter os dados para a análise detalhada. Qualquer relatório que criar será mantido atualizado pelo conector à medida que são acumulados mais custos.

**Exportações do Cost Management**

Pode não precisar de analisar os dados diariamente. Se assim for, pondere utilizar a funcionalidade [Exportações](./tutorial-export-acm-data.md) do Cost Management para agendar exportações de dados para uma Conta de armazenamento do Azure. Em seguida, pode carregar os dados no Power BI, se necessário, ou analisá-los no Excel se o ficheiro for suficientemente pequeno. As exportações estão disponíveis no portal do Azure ou pode configurá-las com a [API de Exportações](/rest/api/cost-management/exports).

**API de Detalhes de Utilização**

Pondere utilizar a [API de Detalhes de Utilização](/rest/api/consumption/usageDetails) se tiver um conjunto de dados de custos pequeno. Se tiver uma grande quantidade de dados de custos, deverá pedir a mais pequena quantidade possível de dados de utilização para um determinado período. Para o fazer, especifique um intervalo de tempo pequeno ou utilize um filtro no pedido. Por exemplo, num cenário em que são necessários três anos de dados de custos, a API funciona melhor quando faz várias chamadas para intervalos de tempo diferentes em vez do que com uma única chamada. A partir daí, pode carregar os dados no Excel para uma análise mais detalhada.

## <a name="automate-retrieval-with-usage-details-api"></a>Automatizar a obtenção com a API de Detalhes de Utilização

A [API de Detalhes de Utilização ](/rest/api/consumption/usageDetails) proporciona uma forma fácil de obter dados de custos não processados nem agregados que correspondam à sua fatura do Azure. A API é útil quando a sua organização precisa de uma solução de obtenção de dados através de programação. Pondere utilizar a API se quiser analisar conjuntos de dados de custos mais pequenos. No entanto, caso tenha conjuntos de dados maiores, deve utilizar as outras soluções identificadas anteriormente. Os dados dos Detalhes de Utilização são disponibilizados por medidor e por dia. São utilizados no cálculo da fatura mensal. A versão de disponibilidade geral destas APIs é `2019-10-01`. Utilize a `2019-04-01-preview` para aceder à versão de pré-visualização para reserva e compras do Azure Marketplace com as APIs.

### <a name="usage-details-api-suggestions"></a>Sugestões da API de Detalhes de Utilização

**Agendamento do pedido**

Recomendamos que _não faça mais do que um pedido_ à API de Detalhes de Utilização por dia. Para obter mais informações sobre a frequência com que os dados de custos são atualizados e como se processa o arredondamento, veja [Compreender os dados do Cost Management](./understand-cost-mgt-data.md).

**Estabelecer âmbitos de nível superior sem filtragem**

Utilize a API para obter todos os dados de que precisa no âmbito mais alto disponível. Aguarde até que todos os dados necessários sejam ingeridos antes de utilizar a filtragem, o agrupamento ou a análise agregada. A API está otimizada especificamente para proporcionar grandes quantidades de dados de custos não processados nem agregados. Para obter mais informações sobre os âmbitos disponíveis no Cost Management, veja [Compreender e trabalhar com âmbitos](./understand-work-scopes.md). Assim que tiver transferido os dados necessários para um âmbito, utilize o Excel para analisar melhor os dados com filtros e tabelas dinâmicas.

### <a name="notes-about-pricing"></a>Notas sobre os preços

Se quiser reconciliar a utilização e as cobranças com a sua folha de preços ou fatura, tenha em atenção as seguintes informações.

Comportamento dos preços da Folha de Preços - Os preços apresentados na folha de preços são os preços que recebe do Azure. Estes preços estão dimensionados para uma unidade de medida específica. Infelizmente, a unidade de medida nem sempre está em linha com a unidade de medida com base na qual a utilização e as cobranças reais dos recursos são emitidas.

Comportamento dos preços dos Detalhes de Utilização - Os ficheiros de utilização mostram informações dimensionadas que podem não corresponder precisamente à folha de preços. Especificamente:

- Preço Unitário - O preço é dimensionado para corresponder à unidade de medida com base na qual as cobranças são de facto emitidas pelos recursos do Azure. Se o dimensionamento ocorrer, o preço não corresponderá ao preço apresentado na Folha de Preços.
- Unidade de medida - Representa a unidade de medida com base na qual as cobranças são de facto emitidas pelos recursos do Azure.
- Preço Efetivo/Tarifa de Recursos - O preço representa a tarifa real que acaba por pagar por unidade após a aplicação de descontos. É o preço que tem de ser utilizado com a Quantidade para fazer cálculos de Quantidade-Preço para reconciliar as cobranças. O preço tem em consideração os seguintes cenários e o preço unitário dimensionado que também está presente nos ficheiros. Por conseguinte, o mesmo pode diferir do preço unitário dimensionado.
  - Preços em camadas - Por exemplo: 10 $ para as primeiras 100 unidades, 8 $ para as 100 unidades seguintes.
  - Quantidade incluída - Por exemplo: as primeiras 100 unidades são gratuitas e, em seguida, passa a 10 $ por unidade.
  - Reservas
  - Arredondamento que ocorre durante o cálculo - O arredondamento tem em conta a quantidade consumida, os preços da quantidade incluída/em camadas e o preço unitário dimensionado.

## <a name="example-usage-details-api-requests"></a>Exemplo de pedidos à API de Detalhes de Utilização

Os exemplos seguintes de pedidos são utilizados pelos clientes da Microsoft para lidar com cenários comuns que poderá encontrar.

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>Obter os Detalhes de Utilização de um âmbito para um intervalo de datas específico

Os dados devolvidos pelo pedido correspondem à data em que o sistema de faturação recebeu a utilização. Poderão incluir custos de várias faturas. A chamada a utilizar varia de acordo com o seu tipo de subscrição.

Relativamente a clientes legados com Contrato Enterprise (EA) ou uma subscrição pay as you go, utilize a chamada seguinte:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01
```

Relativamente aos clientes com um Contrato de Cliente Microsoft, utilize a chamada seguinte:

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?startDate=2020-08-01&endDate=&2020-08-05$top=1000&api-version=2019-10-01
```

### <a name="get-amortized-cost-details"></a>Obter detalhes de custos amortizados

Se precisar que os custos reais mostrem as compras à medida que são acumulados, altere a *métrica* para `ActualCost` no pedido seguinte. Para utilizar custos amortizados e reais, tem de utilizar a versão `2019-04-01-preview`. A versão atual da API funciona da mesma maneira que a versão `2019-10-01`, com exceção do novo atributo tipo/métrica e os nomes de propriedade alterados. Se tiver um Contrato de Cliente Microsoft, os filtros são `startDate` e `endDate` no exemplo seguinte.

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>Obter conjuntos de dados de custos de grande tamanho de forma recorrente com as Exportações

Pode exportar regularmente grandes volumes de dados com exportações a partir do Cost Management. A exportação é o método recomendado para a obtenção de dados de custo não agregados. Em especial quando os ficheiros de utilização são demasiado grandes para serem chamados e transferidos de forma fiável com a API de Detalhes de Utilização. Os dados exportados são colocados na conta de Armazenamento do Azure escolhida por si. A partir daí, pode carregá-los nos seus próprios sistemas e analisá-los conforme necessário. Para configurar as exportações no portal do Azure, veja [Exportar dados](tutorial-export-acm-data.md).

Se pretender automatizar as exportações em vários âmbitos, o exemplo de pedido de API apresentado na secção seguinte é um bom ponto de partida. Pode utilizar a API de Exportações para criar exportações automáticas como parte da configuração do ambiente geral. As exportações automáticas ajudam a garantir que tem os dados de que necessita. Pode utilizá-los nos sistemas da sua própria organização à medida que for expandindo a utilização do Azure.

### <a name="common-export-configurations"></a>Configurações de exportação comuns

Antes de criar a sua primeira exportação, reflita sobre o seu cenário e as opções de configuração de que necessita para o tornar possível. Considere as seguintes opções de exportação:

- **Periodicidade** – Determina a frequência com que a tarefa de exportação é executada quando um ficheiro é colocado na sua conta de Armazenamento do Azure. Escolha entre Diariamente, Semanalmente e Mensalmente. Tente configurar a periodicidade de modo a corresponder às tarefas de importação de dados utilizadas pelo sistema interno da sua organização.
- **Período de Periodicidade** – Determina o período de validade da Exportação. Os ficheiros só são exportados durante o período de periodicidade.
- **Intervalo de Tempo** – Determina o volume de dados gerado pela exportação numa determinada execução. As opções comuns são MonthToDate e WeekToDate.
- **StartDate** – Configura o início da agenda de exportações pretendido. É criada uma exportação na data de início (StartDate) e, mais tarde, com base na Periodicidade escolhida.
- **Tipo** – Existem três tipos de exportação:
  - ActualCost – Mostra o total da utilização e dos custos para o período especificado, à medida que os valores vão sendo acumulados, e apresenta-o na sua fatura.
  - AmortizedCost – Mostra o total da utilização e dos custos para o período especificado, com a amortização aplicada aos custos de compra de reserva aplicáveis.
  - Utilização – Todas as exportações criadas antes de 20 de julho de 2020 são do tipo Utilização. Atualize todas as exportações agendadas como ActualCost ou AmortizedCost.
- **Colunas** – Define os campos de dados que pretende ver incluídos no ficheiro de exportação. Coincidem com os campos disponíveis na API de Detalhes de Utilização. Para obter mais informações, consulte [API de Detalhes de Utilização](/rest/api/consumption/usagedetails/list).

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>Criar uma exportação do mês acumulada até hoje diária para uma subscrição

URL do Pedido: `PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>Automatizar alertas e ações com Orçamentos

Existem dois componentes essenciais para maximizar o valor do seu investimento na cloud. Um reporta à criação automática de orçamentos. O outro diz respeito à configuração da orquestração com base nos custos em resposta aos alertas de orçamento. A criação de orçamentos do Azure pode ser automatizada de diferentes maneiras. São emitidas várias respostas de alerta quando os limites de alerta configurados são excedidos.

As secções que se seguem abordam as opções disponíveis e fornecem exemplos de pedidos de API para poder começar a explorar a automatização de orçamentos.

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>Método de avaliação dos custos face ao limite de orçamento

Os custos são avaliados face ao limite de orçamento uma vez por dia. Quando cria um novo orçamento ou no dia de reposição do orçamento, a comparação dos custos com o limite será equivalente a zero ou nula porque a avaliação pode não ter ocorrido.

Quando o Azure deteta que os custos excederam o limite, é acionada uma notificação no espaço de uma hora após o período de deteção.

#### <a name="view-your-current-cost"></a>Ver o custo atual

Para ver os custos atuais, precisa de executar uma chamada GET através da [API de Consulta](/rest/api/cost-management/query).

Uma chamada GET para a API de Orçamentos não devolve os custos atuais mostrados na Análise de Custos. Em vez disso, a chamada devolve o último custo avaliado.

### <a name="automate-budget-creation"></a>Automatizar a criação de orçamentos

Pode automatizar a criação de orçamentos com a [API de Orçamentos](/rest/api/consumption/budgets). Também pode criar um orçamento com um [modelo de orçamento](quick-create-budget-template.md). Os modelos são uma maneira fácil de uniformizar as implementações do Azure, ao mesmo tempo que garantem uma configuração e imposição corretas do controlo de custos.

#### <a name="supported-locales-for-budget-alert-emails"></a>Regiões suportadas para e-mails de alerta de orçamento

Com os orçamentos, será alertado quando os custos ultrapassarem um limiar definido. Pode configurar até cinco destinatários de e-mail por orçamento. Os destinatários recebem os alertas de e-mail no prazo de 24 horas depois de os custos ultrapassarem o limiar do orçamento. No entanto, o destinatário poderá precisar de receber um e-mail num idioma diferente. Pode utilizar os seguintes códigos de cultura de idioma com a API de Orçamentos. Defina o código de cultura com o parâmetro `locale` conforme ilustrado no exemplo a seguir.

```json
{
  "eTag": "\"1d681a8fc67f77a\"",
  "properties": {
    "timePeriod": {
      "startDate": "2020-07-24T00:00:00Z",
      "endDate": "2022-07-23T00:00:00Z"
    },
    "timeGrain": "BillingMonth",
    "amount": 1,
    "currentSpend": {
      "amount": 0,
      "unit": "USD"
    },
    "category": "Cost",
    "notifications": {
      "actual_GreaterThan_10_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 20,
        "locale": "en-us",
        "contactEmails": [
          "user@contoso.com"
        ],
        "contactRoles": [],
        "contactGroups": [],
        "thresholdType": "Actual"
      }
    }
  }
}

```

Idiomas suportados por um código de cultura:

| Código de cultura| Linguagem |
| --- | --- |
| pt-pt | Inglês (Estados Unidos) |
| ja-jp | Japonês (Japão) |
| zh-cn | Chinês (Simplificado, China) |
| de-de | Alemão (Alemanha) |
| es-es | Espanhol (Espanha, Internacional) |
| fr-fr | Francês (França) |
| it-it | Italiano (Itália) |
| ko-kr | Coreano (Coreia) |
| pt-br | Português (Brasil) |
| ru-ru | Russo (Rússia) |
| zh-tw | Chinês (Tradicional, Taiwan) |
| cs-cz | Checo (República Checa) |
| pl-pl | Polaco (Polónia) |
| tr-tr | Turco (Turquia) |
| da-dk | Dinamarquês (Dinamarca) |
| dn-gb | Inglês (Reino Unido) |
| hu-hu | Húngaro (Hungria) |
| nb-bo | Norueguês Bokmål (Noruega) |
| nl-nl | Neerlandês (Países Baixos) |
| pt-pt | Português (Portugal) |
| sv-se | Sueco (Suécia) |

#### <a name="common-budgets-api-configurations"></a>Configurações comuns da API de Orçamentos

Existem várias maneiras de configurar um orçamento no ambiente do Azure. Reflita sobre o seu cenário primeiro e, em seguida, identifique as opções de configuração que o tornam possível. Analise as seguintes opções:

- **Intervalo de Agregação** - representa o período de periodicidade utilizado pelo seu orçamento para acumular e avaliar os custos. As opções mais comuns são Mensal, Trimestral e Anual.
- **Período de Tempo** – representa o período de validade do orçamento. O orçamento monitoriza e alerta ativamente o utilizador apenas enquanto é válido.
- **Notificações**
  - E-mails de Contacto – os endereços de e-mail recebem alertas quando um orçamento acumula custos e excede os limites definidos.
  - Funções de Contacto – todos os utilizadores com uma função do Azure correspondente no âmbito especificado recebem alertas por e-mail com esta opção. Por exemplo, os Proprietários de Subscrições podem receber um alerta relativo a um orçamento criado no âmbito das subscrições.
  - Grupos de Contacto – o orçamento chama os grupos de ações configurados quando o limite de um alerta é excedido.
- **Filtros de dimensão de custo** – a mesma filtragem que é possível fazer na Análise de Custos ou na API de Consulta também pode ser feita no seu orçamento. Utilize este filtro para reduzir o intervalo de custos que monitoriza com o orçamento.

Depois de identificar as opções de criação de orçamentos que satisfazem as suas necessidades, crie o orçamento com a API. Utilize o exemplo fornecido abaixo para começar com uma configuração de orçamento comum.

**Criar um orçamento filtrado para vários recursos e etiquetas**

URL do Pedido: `PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>Configurar a orquestração com base nos custos para alertas de orçamento

Pode configurar orçamentos para iniciar ações automatizadas através de Grupos de Ações do Azure. Para saber mais sobre como automatizar ações através de orçamentos, consulte [Automatização com Orçamentos do Azure](../manage/cost-management-budget-scenario.md).

## <a name="data-latency-and-rate-limits"></a>Latência de dados e limites de velocidade

Recomendamos que as APIs não sejam chamadas mais do que uma vez por dia. Os dados do Cost Management são atualizados a cada quatro horas à medida que são recebidos os novos dados de utilização dos fornecedores de recursos do Azure. Fazer chamadas com mais frequência não fornece mais dados. Pelo contrário, cria uma carga maior. Para saber mais sobre a frequência com que os dados são alterados e como se processa a latência dos dados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md).

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>Código de erro 429 – O número de chamadas excedeu os limites

Para proporcionar uma experiência consistente a todos os subscritores do Cost Management, as APIs do Cost Management têm limites. Quando atingir o limite, recebe o código de estado HTTP `429: Too many requests`. Os limites de débito atuais das nossas APIs são os seguintes:

- 30 chamadas por minuto – a contar por âmbito, por utilizador ou aplicação.
- 200 chamadas por minuto – a contar por inquilino, por utilizador ou aplicação.

## <a name="next-steps"></a>Passos seguintes

- [Analisar os custos do Azure com a aplicação de modelo do Power BI](./analyze-cost-data-azure-cost-management-power-bi-template-app.md).
- [Criar e gerir dados exportados](./tutorial-export-acm-data.md) com as Exportações.
- Saiba mais sobre a [API de Detalhes de Utilização](/rest/api/consumption/usageDetails).