---
title: Visão geral da autenticação do Diretório Ativo Azure
description: Conheça os diferentes métodos de autenticação e funcionalidades de segurança para os sessão de entrada do utilizador com o Diretório Ativo Azure.
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76261267"
---
# <a name="what-is-azure-active-directory-authentication"></a>O que é a autenticação do Diretório Ativo Azure?

Uma das principais características de uma plataforma de identidade é verificar, ou *autenticar,* credenciais quando um utilizador assina num dispositivo, aplicação ou serviço. No Azure Ative Directory (Azure AD), a autenticação envolve mais do que apenas a verificação de um nome de utilizador e palavra-passe. Para melhorar a segurança e reduzir a necessidade de assistência ao balcão de ajuda, a autenticação da AD Azure inclui os seguintes componentes:

* Reposição personalizada de palavra-passe
* Multi-Factor Authentication do Azure
* Integração híbrida para escrever alterações de palavra-passe de volta ao ambiente no local
* Integração híbrida para impor políticas de proteção de senhas para um ambiente no local
* Autenticação sem palavra-passe

## <a name="improve-the-end-user-experience"></a>Melhorar a experiência do utilizador final

A Azure AD ajuda a proteger a identidade de um utilizador e a simplificar a sua experiência de entrada. Funcionalidades como o reset de palavra-passe self-service permitem aos utilizadores atualizar ou alterar as suas palavras-passe usando um navegador web de qualquer dispositivo. Esta funcionalidade é especialmente útil quando o utilizador se esqueceu da sua palavra-passe ou a sua conta está bloqueada. Sem esperar que um helpdesk ou administrador forneça suporte, um utilizador pode desbloquear-se e continuar a trabalhar.

A Autenticação Multi-Factor Azure permite que os utilizadores escolham uma forma adicional de autenticação durante o início de sessão, como uma chamada telefónica ou uma notificação de aplicação móvel. Esta capacidade reduz o requisito para uma única forma fixa de autenticação secundária como um símbolo de hardware. Se o utilizador não tiver atualmente uma forma de autenticação adicional, pode escolher um método diferente e continuar a trabalhar.

![Métodos de autenticação em uso no ecrã de início de sessão](media/concept-authentication-methods/overview-login.png)

A autenticação sem palavras-passe remove a necessidade de o utilizador criar e lembrar-se de uma palavra-passe segura. Capacidades como o Windows Hello for Business ou as chaves de segurança FIDO2 permitem que os utilizadores instem um dispositivo ou aplicação sem senha. Esta capacidade pode reduzir a complexidade da gestão de senhas em diferentes ambientes.

## <a name="self-service-password-reset"></a>Reposição personalizada de palavra-passe

O reset da palavra-passe self-service dá aos utilizadores a possibilidade de alterar ou redefinir a sua palavra-passe, sem qualquer envolvimento de administrador ou de secretária. Se a conta de um utilizador estiver bloqueada ou se esquecer em palavra-passe, pode seguir as indicações para se desbloquear em si e voltar ao trabalho. Esta capacidade reduz as chamadas de secretária e a perda de produtividade quando um utilizador não pode iniciar sessão no seu dispositivo ou numa aplicação.

O reset da palavra-passe self-service funciona nos seguintes cenários:

* **Mudança de palavra-passe -** quando um utilizador conhece a sua palavra-passe mas quer mudá-la para algo novo.
* **Reset de palavra-passe -** quando um utilizador não pode iniciar sessão, como quando se esqueceu da palavra-passe, e quer redefinir a sua palavra-passe.
* **Desbloqueio da conta -** quando um utilizador não pode iniciar sessão porque a sua conta está bloqueada e quer desbloquear a sua conta.

Quando um utilizador atualiza ou repõe a sua palavra-passe utilizando o reset da palavra-passe self-service, essa palavra-passe também pode ser reescrita para um ambiente de Diretório Ativo no local. A reescrita da palavra-passe garante que um utilizador pode utilizar imediatamente as suas credenciais atualizadas com dispositivos e aplicações no local.

## <a name="azure-multi-factor-authentication"></a>Multi-Factor Authentication do Azure

