---
title: Requisitos híbridos de controlo de acesso ao design de identidade Azure | Microsoft Docs
description: Abrange os pilares da identidade e identifica os requisitos de acesso dos recursos para os utilizadores num ambiente híbrido.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: e3b3b984-0d15-4654-93be-a396324b9f5e
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 84b786a1701892823554a83fa2015ac88d6eff4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "60295148"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de controlo de acesso para a sua solução de identidade híbrida
Quando uma organização está a desenhar a sua solução de identidade híbrida, também pode aproveitar esta oportunidade para rever os requisitos de acesso aos recursos que planeiam disponibilizar para os utilizadores. O acesso aos dados cruza os quatro pilares da identidade, que são:

* Administração
* Autenticação
* Autorização
* Auditoria

As secções que se seguem abrangerão a autenticação e autorização em mais detalhes, administração e auditoria fazem parte do ciclo de vida da identidade híbrida. Leia [Determine As tarefas de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para obter mais informações sobre estas capacidades.

> [!NOTE]
> Leia [Os Quatro Pilares da Identidade - Gestão de Identidade na Era da It Híbrida](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para mais informações sobre cada um desses pilares.
> 
> 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Existem diferentes cenários para a autenticação e autorização, estes cenários terão requisitos específicos que devem ser cumpridos pela solução de identidade híbrida que a empresa vai adotar. Os cenários que envolvem a comunicação Business to Business (B2B) podem acrescentar um desafio extra para os administradores de TI, uma vez que terão de garantir que o método de autenticação e autorização utilizado pela organização possa comunicar com os seus parceiros de negócio. Durante o processo de conceção dos requisitos de autenticação e autorização, certifique-se de que as seguintes questões são respondidas:

* A sua organização autenticará e autorizará apenas os utilizadores localizados no seu sistema de gestão de identidade?
  * Há algum plano para cenários B2B?
  * Se sim, já sabe quais os protocolos (SAML, OAuth, Kerberos ou Certificados) que serão utilizados para ligar ambas as empresas?
* A solução de identidade híbrida que vai adotar apoia esses protocolos?

Outro ponto importante a ter em conta é onde será localizado o repositório de autenticação que será utilizado pelos utilizadores e parceiros e o modelo administrativo a utilizar. Considere as duas opções principais:

* Centralizado: neste modelo, as credenciais, políticas e administração do utilizador podem ser centralizadas no local ou na nuvem.
* Híbrido: neste modelo, as credenciais, políticas e administração do utilizador serão centralizadas no local e replicadas na nuvem.

Qual o modelo que a sua organização irá adotar variará de acordo com os seus requisitos de negócio, deseja responder às seguintes questões para identificar onde reside o sistema de gestão de identidade e o modo administrativo de utilização:

* A sua organização tem atualmente uma gestão de identidade no local?
  * Se sim, planeiam mantê-lo?
  * Existe algum regulamento ou requisitos de conformidade que a sua organização deve seguir que dita onde o sistema de gestão de identidade deve residir?
* A sua organização utiliza um único sign-on para aplicações localizadas no local ou na nuvem?
  * Se sim, a adoção de um modelo de identidade híbrida afeta este processo?

## <a name="access-control"></a>Controlo de Acesso
Embora a autenticação e autorização sejam elementos fundamentais para permitir o acesso aos dados corporativos através da validação do utilizador, também é importante controlar o nível de acesso que estes utilizadores terão e o nível de acesso que os administradores terão sobre os recursos que estão a gerir. A sua solução de identidade híbrida deve ser capaz de fornecer acesso granular aos recursos, delegação e controlo de acesso à base de função. Certifique-se de que a seguinte pergunta é respondida sobre o controlo de acesso:

* A sua empresa tem mais do que um utilizador com elevado privilégio de gerir o seu sistema de identidade?
  * Se sim, cada utilizador precisa do mesmo nível de acesso?
* A sua empresa precisaria de delegar o acesso aos utilizadores para gerir recursos específicos?
  * Se sim, com que frequência isto acontece?
* A sua empresa precisaria de integrar capacidades de controlo de acesso entre as instalações e os recursos em nuvem?
* A sua empresa precisaria de limitar o acesso aos recursos de acordo com algumas condições?
* A sua empresa teria alguma aplicação que precisasse de acesso personalizado a alguns recursos?
  * Se sim, onde estão essas aplicações (no local ou na nuvem)?
  * Se sim, onde estão os recursos-alvo localizados (no local ou na nuvem)?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Definir a Estratégia de Proteção de Dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) irá sobre as opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às necessidades do seu negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)

