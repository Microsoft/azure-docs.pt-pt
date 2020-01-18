---
title: Visão geral da autenticação de Azure Active Directory
description: Saiba mais sobre os diferentes métodos de autenticação e recursos de segurança para entradas de usuário com Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/17/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f4c2fa8488490561e8f11746e8e737718ee9f37
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/17/2020
ms.locfileid: "76261267"
---
# <a name="what-is-azure-active-directory-authentication"></a>O que é Azure Active Directory autenticação?

Um dos principais recursos de uma plataforma de identidade é verificar ou *autenticar*credenciais quando um usuário entra em um dispositivo, aplicativo ou serviço. No Azure Active Directory (AD do Azure), a autenticação envolve mais do que apenas a verificação de um nome de usuário e senha. Para aumentar a segurança e reduzir a necessidade de assistência técnica, a autenticação do Azure AD inclui os seguintes componentes:

* Reposição personalizada de palavra-passe
* Multi-Factor Authentication do Azure
* Integração híbrida para gravar alterações de senha de volta no ambiente local
* Integração híbrida para impor políticas de proteção de senha para um ambiente local
* Autenticação sem palavra-passe

## <a name="improve-the-end-user-experience"></a>Melhorar a experiência do usuário final

O Azure AD ajuda a proteger a identidade de um usuário e a simplificar sua experiência de entrada. Recursos como redefinição de senha de autoatendimento permitem que os usuários atualizem ou alterem suas senhas usando um navegador da Web de qualquer dispositivo. Esse recurso é especialmente útil quando o usuário esqueceu sua senha ou sua conta está bloqueada. Sem esperar que um administrador ou assistência técnica forneça suporte, um usuário pode desbloquear a si mesmo e continuar a trabalhar.

A autenticação multifator do Azure permite que os usuários escolham uma forma adicional de autenticação durante a entrada, como uma chamada telefônica ou uma notificação de aplicativo móvel. Essa capacidade reduz o requisito para uma forma fixa única de autenticação secundária, como um token de hardware. Se o usuário não tiver uma forma de autenticação adicional no momento, ele poderá escolher um método diferente e continuar funcionando.

![Métodos de autenticação em uso na tela de entrada](media/concept-authentication-methods/overview-login.png)

A autenticação sem senha elimina a necessidade de o usuário criar e lembrar uma senha segura. Recursos como o Windows Hello para empresas ou chaves de segurança FIDO2 permitem que os usuários entrem em um dispositivo ou aplicativo sem uma senha. Essa capacidade pode reduzir a complexidade do gerenciamento de senhas em diferentes ambientes.

## <a name="self-service-password-reset"></a>Reposição personalizada de palavra-passe

A redefinição de senha de autoatendimento dá aos usuários a capacidade de alterar ou redefinir sua senha, sem envolvimento de administrador ou suporte técnico. Se a conta de um usuário estiver bloqueada ou se esquecerem da senha, elas poderão seguir os prompts para desbloquear e voltar para o trabalho. Essa capacidade reduz as chamadas de suporte técnico e a perda de produtividade quando um usuário não consegue entrar no dispositivo ou em um aplicativo.

A redefinição de senha de autoatendimento funciona nos seguintes cenários:

* **Alteração de senha –** quando um usuário sabe sua senha, mas deseja alterá-la para algo novo.
* **Redefinição de senha –** quando um usuário não pode entrar, como quando esqueceu a senha e deseja redefinir sua senha.
* **Desbloqueio de conta –** quando um usuário não pode entrar porque sua conta está bloqueada e deseja desbloquear sua conta.

Quando um usuário atualiza ou redefine sua senha usando a redefinição de senha de autoatendimento, essa senha também pode ser gravada novamente em um ambiente de Active Directory local. O Write-back de senha garante que um usuário possa usar imediatamente suas credenciais atualizadas com dispositivos e aplicativos locais.

## <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

A autenticação multifator é um processo em que um usuário é solicitado durante o processo de entrada para obter uma forma adicional de identificação, como inserir um código em seu celular ou para fornecer uma verificação de impressão digital.

Se você usar apenas uma senha para autenticar um usuário, ele deixará um vetor inseguro para ataque. Se a senha for fraca ou se tiver sido exposta em outro lugar, é realmente o usuário entrar com o nome de usuário e a senha, ou é um invasor? Quando você precisa de uma segunda forma de autenticação, a segurança é aumentada, pois esse fator adicional não é algo fácil para um invasor obter ou duplicar.

![Imagem conceitual das diferentes formas de autenticação multifator](./media/concept-mfa-howitworks/methods.png)

A autenticação multifator do Azure funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe, normalmente uma senha.
* Algo que você tem, como um dispositivo confiável que não é facilmente duplicado, como um telefone ou uma chave de hardware.
* Algo que você é uma biometria, como uma impressão digital ou uma verificação facial.

Os usuários podem se registrar para a redefinição de senha de autoatendimento e a autenticação multifator do Azure em uma etapa para simplificar a experiência de integração. Os administradores podem definir quais formas de autenticação secundária podem ser usadas. A autenticação multifator do Azure também pode ser necessária quando os usuários executam uma redefinição de senha de autoatendimento para proteger ainda mais esse processo.

## <a name="password-protection"></a>Proteção por senha

Por padrão, o Azure AD bloqueia senhas fracas, como *password1*. Uma lista de senhas proibidas global é automaticamente atualizada e imposta que inclui senhas fracas conhecidas. Se um usuário do Azure AD tentar definir sua senha para uma dessas senhas fracas, ele receberá uma notificação para escolher uma senha mais segura.

Para aumentar a segurança, você pode definir políticas de proteção de senha personalizadas. Essas políticas podem usar filtros para bloquear qualquer variação de uma senha que contenha um nome como a *contoso* ou um local como *London*, por exemplo.

Para segurança híbrida, você pode integrar a proteção de senha do Azure AD a um ambiente de Active Directory local. Um componente instalado no ambiente local recebe a lista de senhas excluídas globais e as políticas de proteção de senha personalizadas do Azure AD e os controladores de domínio as usam para processar eventos de alteração de senha. Essa abordagem híbrida garante que, independentemente de como ou onde um usuário altere suas credenciais, você impõe o uso de senhas fortes.

## <a name="passwordless-authentication"></a>Autenticação sem palavra-passe

A meta final para muitos ambientes é remover o uso de senhas como parte dos eventos de entrada. Recursos como a proteção de senha do Azure ou a autenticação multifator do Azure ajudam a melhorar a segurança, mas um nome de usuário e senha permanecem como uma forma fraca de autenticação que pode ser exposta ou atacado por força bruta.

![Segurança versus conveniência com o processo de autenticação que leva a senhas](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Quando você entra com um método sem senha, as credenciais são fornecidas por meio do uso de métodos como biometria com o Windows Hello para empresas ou uma chave de segurança FIDO2. Esses métodos de autenticação não podem ser facilmente duplicados por um invasor.

O Azure AD fornece maneiras de se autenticar nativamente usando métodos sem senha para simplificar a experiência de entrada para os usuários e reduzir o risco de ataques.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o tutorial [de início rápido para redefinição de senha por autoatendimento][quickstart-sspr] e [autenticação multifator do Azure][tutorial-mfa-applications].

Para saber mais sobre os conceitos de redefinição de senha de autoatendimento, confira [como funciona a redefinição de senha de autoatendimento do Azure ad][concept-sspr].

Para saber mais sobre os conceitos da autenticação multifator, confira [como funciona a autenticação multifator do Azure][concept-mfa].

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
