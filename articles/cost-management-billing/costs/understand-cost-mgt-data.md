---
title: Compreender os dados do Azure Cost Management
description: Este artigo ajuda-o a compreender melhor os dados que estão incluídos no Azure Cost Management e com que frequência são processados, recolhidos, mostrados e fechados.
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: 97ae2ba26818bbc306da71af814d9b4f95858b6a
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97032580"
---
# <a name="understand-cost-management-data"></a>Compreender os dados do Cost Management

Este artigo ajuda-o a compreender melhor os dados de custos e utilização do Azure que estão incluídos no Azure Cost Management. Aqui explicamos-lhe com que frequência os dados são processados, recolhidos, mostrados e fechados. A sua faturação corresponde a uma utilização mensal do Azure. Apesar de os períodos de faturação serem períodos mensais, as datas de início e fim do ciclo variam consoante o tipo de subscrição. A frequência com que o Cost Management recebe os dados de utilização varia com base em diferentes fatores. Esses fatores incluem o tempo de duração do processamento de dados e a frequência com que os serviços do Azure emitem a utilização para o sistema de faturação.

O Cost Management inclui toda a utilização e todas as compras, incluindo reservas e ofertas de terceiros para contas de Contrato Enterprise (EA). As contas e as subscrições individuais do Contrato de Cliente Microsoft com tarifas Pay As You Go incluem apenas a utilização dos serviços do Azure e do Marketplace. O suporte e os outros custos não estão incluídos. Os custos são estimados até ser gerada uma fatura e não influenciam os créditos.

Se tiver uma subscrição nova, não pode utilizar as funcionalidades do Cost Management imediatamente. Poderá demorar até 48 horas até poder utilizar todas as funcionalidades do Cost Management.

## <a name="supported-microsoft-azure-offers"></a>Ofertas suportadas do Microsoft Azure

