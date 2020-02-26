---
title: Gerir problemas de proteção de pontos finais com o Azure Security Center  Microsoft Docs
description: Saiba mais sobre a monitorização do Centro de Segurança da sua proteção de ponto final e como remediar quaisquer problemas que surjam.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 1599ad5f-d810-421d-aafc-892e831b403f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/28/2019
ms.author: memildin
ms.openlocfilehash: e1ed403babe66b465fb1800dc8c5a90c7a8f1a08
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77604106"
---
# <a name="manage-endpoint-protection-issues-with-azure-security-center"></a>Gerir problemas de proteção de pontos finais com o Azure Security Center
O Azure Security Center monitoriza o estado da proteção antimalware e informa-o na página de problemas de proteção do Endpoint. O Centro de Segurança destaca questões, como ameaças detetadas e proteção insuficiente, que podem tornar as suas máquinas virtuais (VMs) e computadores vulneráveis a ameaças antimalware. Ao utilizar as informações em questões de **proteção endpoint,** pode identificar um plano para resolver quaisquer problemas identificados.

O Centro de Segurança informa os seguintes problemas de proteção do ponto final:

- Proteção endpoint não instalada em VMs Azure – Não é instalada uma solução antimalware suportada nestes VMs Azure.
- Proteção endpoint não instalada em computadores não Azure – Não é instalado um antimalware suportado nestes computadores não Azure.
- Saúde de proteção do ponto final:

  - Assinatura desatualizada – Uma solução antimalware está instalada nestes VMs e computadores, mas a solução não tem as mais recentes assinaturas antimalware.
  - Sem proteção em tempo real – Uma solução antimalware está instalada nestes VMs e computadores, mas não está configurada para proteção em tempo real. O serviço pode ser desativado ou o Centro de Segurança pode não conseguir obter o estado porque a solução não é suportada. Consulte a integração de [parceiros](security-center-services.md#endpoint-supported) para obter uma lista de soluções suportadas.
  - Não reportando – Uma solução antimalware está instalada mas não reporta dados.
  - Desconhecido – Uma solução antimalware está instalada mas o seu estado é desconhecido ou reporta um erro desconhecido.

    > [!NOTE]
    > Consulte Soluções de [segurança Integradas](security-center-services.md#endpoint-supported) para uma lista de soluções de segurança de proteção de pontofinal integradas no Centro de Segurança.
    >
    >

## <a name="implement-the-recommendation"></a>Implementar a recomendação
As questões de proteção do ponto final são apresentadas como uma recomendação no Centro de Segurança. Se o seu ambiente for vulnerável a ameaças antimalware, esta recomendação será apresentada de acordo com **recomendações** e ao abrigo da **Compute**. Para ver o dashboard de problemas de **proteção endpoint,** tem de seguir o fluxo de trabalho da Compute.

Neste exemplo, usaremos a **Compute**.  Vamos ver como instalar antimalware em VMs Azure e em computadores não-Azure.

## <a name="install-antimalware-on-azure-vms"></a>Instale antimalware em VMs Azure

1. Selecione **Compute & apps** no menu principal do Security Center ou **na visão geral**.

   ![Selecione Compute][1]

2. Em **Compute, selecione**problemas de **proteção**do ponto final . A **proteção endpoint emite** o dashboard.

   ![Selecione problemas de proteção endpoint][2]

   A parte superior do painel fornece:

   - Fornecedores de proteção de pontofinal instalados - Lista os diferentes fornecedores identificados pelo Security Center.
   - Estado de saúde de proteção de pontofinal instalado - Mostra o estado de saúde de VMs e computadores que têm uma solução de proteção de ponto final instalada. O gráfico mostra o número de VMs e computadores que são saudáveis e o número com proteção insuficiente.
   - Malware detetado – Mostra o número de VMs e computadores onde o Security Center está a reportar malware detetado.
   - Computadores atacados – Mostra o número de VMs e computadores onde o Security Center está a reportar ataques por malware.

   Na parte inferior do painel de instrumentos existe uma lista de questões de proteção do ponto final que inclui as seguintes informações:  

   - **TOTAL** - O número de VMs e computadores impactados pelo problema.
   - Um bar que agrega o número de VMs e computadores impactados pelo problema. As cores do bar identificam prioridade:

      - Vermelho - Alta prioridade e deve ser abordado imediatamente
      - Laranja - Prioridade média e deve ser abordado o mais rapidamente possível

3. Selecione **proteção endpoint não instalada em VMs Azure**.

   ![Selecione proteção endpoint não instalada em VMs Azure][3]

4. Sob **a proteção Endpoint não instalada em VMs Azure** está uma lista de VMs Azure que não têm antimalware instalado.  Pode optar por instalar antimalware em todos os VMs da lista ou selecionar VMs individuais para instalar antimalware clicando no VM específico.
5. Sob **a proteção Select Endpoint,** selecione a solução de proteção de pontofinal que pretende utilizar. Neste exemplo, selecione **Microsoft Antimalware**.
6. São apresentadas informações adicionais sobre a solução de proteção do ponto final. Selecione **Criar**.

## <a name="install-antimalware-on-non-azure-computers"></a>Instale antimalware em computadores não-Azure

1. Volte a questões de **proteção endpoint** e selecione **a proteção Endpoint não instalada em computadores não Azure**.

   ![Selecione proteção Endpoint não instalada em computadores não Azure][4]

2. Sob **a proteção Endpoint não instalada em computadores não Azure,** selecione um espaço de trabalho. Uma consulta de pesquisa de registos do Azure Monitor filtrada para o espaço de trabalho abre e lista computadores em falta de antimalware. Selecione um computador da lista para obter mais informações.

   ![Pesquisa de registos do Monitor Azure][5]

Outro resultado de pesquisa abre com informação filtrada apenas para esse computador.

  ![Pesquisa de registos do Monitor Azure][6]

> [!NOTE]
> Recomendamos que seja disponibilizada proteção de pontos finais para todos os VMs e computadores para ajudar a identificar e remover vírus, spyware e outros softwares maliciosos.
>
>

## <a name="next-steps"></a>Passos seguintes
Este artigo mostrou-lhe como implementar a recomendação do Centro de Segurança "Instale proteção de ponto final". Para saber mais sobre a ativação do Microsoft Antimalware em Azure, consulte o seguinte documento:

* [Microsoft Antimalware para Serviços cloud e máquinas virtuais](../security/fundamentals/antimalware.md) -- Aprenda a implementar o Antimalware da Microsoft.

Para saber mais sobre o Centro de Segurança, consulte os seguintes documentos:

* Definir políticas de segurança no Centro de [Segurança Azure.](tutorial-security-policy.md)
* [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center -- Saiba como as recomendações ajudam a proteger os seus recursos Azure.
* [Monitorização](security-center-monitoring.md) de saúde de segurança no Centro de Segurança Azure.- Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.

<!--Image references-->
[1]:./media/security-center-install-endpoint-protection/compute.png
[2]:./media/security-center-install-endpoint-protection/endpoint-protection-issues.png
[3]:./media/security-center-install-endpoint-protection/install-endpoint-protection.png
[4]:./media/security-center-install-endpoint-protection/endpoint-protection-issues-computers.png
[5]:./media/security-center-install-endpoint-protection/log-search.png
[6]:./media/security-center-install-endpoint-protection/info-filtered-to-computer.png
