---
title: Aplicar atualizações do sistema no Centro de Segurança Do Azure Microsoft Docs
description: Este documento mostra-lhe como implementar as recomendações do Azure Security Center **Aplicar atualizações** do sistema e **reiniciar após atualizações**do sistema .
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: memildin
ms.openlocfilehash: 3f27753b0775f44cbdf9d4c478a19e423b8e1f19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604552"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar atualizações do sistema no Centro de Segurança Azure
O Centro de Segurança do Azure monitoriza diariamente as máquinas virtuais (VMs) Windows e Linux e os computadores quanto a atualizações do sistema operativo em falta. O Centro de Segurança obtém uma lista das atualizações críticas e de segurança disponíveis a partir do Windows Update ou dos Windows Server Update Services (WSUS), dependendo do serviço que está configurado no computador Windows. O Centro de Segurança também verifica se existem atualizações mais recentes em sistemas Linux. Se uma atualização do sistema estiver em falta na VM ou no computador, o Centro de Segurança recomendará que aplique as atualizações do sistema.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Aplicar atualizações do sistema é apresentado como uma recomendação no Centro de Segurança. Se o seu VM ou computador estiver a faltar uma atualização do sistema, esta recomendação será apresentada de acordo com **recomendações** e no âmbito **da Compute**.  A seleção da recomendação abre o painel de atualizações do **sistema Apply.**

Neste exemplo, usaremos a **Compute**.

1. **Selecione Compute** sob o menu principal do Centro de Segurança.

   ![Selecione Compute][1]

2. Em **Compute, selecione** **as atualizações**do sistema Missing . O painel de **atualizações** do sistema Apply abre-se.

   ![Aplicar o painel de atualizações do sistema][2]

   A parte superior do painel fornece:

    - O número total de VMs Windows e Linux e computadores em falta de atualizações do sistema.
    - O número total de atualizações críticas em falta nos seus VMs e computadores.
    - O número total de atualizações de segurança em falta nos seus VMs e computadores.

   A parte inferior do painel de instrumentos lista todas as atualizações em falta nos seus VMs e computadores e a gravidade da atualização em falta.  A lista inclui:

    - NOME: o nome da atualização em falta.
    - N.º DE VMs & COMPUTADORES: Número total de VMs e computadores que estão a perder esta atualização.
    - ESTADO: o estado atual da recomendação:

      - Aberto: A recomendação ainda não foi abordada.
      - Em curso: a recomendação está a ser aplicada a esses recursos e não tem de tomar qualquer medida.
      - Resolvido: a recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).

    - GRAVIDADE: descreve a gravidade dessa recomendação específica:

      - Elevada: existe uma vulnerabilidade num recurso significativo (aplicação, máquina virtual ou grupo de segurança de rede) e necessita de atenção.
      - Média: são precisos passos não críticos ou adicionais para concluir um processo ou eliminar uma vulnerabilidade.
      - Baixa: uma vulnerabilidade deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).

3. Selecione uma atualização em falta na lista para visualizar detalhes.

   ![Falta de atualização de segurança][3]

4. Selecione o ícone **'Procurar'** na fita superior.  Uma consulta de pesquisa de registos do Monitor Azure abre-se filtrada para os computadores que não têm a atualização.

   ![Pesquisa de registos do Monitor Azure][4]

5. Selecione um computador da lista para obter mais informações. Outro resultado de pesquisa abre com informação filtrada apenas para esse computador.

    ![Pesquisa de registos do Monitor Azure][5]

## <a name="next-steps"></a>Passos seguintes
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center -- Saiba como as recomendações ajudam a proteger os seus recursos Azure.
* [Monitorização](security-center-monitoring.md) de saúde de segurança no Centro de Segurança Azure.- Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Gerir e responder a alertas](security-center-managing-and-responding-alerts.md) de segurança no Centro de Segurança Azure -- Aprenda a gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) -- Encontre posts de blog sobre segurança e conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
