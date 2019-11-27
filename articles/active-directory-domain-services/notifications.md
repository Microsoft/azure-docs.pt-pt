---
title: Notificações por email para Azure AD Domain Services | Microsoft Docs '
description: Saiba como configurar notificações por email para alertar sobre problemas em um domínio gerenciado Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: cd0e77d1534bf540376fd397ee3e6d45bf8c424a
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74546263"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Configurar notificações por email para problemas no Azure Active Directory Domain Services

A integridade de um domínio gerenciado do Azure Active Directory Domain Services (AD DS do Azure) é monitorada pela plataforma do Azure. A página status de integridade na portal do Azure mostra todos os alertas para o domínio gerenciado. Para garantir que os problemas sejam respondidos em tempo hábil, as notificações por email podem ser configuradas para relatar alertas de integridade assim que forem detectados no domínio gerenciado do Azure AD DS.

Este artigo mostra como configurar destinatários de notificação por email para um domínio gerenciado do Azure AD DS.

## <a name="email-notification-overview"></a>Visão geral da notificação por email

Para alertá-lo sobre problemas com um domínio gerenciado do Azure AD DS, você pode configurar notificações por email. Essas notificações de email especificam o domínio gerenciado do Azure AD DS em que o alerta está presente, bem como a hora da detecção e um link para a página de integridade na portal do Azure. Em seguida, você pode seguir o aviso de solução de problemas fornecido para resolver os problemas.

O exemplo de notificação por email a seguir indica que um aviso crítico ou alerta foi gerado no domínio gerenciado AD DS do Azure:

![Exemplo de notificação por email](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Sempre verifique se o email é proveniente de um Microsoft Sender verificado antes de clicar nos links na mensagem. As notificações por email são sempre provenientes do endereço `azure-noreply@microsoft.com`.

### <a name="why-would-i-receive-email-notifications"></a>Por que eu receberia notificações por email?

O Azure AD DS envia notificações por email para atualizações importantes sobre o domínio gerenciado. Essas notificações são apenas para problemas urgentes que afetam o serviço e devem ser resolvidas imediatamente. Cada notificação por email é disparada por um alerta no domínio gerenciado AD DS do Azure. Os alertas também aparecem na portal do Azure e podem ser exibidos na página de [integridade do AD DS do Azure][check-health].

O Azure AD DS não envia emails para fins de anúncio, atualizações ou vendas.

### <a name="when-will-i-receive-email-notifications"></a>Quando receberei notificações por email?

Uma notificação é enviada imediatamente quando um [novo alerta][troubleshoot-alerts] é encontrado em um domínio gerenciado AD DS do Azure. Se o alerta não for resolvido, as notificações de email adicionais serão enviadas como um lembrete a cada quatro dias.

### <a name="who-should-receive-the-email-notifications"></a>Quem deve receber as notificações por email?

A lista de destinatários de email para o Azure AD DS deve ser composta por pessoas que são capazes de administrar e fazer alterações no domínio gerenciado. Essa lista de emails deve ser considerada como seus "primeiros respondentes" a quaisquer alertas e problemas.

Você pode adicionar até cinco destinatários de email adicionais para notificações por email. Se você quiser mais de cinco destinatários para notificações por email, crie uma lista de distribuição e adicione-a à lista de notificações em vez disso.

Você também pode optar por ter todos os *administradores globais* do diretório do Azure AD e cada membro do grupo de *Administradores do AAD DC* receber notificações por email. O Azure AD DS envia somente a notificação para até 100 endereços de email, incluindo a lista de administradores globais e os administradores do AAD DC.

## <a name="configure-email-notifications"></a>Configurar as notificações por e-mail

Para examinar os destinatários de notificação por email existentes ou Adicionar destinatários adicionais, conclua as seguintes etapas:

1. Na portal do Azure, procure e selecione **Azure AD Domain Services**.
1. Selecione seu domínio gerenciado AD DS do Azure, como *contoso.com*.
1. No lado esquerdo da janela de recursos do AD DS do Azure, selecione **configurações de notificação**. Os destinatários existentes para notificações por email são mostrados.
1. Para adicionar um destinatário de email, insira o endereço de email na tabela destinatários adicionais.
1. Quando terminar, selecione **salvar** na navegação superior.

> [!WARNING]
> Quando você altera as configurações de notificação, as configurações de notificação para todo o domínio gerenciado do Azure AD DS são atualizadas, não apenas por conta própria.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Recebi uma notificação por email para um alerta, mas quando fiz logon no portal do Azure não havia nenhum alerta. O que aconteceu?

Se um alerta for resolvido, o alerta será removido da portal do Azure. O motivo mais provável é que outra pessoa que recebe notificações por email resolveu o alerta no domínio gerenciado AD DS do Azure ou foi automaticamente resolvido pela plataforma Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Por que não posso editar as configurações de notificação?

Se não for possível acessar a página de configurações de notificação no portal do Azure, você não terá as permissões para editar o domínio gerenciado do Azure AD DS. Você deve entrar em contato com um administrador global para obter permissões para editar o recurso de AD DS do Azure ou ser removido da lista de destinatários.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Não parece que estou recebendo notificações por email, embora eu tenha fornecido meu endereço de email. Porquê?

Verifique sua pasta de spam ou lixo eletrônico em seu email para a notificação e certifique-se de permitir o remetente da `azure-noreply@microsoft.com`.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre como solucionar alguns dos problemas que podem ser relatados, consulte [resolver alertas em um domínio gerenciado do Azure AD DS][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
