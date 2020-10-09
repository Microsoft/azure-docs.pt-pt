---
title: Disponibilidade de infraestrutura Azure - Segurança Azure
description: Este artigo fornece informações sobre o que a Microsoft faz para proteger a infraestrutura Azure e fornecer a máxima disponibilidade dos dados dos clientes.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68727222"
---
# <a name="azure-infrastructure-availability"></a>Disponibilidade de infraestrutura Azure
Este artigo fornece informações sobre o que a Microsoft faz para proteger a infraestrutura Azure e fornecer a máxima disponibilidade dos dados dos clientes. O Azure proporciona uma disponibilidade robusta, baseada na extensa redundância alcançada com a tecnologia de virtualização.

## <a name="temporary-outages-and-natural-disaster"></a>Interrupções temporárias e desastre natural
A equipa de Infraestruturas e Operações da Microsoft Cloud projeta, constrói, opera e melhora a segurança da infraestrutura em nuvem. Esta equipa garante que a infraestrutura Azure está a oferecer elevada disponibilidade e fiabilidade, alta eficiência e escalabilidade inteligente. A equipa fornece uma nuvem mais segura, privada e de confiança.

Fontes de alimentação ininterruptas e grandes bancos de baterias asseguram que a eletricidade permanece contínua se ocorrer uma rutura de energia a curto prazo. Os geradores de emergência fornecem energia de reserva para interrupções prolongadas e manutenção planeada. Se ocorrer uma catástrofe natural, o datacenter pode utilizar reservas de combustível no local.

Redes de fibra ótica de alta velocidade e robustas conectam datacenters com outros grandes centros e internautas. Os nós computatórios hospedam cargas de trabalho mais próximas dos utilizadores para reduzir a latência, fornecer geo-redundância e aumentar a resiliência geral do serviço. Uma equipa de engenheiros trabalha 24 horas por dia para garantir que os serviços estão persistentemente disponíveis.

A Microsoft garante uma elevada disponibilidade através de monitorização avançada e resposta a incidentes, suporte ao serviço e capacidade de falha de backup. Os centros de operações da Microsoft distribuídos geograficamente operam 24/7/365. A rede Azure é uma das maiores do mundo. A rede de distribuição de fibra ótica e de conteúdo liga datacenters e nós de borda para garantir um alto desempenho e fiabilidade.

## <a name="disaster-recovery"></a>Recuperação após desastre
A Azure mantém os seus dados duráveis em dois locais. Pode escolher a localização do site de reserva. Em ambos os locais, o Azure mantém constantemente três réplicas saudáveis dos seus dados.

## <a name="database-availability"></a>Disponibilidade da base de dados
O Azure garante que uma base de dados é acessível à Internet através de um portal de internet com disponibilidade de base de dados sustentada. A monitorização avalia a saúde e o estado das bases de dados ativas em intervalos de cinco minutos.

## <a name="storage-availability"></a>Disponibilidade de armazenamento
O Azure fornece armazenamento através de um serviço de armazenamento altamente escalável e durável, que fornece pontos finais de conectividade. Isto significa que uma aplicação pode aceder diretamente ao serviço de armazenamento. O serviço de armazenamento processa pedidos de armazenamento de entrada eficientemente, com integridade transacional.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
