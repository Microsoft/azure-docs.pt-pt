---
title: APIs REST do Azure Enterprise
description: Este artigo descreve as APIs REST para usar com a sua inscrição do Azure Enterprise.
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: c4c99142c64278514066efa8925ed8e3f6617235
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132589"
---
# <a name="azure-enterprise-rest-apis"></a>APIs REST do Azure Enterprise

Este artigo descreve as APIs REST para usar com a sua inscrição do Azure Enterprise. O artigo também explica como resolver problemas comuns relacionados com as APIs REST.

## <a name="consumption-and-usage-apis"></a>APIs de Consumo e Utilização

Os clientes Microsoft Azure Enterprise podem obter as informações de utilização e de faturação através das APIs REST. O proprietário de função (Administrador do Enterprise, Administrador de Departamento, Proprietário da Conta) tem de ativar o acesso à API através da geração de uma chave no Azure EA Portal. Depois disso, qualquer pessoa com o número e a chave da inscrição pode aceder aos dados através da API.

### <a name="available-apis"></a>APIs disponíveis

**Saldo e Resumo –** a [API de Saldo e Resumo](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) fornece um resumo mensal das informações sobre os saldos, as novas compras, os custos dos serviços do Azure Marketplace, os ajustes e os custos de utilização excedida. Para obter mais informações, veja [Reporting APIs for Enterprise customers – Balance and Summary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) (APIs de Relatórios para clientes Enterprise – Saldo e Resumo).

**Detalhes de Utilização –** a [API de Detalhes de Utilização](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) fornece uma discriminação diária das quantidades consumidas e dos custos estimados de uma inscrição. O resultado também inclui informações sobre as instâncias, os medidores e os departamentos. Pode consultar a API por período de faturação ou por uma data de início e de fim especificada. Para obter mais informações, veja [Reporting APIs for Enterprise customers – Usage Details](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) (APIs de Relatórios para clientes Enterprise – Detalhes de Utilização).

**Custo da Loja do Marketplace –** a [API de Custo da Loja do Marketplace](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) apresenta os custos do marketplace com base na utilização, discriminados por dia relativamente ao período de faturação especificado ou às datas de início e de fim. Para obter mais informações, veja [Reporting APIs for Enterprise customers – Marketplace Store Charge](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) (APIs de Relatórios para clientes Enterprise – Custo da Loja Marketplace).

**Folha de Preços –**  a [API da Folha de Preços](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) fornece a tarifa aplicável de cada medidor para uma inscrição e o período de faturação. Para obter mais informações, veja [Reporting APIs for Enterprise customers – Price Sheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) (APIs de Relatórios para clientes Enterprise – Folha de Preços).

**Períodos de Faturação –**  a [API de Períodos de Faturação](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) apresenta, por ordem cronológica inversa, uma lista dos períodos de faturação que têm dados de consumo para uma inscrição. Cada período contém uma propriedade que aponta para a rota da API para os quatro conjuntos de dados: BalanceSummary, UsageDetails, Marketplace Charges e PriceSheet. Para obter mais informações, veja [Reporting APIs for Enterprise customers – Billing Periods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) (APIs de Relatórios para clientes Enterprise – Períodos de Faturação).

### <a name="enable-api-data-access"></a>Ativar o acesso aos dados da API

Os proprietários da função podem executar os seguintes passos no Azure EA Portal. Navegue até **Relatórios** > **Transferir Utilização** > **Chave de Acesso da API**. Em seguida, podem:

- Gerar as chaves de acesso primárias e secundárias.
- Desativar as chaves de acesso.
- Ver as datas de início e de fim das chaves de acesso.

### <a name="generate-or-retrieve-the-api-key"></a>Gerar ou obter a Chave de API

