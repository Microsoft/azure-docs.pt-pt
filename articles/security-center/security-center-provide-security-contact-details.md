---
title: Forneça detalhes de contato de segurança no Centro de Segurança Azure ! Microsoft Docs
description: Este documento mostra-lhe como fornecer dados de contacto de segurança no Centro de Segurança Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204105"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Forneça detalhes de contato de segurança no Centro de Segurança Azure
O Centro de Segurança do Azure recomendará que forneça os detalhes de contacto de segurança para a subscrição do Azure, caso ainda não o tenha feito. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. O MSRC realiza uma monitorização de segurança selecionada da rede e da infraestrutura do Azure e recebe informações sobre ameaças e reclamações de abuso de terceiros.

É enviada uma notificação por e-mail na primeira ocorrência diária de um alerta e apenas relativamente a alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Os grupos de recursos numa subscrição herdarão estas definições. Os alertas só estão disponíveis no nível padrão do Centro de Segurança Azure.

As notificações de e-mail de alerta são enviadas:
- Para um único destinatário de e-mail por tipo de alerta por dia  
- Não são enviadas mais de 3 mensagens de correio-e-mail a um único destinatário num só dia
- Cada mensagem de e-mail contém um único alerta, não uma agregação de alertas
- Apenas para alertas de gravidade elevada

> [!TIP]
> Para alertas com outros níveis de gravidade, crie uma [automatização de fluxo de trabalho](workflow-automation.md) para utilizar uma App Lógica que enviará e-mails para o pessoal relevante.
 
Por exemplo, se uma mensagem de e-mail já tiver sido enviada para o alertar sobre um ataque RDP, não receberá outra mensagem de e-mail sobre um ataque RDP no mesmo dia, mesmo que seja acionado outro alerta. 

> [!IMPORTANT]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.

## <a name="set-up-email-notifications-for-alerts"></a>Configurar notificações de email para alertas<a name="email"></a>

1. Como utilizador com a função Security Admin ou Proprietário de Subscrição, abra a página **de notificações de email:**

    - Para alertas, abra **as definições de preços &,** selecione a subscrição relevante e selecione **notificações por e-mail**.

    - Se estiver a implementar uma recomendação, então, de acordo com **recomendações,** **selecione Fornecer dados de contacto de segurança,** selecione a assinatura Azure para fornecer informações de contacto. Isto abre **notificações por e-mail.**

   ![Disponibilizar detalhes de contacto de segurança][2]

1. Insira o endereço de e-mail de contacto de segurança ou endereços separados por vírgulas. Não há limite para o número de endereços de e-mail que pode introduzir.

1. Para receber e-mails sobre alertas de alta gravidade, ligue a opção **Enviar por emails sobre alertas.** Para outros níveis de gravidade, utilize uma App Lógica como explicada na [automatização do fluxo de trabalho.](workflow-automation.md)

1. Pode enviar notificações por e-mail aos proprietários de subscrições (administrador de serviço clássico e Coadministradores, além da função de Proprietário do RBAC no âmbito de subscrição).

1. Para aplicar as informações de contacto de segurança à sua subscrição, **selecione Guardar**.

## <a name="see-also"></a>Ver também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações de segurança no Azure Security Center--](security-center-recommendations.md) Saiba como as recomendações o ajudam a proteger os seus recursos Azure.
* [Monitorização de saúde de segurança no Centro de Segurança Azure.](security-center-monitoring.md)
* [Gerir e responder aos alertas de segurança no Azure Security Center--](security-center-managing-and-responding-alerts.md) Aprender a gerir e responder aos alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
