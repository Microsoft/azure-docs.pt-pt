---
title: Requisitos de controlo de acesso ao design de identidade híbrido Saem Os requisitos de controlo do acesso ao azure Microsoft Docs
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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "60295148"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determine os requisitos de controlo de acesso para a sua solução de identidade híbrida
Quando uma organização está a desenhar a sua solução de identidade híbrida, também pode aproveitar esta oportunidade para rever os requisitos de acesso aos recursos que estão a planear disponibilizar aos utilizadores. O acesso aos dados cruza os quatro pilares da identidade, que são:

* Administração
* Autenticação
* Autorização
* Auditoria

As secções que se seguem abrangerão a autenticação e a autorização em mais detalhes, administração e auditoria fazem parte do ciclo de vida da identidade híbrida. Leia [Determinar as tarefas híbridas](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) de gestão de identidade para obter mais informações sobre estas capacidades.

> [!NOTE]
> Leia [Os Quatro Pilares da Identidade - Gestão de Identidade na Era das TI Híbridas](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para obter mais informações sobre cada um desses pilares.
> 
> 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Existem diferentes cenários de autenticação e autorização, estes cenários terão requisitos específicos que devem ser cumpridos pela solução de identidade híbrida que a empresa vai adotar. Cenários que envolvam a comunicação Business to Business (B2B) podem acrescentar um desafio extra aos administradores de TI, uma vez que terão de garantir que o método de autenticação e autorização utilizado pela organização pode comunicar com os seus parceiros de negócio. Durante o processo de conceção dos requisitos de autenticação e autorização, certifique-se de que as seguintes questões são respondidas:

* A sua organização irá autenticar e autorizar apenas utilizadores localizados no seu sistema de gestão de identidade?
  * Há planos para cenários B2B?
  * Se sim, já sabe quais os protocolos (SAML, OAuth, Kerberos ou Certificados) para ligar ambas as empresas?
* A solução de identidade híbrida que vai adotar apoia esses protocolos?

Outro ponto importante a ter em conta é o local onde será localizado o repositório de autenticação que será utilizado pelos utilizadores e parceiros e o modelo administrativo a utilizar. Considere as seguintes duas opções principais:

* Centralizado: neste modelo, as credenciais, políticas e administração do utilizador podem ser centralizadas no local ou na nuvem.
* Híbrido: neste modelo, as credenciais, políticas e administração do utilizador serão centralizadas no local e uma replicada na nuvem.

Qual o modelo que a sua organização irá adotar variará de acordo com os seus requisitos de negócio, pretende responder às seguintes questões para identificar onde o sistema de gestão de identidade vai residir e o modo administrativo a utilizar:

* A sua organização tem atualmente uma gestão de identidade no local?
  * Se sim, planeiam mantê-lo?
  * Existem requisitos de regulação ou conformidade que a sua organização deve seguir que ditem onde o sistema de gestão de identidade deve residir?
* A sua organização utiliza um único sinal para aplicações localizadas no local ou na nuvem?
  * Se sim, a adoção de um modelo de identidade híbrida afeta este processo?

## <a name="access-control"></a>Controlo de Acesso
Embora a autenticação e a autorização sejam elementos fundamentais para permitir o acesso aos dados corporativos através da validação do utilizador, é também importante controlar o nível de acesso que estes utilizadores terão e o nível de acesso que os administradores terão sobre os recursos que estão a gerir. A sua solução de identidade híbrida deve ser capaz de fornecer acesso granular aos recursos, delegação e controlo de acesso de base. Certifique-se de que a seguinte pergunta é respondida no que diz respeito ao controlo de acesso:

* A sua empresa tem mais do que um utilizador com privilégioelevado para gerir o seu sistema de identidade?
  * Se sim, cada utilizador precisa do mesmo nível de acesso?
* A sua empresa necessita de delegar acesso aos utilizadores para gerir recursos específicos?
  * Se sim, com que frequência isto acontece?
* A sua empresa necessitaria de integrar as capacidades de controlo de acesso entre as instalações e os recursos na nuvem?
* A sua empresa teria de limitar o acesso aos recursos de acordo com algumas condições?
* A sua empresa teria alguma aplicação que necessite de acesso personalizado a alguns recursos?
  * Se sim, onde estão as aplicações localizadas (no local ou na nuvem)?
  * Se sim, onde estão os recursos-alvo localizados (no local ou na nuvem)?

> [!NOTE]
> Certifique-se de que anota cada resposta e que compreende os fundamentos das mesmas. [Definir a Estratégia](plan-hybrid-identity-design-considerations-data-protection-strategy.md) de Proteção de Dados irá passar por cima das opções disponíveis e vantagens/desvantagens de cada opção.  Ao responder a essas perguntas, irá selecionar qual a opção que melhor se adequa às suas necessidades de negócio.
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Veja também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

