---
title: Design de identidade híbrida - tarefas de gestão Azure | Microsoft Docs
description: A Azure AD verifica as condições específicas que escolhe ao autenticar o utilizador e antes de permitir o acesso à aplicação com controlo de Acesso Condicional.
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
ms.openlocfilehash: c416bf19acb1736eeed679c16dbd87de1cc98537
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90986527"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plano para ciclo de vida de identidade híbrida
A identidade é um dos alicerces da sua estratégia de mobilidade empresarial e de acesso a aplicações. Quer esteja a iniciar sessão no seu dispositivo móvel ou na aplicação SaaS, a sua identidade é a chave para ter acesso a tudo. Ao mais alto nível, uma solução de gestão de identidade engloba a unificação e a sincronização entre os seus repositórios de identidade, que inclui automatizar e centralizar o processo de apreensão de recursos. A solução de identidade deve ser uma identidade centralizada em todos os locais e na nuvem e também usar alguma forma de federação de identidade para manter a autenticação centralizada e partilhar e colaborar de forma segura com utilizadores e empresas externos. Os recursos vão desde sistemas operativos e aplicações a pessoas ou afiliadas a uma organização. A estrutura organizacional pode ser alterada para acomodar as políticas e procedimentos de provisionamento.

Também é importante ter uma solução de identidade orientada para capacitar os seus utilizadores, proporcionando-lhes experiências de autosserviço para os manter produtivos. A sua solução de identidade é mais robusta se permitir uma única solução para os utilizadores em todos os recursos que necessitam de acesso. Os administradores a todos os níveis podem utilizar procedimentos padronizados para gerir as credenciais dos utilizadores. Alguns níveis de administração podem ser reduzidos ou eliminados, dependendo da amplitude da solução de gestão do a provisionamento. Além disso, pode distribuir de forma segura as capacidades de administração, manual ou automaticamente, entre várias organizações. Por exemplo, um administrador de domínio pode servir apenas as pessoas e recursos nesse domínio. Este utilizador pode fazer tarefas administrativas e de provisionamento, mas não está autorizado a fazer tarefas de configuração, tais como a criação de fluxos de trabalho.

## <a name="determine-hybrid-identity-management-tasks"></a>Determinar Tarefas híbridas de gestão de identidade
A distribuição de tarefas administrativas na sua organização melhora a precisão e eficácia da administração e melhora o equilíbrio da carga de trabalho de uma organização. Seguem-se os pivôs que definem um robusto sistema de gestão de identidade.

 ![considerações de gestão de identidade](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Para definir tarefas híbridas de gestão de identidade, é preciso compreender algumas características essenciais da organização que vai adotar a identidade híbrida. É importante compreender os atuais repositórios que estão a ser utilizados para fontes de identidade. Ao conhecer esses elementos fundamentais, terá os requisitos fundamentais e com base nisso terá de fazer mais perguntas granulares que o levarão a uma melhor decisão de design para a sua solução de Identidade.  

Ao definir esses requisitos, certifique-se de que pelo menos as seguintes questões são respondidas

* Opções de provisionamento: 
  
  * A solução de identidade híbrida suporta um sistema robusto de gestão e provisionamento de acesso à conta?
  * Como é que os utilizadores, grupos e senhas vão ser geridos?
  * A gestão do ciclo de vida da identidade é sensível? 
    * Quanto tempo demora a suspensão da conta de atualizações de passwords?
* Gestão de licenças: 
  
  * A solução de identidade híbrida trata da gestão da licença?
    * Se sim, que capacidades estão disponíveis?
  * A solução lida com a gestão de licenças baseadas em grupo? 
  
    * Se sim, é possível atribuir-lhe um grupo de segurança? 
    * Se sim, o diretório de nuvem atribuirá automaticamente licenças a todos os membros do grupo? 
    * O que acontece se um utilizador for posteriormente adicionado ou removido do grupo, uma licença será automaticamente atribuída ou removida conforme apropriado? 
* Integração com outros fornecedores de identidade de terceiros:
  * Esta solução híbrida pode ser integrada com fornecedores de identidade de terceiros para implementar uma única solução?
  * É possível unificar todos os diferentes fornecedores de identidade num sistema de identidade coeso?
  * Se sim, como e quais são e quais as capacidades disponíveis?

## <a name="synchronization-management"></a>Gestão da Sincronização
Um dos objetivos de um gestor de identidade, poder trazer todos os fornecedores de identidade e mantê-los sincronizados. Mantém os dados sincronizados com base num fornecedor de identidade principal autorizado. Num cenário de identidade híbrida, com um modelo de gestão sincronizado, gere todas as identidades de utilizador e dispositivo num servidor no local e sincroniza as contas e, opcionalmente, as palavras-passe para a nuvem. O utilizador introduz a mesma palavra-passe no local do que na nuvem e, no início, a palavra-passe é verificada pela solução de identidade. Este modelo utiliza uma ferramenta de sincronização de diretórios.

![Sincronização de diretório ](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) Para conceber adequadamente a sincronização da sua solução de identidade híbrida, certifique-se de que as seguintes questões são respondidas:
*    Quais são as soluções sincronizadas disponíveis para a solução de identidade híbrida?
*    Quais são os sinais únicos nas capacidades disponíveis?
*    Quais são as opções para a federação de identidade entre B2B e B2C?

## <a name="next-steps"></a>Passos seguintes
[Determinar estratégia híbrida de gestão de identidade](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)

