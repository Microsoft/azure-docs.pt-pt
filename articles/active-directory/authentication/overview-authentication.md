---
title: Visão geral da autenticação do Azure Ative Directory
description: Saiba mais sobre os diferentes métodos de autenticação e funcionalidades de segurança para os insusentus do utilizador com o Azure Ative Directory.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: overview
ms.date: 01/20/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: sahenry, michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae7ae239493c999d98100bf9dee4f4f0183addf2
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660818"
---
# <a name="what-is-azure-active-directory-authentication"></a>O que é a autenticação do Azure Ative Directory?

Uma das principais características de uma plataforma de identidade é verificar, ou *autenticar,* credenciais quando um utilizador assina um dispositivo, aplicação ou serviço. No Azure Ative Directory (Azure AD), a autenticação envolve mais do que apenas a verificação de um nome de utilizador e palavra-passe. Para melhorar a segurança e reduzir a necessidade de assistência ao balcão de ajuda, a autenticação AZure AD inclui os seguintes componentes:

* Reposição personalizada de palavra-passe
* Multi-Factor Authentication do Azure AD
* Integração híbrida para escrever alterações de palavra-passe de volta ao ambiente no local
* Integração híbrida para impor políticas de proteção de senhas para um ambiente no local
* Autenticação sem palavra-passe

Veja o nosso pequeno vídeo para saber mais sobre estes componentes de autenticação.

## <a name="improve-the-end-user-experience"></a>Melhorar a experiência do utilizador final

O Azure AD ajuda a proteger a identidade de um utilizador e a simplificar a sua experiência de inscrição. Funcionalidades como o reset da palavra-passe de autosserviço permitem que os utilizadores atualizem ou alterem as suas palavras-passe utilizando um navegador web a partir de qualquer dispositivo. Esta funcionalidade é especialmente útil quando o utilizador se esqueceu da sua palavra-passe ou se a sua conta está bloqueada. Sem esperar que um helpdesk ou administrador forneça suporte, um utilizador pode desbloquear-se e continuar a trabalhar.

A autenticação multi-factor Azure AD permite que os utilizadores escolham uma forma adicional de autenticação durante a sação, como uma chamada telefónica ou notificação de aplicações móveis. Esta capacidade reduz a exigência de uma única forma fixa de autenticação secundária como um token de hardware. Se o utilizador não tiver atualmente uma forma de autenticação adicional, pode escolher um método diferente e continuar a trabalhar.

![Métodos de autenticação em uso no ecrã de inscrição](media/concept-authentication-methods/overview-login.png)

A autenticação sem palavra-passe remove a necessidade de o utilizador criar e lembrar uma senha segura. Capacidades como o Windows Hello for Business ou as teclas de segurança FIDO2 permitem que os utilizadores entrem num dispositivo ou aplicação sem senha. Esta capacidade pode reduzir a complexidade da gestão de senhas em diferentes ambientes.

## <a name="self-service-password-reset"></a>Reposição personalizada de palavra-passe

O reset da palavra-passe de autosserviço dá aos utilizadores a capacidade de alterar ou redefinir a sua palavra-passe, sem qualquer administrador ou envolvimento do balcão de ajuda. Se a conta de um utilizador estiver bloqueada ou se esquecerem da sua palavra-passe, podem seguir as instruções para se desbloquearem e voltarem ao trabalho. Esta capacidade reduz as chamadas de secretária de ajuda e a perda de produtividade quando um utilizador não pode iniciar sômata ao seu dispositivo ou a uma aplicação.

A redefinição da palavra-passe de autosserviço funciona nos seguintes cenários:

* **Mudança de palavra-passe -** quando um utilizador sabe a sua palavra-passe mas quer mudá-la para algo novo.
* **Redefinição da palavra-passe -** quando um utilizador não pode iniciar sposição, como quando se esqueceu da palavra-passe, e quer redefinir a sua palavra-passe.
* **Desbloqueie a conta -** quando um utilizador não pode iniciar scontabilidade porque a sua conta está bloqueada e quer desbloquear a sua conta.

Quando um utilizador atualiza ou reinicia a sua palavra-passe utilizando a palavra-passe de autosserviço, essa palavra-passe também pode ser escrita de volta para um ambiente de Ative Directory no local. O writeback da palavra-passe garante que um utilizador pode usar imediatamente as suas credenciais atualizadas com dispositivos e aplicações no local.

