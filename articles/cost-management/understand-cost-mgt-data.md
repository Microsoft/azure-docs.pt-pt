---
title: Entender os dados de gerenciamento de custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender melhor os dados incluídos no gerenciamento de custos do Azure e com que frequência eles são processados, coletados, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/06/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 681ccc768b1fa3d5a968847d11987fbd83898b59
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721367"
---
# <a name="understand-cost-management-data"></a>Compreender os dados do Cost Management

Este artigo ajuda você a entender melhor os dados de uso e de custo do Azure incluídos no gerenciamento de custos do Azure. Ele explica com que frequência os dados são processados, coletados, mostrados e fechados. Você é cobrado pelo uso mensal do Azure. Embora os ciclos de cobrança sejam períodos mensais, as datas de início e término do ciclo variam por tipo de assinatura. A frequência com que o gerenciamento de custos recebe dados de uso varia de acordo com os diferentes fatores. Esses fatores incluem quanto tempo leva para processar os dados e com que frequência os serviços do Azure emitem o uso para o sistema de cobrança.

O gerenciamento de custos inclui todo o uso e as compras, incluindo reservas e ofertas de terceiros para contas de Enterprise Agreement (EA). As contas de contrato de cliente da Microsoft e as assinaturas individuais com tarifas pagas conforme o uso incluem apenas os serviços do Azure e do Marketplace. O suporte e outros custos não são incluídos. Os custos são estimados até que uma nota fiscal seja gerada e não Fatore os créditos.

## <a name="supported-microsoft-azure-offers"></a>Ofertas de Microsoft Azure com suporte

