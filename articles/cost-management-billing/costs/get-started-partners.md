---
title: Começar a utilizar o Azure Cost Management para parceiros
description: Este artigo explica como os parceiros utilizam as funcionalidades do Azure Cost Management e como ativam o acesso ao Cost Management para os clientes.
author: bandersmsft
ms.author: banders
ms.date: 01/27/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: 40c683d98a13bf4810ec4575af3fba5ae64961f0
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946982"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Começar a utilizar o Azure Cost Management para parceiros

O Azure Cost Management está disponível de forma nativa para os parceiros diretos que tenham integrado os clientes num Contrato de Cliente Microsoft e tenham [comprado um Plano do Azure](/partner-center/purchase-azure-plan). Este artigo explica como os parceiros utilizam as funcionalidades do [Azure Cost Management](../index.yml) para ver os custos das subscrições no Plano do Azure. Também descreve como os parceiros ativam o acesso ao Cost Management a preços de retalho para os clientes.

No caso dos parceiros diretos e fornecedores indiretos, o administrador global e os agentes de administração podem aceder ao Cost Management no inquilino do parceiro e gerir os custos aos preços faturados.

Os revendedores e clientes podem aceder à Cost Management no cliente inquilino e ver os custos de consumo das subscrições, onde os custos são calculados e mostrados a preços de venda a retalho. No entanto, têm de ter acesso RBAC do Azure à subscrição no inquilino do cliente para verem os custos. A política de visibilidade dos custos tem de ser ativada pelo fornecedor para o inquilino do cliente.

Os clientes podem utilizar as funcionalidades do Cost Management quando estas forem ativadas pelo parceiro CSP.

Os parceiros CSP utilizam o Cost Management para:

