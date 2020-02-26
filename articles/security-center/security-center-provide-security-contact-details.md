---
title: Fornecer detalhes de contacto de segurança no Centro de Segurança Azure  Microsoft Docs
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
ms.openlocfilehash: 15029c3e0bd3959000786af484a42691f00bb704
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2020
ms.locfileid: "77603561"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Forneça detalhes de contacto de segurança no Centro de Segurança Azure
O Azure Security Center recomendará que forneça dados de contacto de segurança para a sua subscrição azure se ainda não o fez. A Microsoft irá utilizar estas informações para o contactar se o Microsoft Security Response Center (MSRC) detetar que os seus dados de cliente foram acedidos de forma ilícita ou não autorizada. A MSRC realiza uma monitorização de segurança selecionada da rede e infraestrutura do Azure e recebe queixas de inteligência e abuso de ameaças de terceiros.

É enviada uma notificação por e-mail na primeira ocorrência diária de um alerta e apenas relativamente a alertas de gravidade elevada. As preferências de e-mail só podem ser configuradas para as políticas de subscrição. Os grupos de recursos numa subscrição herdarão estas definições. Os alertas estão disponíveis apenas no nível Standard do Centro de Segurança Azure.

As notificações de e-mail de alerta são enviadas:
- Apenas para alertas de gravidade elevada
- Para um único destinatário de e-mail por tipo de alerta por dia  
- Não mais do que 3 mensagens de e-mail são enviadas a um único destinatário num único dia
- Cada mensagem de e-mail contém um único alerta, não uma agregação de alertas
 
Por exemplo, se uma mensagem de e-mail já tiver sido enviada para o alertar sobre um ataque RDP, não receberá outra mensagem de e-mail sobre um ataque RDP no mesmo dia, mesmo que seja acionado outro alerta. 

> [!NOTE]
> Este documento apresenta o serviço ao utilizar um exemplo de implementação.  Não se trata de um guia passo-a-passo.

## Configurar notificações por e-mail para alertas<a name="email"></a>

1. A partir do portal, selecione **Preços e configurações**.
1. Clique na subscrição.
1. Clique em **notificações de e-mail**.

> [!NOTE]
> Se estiver a implementar uma recomendação, então, em **recomendações,** selecione Fornecer dados de contacto de **segurança,** selecione a subscrição Azure para fornecer informações de contacto. Isto abre notificações por **e-mail.**

   ![Disponibilizar detalhes de contacto de segurança][2]

   * Introduza o endereço de e-mail de contacto de segurança ou endereços separados por vírgulas. Não existe um limite para o número de endereços de e-mail que pode introduzir.
   * Introduza um número de telefone internacional de contacto de segurança.
   * Para receber e-mails sobre alertas de alta severidade, ligue a opção **Enviar-me um email sobre alertas**.
   * Tem a opção de enviar notificações de e-mail aos proprietários de subscrições (administrador de serviço clássico e coadministradores, além da função do Proprietário RBAC no âmbito da subscrição).
   * Selecione **Guardar** para aplicar as informações de contacto de segurança na sua subscrição.

## <a name="see-also"></a>Consulte também
Para saber mais acerca do Centro de Segurança, consulte o seguinte:

* [Definir políticas de segurança no Centro de Segurança do Azure](tutorial-security-policy.md) – Saiba como configurar políticas de segurança para as suas subscrições e grupos de recursos do Azure.
* [Gerir recomendações](security-center-recommendations.md) de segurança no Azure Security Center -- Saiba como as recomendações ajudam a proteger os seus recursos Azure.
* [Monitorização](security-center-monitoring.md) de saúde de segurança no Centro de Segurança Azure.- Aprenda a monitorizar a saúde dos seus recursos Azure.
* [Gerir e responder a alertas de segurança no Centro de Segurança do Azure](security-center-managing-and-responding-alerts.md) – Saiba como gerir e responder a alertas de segurança.
* [Monitorização de soluções de parceiros com o Centro de Segurança do Azure](security-center-partner-solutions.md) – Saiba como monitorizar o estado de funcionamento das suas soluções de parceiros.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