A autenticação de vários fatores é um processo em que um utilizador é solicitado durante o processo de iniciar sessão para uma forma adicional de identificação, como introduzir um código no seu telemóvel ou fornecer uma digitalização de impressões digitais.

Se usar apenas uma palavra-passe para autenticar um utilizador, deixa um vetor inseguro para o ataque. Se a palavra-passe é fraca ou foi exposta noutro local, será realmente o utilizador a iniciar sessão com o nome de utilizador e a palavra-passe, ou se é um intruso? Quando se exige uma segunda forma de autenticação, a segurança é aumentada, uma vez que este fator adicional não é algo que seja fácil para um intruso obter ou duplicar.

![Imagem conceptual das diferentes formas de autenticação multifactor](./media/concept-mfa-howitworks/methods.png)

A autenticação multi-factor Azure funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que sabe, tipicamente uma senha.
* Algo que tenha, como um dispositivo de confiança que não é facilmente duplicado, como uma chave de telefone ou hardware.
* Algo que tu és, biometria como uma impressão digital ou um exame facial.

Os utilizadores podem registar-se tanto para o reset de palavra-passe self-service como para a Autenticação Multi-Factor Azure num só passo para simplificar a experiência de embarque. Os administradores podem definir quais as formas de autenticação secundária que podem ser utilizadas. A autenticação de multi-factores Azure também pode ser exigida quando os utilizadores realizam uma redefinição da palavra-passe self-service para garantir mais esse processo.

## <a name="password-protection"></a>Proteção de senhas

Por predefinição, o Azure AD bloqueia senhas fracas como *passwords*1 . Uma lista global de senhas proibidas é automaticamente atualizada e executada que inclui senhas fracas conhecidas. Se um utilizador da AD Azure tentar definir a sua palavra-passe numa destas palavras-passe fracas, recebe uma notificação para escolher uma palavra-passe mais segura.

Para aumentar a segurança, pode definir políticas personalizadas de proteção de senhas. Estas políticas podem usar filtros para bloquear qualquer variação de uma palavra-passe contendo um nome como *Contoso* ou um local como *Londres,* por exemplo.

Para segurança híbrida, pode integrar a proteção de senhas Azure AD com um ambiente de Diretório Ativo no local. Um componente instalado no ambiente on-prem recebe a lista global de passwords proibida e políticas personalizadas de proteção de passwords da Azure AD, e os controladores de domínio usam-nos para processar eventos de mudança de palavra-passe. Esta abordagem híbrida garante que, independentemente de como ou onde um utilizador mude as suas credenciais, aplica o uso de senhas fortes.

## <a name="passwordless-authentication"></a>Autenticação sem palavra-passe

O objetivo final para muitos ambientes é remover o uso de senhas como parte de eventos de início de sessão. Funcionalidades como a proteção de passwords Azure ou a Autenticação Multi-Factor Azure ajudam a melhorar a segurança, mas um nome de utilizador e uma palavra-passe permanecem uma forma fraca de autenticação que pode ser exposta ou bruta-força atacada.

![Segurança versus conveniência com o processo de autenticação que leva a palavra-passe sem palavras-passe](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Quando se insere com um método sem palavras-passe, as credenciais são fornecidas através da utilização de métodos como biometria com o Windows Hello for Business ou uma chave de segurança FIDO2. Estes métodos de autenticação não podem ser facilmente duplicados por um intruso.

A Azure AD fornece formas de autenticar de forma nativa usando métodos sem palavras-passe para simplificar a experiência de entrada para os utilizadores e reduzir o risco de ataques.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o [quickstart para reset de senha de autosserviço][quickstart-sspr] e tutorial de [autenticação multi-factor Azure][tutorial-mfa-applications].

Para saber mais sobre conceitos de redefinição de senha de autosserviço, consulte como funciona o reset da [palavra-passe autosserviço da Azure AD][concept-sspr].

Para saber mais sobre conceitos de autenticação multifactor, consulte como funciona a [autenticação multi-factor Azure.][concept-mfa]

<!-- INTERNAL LINKS -->
[quickstart-sspr]: quickstart-sspr.md
[tutorial-mfa-applications]: tutorial-mfa-applications.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
