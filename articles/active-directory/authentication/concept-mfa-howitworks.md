---
title: Visão geral da autenticação multi-factor Azure AD
description: Saiba como a autenticação multi-factor Azure AD ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um simples processo de inscrição.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cc5227cb4b5de02cba65a60dd469da93d3767e5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96744062"
---
# <a name="how-it-works-azure-ad-multi-factor-authentication"></a>Como funciona: Autenticação multi-factor Azure AD

A autenticação multifator é um processo no qual é pedido a um utilizador uma forma adicional de identificação durante o início de sessão, tal como a introdução de um código no respetivo telemóvel ou a análise de impressões digitais.

Se utilizar apenas uma palavra-passe para autenticar um utilizador, deixa um vetor inseguro para o ataque. Se a palavra-passe é fraca ou foi exposta em outro lugar, é realmente o utilizador que está a iniciar sessão com o nome de utilizador e a palavra-passe, ou é um intruso? Quando se precisa de uma segunda forma de autenticação, a segurança é aumentada, uma vez que este fator adicional não é algo que seja fácil para um intruso obter ou duplicar.

![Imagem conceptual das diferentes formas de autenticação multi-factor](./media/concept-mfa-howitworks/methods.png)

Azure AD Multi-Factor Authentication funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que sabe, tipicamente uma senha.
* Algo que você tem, como um dispositivo de confiança que não é facilmente duplicado, como um telefone ou chave de hardware.
* Algo que tu és, biometria como uma impressão digital ou uma tomografia facial.

Os utilizadores podem registar-se tanto para o reset da palavra-passe de autosserviço como para a autenticação multi-factor Ad Azure, num passo para simplificar a experiência de embarque. Os administradores podem definir que formas de autenticação secundária podem ser utilizadas. A autenticação multi-factor Azure AD também pode ser necessária quando os utilizadores efetuam uma redefinição de senha de autosserviço para garantir ainda mais esse processo.

![Métodos de autenticação em uso no ecrã de inscrição](media/concept-authentication-methods/overview-login.png)

A autenticação multi-factor Azure AD ajuda a salvaguardar o acesso a dados e aplicações, mantendo a simplicidade para os utilizadores. Proporciona segurança adicional exigindo uma segunda forma de autenticação e proporciona uma autenticação forte através de uma gama de métodos de [autenticação](concept-authentication-methods.md)fáceis de usar . Os utilizadores podem ou não ser desafiados para mFA com base nas decisões de configuração que um administrador toma.

As suas aplicações ou serviços não necessitam de escamar quaisquer alterações para utilizar a Autenticação Multi-Factor AD Azure. As solicitações de verificação fazem parte do evento de inscrição Azure AD, que automaticamente solicita e processa o desafio MFA quando necessário.

## <a name="available-verification-methods"></a>Métodos de verificação disponíveis

Quando um utilizador se inscreve numa aplicação ou serviço e recebe uma solicitação de MFA, pode escolher entre uma das suas formas de verificação adicional registadas. Um administrador pode exigir o registo destes métodos de verificação de autenticação multi-factor Azure AD, ou o utilizador pode aceder ao seu próprio [Perfil](https://myprofile.microsoft.com) para editar ou adicionar métodos de verificação.

As seguintes formas adicionais de verificação podem ser utilizadas com a autenticação multi-factor Azure AD:

* Aplicação Microsoft Authenticator
* Ficha de hardware do OATH
* SMS
* Chamada de voz

## <a name="how-to-enable-and-use-azure-ad-multi-factor-authentication"></a>Como ativar e utilizar a autenticação multi-factor Azure AD

Os utilizadores e grupos podem ser ativados para a autenticação multi-factor Azure AD para solicitar uma verificação adicional durante o evento de entrada. [Os padrão de segurança](../fundamentals/concept-fundamentals-security-defaults.md) estão disponíveis para todos os inquilinos Azure AD para permitir rapidamente a utilização da aplicação Microsoft Authenticator para todos os utilizadores.

Para mais controlos granulares, as políticas [de Acesso Condicional](../conditional-access/overview.md) podem ser usadas para definir eventos ou aplicações que requerem MFA. Estas políticas podem permitir eventos de entrada regulares quando o utilizador está na rede corporativa ou num dispositivo registado, mas solicitam fatores de verificação adicionais quando remotos ou num dispositivo pessoal.

![Resumo do diagrama de como o Acesso Condicional funciona para garantir o processo de inscrição](media/tutorial-enable-azure-mfa/conditional-access-overview.png)

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre o licenciamento, consulte [funcionalidades e licenças para autenticação multi-factor Azure AD](concept-mfa-licensing.md).

Para ver o MFA em ação, ative a autenticação multi-factor Azure AD para um conjunto de utilizadores de teste no seguinte tutorial:

> [!div class="nextstepaction"]
> [Ativar o Multi-Factor Authentication do Azure AD](./tutorial-enable-azure-mfa.md)