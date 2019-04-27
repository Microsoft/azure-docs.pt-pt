---
title: Segurança da infraestrutura do Azure | Documentos da Microsoft
description: O artigo descreve como a Microsoft trabalha para proteger os nossos datacenters do Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: dc9b4db37e811d8bac6df2d532fd3629d98c9650
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586579"
---
# <a name="azure-infrastructure-security"></a>Segurança da infraestrutura do Azure
Microsoft Azure é executado em datacenters gerenciado e operado pela Microsoft. Estes datacenters dispersos geograficamente está em conformidade com padrões da indústria de chave, como a ISO/IEC 27001:2013 e SP de NIST 800-53, segurança e confiabilidade. Centros de dados são geridos, monitorizados e administrados pela equipe de operações do Microsoft. A equipa de operações tem anos de experiência no fornecimento de maiores serviços online do mundo com 24x7 continuidade.

Esta série de artigos fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure. O endereço de artigos:

- [segurança física](azure-physical-security.md)
- [Disponibilidade](azure-infrastructure-availability.md)
- [Limites e componentes](azure-infrastructure-components.md)
- [Arquitetura de rede](azure-infrastructure-network.md)
- [Rede de produção](azure-production-network.md)
- [Base de Dados SQL](azure-infrastructure-sql.md)
- [Operações](azure-infrastructure-operations.md)
- [Monitorização](azure-infrastructure-monitoring.md)
- [Integridade](azure-infrastructure-integrity.md)
- [Proteção de dados](azure-protection-of-customer-data.md)

## <a name="shared-responsibility-model"></a>Modelo de responsabilidade partilhada
É importante entender a divisão de responsabilidade entre e a Microsoft. No local, que possui a pilha inteira, mas como mover para a cloud, algumas responsabilidades transfere para a Microsoft. O gráfico seguinte ilustra as áreas de responsabilidade, de acordo com o tipo de implementação de sua pilha (software como um serviço [SaaS], plataforma como serviço [PaaS], infraestrutura como serviço [IaaS] e no local).

![Gráfico com responsabilidades][1]

Sempre é responsáveis pelo seguinte, independentemente do tipo de implementação:

- Dados
- Pontos Finais
- Conta
- Gestão de acesso

Certifique-se de que compreende a divisão de responsabilidade entre e a Microsoft numa implantação de SaaS, PaaS e IaaS. Para obter mais informações, consulte [partilhado responsabilidades para a computação em nuvem](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-security-infrastructure/responsibility-zones.png
