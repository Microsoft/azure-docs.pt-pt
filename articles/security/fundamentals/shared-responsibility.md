---
title: Responsabilidade partilhada na nuvem - Microsoft Azure
description: Compreenda o modelo de responsabilidade partilhada e quais as tarefas de segurança que são tratadas pelo fornecedor de nuvem e quais as tarefas que são tratadas por si.
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
ms.date: 10/16/2019
ms.author: terrylan
ms.openlocfilehash: 8f16105d6bda1798828bc423ec8a158d49e0cf2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "72518372"
---
# <a name="shared-responsibility-in-the-cloud"></a>Responsabilidade partilhada na cloud

Ao considerar e avaliar os serviços públicos de nuvem, é fundamental compreender o modelo de responsabilidade partilhada e quais as tarefas de segurança que são tratadas pelo fornecedor de nuvem e quais as tarefas que são tratadas por si. As responsabilidades de carga de trabalho variam consoante a carga de trabalho seja hospedada no Software como Serviço (SaaS), Plataforma como Serviço (PaaS), Infraestrutura como Serviço (IaaS), ou num centro de dados no local

## <a name="division-of-responsibility"></a>Divisão de responsabilidade
Num centro de dados no local, és dono de toda a pilha. À medida que se move para a nuvem, algumas responsabilidades transferem para a Microsoft. O diagrama que se segue ilustra as áreas de responsabilidade entre si e a Microsoft, de acordo com o tipo de implementação da sua stack.

![Zonas de responsabilidade](./media/shared-responsibility/shared-responsibility.png)

É o proprietário dos seus dados e identidades, independentemente dos tipos de implementação na cloud. É responsável por proteger a segurança dos seus dados e identidades, recursos no local e os componentes da nuvem que controla (que varia por tipo de serviço).

Independentemente do tipo de implantação, as seguintes responsabilidades são sempre mantidas por si:

- Dados
- Pontos Finais
- Conta
- Gestão de acesso

## <a name="cloud-security-advantages"></a>Vantagens de segurança na nuvem
A nuvem oferece vantagens significativas para resolver desafios de segurança da informação de longa data. Num ambiente no local, as organizações provavelmente têm responsabilidades não atendidas e recursos limitados disponíveis para investir em segurança, o que cria um ambiente onde os atacantes são capazes de explorar vulnerabilidades em todas as camadas.

O diagrama seguinte mostra uma abordagem tradicional onde muitas responsabilidades de segurança não são cumpridas devido a recursos limitados. Na abordagem ativada pela nuvem, é capaz de transferir as responsabilidades de segurança do dia-a-dia para o seu fornecedor de nuvem e realocar os seus recursos.

![Vantagens de segurança da era da nuvem](./media/shared-responsibility/cloud-enabled-security.png)

Na abordagem ativada pela nuvem, você também é capaz de aproveitar as capacidades de segurança baseadas na nuvem para uma maior eficácia e usar a inteligência na nuvem para melhorar o seu tempo de deteção e resposta de ameaça. Ao transferir responsabilidades para o fornecedor de nuvem, as organizações podem obter mais cobertura de segurança, o que lhes permite realocar recursos de segurança e orçamento para outras prioridades do negócio.

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre a divisão de responsabilidade entre si e a Microsoft numa implementação saaS, PaaS e IaaS, consulte [responsabilidades partilhadas para](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91)a computação em nuvem .
