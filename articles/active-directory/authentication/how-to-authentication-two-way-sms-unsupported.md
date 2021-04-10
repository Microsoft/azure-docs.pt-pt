---
title: SMS bidireccionado já não suportado - Azure Ative Directory
description: Explica como ativar outro método para os utilizadores que ainda utilizam SMS bidirecionais.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/02/2021
ms.author: justinha
author: rhicock
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: d25ed1e46823ec6d820addf3944c96c97fcabcb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101689033"
---
# <a name="two-way-sms-unsupported"></a>SMS bidirecciona sem suporte

O servidor de autenticação multi-factor Azure AD (MFA) foi originalmente depreciado em 2018, e já não é suportado após 24 de fevereiro de 2021. Os administradores devem permitir outro método para os utilizadores que ainda utilizam SMS bidirecionais.

As notificações por email e as notificações do portal Azure Service Health (tostas do portal) foram enviadas aos administradores afetados nos dias 8 de dezembro de 2020 e 28 de janeiro de 2021. Os alertas foram para as funções de Proprietário, Coproprietário, Administrador e Administrador de Serviços RBAC ligadas às subscrições. Se já completou os seguintes passos, não é necessária nenhuma ação.

## <a name="required-actions"></a>Ações necessárias

1. Ativar a aplicação móvel para os seus utilizadores, caso ainda não o tenha feito. Para obter mais informações, consulte [Ativar a autenticação de aplicações móveis com o MFA Server](howto-mfaserver-deploy-mobileapp.md).
1. Notifique os seus utilizadores finais para visitar o [seu portal de utilizador do](howto-mfaserver-deploy-userportal.md) servidor MFA para ativar a aplicação móvel. A [aplicação Microsoft Authenticator](https://www.microsoft.com/en-us/account/authenticator) é a opção de verificação recomendada, uma vez que é mais segura do que SMS bidireccionada. Para mais informações, consulte [a hora de desligar nos transportes telefónicos para autenticação.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752)
1. Altere as definições do utilizador de mensagem de texto bidirecionais para aplicação móvel como método padrão.

## <a name="faq"></a>FAQ

### <a name="what-if-i-dont-change-the-default-method-from-two-way-sms-to-the-mobile-app"></a>E se eu não alterar o método padrão de SMS bidirecciona para a aplicação móvel?
SMS bidirecionais falha após 24 de fevereiro de 2021. Os utilizadores verão um erro quando tentarem iniciar sômpersião e passarão MFA.

### <a name="how-do-i-change-the-user-settings-from-two-way-text-message-to-mobile-app"></a>Como posso alterar as definições do utilizador de mensagem de texto bidirecionais para aplicação móvel?

Deve alterar as definições do utilizador seguindo estes passos:

1. No MFA Server, filtrar a lista de utilizadores para uma mensagem de texto bidireccionada.
1. Selecione todos os utilizadores.
1. Abra o diálogo dos Utilizadores de Edição.
1. Altere os utilizadores de mensagem de texto para aplicativo Mobile.

   ![Screenshot dos utilizadores finais](media/how-to-authentication-two-way-sms-unsupported/end-users.png)

### <a name="do-my-users-need-to-take-any-action-if-yes-how"></a>Os meus utilizadores precisam de tomar alguma medida? Se sim, como?
Sim. Os seus utilizadores finais precisam de visitar o portal específico do Utilizador do Servidor MFA para ativar a aplicação móvel, caso ainda não o tenham feito. Depois de ter feito o Passo 3, qualquer utilizador que não tenha visitado o Portal do Utilizador para configurar a aplicação móvel começará a falhar o sômin até que visite o portal do Utilizador para se re-registar.

### <a name="what-if-my-users-cant-install-the-mobile-app-what-other-options-do-they-have"></a>E se os meus utilizadores não conseguirem instalar a aplicação móvel? Que outras opções têm?
A alternativa a SMS bidirecciona ou a aplicação móvel é uma chamada telefónica. No entanto, a aplicação Microsoft Authenticator é o método de verificação recomendado.

### <a name="will-one-way-sms-be-deprecated-as-well"></a>Será que os SMS unidireccionados também serão depreciados?
Não, só sms bidireccionados está a ser depreciado. Para o MFA Server, o SMS unidirecionais funciona para um subconjunto de cenários:

- Adaptador de AD FS
- Autenticação IIS (requer Portal do Utilizador e configuração)
- RADIUS (requer que os clientes RADIUS suportem o desafio de acesso e que o protocolo PAP seja utilizado)

Existem limitações para quando pode ser utilizado UM SMS de ida que tornam a aplicação móvel uma melhor alternativa, porque não requer a solicitação do código de verificação.
Se ainda quiser utilizar SMS unidirecionais em alguns cenários, poderá deixar estes verificados, mas alterar a secção **de Definições da Empresa,** **a** Mensagem de Texto do Utilizador **por defeitos gerais** para uma só **de ida** em vez de **"Duas vias".** Por último, se utilizar a Sincronização do Diretório que predefinição em SMS, terá de alterá-la para One-Way em vez de "Two-Way".

### <a name="how-can-i-check-which-users-are-still-using-two-way-sms"></a>Como posso verificar quais os utilizadores que ainda utilizam SMS bidireccionis?
Para listar estes utilizadores, inicie o **MFA Server**, selecione a secção **de Utilizadores,** clique na **Lista de Utilizadores do Filtro** e filtre por **Mensagem de Texto de duas vias**.

### <a name="how-do-we-hide-two-way-sms-as-an-option-in-the-mfa-portal-to-prevent-users-from-selecting-it-in-the-future"></a>Como é que escondemos o SMS bidireccionário como opção no portal MFA para impedir que os utilizadores o selecionem no futuro?
No portal do utilizador do servidor MFA, clique em **Definições,** pode limpar **a Mensagem de Texto** para que não esteja disponível. O mesmo acontece na secção **AD FS** se estiver a utilizar O FS AD para inscrição no utilizador.

