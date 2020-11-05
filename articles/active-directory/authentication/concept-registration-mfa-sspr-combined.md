---
title: Registo combinado para autenticação multi-factor SSPR e Azure - Diretório Ativo Azure
description: Conheça a experiência de registo combinado do Azure Ative Directory para permitir que os utilizadores se registem tanto para a autenticação multi-factor Azure como para o reset da palavra-passe de autosserviço
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7e1036e63b4fdef241350786fa3a246946a9223c
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378011"
---
# <a name="combined-security-information-registration-for-azure-active-directory-overview"></a>Registo combinado de informações de segurança para a visão geral do Azure Ative Directory

Antes do registo combinado, os utilizadores registaram métodos de autenticação para autenticação multi-factor Azure e redefinição de senha de autosserviço (SSPR) separadamente. As pessoas confundiram-se com o facto de métodos semelhantes terem sido usados para autenticação multi-factor e SSPR, mas tiveram de se registar para ambas as funcionalidades. Agora, com o registo combinado, os utilizadores podem registar-se uma vez e obter os benefícios tanto da Autenticação Multi-Factor como da SSPR.

> [!NOTE]
> A partir de 15 de agosto de 2020, todos os novos inquilinos da AD Azure serão automaticamente habilitados para o registo combinado.

Este artigo descreve o que é o registo de segurança combinado. Para começar com o registo combinado de segurança, consulte o seguinte artigo:

> [!div class="nextstepaction"]
> [Permitir o registo de segurança combinado](howto-registration-mfa-sspr-combined.md)

