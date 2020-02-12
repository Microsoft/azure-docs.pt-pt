---
title: Compreender os dados do Azure Cost Management | Microsoft Docs
description: Este artigo ajuda-o a compreender melhor os dados que estão incluídos no Azure Cost Management e com que frequência são processados, recolhidos, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 01/29/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: ''
ms.openlocfilehash: 156684676758d777231d3b159ba7bc4749b8582a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76901772"
---
# <a name="understand-cost-management-data"></a>Compreender os dados do Cost Management

Este artigo ajuda-o a compreender melhor os dados de custos e utilização do Azure que estão incluídos no Azure Cost Management. Aqui explicamos-lhe com que frequência os dados são processados, recolhidos, mostrados e fechados. A sua faturação corresponde a uma utilização mensal do Azure. Apesar de os períodos de faturação serem períodos mensais, as datas de início e fim do ciclo variam consoante o tipo de subscrição. A frequência com que o Cost Management recebe os dados de utilização varia com base em diferentes fatores. Esses fatores incluem o tempo de duração do processamento de dados e a frequência com que os serviços do Azure emitem a utilização para o sistema de faturação.

O Cost Management inclui toda a utilização e todas as compras, incluindo reservas e ofertas de terceiros para contas de Contrato Enterprise (EA). As contas e as subscrições individuais do Contrato de Cliente Microsoft com tarifas Pay As You Go incluem apenas a utilização dos serviços do Azure e do Marketplace. O suporte e os outros custos não estão incluídos. Os custos são estimados até ser gerada uma fatura e não influenciam os créditos.

## <a name="supported-microsoft-azure-offers"></a>Ofertas suportadas do Microsoft Azure

As seguintes informações mostram as [ofertas atualmente suportadas pelo Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) no Azure Cost Management. Uma oferta do Azure é o tipo de subscrição do Azure que possui. Os dados estão disponíveis no Cost Management a partir da data **Dados disponíveis a partir de**. Se uma subscrição alterar as ofertas, os custos anteriores à data de alteração da oferta deixam de estar disponíveis.

