---
title: Remediar configurações de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como implementar a recomendação do Centro de segurança do Azure, "Remediar configurações de segurança".
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 991d41f5-1d17-468d-a66d-83ec1308ab79
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: f3764e5f4a5d6bbc3d6988321a2d7648012825ae
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57242246"
---
# <a name="remediate-security-configurations-in-azure-security-center"></a>Remediar configurações de segurança no Centro de segurança do Azure
Centro de segurança do Azure diariamente analisa o sistema operativo (SO) das suas máquinas virtuais (VMs) e a computadores para uma configuração que poderia fazer com que as VMs e computadores mais vulneráveis a ataques. Centro de segurança recomenda que resolver vulnerabilidades quando a configuração do SO não coincide com as regras de configuração de segurança recomendadas e recomenda alterações de configuração para resolver estas vulnerabilidades.

Para obter mais informações sobre as configurações específicas que estão a ser monitorizadas, consulte a [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Para saber como personalizar as avaliações de configuração de segurança, veja [configurações de segurança de personalizar o sistema operacional no Centro de segurança do Azure (pré-visualização)](security-center-customize-os-security-config.md).

## <a name="implement-the-recommendation"></a>Implementar a recomendação
"Remediar configurações de segurança" é apresentada como uma recomendação no Centro de segurança. A recomendação é apresentada em **recomendações** > **computação e aplicações**.

Este exemplo aborda a recomendação "Remediar configurações de segurança" em **computação e aplicações**.
1. No Centro de segurança, no painel esquerdo, selecione **computação e aplicações**.  
  O **computação e aplicações** é aberta a janela.

   ![Remediar configurações de segurança][1]

2. Selecione **remediar configurações de segurança**.  
  O **configurações de segurança** é aberta a janela.

   ![A janela de "Configurações de segurança"][2]

  A secção superior de dashboard apresenta:

  - **Regras com falhas por gravidade**: O número total de regras que a configuração do SO falhou em suas VMs e computadores, discriminados por gravidade.
  - **Regras falhadas por tipo**: O número total de regras que a configuração do SO falhou em suas VMs e computadores, divididas por tipo.
  - **Regras do Windows com falhas**: O número total de regras com falhas por suas configurações de SO do Windows.
  - **Regras do Linux com falhas**: O número total de regras com falhas por suas configurações de SO Linux.

  Seção inferior do dashboard apresenta uma lista de todas as regras com falhas para as suas VMs e computadores e a gravidade da atualização em falta. A lista contém os seguintes elementos:

  - **CCEID**: O identificador exclusivo CCE para a regra. Centro de segurança utiliza a enumeração de configuração comum (CCE) para atribuir os identificadores exclusivos para as regras de configuração.
  - **Nome**: O nome da regra com falhas.
  - **Tipo de regra**: O *chave de registo*, *política de segurança*, *política de auditoria*, ou *IIS* tipo de regra.
  - **Não. de VMs e computadores**: O número total de VMs e computadores que se aplica a regra com falhas.
  - **Gravidade de regra**: O valor CCE *crítico*, *importante*, ou *aviso*.
  - **estado**: O estado atual da Recomendação:

    - **Abra**: A recomendação ainda não foi tratada.
    - **Em curso**: A recomendação está atualmente a ser aplicada aos recursos e é necessária nenhuma ação por si.
    - **Resolvido**: A recomendação foi aplicada. Quando o problema for resolvido, a entrada fica a cinzento.

3. Para ver os detalhes de uma regra com falhas, selecione-o na lista.

   ![Vista detalhada de uma regra de configuração com falhas][3]

   A vista detalhada apresenta as seguintes informações:

   - **Nome**: O nome da regra.
   - **CCIED**: O identificador exclusivo CCE para a regra.
   - **Versão do SO**: A versão do SO da VM ou do computador.
   - **Gravidade de regra**: O valor CCE *crítico*, *importante*, ou *aviso*.
   - **Total de descrição**: A descrição da regra.
   - **Vulnerabilidade**: A explicação de vulnerabilidade ou risco se a regra não é aplicada.
   - **Impacto potencial**: O impacto comercial quando a regra é aplicada.
   - **Contramedida**: Os passos de remediação.
   - **Valor esperado**: O valor que é esperado quando o Centro de segurança analisa a configuração do SO da VM contra a regra.
   - **Valor real**: O valor que é devolvido após uma análise da sua configuração do SO da VM contra a regra.
   - **Operação de regra**: A operação de regra que é utilizada pelo centro de segurança durante a análise da sua configuração do SO da VM contra a regra.

4. Na parte superior da janela de vista detalhada, selecione **pesquisa**.  
  Pesquisa abre uma lista de áreas de trabalho têm VMs e computadores com o erro de correspondência de configurações de segurança selecionado. A seleção de área de trabalho é mostrada apenas se a regra selecionada aplica-se a várias VMs que estão ligadas a diferentes áreas de trabalho.

   ![Áreas de trabalho listadas][4]

5. Selecione uma área de trabalho.  
  Uma consulta de pesquisa de registos do Azure Monitor abre filtrado para a área de trabalho com o erro de correspondência de configurações de segurança.

   ![Área de trabalho com a Vulnerabilidade do SO][5]

6. Na lista, selecione um computador.  
  Um novo resultado da pesquisa é aberto com informações filtradas apenas para esse computador.

   ![Informações detalhadas sobre o computador selecionado][6]

## <a name="next-steps"></a>Passos Seguintes
Este artigo mostrou como implementar a recomendação do Centro de segurança "Remediar configurações de segurança". Para saber como personalizar as avaliações de configuração de segurança, veja [configurações de segurança de personalizar o sistema operacional no Centro de segurança do Azure (pré-visualização)](security-center-customize-os-security-config.md).

Para rever as configurações específicas que estão a ser monitorizadas, veja [lista de regras de configuração recomendadas](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). Centro de segurança utiliza a enumeração de configuração comum (CCE) para atribuir os identificadores exclusivos para as regras de configuração. Para obter mais informações, vá para o [CCE](https://nvd.nist.gov/cce/index.cfm) site.

Para saber mais sobre o Centro de segurança, consulte os seguintes recursos:

* Para obter uma lista de VMs do Linux e Windows suportados, consulte [plataformas suportadas no Centro de segurança do Azure](security-center-os-coverage.md).
* Para saber como configurar políticas de segurança para as suas subscrições do Azure e grupos de recursos, veja [definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md).
* Para saber como recomendações ajudá-lo a proteger os seus recursos do Azure, veja [recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md).
* Para saber como monitorizar o estado de funcionamento dos seus recursos do Azure, veja [monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md).
* Para saber como gerir e responder a alertas de segurança, veja [gerir e responder a alertas de segurança no Centro de segurança do Azure](security-center-managing-and-responding-alerts.md).
* Para saber como monitorizar o estado de funcionamento das suas soluções de parceiros, veja [monitorizar soluções de parceiros no Centro de segurança do Azure](security-center-partner-solutions.md).
* Para obter respostas a perguntas mais frequentes sobre o serviço, consulte [FAQ do Centro de segurança do Azure](security-center-faq.md).
* Para mensagens de blogue acerca da segurança do Azure e de conformidade, veja [blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/).

<!--Image references-->
[1]: ./media/security-center-remediate-os-vulnerabilities/compute-blade.png
[2]:./media/security-center-remediate-os-vulnerabilities/os-vulnerabilities.png
[3]: ./media/security-center-remediate-os-vulnerabilities/vulnerability-details.png
[4]: ./media/security-center-remediate-os-vulnerabilities/search.png
[5]: ./media/security-center-remediate-os-vulnerabilities/log-search.png
[6]: ./media/security-center-remediate-os-vulnerabilities/search-results.png
