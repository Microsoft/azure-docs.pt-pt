---
title: Fornecer detalhes de contacto de segurança no Centro de Segurança Azure [ Microsoft Docs
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
ms.openlocfilehash: c591dc1efc3b64b0f926e95c14cff681dbcdf957
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385015"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Forneça detalhes de contacto de segurança no Centro de Segurança Azure
O Centro de Segurança do Azure recomendará que forneça os detalhes de contacto de segurança para a subscrição do Azure, caso ainda não o tenha feito. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. O MSRC realiza uma monitorização de segurança selecionada da rede e da infraestrutura do Azure e recebe informações sobre ameaças e reclamações de abuso de terceiros.

É enviada uma notificação por e-mail na primeira ocorrência diária de um alerta e apenas relativamente a alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Os grupos de recursos numa subscrição herdarão estas definições. Os alertas estão disponíveis apenas no nível Standard do Centro de Segurança Azure.

As notificações de e-mail de alerta são enviadas:
- Para um único destinatário de e-mail por tipo de alerta por dia  
- Não mais do que 3 mensagens de e-mail são enviadas a um único destinatário num único dia
- Cada mensagem de e-mail contém um único alerta, não uma agregação de alertas
- Apenas para alertas de gravidade elevada

> [!TIP]
> Para alertas com outros níveis de gravidade, crie uma [automatização](workflow-automation.md) de fluxo de trabalho para utilizar uma App Lógica que enviará e-mails para o pessoal relevante.
 
Por exemplo, se uma mensagem de e-mail já tiver sido enviada para o alertar sobre um ataque RDP, não receberá outra mensagem de e-mail sobre um ataque RDP no mesmo dia, mesmo que seja acionado outro alerta. 

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.

## <a name="set-up-email-notifications-for-alerts"></a>Configurar notificações por e-mail para alertas<a name="email"></a>

1. Abra a página de notificações por **e-mail:**

    1. Para alertas, abra **as definições de preços &**, selecione a subscrição relevante e selecione notificações de **e-mail.**

    1. Se estiver a implementar uma recomendação, então, em **recomendações,** selecione Fornecer dados de contacto de **segurança,** selecione a subscrição Azure para fornecer informações de contacto. Isto abre notificações por **e-mail.**

   ![Disponibilizar detalhes de contacto de segurança][2]

   * Introduza o endereço de e-mail de contacto de segurança ou endereços separados por vírgulas. Não existe limite para o número de endereços de e-mail que pode introduzir.
   * Para receber e-mails sobre alertas de alta severidade, ligue a opção **Enviar-me um email sobre alertas**. Para outros níveis de gravidade, utilize uma App Lógica, como explicado na automatização do fluxo de [trabalho.](workflow-automation.md)
   * Pode enviar notificações de e-mail aos proprietários de subscrições (administrador de serviço clássico e coadministradores, além da função do Proprietário RBAC no âmbito da subscrição).
   * Para aplicar as informações de contacto de segurança na sua subscrição, selecione **Guardar**.

## <a name="see-also"></a>Consulte também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center -- Saiba como as recomendações ajudam a proteger os seus recursos Azure.
* [Monitorização](security-center-monitoring.md) de saúde de segurança no Centro de Segurança Azure.- Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Gerir e responder a alertas](security-center-managing-and-responding-alerts.md) de segurança no Centro de Segurança Azure -- Aprenda a gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
