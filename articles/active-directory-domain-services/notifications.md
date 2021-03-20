---
title: Notificações de email para serviços de domínio Azure AD | Microsoft Docs
description: Saiba como configurar notificações de email para alertá-lo sobre problemas num domínio gerido por Serviços de Domínio do Diretório Ativo Azure
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96619169"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Configure notificações de e-mail para problemas nos Serviços de Domínio do Diretório Ativo Azure

A saúde de um domínio gerido pelo Azure Ative Directory Domain Services (Azure AD DS) é monitorizada pela plataforma Azure. A página de estado de saúde no portal Azure mostra quaisquer alertas para o domínio gerido. Para garantir que as questões são respondidas em tempo útil, as notificações por e-mail podem ser configuradas para reportar alertas de saúde assim que forem detetadas no domínio gerido pela Azure AD DS.

Este artigo mostra-lhe como configurar os destinatários da notificação por e-mail para um domínio gerido.

## <a name="email-notification-overview"></a>Visão geral da notificação de e-mail

Para alertá-lo sobre problemas com um domínio gerido, pode configurar notificações de e-mail. Estas notificações de email especificam o domínio gerido em que o alerta está presente, bem como dar o tempo de deteção e um link para a página de saúde no portal Azure. Em seguida, pode seguir os conselhos de resolução de problemas fornecidos para resolver os problemas.

A seguinte notificação por e-mail indica que foi gerado um alerta crítico ou de alerta no domínio gerido:

![Exemplo de notificação de e-mail](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Certifique-se sempre de que o e-mail provém de um remetente da Microsoft verificado antes de clicar nos links da mensagem. As notificações por e-mail vêm sempre do `azure-noreply@microsoft.com` endereço.

### <a name="why-would-i-receive-email-notifications"></a>Por que receberia notificações por e-mail?

A Azure AD DS envia notificações de e-mail para atualizações importantes sobre o domínio gerido. Estas notificações são apenas para questões urgentes que impactam o serviço e devem ser tratadas imediatamente. Cada notificação de e-mail é desencadeada por um alerta sobre o domínio gerido. Os alertas também aparecem no portal Azure e podem ser vistos na página de saúde da [Azure AD DS][check-health].

A Azure AD DS não envia e-mails para fins de publicidade, atualizações ou vendas.

### <a name="when-will-i-receive-email-notifications"></a>Quando receberei notificações por e-mail?

Uma notificação é enviada imediatamente quando um [novo alerta][troubleshoot-alerts] é encontrado em um domínio gerido. Se o alerta não for resolvido, notificações adicionais de e-mail são enviadas como lembrete a cada quatro dias.

### <a name="who-should-receive-the-email-notifications"></a>Quem deve receber as notificações por e-mail?

A lista de destinatários de e-mail para Azure AD DS deve ser composta por pessoas que são capazes de administrar e fazer alterações no domínio gerido. Esta lista de e-mails deve ser considerada como os seus "primeiros socorristas" para quaisquer alertas e problemas.

Pode adicionar até cinco destinatários de e-mails adicionais para notificações por e-mail. Se quiser mais de cinco destinatários para notificações por e-mail, crie uma lista de distribuição e adicione-a à lista de notificações.

Também pode optar por ter todos os *Administradores Globais* do diretório AD Azure e todos os membros do grupo de administradores da *AAD DC* recebem notificações de e-mail. A Azure AD DS apenas envia notificação para até 100 endereços de e-mail, incluindo a lista de administradores globais e administradores da AAD DC.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para rever os destinatários de notificação de e-mail existentes ou adicionar destinatários adicionais, complete os seguintes passos:

1. No portal Azure, procure e selecione **serviços de domínio Azure AD**.
1. Selecione o seu domínio gerido, como *aaddscontoso.com*.
1. No lado esquerdo da janela de recursos Azure AD DS, selecione **definições de Notificação**. Os destinatários existentes para notificações por e-mail são apresentados.
1. Para adicionar um destinatário de e-mail, insira o endereço de e-mail na tabela de destinatários adicionais.
1. Quando terminar, **selecione Guarde** na navegação de primeira mão.

> [!WARNING]
> Quando altera as definições de notificação, as definições de notificação para todo o domínio gerido são atualizadas e não apenas a si mesmo.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Recebi uma notificação por e-mail para um alerta, mas quando entrei no portal Azure não houve alerta. O que aconteceu?

Se um alerta for resolvido, o alerta é limpo do portal Azure. A razão mais provável é que alguém que recebe notificações por e-mail resolveu o alerta no domínio gerido, ou foi autoresolved pela plataforma Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Por que não posso editar as definições de notificação?

Se não conseguir aceder à página de definições de notificação no portal Azure, não tem as permissões para editar o domínio gerido. Contacte um administrador global para obter permissões para editar o recurso Azure AD DS ou ser removido da lista de destinatários.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Não parece estar a receber notificações por e-mail, apesar de ter fornecido o meu endereço de e-mail. Porquê?

Verifique o seu spam ou pasta de lixo no seu e-mail para obter a notificação e certifique-se de permitir o remetente de `azure-noreply@microsoft.com` .

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a resolução de problemas sobre alguns dos problemas que podem ser relatados, consulte [alertas resolvendo num domínio gerido][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
