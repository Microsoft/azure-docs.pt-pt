---
title: Aplicar atualizações do sistema no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar as recomendações do Centro de segurança do Azure **aplicar atualizações do sistema** e **reiniciar após atualizações do sistema**.
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
ms.author: rkarlin
ms.openlocfilehash: 9c2d6c48b9bf51062181886c8a9ef789b1c2b359
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57240835"
---
# <a name="apply-system-updates-in-azure-security-center"></a>Aplicar atualizações do sistema no Centro de segurança do Azure
Centro de segurança do Azure monitoriza diárias máquinas virtuais (VMs) Windows e Linux e computadores para atualizações de sistema operativo em falta. Centro de segurança obtém uma lista de atualizações críticas e de segurança disponíveis do Windows Update ou Windows Server Update Services (WSUS), dependendo de qual o serviço está configurado num computador Windows. Centro de segurança também verifica as atualizações mais recentes nos sistemas Linux. Se a sua VM ou o computador está em falta uma atualização do sistema, o Centro de segurança recomendará que aplique as atualizações do sistema.

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Aplicar o sistema atualizações é apresentada como uma recomendação no Centro de segurança. Se seu computador ou VM está em falta uma atualização do sistema, será apresentada esta recomendação sob **recomendações** e, em **computação**.  Selecionar a recomendação abre o **aplicar atualizações do sistema** dashboard.

Neste exemplo, utilizamos **computação**.

1. Selecione **computação** no menu principal do Centro de segurança.

   ![Selecione computação][1]

2. Sob **computação**, selecione **em falta atualizações do sistema**. O **aplicar atualizações do sistema** dashboard abre.

   ![Aplicar o dashboard de atualizações de sistema][2]

   Fornece a parte superior do dashboard:

    - O número total de Windows e VMs do Linux e computadores em falta atualizações do sistema.
    - O número total de atualizações críticas em falta em suas VMs e computadores.
    - O número total de atualizações de segurança em falta em suas VMs e computadores.

  Na parte inferior do dashboard apresenta uma lista de todas as atualizações em falta em suas VMs e computadores e a gravidade da atualização em falta.  A lista inclui:

    - NOME: Nome da atualização em falta.
    - NÃO. DE VMs e computadores: Número total de VMs e computadores que estão em falta esta atualização.
    - ESTADO: O estado atual da Recomendação:

      - Abrir: A recomendação ainda não foi tratada.
      - Em curso: A recomendação está atualmente a ser aplicada a esses recursos, e é necessária nenhuma ação por si.
      - Resolvidos: A recomendação já foi concluída. (Quando o problema for resolvido, a entrada fica a cinzento).

    - GRAVIDADE: Descreve a gravidade dessa recomendação específica:

      - Alta: Uma vulnerabilidade existe com um recurso significativo (aplicação, máquina virtual ou grupo de segurança de rede) e necessita de atenção.
      - Médio: Passos não críticos ou adicionais são necessários para concluir um processo ou eliminar uma vulnerabilidade.
      - Baixo: Uma vulnerabilidade deve ser tratada, mas não necessita de atenção imediata. (Por predefinição, as recomendações baixas não são apresentadas, mas pode filtrar por recomendações baixas se pretender visualizá-las).

3. Selecione uma atualização em falta na lista para ver os detalhes.

   ![Atualização de segurança em falta][3]

4. Selecione o **pesquisa** ícone na fita superior.  Uma consulta de pesquisa de registos do Azure Monitor abre filtrada para os computadores em falta a atualização.

   ![Pesquisa de registos de Monitor do Azure][4]

5. Selecione um computador a partir da lista para obter mais informações. Outro resultado da pesquisa é aberto com informações filtradas apenas para esse computador.

    ![Pesquisa de registos de Monitor do Azure][5]

## <a name="reboot-after-system-updates"></a>Reiniciar após atualizações do sistema
1. Retorno para o **recomendações** painel. Uma nova entrada foi gerada depois aplicado atualizações do sistema, chamadas **reiniciar após atualizações do sistema**. Esta entrada permite-lhe saber que terá de reiniciar a VM para concluir o processo de aplicar atualizações do sistema.

   ![Reiniciar após atualizações do sistema][6]
2. Selecione **reiniciar após atualizações do sistema**. Esta ação abre **um reinício está pendente para concluir as atualizações do sistema** processo de atualizações de painel a apresentar uma lista de VMs que é necessário reiniciar para concluir o aplicar o sistema.

   ![Reinício pendente][7]

Reinicie a VM do Azure para concluir o processo.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e de conformidade.

<!--Image references-->
[1]: ./media/security-center-apply-system-updates/missing-system-updates.png
[2]:./media/security-center-apply-system-updates/apply-system-updates.png
[3]: ./media/security-center-apply-system-updates/detail-on-missing-update.png
[4]: ./media/security-center-apply-system-updates/log-search.png
[5]: ./media/security-center-apply-system-updates/search-details.png
[6]: ./media/security-center-apply-system-updates/reboot-after-system-updates.png
[7]: ./media/security-center-apply-system-updates/restart-pending.png