| **Categoria**  | **Nome da oferta** | **ID de quota** | **Número de oferta** | **Dados disponíveis a partir de** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | maio de 2014<sup>1</sup> |
| **Contrato Enterprise (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | maio de 2014<sup>1</sup> |
| **Contrato Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | maio de 2014<sup>1</sup> |
| **Contrato de Cliente da Microsoft** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/D | março de 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft** | [Plano do Microsoft Azure para Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/D | março de 2019<sup>3</sup> |
| **Contrato de Cliente Microsoft suportado por parceiros** | Plano do Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>O ID de quota é reutilizado para o Contrato de Cliente Microsoft e subscrições de CSP de legado. Atualmente, apenas são suportadas as subscrições do Contrato de Cliente Microsoft. | N/D | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Avaliação Gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Azure no Licenciamento Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 de outubro de 2018<sup>2</sup> |

_<sup>**1**</sup> Para dados anteriores a maio de 2014, visite o [portal do Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> Para dados anteriores a 2 de outubro de 2018, visite o [Centro de Contas do Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Os contratos de Clientes Microsoft começaram em março de 2019 e não têm quaisquer dados históricos anteriores a este ponto._

_<sup>**4**</sup> Os dados históricos de subscrições baseadas em crédito e de pré-pagamento podem não corresponder à sua fatura. Veja [Os dados históricos podem não corresponder à fatura](#historical-data-might-not-match-invoice) abaixo._

As seguintes ofertas ainda não são suportadas:

| Categoria  | **Nome da oferta** | **ID de quota** | **Número de oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Pay As You Go do Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Pay As You Go do Azure Government | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Fornecedor de Soluções Cloud (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Fornecedor de Soluções Cloud (CSP)** | CSP do Azure Government                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Fornecedor de Soluções Cloud (CSP)** | Azure Alemanha em CSP para a Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay As You Go**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay As You Go** | [Microsoft Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay As You Go**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de Suporte** | Suporte Padrão                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de Suporte** | Suporte Direto Profissional         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de Suporte** | Suporte para Programadores                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de Suporte** | Plano de suporte da Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de Suporte** | Suporte Padrão do Azure Government   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de Suporte** | Suporte Pro-Direct do Azure Government | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de Suporte** | Suporte para Programadores do Azure Government  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se não vir os dados de uma subscrição e quiser determinar se a sua subscrição se enquadra nas ofertas suportadas, poderá validar o suporte da sua subscrição. Para validar o suporte de uma subscrição do Azure, inicie sessão no [portal do Azure](https://portal.azure.com). Em seguida, selecione **Todos os Serviços** no menu à esquerda. Na lista de serviços, selecione **Subscrições**. No menu da lista de subscrições, selecione a subscrição pretende verificar. A sua subscrição é mostrada no separador Descrição geral e pode ver a **Oferta** e o **ID de Oferta**. A imagem seguinte mostra um exemplo.

![Exemplo do separador Descrição geral da Subscrição que mostra a Oferta e o ID de Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Cost Management

As seguintes tabelas mostram os dados que são incluídos ou não no Cost Management. Todos os custos são estimados até que seja gerada uma fatura. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

**Dados de custos e de utilização**

| **Incluídos** | **Não incluídos** |
| --- | --- |
| Utilização do serviço do Azure<sup>5</sup>        | Custos de suporte – para obter mais informações, veja [Termos da fatura explicados](../understand/understand-invoice.md). |
| Utilização das ofertas do Marketplace<sup>6</sup> | Impostos – para obter mais informações, veja [Termos da fatura explicados](../understand/understand-invoice.md). |
| Compras do Marketplace<sup>6</sup>      | Créditos – para obter mais informações, veja [Termos da fatura explicados](../understand/understand-invoice.md). |
| Compras de reservas<sup>7</sup>      |  |
| Amortização de compras de reservas<sup>7</sup>      |  |

_<sup>**5**</sup> A utilização dos serviços do Azure baseia-se na reserva e nos preços negociados._

_<sup>**6**</sup> De momento, as compras do Marketplace não estão disponíveis para ofertas de Pay As You Go, MSDN e Visual Studio._

_<sup>**7**</sup> De momento, as compras de reservas apenas estão disponíveis para contas de Contrato Enterprise (EA)._

**Metadados**

| **Incluídos** | **Não incluídos** |
| --- | --- |
| Etiquetas de recursos<sup>8</sup> | Etiquetas de grupos de recursos |

_<sup>**8**</sup> As etiquetas de recursos são aplicadas à medida que a utilização é emitida por cada um dos serviços e não estão disponíveis retroativamente para utilização de histórico._

**Avaliação gratuita para atualização de Pay As You Go**

Os clientes com uma oferta de Avaliação Gratuita (044P) a converter para a oferta de Pay As You Go (003P) podem ver a utilização deles durante o período de Avaliação Gratuita. Contudo, perdem a visibilidade da utilização da Avaliação Gratuita após a conversão. Depois da conversão, apenas a utilização e os custos Pay As You Go são mostrados no Cost Management.

## <a name="rated-usage-data-refresh-schedule"></a>Agendamento de atualização de dados de utilização classificada

Os dados de custos e utilização estão disponíveis em Cost Management + Faturação no portal do Azure e em [APIs de suporte](../index.yml). Tenha em consideração os seguintes pontos ao rever os custos:

- Os custos estimados para o atual período de faturação são atualizados seis vezes por dia.
- Os custos estimados para o atual período de faturação pode ser alterado à medida que incorre em mais utilização.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _fecha_ o período de faturação atual até 72 horas (três dias do calendário) após a terminação do período de faturação.

Os seguintes exemplos ilustram a forma como os períodos de faturação podem terminar.

Subscrições de Contrato Enterprise (EA) – se o mês de faturação terminar em 31 de março, os custos estimados serão atualizados até 72 horas depois. Neste exemplo, até à meia-noite (UTC) de 4 de abril.

Subscrições Pay As You Go – se o mês de faturação terminar em 15 de maio, os custos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, até à meia-noite (UTC) de 19 de maio.

### <a name="rerated-data"></a>Dados reclassificados

Quer utilize as [APIs do Cost Management](../index.yml), o Power BI ou o portal do Azure para obter dados, conte com a reclassificação dos custos do período de faturação atual e, consequentemente, com a consequente alteração, até que a faturação seja fechada.

## <a name="usage-data-update-frequency-varies"></a>A frequência de atualização dos dados de utiliza é variável

A disponibilidade dos dados da utilização incorrida no Cost Management depende de um par de fatores:

- Com que frequência os serviços do Azure (como Armazenamento, Computação, CDN e SQL) emitem a utilização.
- A duração do processamento dos dados de utilização através do motor de classificação e dos pipelines de gestão de custos.

Alguns serviços emitem a utilização com maior frequência do que outros. Por isso, poderá ver dados no Cost Management relativos a alguns serviços mais cedo do que noutros serviços que emitem os dados com menor frequência. Normalmente, a utilização dos serviços demora entre 8 e 24 horas a aparecer no Cost Management. Lembre-se de que os dados de um mês ainda aberto são atualizados à medida que incorre em mais utilização, pois as atualizações são cumulativas.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos poderão não corresponder à fatura

Os dados históricos de ofertas baseadas em crédito e de pré-pagamento podem não corresponder à sua fatura. Algumas ofertas de Pay As You Go, MSDN e Visual Studio do Azure podem ter créditos do Azure e pagamentos avançados aplicados à fatura. Contudo, os dados históricos mostrados no Cost Management baseiam-se apenas nos custos de consumo estimados. Os dados históricos do Cost Management não incluem pagamentos nem créditos. Por esta razão, os dados históricos mostrados para as seguintes ofertas podem não corresponder exatamente à sua fatura.

- Microsoft Azure for Students (MS-AZR-0170P)
- Azure no Licenciamento Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Avaliação Gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Consulte também

- Se ainda não tiver concluído o primeiro início rápido do Cost Management, leia-o em [Começar a analisar os custos](../../cost-management/quick-acm-cost-analysis.md).
