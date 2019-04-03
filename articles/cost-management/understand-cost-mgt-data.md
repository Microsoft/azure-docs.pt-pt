---
title: Compreender os dados do Azure Cost Management | Documentos da Microsoft
description: Este artigo ajuda-o a compreender melhor os dados que estão incluídos no Azure Cost Management e a frequência com que é processada, recolhidos, mostrado e fechados.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 04/02/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: ca0d77ba2d1968d49e8ac556d42137cdc4c81e53
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879442"
---
# <a name="understand-cost-management-data"></a>Compreender os dados do Cost Management

Este artigo ajuda-o a compreender melhor os dados que estão incluídos no Azure Cost Management. E explica a frequência com que dados são processados, recolhidos, mostrado e fechados. A faturação para utilização do Azure mensal. No entanto, o seu tipo de subscrição do Azure determina quando o seu mês de faturação termina. A frequência com que o Cost Management recebe a utilização de dados variam com base em diferentes fatores. Esses fatores incluem o tempo que demora a processar os dados e a frequência com que os serviços do Azure emitem utilização ao sistema de faturação.

## <a name="supported-microsoft-offers"></a>Suporte oferecidos pela Microsoft

As seguintes informações são apresentadas suportado atualmente [o Microsoft Azure oferece](https://azure.microsoft.com/support/legal/offer-details/) no Azure Cost Management.  Uma oferta do Azure é o tipo de subscrição do Azure que tem.

| Categoria  | **Nome da oferta** | **ID de quota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | [Pay As You Go Azure Alemanha](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **Contrato Enterprise (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **Contrato Enterprise (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Microsoft Developer Network (MSDN)** | [Plataformas da MSDN](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **"Pay As You Go"** | ["Pay As You Go"](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **"Pay As You Go"** | [Pay As You Go Dev/Test](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **"Pay As You Go"** | [Microsoft Partner Network](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **"Pay As You Go"** | [Avaliação gratuita](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **"Pay As You Go"** | [Azure no Licenciamento Open](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **"Pay As You Go"** | [Azure para Estudantes](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **"Pay As You Go"** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P - MS-AZR-0125P, MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

A tabela seguinte mostra as ofertas não suportadas.

| Categoria  | **Nome da oferta** | **ID de quota** | **Número da oferta** |
| --- | --- | --- | --- |
| **Fornecedor de Soluções Cloud (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Fornecedor de Soluções Cloud (CSP)** | O Azure Government CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Fornecedor de Soluções Cloud (CSP)** | Azure Alemanha no CSP para a Microsoft Cloud Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **"Pay As You Go"**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **"Pay As You Go"**                 | [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de Suporte** | Suporte Padrão                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de Suporte** | Suporte direto profissional         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de Suporte** | Suporte para programadores                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de Suporte** | Plano de suporte da Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de Suporte** | Suporte padrão do Azure Government   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de Suporte** | Suporte direto profissional para o Azure Government | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de Suporte** | Suporte para programadores do Azure Government  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

Para os clientes com as categorias da oferta pay as you go, o MSDN e o Visual Studio, os dados estão disponíveis no Cost Management a partir de 10/02/2018. Para aceder aos dados para a sua subscrição antes de 10/02/2018, pode utilizar o [Centro de contas do Azure](https://account.azure.com/subscriptions) para transferir a sua utilização detalhes num arquivo CSV ou pode utilizar os [API de detalhes de utilização](/rest/api/consumption/usagedetails).

## <a name="determine-your-offer-type"></a>Determinar o tipo de oferta
Se não vir dados para uma subscrição e pretende determinar se a sua subscrição está sob as ofertas de suporte, pode validar que a sua subscrição é suportada. Para validar que uma subscrição do Azure é suportada, início de sessão para o [portal do Azure](https://portal.azure.com). Em seguida, selecione **todos os serviços** no painel de menu à esquerda. Na lista de serviços, selecione **subscrições**. No menu de lista de subscrição, clique na subscrição que pretende verificar. A sua subscrição é apresentada no separador de descrição geral e pode ver o **oferecem** e **ID da oferta**. A imagem seguinte mostra um exemplo.

![Exemplo da guia visão geral de subscrição que mostra a oferta e o ID da oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Cost Management

As tabelas seguintes mostram os dados que incluiu ou não está no Cost Management. Todos os custos são estimados até que uma nota fiscal é gerada. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

**Dados de utilização e custo**

| **Incluída** | **Não incluídos** |
| --- | --- |
| Utilização do serviço do Azure<sup>1</sup> | Reserva de compras – para obter mais informações, consulte [APIs para a automatização do Azure reserva](../billing/billing-reservation-apis.md). |
| Utilização da oferta do Marketplace | As compras no Marketplace – para obter mais informações, consulte [encargos de serviços de terceiros](../billing/billing-understand-your-azure-marketplace-charges.md). |
|   | Suporte de custos - para obter mais informações, consulte [termos explicados de nota fiscal](../billing/billing-understand-your-invoice.md). |
|   | Impostos - para obter mais informações, consulte [termos explicados de nota fiscal](../billing/billing-understand-your-invoice.md). |
|   | Créditos - para obter mais informações, consulte [termos explicados de nota fiscal](../billing/billing-understand-your-invoice.md). |

<sup>1</sup> utilização do serviço do azure é baseada em reserva e negociado os preços.

**Metadados**

| **Incluída** | **Não incluídos** |
| --- | --- |
| Os sinalizadores de recurso<sup>2</sup> | sinalizadores de grupo de recursos |

<sup>2</sup> etiquetas de recursos são aplicadas conforme a utilização é emitida a partir de cada serviço e não estão disponíveis de forma retroativa a histórico de utilização.

## <a name="rated-usage-data-refresh-schedule"></a>Agenda de atualização de dados de utilização mais votados

Dados de utilização e custos estão disponíveis na gestão de custos + faturação no portal do Azure e [que suportam APIs](index.yml). Tenha os seguintes pontos em mente ao analisar os custos:

- Estimativa de custos para o período de faturação atual é atualizada seis vezes por dia.
- Pode alterar a estimativa de custos para o período de faturação atual como pode implicar a utilização de mais.
- Cada atualização é cumulativa e inclui todos os itens de linha e as informações da atualização anterior.
- Finaliza do Azure ou _fecha_ o período de faturação atual até 72 horas (três dias de calendário) depois de terminar o período de faturação.

Os exemplos seguintes mostram como períodos de faturação foi possível terminar.

Subscrições de contrato Enterprise (EA) – se o mês de faturação termina a 31 de Março, estimado custos são atualizados até 72 horas mais tarde. Neste exemplo, por meia-noite (UTC) 4 de Abril.

Subscrições pay as you go – se o mês de faturação termina a 15 de Maio, em seguida, a estimativa de custos poderá atualizado até 72 horas mais tarde. Neste exemplo, por meia-noite (UTC) 19 de Maio.

### <a name="rerated-data"></a>Dados rerated

Se utilizar o [APIs de gestão de custos](https://aka.ms/costmgmt/docs), PowerBI ou do portal do Azure para obter dados, esperar que as taxas do período de faturação atual para obter novamente classificados e, consequentemente, alterar, até que a nota fiscal seja fechada.

## <a name="usage-data-update-frequency-varies"></a>Varia de frequência de atualização de dados de utilização

A disponibilidade dos seus dados de utilização incorrida na gestão de custos depende de dois fatores, incluindo:

- A frequência com que os serviços do Azure (por exemplo, armazenamento, computação, CDN e SQL) emitem utilização.
- O tempo necessário para processar os dados de utilização por meio do mecanismo de classificação e pipelines de gestão de custos.

Alguns serviços emitem utilização com mais frequência do que outras. Por isso, poderá ver dados de gestão de custos para alguns serviços mais cedo do que outros serviços que emitem dados menos frequentemente. Normalmente, a utilização para os serviços demora 8 a 24 horas a aparecer no Cost Management. Tenha em atenção que os dados para um mês de abertura atualizado à medida que pode implicar uma utilização mais uma vez que as atualizações são cumulativas.

## <a name="see-also"></a>Consulte também

- Se ainda não tiver concluído o primeira guia de introdução do Cost Management, leia-a em [começar a analisar os custos](quick-acm-cost-analysis.md).
