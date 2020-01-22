---
title: Introdução ao gerenciamento de custos do Azure para parceiros
description: Este artigo explica como os parceiros usam os recursos de gerenciamento de custos do Azure e como eles permitem o acesso de gerenciamento de custos para seus clientes.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
manager: aparnag
ms.custom: secdec18
ms.openlocfilehash: 10889f6d872510fb53e76ab3722343aa2ee6a5e8
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293917"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Introdução ao gerenciamento de custos do Azure para parceiros

O gerenciamento de custos do Azure está disponível nativamente para parceiros que integraram seus clientes a um contrato de cliente da Microsoft e [compraram um plano do Azure](/partner-center/purchase-azure-plan). Este artigo explica como os parceiros usam os recursos de [Gerenciamento de custos do Azure](../index.yml) para exibir os custos de assinaturas no plano do Azure. Ele também descreve como os parceiros permitem o acesso de gerenciamento de custos para seus clientes. Os clientes podem usar os recursos de gerenciamento de custos quando habilitados pelo parceiro CSP.

Os parceiros CSP usam o gerenciamento de custos para:

- Entenda os custos faturados e associe os custos ao cliente, às assinaturas, aos grupos de recursos e aos serviços.
- Obtenha uma visão intuitiva dos custos do Azure na [análise de custos](quick-acm-cost-analysis.md) com recursos para analisar os custos por cliente, assinatura, grupo de recursos, recurso, medidor, serviço e muitas outras dimensões.
- Exibir os custos de recursos que têm o PEC (crédito ganho) do parceiro aplicado na análise de custo.
- Configure notificações e automação usando [orçamentos](tutorial-acm-create-budgets.md) programáticos e alertas quando os custos excederem orçamentos.
- Habilite a política de Azure Resource Manager que fornece acesso de cliente aos dados de gerenciamento de custos. Os clientes podem exibir dados de custo de consumo para suas assinaturas usando as tarifas pré- [pagas](https://azure.microsoft.com/pricing/calculator/).
- Exporte seus dados de custo e uso para um blob de armazenamento com uma assinatura paga conforme o uso.

Aqui está um exemplo mostrando os custos para todos os clientes.
![exemplo mostrando os custos para todos os clientes](./media/get-started-partners/customer-costs1.png)

Veja um exemplo que mostra os custos de um único cliente.
![exemplo mostrando os custos de um único cliente](./media/get-started-partners/customer-costs2.png)

Todas as funcionalidades disponíveis no gerenciamento de custos do Azure também estão disponíveis com APIs REST. Use as APIs para automatizar as tarefas de gerenciamento de custos.

## <a name="prerequisites"></a>Pré-requisitos

Como parceiro, o gerenciamento de custos do Azure está disponível nativamente apenas para assinaturas que estão no plano do Azure.

Para habilitar o gerenciamento de custos do Azure no portal do Azure, você deve ter confirmado a aceitação do cliente do contrato do cliente da Microsoft (em nome do cliente) e transicionado o cliente para o plano do Azure. Somente os custos de assinaturas que estão em transição para o plano do Azure estão disponíveis no gerenciamento de custos do Azure.

O gerenciamento de custos do Azure requer acesso de leitura à sua conta de cobrança ou assinatura.

Para obter mais informações sobre como habilitar e atribuir acesso ao gerenciamento de custos do Azure para uma conta de cobrança, consulte [atribuir funções e permissões de usuários](/partner-center/permissions-overview). As funções de **administrador global** e **agente de administração** podem gerenciar os custos de uma conta de cobrança.

Para acessar o gerenciamento de custos do Azure no escopo da assinatura, qualquer usuário com acesso RBAC a uma assinatura pode exibir os custos em tarifas de varejo (pago conforme o uso). No entanto, a política de visibilidade de custo para o locatário do cliente deve ser habilitada. Para exibir uma lista completa de tipos de conta com suporte, consulte [entender os dados de gerenciamento de custos](understand-cost-mgt-data.md).


## <a name="how-cost-management-uses-scopes"></a>Como o gerenciamento de custos usa escopos

Os escopos são onde você gerencia os dados de cobrança, têm funções específicas para pagamentos, exibem faturas e realizam o gerenciamento geral da conta. As funções de cobrança e de conta são gerenciadas separadamente dos escopos usados para o gerenciamento de recursos, que usam o RBAC. Para distinguir claramente a intenção dos escopos separados, incluindo as diferenças de controle de acesso, eles são chamados de escopos de cobrança e escopos de RBAC, respectivamente.

Para entender escopos de cobrança e escopos de RBAC e como o gerenciamento de custos funciona com escopos, consulte [entender e trabalhar com escopos](understand-work-scopes.md).

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Gerenciar custos com escopos de cobrança de locatário do parceiro

Depois de integrar seus clientes a um contrato do cliente da Microsoft, os _escopos de cobrança_ a seguir estão disponíveis em seu locatário. Use os escopos para gerenciar custos no gerenciamento de custos.

### <a name="billing-account-scope"></a>Escopo da conta de cobrança

Use o escopo da conta de cobrança para exibir os custos de imposto em todos os seus clientes e perfis de cobrança. Os custos da fatura são mostrados apenas para produtos baseados em consumo do cliente no contrato do cliente da Microsoft. No entanto, os custos de fatura são mostrados para produtos baseados em compra para clientes no contrato do cliente da Microsoft e na oferta do CSP. Atualmente, a moeda padrão para exibir os custos no escopo é de US $100,00. Os orçamentos definidos para o escopo também estão em USD.

Independentemente das diferentes moedas cobradas pelo cliente, os parceiros usam o escopo da conta de cobrança para definir orçamentos e gerenciar custos em USD em seus clientes, assinaturas, recursos e grupos de recursos.

Os parceiros também filtram os custos em uma moeda de cobrança específica entre clientes na exibição de análise de custo. Selecione a lista de **custo real** para exibir os custos em moedas de cobrança de cliente com suporte.

![Exemplo mostrando a seleção de custo real para moedas](./media/get-started-partners/actual-cost-selector.png)

Use a [exibição de custo amortizada](quick-acm-cost-analysis.md#customize-cost-views) em escopos de cobrança para exibir os custos amortizados da instância reservada em um termo de reserva.

### <a name="billing-profile-scope"></a>Escopo do perfil de cobrança

Use o escopo do perfil de cobrança para exibir os custos de imposto na moeda de cobrança em todos os seus clientes para todos os produtos e assinaturas incluídos em uma fatura. Você pode filtrar os custos em um perfil de cobrança para uma fatura específica usando o filtro **faturaid** . O filtro mostra os custos de consumo e compra de produto para uma nota fiscal específica. Você também pode filtrar os custos de um cliente específico na fatura para ver os custos de imposto prévio.

Depois de integrar os clientes a um contrato de cliente da Microsoft, você receberá uma fatura que inclui todos os encargos de todos os produtos (consumo, compras e direitos) para esses clientes no contrato do cliente da Microsoft. Quando cobrado na mesma moeda, essas notas fiscais também incluem os encargos de produtos adquiridos e de qualificação, como SaaS, Azure Marketplace e reservas para clientes que ainda estão na oferta do CSP.

Para ajudar a reconciliar os encargos em relação à fatura do cliente, o escopo do perfil de cobrança permite que você veja todos os custos acumulados para uma fatura para seus clientes. Como a fatura, o escopo mostra os custos de cada cliente no novo contrato do cliente da Microsoft. O escopo também mostra cada encargo para os produtos de direitos do cliente ainda na oferta atual do CSP.

O perfil de cobrança e os escopos de conta de cobrança são os únicos escopos aplicáveis que mostram encargos para produtos baseados em compras e de compra, como o Azure Marketplace e as compras de reserva.

Os perfis de cobrança definem as assinaturas que são incluídas em uma fatura. Os perfis de cobrança são o equivalente funcional de um registro do Enterprise Agreement. Um perfil de cobrança é o escopo onde as notas fiscais são geradas.

Atualmente, a moeda de cobrança do cliente é a moeda padrão ao exibir os custos no escopo do perfil de cobrança. Os orçamentos definidos no escopo do perfil de cobrança estão na moeda de cobrança.

Os parceiros podem usar o escopo para reconciliar com faturas. E eles usam o escopo para definir orçamentos na moeda de cobrança para os seguintes itens:

- Fatura filtrada específica
- Cliente
- Subscrição
- Grupo de recursos
- Recurso
- Serviço do Azure
- Medidor
- ResellerMPNID

### <a name="customer-scope"></a>Escopo do cliente

Os parceiros usam o escopo para gerenciar os custos associados aos clientes que são integrados ao contrato do cliente da Microsoft. O escopo permite que os parceiros exibam os custos de imposto de um cliente específico. Você também pode filtrar os custos antes do imposto para uma assinatura, um grupo de recursos ou um recurso específico.

O escopo do cliente não inclui clientes que estão na oferta atual do CSP. O escopo inclui apenas os clientes que têm um contrato de cliente da Microsoft. Os custos de direito, não o uso do Azure, para a oferta atual do CSP oferecem aos clientes a conta de cobrança e os escopos do perfil de cobrança quando você aplica o filtro de cliente.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Acesso de parceiro a escopos de cobrança no gerenciamento de custos

Somente os usuários com funções de **administrador global** e **agente de administração** podem gerenciar e exibir os custos de contas de cobrança, perfis de cobrança e clientes diretamente no locatário do Azure do parceiro. Para obter mais informações sobre as funções do Partner Center, consulte [atribuir funções e permissões de usuários](/partner-center/permissions-overview).

## <a name="enable-cost-management-in-the-customer-tenant"></a>Habilitar o gerenciamento de custos no locatário do cliente

Os parceiros podem habilitar o acesso ao gerenciamento de custos depois que os clientes são integrados a um contrato de cliente da Microsoft. Em seguida, os parceiros podem habilitar uma política que permite aos clientes Exibir seus custos computados com taxas de varejo pagas conforme o uso. Os custos são mostrados na moeda de cobrança do cliente para seu uso consumido em escopos de grupos de recursos e assinatura RBAC.

Quando a política de visibilidade de custo é habilitada pelo parceiro, qualquer usuário com acesso Azure Resource Manager à assinatura pode gerenciar e analisar os custos de acordo com as tarifas pagas conforme o uso. Efetivamente, revendedores e clientes que têm o acesso RBAC apropriado às assinaturas do Azure podem exibir o custo.

Independentemente da política, os parceiros também podem exibir os custos se tiverem acesso à assinatura e ao grupo de recursos.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Habilitar a política para exibir os encargos de uso do Azure

Os parceiros usam as informações a seguir para habilitar a política para exibir os encargos de uso do Azure para seus clientes.

No portal do Azure, entre no locatário do parceiro e selecione gerenciamento de **custos + cobrança**. Selecione a conta de cobrança relevante do Microsoft Partner Agreement e, em seguida, selecione **clientes**. A lista de clientes está associada à conta de cobrança.

Na lista de clientes, selecione o cliente que você deseja permitir para exibir os custos.

![Selecionar clientes no gerenciamento de custos](./media/get-started-partners/customer-list.png)

Em **configurações**, selecione **políticas**.

A política de visibilidade de custo atual é mostrada para encargos de **uso do Azure** associados às assinaturas do cliente selecionado.
![política para permitir que os clientes exibam encargos pagos conforme o uso](./media/get-started-partners/cost-management-billing-policies.png)

Quando a política estiver definida como **não**, o gerenciamento de custos do Azure não estará disponível para os usuários de assinatura associados ao cliente. A menos que seja habilitado por um parceiro, a política de visibilidade de custo é desabilitada por padrão para todos os usuários de assinatura.

Quando a política de custo é definida como **Sim**, os usuários de assinatura associados ao locatário do cliente podem ver os encargos de uso em tarifas pagas conforme o uso.

Quando a política de visibilidade de custo é habilitada, todos os serviços que têm uso de assinatura mostram os custos em tarifas pagas conforme o uso. O uso de reserva é exibido com encargos zero para custos reais e amortizados. As compras e os direitos não estão associados a uma assinatura específica. Portanto, as compras não são exibidas no escopo da assinatura.

Para exibir os custos do locatário do cliente, abra **Gerenciamento de custos + cobrança** e selecione a conta de cobrança relevante do Microsoft Partner Agreement.

![Selecionar uma conta de cobrança](./media/get-started-partners/select-billing-account.png)

Em **cobrança**, selecione **assinaturas do Azure**e, em seguida, selecione um cliente.

![Selecionar um cliente de assinatura do Azure](./media/get-started-partners/subscriptions-select-customer.png)

Selecione **análise de custo** e comece a revisar os custos.
Análise de custo, orçamentos e alertas estão disponíveis para os escopos de RBAC de grupo de recursos e assinatura em custos pagos conforme o uso.

![Exibir análise de custo como um cliente ](./media/get-started-partners/customer-tenant-view-cost-analysis.png)

As exibições amortizadas e os custos reais das instâncias reservadas nos escopos do RBAC mostram zero encargos. Os custos de instância reservada são mostrados apenas em escopos de cobrança em que as compras foram feitas.

## <a name="analyze-costs-in-cost-analysis"></a>Analisar custos na análise de custo

Os parceiros com acesso a escopos de cobrança no locatário do parceiro podem explorar e analisar os custos faturados na análise de custos entre clientes para um cliente específico ou para uma fatura. Na exibição [análise de custo](quick-acm-cost-analysis.md) , você também pode [salvar exibições](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) e exportar dados para [arquivos CSV e png](quick-acm-cost-analysis.md#automation-and-offline-analysis).

Os usuários do RBAC com acesso à assinatura no locatário do cliente também podem analisar os custos de varejo para assinaturas no locatário do cliente, salvar exibições e exportar dados para arquivos CSV e PNG.

Você pode usar filtrar e agrupar por recursos na análise de custo para analisar os custos por vários campos. Os campos específicos do parceiro são mostrados na próxima seção.

## <a name="data-fields"></a>Campos de dados

Os campos de dados a seguir são encontrados em arquivos de detalhes de uso e APIs de gerenciamento de custos. Quando disponível, as informações equivalentes do Partner Center são mostradas. Para os seguintes campos em negrito, os parceiros podem usar filtrar e agrupar por recursos na análise de custo para analisar os custos por vários campos. Os campos em negrito se aplicam somente aos contratos de clientes da Microsoft com suporte pelos parceiros.

| **Nome do campo** | **Descrição** | **Equivalente do Partner Center** |
| --- | --- | --- |
| invoiceId | A ID da nota fiscal mostrada na nota fiscal para a transação específica. | Número da nota fiscal em que a transação é mostrada. |
| previousInvoiceID | Referência a uma fatura original há um reembolso (custo negativo). Populado somente quando há um reembolso. | N/A |
| billingAccountName | Nome da conta de cobrança que representa o parceiro. Ele acumula todos os custos entre os clientes que se integraram a um contrato com o cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. | N/A |
| billingAccountID | Identificador da conta de cobrança que representa o parceiro. | ID raiz do MCAPI Partner Commerce. Usado em uma solicitação, mas não incluído em uma resposta.|
| billingProfileID | Identificador do perfil de cobrança que agrupa custos entre faturas em uma única moeda de cobrança entre os clientes que se integraram a um contrato de cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e Reservas. | ID do grupo de cobrança do parceiro MCAPI. Usado em uma solicitação, mas não incluído em uma resposta. |
| billingProfileName | Nome do perfil de cobrança que agrupa custos entre faturas em uma única moeda de cobrança entre os clientes que se integraram a um contrato de cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e Reservas. | N/A |
| invoiceSectionName | Nome do projeto que está sendo cobrado na fatura. Não aplicável para contratos de clientes da Microsoft integrados por parceiros. | N/A |
| invoiceSectionID | Identificador do projeto que está sendo cobrado na fatura. Não aplicável para contratos de clientes da Microsoft integrados por parceiros. | N/A |
| **CustomerTenantID** | Identificador do locatário de Azure Active Directory da assinatura do cliente. | ID organizacional do cliente – a Tenantid de Azure Active Directory do cliente. |
| **Customer** | Nome do locatário de Azure Active Directory para a assinatura do cliente. | Nome da organização do cliente, conforme mostrado no Partner Center. Importante para reconciliar a fatura com as informações do sistema. |
| **CustomerTenantDomainName** | Nome de domínio para o locatário de Azure Active Directory da assinatura do cliente. | Domínio de locatário de Azure Active Directory do cliente. |
| **PartnerTenantID** | Identificador para o locatário de Azure Active Directory do parceiro. | O parceiro Azure Active Directory ID do locatário chamado como ID do parceiro, no formato GUID. |
| **PartnerName** | Nome do locatário do parceiro Azure Active Directory. | Nome do parceiro. |
| **ResellerMPNID** | MPNID para o revendedor associado à assinatura. | ID de MPN do revendedor no registro para a assinatura. Não disponível para a atividade atual. |
| costCenter | Centro de custo associado à assinatura. | N/A |
| billingPeriodStartDate | Data de início do período de cobrança, conforme mostrado na nota fiscal. | N/A |
| billingPeriodEndDate | Data de término do período de cobrança, conforme mostrado na nota fiscal. | N/A |
| servicePeriodStartDate | Data de início do período de classificação em que o uso do serviço foi classificado para cobranças. Os preços dos serviços do Azure são determinados para o período de classificação. | ChargeStartDate no Partner Center. Data de início do ciclo de cobrança, exceto ao apresentar datas de dados de uso latentes anteriormente não cobrados de um ciclo de cobrança anterior. O tempo é sempre o início do dia, 0:00. |
| servicePeriodEndDate | Data de término do período em que o uso do serviço foi classificado para cobranças. Os preços dos serviços do Azure são determinados com base no período de classificação. | N/A |
| date | Para dados de consumo do Azure, ele mostra a data de uso como classificado. Para a instância reservada, ela mostra a data de compra. Para encargos recorrentes e encargos de uso único, como Marketplace e suporte, ele mostra a data de compra. | N/A |
| productID | Identificador do produto que tem encargos acumulados por consumo ou compra. É a chave concatenada de productID e SKuID, conforme mostrado no Partner Center. | A ID do produto. |
| produto | Nome do produto que tem encargos acumulados por consumo ou compra, conforme mostrado na nota fiscal. | O nome do produto no catálogo. |
| serviceFamily | Mostra a família de serviços para o produto adquirido ou cobrado. Por exemplo, armazenamento ou computação. | N/A |
| productOrderID | O identificador do ativo ou o nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. | N/A |
| productOrderName | O nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. | N/A|
| consumedService | Serviço consumido (taxonomia herdada), conforme usado em detalhes de uso de EA herdado. | Serviço mostrado no Partner Center. Por exemplo, Microsoft. Storage, Microsoft. Compute e Microsoft. operationalinsights. |
| meterId | Identificador medido para consumo medido. | A ID do medidor usado. |
| meterName | Identifica o nome do medidor para consumo medido. | O nome do medidor consumido. |
| meterCategory | Identifica o serviço de nível superior para uso. | O serviço de nível superior para o uso. |
| meterSubCategory | Define o tipo ou a subcategoria do serviço do Azure que pode afetar a taxa. | O tipo de serviço do Azure que pode afetar a taxa.|
| meterRegion | Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter. | O local regional de um data center para serviços, quando aplicável e preenchidos. |
| ID da subscrição | Identificador exclusivo gerado pela Microsoft para a assinatura do Azure. | N/A |
| subscriptionName | Nome da subscrição do Azure. | N/A |
| Duração | Apresenta o termo da validade da oferta. Por exemplo, as instâncias reservadas mostram 12 meses de um termo anual da instância reservada. Para compras de uma vez ou compras recorrentes, o termo exibe um mês para SaaS, Azure Marketplace e suporte. Não aplicável ao consumo do Azure. | N/A |
| PublisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo de Publicador que identifica o Publicador como primeira parte, revendedor de terceiros ou agência de terceiros. | N/A |
| partNumber | Número de peça para a instância reservada não usada e os serviços do Azure Marketplace. | N/A |
| publisherName | Nome do editor do serviço, incluindo Publicadores da Microsoft ou de terceiros. | O nome do editor do produto.|
| reservationId | Identificador para a compra de instância reservada. | N/A |
| reservationName | Nome da instância reservada. | N/A |
| reservationOrderId | OrderID para a instância reservada. | N/A |
| frequência | Frequência de pagamento para uma instância reservada. | N/A |
| resourceGroup | Nome do grupo de recursos do Azure usado para gerenciamento de recursos do ciclo de vida. | Nome do grupo de recursos. |
| instanceID (ou) ResourceId | Identificador da instância de recurso. | Mostrado como um ResourceURI que inclui propriedades de recurso completas. |
| resourceLocation | Nome do local do recurso. | O local do recurso. |
| Localização | Local normalizado do recurso. | N/A |
| effectivePrice | O preço unitário efetivo do serviço, em moeda de preços. Exclusivo para um produto, uma família de serviços, um medidor e uma oferta. Usado com preços na folha de preços da conta de cobrança. Quando há preços em camadas ou uma quantidade incluída, ele mostra o preço combinado do consumo. | O preço unitário após os ajustes serem feitos. |
| Quantidade | Quantidade medida comprada ou consumida. O valor do medidor usado durante o período de cobrança. | Número de unidades. Verifique se ele corresponde às informações em seu sistema de cobrança durante a reconciliação. |
| unitOfMeasure | Identifica a unidade na qual o serviço é cobrado. Por exemplo, GB e horas. | Identifica a unidade na qual o serviço é cobrado. Por exemplo, GB, horas e 10.000 s. |
| pricingCurrency | A moeda que define o preço unitário. | A moeda na lista de preços.|
| billingCurrency | A moeda que define o custo cobrado. | A moeda da região geográfica do cliente. |
| chargeType | Define o tipo de encargo que o custo representa no gerenciamento de custos do Azure, como compra e reembolso. | O tipo de encargo ou ajuste. Não disponível para a atividade atual. |
| costinBillingCurrency | ExtendedCost ou custo combinado antes do imposto na moeda cobrada. | N/A |
| costinPricingCurrency | ExtendedCost ou custo combinado antes do imposto na moeda de preços para correlacionar com os preços. | N/A |
| **costinUSD** | ExtendedCost estimado ou custo combinado antes do imposto em USD. | N/A |
| **paygCostInBillingCurrency** | Mostra os custos se os preços estiverem em preços de varejo. Mostra os preços pagos conforme o uso na moeda de cobrança. Disponível somente em escopos de RBAC. | N/A |
| **paygCostInUSD** | Mostra os custos se os preços estiverem em preços de varejo. Mostra os preços pagos conforme o uso em USD. Disponível somente em escopos de RBAC. | N/A |
| exchangeRate | Taxa de câmbio usada para converter da moeda de preço para a moeda de cobrança. | Conhecido como PCToBCExchangeRate no Partner Center. A moeda de preço para a taxa de câmbio da moeda de cobrança.|
| exchangeRateDate | A data da taxa de câmbio usada para converter da moeda de preço para a moeda de cobrança. | Conhecido como PCToBCExchangeRateDat no Partner Center. A moeda de preço para cobrança da moeda da taxa de câmbio.|
| isAzureCreditEligible | Indica se o custo é qualificado para pagamento por créditos do Azure. | N/A |
| serviceInfo1 | Campo herdado que captura metadados específicos do serviço opcionais. | Metadados de serviço do Azure internos. |
| serviceInfo2 | Campo herdado que captura metadados específicos do serviço opcionais. | Informações de serviço. Por exemplo, um tipo de imagem para uma máquina virtual e o nome do ISP para ExpressRoute.|
| additionalInfo | Metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual. | Quaisquer informações adicionais não abordadas em outras colunas. Os metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual.|
| etiquetas | Marca que você atribui ao medidor. Use marcas para agrupar registros de cobrança. Por exemplo, você pode usar marcas para distribuir custos pelo departamento que usa o medidor. | Marcas adicionadas pelo cliente.|
| **partnerEarnedCreditRate** | Taxa de desconto aplicado se houver um acordo de crédito de parceiro (PEC) com base no acesso de link do administrador do parceiro. | A taxa de o PEC (crédito ganho do parceiro). Por exemplo, 0% ou 15%. |
| **partnerEarnedCreditApplied** | Indica se o crédito obtido do parceiro foi aplicado. | N/A |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Exibir custos de recursos do PEC (crédito acumulado) do parceiro

No gerenciamento de custos do Azure, os parceiros podem usar a análise de custo para exibir os custos que receberam os benefícios do PEC.

No portal do Azure, entre no locatário do parceiro e selecione gerenciamento de **custos + cobrança**. Em **Gerenciamento de custos**, selecione **análise de custo**.

A exibição análise de custo mostra os custos da conta de cobrança do parceiro. Selecione o **escopo** conforme necessário para o parceiro, um cliente específico ou um perfil de cobrança para reconciliar faturas.

Em um gráfico de rosca, selecione a lista suspensa e selecione **PartnerEarnedCreditApplied** para analisar os custos do PEC.

![Exemplo mostrando como exibir o crédito obtido pelo parceiro](./media/get-started-partners/cost-analysis-pec1.png)

Quando a propriedade **PartnerEarnedCreditApplied** é _verdadeira_, o custo associado tem o benefício do acesso de administrador obtido pelo parceiro.

Quando a propriedade **PartnerEarnedCreditApplied** é _false_, o custo associado não atende à qualificação necessária para o crédito. Ou, o serviço adquirido não está qualificado para crédito de parceiro ganho.

Os dados de uso do serviço normalmente levam 8-24 horas para serem exibidos no gerenciamento de custos. Para obter mais informações, consulte a [frequência de atualização de dados de uso varia](understand-cost-mgt-data.md#usage-data-update-frequency-varies). Os créditos do PEC aparecem dentro de 48 horas a partir do tempo de acesso no gerenciamento de custos do Azure.


Você também pode agrupar e filtrar pela propriedade **PartnerEarnedCreditApplied** usando as opções **Agrupar por** . Use as opções para examinar os custos que fazem e não têm o PEC.

![Agrupar ou filtrar por crédito obtido pelo parceiro](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Exportar dados de custo para o armazenamento do Azure

Os parceiros com acesso a escopos de cobrança em um locatário de parceiro podem exportar seus dados de custo e de uso para um blob de armazenamento do Azure. O blob deve estar em uma assinatura no locatário do parceiro que não seja uma assinatura de serviço compartilhada ou uma assinatura do cliente. Para habilitar a exportação de dados de custo, recomendamos que você configure uma assinatura paga conforme o uso independente no locatário do parceiro para hospedar os dados de custo exportados. A conta de armazenamento de exportação é criada no blob de armazenamento do Azure hospedado na assinatura paga conforme o uso. Com base no escopo em que o parceiro cria a exportação, os dados associados são exportados para a conta de armazenamento automaticamente de forma recorrente.

Os usuários com acesso RBAC à assinatura também podem exportar os dados de custo para um blob de armazenamento do Azure hospedado em qualquer assinatura no locatário do cliente.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Criar uma exportação em um locatário de parceiro ou locatário de cliente

No portal do Azure, entre no locatário do parceiro ou no locatário do cliente e selecione **Gerenciamento de custos + cobrança**. Selecione um escopo apropriado, por exemplo, uma conta de cobrança do contrato de parceiro da Microsoft e selecione **análise de custo**. Quando a página for carregada, selecione **Exportar**. Selecione **Exibir todas as exportações** em agendar exportação.

![Selecionar exportar e exibir todas as exportações](./media/get-started-partners/export01.png)

Em seguida, selecione **Adicionar** e digite o nome e selecione um tipo de exportação. Selecione a guia **armazenamento** e insira as informações necessárias.

![Adicionar nova exportação e selecionar a guia armazenamento](./media/get-started-partners/export02.png)

Ao criar uma exportação no locatário do parceiro, selecione a assinatura paga conforme o uso no locatário do parceiro. Crie uma conta de armazenamento do Azure usando essa assinatura.

Para usuários do RBAC no locatário do cliente, selecione uma assinatura no locatário do cliente. Crie uma conta de armazenamento do Azure usando a assinatura.

Examine o conteúdo e, em seguida, selecione **criar** para agendar uma exportação.

Para verificar os dados na lista exportar, selecione o nome da conta de armazenamento. Na página conta de armazenamento, selecione **contêineres** e, em seguida, selecione o contêiner. Navegue até a pasta correspondente e selecione o arquivo CSV. Selecione **baixar** para obter o arquivo CSV e abri-lo. Os dados exportados exportados se assemelham a dados de custo semelhantes aos detalhes de uso do portal do Azure.

![Exemplo de dados exportados](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>APIs REST de gerenciamento de custos

Parceiros e clientes podem usar as APIs de gerenciamento de custos descritas nas seções a seguir para tarefas comuns.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>APIs de gerenciamento de custos do Azure – provedores diretos e indiretos

Os parceiros com acesso a escopos de cobrança em um locatário de parceiro podem usar as seguintes APIs para exibir os custos faturados.

As APIs no escopo da assinatura podem ser chamadas por um parceiro, independentemente da política de custo, se tiverem acesso à assinatura. Outros usuários com acesso à assinatura, como o cliente ou revendedor, podem chamar as APIs somente depois que o parceiro habilita a política de custo para o locatário do cliente.


#### <a name="to-get-a-list-of-billing-accounts"></a>Para obter uma lista de contas de cobrança

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Para obter uma lista de clientes

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Para obter uma lista de assinaturas

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Para obter uma lista de faturas por um período de tempo

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

A chamada à API retorna uma matriz de faturas que tem elementos semelhantes ao código JSON a seguir.

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

Use o valor do campo ID retornado anteriormente e substitua-o no exemplo a seguir como o escopo para consultar os detalhes de uso.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

O exemplo retorna os registros de uso associados à fatura específica.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Para obter a política para os clientes exibirem custos

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Para definir a política para os clientes exibirem custos

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Para obter o uso do serviço do Azure para uma conta de cobrança

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Para baixar o uso do serviço do Azure de um cliente

A seguinte chamada Get é uma operação assíncrona.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Chame o URI de `Location` retornado na resposta para verificar o status da operação. Quando o status for *concluído*, a propriedade `downloadUrl` conterá um link que você pode usar para baixar o relatório gerado.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Para obter ou baixar a folha de preços dos serviços do Azure consumidos

Primeiro, use a seguinte postagem.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Em seguida, chame o valor da propriedade de operação assíncrona. Por exemplo:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
A chamada Get anterior retorna o link de download que contém a folha de preços.


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

#### <a name="delete-a-budget"></a>Excluir um orçamento

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Passos seguintes
- [Comece a analisar os custos](quick-acm-cost-analysis.md) no gerenciamento de custos
- [Criar e gerenciar orçamentos](tutorial-acm-create-budgets.md) no gerenciamento de custos
