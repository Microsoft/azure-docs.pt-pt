---
title: Responsabilidade partilhada na nuvem - Microsoft Azure
description: Compreenda o modelo de responsabilidade partilhada e quais as tarefas de segurança que são tratadas pelo provedor da nuvem e quais as tarefas que são tratadas por si.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
editor: na
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: terrylan
ms.openlocfilehash: 5d9d3878c34bd203b6c14b45e1196245ef8524f6
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99548954"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilidade partilhada na cloud

Ao considerar e avaliar os serviços públicos na nuvem, é fundamental compreender o modelo de responsabilidade partilhada e quais as tarefas de segurança que são tratadas pelo provedor da nuvem e quais as tarefas que são tratadas por si. As responsabilidades de carga de trabalho variam consoante a carga de trabalho seja hospedada em Software como serviço (SaaS), Plataforma como Serviço (PaaS), Infraestrutura como Serviço (IaaS), ou num datacenter no local

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
Num datacenter no local, és dono de toda a pilha. À medida que avanças para a nuvem, algumas responsabilidades transferem-se para a Microsoft. O diagrama que se segue ilustra as áreas de responsabilidade entre si e a Microsoft, de acordo com o tipo de implementação da sua stack.

![Zonas de responsabilidade](./media/shared-responsibility/shared-responsibility.png)

É o proprietário dos seus dados e identidades, independentemente dos tipos de implementação na cloud. É responsável por proteger a segurança dos seus dados e identidades, dos recursos no local e dos componentes da nuvem que controla (que varia por tipo de serviço).

Independentemente do tipo de implantação, as seguintes responsabilidades são sempre mantidas por si:

- Dados
- Pontos Finais
- Conta
- Gestão de acesso

## <a name="cloud-security-advantages"></a>Vantagens de segurança na nuvem
A nuvem oferece vantagens significativas para resolver desafios de segurança de informação de longa data. Num ambiente no local, as organizações provavelmente têm responsabilidades insatisfeitas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente onde os atacantes são capazes de explorar vulnerabilidades em todas as camadas.

O diagrama que se segue mostra uma abordagem tradicional em que muitas responsabilidades de segurança não são cumpridas devido a recursos limitados. Na abordagem ativada pela nuvem, é possível transferir as responsabilidades de segurança do dia-a-dia para o seu fornecedor de nuvem e realocar os seus recursos.

![Vantagens de segurança da era da nuvem](./media/shared-responsibility/cloud-enabled-security.png)

Na abordagem ativada pela nuvem, também é capaz de aproveitar as capacidades de segurança baseadas na nuvem para obter mais eficácia e usar inteligência em nuvem para melhorar o seu tempo de deteção e resposta de ameaças. Ao transferir responsabilidades para o fornecedor de nuvem, as organizações podem obter mais cobertura de segurança, o que lhes permite realocar recursos de segurança e orçamento para outras prioridades do negócio.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a divisão de responsabilidades entre si e a Microsoft numa implementação de SaaS, PaaS e IaaS, consulte [responsabilidades partilhadas para computação](https://azure.microsoft.com/resources/shared-responsibility-for-cloud-computing/)em nuvem.
