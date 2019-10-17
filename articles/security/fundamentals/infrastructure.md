---
title: Segurança de infraestrutura do Azure | Microsoft Docs
description: O artigo descreve como a Microsoft trabalha para proteger nossos data centers do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: a17d98d49d2c653e2498a663829d26e8a171fd74
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72433505"
---
# <a name="azure-infrastructure-security"></a>Segurança de infraestrutura do Azure
O Microsoft Azure é executado em data centers gerenciados e operados pela Microsoft. Esses data centers dispersos geograficamente estão em conformidade com os principais padrões do setor, como ISO/IEC 27001:2013 e NIST SP 800-53, para segurança e confiabilidade. Os data centers são gerenciados, monitorados e administrados pela equipe de operações da Microsoft. A equipe de operações tem anos de experiência no fornecimento do maior serviços online do mundo com continuidade de 24 x 7.

Esta série de artigos fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure. O endereço do artigo:

- [Segurança física](physical-security.md)
- [Disponibilidade](infrastructure-availability.md)
- [Componentes e limites](infrastructure-components.md)
- [Arquitetura de rede](infrastructure-network.md)
- [Rede de produção](production-network.md)
- [Base de Dados SQL](infrastructure-sql.md)
- [Operações](infrastructure-operations.md)
- [Monitorização](infrastructure-monitoring.md)
- [Verifica](infrastructure-integrity.md)
- [Proteção de dados](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade compartilhada
É importante entender a divisão de responsabilidade entre você e a Microsoft. No local, você possui a pilha inteira, mas à medida que passa para a nuvem, algumas responsabilidades são transferidas para a Microsoft. O gráfico a seguir ilustra as áreas de responsabilidade, de acordo com o tipo de implantação da pilha (software como serviço [SaaS], plataforma como serviço [PaaS], infraestrutura como serviço [IaaS] e local).

![Gráfico mostrando responsabilidades](./media/infrastructure/responsibility-zones.png)

Você é sempre responsável pelo seguinte, independentemente do tipo de implantação:

- Dados
- Pontos Finais
- Conta
- Gestão de acesso

Certifique-se de entender a divisão de responsabilidade entre você e a Microsoft em uma implantação de SaaS, PaaS e IaaS. Para obter mais informações, consulte [responsabilidades compartilhadas para computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/225237/1/Shared%20Responsibilities%20for%20Cloud%20Computing%20(2017-04-03).pdf).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)


