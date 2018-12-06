---
title: Fornecer detalhes de contactos de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como fornecer detalhes de contacto de segurança no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/20/2018
ms.author: rkarlin
ms.openlocfilehash: 5ed7550b3582fe90bd581dff068e2a6e2c8b72d8
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52967440"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contactos de segurança no Centro de segurança do Azure
Centro de segurança do Azure irá recomendar que forneça os detalhes de contacto de segurança para a sua subscrição do Azure se ainda não o fez. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. MSRC executa segurança selecione monitorização da rede do Azure e infraestrutura e recebe a reclamações de abuso e inteligência de ameaças de terceiros.

É enviada uma notificação de e-mail na primeira ocorrência diária de um alerta e apenas para alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Grupos de recursos numa subscrição herdarão essas configurações.

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.
>
>

## <a name="implement-the-recommendation"></a>Implementar a recomendação
1. Sob **recomendações**, selecione **fornecer detalhes de contactos de segurança**.
   ![Fornecer o contacto de segurança][1]
2. Selecione a subscrição do Azure para fornecer informações de contacto no.
3. Esta ação abre **notificações por E-Mail**.

   ![Disponibilizar detalhes de contacto de segurança][2]

   * Introduza o endereço de correio eletrónico de contacto de segurança ou endereços separados por vírgulas. Não existe um limite ao número de endereços de e-mail que pode introduzir.
   * Introduza um número de telefone internacional de contacto de segurança.
   * Para receber e-mails sobre alertas de gravidade elevada, ative a opção **enviar-me e-mails sobre alertas**.
   * No futuro, terá a opção para enviar notificações por e-mail para proprietários de subscrições. Esta opção está atualmente a cinzento.
   * Selecione **guardar** para aplicar as informações de contacto de segurança à sua subscrição.

## <a name="see-also"></a>Consulte também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](security-center-azure-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
