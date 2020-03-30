---
title: Como funciona Azure MFA - Azure Ative Directory
description: A Autenticação Multi-Factor Azure ajuda a salvaguardar o acesso a dados e aplicações, ao mesmo tempo que satisfaz a procura do utilizador por um processo de entrada simples.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39948214f5bd080be417ed515bea6bff87d3b303
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484065"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: Multi-Factor Authentication do Azure

A segurança da verificação em duas etapas reside na sua abordagem em camadas. Comprometer múltiplos fatores de autenticação representa um desafio significativo para os atacantes. Mesmo que um intruso consiga aprender a palavra-passe do utilizador, é inútil sem também ter posse do método de autenticação adicional. Funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que você sabe (tipicamente uma palavra-passe)
* Algo que você tem (um dispositivo de confiança que não é facilmente duplicado, como um telefone)
* Algo que você é (biometria)

<center>

![Imagem de métodos de autenticação conceptual](./media/concept-mfa-howitworks/methods.png)</center>

A Autenticação Multi-Factor (MFA) do Azure ajuda a salvaguardar o acesso a dados e aplicações, mantendo a simplicidade para os utilizadores. Proporciona segurança adicional exigindo uma segunda forma de autenticação e fornece autenticação forte através de uma gama de métodos de [autenticação](concept-authentication-methods.md)fáceis de usar. Os utilizadores podem ou não ser contestados para o MFA com base em decisões de configuração que um administrador toma.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a Multi-Factor Authentication?

A Multi-Factor Authentication é fornecida como parte das seguintes ofertas:

* **Azure Ative Directory Premium** ou **Microsoft 365 Business** - Utilização em destaque total da autenticação multi-factor Azure utilizando políticas de acesso condicional para exigir a autenticação de vários fatores.

* **Azure AD Free** ou Autónoma **Office 365** licenças - Use [Predefinições](../fundamentals/concept-fundamentals-security-defaults.md) de Segurança para exigir autenticação multifactor para os seus utilizadores e administradores.

* **Administradores Globais do Azure Active Directory** – está disponível um subconjunto de recursos da Multi-Factor Authentication do Azure como forma de proteger contas de administradores globais.

> [!NOTE]
> Os novos clientes podem deixar de adquirir a Autenticação Multi-Factor Azure como uma oferta autónoma a 1 de setembro de 2018. A autenticação multifactor continuará a ser uma funcionalidade disponível nas licenças Azure AD Premium.

## <a name="supportability"></a>Suportabilidade

Uma vez que a maioria dos utilizadores está habituada a usar apenas senhas para autenticar, é importante que a sua organização comunique a todos os utilizadores sobre este processo. A consciência pode reduzir a probabilidade de os utilizadores ligarem para o seu balcão de ajuda para problemas menores relacionados com o MFA. No entanto, existem alguns cenários em que é necessário desativar temporariamente o MFA. Utilize as seguintes orientações para entender como lidar com esses cenários:

* Treine a sua equipa de suporte para lidar com cenários em que o utilizador não possa iniciar sessão porque não tem acesso aos seus métodos de autenticação ou não está a funcionar corretamente.
   * Utilizando as políticas de Acesso Condicional para o Serviço Azure MFA, a sua equipa de suporte pode adicionar um utilizador a um grupo que está excluído de uma política que exige MFA.
* Considere a utilização de locais nomeados pelo Acesso Condicional como uma forma de minimizar as solicitações de verificação em duas etapas. Com esta funcionalidade, os administradores podem contornar a verificação em duas etapas para os utilizadores que estão a iniciar sessão a partir de uma localização de rede fidedigna segura, como um segmento de rede utilizado para o embarque de novos utilizadores.
* Implementar [a Proteção de Identidade Azure AD](../active-directory-identityprotection.md) e desencadear uma verificação em duas etapas com base em deteções de risco.

## <a name="next-steps"></a>Passos seguintes

- [Implementação passo a passo da autenticação de multifactor do Azure](howto-mfa-getstarted.md)
