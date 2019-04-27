---
title: Disponibilidade da infraestrutura do Azure
description: O artigo descreve os níveis de redundância para fornecer disponibilidade máxima dos dados dos clientes.
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
ms.openlocfilehash: a9a55f61f032512be032897d5f21ece405844634
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587199"
---
# <a name="azure-infrastructure-availability"></a>Disponibilidade da infraestrutura do Azure
O Azure fornece disponibilidade robusta, com base em ampla redundância obtida com tecnologia de virtualização. O Azure fornece vários níveis de redundância para fornecer disponibilidade máxima dos dados dos clientes.

## <a name="temporary-outages-and-natural-disaster"></a>Falhas temporárias e desastre natural
A equipe de operações de infraestrutura de nuvem da Microsoft e projeta, cria, opera e melhora a segurança da infraestrutura de nuvem. Esta equipe garante que a infraestrutura do Azure é a entrega de elevada disponibilidade e confiabilidade, alta eficiência e escalabilidade inteligente. A equipe fornece uma nuvem mais segura, privada e confiável.

Fontes de alimentação ininterrupta e grandes bancos de baterias Certifique-se de que eletricidade permanece contínua se ocorrer uma interrupção de energia de curto prazo. Os geradores de emergência fornecem o poder de cópia de segurança para falhas expandidas e a manutenção planeada. Se ocorrer um desastre natural, o Centro de dados pode utilizar as reservas de combustível no local.

Redes de alta velocidade e robusto fibra óptica ligarem os datacenters com outros hubs principais e os usuários da internet. Nós de computação alojar cargas de trabalho mais próximo aos utilizadores para reduzir a latência, fornecem redundância geográfica em aumentam a resiliência em geral do serviço. Uma equipa de engenheiros trabalha ininterruptamente para garantir que os serviços estão disponíveis de forma permanente.

Microsoft garante elevada disponibilidade através de monitorização avançada e resposta a incidentes, suporte de serviço e capacidade de ativação pós-falha de cópia de segurança. Centros de operações do Microsoft distribuídos geograficamente operam 7/24/365. A rede do Azure é um dos maiores no mundo. A rede de distribuição de conteúdo e óptica fibra liga-se nós de datacenters e periferia para se certificar de alto desempenho e confiabilidade.

## <a name="disaster-recovery"></a>Recuperação após desastre
Azure mantém os seus dados duráveis em duas localizações. Pode escolher a localização do site de cópia de segurança. Em ambos os locais, o Azure constantemente mantém três réplicas de bom estado de funcionamento dos seus dados.

## <a name="database-availability"></a>Disponibilidade de base de dados
Azure garante que uma base de dados está acessível através de um gateway de internet com disponibilidade de base de dados constante da internet. Monitorização avalia o estado de funcionamento e o estado das bases de dados Active Directory em intervalos de tempo de cinco minutos.

## <a name="storage-availability"></a>Disponibilidade de armazenamento
O Azure disponibiliza armazenamento através de um serviço de aplicações altamente escaláveis e duráveis de armazenamento, que fornece pontos finais de conectividade. Isso significa que uma aplicação pode aceder ao serviço de armazenamento diretamente. O serviço de armazenamento processa com eficiência, os pedidos recebidos de armazenamento com integridade transacional.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)
