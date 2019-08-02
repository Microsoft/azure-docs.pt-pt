---
title: Monitoramento de infraestrutura do Azure
description: Este artigo discute o monitoramento da rede de produção do Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: af9c157b4644156edc6dcdb1b53c141263576500
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727159"
---
# <a name="azure-infrastructure-monitoring"></a>Monitoramento de infraestrutura do Azure   

## <a name="configuration-and-change-management"></a>Configuração e gerenciamento de alterações
O Azure revisa e atualiza definições de configuração e configurações de linha de base de hardware, software e dispositivos de rede anualmente. As alterações são desenvolvidas, testadas e aprovadas antes de entrar no ambiente de produção a partir de um ambiente de desenvolvimento e/ou teste.

As configurações de linha de base necessárias para os serviços baseados no Azure são revisadas pela equipe de segurança e conformidade do Azure e pelas equipes de serviço. Uma análise da equipe de serviço faz parte do teste que ocorre antes da implantação de seu serviço de produção.

## <a name="vulnerability-management"></a>Gerenciamento de vulnerabilidades
O gerenciamento de atualizações de segurança ajuda a proteger sistemas contra vulnerabilidades conhecidas. O Azure usa sistemas de implantação integrados para gerenciar a distribuição e a instalação de atualizações de segurança para softwares da Microsoft. O Azure também pode desenhar sobre os recursos do MSRC (Microsoft Security Response Center). O MSRC identifica, monitora, responde e resolve incidentes de segurança e vulnerabilidades de nuvem 24 horas por dia, todos os dias do ano.

## <a name="vulnerability-scanning"></a>Verificação de vulnerabilidade
A verificação de vulnerabilidades é executada em sistemas operacionais de servidor, bancos de dados e dispositivos de rede. As verificações de vulnerabilidade são executadas a cada trimestre, no mínimo. O Azure contrai com avaliadores independentes para realizar testes de penetração do limite do Azure. Os exercícios da equipe vermelha também são realizados rotineiramente e os resultados são usados para fazer melhorias de segurança.

## <a name="protective-monitoring"></a>Monitoramento de proteção
A segurança do Azure definiu os requisitos para o monitoramento ativo. As equipes de serviço configuram ferramentas de monitoramento ativas de acordo com esses requisitos. As ferramentas de monitoramento ativas incluem o Microsoft Monitoring Agent (MMA) e System Center Operations Manager. Essas ferramentas são configuradas para fornecer alertas de tempo para a equipe de segurança do Azure em situações que exigem ação imediata.

## <a name="incident-management"></a>gestão de incidentes
A Microsoft implementa um processo de gerenciamento de incidentes de segurança para facilitar uma resposta coordenada aos incidentes, caso ocorra um.

Se a Microsoft estiver ciente do acesso não autorizado aos dados do cliente armazenados em seu equipamento ou em suas instalações, ou se estiver ciente do acesso não autorizado a tais equipamentos ou instalações, resultando em perda, divulgação ou alteração de dados do cliente, A Microsoft executa as seguintes ações:

- Notifica imediatamente o cliente sobre o incidente de segurança.
- Investiga imediatamente o incidente de segurança e fornece aos clientes informações detalhadas sobre o incidente de segurança.
- O toma as medidas razoáveis e de prompt para mitigar os efeitos e minimizar qualquer dano resultante do incidente de segurança.

Foi estabelecida uma estrutura de gerenciamento de incidentes que define funções e aloca responsabilidades. A equipe de gerenciamento de incidentes de segurança do Azure é responsável por gerenciar incidentes de segurança, incluindo o escalonamento e garantir o envolvimento das equipes de especialistas quando necessário. Os gerenciadores de operações do Azure são responsáveis por supervisionar a investigação e a resolução de incidentes de segurança e privacidade.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)
