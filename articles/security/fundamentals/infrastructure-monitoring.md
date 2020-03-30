---
title: Monitorização de infraestruturas Azure
description: Este artigo discute a monitorização da rede de produção do Azure.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727159"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorização de infraestruturas Azure   

## <a name="configuration-and-change-management"></a>Gestão de configuração e mudança
O Azure analisa e atualiza as configurações de configuração e configurações de base de dispositivos de hardware, software e rede anualmente. As alterações são desenvolvidas, testadas e aprovadas antes de entrarem no ambiente de produção a partir de um ambiente de desenvolvimento e/ou teste.

As configurações de base que são necessárias para os serviços baseados no Azure são revistas pela equipa de segurança e conformidade do Azure e pelas equipas de serviço. Uma revisão da equipa de serviço faz parte dos testes que ocorrem antes da implantação do seu serviço de produção.

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades
A gestão da atualização de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. O Azure utiliza sistemas de implementação integrados para gerir a distribuição e instalação de atualizações de segurança para o software da Microsoft. O Azure também é capaz de recorrer aos recursos do Microsoft Security Response Center (MSRC). O MSRC identifica, monitoriza, responde e resolve incidentes de segurança e vulnerabilidades na nuvem 24 horas por dia, todos os dias do ano.

## <a name="vulnerability-scanning"></a>Digitalização de vulnerabilidades
A verificação de vulnerabilidades é realizada em sistemas operativos do servidor, bases de dados e dispositivos de rede. As análises de vulnerabilidade são realizadas trimestralmente no mínimo. O Azure contrata com avaliadores independentes para realizar testes de penetração na fronteira azure. Os exercícios da equipa vermelha também são realizados rotineiramente e os resultados são usados para fazer melhorias de segurança.

## <a name="protective-monitoring"></a>Monitorização protetora
A segurança azure definiu requisitos para monitorização ativa. As equipas de serviço configuram ferramentas de monitorização ativas de acordo com estes requisitos. As ferramentas de monitorização ativa incluem o Microsoft Monitoring Agent (MMA) e o System Center Operations Manager. Estas ferramentas estão configuradas para fornecer alertas de tempo ao pessoal de segurança do Azure em situações que requerem ação imediata.

## <a name="incident-management"></a>Gestão de Incidentes
A Microsoft implementa um processo de gestão de incidentes de segurança para facilitar uma resposta coordenada aos incidentes, caso ocorra.

Se a Microsoft tiver conhecimento do acesso não autorizado aos dados dos clientes armazenados nos seus equipamentos ou nas suas instalações, ou se tiver conhecimento do acesso não autorizado a tais equipamentos ou instalações, resultando em perdas, divulgação ou alteração de dados dos clientes, A Microsoft toma as seguintes ações:

- Imediatamente anota o cliente do incidente de segurança.
- Investiga prontamente o incidente de segurança e fornece aos clientes informações detalhadas sobre o incidente de segurança.
- Toma medidas razoáveis e rápidas para mitigar os efeitos e minimizar quaisquer danos resultantes do incidente de segurança.

Foi estabelecido um quadro de gestão de incidentes que define funções e atribui responsabilidades. A equipa de gestão de incidentes de segurança azure é responsável pela gestão de incidentes de segurança, incluindo a escalada, e pela garantia do envolvimento de equipas especializadas quando necessário. Os gestores de operações da Azure são responsáveis pela supervisão da investigação e resolução de incidentes de segurança e privacidade.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)
