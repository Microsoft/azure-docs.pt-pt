---
title: Gerir alertas de segurança no Centro de Segurança do Azure | Microsoft Docs
description: Este documento ajuda-o a utilizar as capacidades do Centro de Segurança do Azure para lidar com incidentes de segurança.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e8feb669-8f30-49eb-ba38-046edf3f9656
ms.service: security-center
ms.topic: conceptual
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/29/2018
ms.author: rkarlin
ms.openlocfilehash: 68bcd2b1916ccdf68eaa31ed251661a6b7e1bca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60704122"
---
# <a name="handling-security-incidents-in-azure-security-center"></a>Lidar com Incidentes de Segurança no Centro de Segurança do Azure
A triagem e a investigação dos alertas de segurança pode ser algo moroso, mesmo para analistas de segurança mais qualificados, e, para muitos, é até difícil saber por onde começar. Através da utilização de [análises](security-center-detection-capabilities.md) para ligar as informações entre [alertas de segurança](security-center-managing-and-responding-alerts.md) distintos, o Centro de Segurança pode fornecer-lhe uma vista única de uma campanha de ataque e todos os alertas relacionados. Deste modo, pode perceber rapidamente quais as ações realizadas pelo atacante e que recursos foram afetados.

Este documento explica como utilizar a capacidade de alerta de segurança no Centro de Segurança para o ajudar a lidar com os incidentes de segurança.

## <a name="what-is-a-security-incident"></a>O que é um incidente de segurança?
No Centro de Segurança, um incidente de segurança é uma agregação de todos os alertas de um recurso que se alinham com padrões de [cadeia de ataque](https://blogs.technet.microsoft.com/office365security/addressing-your-cxos-top-five-cloud-security-concerns/). Os incidentes surgem no mosaico e no painel [Alertas de Segurança](security-center-managing-and-responding-alerts.md). Um Incidente apresenta a lista de alertas relacionados, o que lhe permitir obter mais informações sobre cada ocorrência.

## <a name="managing-security-incidents"></a>Gerir incidentes de segurança
Pode rever os incidentes de segurança atuais ao observar o mosaico Alertas de segurança. Aceda ao Portal do Azure e siga os passos abaixo para ver mais detalhes sobre cada incidente de segurança:

1. No dashboard do Centro de Segurança, verá o mosaico **Alertas de segurança**.

    ![Mosaico Alertas de segurança no Centro de Segurança](./media/security-center-incident/security-center-incident-fig1.png)

2. Clique neste mosaico para o expandir e, se for detetado um incidente de segurança, este será apresentado no gráfico de alertas de segurança, tal como apresentado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig2.png)

3. Observe que a descrição do incidente de segurança tem um ícone diferente em comparação com outros alertas. Clique nele para ver mais detalhes sobre este incidente.

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig3.png)

4. No painel **Incidente**, verá mais detalhes sobre este incidente de segurança, incluindo a descrição completa do incidente, a gravidade (que, neste caso, é elevada), o estado atual do incidente (neste caso, ainda está *ativo*, o que implica que o utilizador ainda não tomou qualquer medida em relação ao incidente; isto pode ser feito clicando com o botão direito do rato no incidente no painel **Alertas de segurança**), o recurso atacado (neste caso, *VM1*), os passos de remediação e, na parte inferior, estão os alertas que foram incluídos neste incidente. Se pretender obter mais informações sobre cada alerta, basta clicar no mesmo e será aberto outro painel, conforme mostrado abaixo:

    ![Incidente de segurança](./media/security-center-incident/security-center-incident-fig4.png)

As informações neste painel irão variar de acordo com o alerta. Para obter mais informações sobre como gerir estes alertas, leia [Managing and responding to security alerts in Azure Security Center (Gerir e responder a alertas de segurança no Centro de Segurança do Azure)](security-center-managing-and-responding-alerts.md). Algumas considerações importantes sobre esta capacidade:

* Um novo filtro permite-lhe personalizar a sua vista para Apenas incidentes, Apenas alertas ou ambos.
* O mesmo alerta pode existir como parte de um incidente (se aplicável), assim como pode estar visível como um alerta autónomo.

## <a name="see-also"></a>Consulte também
Neste documento, aprendeu a utilizar a capacidade de incidentes de segurança no Centro de Segurança. Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).
* [Capacidades de Deteção do Centro de Segurança do Azure](security-center-detection-capabilities.md)
* [Guia de Operações e Planeamento do Centro de Segurança do Azure](security-center-planning-and-operations-guide.md)
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md).
* [FAQ do Centro de Segurança do Azure](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de Segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – Encontre mensagens do blogue acerca da segurança e conformidade do Azure.