As informações a seguir mostram as [ofertas de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no momento no gerenciamento de custos do Azure. Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Os dados estão disponíveis no gerenciamento de custos a partir dos **dados disponíveis a partir da** Data. Se uma assinatura alterar as ofertas, os custos antes da data de alteração da oferta não estarão disponíveis.

| **Categoria**  | **Nome da oferta** | **ID da cota** | **Número da oferta** | **Dados disponíveis de** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maio de 2014<sup>1</sup> |
| **Contrato de Cliente da Microsoft** | [Plano de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | Março de 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft** | [Plano de Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | Março de 2019<sup>3</sup> |
| **Contrato com o cliente da Microsoft com suporte dos parceiros** | Plano do Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>A ID da cota é reutilizada para o contrato do cliente da Microsoft e assinaturas CSP herdadas. Atualmente, há suporte apenas para assinaturas do contrato do cliente da Microsoft. | N/D | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P-MS-AZR-0125P, MS-AZR-0128P-MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 de outubro de 2018<sup>2</sup> |

_<sup>**1**</sup> para os dados antes de 2014 de maio, visite o [portal do Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> para dados antes de 2 de outubro de 2018, visite o [centro de contas do Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> os contratos de clientes da Microsoft começaram em março de 2019 e não têm nenhum dado histórico antes desse ponto._

_<sup>**4**</sup> os dados históricos para assinaturas com base em crédito e de pagamento em adiantamento podem não corresponder à sua fatura. Consulte [os dados históricos podem não corresponder à fatura](#historical-data-might-not-match-invoice) abaixo._

As ofertas a seguir ainda não têm suporte:

| Categoria  | **Nome da oferta** | **ID da cota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Azure Alemanha pago conforme o uso](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Provedor de soluções de nuvem (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Provedor de soluções de nuvem (CSP)** | CSP do Azure governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Provedor de soluções de nuvem (CSP)** | Azure Alemanha no CSP para a Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay As You Go**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay As You Go** | [Azure para estudantes](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay As You Go**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de Suporte** | Suporte Padrão                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de Suporte** | Suporte a Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de Suporte** | Suporte ao desenvolvedor                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de Suporte** | Plano de suporte da Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de Suporte** | Suporte Standard do Azure governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de Suporte** | Suporte pro-Direct do Azure governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de Suporte** | Suporte Developer do Azure governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se você não vir os dados de uma assinatura e quiser determinar se sua assinatura está sob as ofertas com suporte, você pode validar que sua assinatura tem suporte. Para validar que uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **todos os serviços** no painel de menu à esquerda. Na lista de serviços, selecione **assinaturas**. No menu lista de assinaturas, clique na assinatura que você deseja verificar. Sua assinatura é mostrada na guia Visão geral e você pode ver a **oferta** e a **ID da oferta**. A imagem seguinte mostra um exemplo.

![Exemplo da guia Visão geral da assinatura mostrando a oferta e a ID da oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no gerenciamento de custos

As tabelas a seguir mostram os dados que estão incluídos ou não estão no gerenciamento de custos. Todos os custos são estimados até que uma fatura seja gerada. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

**Dados de uso e custo**

| **Incluídos** | **Não incluído** |
| --- | --- |
| Uso do serviço do Azure<sup>5</sup>        | Encargos de suporte-para obter mais informações, consulte os [termos da nota fiscal explicados](../billing/billing-understand-your-invoice.md). |
| Uso<sup>6</sup> da oferta do Marketplace | Impostos-para obter mais informações, consulte os [termos da nota fiscal explicados](../billing/billing-understand-your-invoice.md). |
| Compras do Marketplace<sup>6</sup>      | Créditos-para obter mais informações, consulte os [termos da nota fiscal explicados](../billing/billing-understand-your-invoice.md). |
| Compras de reserva<sup>7</sup>      |  |
| Amortização de compras de reserva<sup>7</sup>      |  |

_<sup>**5**</sup> o uso do serviço do Azure baseia-se na reserva e nos preços negociados._

_<sup>**6**</sup> as compras do Marketplace não estão disponíveis para ofertas de pagamento conforme o uso, MSDN e Visual Studio no momento._

_<sup>**7**</sup> as compras de reserva estão disponíveis somente para contas de Enterprise Agreement (ea) neste momento._

**Los**

| **Incluídos** | **Não incluído** |
| --- | --- |
| Marcas de recurso<sup>8</sup> | Marcas do grupo de recursos |

_<sup>**8**</sup> marcas de recurso são aplicadas conforme o uso é emitido de cada serviço e não está disponível retroativamente para uso histórico._

## <a name="rated-usage-data-refresh-schedule"></a>Agendamento de atualização de dados de uso classificados

Os dados de custo e de uso estão disponíveis no gerenciamento de custos + cobrança no portal do Azure e nas [APIs de suporte](index.yml). Tenha os seguintes pontos em mente ao examinar os custos:

- Os encargos estimados para o período de cobrança atual são atualizados seis vezes por dia.
- Os encargos estimados para o período de cobrança atual podem mudar à medida que você incorre mais uso.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _fecha_ o período de cobrança atual de até 72 horas (três dias de calendário) após o término do período de cobrança.

Os exemplos a seguir ilustram como os períodos de cobrança podem terminar.

Assinaturas do Enterprise Agreement (EA) – se o mês de cobrança terminar em 31 de março, os encargos estimados serão atualizados até 72 horas depois. Neste exemplo, por meia-noite (UTC) 4 de abril.

Assinaturas pagas conforme o uso – se o mês de cobrança terminar em 15 de maio, os encargos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, por meia-noite (UTC) de 19 de maio.

### <a name="rerated-data"></a>Dados reclassificados

Se você usar as [APIs de gerenciamento de custos](index.yml), Power bi ou o portal do Azure para recuperar dados, espere que os encargos do período de cobrança atual sejam reclassificados e, consequentemente, sejam alterados até que a fatura seja fechada.

## <a name="cost-management-data-fields"></a>Campos de dados de gerenciamento de custos

Os campos de dados a seguir são encontrados em arquivos de detalhes de uso e APIs de gerenciamento de custos. Para os seguintes campos em negrito, os parceiros podem usar filtrar e agrupar por recursos na análise de custo para analisar os custos por vários campos. Os campos em negrito se aplicam somente aos contratos de clientes da Microsoft com suporte pelos parceiros.

| **Nome do campo** | **Descrição** |
| --- | --- |
| invoiceId | A ID da nota fiscal mostrada na nota fiscal para a transação específica. |
| previousInvoiceID | Referência a uma fatura original há um reembolso (custo negativo). Populado somente quando há um reembolso. |
| billingAccountName | Nome da conta de cobrança que representa o parceiro. Ele acumula todos os custos entre os clientes que se integraram a um contrato com o cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e reservas. |
| billingAccountID | Identificador da conta de cobrança que representa o parceiro. |
| billingProfileID | Identificador do perfil de cobrança que agrupa custos entre faturas em uma única moeda de cobrança entre os clientes que se integraram a um contrato de cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e Reservas. |
| billingProfileName | Nome do perfil de cobrança que agrupa custos entre faturas em uma única moeda de cobrança entre os clientes que se integraram a um contrato de cliente da Microsoft e os clientes do CSP que fizeram compras de direitos como SaaS, Azure Marketplace e Reservas. |
| invoiceSectionName | Nome do projeto que está sendo cobrado na fatura. Não aplicável para contratos de clientes da Microsoft integrados por parceiros. |
| invoiceSectionID | Identificador do projeto que está sendo cobrado na fatura. Não aplicável para contratos de clientes da Microsoft integrados por parceiros. |
| **CustomerTenantID** | Identificador do locatário de Azure Active Directory da assinatura do&#39;cliente s. |
| **Customer** | Nome do locatário de Azure Active Directory para a assinatura&#39;do cliente s. |
| **CustomerTenantDomainName** | Nome de domínio para o locatário de Azure Active Directory da&#39;assinatura do cliente s. |
| **PartnerTenantID** | Identificador para o locatário&#39;de Azure Active Directory do parceiro. |
| **PartnerName** | Nome do locatário do parceiro Azure Active Directory. |
| **ResellerMPNID** | MPNID para o revendedor associado à assinatura. |
| costCenter | Centro de custo associado à assinatura. |
| billingPeriodStartDate | Data de início do período de cobrança, conforme mostrado na nota fiscal. |
| billingPeriodEndDate | Data de término do período de cobrança, conforme mostrado na nota fiscal. |
| servicePeriodStartDate | Data de início do período de classificação em que o uso do serviço foi classificado para cobranças. Os preços dos serviços do Azure são determinados para o período de classificação. |
| servicePeriodEndDate | Data de término do período em que o uso do serviço foi classificado para cobranças. Os preços dos serviços do Azure são determinados com base no período de classificação. |
| date | Para dados de consumo do Azure, ele mostra a data de uso como classificado. Para a instância reservada, ela mostra a data de compra. Para encargos recorrentes e encargos de uso único, como Marketplace e suporte, ele mostra a data de compra. |
| productID | Identificador do produto que tem encargos acumulados por consumo ou compra. É a chave concatenada de productID e SKuID, conforme mostrado no Partner Center. |
| produto | Nome do produto que tem encargos acumulados por consumo ou compra, conforme mostrado na nota fiscal. |
| serviceFamily | Mostra a família de serviços para o produto adquirido ou cobrado. Por exemplo, armazenamento ou computação. |
| productOrderID | O identificador do ativo ou o nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. |
| productOrderName | O nome do plano do Azure ao qual a assinatura pertence. Por exemplo, plano do Azure. |
| consumedService | Serviço consumido (taxonomia herdada), conforme usado em detalhes de uso de EA herdado. |
| meterId | Identificador medido para consumo medido. |
| meterName | Identifica o nome do medidor para consumo medido. |
| meterCategory | Identifica o serviço de nível superior para uso. |
| meterSubCategory | Define o tipo ou a subcategoria do serviço do Azure que pode afetar a taxa. |
| meterRegion | Identifica a localização do datacenter para determinados serviços cujo preço é definido com base na localização do datacenter. |
| ID da subscrição | Identificador exclusivo gerado pela Microsoft para a assinatura do Azure. |
| subscriptionName | Nome da subscrição do Azure. |
| Termo | Apresenta o termo da validade da oferta. Por exemplo, as instâncias reservadas mostram 12 meses de um termo anual da instância reservada. Para compras de uma vez ou compras recorrentes, o termo exibe um mês para SaaS, Azure Marketplace e suporte. Não aplicável ao consumo do Azure. |
| PublisherType (firstParty, thirdPartyReseller, thirdPartyAgency) | Tipo de Publicador que identifica o Publicador como primeira parte, revendedor de terceiros ou agência de terceiros. |
| PartNumber | Número de peça para a instância reservada não usada e os serviços do Azure Marketplace. |
| publisherName | Nome do editor do serviço, incluindo Publicadores da Microsoft ou de terceiros. |
| reservationId | Identificador para a compra de instância reservada. |
| reservationName | Nome da instância reservada. |
| reservationOrderId | OrderID para a instância reservada. |
| frequência | Frequência de pagamento para uma instância reservada. |
| resourceGroup | Nome do grupo de recursos do Azure usado para gerenciamento de recursos do ciclo de vida. |
| instanceID (ou) ResourceId | Identificador da instância de recurso. |
| resourceLocation | Nome do local do recurso. |
| Localização | Local normalizado do recurso. |
| effectivePrice | O preço unitário efetivo do serviço, em moeda de preços. Exclusivo para um produto, uma família de serviços, um medidor e uma oferta. Usado com preços na folha de preços da conta de cobrança. Quando há preços em camadas ou uma quantidade incluída, ele mostra o preço combinado do consumo. |
| Quantidade | Quantidade medida comprada ou consumida. O valor do medidor usado durante o período de cobrança. |
| unitOfMeasure | Identifica a unidade na qual o serviço é cobrado. Por exemplo, GB e horas. |
| pricingCurrency | A moeda que define o preço unitário. |
| billingCurrency | A moeda que define o custo cobrado |
| chargeType | Define o tipo de encargo que o custo representa no gerenciamento de custos do Azure, como compra e reembolso. |
| costinBillingCurrency | ExtendedCost ou custo combinado antes do imposto na moeda cobrada. |
| costinPricingCurrency | ExtendedCost ou custo combinado antes do imposto na moeda de preços para correlacionar com os preços. |
| **costinUSD** | ExtendedCost estimado ou custo combinado antes do imposto em USD. |
| **paygCostInBillingCurrency** | Mostra os custos se os preços estiverem em preços de varejo. Mostra os preços pagos conforme o uso na moeda de cobrança. Disponível somente em escopos de RBAC. |
| **paygCostInUSD** | Mostra os custos se os preços estiverem em preços de varejo. Mostra os preços pagos conforme o uso em USD. Disponível somente em escopos de RBAC. |
| exchangeRate | Taxa de câmbio usada para converter da moeda de preço para a moeda de cobrança. |
| exchangeRateDate | A data da taxa de câmbio que&#39;s usou para converter da moeda de preço para a moeda de cobrança. |
| isAzureCreditEligible | Indica se o custo é qualificado para pagamento por créditos do Azure. |
| serviceInfo1 | Campo herdado que captura metadados específicos do serviço opcionais. |
| serviceInfo2 | Campo herdado que captura metadados específicos do serviço opcionais. |
| additionalInfo | Metadados específicos do serviço. Por exemplo, um tipo de imagem de uma máquina virtual. |
| etiquetas | Marca que você atribui ao medidor. Use marcas para agrupar registros de cobrança. Por exemplo, você pode usar marcas para distribuir custos pelo departamento que usa o medidor. |
| **partnerEarnedCreditRate** | Taxa de desconto aplicado se houver um acordo de crédito de parceiro (PEC) com base no acesso de link do administrador do parceiro. |
| **partnerEarnedCreditApplied** | Indica se o crédito obtido do parceiro foi aplicado. |


## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização de dados de uso varia

A disponibilidade dos dados de uso incorridos no gerenciamento de custos depende de alguns fatores, incluindo:

- Com que frequência os serviços do Azure (como armazenamento, computação, CDN e SQL) emitem o uso.
- O tempo necessário para processar os dados de uso por meio do mecanismo de classificação e dos pipelines de gerenciamento de custos.

Alguns serviços emitem o uso com mais frequência do que outros. Portanto, você pode ver dados em gerenciamento de custos para alguns serviços antes de outros serviços que emitem dados com menos frequência. Normalmente, o uso de serviços leva 8-24 horas para aparecer no gerenciamento de custos. Tenha em mente que os dados de um mês aberto são atualizados conforme você incorre em mais uso porque as atualizações são cumulativas.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos podem não corresponder à fatura

Os dados históricos para ofertas com base em crédito e de pagamento antecipado podem não corresponder à fatura. Algumas ofertas pagas conforme o uso do Azure, MSDN e Visual Studio podem ter créditos do Azure e pagamentos avançados aplicados à fatura. No entanto, os dados históricos mostrados no gerenciamento de custos baseiam-se apenas nos encargos de consumo estimado. Os dados históricos do gerenciamento de custos não incluem pagamentos e créditos. Como resultado, os dados históricos mostrados para as ofertas a seguir podem não corresponder exatamente à sua fatura.

- Azure para estudantes (MS-AZR-0170P)
- Azure via Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Avaliação gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Consultar também

- Se você ainda não tiver concluído o primeiro início rápido para o gerenciamento de custos, leia-o em [comece a analisar os custos](quick-acm-cost-analysis.md).
