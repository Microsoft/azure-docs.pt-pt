---
title: Disponibilidade de infraestrutura do Azure-segurança do Azure
description: Este artigo fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure e fornecer a máxima disponibilidade dos dados dos clientes.
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: c50c4faf47caf0a7519d61fdc8989ec9fd809d78
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727222"
---
# <a name="azure-infrastructure-availability"></a>Disponibilidade de infraestrutura do Azure
Este artigo fornece informações sobre o que a Microsoft faz para proteger a infraestrutura do Azure e fornecer a máxima disponibilidade dos dados dos clientes. O Azure fornece disponibilidade robusta, com base na ampla redundância obtida com a tecnologia de virtualização.

## <a name="temporary-outages-and-natural-disaster"></a>Interrupções temporárias e desastre natural
A equipe de Microsoft Cloud infraestrutura e operações projeta, compila, Opera e melhora a segurança da infraestrutura de nuvem. Essa equipe garante que a infraestrutura do Azure esteja fornecendo alta disponibilidade e confiabilidade, alta eficiência e escalabilidade inteligente. A equipe fornece uma nuvem mais segura, privada e confiável.

As fontes de alimentação ininterrupta e os vastos bancos de baterias garantem que a eletricidade permaneça contínua se ocorrer uma interrupção de energia de curto prazo. Os geradores de emergência fornecem energia de backup para interrupções estendidas e manutenção planejada. Se ocorrer um desastre natural, o datacenter poderá usar reservas de combustível no local.

Redes de fibra óptica de alta velocidade e robustas conectam data centers com outros principais hubs e usuários da Internet. Os nós de computação hospedam cargas de trabalho mais próximas dos usuários para reduzir a latência, fornecer redundância geográfica e aumentar a resiliência geral do serviço. Uma equipe de engenheiros trabalha 24 horas por dia para garantir que os serviços estejam disponíveis de forma persistente.

A Microsoft garante alta disponibilidade por meio de monitoramento avançado e resposta a incidentes, suporte a serviços e recurso de failover de backup. Os Microsoft Operations centers distribuídos geograficamente operam 24/7/365. A rede do Azure é um dos maiores no mundo. A rede de distribuição de conteúdo e fibra óptica conecta datacenters e nós de borda para garantir alto desempenho e confiabilidade.

## <a name="disaster-recovery"></a>Recuperação após desastre
O Azure mantém seus dados duráveis em dois locais. Você pode escolher o local do site de backup. Em ambos os locais, o Azure mantém constantemente três réplicas íntegras de seus dados.

## <a name="database-availability"></a>Disponibilidade do banco de dados
O Azure garante que um banco de dados seja acessível pela Internet por meio de um gateway de Internet com disponibilidade de banco de dados sustentada. O monitoramento avalia a integridade e o estado dos bancos de dados ativos em intervalos de cinco minutos de tempo.

## <a name="storage-availability"></a>Disponibilidade de armazenamento
O Azure fornece armazenamento por meio de um serviço de armazenamento altamente escalonável e durável, que fornece pontos de extremidade de conectividade. Isso significa que um aplicativo pode acessar o serviço de armazenamento diretamente. O serviço de armazenamento processa solicitações de armazenamento de entrada com eficiência, com integridade transacional.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)
