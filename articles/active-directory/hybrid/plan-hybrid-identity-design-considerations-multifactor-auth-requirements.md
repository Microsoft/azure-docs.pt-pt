---
title: Design de identidade híbrida - requisitos de autenticação multifactor Azure [ Microsoft Docs
description: Com controlo de acesso condicional, o Diretório Ativo Azure verifica as condições específicas que escolhe ao autenticar o utilizador e antes de permitir o acesso à aplicação. Uma vez cumpridas essas condições, o utilizador é autenticado e é permitido o acesso à aplicação.
documentationcenter: ''
services: active-directory
author: billmath
manager: billmath
editor: ''
ms.assetid: 9c59fda9-47d0-4c7e-b3e7-3575c29beabe
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4743195fc79d43571ec79a13b8518edc7e81379b
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67109298"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de autenticação de vários fatores para a sua solução de identidade híbrida
Neste mundo de mobilidade, com os utilizadores a acederem a dados e aplicações na nuvem e a partir de qualquer dispositivo, a garantia desta informação tornou-se primordial.  Todos os dias há uma nova manchete sobre uma falha de segurança.  Embora não haja garantias contra tais violações, a autenticação multifactor, fornece uma camada adicional de segurança para ajudar a prevenir estas violações.
Comece por avaliar os requisitos das organizações para a autenticação de vários fatores. Ou seja, o que é a organização a tentar garantir.  Esta avaliação é importante para definir os requisitos técnicos para a configuração e capacitação dos utilizadores das organizações para a autenticação de vários fatores.

Certifique-se de responder ao seguinte:

* A sua empresa está a tentar proteger as aplicações da Microsoft? 
* Como estas aplicações são publicadas?
* A sua empresa fornece acesso remoto para permitir aos colaboradores acederem a aplicações no local?

Se sim, que tipo de acesso remoto? Também precisa de avaliar onde estarão localizados os utilizadores que acedem a estas aplicações. Esta avaliação é mais um passo importante para definir a estratégia adequada de autenticação multifactor. Certifique-se de responder às seguintes perguntas:

* Onde vão ser localizados os utilizadores?
* Podem estar localizados em algum lugar?
* A sua empresa pretende estabelecer restrições de acordo com a localização do utilizador?

Uma vez que você entende estes requisitos, é importante também avaliar os requisitos do utilizador para a autenticação de vários fatores. Esta avaliação é importante porque definirá os requisitos para a autenticação de vários fatores. Certifique-se de responder às seguintes perguntas:

* Os utilizadores estão familiarizados com a autenticação de vários fatores?
* Serão necessárias algumas utilizações para fornecer autenticação adicional?  
  * Se sim, a toda a hora, quando vem de redes externas, ou acede a aplicações específicas, ou sob outras condições?
* Os utilizadores exigirão formação sobre como configurar e implementar a autenticação de vários fatores?
* Quais são os cenários-chave que a sua empresa quer permitir a autenticação de vários fatores para os seus utilizadores?

Depois de responder às perguntas anteriores, poderá compreender se existe a autenticação de vários fatores já implementada no local. Esta avaliação é importante para definir os requisitos técnicos para a configuração e capacitação dos utilizadores das organizações para a autenticação de vários fatores. Certifique-se de responder às seguintes perguntas:

* A sua empresa precisa de proteger contas privilegiadas com a MFA?
* A sua empresa precisa de ativar o MFA para determinada aplicação por razões de conformidade?
* A sua empresa precisa de ativar o MFA para todos os utilizadores elegíveis desta seleção ou apenas administradores?
* Precisa de ter MFA sempre ativado ou apenas quando os utilizadores estão registados fora da sua rede corporativa?

## <a name="next-steps"></a>Passos seguintes
[Defina uma estratégia híbrida de adoção de identidade](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Consulte também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

