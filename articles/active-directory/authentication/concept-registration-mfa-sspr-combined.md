---
title: Registo combinado para SSPR e MFA – Azure Active Directory
description: Autenticação de vários fatores da AD Azure e registo de palavra-passe de autosserviço
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4caaf8704f2ee49f8f094ad22065ae462154be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143911"
---
# <a name="combined-security-information-registration-overview"></a>Visão geral do registo combinado de informações de segurança

Antes do registo combinado, os utilizadores registaram métodos de autenticação para a autenticação de multi-factores Azure e reset de palavra-passe self-service (SSPR) separadamente. As pessoas estavam confusas de que métodos semelhantes eram usados para a autenticação multi-factor e SSPR, mas tinham de se registar para ambas as funcionalidades. Agora, com o registo combinado, os utilizadores podem registar-se uma vez e obter os benefícios tanto da Autenticação Multi-Factor como do SSPR.

Este artigo descreve o que é o registo combinado de segurança. Para começar com o registo de segurança combinado, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Ativar o registo combinado de segurança](howto-registration-mfa-sspr-combined.md)

![O meu perfil mostrando informações de segurança registadas para um utilizador](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de permitir a nova experiência, reveja esta documentação focada no administrador e a documentação focada no utilizador para garantir que compreende a funcionalidade e o efeito desta funcionalidade. Baseie o seu treino na [documentação](../user-help/user-help-security-info-overview.md) do utilizador para preparar os seus utilizadores para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

O registo combinado de informações de segurança da Azure AD não está atualmente disponível para nuvens nacionais como o Governo azure dos EUA, a Azure Germany ou a Azure China 21Vianet.

> [!IMPORTANT]
> Os utilizadores que estejam habilitados tanto para a pré-visualização original como para a experiência de registo combinado melhorada verão o novo comportamento. Os utilizadores que estiverem habilitados para ambas as experiências verão apenas a nova experiência My Profile. O novo My Profile alinha-se com o aspeto e sensação de registo combinado e proporciona uma experiência perfeita para os utilizadores. Os utilizadores podem ver [https://myprofile.microsoft.com](https://myprofile.microsoft.com)o Meu Perfil indo para .
>
> Pode encontrar uma mensagem de erro enquanto tenta aceder à opção de informação de segurança. Por exemplo, "Desculpe, não podemos inscrevê-lo". Neste caso, confirme que não tem qualquer configuração ou objeto de política de grupo que bloqueie cookies de terceiros no navegador web.

As minhas páginas de Perfil são localizadas com base nas definições linguísticas do computador que acede à página. A Microsoft armazena o idioma mais recente usado no cache do navegador, pelo que as tentativas subsequentes de aceder às páginas continuarão a renderizar no último idioma utilizado. Se limpar a cache, as páginas voltarão a renderizar. Se quiser forçar uma linguagem específica, `?lng=<language>` pode adicionar ao fim `<language>` do URL, onde está o código da linguagem que pretende renderizar.

![Criar SSPR ou outros métodos de verificação de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponíveis no registo combinado

O registo combinado suporta os seguintes métodos e ações de autenticação:

|   | Registar | Alterar | Eliminar |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máximo de 5) | Não | Sim |
| Outra aplicação autenticadora | Sim (máximo de 5) | Não | Sim |
| Ficha de hardware | Não | Não | Sim |
| Telefone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone do escritório | Não | Não | Não |
| Email | Sim | Sim | Sim |
| Perguntas de segurança | Sim | Não | Sim |
| Palavras-passe da aplicação | Sim | Não | Sim |
| Chaves de segurança FIDO2<br />*Modo gerido apenas a partir da página [de informações](https://mysignins.microsoft.com/security-info) de Segurança*| Sim | Sim | Sim |

> [!NOTE]
> As palavras-passe da aplicação estão disponíveis apenas para utilizadores que tenham sido aplicados para autenticação multi-factor. As palavras-passe da aplicação não estão disponíveis para utilizadores que estejam habilitados para autenticação multi-factor através de uma política de acesso condicional.

Os utilizadores podem definir uma das seguintes opções como o método de autenticação padrão de vários fatores:

- Autenticador microsoft – notificação.
- Aplicação autenticadora ou ficha de hardware – código.
- Telefonema.
- Mensagem de texto.

À medida que continuamos a adicionar mais métodos de autenticação à Azure AD, esses métodos estarão disponíveis no registo combinado.

## <a name="combined-registration-modes"></a>Modos de registo combinados

Existem dois modos de registo combinado: interromper e gerir.

- **O modo de interrupção** é uma experiência semelhante a um assistente, apresentada aos utilizadores quando registam ou refrescam as suas informações de segurança no início do sessão.
- **Gerir** o modo faz parte do perfil do utilizador e permite que os utilizadores gerem as suas informações de segurança.

Para ambos os modos, os utilizadores que tenham registado previamente um método que possa ser utilizado para a autenticação multi-factor terão de realizar a Autenticação Multi-Factor antes de poderem aceder às suas informações de segurança. Os utilizadores devem confirmar as suas informações antes de continuarem a utilizar os seus métodos previamente registados. 

### <a name="interrupt-mode"></a>Modo de interrupção

O registo combinado respeita as políticas de Autenticação Multi-Factor e SSPR, se ambos estiverem habilitados para o seu inquilino. Estas políticas controlam se um utilizador é interrompido para registo durante o registo e quais os métodos disponíveis para registo.

Eis vários cenários em que os utilizadores podem ser solicitados a registar ou atualizar as suas informações de segurança:

- Registo de autenticação multi-factor imposto através da Proteção de Identidade: Os utilizadores são convidados a registar-se durante o registo. Registam métodos de autenticação multi-factor e métodos SSPR (se o utilizador estiver ativado para SSPR).
- Registo de autenticação multi-factor imposto através da autenticação multi-factor por utilizador: Os utilizadores são convidados a registar-se durante o registo. Registam métodos de autenticação multi-factor e métodos SSPR (se o utilizador estiver ativado para SSPR).
- Registo de autenticação multi-factor imposto através de Acesso Condicional ou outras políticas: Pede-se aos utilizadores que se registem quando utilizam um recurso que exija a autenticação multi-factor. Registam métodos de autenticação multi-factor e métodos SSPR (se o utilizador estiver ativado para SSPR).
- Registo spr imposto: Os utilizadores são convidados a registar-se durante o registo. Registam apenas métodos SSPR.
- SSPR atualização aplicada: Os utilizadores são obrigados a rever as suas informações de segurança num intervalo definido pelo administrador. Os utilizadores são mostrados as suas informações e podem confirmar a informação atual ou fazer alterações se necessário.

Quando o registo é aplicado, os utilizadores são mostrados o número mínimo de métodos necessários para serem conformes com as políticas de Autenticação Multi-Factor e SSPR, da maioria ao menos seguro.

Por exemplo:

- Um utilizador está ativado para SSPR. A política de SSPR exigia dois métodos para redefinir e permitiu o código de aplicações móveis, e-mail e telefone.
   - Este utilizador é obrigado a registar dois métodos.
      - O utilizador é mostrado aplicação autenticador a e telefone por padrão.
      - O utilizador pode optar por registar e-mails em vez de aplicação ou telefone autenticador.

Este fluxograma descreve quais os métodos que são mostrados a um utilizador quando interrompido para se registar durante o início de sessão:

![Fluxograma de informações de segurança combinado](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se tiver a autenticação multi-factor ativada e o SSPR ativado, recomendamos que aplique o registo de autenticação multi-factor.

Se a política de SSPR exigir que os utilizadores revejam as suas informações de segurança a intervalos regulares, os utilizadores são interrompidos durante o início de sessão e mostram todos os seus métodos registados. Podem confirmar a informação atual se estiver atualizada, ou se podem fazer alterações se for necessário. Os utilizadores devem efetuar a autenticação de vários fatores ao aceder a esta página.

### <a name="manage-mode"></a>Gerir o modo

Os utilizadores podem aceder [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) ao modo de gestão indo ou selecionando **informações** de Segurança a partir do Meu Perfil. A partir daí, os utilizadores podem adicionar métodos, eliminar ou alterar os métodos existentes, alterar o método predefinido e muito mais.

## <a name="key-usage-scenarios"></a>Cenários de utilização chave

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante o início de sessão

Um administrador impôs o registo.

Um utilizador não criou todas as informações de segurança necessárias e vai para o portal Azure. Depois de introduzir o nome de utilizador e a palavra-passe, o utilizador é solicitado a configurar informações de segurança. Em seguida, o utilizador segue os passos mostrados no assistente para configurar as informações de segurança necessárias. Se as suas definições o permitirem, o utilizador pode optar por configurar métodos diferentes dos apresentados por predefinição. Depois de completar o assistente, os utilizadores revêem os métodos que configuram e o seu método padrão de autenticação multi-factor. Para completar o processo de configuração, o utilizador confirma a informação e continua no portal Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança a partir do meu perfil

Um administrador não impôs o registo.

Um utilizador que ainda não tenha configurado todas [https://myprofile.microsoft.com](https://myprofile.microsoft.com)as informações de segurança necessárias vai para . O utilizador seleciona **informações de segurança** no painel esquerdo. A partir daí, o utilizador opta por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue os passos para configurar esse método. Quando terminado, o utilizador vê o método que acabou de ser configurado na página de informações de Segurança.

### <a name="delete-security-info-from-my-profile"></a>Eliminar informações de segurança do Meu Perfil

Um utilizador que já tenha configurado pelo [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)menos um método navega para . O utilizador opta por eliminar um dos métodos previamente registados. Quando terminado, o utilizador já não vê esse método na página de informações de Segurança.

### <a name="change-the-default-method-from-my-profile"></a>Alterar o método padrão do Meu Perfil

Um utilizador que já tenha configurado pelo menos um método que pode [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)ser utilizado para a autenticação multi-factor navega para . O utilizador altera o método padrão atual para um método padrão diferente. Quando terminado, o utilizador vê o novo método predefinido na página de informações de Segurança.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte os tutoriais para permitir o reset da [palavra-passe de autosserviço](tutorial-enable-sspr.md) e ativar a [autenticação de multi-factores Azure](tutorial-enable-azure-mfa.md).

Saiba como permitir o [registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md) ou force os utilizadores a [reregistar métodos de autenticação](howto-mfa-userdevicesettings.md#manage-user-authentication-options).

Também pode rever os [métodos disponíveis para autenticação de multi-factores Azure e SSPR](concept-authentication-methods.md).
