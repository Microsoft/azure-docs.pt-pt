---
title: Tarefas de gestão de design de identidade híbrida - Azure | Documentos da Microsoft
description: Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando está a autenticar o utilizador e antes de permitir o acesso à aplicação. Assim que essas condições são cumpridas, o utilizador é autenticado e permissão para aceder à aplicação.
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
ms.openlocfilehash: 16d20e9ea2203c788a2b7b5eb0507b357350407d
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918518"
---
# <a name="plan-for-hybrid-identity-lifecycle"></a>Plano para o ciclo de vida da identidade híbrida
A identidade é um dos alicerces da sua estratégia de acesso de mobilidade e a aplicação da empresa. Se está a iniciar sessão seu dispositivo móvel ou a aplicação SaaS, a sua identidade é a chave para ganhar acesso a tudo. Em seu nível mais elevado, uma solução de gestão de identidades abrange unificar e sincronização entre seus repositórios de identidades, que inclui automação e centralizar o processo de aprovisionamento de recursos. A solução de identidade deve ser centralizado de identidades no local e na cloud e também utilizar alguma forma de Federação de identidades para manter a autenticação centralizada e com segurança partilhar e colaborar com utilizadores externos e as empresas. Recursos variam de sistemas operacionais e aplicativos para pessoas ou afiliado, uma organização. Estrutura organizacional pode ser alterada para acomodar as políticas e procedimentos de aprovisionamento.

Também é importante ter uma solução de identidade concebidas de modo a permitir que os utilizadores, fornecendo experiências de autoatendimento para se manterem produtivos. A sua solução de identidade é mais robusta, se ele permite início de sessão único para utilizadores em todos os recursos que precisam de acesso. Os administradores em todos os níveis podem usar procedimentos padronizados para a gestão de credenciais de utilizador. Alguns níveis de administração podem ser reduzidos ou eliminados, consoante o volume da solução de gestão de configuração. Além disso, é possível com segurança distribuir recursos de administração, manual ou automaticamente, entre várias organizações. Por exemplo, um administrador de domínio pode servir apenas as pessoas e os recursos nesse domínio. Este utilizador pode efetuar tarefas administrativas e de aprovisionamento, mas não está autorizado a realizar tarefas de configuração, como a criação de fluxos de trabalho.

## <a name="determine-hybrid-identity-management-tasks"></a>Determinar as tarefas de gestão de identidade híbrida
Distribuição de tarefas administrativas na sua organização melhora a precisão e a eficácia da administração e melhora o equilíbrio da carga de trabalho de uma organização. Seguem-se as tabelas dinâmicas que definem um sistema de gerenciamento de identidade robusta.

 ![considerações de gestão de identidade](./media/plan-hybrid-identity-design-considerations/Identity_management_considerations.png)

Para definir tarefas de gestão de identidade híbrida, deve compreender algumas características essenciais da organização que irá ser adoção de identidade híbrida. É importante compreender os repositórios atuais a ser utilizados para origens de identidade. Sabendo esses elementos principais, terá dos requisitos básicos e com base no que precisará fazer perguntas mais detalhadas que o irão direcionar para a melhor decisão de design para a sua solução de identidade.  

Ao definir esses requisitos, certifique-se de que, pelo menos, as seguintes perguntas serão respondidas

* Opções de aprovisionamento: 
  
  * A solução de identidade híbrida suporta um sistema de provisionamento e a gestão de acesso de conta robusta?
  * Como são os utilizadores, grupos e as palavras-passe, vá ser gerido?
  * Está a responder a gestão de ciclo de vida de identidade? 
    * Quanto tempo demora a suspensão de conta de atualizações de palavra-passe?
* Gestão de licenças: 
  
  * Faz a gestão de licenças de identificadores de solução de identidade de híbrida?
    * Se Sim, que capacidades estão disponíveis?
* A solução de lidar com a gestão de licenças com base em grupo? 
  
      - Se Sim, é possível atribuir um grupo de segurança para o mesmo? 
       - Se Sim, o diretório em nuvem atribuirá automaticamente licenças para todos os membros do grupo? 
        - O que acontece se um utilizador, em seguida, é adicionado ou removido do grupo, será uma licença automaticamente atribuída ou removida, conforme adequada? 
* Integração com outros fornecedores de identidade de terceiros:
* Esta solução híbrida pode ser integrada com fornecedores de identidade de terceiros para implementar o início de sessão único?
* É possível unificar todos os fornecedores de identidade diferente num sistema de identidade coesa?
* Se Sim, como e quais são eles e que capacidades estão disponíveis?

## <a name="synchronization-management"></a>Gestão de sincronização
Um dos objetivos de um Gerenciador de identidades, para poder colocar todos os fornecedores de identidade e mantê-las sincronizadas. Para manter os dados sincronizados com base num fornecedor de identidade mestre autoritativa. Num cenário de identidade híbrida, com um modelo de gestão sincronizados, gerir identidades de todos os utilizadores e dispositivos num servidor no local e sincronizar as contas e, opcionalmente, palavras-passe para a cloud. O usuário insere a mesma palavra-passe no local, como fazem na cloud e no início de sessão, a palavra-passe é verificada pela solução de identidade. Este modelo utiliza uma ferramenta de sincronização de diretórios.

![sincronização de diretórios](./media/plan-hybrid-identity-design-considerations/Directory_synchronization.png) ao design adequada a sincronização da sua solução de identidade híbrida Certifique-se de que as seguintes perguntas serão respondidas: • quais são as soluções de sincronização disponíveis para a solução de identidade híbrida?
• O que são o início de sessão único capacidades disponíveis?
• Quais são as opções para a Federação de identidade entre B2B e B2C?

## <a name="next-steps"></a>Passos Seguintes
[Determinar a estratégia de adoção de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

