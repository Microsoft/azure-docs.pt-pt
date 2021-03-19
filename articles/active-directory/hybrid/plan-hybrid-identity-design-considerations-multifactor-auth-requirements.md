---
title: Design de identidade híbrida - requisitos de autenticação de vários fatores Azure | Microsoft Docs
description: Com o controlo de Acesso Condicional, a Azure AD verifica as condições específicas que escolhe ao autenticar o utilizador e antes de permitir o acesso à aplicação.
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
ms.openlocfilehash: 7d8ddf372e234bab242e4b28ba53dce7dd68cc89
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90976060"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de autenticação de vários fatores para a sua solução de identidade híbrida
Neste mundo de mobilidade, com os utilizadores a acederem a dados e aplicações na nuvem e a partir de qualquer dispositivo, a segurança desta informação tornou-se fundamental.  Todos os dias há uma nova manchete sobre uma falha de segurança.  Embora não exista garantia contra tais infrações, a autenticação de vários fatores, fornece uma camada adicional de segurança para ajudar a prevenir estas falhas.
Comece por avaliar os requisitos das organizações para a autenticação de vários fatores. Isto é, o que é que a organização está a tentar assegurar.  Esta avaliação é importante para definir os requisitos técnicos para a configuração e capacitação dos utilizadores das organizações para a autenticação de vários fatores.

Certifique-se de responder ao seguinte:

* A sua empresa está a tentar proteger as aplicações da Microsoft? 
* Como estas aplicações são publicadas?
* A sua empresa fornece acesso remoto para permitir que os colaboradores acedam a aplicações no local?

Se sim, que tipo de acesso remoto? Também é necessário avaliar onde serão localizados os utilizadores que estão a aceder a estas aplicações. Esta avaliação é mais um passo importante para definir a estratégia adequada de autenticação de vários fatores. Certifique-se de responder às seguintes perguntas:

* Onde é que os utilizadores vão estar localizados?
* Podem ser localizados em algum lugar?
* A sua empresa pretende estabelecer restrições de acordo com a localização do utilizador?

Uma vez que compreenda estes requisitos, é importante também avaliar os requisitos do utilizador para a autenticação de vários fatores. Esta avaliação é importante porque definirá os requisitos para a autenticação de vários fatores. Certifique-se de responder às seguintes perguntas:

* Os utilizadores estão familiarizados com a autenticação de vários fatores?
* Serão necessárias algumas utilizações para fornecer autenticação adicional?  
  * Se sim, todo o tempo, quando vem de redes externas, ou acede a aplicações específicas, ou sob outras condições?
* Os utilizadores vão precisar de formação sobre como configurar e implementar a autenticação de vários fatores?
* Quais são os cenários-chave que a sua empresa quer para permitir a autenticação de vários fatores para os seus utilizadores?

Depois de responder às perguntas anteriores, poderá perceber se existem autenticação multi-factor já implementada no local. Esta avaliação é importante para definir os requisitos técnicos para a configuração e capacitação dos utilizadores das organizações para a autenticação de vários fatores. Certifique-se de responder às seguintes perguntas:

* A sua empresa precisa de proteger contas privilegiadas com OMF?
* A sua empresa precisa de ativar o MFA para determinada aplicação por razões de conformidade?
* A sua empresa precisa de ativar o MFA para todos os utilizadores elegíveis destas aplicações ou apenas administradores?
* Precisa de MFA sempre ativado ou apenas quando os utilizadores estiverem registados fora da sua rede corporativa?

## <a name="next-steps"></a>Passos seguintes
[Definir uma estratégia híbrida de adoção de identidade](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Ver também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)

