---
title: Design de identidade híbrida - requisitos de multi-factor authentication do Azure | Documentos da Microsoft
description: Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando está a autenticar o utilizador e antes de permitir o acesso à aplicação. Assim que essas condições são cumpridas, o utilizador é autenticado e permissão para aceder à aplicação.
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
ms.openlocfilehash: 3dabb381c16aa107e41c1d556e61e020b8c6a6c3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60455742"
---
# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de autenticação multifator para a sua solução de identidade híbrida
Neste mundo de mobilidade, com utilizadores que acedem aos dados e aplicações na cloud e a partir de qualquer dispositivo, proteger essas informações se tornou imprescindível.  Todos os dias é uma Manchete novo sobre uma violação de segurança.  Embora, não há nenhuma garantia em relação a tais violações, autenticação multifator, fornece uma camada adicional de segurança para ajudar a evitar essas falhas.
Comece a avaliar os requisitos de organizações para multi-factor authentication. Ou seja, o que é a organização tentar proteger.  Essa avaliação é importante definir os requisitos técnicos para configurar e ativar os utilizadores de organizações para multi-factor authentication.

Certifique-se responder a seguir:

* É da sua empresa a tentar proteger aplicações da Microsoft 
* Como estas aplicações são publicadas?
* É da sua empresa oferece acesso remoto para permitir que os funcionários para acederem a aplicações no local?

Se Sim, o tipo de acesso remoto? Também é necessário avaliar onde estarão localizados os utilizadores que estão a aceder a estas aplicações. Essa avaliação é outro passo importante para definir a estratégia de uma autenticação multifator adequada. Certifique-se responder às seguintes perguntas:

* Em que os utilizadores vão ser localizado?
* Podem, estes estar localizados em qualquer lugar?
* Sua empresa pretende estabelecer restrições de acordo com a localização do utilizador?

Assim que compreender estes requisitos, é importante também avaliar os requisitos do utilizador para autenticação multifator. Essa avaliação é importante porque ele será definir os requisitos para implementar o multi-factor authentication. Certifique-se responder às seguintes perguntas:

* Os utilizadores estão familiarizados com a autenticação multifator?
* Alguns usos será necessários para fornecer autenticação adicional?  
  * Se Sim, o tempo todo, na migração de redes externas ou ao aceder ao aplicações específicas ou em outras condições?
* Os usuários exigirão treinamento sobre como configurar e implementar a autenticação multifator?
* Quais são os cenários-chave que sua empresa quiser ativar a autenticação multifator para os seus utilizadores?

Depois de responder às perguntas anteriores, será capaz de compreender se existem já implementado a autenticação multifator no local. Essa avaliação é importante definir os requisitos técnicos para configurar e ativar os utilizadores de organizações para multi-factor authentication. Certifique-se responder às seguintes perguntas:

* A sua empresa precisa de proteger as contas com privilégios com a MFA?
* A sua empresa precisa de ativar a MFA para determinados aplicativo por motivos de conformidade?
* A sua empresa precisa de ativar a MFA para todos os utilizadores elegíveis de aplicativos ou apenas os administradores?
* Precisa tem MFA sempre ativada ou apenas quando os utilizadores tem sessão iniciados fora da rede empresarial?

## <a name="next-steps"></a>Passos Seguintes
[Definir uma estratégia de adoção de identidade híbrida](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md)

## <a name="see-also"></a>Consulte também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

