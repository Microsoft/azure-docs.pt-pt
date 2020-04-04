---
title: Notificações por e-mail para Serviços de Domínio Azure AD [ Microsoft Docs'
description: Saiba configurar notificações de e-mail para alertá-lo sobre questões num domínio gerido pelo Azure Ative Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 03/30/2020
ms.author: iainfou
ms.openlocfilehash: 8c6d59889e572893877f2178cade57e07aa91413
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654795"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Configure notificações de e-mail para problemas em Serviços de Domínio de Diretório Ativo Azure

A saúde de um domínio gerido pelo Azure Ative Directory (Azure AD DS) é monitorizada pela plataforma Azure. A página de estado de saúde no portal Azure mostra quaisquer alertas para o domínio gerido. Para garantir que os problemas são respondidos atempadamente, as notificações por e-mail podem ser configuradas para reportar alertas de saúde assim que forem detetadas no domínio gerido pelo Azure AD DS.

Este artigo mostra-lhe como configurar destinatários de notificação de e-mail para um domínio gerido pelo Azure AD DS.

## <a name="email-notification-overview"></a>Visão geral da notificação de e-mail

Para alertá-lo de problemas com um domínio gerido pelo Azure AD DS, pode configurar notificações de e-mail. Estas notificações por e-mail especificam o domínio gerido pelo Azure AD DS em que o alerta está presente, bem como dar o tempo de deteção e um link para a página de saúde no portal Azure. Em seguida, pode seguir os conselhos de resolução de problemas fornecidos para resolver os problemas.

O seguinte exemplo de notificação por e-mail indica que foi gerado um aviso crítico ou alerta no domínio gerido pela AD DS do Azure:

![Notificação de e-mail de exemplo](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Certifique-se sempre de que o e-mail vem de um remetente verificado da Microsoft antes de clicar nos links da mensagem. As notificações por e-mail são sempre do `azure-noreply@microsoft.com` endereço.

### <a name="why-would-i-receive-email-notifications"></a>Por que receberia notificações por e-mail?

O Azure AD DS envia notificações por e-mail para atualizações importantes sobre o domínio gerido. Estas notificações são apenas para questões urgentes que impactam o serviço e devem ser tratadas imediatamente. Cada notificação por e-mail é desencadeada por um alerta sobre o domínio gerido pelo Azure AD DS. Os alertas também aparecem no portal Azure e podem ser vistos na página de [saúde Azure AD DS][check-health].

O Azure AD DS não envia e-mails para fins de publicidade, atualizações ou vendas.

### <a name="when-will-i-receive-email-notifications"></a>Quando receberei notificações por e-mail?

Uma notificação é enviada imediatamente quando um [novo alerta][troubleshoot-alerts] é encontrado num domínio gerido pela AD DS azure. Se o alerta não for resolvido, as notificações adicionais de e-mail são enviadas como um lembrete a cada quatro dias.

### <a name="who-should-receive-the-email-notifications"></a>Quem deve receber as notificações de e-mail?

A lista de destinatários de e-mail para AD DS Azure deve ser composta por pessoas que são capazes de administrar e fazer alterações no domínio gerido. Esta lista de e-mails deve ser considerada como os seus "socorristas" para quaisquer alertas e problemas.

Pode adicionar até cinco destinatários adicionais de e-mail para notificações por e-mail. Se quiser mais de cinco destinatários para notificações por e-mail, crie uma lista de distribuição e adicione-a à lista de notificações.

Também pode optar por ter todos os *Administradores Globais* do diretório Azure AD e todos os membros do grupo de administradores da *AAD DC* recebem notificações por e-mail. A Azure AD DS apenas envia notificação para até 100 endereços de e-mail, incluindo a lista de administradores globais e administradores da AAD DC.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para rever os destinatários de notificação de e-mail existentes ou adicionar destinatários adicionais, preencha os seguintes passos:

1. No portal Azure, procure e selecione Serviços de **Domínio Azure AD**.
1. Selecione o seu domínio gerido pelo Azure AD DS, como *aaddscontoso.com*.
1. No lado esquerdo da janela de recursos Azure AD DS, selecione definições de **Notificação**. Os destinatários existentes para notificações por e-mail são mostrados.
1. Para adicionar um destinatário de e-mail, insira o endereço de e-mail na tabela de destinatários adicional.
1. Quando terminar, selecione **Guardar** na navegação em cima.

> [!WARNING]
> Ao alterar as definições de notificação, as definições de notificação de todo o domínio gerido pelo Azure AD DS são atualizadas e não apenas a si mesmo.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Recebi uma notificação por e-mail para um alerta, mas quando entrei no portal Azure não houve alerta. O que aconteceu?

Se um alerta for resolvido, o alerta é retirado do portal Azure. A razão mais provável é que alguém que recebe notificações de e-mail resolveu o alerta no domínio gerido pela AD DS do Azure, ou foi auto-resolvido pela plataforma Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Por que não posso editar as definições de notificação?

Se não conseguir aceder à página de definições de notificação no portal Azure, não tem permissões para editar o domínio gerido pelo Azure AD DS. Deve contactar um administrador global para obter permissões para editar o recurso Azure AD DS ou ser removido da lista de destinatários.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Não estou a receber notificações por e-mail, apesar de ter fornecido o meu endereço de e-mail. Porquê?

Verifique o seu spam ou pasta de lixo no seu e-mail para a notificação e certifique-se de que permite o remetente de `azure-noreply@microsoft.com`.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre a resolução de problemas de alguns dos problemas que podem ser relatados, consulte [alertas Resolve sobre um domínio gerido pela AD DS azure][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
