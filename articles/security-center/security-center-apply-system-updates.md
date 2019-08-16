---
title: Aplicar atualizações do sistema na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como implementar as recomendações da central de segurança do Azure **aplicar atualizações do sistema** e reinicializar **após as atualizações do sistema**.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e5bd7f55-38fd-4ebb-84ab-32bd60e9fa7a
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: v-mohabe
ms.openlocfilehash: 7f9c5b702c0da8de4476508114be911a9d74f78a
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69531462"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar atualizações do sistema na central de segurança do Azure
A central de segurança do Azure monitora as VMs (máquinas virtuais) do Windows e Linux e os computadores com atualizações de sistema operacional ausentes. A central de segurança recupera uma lista de atualizações críticas e de segurança disponíveis do Windows Update ou do Windows Server Update Services (WSUS), dependendo de qual serviço está configurado em um computador com Windows. A central de segurança também verifica as atualizações mais recentes em sistemas Linux. Se sua VM ou computador não tiver uma atualização do sistema, a central de segurança recomendará que você aplique as atualizações do sistema.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
A aplicação de atualizações do sistema é apresentada como uma recomendação na central de segurança. Se sua VM ou computador não tiver uma atualização do sistema, essa recomendação será exibida em **recomendações** e em **computação**.  A seleção da recomendação abre o painel **aplicar atualizações do sistema** .

Neste exemplo, usaremos a **computação**.

1. Selecione **computação** no menu principal da central de segurança.

   ![Selecionar computação][1]

2. Em **computação**, selecione **atualizações do sistema ausentes**. O painel **aplicar atualizações do sistema** é aberto.

   ![Aplicar painel de atualizações do sistema][2]

   A parte superior do painel fornece:

    - O número total de máquinas virtuais do Windows e Linux e computadores com atualizações do sistema ausentes.
    - O número total de atualizações críticas ausentes em suas VMs e computadores.
    - O número total de atualizações de segurança ausentes em suas VMs e computadores.

   A parte inferior do painel lista todas as atualizações ausentes em suas VMs e computadores e a severidade da atualização ausente.  A lista inclui:

    - NOMES Nome da atualização ausente.
    - NÃO. DE VMs & computadores: Número total de VMs e computadores sem esta atualização.
    - STATUS O estado atual da recomendação:

      - Abrir A recomendação ainda não foi resolvida.
      - Em andamento: A recomendação está sendo aplicada atualmente a esses recursos e nenhuma ação é exigida por você.
      - Resolvido A recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).

    - SEVERITY Descreve a severidade dessa recomendação específica:

      - Elevada Existe uma vulnerabilidade com um recurso significativo (aplicativo, máquina virtual ou grupo de segurança de rede) e requer atenção.
      - Médio Etapas não críticas ou adicionais são necessárias para concluir um processo ou eliminar uma vulnerabilidade.
      - Pequena Uma vulnerabilidade deve ser resolvida, mas não requer atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).

3. Selecione uma atualização ausente na lista para exibir detalhes.

   ![Atualização de segurança ausente][3]

4. Selecione o ícone de **pesquisa** na faixa de opções superior.  Uma consulta de pesquisa de logs de Azure Monitor é aberta e filtrada para os computadores que não têm a atualização.

   ![Pesquisa de logs de Azure Monitor][4]

5. Selecione um computador na lista para obter mais informações. Outro resultado da pesquisa é aberto com informações filtradas somente para esse computador.

    ![Pesquisa de logs de Azure Monitor][5]

## <a name="next-steps"></a>Passos Seguintes
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) --encontre postagens no blog sobre a segurança e a conformidade do Azure.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
