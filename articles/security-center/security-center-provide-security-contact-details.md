---
title: Fornecer detalhes de contactos de segurança no Centro de segurança do Azure | Documentos da Microsoft
description: Este documento mostra como fornecer detalhes de contacto de segurança no Centro de segurança do Azure.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/9/2018
ms.author: rkarlin
ms.openlocfilehash: b6babf7d5d5a0f5796efa9418044366c6a135ed9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60909298"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contactos de segurança no Centro de segurança do Azure
Centro de segurança do Azure irá recomendar que forneça os detalhes de contacto de segurança para a sua subscrição do Azure se ainda não o fez. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. MSRC executa segurança selecione monitorização da rede do Azure e infraestrutura e recebe a reclamações de abuso e inteligência de ameaças de terceiros.

É enviada uma notificação por e-mail na primeira ocorrência diária de um alerta e apenas relativamente a alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Os grupos de recursos numa subscrição herdarão estas definições. 

As notificações de e-mail de alerta são enviadas:
- Apenas para alertas de gravidade elevada
- Para um único destinatário de e-mail por tipo de alerta por dia  
- Não mais de 3 mensagens de e-mail são enviadas para um único destinatário no mesmo dia
- Cada mensagem de e-mail contém um único alerta, não uma agregação de alertas
 
Por exemplo, se uma mensagem de e-mail já tiver sido enviada para o alertar sobre um ataque RDP, não receberá outra mensagem de e-mail sobre um ataque RDP no mesmo dia, mesmo que seja acionado outro alerta. 
 

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

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Centro de segurança do Azure](security-center-recommendations.md) – Saiba como o recomendações o ajudam a proteger os seus recursos do Azure.
* [Monitorização de estado de funcionamento de segurança no Centro de segurança do Azure](security-center-monitoring.md) – Saiba como monitorizar o estado de funcionamento dos seus recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blogue de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as mais recentes notícias de segurança do Azure e informações.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
