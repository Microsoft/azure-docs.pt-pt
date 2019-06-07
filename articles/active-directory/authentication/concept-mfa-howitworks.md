---
title: O Azure multi-factor Authentication - como funciona - Azure Active Directory
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
ms.openlocfilehash: fa25e8a965b89c4e97263e3767a9400079fcad7a
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66496796"
---
# <a name="how-it-works-azure-multi-factor-authentication"></a>Como funciona: Multi-Factor Authentication do Azure

A segurança de verificação de dois passos está na sua abordagem em camadas. Comprometer a vários fatores de autenticação apresenta um desafio significativo dos invasores. Mesmo que um atacante conseguir saber a senha do usuário, é inútil sem também ter posse do método de autenticação adicional. Ele funciona exigindo dois ou mais dos seguintes métodos de autenticação:

* Algo que saiba (normalmente uma palavra-passe)
* Algo que tenha (um dispositivo fidedigno que não seja duplicado facilmente, como um telemóvel)
* Algo que seja (biometria)

<center>

![Imagem de métodos de autenticação conceitual](./media/concept-mfa-howitworks/methods.png)</center>

O Azure multi-factor Authentication (MFA) ajuda a salvaguardar o acesso a dados e aplicativos, mantendo a simplicidade para os utilizadores. Ele fornece segurança adicional, exigindo que uma segunda forma de autenticação e proporciona uma autenticação segura através de um conjunto de fácil de usar [métodos de autenticação](concept-authentication-methods.md). Os utilizadores podem ou não podem ser submetidos à MFA com base nas decisões de configuração que faz com que um administrador.

## <a name="how-to-get-multi-factor-authentication"></a>Como obter a multi-factor Authentication?

Autenticação Multifator é fornecido como parte das ofertas seguintes:

* **O Azure Active Directory Premium** ou **Microsoft 365 empresas** -completo do uso de multi-factor Authentication através de políticas de acesso condicional para exigir autenticação multifator.

* **Azure AD gratuito**, **do Azure AD básico**, ou autónoma **do Office 365** licenças - utilização criado previamente [políticas de proteção de linha de base de acesso condicional](../conditional-access/concept-baseline-protection.md) para exigir autenticação multifator para os utilizadores e administradores.

* **Administradores do Azure Active Directory Global** -um subconjunto de capacidades multi-factor Authentication do Azure estão disponíveis como um meio para proteger contas de administrador global.

> [!NOTE]
> Novos clientes já não podem comprar o multi-factor Authentication do Azure como uma autónoma oferta em vigor a partir 1 de Setembro de 2018. Multi-factor authentication irá continuar a ser uma funcionalidade disponível em licenças do Azure AD Premium.

## <a name="supportability"></a>Suportabilidade

Uma vez que a maioria dos usuários estão acostumados a utilizar apenas as palavras-passe para autenticar, é importante que sua organização se comunica a todos os utilizadores em relação a esse processo. Reconhecimento pode reduzir a probabilidade de que os utilizadores, ligue o suporte técnico para problemas secundários relacionados com a MFA. No entanto, existem alguns cenários em que é necessário desativar temporariamente a MFA. Utilize as diretrizes seguintes para compreender como lidar com esses cenários:

* Treine sua equipe de suporte para manipular os cenários em que o utilizador não pode iniciar sessão uma vez que não têm acesso aos seus métodos de autenticação ou que não estão a funcionar corretamente.
   * Utilizar políticas de acesso condicional para o serviço de MFA do Azure, a equipe de suporte pode adicionar um utilizador a um grupo que esteja excluído de uma política que exija a MFA.
* Considere utilizar o acesso condicional com o nome localizações como solicita uma forma para minimizar a verificação de dois passos. Com esta funcionalidade, os administradores podem ignorar a verificação de dois passos para os utilizadores que a sessão iniciada a partir de uma localização de rede fidedigna segura como uma rede utilizado para novas de utilizador de integração de segmento.
* Implementar [do Azure AD Identity Protection](../active-directory-identityprotection.md) e acionar a verificação de dois passos com base em eventos de risco.

## <a name="next-steps"></a>Passos Seguintes

- [Implementação passo a passo do multi-factor Authentication do Azure](howto-mfa-getstarted.md)
