---
title: Registo combinado para SSPR do Azure AD e MFA (pré-visualização) - Azure Active Directory
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
ms.openlocfilehash: 2865c19e747ca1c5b0a6cda84b8be18bfaeb9335
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317668"
---
# <a name="combined-security-information-registration-preview"></a>Registo de informações de segurança combinados (pré-visualização)

Antes do registo combinado, utilizadores registados métodos de autenticação para o Azure multi-factor Authentication (MFA) e a reposição de palavra-passe self-service (SSPR) através de duas experiências diferentes. As pessoas estavam confusos que métodos semelhantes foram utilizados para o MFA do Azure e o SSPR, mas eles tinham que registe-se em separado para cada funcionalidade. Agora, com o registo de combinado, os utilizadores podem registar uma vez e obter os benefícios da MFA do Azure e SSPR.

![Combinar informações de segurança - meu perfil que mostra informações de segurança registada para um utilizador, incluindo o Microsoft Authenticator e telefone para um utilizador de exemplo no diretório.](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de ativar a nova experiência, reveja esta documentação voltada para o administrador e a documentação e focada no usuário para se certificar de que compreende a funcionalidade e o impacto desse recurso. Base seu treinamento sobre a documentação do utilizador para preparar os seus utilizadores para a nova experiência e ajudar a garantir uma implementação com êxito.

|     |
| --- |
| Registo de informações de segurança combinado para a reposição de palavra-passe self-service do Azure multi-factor Authentication e o Azure AD é uma funcionalidade de pré-visualização pública do Azure Active Directory. Para obter mais informações sobre pré-visualizações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

> [!IMPORTANT]
> Se um utilizador está ativado para a pré-visualização original e a experiência melhorada de registo combinado, verão a nova experiência. Os utilizadores que estão ativados para ambas as experiências só irão ver a nova experiência de meu perfil. O novo perfil de meus se alinha com o aspeto e funcionalidade de registo combinado e fornece uma experiência totalmente integrada para os usuários. Os utilizadores podem ver meu perfil ao aceder [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com).

As páginas de MyProfile são localizadas com base nas configurações de idioma atual na máquina de aceder à página. Microsoft armazena o mais recente idioma utilizado na cache do browser, para que tentativas subseqüentes de acesso a irão continuar a processar no último idioma utilizado. Limpar a cache fará com que as páginas para compor novamente. Se deseja forçar um idioma específico, adicionando um `?lng=de-DE` ao final do URL onde `de-DE` está definido para o idioma apropriado código irá forçar as páginas para renderizar nesse idioma.

![Minha interface de perfil que mostra informações de segurança e a capacidade de utilizador para SSPR ou outros métodos de verificação de segurança adicionais de configuração.](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-converged-registration"></a>Métodos disponíveis no registo convergido

Neste momento, o registo combinado suporta os seguintes métodos e as ações para esses métodos:

|   | Registar | Alterar | Eliminar |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (máx. 5) | Não | Sim |
| Outra aplicação de autenticador | Sim (máx. 5) | Não | Sim |
| Token de hardware | Não | Não | Sim |
| Telefone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone do escritório | Não | Não | Não |
| Email | Sim | Sim | Sim |
| Perguntas de segurança | Sim | Não | Sim |
| Palavras-passe de aplicações | Sim | Não | Sim |

> [!NOTE]
> As palavras-passe de aplicação só estão disponíveis para os utilizadores que tenham sido impostos para o MFA. As palavras-passe de aplicação não estão disponíveis para os utilizadores que estão ativados para a MFA através de uma política de acesso condicional.

Os utilizadores podem definir as seguintes opções como método de predefinição da MFA:

- Microsoft Authenticator – notificação
- Aplicação de autenticação ou do hardware do token – de código
- Chamada telefónica
- Mensagem de texto

À medida que Continuamos a adicionar mais métodos de autenticação desse tipo para o Azure AD, esses métodos vão estar disponíveis no registo combinado.

## <a name="combined-registration-modes"></a>Registo combinado modos

Existem dois "modos" do registo combinado: interrupção e gerir.

Modo de interrupção, é uma experiência semelhante ao assistente, mostrada a um utilizador quando registar ou atualizar suas informações de segurança em início de sessão.

Gerir o modo é parte do perfil do usuário e lhes permite gerir as suas informações de segurança.

Em ambos os modos, se um utilizador registou anteriormente um método que pode ser utilizado para a MFA, terão de executar a MFA antes de poderem aceder suas informações de segurança.

### <a name="interrupt-mode"></a>Modo de interrupção

Registo combinado respeita as políticas de MFA e o SSPR, se ambos estiverem ativadas para o seu inquilino. Controle estas políticas, se um utilizador for interrompido, registre durante o início de sessão e quais métodos estão disponíveis para se registar.

A seguir lista vários cenários em que um utilizador poderá ser solicitado a registar ou atualizar suas informações de segurança:

* Registo na MFA imposto através da proteção de identidade: Serão solicitados aos utilizadores Registre durante o início de sessão. Eles se registram os métodos MFA e os métodos SSPR (se o utilizador está ativado para SSPR).
* Registo na MFA imposto através de MFA por utilizador: Serão solicitados aos utilizadores Registre durante o início de sessão. Eles se registram os métodos MFA e os métodos SSPR (se o utilizador está ativado para SSPR).
* Registo na MFA imposto através de acesso condicional ou outras políticas: É pedido aos utilizadores se registem ao aceder a um recurso que requeira o MFA. Os utilizadores irão registar métodos MFA e os métodos SSPR (se o utilizador está ativado para SSPR).
* Registo SSPR imposto: É pedido aos utilizadores para se registar durante o início de sessão. Eles apenas registram métodos SSPR
* Atualização SSPR imposta: Os utilizadores têm de rever as suas informações de segurança num intervalo definido pelo administrador. Os utilizadores são apresentados as informações e podem escolher "Se parece bom" ou fazer alterações, se necessário.

Quando o registo é imposto, os utilizadores são apresentados o número mínimo de métodos necessários para estar em conformidade com as políticas MFA e o SSPR da maioria pelo menos, proteger.

Exemplo:

* Um utilizador está ativado para SSPR. A política da SSPR necessários dois métodos para repor e tiver ativado o código de aplicação móvel, e-mail e telefone.
   * Este utilizador é necessária para registar os dois métodos.
      * Eles-lhe apresentados a aplicação de autenticador e phone por predefinição.
      * O utilizador pode optar por registar e-mail em vez de aplicação authenticator ou telefone.

O fluxograma a seguir descreve os métodos são mostrados a um utilizador quando interrompido Registre durante o início de sessão:

![Combinados gráfico de fluxo de informações de segurança que explica o número de métodos necessários quando são necessárias mais informações ao iniciar sessão. Isto pode alterar se só é necessário o MFA ou apenas a SSPR](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se tiver a MFA e o SSPR ativada, é recomendável que imponha registo na MFA.

Se a política da SSPR requer que os utilizadores rever as informações de segurança em intervalos regulares, os utilizadores são interrompidos durante o início de sessão e mostrados todos os seus métodos registados. Podem escolher "Se parece bom" se as informações estão atualizadas ou podem escolher "Editar informações" para fazer alterações.

### <a name="manage-mode"></a>Gerenciar o modo

Os utilizadores podem aceder a gerenciar o modo de acedendo a [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo) ou ao selecionar "Informações de segurança" meu perfil. A partir daí, os utilizadores podem adicionar métodos, eliminar ou alterar o método existente, alterar o método padrão e muito mais.

## <a name="key-usage-scenarios"></a>Cenários de utilização de chave

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante o início de sessão

Um administrador tem impostas registo.

Um utilizador não tiver configurado a todas as informações de segurança necessário e navega para o portal do Azure. Depois de introduzir o nome de utilizador e palavra-passe, é pedido ao utilizador para configurar informações de segurança. O utilizador, em seguida, seguir os passos apresentados no Assistente para configurar as informações de segurança necessário. O utilizador pode optar por configurar os métodos que não seja o que é mostrado por padrão, se as definições permitirem. No final do assistente, o utilizador analisa os métodos configuraram e seu método padrão para a MFA. Para concluir o processo de configuração, o utilizador confirma as informações e continua ao portal do Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança do meu perfil

Um administrador não tenha imposto registo.

Um utilizador que não tenha ainda configurado a todas as informações de segurança necessário navega para [ https://myprofile.microsoft.com ](https://myprofile.microsoft.com). O utilizador, em seguida, escolhe **informações de segurança** no painel de navegação esquerda. A partir daí, o utilizador optar por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue os passos para configurar esse método. Quando terminar, o utilizador verá o método que acabamos de definir na página de informações de segurança.

### <a name="delete-security-info-from-my-profile"></a>Eliminar informações de segurança do meu perfil

Um utilizador que tiver definido previamente pelo menos um método navega para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O utilizador optar por eliminar um dos métodos registrados anteriormente. Quando terminar, o utilizador verá já não é esse método na página de informações de segurança.

### <a name="change-default-method-from-my-profile"></a>Alterar o método predefinido do meu perfil

Um utilizador que tiver definido previamente pelo menos um método que pode ser utilizado para a MFA navega para [ https://aka.ms/mysecurityinfo ](https://aka.ms/mysecurityinfo). O utilizador altera o método de predefinição atual para um método padrão diferente. Quando terminar, o utilizador verá o novo método de predefinição na página de informações de segurança.

## <a name="next-steps"></a>Passos Seguintes

[Ativar o registo combinado no seu inquilino](howto-registration-mfa-sspr-combined.md)

[Métodos disponíveis para a MFA e o SSPR](concept-authentication-methods.md)

[Configurar a reposição de palavra-passe self-service](howto-sspr-deployment.md)

[Configurar a autenticação Multifator do Azure](howto-mfa-getstarted.md)