1. Inicie sessão como administrador do Enterprise.
2. Clique em **Relatórios** na janela de navegação esquerda e, em seguida, clique no separador **Transferir Utilização**.
3. Clique em **Chave de Acesso da API**.
4. Em **Chaves de Acesso da Inscrição**, selecione o símbolo de geração de chave para gerar uma chave primária ou secundária.
5. Selecione **Expandir Chave** para ver toda a chave de acesso da API gerada.
6. Selecione **Copiar** para obter a chave de acesso da API para utilização imediata.

![Exemplo que mostra a página Chave de Acesso da API](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Se quiser fornecer as chaves de acesso da API a pessoas que não são administradores do Enterprise na sua inscrição, execute os passos seguintes:

1. Na janela de navegação esquerda, clique em **Gerir**.
2. Clique no símbolo do lápis junto a **Os Administradores de Departamento podem ver os custos**.
3. Clique em **Ativar** e, em seguida, em **Guardar**.
4. Clique no símbolo do lápis junto a **Os Proprietários de Contas podem ver os custos**.
5. Clique em **Ativar** e, em seguida, em **Guardar**.

![Exemplo que mostra as opções “Os Administradores de Departamento podem ver os custos” e “Os Proprietários de Contas podem ver os custos” ativadas](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) Os passos anteriores concedem aos titulares das chaves de acesso da API acesso às informações dos custos e preços nos relatórios de utilização.

### <a name="pass-keys-in-the-api"></a>Transmitir as chaves na API

Transmita a chave de API para cada chamada para autenticação e autorização. Transmita a seguinte propriedade aos cabeçalhos HTTP:

| Chave do cabeçalho do pedido | Valor |
| --- | --- |
| Autorização | Especifique o valor neste formato: **token de portador {API\_KEY}**
Exemplo: token de portador \&lt; ApiKey\&gt; |

### <a name="swagger"></a>Swagger

Está disponível um ponto final do Swagger em [Enterprise Reporting v3 APIs](https://consumption.azure.com/swagger/ui/index) ( APIs de Relatórios Enterprise v3) para as seguintes APIs. O Swagger ajuda a inspecionar a API. Utilize o Swagger para gerar SDKs de cliente com o [AutoRest](https://github.com/Azure/AutoRest) ou o [Swagger CodeGen](https://swagger.io/swagger-codegen/). Os dados disponíveis após o dia 1 de maio de 2014 estão acessíveis através da API.

### <a name="api-response-codes"></a>Códigos de resposta da API

Quando utiliza uma API, são mostrados os códigos de estado da resposta. A tabela seguinte descreve-os.

| Código de estado da resposta | Mensagem | Descrição |
| --- | --- | --- |
| 200 | OK | Sem erros |
| 401 | Não autorizado | Chave de API não encontrada, Inválida, Expirada, etc. |
| 404 | Indisponível | Ponto final do relatório não encontrado |
| 400 | Pedido Incorreto | Parâmetros inválidos – Intervalos de datas, números de EA, etc. |
| 500 | Erro do Servidor | Erro inesperado ao processar o pedido |

### <a name="usage-and-billing-data-update-frequency"></a>Frequência de atualização dos dados de utilização e de faturação

Os ficheiros dos dados de utilização e de faturação são atualizados a cada 24 horas para o mês de faturação a decorrer. No entanto, pode ocorrer uma latência de dados de cerca de três dias. Por exemplo, se a utilização tiver ocorrido na segunda-feira, os dados poderão não ser apresentados no ficheiro de dados até quinta-feira.

### <a name="test-enrollment-for-development"></a>Inscrição de teste para desenvolvimento

Se for um parceiro ou um programador sem uma inscrição Azure Enterprise e quiser aceder à API, poderá utilizar a inscrição de teste. O nome da inscrição é _EnrollmentNumber 100_. Pode procurar e testar as informações de utilização até junho de 2018. Em seguida, pode utilizar a chave a seguir para chamar a API e ver os dados de exemplo.

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImpoeXA2UU9DWlZmY1pmdmhDVGR1OFdxeTJ5byJ9.eyJFbnJvbGxtZW50TnVtYmVyIjoiMTAwIiwiSWQiOiI1ZTc2ZmNiMy0xN2I4LTQ5ZDItYjdkOC0zMDU0YjUwOWY0MWYiLCJSZXBvcnRWaWV3IjoiU3lzdGVtIiwiUGFydG5lcklkIjoiIiwiRGVwYXJ0bWVudElkIjoiIiwiQWNjb3VudElkIjoiIiwiaXNzIjoiZWEubWljcm9zb2Z0YXp1cmUuY29tIiwiYXVkIjoiY2xpZW50LmVhLm1pY3Jvc29mdGF6dXJlLmNvbSIsImV4cCI6MTU4NjM5MDA2OSwibmJmIjoxNTcwNTc4ODY5fQ.lENR5pCBph6iZCVexUlN1b-j7StaILCyBewVHoILD-_fn8S2o2bHY1qUseGOkBwNlaFQfk2OZIo-jQYvnf3eP3UNrNVTCINT0APbc1RqgwSjZSxugVVHH9jnSzEjONkJaSKmi4tlidk6zkF1-uY-TPJkKxYN_9ar7BgLshF9JGXk7t8OZhxSCxDZc-smntu6ORFDl4gRZZVBKXhqOGjOAdYX5tPiGDF2Bxb68RSzh9Xyr5PXxKLx5yivZzUdo0-GFHo13V9w6a5VQM4R1w4_ro8jF8WAo3mpGZ_ovx_U5IY6zMNmi_AoA1mUyvTGotgcu94RragutoJRxAGHbNJZ0Q
```

### <a name="azure-service-catalog"></a>Catálogo de serviços do Azure

Todos os serviços do Azure estão publicados num catálogo em formato CSV num blogue de armazenamento do Azure. O catálogo é útil caso precise de compilar um catálogo organizado de todos os serviços do Azure para o seu sistema. O catálogo atual está disponível em [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv).

### <a name="csv-data-file-details"></a>Detalhes do ficheiro de dados CSV

As informações a seguir descrevem as propriedades dos relatórios da API.

#### <a name="usage-summary"></a>Resumo da utilização

O formato JSON é gerado a partir do relatório CSV. Como resultado, o formato é igual ao formato CSV do resumo. O nome da coluna é mantido, pelo que deve anular a serialização numa tabela de dados ao consumir os dados de resumo JSON.

| Nome da coluna CSV | Nome da coluna JSON | Nova coluna JSON | Comentário |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Nome da Conta | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Nome da Subscrição | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | Mostra a data em que o relatório do catálogo de serviços foi executado. O formato é uma cadeia de carateres de data sem nenhum carimbo data/hora. |
| Mês | Mês | Mês |   |
| Dia | Dia | Dia |   |
| Ano | Ano | Ano |   |
| Produto | BillableItemName | Produto |   |
| ID do Medidor | ResourceGUID | MeterId |   |
| Categoria do Medidor | Serviço | MeterCategory | Útil para ajudar a localizar ficheiros. Relevante para serviços que tenham vários ServiceType. Por exemplo, Máquinas Virtuais. |
| Subcategoria do Medidor | ServiceType | MeterSubCategory | Fornece um segundo nível de detalhes para um serviço. Por exemplo, VM A1 (não Windows).  |
| Região do Medidor | ServiceRegion | MeterRegion | O terceiro nível de detalhe necessário para um serviço. Útil para localizar o contexto da região do ResourceGUID. |
| Nome do Medidor | ServiceResource | MeterName | O nome do serviço. |
| Quantidade Consumida | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Localização do Recurso | ServiceSubRegion | ResourceLocation |   |
| Serviço Consumido | ServiceInfo | ConsumedService |   |
| ID da Instância | Componente | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Etiquetas | Etiquetas | Etiquetas |   |
| Identificador do Serviço de Arquivo   | OrderNumber | StoreServiceIdentifier   |   |
| Nome do Departamento | DepartmentName | DepartmentName |   |
| Centro de Custos | CostCenter | CostCenter |   |
| Unidade de Medida | UnitOfMeasure | UnitOfMeasure | Valores de exemplo: Horas, GB, Eventos, Pushes, Unidade, Horas de Unidade, MB, Unidades Diárias |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Relatório do Azure Marketplace

| Nome da coluna CSV | Nome da coluna JSON | Nova coluna JSON |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Nome da Conta | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Nome da Subscrição | SubscriptionName |  SubscriptionName |
| Date | BillingCycle |  Data (apenas Cadeia de Carateres de Data. Sem carimbo de data/hora)
| Mês | Mês |  Mês |
| Dia | Dia |  Dia |
| Ano | Ano |  Ano |
| ID do Medidor | MeterResourceId |  MeterId |
| Nome do Editor | PublisherFriendlyName |  Nome do Editor |
| Nome da Oferta | OfferFriendlyName |  OfferName |
| Nome do Plano | PlanFriendlyName |  PlanName |
| Quantidade Consumida | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Unidade de Medida | UnitOfMeasure | UnitOfMeasure |
| ID da Instância | InstanceId | InstanceId |
| Informações Adicionais | AdditionalInfo | AdditionalInfo |
| Etiquetas | Etiquetas | Etiquetas |
| Número da Encomenda | OrderNumber | OrderNumber |
| Nome do Departamento | DepartmentNames | DepartmentName |
| Centro de Custos | CostCenters |  CostCenter |
| Grupo de Recursos | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Folha de preços

| Nome da coluna CSV | Nome da coluna JSON | Comentário |
| --- | --- | --- |
| Serviço | Serviço |  Sem alteração no preço |
| Unidade de Medida | UnitOfMeasure |   |
| Número de Referência de Utilização Excedida | ConsumptionPartNumber |   |
| Preço de Unidade de Utilização Excedida | ConsumptionPrice |   |
| Código de Moeda | CurrencyCode |     |

### <a name="common-api-issues"></a>Problemas comuns das APIs

À medida que utiliza as APIs REST do Azure Enterprise, poderá deparar-se com alguns dos problemas comuns descritos a seguir.

Pode estar a tentar utilizar uma chave de API que não tem o tipo de autorização correto. As chaves de API são geradas pelo:

- Administrador do Enterprise (EA)
- Administrador de Departamento (AD)
- Proprietário da Conta (PC)

Uma chave gerada pelo administrador EA concede acesso a todas as informações para essa inscrição. Um administrador EA só de leitura não pode gerar uma chave de API.

Uma chave gerada por um AD ou PC não fornece acesso às informações dos saldos, custos e folhas de preços.

As chaves de API expiram ao fim de seis meses. Se um chave expirar, terá de a regenerar.

Se tiver recebido um erro de tempo limite excedido, poderá resolvê-lo ao aumentar o limiar de tempo limite.

Pode receber um erro 401 (não autorizado), que é um erro de expiração. O erro normalmente é causado por uma chave expirada. Se a chave tiver expirado, poderá regenerá-la.

Pode receber os erros 400 e 404 (indisponível) enviados por uma chamada à API quando não existem dados atuais disponíveis para o intervalo de datas selecionado. Por exemplo, este erro pode ocorrer porque foi iniciada uma transferência da inscrição recentemente. Os dados de uma data específica e posterior residem agora numa inscrição nova. Caso contrário, o erro pode ocorrer se estiver a utilizar um número de inscrição novo para obter informações que residem numa inscrição antiga.

## <a name="next-steps"></a>Passos seguintes

- Os administradores do Azure EA Portal devem ler o artigo [Azure EA portal administration](ea-portal-administration.md) (Administração do Azure EA Portal) para saber mais sobre as tarefas administrativas comuns.
- Se precisar de ajuda para resolver problemas com o Azure EA Portal, veja [Troubleshoot Azure EA portal access](ea-portal-troubleshoot.md) (Resolver problemas de acesso do Azure EA Portal).