![O meu perfil mostrando informações de segurança registadas para um utilizador](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de ativar a nova experiência, reveja esta documentação focada no administrador e a documentação focada no utilizador para garantir que compreende a funcionalidade e o efeito desta funcionalidade. Baseie a sua formação na [documentação](../user-help/security-info-setup-signin.md) do utilizador para preparar os seus utilizadores para a nova experiência e ajudar a garantir um lançamento bem-sucedido.

O registo combinado de informações de segurança Azure AD não está atualmente disponível para nuvens nacionais como a Azure Germany ou a Azure China 21Vianet. Está disponível para o Governo Azure US.

> [!IMPORTANT]
> Os utilizadores que estão habilitados tanto para a pré-visualização original como para a experiência de registo combinado melhorada vêem o novo comportamento. Os utilizadores que estão habilitados para ambas as experiências vêem apenas a nova experiência My Profile. O novo *My Profile* alinha-se com o aspeto e a sensação de registo combinado e proporciona uma experiência perfeita para os utilizadores. Os utilizadores podem ver o meu perfil indo para [https://myprofile.microsoft.com](https://myprofile.microsoft.com) .
>
> Pode encontrar uma mensagem de erro enquanto tenta aceder à opção de informação de Segurança, como: "Desculpe, não podemos inscrevê-lo". Confirme que não tem qualquer configuração ou objeto de política de grupo que bloqueie cookies de terceiros no navegador web.

*As páginas do meu perfil* são localizadas com base nas definições de idioma do computador que acede à página. A Microsoft armazena o idioma mais recente utilizado na cache do navegador, pelo que as tentativas subsequentes de aceder às páginas continuam a renderizar no último idioma utilizado. Se limpar a cache, as páginas voltarão a renderizar.

Se quiser forçar uma língua específica, pode adicionar `?lng=<language>` ao final do URL, onde está o `<language>` código do idioma que pretende renderizar.

![Configurar SSPR ou outros métodos de verificação de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponíveis no registo combinado

O registo combinado suporta os seguintes métodos e ações de autenticação:

| Método | Registar | Alterar | Eliminar |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máximo de 5) | No | Sim |
| Outra aplicação autenticadora | Sim (máximo de 5) | No | Sim |
| Ficha de hardware | No | No | Sim |
| Telefone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone do escritório | Sim | Sim | Sim |
| E-mail | Sim | Sim | Sim |
| Perguntas de segurança | Sim | No | Sim |
| Palavras-passe da aplicação | Sim | No | Sim |
| Chaves de segurança FIDO2<br />*Modo gerido apenas a partir da página [de informações de Segurança](https://mysignins.microsoft.com/security-info)*| Sim | Sim | Sim |

> [!NOTE]
> As palavras-passe da aplicação estão disponíveis apenas para utilizadores que tenham sido aplicados para autenticação multi-factor. As palavras-passe da aplicação não estão disponíveis para utilizadores que estejam habilitados para autenticação multi-factor através de uma política de Acesso Condicional.

Os utilizadores podem definir uma das seguintes opções como o método de autenticação multi-factor predefinido:

- Microsoft Authenticator – notificação.
- App autenticador ou token de hardware – código.
- Chamada telefónica.
- Mensagem de texto.

À medida que continuamos a adicionar mais métodos de autenticação ao Azure AD, esses métodos estão disponíveis no registo combinado.

## <a name="combined-registration-modes"></a>Modos de registo combinados

Existem dois modos de registo combinado: interromper e gerir.

- **O modo de interrupção** é uma experiência semelhante a um assistente, apresentada aos utilizadores quando registam ou atualizam as suas informações de segurança no início de sing-in.
- **O modo de gestão** faz parte do perfil do utilizador e permite que os utilizadores gerem as suas informações de segurança.

Para ambos os modos, os utilizadores que tenham registado previamente um método que possa ser utilizado para autenticação multi-factor precisam de realizar a autenticação multi-factor antes de poderem aceder às suas informações de segurança. Os utilizadores devem confirmar as suas informações antes de continuarem a utilizar os seus métodos previamente registados. 

### <a name="interrupt-mode"></a>Modo de interrupção

O registo combinado respeita tanto as políticas de Autenticação Multi-Factor como as políticas SSPR, se ambas estiverem ativadas para o seu inquilino. Estas políticas controlam se um utilizador é interrompido para inscrição durante a entrada e quais os métodos disponíveis para inscrição.

Seguem-se os cenários de amostragem em que os utilizadores podem ser solicitados a registar ou atualizar as suas informações de segurança:

- *Registo de autenticação multi-factor aplicado através da Proteção de Identidade:* Pede-se aos utilizadores que se registem durante a sindução. Registam métodos de autenticação multi-factor e métodos SSPR (se o utilizador estiver habilitado para SSPR).
- *Registo de autenticação multi-factor aplicado através da autenticação multi-factor por utilizador:* Pede-se aos utilizadores que se registem durante a sindução. Registam métodos de autenticação multi-factor e métodos SSPR (se o utilizador estiver habilitado para SSPR).
- *Registo de autenticação multi-factor aplicado através de acesso condicional ou outras políticas:* Pede-se aos utilizadores que se registem quando utilizam um recurso que requer autenticação multi-factor. Registam métodos de autenticação multi-factor e métodos SSPR (se o utilizador estiver habilitado para SSPR).
- *Registo SSPR imposto:* Pede-se aos utilizadores que se registem durante a sindução. Registam apenas métodos SSPR.
- *SSPR refresh forçado:* Os utilizadores são obrigados a rever as suas informações de segurança num intervalo definido pela administração. Os utilizadores são mostrados as suas informações e podem confirmar a informação atual ou fazer alterações se necessário.

Quando o registo é aplicado, os utilizadores são mostrados o número mínimo de métodos necessários para serem compatíveis com as políticas de Autenticação Multi-Factor e SSPR, da maioria para menos segura.

Considere o seguinte cenário de exemplo:

- Um utilizador está ativado para SSPR. A política SSPR exigia dois métodos para reiniciar e permitiu o código de aplicações móveis, e-mail e telefone.
- Este utilizador é obrigado a registar dois métodos.
   - O utilizador é apresentado app autenticador e telefone por padrão.
   - O utilizador pode optar por registar e-mail em vez de app ou telefone autenticador.

O seguinte fluxograma descreve quais os métodos apresentados a um utilizador quando interrompidos para se registar durante a entrada:

![Fluxograma combinado de informações de segurança](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se tiver a autenticação multi-factor e a SSPR ativadas, recomendamos que aplique o registo de autenticação multi-factor.

Se a política SSPR exigir que os utilizadores revejam as suas informações de segurança a intervalos regulares, os utilizadores são interrompidos durante a entrada e mostram todos os seus métodos registados. Podem confirmar a informação atual se estiver em dia ou se podem fazer alterações se necessário. Os utilizadores devem efetuar a autenticação de vários fatores ao aceder a esta página.

### <a name="manage-mode"></a>Modo de gestão

Os utilizadores podem aceder ao modo de gestão [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) indo ou selecionando **informações** de Segurança do Meu Perfil. A partir daí, os utilizadores podem adicionar métodos, eliminar ou alterar métodos existentes, alterar o método padrão, e muito mais.

## <a name="key-usage-scenarios"></a>Cenários-chave de utilização

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante o login

Um administrador impôs o registo.

Um utilizador não criou todas as informações de segurança necessárias e vai para o portal Azure. Depois de introduzir o nome de utilizador e a palavra-passe, o utilizador é solicitado a configurar informações de segurança. Em seguida, o utilizador segue os passos indicados no assistente para configurar as informações de segurança necessárias. Se as suas definições o permitirem, o utilizador pode optar por configurar métodos diferentes dos apresentados por predefinição. Após completar o assistente, os utilizadores analisam os métodos que configuraram e o seu método padrão para autenticação multi-factor. Para completar o processo de configuração, o utilizador confirma a informação e continua até ao portal Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança do Meu Perfil

Um administrador não impôs o registo.

Um utilizador que ainda não tenha configurado todas as informações de segurança necessárias vai para [https://myprofile.microsoft.com](https://myprofile.microsoft.com) . O utilizador seleciona **informações de segurança** no painel esquerdo. A partir daí, o utilizador opta por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue os passos para configurar esse método. Quando terminado, o utilizador vê o método que foi configurado na página de informações de Segurança.

### <a name="delete-security-info-from-my-profile"></a>Eliminar informações de segurança do Meu Perfil

Um utilizador que já criou pelo menos um método navega para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . O utilizador opta por eliminar um dos métodos previamente registados. Quando terminada, o utilizador já não vê esse método na página de informações de Segurança.

### <a name="change-the-default-method-from-my-profile"></a>Alterar o método padrão do Meu Perfil

Um utilizador que já criou pelo menos um método que pode ser utilizado para a autenticação multi-factor para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) . O utilizador altera o método de predefinição atual para um método padrão diferente. Quando terminado, o utilizador vê o novo método predefinido na página de informações de Segurança.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte os tutoriais para permitir o [reset da palavra-passe de autosserviço](tutorial-enable-sspr.md) e [ativar a autenticação multi-factor Azure](tutorial-enable-azure-mfa.md).

Saiba como permitir o [registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md) ou forçar os [utilizadores a re-registar métodos de autenticação.](howto-mfa-userdevicesettings.md#manage-user-authentication-options)

Também pode rever os [métodos disponíveis para autenticação multi-factor Azure e SSPR.](concept-authentication-methods.md)
