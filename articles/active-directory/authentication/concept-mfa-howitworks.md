---
title: Autenticação multifator do Azure-como funciona-Azure Active Directory
description: A Multi-Factor Authentication do Azure ajuda a salvaguardar o acesso a dados e a aplicações, satisfazendo, em simultâneo, a necessidade dos utilizadores de terem um processo de início de sessão simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 06/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57b441803a066b794d17b46afec4d930b94ebdea
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125138"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: Multi-Factor Authentication do Azure

A segurança da verificação em duas etapas está em sua abordagem em camadas. Comprometer vários fatores de autenticação apresenta um desafio significativo para os invasores. Mesmo que um invasor consiga aprender a senha do usuário, ele será inútil sem também ter posse do método de autenticação adicional. Ele funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe (normalmente uma senha)
* Algo que você tem (um dispositivo confiável que não é facilmente duplicado, como um telefone)
* Algo que você é (biometria)

<center>

![Imagem dos métodos de autenticação conceitual](./media/concept-mfa-howitworks/methods.png)</center>

A autenticação multifator do Azure (MFA) ajuda a proteger o acesso a dados e aplicativos, mantendo a simplicidade para os usuários. Ele fornece segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte por meio de uma variedade de métodos de [autenticação](concept-authentication-methods.md)fáceis de usar. Os usuários podem ou não ser desafiados pela MFA com base nas decisões de configuração que um administrador faz.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a autenticação multifator?

A autenticação multifator é fornecido como parte das seguintes ofertas:

* **Azure Active Directory Premium** ou **Microsoft 365 Business** -uso completo da autenticação multifator do Azure usando políticas de acesso condicional para exigir a autenticação multifator.

* **Azure ad gratuito** ou licenças autônomas **do Office 365** – use [políticas de proteção de linha de base de acesso condicional](../conditional-access/concept-baseline-protection.md) pré-criado para exigir a autenticação multifator para seus usuários e administradores.

* **Azure Active Directory administradores globais** – um subconjunto de recursos de autenticação multifator do Azure está disponível como um meio de proteger contas de administrador global.

> [!NOTE]
> Novos clientes não poderão mais comprar a autenticação multifator do Azure como uma oferta autônoma em 1º de setembro de 2018. A autenticação multifator continuará a ser um recurso disponível em licenças Azure AD Premium.

## <a name="supportability"></a>Suportabilidade

Como a maioria dos usuários está acostumado a usar apenas senhas para autenticar, é importante que sua organização se comunique com todos os usuários referentes a esse processo. A conscientização pode reduzir a probabilidade de que os usuários liguem para o suporte técnico para problemas secundários relacionados à MFA. No entanto, há alguns cenários em que desabilitar temporariamente a MFA é necessária. Use as diretrizes a seguir para entender como lidar com esses cenários:

* Treine sua equipe de suporte para lidar com cenários em que o usuário não pode se conectar porque não tem acesso aos seus métodos de autenticação ou não estão funcionando corretamente.
   * Usando políticas de acesso condicional para o serviço do Azure MFA, sua equipe de suporte pode adicionar um usuário a um grupo que é excluído de uma política que requer MFA.
* Considere o uso de locais nomeados de acesso condicional como uma maneira de minimizar prompts de verificação em duas etapas. Com essa funcionalidade, os administradores podem ignorar a verificação em duas etapas para usuários que estão entrando de um local de rede confiável seguro, como um segmento de rede usado para integração de novo usuário.
* Implante [Azure ad Identity Protection](../active-directory-identityprotection.md) e dispare a verificação em duas etapas com base nas detecções de risco.

## <a name="next-steps"></a>Passos Seguintes

- [Implantação passo a passo da autenticação multifator do Azure](howto-mfa-getstarted.md)
