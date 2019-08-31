---
title: Registro combinado para o Azure AD SSPR e autenticação multifator (versão prévia)-Azure Active Directory
description: Autenticação multifator do Azure AD e registro de redefinição de senha de autoatendimento (versão prévia)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f3490e9cb62aa2d5c18b8fd564796dd6d3946c
ms.sourcegitcommit: 19a821fc95da830437873d9d8e6626ffc5e0e9d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70162423"
---
# <a name="combined-security-information-registration-preview"></a>Registro de informações de segurança combinadas (versão prévia)

Antes do registro combinado, os usuários registraram os métodos de autenticação para a autenticação multifator do Azure e a SSPR (redefinição de senha de autoatendimento) separadamente. As pessoas foram confundidas que métodos semelhantes eram usados para autenticação multifator e SSPR, mas tinham que se registrar para ambos os recursos. Agora, com o registro combinado, os usuários podem registrar uma vez e obter os benefícios da autenticação multifator e do SSPR.

![Meu perfil mostrando informações de segurança registradas para um usuário](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

Antes de habilitar a nova experiência, examine esta documentação voltada para o administrador e a documentação voltada para o usuário para garantir que você compreenda a funcionalidade e o efeito desse recurso. Baseie seu treinamento na [documentação do usuário](../user-help/user-help-security-info-overview.md) para preparar seus usuários para a nova experiência e para ajudar a garantir uma distribuição bem-sucedida.

O registro de informações de segurança combinadas do Azure AD não está disponível atualmente para nuvens nacionais como Azure EUA governamental, Azure Alemanha ou Azure China 21Vianet.

|     |
| --- |
| O registro de informações de segurança combinadas para a autenticação multifator e a redefinição de senha de autoatendimento do Azure Active Directory (Azure AD) é um recurso de visualização pública do Azure AD. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

> [!IMPORTANT]
> Os usuários que estão habilitados para a versão prévia original e a experiência de registro combinado aprimorada verão o novo comportamento. Os usuários que estiverem habilitados para as duas experiências verão apenas a nova experiência meu perfil. O novo meu perfil se alinha com a aparência do registro combinado e fornece uma experiência direta para os usuários. Os usuários podem ver meu perfil acessando [https://myprofile.microsoft.com](https://myprofile.microsoft.com).

Minhas páginas de perfil são localizadas com base nas configurações de idioma do computador que está acessando a página. A Microsoft armazena a linguagem mais recente usada no cache do navegador, portanto, as tentativas subsequentes de acessar as páginas continuarão a ser renderizadas no último idioma usado. Se você limpar o cache, as páginas serão renderizadas novamente. Se você quiser forçar um idioma específico, poderá adicionar `?lng=<language>` ao final da URL, em que `<language>` é o código do idioma que você deseja renderizar.

![Configurar SSPR ou outros métodos de verificação de segurança](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>Métodos disponíveis no registro combinado

O registro combinado dá suporte aos seguintes métodos e ações de autenticação:

|   | Registar | Alterar | Eliminar |
| --- | --- | --- | --- |
| Microsoft Authenticator | Sim (no máximo 5) | Não | Sim |
| Outro aplicativo autenticador | Sim (no máximo 5) | Não | Sim |
| Token de hardware | Não | Não | Sim |
| Phone | Sim | Sim | Sim |
| Telefone alternativo | Sim | Sim | Sim |
| Telefone do escritório | Não | Não | Não |
| Email | Sim | Sim | Sim |
| Perguntas de segurança | Sim | Não | Sim |
| Palavras-passe de aplicações | Sim | Não | Sim |

> [!NOTE]
> As senhas de aplicativo estão disponíveis somente para usuários que foram impostos para a autenticação multifator. As senhas de aplicativo não estão disponíveis para usuários que estão habilitados para autenticação multifator por meio de uma política de acesso condicional.

Os usuários podem definir uma das seguintes opções como o método de autenticação multifator padrão:

- Microsoft Authenticator – notificação.
- Aplicativo autenticador ou token de hardware – código.
- Chamada telefônica.
- Mensagem de texto.

À medida que continuarmos adicionando mais métodos de autenticação ao Azure AD, esses métodos estarão disponíveis no registro combinado.

## <a name="combined-registration-modes"></a>Modos de registro combinados

Há dois modos de registro combinado: interrupção e gerenciamento.

- O **modo de interrupção** é uma experiência semelhante a um assistente, apresentada aos usuários quando eles registram ou atualizam suas informações de segurança na entrada.

- O **modo gerenciar** faz parte do perfil do usuário e permite que os usuários gerenciem suas informações de segurança.

Para ambos os modos, os usuários que registraram anteriormente um método que pode ser usado para a autenticação multifator precisarão executar a autenticação multifator antes de poderem acessar suas informações de segurança.

### <a name="interrupt-mode"></a>Modo de interrupção

O registro combinado respeita as políticas de autenticação multifator e SSPR, se ambas estiverem habilitadas para seu locatário. Essas políticas controlam se um usuário é interrompido para registro durante a entrada e quais métodos estão disponíveis para o registro.

Aqui estão vários cenários em que os usuários podem ser solicitados a registrar ou atualizar suas informações de segurança:

- Registro de autenticação multifator imposto por meio da proteção de identidade: Os usuários são solicitados a se registrar durante a entrada. Eles registram métodos de autenticação multifator e métodos SSPR (se o usuário estiver habilitado para SSPR).
- Registro de autenticação multifator imposto por meio da autenticação multifator por usuário: Os usuários são solicitados a se registrar durante a entrada. Eles registram métodos de autenticação multifator e métodos SSPR (se o usuário estiver habilitado para SSPR).
- Registro de autenticação multifator imposto por meio de acesso condicional ou outras políticas: Os usuários são solicitados a se registrar quando usam um recurso que requer autenticação multifator. Eles registram métodos de autenticação multifator e métodos SSPR (se o usuário estiver habilitado para SSPR).
- Registro de SSPR imposto: Os usuários são solicitados a se registrar durante a entrada. Eles registram apenas os métodos SSPR.
- SSPR atualização imposta: Os usuários são obrigados a revisar suas informações de segurança em um intervalo definido pelo administrador. Os usuários são mostrados suas informações e podem confirmar as informações atuais ou fazer alterações, se necessário.

Quando o registro é imposto, os usuários são mostrados o número mínimo de métodos necessários para serem compatíveis com as políticas de autenticação multifator e SSPR, do mais para o menos seguro.

Por exemplo:

- Um usuário está habilitado para SSPR. A política SSPR exigiu dois métodos para redefinir e habilitou o código, o email e o telefone do aplicativo móvel.
   - Esse usuário é necessário para registrar dois métodos.
      - Por padrão, o usuário é mostrado aplicativo autenticador e telefone.
      - O usuário pode optar por registrar o email em vez do aplicativo autenticador ou telefone.

Este fluxograma descreve quais métodos são mostrados para um usuário quando interrompido para registro durante a entrada:

![Fluxograma de informações de segurança combinadas](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

Se você tiver a autenticação multifator e a SSPR habilitada, recomendamos que você imponha o registro da autenticação multifator.

Se a política de SSPR exigir que os usuários revisem suas informações de segurança em intervalos regulares, os usuários serão interrompidos durante a entrada e mostrarão todos os seus métodos registrados. Eles podem confirmar as informações atuais se estiverem atualizadas ou poderão fazer alterações se precisarem dela.

### <a name="manage-mode"></a>Modo de gerenciamento

Os usuários podem acessar o modo [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo) de gerenciamento acessando ou selecionando informações de **segurança** em meu perfil. A partir daí, os usuários podem adicionar métodos, excluir ou alterar métodos existentes, alterar o método padrão e muito mais.

## <a name="key-usage-scenarios"></a>Principais cenários de uso

### <a name="set-up-security-info-during-sign-in"></a>Configurar informações de segurança durante a entrada

Um administrador impôs o registro.

Um usuário não configurou todas as informações de segurança necessárias e vai para a portal do Azure. Depois de inserir o nome de usuário e a senha, o usuário será solicitado a configurar as informações de segurança. Em seguida, o usuário segue as etapas mostradas no Assistente para configurar as informações de segurança necessárias. Se suas configurações permitirem, o usuário poderá optar por configurar métodos diferentes daqueles mostrados por padrão. Depois de concluir o assistente, os usuários examinam os métodos que eles configuram e seu método padrão para a autenticação multifator. Para concluir o processo de instalação, o usuário confirma as informações e continua a portal do Azure.

### <a name="set-up-security-info-from-my-profile"></a>Configurar informações de segurança do meu perfil

Um administrador não impô o registro.

Um usuário que ainda não configurou todas as informações de segurança necessárias [https://myprofile.microsoft.com](https://myprofile.microsoft.com)vai para. O usuário seleciona **informações de segurança** no painel esquerdo. A partir daí, o usuário opta por adicionar um método, seleciona qualquer um dos métodos disponíveis e segue as etapas para configurar esse método. Quando terminar, o usuário verá o método que acabou de ser configurado na página informações de segurança.

### <a name="delete-security-info-from-my-profile"></a>Excluir informações de segurança do meu perfil

Um usuário que tenha configurado anteriormente pelo menos um método navega para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). O usuário escolhe excluir um dos métodos registrados anteriormente. Quando terminar, o usuário não verá mais esse método na página informações de segurança.

### <a name="change-the-default-method-from-my-profile"></a>Alterar o método padrão do meu perfil

Um usuário que configurou anteriormente pelo menos um método que pode ser usado para a autenticação multifator navega para [https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo). O usuário altera o método padrão atual para um método padrão diferente. Quando terminar, o usuário verá o novo método padrão na página informações de segurança.

## <a name="next-steps"></a>Passos Seguintes

[Forçar os usuários a registrar novamente os métodos de autenticação](howto-mfa-userdevicesettings.md#manage-authentication-methods)

[Habilitar o registro combinado em seu locatário](howto-registration-mfa-sspr-combined.md)

[Relatórios de uso e informações do SSPR e MFA](howto-authentication-methods-usage-insights.md)

[Métodos disponíveis para autenticação multifator e SSPR](concept-authentication-methods.md)

[Configurar a redefinição de senha de autoatendimento](howto-sspr-deployment.md)

[Configurar a autenticação multifator do Azure](howto-mfa-getstarted.md)
