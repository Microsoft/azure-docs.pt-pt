---
title: Azure Ative Directory considerações de design de identidade híbrida - visão geral / Microsoft Docs
description: Guia de considerações de design de identidade híbrida
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381492"
---
# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerações de Design da Identidade Híbrida do Azure Active Directory
Os dispositivos baseados no consumidor estão a proliferar no mundo corporativo, e as aplicações de software como serviço (SaaS) baseadas na nuvem são fáceis de adotar. Como resultado, manter o controlo do acesso de aplicações dos utilizadores através de datacenters internos e plataformas de nuvem é um desafio.  

As soluções de identidade da Microsoft abrangem capacidades baseadas na cloud e no local, ao criar uma única identidade de utilizador para autenticação e autorização a todos os recursos, independentemente da localização. Este conceito é conhecido como Identidade Híbrida. Existem diferentes opções de design e configuração para identidade híbrida usando soluções Microsoft, e em alguns casos pode ser difícil determinar qual a combinação que melhor vai atender às necessidades da sua organização. 

Este Guia de Considerações de Design de Identidade Híbrida irá ajudá-lo a entender como conceber uma solução de identidade híbrida que melhor se adapte às necessidades de negócio e tecnologia para a sua organização.  Este guia detalha uma série de etapas e tarefas que pode seguir para ajudá-lo a desenhar uma solução de identidade híbrida que satisfaça os requisitos únicos da sua organização. Ao longo dos passos e tarefas, o guia apresentará as tecnologias e opções de funcionalidades relevantes disponíveis para as organizações para atender à qualidade funcional e de serviço (como disponibilidade, escalabilidade, desempenho, capacidade de gestão e segurança) requisitos. 

Especificamente, as considerações de design de identidade híbrida são os objetivos de responder às seguintes questões: 

* Que perguntas preciso fazer e responder para impulsionar um design híbrido específico da identidade para uma tecnologia ou domínio problemático que melhor satisfaça os meus requisitos?
* Que sequência de atividades devo completar para conceber uma solução de identidade híbrida para a tecnologia ou domínio problemático? 
* Que tecnologia de identidade híbrida e opções de configuração estão disponíveis para me ajudar a satisfazer os meus requisitos? Quais são as trocas entre essas opções para que eu possa selecionar a melhor opção para o meu negócio?

## <a name="who-is-this-guide-intended-for"></a>Este guia destina-se a quem?
 CIO, CITO, Chief Identity Architects, Enterprise Architects e IT Architects responsáveis pela conceção de uma solução de identidade híbrida para organizações médias ou grandes.

## <a name="how-can-this-guide-help-you"></a>Como é que este guia o pode ajudar?
Pode utilizar este guia para entender como conceber uma solução de identidade híbrida capaz de integrar um sistema de gestão de identidade baseado na nuvem com a sua atual solução de identidade no local. 

O gráfico seguinte mostra um exemplo de uma solução de identidade híbrida que permite aos administradores de TI conseguir integrar a sua atual solução de Diretório Ativo do Windows Server localizada no local com o Microsoft Azure Ative Directory para permitir aos utilizadores utilizarem o Single Sign-On ( O Único Sinal- On). SSO) através de aplicações localizadas na nuvem e no local.

![Exemplo](media/plan-hybrid-identity-design-considerations/hybridID-example.png)

A ilustração acima é um exemplo de uma solução de identidade híbrida que está a aproveitar os serviços de nuvem para se integrar com capacidades no local de forma a proporcionar uma única experiência ao processo de autenticação do utilizador final e facilitar a gestão de TI recursos. Embora este exemplo possa ser um cenário comum, o design de identidade híbrida de cada organização é provavelmente diferente do exemplo ilustrado na Figura 1 devido a diferentes requisitos. 

Este guia fornece uma série de etapas e tarefas que pode seguir para conceber uma solução de identidade híbrida que satisfaça os requisitos únicos da sua organização. Ao longo dos seguintes passos e tarefas, o guia apresenta as tecnologias e opções de funcionalidades relevantes disponíveis para satisfazer os requisitos de nível de qualidade funcional e de serviço para a sua organização.

**Suposições:** Tem alguma experiência com o Windows Server, Ative Directory Domain Services e Azure Ative Directory. Neste documento, presume-se que está à procura de como estas soluções podem satisfazer as suas necessidades de negócio por si só, ou numa solução integrada.

## <a name="design-considerations-overview"></a>Visão geral das considerações de conceção
Este documento fornece um conjunto de passos e tarefas que pode seguir para conceber uma solução de identidade híbrida que melhor satisfaça os seus requisitos. Os passos são apresentados numa sequência ordenada. Considerações de design que você aprende em passos posteriores pode exigir que você mude as decisões que tomou em passos anteriores, no entanto, devido a escolhas de design conflituosas. Todas as tentativas são feitas para alertá-lo para potenciais conflitos de design ao longo do documento. 

Chegará ao design que melhor satisfaz os seus requisitos apenas depois de iterar através dos passos quantas vezes for necessário para incorporar todas as considerações dentro do documento. 

| Fase de Identidade Híbrida | Lista de Tópicos |
| --- | --- |
| Determinar requisitos de identidade |[Determinar as necessidades empresariais](plan-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar os requisitos de sincronização do diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar requisitos de autenticação de vários fatores](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Defina uma estratégia híbrida de adoção de identidade](plan-hybrid-identity-design-considerations-identity-adoption-strategy.md) |
| Plano para reforçar a segurança dos dados através de uma solução de identidade forte |[Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar requisitos de gestão de conteúdos](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar requisitos do controlo de acesso](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar requisitos de resposta a incidentes](plan-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir estratégia de proteção de dados](plan-hybrid-identity-design-considerations-data-protection-strategy.md) |
| Planear para ciclo de vida da identidade híbrida |[Determinar tarefas híbridas de gestão da identidade](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestão de Sincronização](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar a estratégia de adoção de identidade híbrida](plan-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |

## <a name="next-steps"></a>Passos Seguintes
[Determinar requisitos de identidade](plan-hybrid-identity-design-considerations-business-needs.md)

