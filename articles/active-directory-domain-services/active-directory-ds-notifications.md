---
title: 'Azure Active Directory Domain Services: Definições de notificação | Documentos da Microsoft'
description: Definições de notificação para o Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2018
ms.author: ergreenl
ms.openlocfilehash: 85fcd28b5964cbe0246a7c79fa6d24d756f7bd35
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086569"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Definições de notificação no Azure AD Domain Services

Notificações para os serviços de domínio do Azure AD permite-lhe ser atualizada assim que foi detetado um alerta de estado de funcionamento no seu domínio gerido.  

Esta funcionalidade só está disponível para domínios geridos que não estão em redes virtuais clássicas.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>Como verificar as definições de notificação de e-mail do Azure AD Domain Services

1. Navegue para o [página do Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure
2. Escolha o seu domínio gerido da tabela
3. No painel de navegação esquerda, escolha **definições de notificação**

Na página lista todos os destinatários de e-mail para notificações por e-mail para o Azure AD Domain Services.

## <a name="what-does-an-email-notification-look-like"></a>O que o aspeto de uma notificação por e-mail?

A imagem seguinte é um exemplo de uma notificação por e-mail:

![Notificação de e-mail de exemplo](./media/active-directory-domain-services-alerts/email-alert.png)

O e-mail Especifica o domínio gerido que o alerta está presente, bem como dar a hora da deteção e uma ligação para a página de estado de funcionamento do Azure AD Domain Services no portal do Azure.

> [!WARNING]
> Sempre Certifique-se de que o e-mail é proveniente de um remetente Microsoft verificado antes de clicar em links em suas mensagens de e-mail. Os e-mails provenientes sempre a mensagem de e-mail azure-noreply@microsoft.com


## <a name="why-would-i-receive-email-notifications"></a>Por que vou receber notificações por e-mail?

Serviços de domínio do Azure AD envia notificações de e-mail para as atualizações importantes acerca do seu domínio.  Estas notificações são apenas para questões de urgentes que irão afetar o seu serviço e devem ser resolvidos imediatamente. Cada notificação de e-mail é acionada por um alerta no seu domínio gerido. Estes alertas também será apresentada no portal do Azure e podem ser visualizados no [página de estado de funcionamento do Azure AD Domain Services](active-directory-ds-check-health.md).

O Azure AD Domain Services não envia e-mails a esta lista para fins de vendas, de atualizações ou de anúncio.

## <a name="when-will-i-receive-email-notifications"></a>Quando receberá notificações por e-mail?

Uma notificação será enviada imediatamente quando um [novo alerta](active-directory-ds-troubleshoot-alerts.md) encontra-se no seu domínio gerido. Se o alerta não for resolvido, todos os quatro dias será enviada como um lembrete uma notificação por e-mail.

## <a name="who-should-receive-the-email-notifications"></a>Quem deve receber as notificações de e-mail?


 Recomendamos a lista de destinatários de e-mail para o Azure AD Domain Services deve ser composto de pessoas que podem administrar e fazer alterações ao domínio gerido. Esta lista de e-mail deve ser considerada como seu "respondentes primeiro" para qualquer problema encontrado. Se tiver mais de cinco mensagens de e-mail adicionais que pretende adicionar, recomendamos que crie uma lista de distribuição para adicionar à lista de notificação em vez disso.

É possível adicionar até cinco e-mails adicionais para notificações sobre o Azure AD Domain Services. Além disso, também pode optar por ter todos os administradores globais do seu diretório e cada membro do grupo "administradores do AAD DC" receber notificações de e-mail do Azure AD Domain Services. O Azure AD Domain Services só irão enviar notificações para até 100 endereços de e-mail, incluindo a lista de administradores globais e administradores do AAD DC.


## <a name="how-to-add-an-additional-email-recipient"></a>Como adicionar um destinatário de e-mail adicionais

> [!WARNING]
> Quando alterar as definições de notificação, está a alterar as definições de notificação para o domínio gerido inteiro, não apenas por conta própria.

1. Navegue para o [página do Azure AD Domain Services](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) no portal do Azure.
2. Clique no seu domínio gerido.
3. No painel de navegação esquerdo, clique em **definições de notificação**.
4. Para adicionar uma mensagem de e-mail, escreva o endereço de e-mail na tabela os destinatários adicionais.
5. Clique em "Guardar" na navegação do lado da parte superior.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Recebi uma notificação por e-mail para um alerta, mas quando eu tiver sessão iniciada portal do Azure não ocorreu nenhum alerta. O que aconteceu?

Se um alerta é resolvido, o alerta desaparecerá do portal do Azure. A razão mais provável é que outra pessoa que recebe notificações de e-mail resolver o alerta no seu domínio gerido, ou foi resolvido automaticamente pelo Azure AD Domain Services.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Por que motivo não posso editar as definições de notificação?

Se não conseguir acessar a página de definições de notificação no portal do Azure, não tem permissões para editar o Azure AD Domain Services. Tem de contactar o administrador global para obter permissões para editar recursos do Azure AD Domain Services ou ser removidas da lista de destinatário.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Não parece ser a receber notificações por e-mail, apesar de eu forneci o meu endereço de e-mail. Porquê?

Verifique a pasta de spam ou de lixo no e-mail para a notificação e certifique-se à lista de permissões do remetente (azure-noreply@microsoft.com).

## <a name="next-steps"></a>Passos Seguintes
- [Resolver alertas no seu domínio gerido](active-directory-ds-troubleshoot-alerts.md)
- [Leia mais sobre o Azure AD Domain Services](active-directory-ds-overview.md)
- [Contacte a equipa de produto](active-directory-ds-contact-us.md)

## <a name="contact-us"></a>Contacte-nos
Contacte a equipa de produto do Azure Active Directory Domain Services para [partilhar comentários ou para o suporte](active-directory-ds-contact-us.md).