## <a name="azure-ad-multi-factor-authentication"></a>Multi-Factor Authentication do Azure AD

A autenticação multifator é um processo no qual é pedido a um utilizador uma forma adicional de identificação durante o início de sessão, tal como a introdução de um código no respetivo telemóvel ou a análise de impressões digitais.

Se utilizar apenas uma palavra-passe para autenticar um utilizador, deixa um vetor inseguro para o ataque. Se a palavra-passe é fraca ou foi exposta em outro lugar, é realmente o utilizador que está a iniciar sessão com o nome de utilizador e a palavra-passe, ou é um intruso? Quando se precisa de uma segunda forma de autenticação, a segurança é aumentada, uma vez que este fator adicional não é algo que seja fácil para um intruso obter ou duplicar.

![Imagem conceptual das diferentes formas de autenticação multi-factor](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que sabe, tipicamente uma senha.
* Algo que você tem, como um dispositivo de confiança que não é facilmente duplicado, como um telefone ou chave de hardware.
* Algo que tu és, biometria como uma impressão digital ou uma tomografia facial.

Os utilizadores podem registar-se tanto para o reset da palavra-passe de autosserviço como para a autenticação multi-factor Ad Azure, num passo para simplificar a experiência de embarque. Os administradores podem definir que formas de autenticação secundária podem ser utilizadas. A autenticação multi-factor Azure AD também pode ser necessária quando os utilizadores efetuam uma redefinição de senha de autosserviço para garantir ainda mais esse processo.

## <a name="password-protection"></a>Proteção por palavra-passe

Por padrão, a Azure AD bloqueia senhas fracas como *a Password1*. Uma lista global de palavras-passe proibida é automaticamente atualizada e aplicada que inclui senhas fracas conhecidas. Se um utilizador AD Azure tentar definir a sua palavra-passe numa destas palavras-passe fracas, receberá uma notificação para escolher uma palavra-passe mais segura.

Para aumentar a segurança, pode definir políticas de proteção de senhas personalizadas. Estas políticas podem usar filtros para bloquear qualquer variação de uma palavra-passe que contenha um nome como *Contoso* ou um local como *Londres,* por exemplo.

Para a segurança híbrida, pode integrar a proteção de senha AZure AD com um ambiente de Ative Directory no local. Um componente instalado no ambiente on-prem recebe a lista global de senhas proibidas e as políticas de proteção de senhas personalizadas do Azure AD, e os controladores de domínio usam-nas para processar eventos de mudança de palavras-passe. Esta abordagem híbrida garante que, independentemente de como ou onde um utilizador mude as suas credenciais, aplica o uso de senhas fortes.

## <a name="passwordless-authentication"></a>Autenticação sem palavra-passe

O objetivo final para muitos ambientes é remover o uso de senhas como parte de eventos de entrada. Funcionalidades como a proteção de passwords Azure ou Azure AD Multi-Factor Authentication ajudam a melhorar a segurança, mas um nome de utilizador e palavra-passe continua a ser uma forma fraca de autenticação que pode ser exposta ou atacada pela força bruta.

![Segurança versus conveniência com o processo de autenticação que leva a sem palavras-passe](./media/concept-authentication-passwordless/passwordless-convenience-security.png)

Quando se inscreve com um método sem palavras-passe, as credenciais são fornecidas utilizando métodos como biometria com Windows Hello for Business ou uma chave de segurança FIDO2. Estes métodos de autenticação não podem ser facilmente duplicados por um intruso.

O Azure AD fornece formas de autenticar de forma nativa utilizando métodos sem palavras-passe para simplificar a experiência de entrada para os utilizadores e reduzir o risco de ataques.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte o [tutorial para reset de senha de autosserviço (SSPR)][tutorial-sspr] e [autenticação multi-factor AD Azure][tutorial-azure-mfa].

Para saber mais sobre conceitos de redefinição de senha de autosserviço, consulte [como funciona o reset da palavra-passe de autosserviço AD Azure][concept-sspr].

Para saber mais sobre conceitos de autenticação de vários fatores, consulte [como funciona a autenticação multi-factor Ad Azure.][concept-mfa]

<!-- INTERNAL LINKS -->
[tutorial-sspr]: tutorial-enable-sspr.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[concept-sspr]: concept-sspr-howitworks.md
[concept-mfa]: concept-mfa-howitworks.md
