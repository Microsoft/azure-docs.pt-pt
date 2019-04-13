---
title: Combinados de registo para o Azure AD SSPR e o multi-factor Authentication (pré-visualização) - Azure Active Directory
description: AD multi-factor Authentication do Azure e palavra-passe self-service de reposição do registo (pré-visualização)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf8d5cb13b39d58920555ff9d99a4949e1bfc20
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521444"
---
# <a name="combined-security-information-registration-preview"></a>Registo de informações de segurança combinados (pré-visualização)

Antes do registo combinado, o utilizadores registados métodos de autenticação para o Azure multi-factor Authentication e reposição de palavra-passe self-service (SSPR) em separado. As pessoas estavam confusos que métodos semelhantes foram utilizados para o multi-factor Authentication e SSPR, mas eles tinham que se registrar para os dois recursos. Agora, com o registo de combinado, os utilizadores podem registar uma vez e obter os benefícios da multi-factor Authentication e SSPR.

![A mostrar meu perfil registado informações de segurança para um utilizador](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de ativar a nova experiência, reveja esta documentação voltada para o administrador e a documentação e focada no usuário para se certificar de que compreende a funcionalidade e o efeito desta funcionalidade. Base seu treinamento sobre a documentação do utilizador para preparar os seus utilizadores para a nova experiência e ajudar a garantir uma implementação com êxito.

O Azure AD combinados registo não está atualmente disponível para clouds nacionais, como o Azure US Government, Azure Alemanha ou Azure China 21Vianet de informações de segurança.

|     |
| --- |
| Registo de informações de segurança combinado para autenticação Multifator e reposição de palavra-passe self-service do Azure Active Directory (Azure AD) é uma funcionalidade de pré-visualização pública do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Os utilizadores que estão ativados para a pré-visualização original e a experiência melhorada de registo combinado irão ver o novo comportamento. Os utilizadores que estão ativados para ambas as experiências irão ver a nova experiência meu perfil. O novo perfil de meus se alinha com o aspeto e funcionalidade de registo combinado e fornece uma experiência totalmente integrada para os usuários. Os utilizadores podem ver meu perfil ao aceder [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

As minhas páginas de perfil são localizadas com base nas configurações de idioma do computador aceder à página. Microsoft armazena o mais recente idioma utilizado na cache do browser, para que as tentativas subsequentes para acessar as páginas irão continuar a processar no último idioma utilizado. Se limpar a cache, as páginas irão compor novamente. Se quiser forçar um idioma específico, pode adicionar `?lng=<language>` no final do URL, onde `<language>` é o código do idioma que pretende processar.

![Configurar a SSPR ou outros métodos de verificação de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponíveis no registo combinado

Combinados registo suporta os seguintes métodos de autenticação e ações:

|   | Registar | Alterar | Eliminar |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máximo de 5) | Não | Sim |
| Outra aplicação de autenticador | Sim (máximo de 5) | Não | Sim |
| Token de hardware | Não | Não | Sim |
| Telefone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone do escritório | Não | Não | Não |
| Email | Sim | Sim | Sim |
| Perguntas de segurança | Sim | Não | Sim |
| Palavras-passe de aplicações | Sim | Não | Sim |

> [!NOTE]
> As palavras-passe de aplicação estão disponíveis apenas para os utilizadores que tenham sido impostos para o multi-factor Authentication. As palavras-passe de aplicação não estão disponíveis para os utilizadores que estão ativados para multi-factor Authentication através de uma política de acesso condicional.

Os usuários podem configurar-se uma das seguintes opções, como o método de multi-factor Authentication padrão:

- Microsoft Authenticator – notificação.
- Aplicação de autenticação ou do hardware do token – de código.
- Chamada telefónica.
- Mensagem de texto.

À medida que Continuamos a adicionar mais métodos de autenticação para o Azure AD, esses métodos vão estar disponíveis no registo combinado.

## <a name="combined-registration-modes"></a>Modos de registo combinado

Existem dois modos de registo combinado: interrupção e gerir.

- **Modo de interrupção** é uma experiência de tipo assistentes, apresentada aos usuários quando eles se registrar ou atualizar suas informações de segurança no início de sessão.

- **Gerenciar o modo de** faz parte do perfil do usuário e permite aos utilizadores gerir as suas informações de segurança.

Em ambos os modos, os utilizadores que registou anteriormente um método que pode ser utilizado para o multi-factor Authentication serão preciso realizar a autenticação Multifator para que poderem aceder suas informações de segurança.

### <a name="interrupt-mode"></a>Modo de interrupção

Registo combinado respeita as políticas de multi-factor Authentication e SSPR, se ambos estiverem ativadas para o seu inquilino. Estas políticas controlam se um utilizador para o registo é interrompido durante o início de sessão e quais métodos estão disponíveis para o registo.

Seguem-se vários cenários em que poderá ser pedido aos utilizadores para registar ou atualizar suas informações de segurança:

* Registo de autenticação Multifator imposto através da proteção de identidade: É pedido aos utilizadores para se registar durante o início de sessão. Eles se registram os métodos de multi-factor Authentication e os métodos SSPR (se o utilizador está ativado para SSPR).
* Registo de autenticação Multifator imposto através de por utilizador multi-factor Authentication: É pedido aos utilizadores para se registar durante o início de sessão. Eles se registram os métodos de multi-factor Authentication e os métodos SSPR (se o utilizador está ativado para SSPR).
* Registo de autenticação Multifator imposto através de acesso condicional ou outras políticas: É pedido aos utilizadores se registem ao utilizarem um recurso que requer o multi-factor Authentication. Eles se registram os métodos de multi-factor Authentication e os métodos SSPR (se o utilizador está ativado para SSPR).
* Registo SSPR imposto: É pedido aos utilizadores para se registar durante o início de sessão. Eles se registram apenas os métodos SSPR.
* Atualização SSPR imposta: Os utilizadores têm de rever as suas informações de segurança num intervalo definido pelo administrador. Os utilizadores são apresentados as informações e podem confirmar as informações atuais ou fazer alterações, se necessário.

Quando o registo é imposto, os utilizadores são apresentados o número mínimo de métodos necessários para estar em conformidade com as políticas de multi-factor Authentication e SSPR, da maioria pelo menos, proteger.

Por exemplo:

* Um utilizador está ativado para SSPR. A política da SSPR necessários dois métodos para repor e tiver ativado o código de aplicação móvel, e-mail e telefone.
   * Este utilizador é necessária para registar os dois métodos.
      * O utilizador é apresentado a aplicação de autenticação e phone por predefinição.
      * O utilizador pode optar por registar e-mail em vez de aplicação authenticator ou telefone.

Este fluxograma descreve os métodos são mostrados a um utilizador quando interrompido Registre durante o início de sessão:

![Fluxograma de informações de segurança combinada](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se tiver o multi-factor Authentication e SSPR ativada, é recomendável que imponha o registo de multi-factor Authentication.

Se a política da SSPR requer que os utilizadores rever as informações de segurança em intervalos regulares, os utilizadores são interrompidos durante o início de sessão e mostrados todos os seus métodos registados. Eles podem confirmar as informações atuais se ele é atualizado ou fazer alterações se precisarem.

### <a name="manage-mode"></a>Gerenciar o modo

Os utilizadores podem aceder a gerenciar o modo de acedendo a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) ou ao selecionar **informações de segurança** do meu perfil. A partir daí, os utilizadores podem adicionar métodos, eliminar ou alterar o método existente, alterar o método padrão e muito mais.

## <a name="key-usage-scenarios"></a>Cenários de utilização de chave

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante o início de sessão

Um administrador tem impostas registo.

Um utilizador não tiver configurado a todas as informações de segurança necessário e vai para o portal do Azure. Depois de introduzir o nome de utilizador e palavra-passe, é pedido ao utilizador para configurar informações de segurança. O utilizador, em seguida, seguir os passos apresentados no Assistente para configurar as informações de segurança necessário. Se as definições o permitirem, o utilizador pode optar por configurar os métodos que não tenham mostrada por padrão. Depois de concluir o assistente, os utilizadores rever os métodos configuraram e seu método de padrão de multi-factor Authentication. Para concluir o processo de configuração, o utilizador confirma as informações e continua ao portal do Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança do meu perfil

Um administrador não tenha imposto registo.

Um usuário que ainda não tiver configurado a todas as informações de segurança necessário ir para [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). O utilizador seleciona **informações de segurança** no painel esquerdo. A partir daí, o utilizador optar por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue os passos para configurar esse método. Quando terminar, o utilizador verá o método que apenas foi definido na página de informações de segurança.

### <a name="delete-security-info-from-my-profile"></a>Eliminar informações de segurança do meu perfil

Um utilizador que tiver definido previamente pelo menos um método navega para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O utilizador optar por eliminar um dos métodos registrados anteriormente. Quando terminar, o utilizador verá já não é esse método na página de informações de segurança.

### <a name="change-the-default-method-from-my-profile"></a>Alterar o método predefinido do meu perfil

Um utilizador que tiver definido previamente pelo menos um método que pode ser utilizado para o multi-factor Authentication navega para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O utilizador altera o atual método predefinido para um método padrão diferente. Quando terminar, o utilizador verá o novo método de predefinição na página de informações de segurança.

## <a name="next-steps"></a>Passos Seguintes

[Ativar o registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md)

[Métodos disponíveis para o multi-factor Authentication e SSPR](concept-authentication-methods.md)

[Configurar a reposição de palavra-passe self-service](howto-sspr-deployment.md)

[Configurar a autenticação Multifator do Azure](howto-mfa-getstarted.md)
