---
title: Fornecer detalhes de contato de segurança na central de segurança do Azure | Microsoft Docs
description: Este documento mostra como fornecer detalhes de contato de segurança na central de segurança do Azure.
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
ms.date: 08/09/2019
ms.author: rkarlin
ms.openlocfilehash: ae7c130df40ea0553bd9e3ae117534fe98466f26
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/12/2019
ms.locfileid: "68934897"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contato de segurança na central de segurança do Azure
A central de segurança do Azure recomendará que você forneça detalhes de contato de segurança para sua assinatura do Azure, se ainda não tiver feito isso. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. O MSRC realiza o monitoramento de segurança selecionado da rede e da infraestrutura do Azure e recebe a inteligência contra ameaças e reclamações de abuso de terceiros.

É enviada uma notificação por e-mail na primeira ocorrência diária de um alerta e apenas relativamente a alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Os grupos de recursos numa subscrição herdarão estas definições. 

As notificações de e-mail de alerta são enviadas:
- Apenas para alertas de gravidade elevada
- Para um único destinatário de e-mail por tipo de alerta por dia  
- No máximo três mensagens de email são enviadas para um único destinatário em um único dia
- Cada mensagem de e-mail contém um único alerta, não uma agregação de alertas
 
Por exemplo, se uma mensagem de e-mail já tiver sido enviada para o alertar sobre um ataque RDP, não receberá outra mensagem de e-mail sobre um ataque RDP no mesmo dia, mesmo que seja acionado outro alerta. 

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.

## Configurar notificações por email para alertas<a name="email"></a>

1. No portal, selecione **preços & configurações**.
1. Clique na assinatura.
1. Clique em **notificações por email**.

> [!NOTE]
> Se você estiver implementando uma recomendação, em **recomendações**, selecione **fornecer detalhes de contato de segurança**, selecione a assinatura do Azure para fornecer informações de contato. Isso abre **notificações por email**.

   ![Fornecer detalhes de contactos de segurança][2]

   * Insira o endereço de email de contato de segurança ou endereços separados por vírgulas. Não há um limite para o número de endereços de email que você pode inserir.
   * Insira um número de telefone internacional de contato de segurança.
   * Para receber emails sobre alertas de severidade alta, ative a opção **enviar-me emails sobre alertas**.
   * No futuro, você terá a opção de enviar notificações por email aos proprietários da assinatura. Esta opção está esmaecida no momento.
   * Selecione **salvar** para aplicar as informações de contato de segurança à sua assinatura.

## <a name="see-also"></a>Consulte também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerenciando recomendações de segurança na central de segurança do Azure](security-center-recommendations.md) – saiba como as recomendações ajudam a proteger seus recursos do Azure.
* [Monitoramento de integridade de segurança na central de segurança do Azure](security-center-monitoring.md) – saiba como monitorar a integridade dos recursos do Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.
* [Azure Security Center FAQ (FAQ do Centro de Segurança do Azure)](security-center-faq.md) – Encontre as perguntas mais frequentes acerca de como utilizar o serviço.
* [Blog de segurança do Azure](https://blogs.msdn.com/b/azuresecurity/) – obtenha as últimas notícias e informações de segurança do Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
