---
title: Entender os dados de gerenciamento de custos do Azure | Microsoft Docs
description: Este artigo ajuda você a entender melhor os dados incluídos no gerenciamento de custos do Azure e com que frequência eles são processados, coletados, mostrados e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 75d414756d8818bd4e29fc0507af73eccf0e0e01
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75993768"
---
# <a name="understand-cost-management-data"></a>Compreender os dados do Cost Management

Este artigo ajuda você a entender melhor os dados de uso e de custo do Azure incluídos no gerenciamento de custos do Azure. Ele explica com que frequência os dados são processados, coletados, mostrados e fechados. Você é cobrado pelo uso mensal do Azure. Embora os ciclos de cobrança sejam períodos mensais, as datas de início e término do ciclo variam por tipo de assinatura. A frequência com que o gerenciamento de custos recebe dados de uso varia de acordo com os diferentes fatores. Esses fatores incluem quanto tempo leva para processar os dados e com que frequência os serviços do Azure emitem o uso para o sistema de cobrança.

O gerenciamento de custos inclui todo o uso e as compras, incluindo reservas e ofertas de terceiros para contas de Enterprise Agreement (EA). As contas de contrato de cliente da Microsoft e as assinaturas individuais com tarifas pagas conforme o uso incluem apenas os serviços do Azure e do Marketplace. O suporte e outros custos não são incluídos. Os custos são estimados até que uma nota fiscal seja gerada e não Fatore os créditos.

## <a name="supported-microsoft-azure-offers"></a>Ofertas de Microsoft Azure com suporte

As informações a seguir mostram as [ofertas de Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) com suporte no momento no gerenciamento de custos do Azure. Uma oferta do Azure é o tipo de assinatura do Azure que você tem. Os dados estão disponíveis no gerenciamento de custos a partir dos **dados disponíveis a partir da** Data. Se uma assinatura alterar as ofertas, os custos antes da data de alteração da oferta não estarão disponíveis.

| **Categoria**  | **Nome da oferta** | **ID da cota** | **Número da oferta** | **Dados disponíveis de** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure governamental Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | Enterprise Programador/Teste                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | Maio de 2014<sup>1</sup> |
| **Enterprise Agreement (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | Maio de 2014<sup>1</sup> |
| **Contrato de Cliente da Microsoft** | [Plano de Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | Março de 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft** | [Plano de Microsoft Azure para desenvolvimento/teste](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | Março de 2019<sup>3</sup> |
| **Contrato com o cliente da Microsoft com suporte dos parceiros** | Plano do Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>A ID da cota é reutilizada para o contrato do cliente da Microsoft e assinaturas CSP herdadas. Atualmente, há suporte apenas para assinaturas do contrato do cliente da Microsoft. | N/A | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | [Plataformas MSDN](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Pay As You Go](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | [Azure via Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
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
| **Azure Government** | Pay As You Go do Azure Government | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Provedor de soluções de nuvem (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Provedor de soluções de nuvem (CSP)** | CSP do Azure governamental                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Provedor de soluções de nuvem (CSP)** | Azure Alemanha no CSP para a Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay As You Go**                 | Azure para Estudantes Iniciantes | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay As You Go** | [Azure para estudantes](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay As You Go**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de Suporte** | Suporte Padrão                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de Suporte** | Suporte a Professional Direct         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de Suporte** | Suporte para programadores                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de Suporte** | Plano de suporte da Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de Suporte** | Suporte Standard do Azure governamental   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de Suporte** | Suporte pro-Direct do Azure governamental | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de Suporte** | Suporte Developer do Azure governamental  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se você não vir os dados de uma assinatura e quiser determinar se sua assinatura está sob as ofertas com suporte, você pode validar que sua assinatura tem suporte. Para validar que uma assinatura do Azure tem suporte, entre no [portal do Azure](https://portal.azure.com). Em seguida, selecione **todos os serviços** no painel de menu à esquerda. Na lista de serviços, selecione **Subscrições**. No menu lista de assinaturas, clique na assinatura que você deseja verificar. Sua assinatura é mostrada na guia Visão geral e você pode ver a **oferta** e a **ID da oferta**. A imagem seguinte mostra um exemplo.

![Exemplo da guia Visão geral da assinatura mostrando a oferta e a ID da oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no gerenciamento de custos

As tabelas a seguir mostram os dados que estão incluídos ou não estão no gerenciamento de custos. Todos os custos são estimados até que uma fatura seja gerada. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

**Dados de uso e custo**

| **Incluídos** | **Não incluído** |
| --- | --- |
| Uso do serviço do Azure<sup>5</sup>        | Encargos de suporte-para obter mais informações, consulte os [termos da nota fiscal explicados](../understand/understand-invoice.md). |
| Uso<sup>6</sup> da oferta do Marketplace | Impostos-para obter mais informações, consulte os [termos da nota fiscal explicados](../understand/understand-invoice.md). |
| Compras do Marketplace<sup>6</sup>      | Créditos-para obter mais informações, consulte os [termos da nota fiscal explicados](../understand/understand-invoice.md). |
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

Os dados de custo e de uso estão disponíveis no gerenciamento de custos + cobrança no portal do Azure e nas [APIs de suporte](../index.yml). Tenha os seguintes pontos em mente ao examinar os custos:

- Os encargos estimados para o período de cobrança atual são atualizados seis vezes por dia.
- Os encargos estimados para o período de cobrança atual podem mudar à medida que você incorre mais uso.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _fecha_ o período de cobrança atual de até 72 horas (três dias de calendário) após o término do período de cobrança.

Os exemplos a seguir ilustram como os períodos de cobrança podem terminar.

Assinaturas do Enterprise Agreement (EA) – se o mês de cobrança terminar em 31 de março, os encargos estimados serão atualizados até 72 horas depois. Neste exemplo, por meia-noite (UTC) 4 de abril.

Assinaturas pagas conforme o uso – se o mês de cobrança terminar em 15 de maio, os encargos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, por meia-noite (UTC) de 19 de maio.

### <a name="rerated-data"></a>Dados reclassificados

Se você usar as [APIs de gerenciamento de custos](../index.yml), Power bi ou o portal do Azure para recuperar dados, espere que os encargos do período de cobrança atual sejam reclassificados e, consequentemente, sejam alterados até que a fatura seja fechada.

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

## <a name="see-also"></a>Ver também

- Se ainda não tiver concluído o primeira guia de introdução do Cost Management, leia-a em [começar a analisar os custos](../../cost-management/quick-acm-cost-analysis.md).
