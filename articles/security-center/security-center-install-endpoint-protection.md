---
title: Gerenciar problemas do Endpoint Protection com a central de segurança do Azure | Microsoft Docs
description: Saiba como gerenciar problemas do Endpoint Protection na central de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: rkarlin
ms.openlocfilehash: 58a220192d6a02c6ad66094e8421405578a82a01
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231315"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gerenciar problemas do Endpoint Protection com a central de segurança do Azure
A central de segurança do Azure monitora o status da proteção antimalware e relata isso na folha problemas do Endpoint Protection. A central de segurança realça problemas, como ameaças detectadas e proteção insuficiente, que podem tornar suas VMs (máquinas virtuais) e computadores vulneráveis a ameaças antimalware. Usando as informações em **problemas do Endpoint Protection**, você pode identificar um plano para resolver quaisquer problemas identificados.

A central de segurança relata os seguintes problemas de proteção de ponto de extremidade:

- Endpoint Protection não instalado em VMs do Azure – uma solução antimalware com suporte não está instalada nessas VMs do Azure.
- Endpoint Protection não instalado em computadores não Azure – um Antimalware com suporte não está instalado nesses computadores não Azure.
- Integridade do Endpoint Protection:

  - Assinatura desatualizada – uma solução antimalware está instalada nessas VMs e computadores, mas a solução não tem as assinaturas antimalware mais recentes.
  - Sem proteção em tempo real – uma solução antimalware é instalada nessas VMs e computadores, mas não está configurada para proteção em tempo real.   O serviço pode estar desabilitado ou a central de segurança pode não conseguir obter o status porque a solução não tem suporte. Consulte [integração de parceiros](security-center-services.md#endpoint-supported) para obter uma lista de soluções com suporte.
  - Sem relatórios – uma solução antimalware está instalada, mas não está relatando dados.
  - Desconhecido – uma solução antimalware está instalada, mas seu status é desconhecido ou está relatando um erro desconhecido.

    > [!NOTE]
    > Consulte [integrar soluções de segurança](security-center-services.md#endpoint-supported) para obter uma lista de soluções de segurança do Endpoint Protection integradas à central de segurança.
    >
    >

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Problemas do Endpoint Protection são apresentados como uma recomendação na central de segurança.  Se o seu ambiente estiver vulnerável a ameaças Antimalware, essa recomendação será exibida em **recomendações** e em **computação**. Para ver o **painel de problemas do Endpoint Protection**, você precisa seguir o fluxo de trabalho de computação.

Neste exemplo, usaremos a **computação**.  Veremos como instalar o antimalware em VMs do Azure e em computadores não Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instalar o antimalware em VMs do Azure

1. Selecione **computação & aplicativos** no menu principal da central de segurança ou **visão geral**.

   ![Selecionar computação][1]

2. Em **computação**, selecione **problemas de proteção de ponto de extremidade**. O painel **problemas do Endpoint Protection** é aberto.

   ![Selecionar problemas do Endpoint Protection][2]

   A parte superior do painel fornece:

   - Provedores do Endpoint Protection instalados – lista os diferentes provedores identificados pela central de segurança.
   - Estado de integridade do Endpoint Protection instalado – mostra o estado de integridade de VMs e computadores que têm uma solução de proteção de ponto de extremidade instalada. O gráfico mostra o número de VMs e computadores que estão íntegros e o número com proteção insuficiente.
   - Malware detectado – mostra o número de VMs e computadores em que a central de segurança está relatando malware detectado.
   - Computadores atacados – mostra o número de VMs e computadores em que a central de segurança está relatando ataques por malware.

   Na parte inferior do painel há uma lista de problemas do Endpoint Protection que inclui as seguintes informações:  

   - **Total** -o número de VMs e computadores afetados pelo problema.
   - Uma barra que agrega o número de VMs e computadores afetados pelo problema. As cores na barra identificam a prioridade:

      - Vermelho-alta prioridade e deve ser endereçado imediatamente
      - Laranja-prioridade média e deve ser resolvido assim que possível

3. Selecione **Endpoint Protection não instalado em VMs do Azure**.

   ![Selecionar proteção de ponto de extremidade não instalada em VMs do Azure][3]

4. Em **proteção de ponto de extremidade não instalada em VMs do Azure** , há uma lista de VMs do Azure que não têm Antimalware instalado.  Você pode optar por instalar o antimalware em todas as VMs na lista ou selecionar VMs individuais para instalar o antimalware clicando na VM específica.
5. Em **selecionar proteção de ponto de extremidade**, selecione a solução de proteção de ponto de extremidade que você deseja usar. Neste exemplo, selecione **Microsoft Antimalware**.
6. São exibidas informações adicionais sobre a solução Endpoint Protection. Selecione **Criar**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalar o antimalware em computadores não Azure

1. Volte para **problemas do Endpoint Protection** e selecione **Endpoint Protection não instalado em computadores não Azure**.

   ![Selecionar proteção de ponto de extremidade não instalada em computadores não Azure][4]

2. Em **proteção de ponto de extremidade não instalada em computadores não Azure**, selecione um espaço de trabalho. Uma consulta de pesquisa de logs de Azure Monitor filtrada para o espaço de trabalho é aberta e lista os computadores com Antimalware ausentes. Selecione um computador na lista para obter mais informações.

   ![Pesquisa de logs de Azure Monitor][5]

Outro resultado da pesquisa é aberto com informações filtradas somente para esse computador.

  ![Pesquisa de logs de Azure Monitor][6]

> [!NOTE]
> Recomendamos que o Endpoint Protection seja provisionado para todas as VMs e computadores para ajudar a identificar e remover vírus, spyware e outros softwares mal-intencionados.
>
>

## <a name="next-steps"></a>Passos Seguintes
Este artigo mostrou como implementar a recomendação "instalar Endpoint Protection" da central de segurança. Para saber mais sobre como habilitar o Microsoft Antimalware no Azure, consulte o seguinte documento:

* [Microsoft antimalware para serviços de nuvem e máquinas virtuais](../security/fundamentals/antimalware.md) – saiba como implantar o Microsoft antimalware.

Para saber mais sobre a central de segurança, consulte os seguintes documentos:

* [Configurando políticas de segurança na central de segurança do Azure](tutorial-security-policy.md) – saiba como configurar políticas de segurança.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) --encontre postagens no blog sobre a segurança e a conformidade do Azure.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