- Compreender os custos faturados e associar os custos ao cliente, às subscrições, aos grupos de recursos e aos serviços.
- Obter uma vista intuitiva dos custos do Azure na [análise de custos](quick-acm-cost-analysis.md) com capacidades para analisar os custos por cliente, subscrição, grupo de recursos, recurso, medidor, serviço e muitas outras dimensões.
- Ver os custos dos recursos com o Crédito Ganho pelo Parceiro (PEC) aplicado na Análise de Custos.
- Configurar notificações e a automatização com [orçamentos ](tutorial-acm-create-budgets.md) programáticos e alertas quando os custos ultrapassam os orçamentos.
- Ativar a política do Azure Resource Manager que fornece aos clientes acesso aos dados do Cost Management. Em seguida, os clientes poderão ver os dados dos custos de consumo das subscrições com as [tarifas pay as you go](https://azure.microsoft.com/pricing/calculator/).
- Exportar os dados dos custos e da utilização para uma blob de armazenamento com uma subscrição pay as you go.

Segue-se um exemplo que mostra os custos para todos os clientes.

[![Exemplo que mostra os custos para todos os clientes](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Segue-se um exemplo que mostra os custos para um único cliente.

[![Exemplo que mostra os custos para um único cliente](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Todas as funcionalidades disponíveis no Azure Cost Management também estão disponíveis com as APIs REST. Utilize as APIs para automatizar as tarefas de gestão dos custos.

## <a name="prerequisites"></a>Pré-requisitos

Como parceiro, o Azure Cost Management está disponível de forma nativa apenas para subscrições incluídas no plano do Azure.

Para ativar o Azure Cost Management no portal do Azure, deve confirmar a aceitação do cliente do Contrato de Cliente Microsoft (em nome do cliente) e migrar o cliente para o Plano do Azure. Apenas os custos das subscrições migradas para o plano do Azure estão disponíveis no Azure Cost Management.

O Azure Cost Management requer acesso de leitura à conta de faturação ou à subscrição.

Para obter mais informações sobre a ativação e a atribuição de acesso ao Azure Cost Management para uma conta de faturação, veja [Assign users roles and permissions](/partner-center/permissions-overview) (Atribuir funções e permissões de utilizadores). As funções **Administrador global** e **Agente de administração** podem gerir os custos de uma conta de faturação.

Para aceder ao Azure Cost Management no âmbito da subscrição, qualquer utilizador com acesso RBAC do Azure a uma subscrição pode ver os custos nas tarifas de retalho (pay as you go). No entanto, a [política de visibilidade dos custos do inquilino do cliente](#enable-the-policy-to-view-azure-usage-charges) tem de estar ativada. Para ver uma lista completa dos tipos de contas suportados, veja [Compreender os dados do Cost Management](understand-cost-mgt-data.md).

Ao transferir os contratos de faturação existentes para um novo parceiro, as capacidades de gestão de custos só estão disponíveis para a atual relação de faturação com o parceiro. Os custos históricos antes da transferência para o novo parceiro não passam para a nova conta de faturação. No entanto, o histórico de custos mantém-se com a conta de faturação associada original.

## <a name="how-cost-management-uses-scopes"></a>Como o Cost Management utiliza os âmbitos

Os âmbitos são onde gere os dados de faturação, tem funções específicas de pagamentos, visualiza faturas e onde realiza a gestão global da conta. As funções de faturação e de conta são geridas separadamente dos âmbitos utilizados na gestão de recursos, que utilizam o RBAC do Azure. Para fazermos a distinção clara do objetivo dos diferentes âmbitos, incluindo as diferenças de controlo de acesso, serão designados como âmbitos de faturação e âmbitos RBAC do Azure, respetivamente.

Para compreender os âmbitos de faturação e os âmbitos RBAC do Azure, e como funciona a gestão de custos com âmbitos, veja [Compreender e trabalhar com âmbitos](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gerir os custos com âmbitos de faturação do inquilino do parceiro

Depois de integrar os seus clientes num Contrato de Cliente Microsoft, estarão disponíveis os seguintes _âmbitos de faturação_ no inquilino. Utilize os âmbitos para gerir os custos no Cost Management.

### <a name="billing-account-scope"></a>Âmbito da conta de faturação

Utilize o âmbito da conta de faturação para ver os custos antes de impostos em todos os seus clientes e perfis de faturação. Os custos faturados só são apresentados para os produtos baseados no consumo do cliente no Contrato de Cliente Microsoft. No entanto, os custos faturados são apresentados para os produtos baseados na compra para clientes no Contrato de Cliente Microsoft e na oferta CSP. Atualmente, a moeda predefinida para ver os custos no âmbito é o dólar americano. Os orçamentos definidos para o âmbito também estão em USD.

Independentemente das diferentes moedas faturadas, os parceiros utilizam o âmbito da Conta de faturação para definir orçamentos e gerir custos em USD para os respetivos clientes, subscrições, recursos e grupos de recursos.

Os parceiros também filtram os custos com base numa moeda de faturação específica para os clientes na vista de análise de custos. Selecione a lista **Custos reais** para ver os custos nas moedas de faturação suportadas.

![Exemplo a mostrar a seleção de Custos reais para as moedas](./media/get-started-partners/actual-cost-selector.png)

Utilize a [vista de custos amortizados](quick-acm-cost-analysis.md#customize-cost-views) nos âmbitos de faturação para ver os custos amortizados das instâncias reservadas dentro do termo de reserva.

### <a name="billing-profile-scope"></a>Âmbito do perfil de faturação

Utilize o âmbito do perfil de faturação para ver os custos antes de impostos na moeda de faturação em todos os seus clientes para todos os produtos e subscrições incluídos numa fatura. Pode filtrar os custos num perfil de faturação para uma fatura específica através do filtro **InvoiceID**. O filtro mostra os custos de consumo e de compra de produtos para uma fatura específica. Também pode filtrar os custos de um cliente específico na fatura para ver os custos antes de impostos.

Depois de integrar os clientes num Contrato de Cliente Microsoft, receberá uma fatura com todos os custos de todos os produtos (consumo, compras e direitos) para estes clientes no Contrato de Cliente Microsoft. Quando faturadas na mesma moeda, estas faturas também incluem os custos relativos aos produtos de elegibilidade e comprados, tais como SaaS, Azure Marketplace e reservas para os clientes que ainda estejam na oferta CSP clássica no plano do Azure.

Para ajudar a reconciliar os custos com a fatura do cliente, o âmbito do perfil de faturação permite-lhe ver todos os custos acumulados numa fatura dos seus clientes. Tal como a fatura, o âmbito mostra os custos de cada cliente no novo Contrato de Cliente Microsoft. O âmbito também mostra todos os custos dos produtos de direito do cliente ainda na oferta CSP atual.

Os âmbitos do perfil de faturação e da conta de faturação são os únicos âmbitos aplicáveis que apresentam os custos dos produtos baseados no direito e na compra, como o Azure Marketplace e as compras de reservas.

Os perfis de faturação definem as subscrições que estão incluídas numa fatura. Os perfis de faturação são o equivalente funcional de uma inscrição no contrato Enterprise. Um perfil de faturação é o âmbito onde as faturas são geradas.

Atualmente, a moeda de faturação é a moeda predefinida quando vê os custos no âmbito do perfil de faturação. Os orçamentos definidos no âmbito do perfil de faturação estão na moeda de faturação.

Os parceiros podem utilizar o âmbito para fazer a reconciliação com as faturas. Além disso, utilizam o âmbito para definir orçamentos na moeda de faturação para os seguintes itens:

- Fatura filtrada específica
- Cliente
- Subscrição
- Grupo de recursos
- Recurso
- Serviço do Azure
- Medidor
- ResellerMPNID

### <a name="customer-scope"></a>Âmbito do cliente

Os parceiros utilizam o âmbito para gerir os custos associados aos clientes integrados no Contrato de Cliente Microsoft. O âmbito permite aos parceiros ver os custos antes de impostos para um cliente específico numa moeda de faturação. Também pode filtrar os custos antes de impostos para uma subscrição, grupo de recursos ou recurso específicos.

O âmbito do cliente não inclui clientes que estejam na oferta CSP atual. O âmbito inclui apenas clientes que tenham um Contrato de Cliente Microsoft. Os custos de direito, não os da utilização do Azure, para os clientes da oferta CSP atual estão disponíveis nos âmbitos da conta de faturação e do perfil de faturação quando aplica o filtro do cliente. Os orçamentos definidos neste âmbito estão na moeda de faturação.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Acesso dos parceiros aos âmbitos de faturação no Cost Management

Apenas os utilizadores com as funções **Administrador global** e **Agente de administração** podem gerir e ver os custos para contas de faturação, perfis de faturação e clientes diretamente no inquilino do Azure do parceiro. Para obter mais informações sobre as funções do centro de parceiros, veja [Assign users roles and permissions](/partner-center/permissions-overview) (Atribuir funções e permissões de utilizadores).

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Ativar o Cost Management para subscrições de inquilinos de clientes

Os parceiros podem permitir o acesso ao Cost Management depois de os clientes estarem integrados num Contrato de Cliente Microsoft. Em seguida, podem ativar uma política que permita aos clientes ver os custos dos serviços do Azure consumidos calculados de acordo com as tarifas de retalho pay as you go. Os custos são apresentados na moeda de faturação do cliente relativos ao consumo nos âmbitos da subscrição RBAC do Azure e dos grupos de recursos.

Quando a política de visibilidade dos custos for ativada pelo parceiro, qualquer utilizador com acesso do Azure Resource Manager à subscrição pode gerir e analisar os custos nas tarifas pay as you go. Efetivamente, os revendedores e os clientes com acesso RBAC do Azure adequado às subscrições do Azure podem ver os custos.

Independentemente da política, os administradores globais e os agentes administradores do fornecedor podem ver os custos da subscrição caso tenham acesso à subscrição e ao grupo de recursos.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Ativar a política para ver os custos de utilização do Azure

Tem de ser membro do grupo **agente de administração** para ver e atualizar a política. Utilize as seguintes informações para ativar a política que permite aos clientes ver os custos de utilização do Azure.

No portal do Azure, inicie sessão no *inquilino do parceiro* e selecione **Cost Management + Faturação**. Selecione o âmbito de faturação relevante na área Âmbito de Faturação e, em seguida, selecione **Clientes**. A lista de clientes está associada à conta de faturação. *Se iniciar sessão por engano no inquilino do cliente, não verá a lista **Clientes**.*

Na lista de clientes, selecione o cliente que quer permitir que veja os custos.

[![Selecionar clientes no Cost Management](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

Em **Definições**, selecione **Políticas**.

A política de visibilidade dos custos atual é apresentada para os custos de **Utilização do Azure** associados às subscrições do cliente selecionado.
![Política para permitir aos clientes ver os custos pay as you go](./media/get-started-partners/cost-management-billing-policies.png)

Quando a política está definida como **Não**, o Azure Cost Management não está disponível para os utilizadores da subscrição associados ao cliente. A menos que seja ativada por um parceiro, a política de visibilidade dos custos está desativada por predefinição para todos os utilizadores da subscrição.

Quando a política de custos está definida como **Sim**, os utilizadores da subscrição associados ao inquilino do cliente podem ver os custos de utilização nas tarifas pay as you go.

Quando a política de visibilidade dos custos está ativada, todos os serviços com utilização de subscrições mostram os custos nas tarifas pay as you go. A utilização de reservas aparece com zero custos para custos reais e amortizados. As compras e os direitos não estão associados a uma subscrição específica. Assim, as compras não são apresentadas no âmbito da subscrição. O administrador global/agente administrador de um parceiro direto ou de um fornecedor indireto também pode utilizar a [Update Customer API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) para definir a política de visibilidade de custos de cada cliente em escala.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Ver os custos da subscrição no inquilino do cliente

Para ver os custos de uma subscrição, abra **Cost Management + Faturação** no inquilino do Azure do cliente. Selecione **Análise de custos** na subscrição relevante para começar a rever os custos. Pode ver os custos de consumo de cada subscrição individualmente no inquilino do cliente.

[![Ver a análise de custos como cliente ](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

A análise de custos, os orçamentos e os alertas estão disponíveis para os âmbitos RBAC do Azure da subscrição e do grupo de subscrição nas tarifas pay as you go.

As vistas amortizadas e os custos reais das instâncias reservadas nos âmbitos RBAC do Azure mostram zero custos. Os custos de compra de direitos, como instâncias reservadas e taxas do Marketplace, são mostrados apenas nos âmbitos da faturação no inquilino do parceiro onde as compras foram realizadas.

As taxas de retalho utilizadas para calcular os custos mostrados na vista são os mesmos preços apresentados na Calculadora de Preços do Azure para todos os clientes. Os custos apresentados não incluem descontos nem créditos que o parceiro possa ter, como Créditos Ganhos pelo Parceiro, Descontos de Escalão e descontos de Serviço Global.

 

## <a name="analyze-costs-in-cost-analysis"></a>Analisar os custos na análise de custos

Os parceiros com acesso aos âmbitos de faturação no inquilino do parceiro podem explorar e analisar os custos faturados na análise de custos para um cliente específico ou para uma fatura. Na vista [análise de custos](quick-acm-cost-analysis.md), também pode [guardar vistas](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) e exportar dados para [ficheiros CSV e PNG](quick-acm-cost-analysis.md#download-usage-data).

Os utilizadores RBAC do Azure com acesso à subscrição no inquilino do cliente também podem analisar os custos de retalho das subscrições no inquilino do cliente, guardar vistas e exportar dados para ficheiros CSV e PNG.

Pode utilizar filtros e grupos por funcionalidades na análise de custos para analisar os custos em vários campos. Os campos específicos do parceiro são mostrados na secção seguinte.

## <a name="data-fields"></a>Campos de dados

Os seguintes campos de dados estão disponíveis nos ficheiros de detalhes de utilização e nas APIs do Cost Management. Sempre que estiverem disponíveis, são mostradas as informações equivalentes no Centro de Parceiros. Para seguintes os campos a negrito, os parceiros podem utilizar as funcionalidades para filtrar e agrupar da análise de custos para analisar os custos em vários campos. Os campos a negrito são apenas aplicáveis aos Contratos de Cliente Microsoft suportados pelos parceiros.

| **Nome do campo** | **Descrição** | **Equivalente no Centro de Parceiros** |
| --- | --- | --- |
| invoiceId | ID da fatura mostrado na fatura para a transação específica. | Número da fatura onde a transação é mostrada. |
| previousInvoiceID | Referência a uma fatura original em caso de reembolso (custo negativo). Povoado apenas quando existe um reembolso. | N/D |
| billingAccountName | Nome da conta de faturação que representa o parceiro. Acumula todos os custos dos clientes que integraram um Contrato de Cliente Microsoft e os clientes CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | N/D |
| billingAccountID | Identificador da conta de faturação que representa o parceiro. | ID Raiz do Comércio de Parceiros MCAPI. Utilizado num pedido, mas não incluído numa resposta.|
| billingProfileID | Identificador do perfil de faturação que agrupa os custos nas faturas numa única moeda de faturação para os clientes que integraram um Contrato de Cliente Microsoft e para os clientes CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | ID do Grupo de Faturação do Parceiro MCAPI. Utilizado num pedido, mas não incluído numa resposta. |
| billingProfileName | Nome do perfil de faturação que agrupa os custos nas faturas numa única moeda de faturação nos clientes que integraram um Contrato de Cliente Microsoft e os clientes CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | N/D |
| invoiceSectionName | Nome do projeto que está a ser cobrado na fatura. Não aplicável aos Contratos de Cliente Microsoft integrados por parceiros. | N/D |
| invoiceSectionID | Identificador do projeto que está a ser cobrado na fatura. Não aplicável aos Contratos de Cliente Microsoft integrados por parceiros. | N/D |
| **CustomerTenantID** | Identificador do inquilino do Azure Active Directory da subscrição do cliente. | ID organizacional do cliente: o TenantID do Azure Ative Directory do cliente. |
| **CustomerName** | Nome do inquilino do Azure Active Directory da subscrição do cliente. | Nome da organização do cliente, conforme mostrado no Centro de Parceiros. Importante para reconciliar a fatura com as informações do sistema. |
| **CustomerTenantDomainName** | Nome de domínio do inquilino do Azure Active Directory da subscrição do cliente. | Domínio do inquilino do Azure Active Directory do cliente. |
| **PartnerTenantID** | Identificador do inquilino do Azure Ative Directory do parceiro. | ID do Inquilino do Azure Active Directory do Parceiro denominado ID de Parceiro, no formato GUID. |
| **PartnerName** | Nome do inquilino do Azure Ative Directory do parceiro. | Nome do parceiro. |
| **ResellerMPNID** | MPNID do revendedor associado à subscrição. | ID do MPN do revendedor no registo da subscrição. Não disponível para a atividade atual. |
| costCenter | Centro de custos associado à subscrição. | N/D |
| billingPeriodStartDate | Data de início do período de faturação, conforme mostrado na fatura. | N/D |
| billingPeriodEndDate | Data de fim do período de faturação, conforme mostrado na fatura. | N/D |
| servicePeriodStartDate | Data de início do período de classificação quando a utilização do serviço foi classificada para os custos. Os preços dos serviços do Azure são determinados para o período de classificação. | ChargeStartDate no Centro de Parceiros.  Data de início do ciclo de faturação, exceto quando apresentar datas de dados de utilização latente anteriormente não cobrados de um ciclo de faturação anterior. A hora é sempre o início do dia, 00:00. |
| servicePeriodEndDate | Data de fim do período quando a utilização do serviço foi classificada para os custos. Os preços dos serviços do Azure são determinados com base no período de classificação. | N/D |
| date | Para dados de consumo do Azure, mostra a data de utilização conforme foi classificada. Para uma instância reservada, mostra a data de compra. Para custos recorrentes e únicos, como o Marketplace e o suporte, mostra a data de compra. | N/D |
| productID | Identificador do produto que acumulou custos por consumo ou compra. Trata-se da chave concatenada de productID e SKuID, conforme mostrado no Centro de Parceiros. | ID do produto. |
| produto | Nome do produto que acumulou custos por consumo ou compra, conforme mostrado na fatura. | O nome do produto no catálogo. |
| serviceFamily | Mostra a família de serviços do produto comprado ou cobrado. Por exemplo, Armazenamento ou Computação. | N/D |
| productOrderID | O identificador do recurso ou nome do plano do Azure a que a subscrição pertence. Por exemplo, Plano do Azure. | CommerceSubscriptionID |
| productOrderName | O nome do plano do Azure a que a subscrição pertence. Por exemplo, Plano do Azure. | N/D|
| consumedService | Serviço consumido (taxonomia legada) conforme utilizado nos detalhes de utilização do EA legados. | Serviço mostrado no Centro de Parceiros. Por exemplo, Microsoft.Storage, Microsoft.Compute e microsoft.operationalinsights. |
| meterId | Identificador medido para o consumo medido. | O ID do medidor utilizado. |
| meterName | Identifica o nome do medidor para o consumo medido. | O nome do medidor consumido. |
| meterCategory | Identifica o serviço de nível superior para a utilização. | O serviço de nível superior para a utilização. |
| meterSubCategory | Define o tipo ou subcategoria de serviço do Azure que pode afetar a tarifa. | O tipo de serviço do Azure que pode afetar a tarifa.|
| meterRegion | Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter. | A localização regional de um datacenter para os serviços, quando aplicável e povoada. |
| ID da subscrição | Identificador exclusivo gerado pela Microsoft para a subscrição do Azure. | EntitlementID |
| subscriptionName | Nome da subscrição do Azure. | N/D |
| Termo | Apresenta o termo da validade da oferta. Por exemplo, as instâncias reservadas mostram 12 meses de um período anual da instância reservada. Para compras únicas ou recorrentes, este período apresenta um mês para SaaS, Azure Marketplace e suporte. Não aplicável ao consumo do Azure. | N/D |
| publisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo de editor que identifica o editor como proprietário, revendedor de terceiros ou agência de terceiros. | N/D |
| partNumber | Número de peça da instância reservada não utilizada e dos serviços do Azure Marketplace. | N/D |
| publisherName | Nome do publicador do serviço, incluindo a Microsoft ou editores de terceiros. | O nome do editor do produto.|
| reservationId | Identificador da compra da instância reservada. | N/D |
| reservationName | Nome da instância reservada. | N/D |
| reservationOrderId | OrderID da instância reservada. | N/D |
| frequência | Frequência de pagamento de uma instância reservada. | N/D |
| resourceGroup | Nome do grupo de recursos do Azure utilizado na gestão de recursos do ciclo de vida. | Nome do grupo de recursos. |
| instanceID (ou) ResourceID | Identificador da instância de recurso. | Mostrado como um ResourceURI que inclui propriedades de recursos completas. |
| resourceLocation | Nome da localização do recurso. | A localização do recurso. |
| Localização | Localização normalizada do recurso. | N/D |
| effectivePrice | O preço unitário efetivo do serviço, na moeda do preço. Exclusivo para um produto, família de serviços, medidor e oferta. Utilizado nos preços da folha de preços da conta de faturação. Quando existem preços em escalões ou uma quantidade incluída, mostra o preço do consumo combinado. | O preço unitário após os ajustes. |
| Quantidade | Quantidade medida comprada ou consumida. O valor do medidor utilizado durante o período de faturação. | Número de unidades. Verifique se corresponde às informações no sistema de faturação durante a reconciliação. |
| unitOfMeasure | Identifica a unidade em que o serviço é cobrado. Por exemplo, GB e horas. | Identifica a unidade em que o serviço é cobrado. Por exemplo, GB, horas e 10 000 s. |
| pricingCurrency | A moeda que define o preço unitário. | A moeda na lista de preços.|
| billingCurrency | A moeda que define o custo faturado. | A moeda definida como a moeda faturada na fatura. |
| chargeType | Define o tipo de custo representado no Azure Cost Management, como compra e reembolso. | O tipo de custo ou ajuste. Não disponível para a atividade atual. |
| costinBillingCurrency | ExtendedCost ou custo combinado antes de impostos na moeda faturada. | N/D |
| costinPricingCurrency | ExtendedCost ou custo combinado antes de impostos na moeda de preço para correlacionar com os preços. | N/D |
| **costinUSD** | ExtendedCost estimado ou custo combinado antes de impostos em USD. | N/D |
| **paygCostInBillingCurrency** | Mostrará os custos se os preços forem de retalho. Mostra preços pay as you go na moeda de faturação. Disponível apenas nos âmbitos RBAC do Azure. | N/D |
| **paygCostInUSD** | Mostrará os custos se os preços forem de retalho. Mostra os preços pay as you go em USD. Disponível apenas nos âmbitos RBAC do Azure. | N/D |
| exchangeRate | Taxa de câmbio utilizada para converter a moeda do preço na moeda de faturação. | Referida como PCToBCExchangeRate no Centro de Parceiros. A moeda do preço à taxa de câmbio da moeda de faturação.|
| exchangeRateDate | A data da taxa de câmbio utilizada para converter a moeda do preço na moeda de faturação. | Referida como PCToBCExchangeRateDat no Centro de Parceiros. A moeda do preço à data da taxa de câmbio da moeda de faturação.|
| isAzureCreditEligible | Indica se o custo é elegível para pagamento por créditos do Azure. | N/D |
| serviceInfo1 | Campo legado que captura metadados opcionais específicos do serviço. | Metadados internos do serviço do Azure. |
| serviceInfo2 | Campo legado que captura metadados opcionais específicos do serviço. | Informações do serviço. Por exemplo, um tipo de imagem de uma máquina virtual e o nome do ISP para o ExpressRoute.|
| additionalInfo | Metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual. | Qualquer informação adicional não abrangida noutras colunas. Os metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual.|
| etiquetas | Etiqueta que atribui ao medidor. Utilize etiquetas para agrupar os registos de faturação. Por exemplo, pode utilizar etiquetas para distribuir os custos pelo departamento que utiliza o medidor. | Etiquetas adicionadas pelo cliente.|
| **partnerEarnedCreditRate** | Taxa de desconto aplicada se houver um crédito ganho pelo parceiro (PEC) com base no acesso da ligação de administração do parceiro. | A taxa de crédito ganho pelo parceiro (PEC). Por exemplo, 0% ou 15%. |
| **partnerEarnedCreditApplied** | Indica se foi aplicado o crédito ganho pelo parceiro. | N/D |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Ver os custos de recursos do Crédito Ganho pelo Parceiro (PEC)

No Azure Cost Management, os parceiros podem utilizar a análise de custos para ver os custos que receberam os benefícios do PEC.

No portal do Azure, inicie sessão no inquilino do parceiro e selecione **Cost Management + Faturação**. Em **Cost Management**, selecione **Análise de custos**.

A vista Análise de custos mostra os custos da conta de faturação do parceiro. Selecione o **Âmbito** necessário para o parceiro, um cliente específico ou um perfil de faturação para reconciliar as faturas.

Num gráfico em anel, selecione a lista pendente e selecione **PartnerEarnedCreditApplied** para analisar os custos PEC.

![Exemplo que mostra como ver o crédito ganho pelo parceiro](./media/get-started-partners/cost-analysis-pec1.png)

Quando a propriedade **PartnerEarnedCreditApplied** for _True_, o custo associado tem o benefício do acesso administrativo ganho pelo parceiro.

Quando a propriedade **PartnerEarnedCreditApplied** for _False_, o custo associado não cumpriu a elegibilidade necessária para o crédito. Ou o serviço comprado não é elegível para o crédito ganho pelo parceiro.

Os dados de utilização do serviço demoram normalmente 8 a 24 horas a aparecer no Cost Management. Para obter mais in, veja [Atualizações e retenção de dados de custos e utilização](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention). Os créditos PEC aparecem dentro de 48 horas a partir da hora de acesso no Azure Cost Management.


Também pode agrupar e filtrar pela propriedade **PartnerEarnedCreditApplied** com as opções **Agrupar por**. Utilize as opções para examinar os custos que têm e não têm PEC.

![Agrupar ou filtrar pelo crédito ganho pelo parceiro](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Exportar os dados dos custos para o Armazenamento do Microsoft Azure

Os parceiros com acesso aos âmbitos de faturação num inquilino do parceiro podem exportar os dados dos custos e da utilização para um blob do Armazenamento do Microsoft Azure. O blob tem de estar numa subscrição no inquilino do parceiro que não seja uma [subscrição de serviço partilhada](/partner-center/shared-services) ou uma subscrição do cliente. Para permitir a exportação dos dados dos custos, recomendamos que configure uma subscrição pay as you go independente no inquilino do parceiro para alojar os dados dos custos exportados. A conta de armazenamento de exportação é criada no blob do Armazenamento do Microsoft Azure alojado na subscrição pay as you go. Com base no âmbito em que o parceiro cria a exportação, os dados associados são exportados automaticamente para a conta de armazenamento periodicamente.

Os utilizadores com acesso RBAC do Azure à subscrição também podem exportar os dados de custos para um blob do Armazenamento do Microsoft Azure alojado em qualquer subscrição do inquilino do cliente.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Criar uma exportação num inquilino do parceiro ou do cliente

No portal do Azure, inicie sessão no inquilino do parceiro ou do cliente e selecione **Cost Management + Faturação**. Selecione um âmbito adequado, por exemplo, uma conta de faturação do Contrato de Parceiro da Microsoft e, em seguida, selecione **Análise de Custos**. Quando a página for carregada, selecione **Exportar**. Selecione **Ver todas as exportações** em Agendar Exportação.

![Selecionar Exportar e Ver todas as exportações](./media/get-started-partners/export01.png)

Em seguida, selecione **Adicionar**, escreva o nome e selecione um tipo de exportação. Selecione o separador **Armazenamento** e introduza as informações necessárias.

![Adicione a nova exportação e selecione o separador Armazenamento](./media/get-started-partners/export02.png)

Quando criar uma exportação no inquilino do parceiro, selecione a subscrição pay as you go no inquilino do parceiro. Crie uma conta do Armazenamento do Microsoft Azure com essa subscrição.

Relativamente aos utilizadores RBAC do Azure no inquilino do cliente, selecione uma subscrição no inquilino do mesmo. Crie uma conta de Armazenamento do Azure com a subscrição.

Reveja o conteúdo e, em seguida, selecione **Criar** para agendar uma exportação.

Para verificar os dados na lista de exportação, selecione o nome da conta de armazenamento. Na página da conta de armazenamento, selecione **Contentores** e, em seguida, selecione o contentor. Navegue para a pasta correspondente e selecione o ficheiro CSV. Selecione **Transferir** para obter o ficheiro CSV e abri-lo. Os dados exportados assemelham-se aos dados dos custos semelhantes aos detalhes de utilização do portal do Azure.

![Exemplo de dados exportados](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>APIs REST do Cost Management

Os parceiros e clientes podem utilizar as APIs do Cost Management descritas nas seguintes secções para tarefas comuns.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>APIs do Azure Cost Management – Fornecedores diretos e indiretos

Os parceiros com acesso aos âmbitos de faturação num inquilino do parceiro podem utilizar as seguintes APIs para ver os custos faturados.

As APIs no âmbito da subscrição poderão ser chamadas por um parceiro, independentemente da política de custos, se tiverem acesso à subscrição. Outros utilizadores com acesso à subscrição, como o cliente ou o revendedor, só podem chamar as APIs depois de o parceiro ativar a política de custos do inquilino do cliente.


#### <a name="to-get-a-list-of-billing-accounts"></a>Para obter uma lista de contas de faturação

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Para obter uma lista de clientes

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Para obter uma lista de subscrições

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Para obter uma lista de faturas para um período de tempo

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

A chamada à API devolve uma matriz de faturas com elementos semelhantes ao seguinte código JSON.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Utilize o valor de campo do ID anterior devolvido e substitua-o no exemplo seguinte como âmbito para consultar os detalhes de utilização.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

O exemplo devolve os registos de utilização associados à fatura específica.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Para obter a política para os clientes verem os custos

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Para definir a política para os clientes verem os custos

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Para obter a utilização do serviço do Azure para uma conta de faturação

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Para transferir a utilização do serviço do Azure de um cliente

A chamada GET seguinte é uma operação assíncrona.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Chame o URI `Location` devolvido na resposta para verificar o estado da operação. Quando o estado for *Concluído*, a propriedade `downloadUrl` apresentará uma ligação que pode utilizar para transferir o relatório gerado.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Para obter ou transferir a folha de preços dos serviços do Azure consumidos

Primeiro, utilize a seguinte publicação.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Em seguida, chame o valor de propriedade da operação assíncrona. Por exemplo:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
A chamada GET anterior devolve a ligação de transferência com a folha de preços.


#### <a name="to-get-aggregated-costs"></a>Para obter custos agregados

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Criar um orçamento para um parceiro

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Criar um orçamento para um cliente

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Eliminar um orçamento

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Passos seguintes
- [Começar a analisar custos](quick-acm-cost-analysis.md) no Cost Management
- [Criar e gerir orçamentos](tutorial-acm-create-budgets.md) no Cost Management
