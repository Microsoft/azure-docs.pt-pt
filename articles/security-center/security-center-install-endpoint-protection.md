---
title: Gerir problemas de proteção de ponto final com o Centro de segurança do Azure | Documentos da Microsoft
description: Saiba como gerir problemas de proteção de ponto final no Centro de segurança do Azure.
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
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 6d9c5b080c491d3a6f35f591c69d4c39989b22a8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58099802"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gerir problemas de proteção de ponto final com o Centro de segurança do Azure
Centro de segurança do Azure monitoriza o estado de proteção antimalware e os relatórios isso o painel de problemas de proteção de ponto final. Centro de segurança destaca os problemas, como ameaças detetadas e proteção insuficiente, que pode tornar as máquinas virtuais (VMs) e os computadores vulneráveis a ameaças de antimalware. Ao utilizar as informações em **problemas do Endpoint protection**, pode identificar um plano para resolver quaisquer problemas identificados.

Centro de segurança reporta os seguintes problemas de proteção de ponto final:

- Endpoint protection não instalado em VMs do Azure – uma solução de antimalware suportadas não está instalado nestas VMs do Azure.
- Endpoint protection não instalado em computadores não Azure – um suporte de antimalware não está instalado nesses computadores não Azure.
- Estado de funcionamento do Endpoint protection:

  - Assinatura desatualizada – uma solução antimalware está instalada nessas VMs e computadores, mas a solução não tem as assinaturas de antimalware mais recentes.
  - Sem proteção em tempo real – uma solução antimalware está instalada nessas VMs e computadores, mas não está configurada para proteção em tempo real.   O serviço poderá estar desabilitado ou centro de segurança pode não ser possível obter o estado, uma vez que a solução não é suportada. Ver [integração de parceiros](security-center-partner-integration.md) para obter uma lista de soluções suportadas.
  - Relatório não — uma solução antimalware é instalada, mas não os dados de relatórios.
  - Desconhecido – uma solução antimalware está instalado mas o respetivo estado é desconhecido ou reportar um erro desconhecido.

    > [!NOTE]
    > Ver [integrar soluções de segurança](security-center-partner-integration.md#integrated-azure-security-solutions) para obter uma lista de soluções de segurança de proteção de ponto final integrado com o Centro de segurança.
    >
    >

## <a name="implement-the-recommendation"></a>Implementar a recomendação
Problemas de proteção de ponto final é apresentado como uma recomendação no Centro de segurança.  Se o ambiente está vulnerável a ameaças de antimalware, esta recomendação irá ser apresentada em **recomendações** e, em **computação**. Para ver os **dashboard de problemas de proteção de ponto final**, tem de seguir o fluxo de trabalho de computação.

Neste exemplo, utilizamos **computação**.  Veremos como instalar o antimalware em VMs do Azure e em computadores não Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instalar o antimalware em VMs do Azure

1. Selecione **computação** no menu principal do Centro de segurança ou **descrição geral**.

   ![Selecione computação][1]

2. Sob **computação**, selecione **problemas do Endpoint protection**. O **problemas do Endpoint protection** dashboard abre.

   ![Selecione os problemas do Endpoint protection][2]

   Fornece a parte superior do dashboard:

   - Instalado fornecedores do endpoint protection - listas os diferentes fornecedores identificados pelo centro de segurança.
   - Estado de funcionamento da proteção de ponto final instalado - mostra o estado de funcionamento das VMs e computadores que têm uma solução de proteção de ponto final instalada. O gráfico mostra o número de VMs e computadores que estão em bom Estados e o número com proteção insuficiente.
   - Software maligno detetado – mostra o número de VMs e computadores em que está a comunicar o Centro de segurança detetou software maligno.
   - Computadores atacados – mostra o número de VMs e computadores em que o Centro de segurança está a comunicar ataques por software maligno.

   Na parte inferior do dashboard há uma lista de ponto final de problemas de proteção que inclui as seguintes informações:  

   - **TOTAL** -o número de VMs e computadores afetados pelo problema.
   - A barra de agregar o número de VMs e computadores afetados pelo problema. As cores na barra de identificar prioridade:

      - Red - alta prioridade e deve ser resolvido imediatamente
      - Cor de laranja, prioridade média e deve ser resolvido logo que possível

3. Selecione **Endpoint protection não instalado em VMs do Azure**.

   ![Selecione o Endpoint protection não instalado em VMs do Azure][3]

4. Sob **Endpoint protection não instalado em VMs do Azure** é uma lista de VMs do Azure que não têm antimalware instalado.  Pode optar por instalar o antimalware em todas as VMs na lista ou selecione as VMs individuais para instalar o antimalware em clicando na VM específica.
5. Sob **selecionar Endpoint protection**, selecione a solução de proteção de ponto final que pretende utilizar. Neste exemplo, selecione **Microsoft Antimalware**.
6. São apresentadas informações adicionais sobre a solução de proteção de ponto final. Selecione **Criar**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instalar o antimalware em computadores não Azure

1. Volte ao **problemas do Endpoint protection** e selecione **Endpoint protection não instalado em computadores não Azure**.

   ![Selecione o Endpoint protection não instalado em computadores não Azure][4]

2. Sob **Endpoint protection não instalado em computadores não Azure**, selecione uma área de trabalho. Uma consulta de pesquisa de registos do Azure Monitor filtrada para a área de trabalho abre e apresenta uma lista de computadores em falta antimalware. Selecione um computador a partir da lista para obter mais informações.

   ![Pesquisa de registos de Monitor do Azure][5]

Outro resultado da pesquisa é aberto com informações filtradas apenas para esse computador.

  ![Pesquisa de registos de Monitor do Azure][6]

> [!NOTE]
> Recomendamos que a proteção de ponto final ser aprovisionada para todas as VMs e computadores ajudar a identificar e remover vírus, spyware e outro software malicioso.
>
>

## <a name="next-steps"></a>Passos Seguintes
Este artigo mostrou como implementar a recomendação do Centro de segurança "Instalar o Endpoint Protection." Para saber mais sobre como ativar o Antimalware da Microsoft no Azure, consulte o documento seguinte:

* [O Microsoft Antimalware para serviços Cloud e máquinas virtuais](../security/azure-security-antimalware.md) – Saiba como implementar Antimalware da Microsoft.

Para saber mais sobre o Centro de segurança, consulte os seguintes documentos:

* [Definir políticas de segurança no Centro de segurança do Azure](tutorial-security-policy.md) – Saiba como configurar as políticas de segurança.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – encontre mensagens do Blogue acerca da segurança do Azure e de conformidade.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
