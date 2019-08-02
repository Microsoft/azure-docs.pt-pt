---
title: Pesquisa da central de segurança do Azure | Microsoft Docs
description: Saiba como a central de segurança do Azure usa a pesquisa de Azure Monitor logs para recuperar e analisar seus dados de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 45b9756b-6449-49ec-950b-5ed1e7c56daa
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2017
ms.author: rkarlin
ms.openlocfilehash: c88e2025444aa2fd83e05fdff97ea640ceefaa04
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68662919"
---
# <a name="azure-security-center-search-retired"></a>Pesquisa da central de segurança do Azure (desativada)

> [!NOTE]
> O painel de pesquisa da central de segurança foi desativado em 31 de julho de 2019. Para obter mais informações e serviços alternativos, consulte [aposentadoria dos recursos da central de segurança (julho de 2019)](security-center-features-retirement-july2019.md#menu_search).

A central de segurança do Azure usa a [pesquisa de Azure monitor logs](../log-analytics/log-analytics-log-searches.md) para recuperar e analisar seus dados de segurança. Os logs de Azure Monitor incluem uma linguagem de consulta para recuperar e consolidar dados rapidamente. Na central de segurança, você pode aproveitar a pesquisa de Azure Monitor logs para construir consultas e analisar os dados coletados.

A pesquisa está disponível tanto na camada gratuita quanto na camada Standard da central de segurança.  Os dados disponíveis em suas pesquisas de log dependem do nível de camada aplicado ao seu espaço de trabalho.  Consulte a página de [preços](../security-center/security-center-pricing.md) da central de segurança para obter mais informações.


> [!NOTE]
> A central de segurança não salva dados de segurança para um espaço de trabalho na camada gratuita. Você pode enviar uma variedade de logs para um espaço de trabalho na camada gratuita e Pesquisar esses dados, mas os resultados da pesquisa não incluem dados da central de segurança. A central de segurança salva apenas os dados em um espaço de trabalho na camada Standard.
>
>

## <a name="access-search"></a>Pesquisa de acesso
1. No menu principal da central de segurança, selecione **Pesquisar**.

   ![Selecionar pesquisa de logs][1]

2. A central de segurança lista todos os espaços de trabalho em suas assinaturas do Azure. Selecione uma área de trabalho. (Se você tiver apenas um espaço de trabalho, esse seletor de espaço de trabalho não será exibido.)

   ![Selecione uma área de trabalho][2]

3. A **pesquisa de logs** é aberta. Para consultar mais dados no espaço de trabalho selecionado, insira este exemplo de consulta:

   SecurityEvent | em que EventID = = 4625 | resumir contagem () por TargetAccount

   Resultado mostra todas as contas que falharam ao entrar (evento 4625).

   ![Resultados da pesquisa][3]

Consulte [linguagem de consulta do Kusto](../log-analytics/log-analytics-search-reference.md) para obter mais informações sobre como consultar dados no espaço de trabalho selecionado.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, você aprendeu a acessar a pesquisa na central de segurança. A central de segurança usa a pesquisa de logs de Azure Monitor. Para saber mais sobre a pesquisa de logs de Azure Monitor, consulte:

- [O que é Azure Monitor logs?](../log-analytics/log-analytics-overview.md) – Visão geral dos logs de Azure Monitor
- [Noções básicas sobre pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-search-new.md) – descreve como as pesquisas de log são usadas em logs de Azure monitor e fornece conceitos que devem ser compreendidos antes de criar uma pesquisa de logs
- [Localizar dados usando pesquisas de log em logs de Azure monitor](../log-analytics/log-analytics-log-searches.md) – tutorial sobre como usar a pesquisa de logs
- [Referência de pesquisa do Kusto](../log-analytics/log-analytics-search-reference.md) – descreve a linguagem de consulta em logs de Azure monitor

Para saber mais sobre a central de segurança, consulte:

- [Visão geral da central de segurança](security-center-intro.md) – descreve os principais recursos da central de segurança

<!--Image references-->
[1]: ./media/security-center-search/search.png
[2]: ./media/security-center-search/workspace-selector.png
[3]: ./media/security-center-search/log-search.png
