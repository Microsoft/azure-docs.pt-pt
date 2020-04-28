---
title: Design de identidade híbrida - tarefas de gestão Azure [ Microsoft Docs
description: Com controlo de acesso condicional, o Diretório Ativo Azure verifica as condições específicas que escolhe ao autenticar o utilizador e antes de permitir o acesso à aplicação. Uma vez cumpridas essas condições, o utilizador é autenticado e é permitido o acesso à aplicação.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 65f80aea-0426-4072-83e1-faf5b76df034
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a829d39ff21a1abeafd3b4362747894d196d9d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67109390"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plano para ciclo de vida de identidade híbrida
A identidade é uma das bases da sua estratégia de mobilidade empresarial e de acesso a aplicações. Quer esteja a iniciar sessão no seu dispositivo móvel ou na aplicação SaaS, a sua identidade é a chave para ter acesso a tudo. Ao seu mais alto nível, uma solução de gestão de identidade engloba unificação e sincronização entre os seus repositórios de identidade, que inclui automatizar e centralizar o processo de disponibilização de recursos. A solução identitária deve ser uma identidade centralizada em todo o local e na nuvem e também utilizar alguma forma de federação identitária para manter a autenticação centralizada e partilhar e colaborar de forma segura com utilizadores e empresas externos. Os recursos vão desde sistemas operativos e aplicações a pessoas de uma organização ou afiliadas. A estrutura organizacional pode ser alterada para acomodar as políticas e procedimentos de provisionamento.

Também é importante ter uma solução de identidade orientada para capacitar os seus utilizadores, fornecendo-lhes experiências de self-service para mantê-los produtivos. A sua solução de identidade é mais robusta se permitir um único sinal para os utilizadores em todos os recursos de que necessitam de acesso. Os administradores a todos os níveis podem utilizar procedimentos padronizados para gerir as credenciais dos utilizadores. Alguns níveis de administração podem ser reduzidos ou eliminados, dependendo da amplitude da solução de gestão do provisionamento. Além disso, pode distribuir de forma segura as capacidades de administração, manual ou automaticamente, entre várias organizações. Por exemplo, um administrador de domínio só pode servir as pessoas e os recursos desse domínio. Este utilizador pode fazer tarefas administrativas e de provisionamento, mas não está autorizado a fazer tarefas de configuração, tais como a criação de fluxos de trabalho.

## <a name="determine-hybrid-identity-management-tasks"></a>Determinar tarefas híbridas de gestão da identidade
A distribuição de tarefas administrativas na sua organização melhora a precisão e eficácia da administração e melhora o equilíbrio da carga de trabalho de uma organização. Seguem-se os pivôs que definem um sistema robusto de gestão de identidade.

 ![considerações de gestão de identidade](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Para definir tarefas híbridas de gestão de identidade, deve compreender algumas características essenciais da organização que irá adotar a identidade híbrida. É importante compreender os atuais repositórios que estão a ser utilizados para fontes de identidade. Conhecendo esses elementos fundamentais, terá os requisitos fundamentais e com base nisso terá de fazer mais perguntas granulares que o levarão a uma melhor decisão de design para a sua solução de Identidade.  

Ao definir esses requisitos, certifique-se de que pelo menos as seguintes perguntas são respondidas

* Opções de provisionamento: 
  
  * A solução de identidade híbrida suporta um sistema robusto de gestão e provisionamento de acesso a contas?
  * Como é que os utilizadores, grupos e senhas vão ser geridos?
  * A gestão do ciclo de vida da identidade é responsiva? 
    * Quanto tempo demora a suspensão da conta de atualizações de password?
* Gestão de licenças: 
  
  * A solução de identidade híbrida trata da gestão da licença?
    * Se sim, que capacidades estão disponíveis?
  * A solução lida com a gestão de licenças baseada em grupo? 
  
    * Se sim, é possível atribuir-lhe um grupo de segurança? 
    * Se sim, o diretório da nuvem atribuirá automaticamente licenças a todos os membros do grupo? 
    * O que acontece se um utilizador for adicionado ou removido do grupo, uma licença será automaticamente atribuída ou removida conforme apropriado? 
* Integração com outros fornecedores de identidade de terceiros:
  * Poderá esta solução híbrida ser integrada com fornecedores de identidade de terceiros para implementar um único sign-on?
  * É possível unificar todos os diferentes fornecedores de identidade num sistema de identidade coeso?
  * Se sim, como e quais são e quais as capacidades disponíveis?

## <a name="synchronization-management"></a>Gestão de Sincronização
Um dos objetivos de um gestor de identidade, poder trazer todos os fornecedores de identidade e mantê-los sincronizados. Mantém os dados sincronizados com base num provedor de identidade principal autoritário. Num cenário de identidade híbrida, com um modelo de gestão sincronizado, gere todas as identidades do utilizador e do dispositivo num servidor no local e sincroniza as contas e, opcionalmente, as palavras-passe para a nuvem. O utilizador introduz a mesma palavra-passe no local que faz na nuvem e, no início do sessão, a palavra-passe é verificada pela solução de identidade. Este modelo utiliza uma ferramenta de sincronização de diretório.

![sincronização](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) de diretório Para conceber adequadamente a sincronização da sua solução de identidade híbrida, certifique-se de que as seguintes perguntas são respondidas:
*    Quais são as soluções de sincronização disponíveis para a solução de identidade híbrida?
*    Qual é o único sinal sobre as capacidades disponíveis?
*    Quais são as opções para a federação de identidade entre B2B e B2C?

## <a name="next-steps"></a>Passos seguintes
[Determinar a estratégia de adoção de identidade híbrida](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Veja também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

