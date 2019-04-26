---
title: O Azure Active Directory híbrida identidade considerações de design - descrição geral | Documentos da Microsoft
description: Descrição geral e mapa de conteúdos do guia de considerações de design de identidade híbrida
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 100509c4-0b83-4207-90c8-549ba8372cf7
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7f8dd49f3668b8f68753681123a04d21edac46c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60381492"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerações de Design da Identidade Híbrida do Azure Active Directory
Dispositivos com base no consumidor são proliferando o mundo Corporativo e baseado na nuvem aplicações do software-como-serviço (SaaS) são fáceis de adotar. Como resultado, é difícil manter o controlo de acesso dos utilizadores da aplicação em todas as plataformas internas de datacenters e na cloud.  

As soluções de identidade da Microsoft abrangem capacidades baseadas na cloud e no local, ao criar uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Esse conceito é conhecido como identidade híbrida. Existem diferente design e opções de configuração para a identidade híbrida com soluções da Microsoft e, em alguns casos poderá ser difícil determinar que combinação será melhor satisfazer as necessidades da sua organização. 

Este guia de considerações de Design de identidade híbrida lhe ajudarão a compreender como conceber uma solução de identidade híbrida que melhor se adequa o negócios e tecnologia às necessidades da sua organização.  Este guia descreve em detalhe uma série de passos e tarefas que pode seguir para criar uma solução de identidade híbrida que cumpre os requisitos exclusivos da sua organização. Ao longo dos passos e tarefas, o guia irá apresentar as tecnologias relevantes e opções disponíveis para as organizações a cumprir funcionais e de qualidade de serviço (como disponibilidade, escalabilidade, desempenho, capacidade de gerenciamento e segurança) de recursos ao nível do requisitos. 

Especificamente, as metas de guia de considerações de design de identidade de híbrida são responder às seguintes perguntas: 

* Que perguntas preciso fazer e responder para implementar um identidade específica projeto híbrido para um domínio de tecnologia ou problema que melhor corresponda às minhas necessidades?
* Que sequência de atividades deve concluir para conceber uma solução de identidade híbrida para o domínio de tecnologia ou problema? 
* Que opções de configuração e tecnologia de identidade do híbrida estão disponíveis para me ajudar a satisfazer as minhas necessidades? Quais são os compromissos entre essas opções, para que possa selecionar a melhor opção para a minha empresa?

## <a name="who-is-this-guide-intended-for"></a>Que este guia destina?
 CIO, CITO, Arquitetos de identidade do diretor, Enterprise Architects e Arquitetos de TI responsáveis pela conceção de uma solução de identidade híbrida para organizações médias ou grandes.

## <a name="how-can-this-guide-help-you"></a>Como pode neste guia ajudá-lo?
Pode utilizar este guia para saber como conceber uma solução de identidade híbrida que consegue integrar um sistema de gerenciamento de identidade com base na cloud com a sua solução de identidade no local atual. 

O gráfico seguinte mostra um exemplo de uma solução de identidade híbrida que permite que os administradores de TI gerir para integrar o seu atual do Active Directory do Windows Server solução localizado no local com o Microsoft Azure Active Directory para permitir que os utilizadores a utilizar o início de sessão único ( SSO) em todos os aplicativos localizados na cloud e no local.

![Exemplo](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

A ilustração acima é um exemplo de uma solução de identidade híbrida que tira partido de serviços cloud para integração com capacidades no local para fornecer uma experiência única para o processo de autenticação de utilizador final e para facilitar a gerenciá-las de IT recursos. Embora este exemplo pode ser um cenário comum, design de identidade híbrida de cada organização é provável que seja diferente do exemplo ilustrado na figura 1 devido a requisitos diferentes. 

Este guia fornece uma série de passos e tarefas que pode seguir para conceber uma solução de identidade híbrida que cumpre os requisitos exclusivos da sua organização. Durante os seguintes passos e tarefas, o Guia apresenta as tecnologias relevantes e as opções de funcionalidades disponíveis para si para atender aos requisitos de nível de qualidade de serviço para a sua organização e funcional.

**Suposições**: Tem alguma experiência com o Windows Server, serviços de domínio do Active Directory e Azure Active Directory. Neste documento, presume que está procurando como essas soluções podem satisfazer as necessidades da sua empresa por conta própria ou numa solução integrada.

## <a name="design-considerations-overview"></a>Descrição geral das considerações de design
Este documento fornece um conjunto de passos e tarefas que pode seguir para conceber uma solução de identidade híbrida que melhor satisfaz os requisitos. Os passos são apresentados numa sequência ordenada. As considerações de design que aprender em passos posteriores podem exigir a alteração das decisões que tomou em passos anteriores, no entanto, devido a opções de design em conflito. Tentaremos para alertá-lo para potenciais conflitos de design em todo o documento. 

Irá deparar-se com o design mais adequado aos seus requisitos depois de repetir os passos as vezes necessárias para incorporar todas as considerações no documento. 

| Fase de identidade híbrida | Lista de tópico |
| --- | --- |
| Determinar requisitos de identidade |[Determinar as necessidades de negócio](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar requisitos de sincronização de diretórios](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar os requisitos de autenticação multifator](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definir uma estratégia de adoção de identidade híbrida](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plano para melhorar a segurança de dados através da solução de identidade forte |[Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar os requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar os requisitos de controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar os requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir a estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planear para ciclo de vida da identidade híbrida |[Determinar as tarefas de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestão de sincronização](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar a estratégia de adoção de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Próximos Passos
[Determinar os requisitos de identidade](plan-hybrid-identity-design-considerations-business-needs.md)

