---
title: Requisitos de controlo de acesso de design de identidade do híbrida do Azure | Documentos da Microsoft
description: Aborda os pilares da identidade e identificar os requisitos de acesso para recursos para os utilizadores num ambiente híbrido.
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60295148"
---
# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determinar os requisitos de controlo de acesso para a sua solução de identidade híbrida
Quando uma organização é conceber a sua solução de identidade híbrida, também podem utilizar esta oportunidade para rever os requisitos de acesso para os recursos que eles estiverem planejando para disponibilizá-lo para os utilizadores. O acesso a dados em várias quatro todos os pilares da identidade, que são:

* Administração
* Autenticação
* Autorização
* Auditoria

As secções que se seguem abordará autenticação e autorização em mais detalhes, administração, e auditoria fazem parte do ciclo de vida de identidade híbrida. Leia [determinar as tarefas de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para obter mais informações sobre estas capacidades.

> [!NOTE]
> Leia [os quatro pilares da identidade - gestão de identidades na idade de TI híbrida](https://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para obter mais informações sobre cada uma dessas pilares.
> 
> 

## <a name="authentication-and-authorization"></a>Autenticação e autorização
Existem diferentes cenários de autenticação e autorização, esses cenários terá requisitos específicos que devem ser cumpridos pela solução de identidade híbrida que a empresa irá adotar. Cenários que envolvem a comunicação do empresa-empresa (B2B) podem adicionar um desafio adicional para os administradores de TI, uma vez que têm de se certificar de que o método de autenticação e autorização utilizado pela organização consegue comunicar com seus parceiros de negócios. Durante o processo de conceção para requisitos de autenticação e autorização, certifique-se de que as seguintes perguntas serão respondidas:

* Será a sua organização autenticar e autorizar o somente os usuários localizados em seu sistema de gestão de identidade?
  * Existem planos para cenários B2B?
  * Se Sim, já sabe quais protocolos (SAML, OAuth, Kerberos ou certificados) serão utilizados para ligar a ambas as empresas?
* É a solução de identidade híbrida que vai para adotar o suporte esses protocolos?

Outro ponto importante a considerar é onde estarão localizado no repositório de autenticação que será utilizado por utilizadores e parceiros e o modelo administrativo para ser utilizado. Considere as seguintes opções de dois núcleos:

* Centralizado: neste modelo, as credenciais do utilizador, políticas e administração podem ser centralizado no local ou na cloud.
* Híbrida: neste modelo, as credenciais do utilizador, políticas e administração será centralizado no local e um replicadas na cloud.

Qual modelo de sua organização irá adotar varia de acordo com seus requisitos de negócios, que pretende responder às perguntas seguintes para identificar qual irão residir no sistema de gerenciamento de identidade e o modo administrativo a utilizar:

* Sua organização tem atualmente uma gestão de identidades no local?
  * Se Sim, eles pretende mantê-lo?
  * Existem requisitos de regulamentação ou conformidade que sua organização tem de seguir esse dita em que o sistema de gestão de identidades deve residir?
* A sua organização utiliza o início de sessão único para aplicações existentes no local ou na cloud?
  * Se Sim, a adoção de um modelo de identidade híbrida afeta este processo?

## <a name="access-control"></a>Controlo de Acesso
Embora a autenticação e autorização são elementos principais para ativar o acesso a dados empresariais por meio de validação do utilizador, também é importante controlar o nível de acesso que estes utilizadores têm e o nível de administradores do acesso terá sobre os recursos a Se eles estão a gerir. Sua solução de identidade híbrida tem de ser capaz de fornecer acesso granular para recursos, a delegação e controlo de acesso de base de função. Certifique-se de que o seguinte é às suas perguntas sobre o controlo de acesso:

* A sua empresa tem mais de um usuário com privilégio elevado para gerir o seu sistema de identidade?
  * Se Sim, a cada utilizador é necessário o mesmo nível de acesso?
* A sua empresa precisaria delegar o acesso aos utilizadores para gerir os recursos específicos?
  * Se Sim, a frequência com que isso acontece?
* A sua empresa precisaria integrar os recursos de controle de acesso entre no local e na cloud recursos?
* A sua empresa precisaria limitar o acesso a recursos de acordo com algumas condições?
* A sua empresa teria qualquer aplicativo que precise de acesso de controle personalizado a alguns recursos?
  * Se Sim, onde estão os aplicativos localizados (no local ou na cloud)?
  * Se Sim, onde estão os recursos de destino localizados (no local ou na cloud)?

> [!NOTE]
> Lembre-se de que anota cada resposta e compreender as razões implícitas para a resposta. [Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) abordará as opções disponíveis e as vantagens/desvantagens de cada opção.  Respondendo essas perguntas irá selecionar qual opção melhor se adequa às suas necessidades empresariais.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
[Determinar os requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