As seguintes informações mostram as [ofertas atualmente suportadas pelo Microsoft Azure](https://azure.microsoft.com/support/legal/offer-details/) no Azure Cost Management. Uma oferta do Azure é o tipo de subscrição do Azure que possui. Os dados estão disponíveis no Cost Management a partir da data **Dados disponíveis a partir de**. Se uma subscrição alterar as ofertas, os custos anteriores à data de alteração da oferta deixam de estar disponíveis.

| **Categoria**  | **Nome da oferta** | **ID de quota** | **Número de oferta** | **Dados disponíveis a partir de** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | maio de 2014<sup>1</sup> |
| **Contrato Enterprise (EA)** | Enterprise Dev/Test                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | maio de 2014<sup>1</sup> |
| **Contrato Enterprise (EA)** | Microsoft Azure Enterprise | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | maio de 2014<sup>1</sup> |
| **Contrato de Cliente da Microsoft** | Plano do Microsoft Azure | EnterpriseAgreement_2014-09-01 | N/D | março de 2019<sup>3</sup> |
| **Contrato de Cliente da Microsoft** | Plano do Microsoft Azure para Dev/Test | MSDNDevTest_2014-09-01 | N/D | março de 2019<sup>3</sup> |
| **Contrato de Cliente Microsoft suportado por parceiros** | Plano do Microsoft Azure | CSP_2015-05-01, CSP_MG_2017-12-01 e CSPDEVTEST_2018-05-01<br><br>O ID de quota é reutilizado para o Contrato de Cliente Microsoft e subscrições de CSP de legado. Atualmente, apenas são suportadas as subscrições do Contrato de Cliente Microsoft. | N/D | Outubro de 2019 |
| **Microsoft Developer Network (MSDN)** | MSDN Platforms<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | "Pay As You Go"                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Pay As You Go Dev/Test         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Microsoft Partner Network      | MPN_2014-09-01 | MS-AZR-0025P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Avaliação Gratuita<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Azure no Licenciamento Open<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2 de outubro de 2018<sup>2</sup> |
| **Pay As You Go** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P, MS-AZR-0122P – MS-AZR-0125P, MS-AZR-0128P – MS-AZR-0130P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise – MPN<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Professional<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Test Professional<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2 de outubro de 2018<sup>2</sup> |
| **Visual Studio** | Visual Studio Enterprise: BizSpark<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2 de outubro de 2018<sup>2</sup> |

_<sup>**1**</sup> Para dados anteriores a maio de 2014, visite o [portal do Azure Enterprise](https://ea.azure.com)._

_<sup>**2**</sup> Para dados anteriores a 2 de outubro de 2018, visite o [Centro de Contas do Azure](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Os contratos de Clientes Microsoft começaram em março de 2019 e não têm quaisquer dados históricos anteriores a este ponto._

_<sup>**4**</sup> Os dados históricos de subscrições baseadas em crédito e de pré-pagamento podem não corresponder à sua fatura. Veja [Os dados históricos podem não corresponder à fatura](#historical-data-might-not-match-invoice) abaixo._

As seguintes ofertas ainda não são suportadas:

| Categoria  | **Nome da oferta** | **ID de quota** | **Número de oferta** |
| --- | --- | --- | --- |
| **Azure Alemanha** | Pay As You Go do Azure Alemanha | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Pay As You Go do Azure Government | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **Fornecedor de Soluções Cloud (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **Fornecedor de Soluções Cloud (CSP)** | CSP do Azure Government                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **Fornecedor de Soluções Cloud (CSP)** | Azure Alemanha em CSP para a Microsoft Cloud Alemanha   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **Pay As You Go**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **Pay As You Go** | Microsoft Azure for Students<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **Pay As You Go**                 | Microsoft Azure Sponsorship | Sponsored_2016-01-01 | MS-AZR-0036P |
| **Planos de Suporte** | Suporte Padrão                    | Default_2014-09-01 | MS-AZR-0041P |
| **Planos de Suporte** | Suporte Direto Profissional         | Default_2014-09-01 | MS-AZR-0042P |
| **Planos de Suporte** | Suporte para Programadores                   | Default_2014-09-01 | MS-AZR-0043P |
| **Planos de Suporte** | Plano de suporte da Alemanha                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **Planos de Suporte** | Suporte Padrão do Azure Government   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **Planos de Suporte** | Suporte Pro-Direct do Azure Government | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **Planos de Suporte** | Suporte para Programadores do Azure Government  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

### <a name="free-trial-to-pay-as-you-go-upgrade"></a>Atualização de avaliação gratuita para pay as you go

Para obter mais informações sobre a disponibilidade dos serviços de avaliação gratuita depois de atualizar da Avaliação Gratuita para os preços pay as you go, veja as [FAQ da conta gratuita do Azure](https://azure.microsoft.com/free/free-account-faq/).

### <a name="determine-your-offer-type"></a>Determinar o tipo de oferta

Se não vir os dados de uma subscrição e quiser determinar se a sua subscrição se enquadra nas ofertas suportadas, poderá validar o suporte da sua subscrição. Para validar o suporte de uma subscrição do Azure, inicie sessão no portal do Azure. Em seguida, selecione **Todos os Serviços** no menu à esquerda. Na lista de serviços, selecione **Subscrições**. No menu da lista de subscrições, selecione a subscrição pretende verificar. A sua subscrição é mostrada no separador Descrição geral e pode ver a **Oferta** e o **ID de Oferta**. A imagem seguinte mostra um exemplo.

![Exemplo do separador Descrição geral da Subscrição que mostra a Oferta e o ID de Oferta](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>Custos incluídos no Cost Management

As seguintes tabelas mostram os dados que são incluídos ou não no Cost Management. Todos os custos são estimados até que seja gerada uma fatura. Os custos mostrados não incluem créditos gratuitos e pré-pagos.

| **Incluídos** | **Não incluídos** |
| --- | --- |
| Utilização do serviço do Azure<sup>5</sup>        | Custos de suporte – para obter mais informações, veja [Termos da fatura explicados](../understand/understand-invoice.md). |
| Utilização das ofertas do Marketplace<sup>6</sup> | Impostos – para obter mais informações, veja [Termos da fatura explicados](../understand/understand-invoice.md). |
| Compras do Marketplace<sup>6</sup>      | Créditos – para obter mais informações, veja [Termos da fatura explicados](../understand/understand-invoice.md). |
| Compras de reservas<sup>7</sup>      |  |
| Amortização de compras de reservas<sup>7</sup>      |  |

_<sup>**5**</sup> A utilização dos serviços do Azure baseia-se na reserva e nos preços negociados._

_<sup>**6**</sup> De momento, as compras do Marketplace não estão disponíveis para as ofertas MSDN e Visual Studio._

_<sup>**7**</sup> De momento, as compras de reservas apenas estão disponíveis para contas de Contrato Enterprise (EA) e Contrato de Cliente Microsoft_

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>Como as etiquetas são utilizadas em dados de custos e utilização

O Azure Cost Management recebe etiquetas como parte de cada registo de utilização submetido pelos serviços individuais. As seguintes restrições aplicam-se a estas etiquetas:

- As etiquetas têm de ser aplicadas diretamente aos recursos e não são implicitamente herdadas do grupo de recursos principal.
- As etiquetas de recursos são suportadas apenas para recursos implementados em grupos de recursos.
- Alguns recursos implementados podem não suportar etiquetas ou não incluir etiquetas nos dados de utilização.
- As etiquetas de recursos só são incluídas nos dados de utilização enquanto a etiqueta é aplicada – as etiquetas não são aplicadas a dados históricos.
- As etiquetas de recursos só estão disponíveis no Cost Management depois de os dados serem atualizados.
- As etiquetas de recursos só estão disponíveis no Cost Management quando o recurso estiver ativo/em execução e a produzir registos de utilização (por exemplo, quando uma VM está desalocada).
- A gestão das etiquetas requer acesso de contribuidor a cada recurso.
- A gestão das políticas de etiquetas requer acesso de proprietário ou de contribuidor de política a um grupo de gestão, subscrição ou grupo de recursos.
    
Se não vir uma etiqueta específica no Cost Management, considere o seguinte:

- A etiqueta foi aplicada diretamente no recurso?
- A etiqueta foi aplicada há mais de 24 horas?
- O tipo de recurso suporta etiquetas? Os seguintes tipos de recursos não suportam etiquetas nos dados de utilização a partir de 1 de dezembro de 2019. Veja [Suporte de etiquetas para recursos do Azure](../../azure-resource-manager/management/tag-support.md) para obter a lista completa do que é suportado.
    - Diretórios do Azure Active Directory B2C
    - Azure Bastion
    - Azure Firewalls
    - Azure NetApp Files
    - Data Factory
    - Databricks
    - Balanceadores de carga
    - Observador de Rede
    - Hubs de Notificação
    - Service Bus
    - Time Series Insights
    
Eis algumas sugestões para trabalhar com etiquetas:

- Planeie com antecedência e defina uma estratégia de identificação que lhe permita discriminar os custos por organização, aplicação, ambiente, etc.
- Utilize o Azure Policy para copiar etiquetas de um grupo de recursos para recursos individuais e impor a sua estratégia de identificação.
- Utilize a API de Etiquetas em conjunto com Query ou UsageDetails para obter todos os custos com base nas etiquetas atuais.


## <a name="cost-and-usage-data-updates-and-retention"></a>Atualizações e retenção de dados de custos e utilização

Os dados de custos e utilização estão normalmente disponíveis em Cost Management + Faturação no portal do Azure e em APIs de suporte dentro de 8 a 24 horas. Tenha em consideração os seguintes pontos ao rever os custos:

- Cada serviço do Azure (como Armazenamento, Computação e SQL) emite a utilização em intervalos diferentes. Pode ver os dados de alguns serviços mais cedo do que outros.
- Os custos estimados para o atual período de faturação são atualizados seis vezes por dia.
- Os custos estimados para o atual período de faturação pode ser alterado à medida que incorre em mais utilização.
- Cada atualização é cumulativa e inclui todos os itens de linha e informações da atualização anterior.
- O Azure finaliza ou _fecha_ o período de faturação atual até 72 horas (três dias do calendário) após a terminação do período de faturação.

Os seguintes exemplos ilustram a forma como os períodos de faturação podem terminar:

* Subscrições de Contrato Enterprise (EA) – se o mês de faturação terminar em 31 de março, os custos estimados serão atualizados até 72 horas depois. Neste exemplo, até à meia-noite (UTC) de 4 de abril.
* Subscrições Pay As You Go – se o mês de faturação terminar em 15 de maio, os custos estimados poderão ser atualizados até 72 horas depois. Neste exemplo, até à meia-noite (UTC) de 19 de maio.

Depois de os dados de custos e utilização ficarem disponíveis em Cost Management + Faturação, serão mantidos durante, pelo menos, 7 anos.

### <a name="rerated-data"></a>Dados reclassificados

Quer utilize as APIs do Cost Management, o Power BI ou o portal do Azure para obter dados, conte com a reclassificação dos custos do período de faturação atual e, consequentemente, com a consequente alteração, até que a faturação seja fechada.

## <a name="cost-rounding"></a>Arredondamento dos custos

Os custos apresentados no Cost Management são arredondados. Os custos devolvidos pela API Query não são arredondados. Por exemplo:

- Análise de custos no portal do Azure - os custos são arredondados de acordo com as regras de arredondamento normais, ou seja, os valores iguais ou superiores a 0,5 são arredondados para cima, caso contrário, são arredondados para baixo. O arredondamento só é aplicado quando são apresentados valores. O arredondamento não se verifica durante o processamento de dados e a agregação. Por exemplo, a análise de custos agrega os custos da seguinte forma:
  -    Custo 1: 0,004 €
  - Custo 2: 0,004 €
  -    Custo agregado produzido: 0,004 + 0,004 = 0,008. O custo apresentado é 0,01 €.
- API Query - os custos são mostrados com oito casas decimais e não são arredondados.

## <a name="historical-data-might-not-match-invoice"></a>Os dados históricos poderão não corresponder à fatura

Os dados históricos de ofertas baseadas em crédito e de pré-pagamento podem não corresponder à sua fatura. Algumas ofertas de Pay As You Go, MSDN e Visual Studio do Azure podem ter créditos do Azure e pagamentos avançados aplicados à fatura. Contudo, os dados históricos mostrados no Cost Management baseiam-se apenas nos custos de consumo estimados. Os dados históricos do Cost Management não incluem pagamentos nem créditos. Por esta razão, os dados históricos mostrados para as seguintes ofertas podem não corresponder exatamente à sua fatura.

- Microsoft Azure for Students (MS-AZR-0170P)
- Azure no Licenciamento Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Avaliação Gratuita (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>Consulte também

- Se ainda não tiver concluído o primeiro início rápido do Cost Management, leia-o em [Começar a analisar os custos](./quick-acm-cost-analysis.md).