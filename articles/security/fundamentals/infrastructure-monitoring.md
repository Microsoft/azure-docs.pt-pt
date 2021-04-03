---
title: Monitorização das infraestruturas do Azure
description: Conheça os aspetos de monitorização da infraestrutura da rede de produção Azure, como a digitalização de vulnerabilidades.
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
ms.openlocfilehash: 7b75c9dc874a41d4221c55a8b00dd12d943e80fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87542947"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorização das infraestruturas do Azure   

## <a name="configuration-and-change-management"></a>Gestão de configuração e mudança
Azure analisa e atualiza configurações de configuração e configurações de base de hardware, software e dispositivos de rede anualmente. As alterações são desenvolvidas, testadas e aprovadas antes de entrarem no ambiente de produção a partir de um ambiente de desenvolvimento e/ou teste.

As configurações de base necessárias para os serviços baseados no Azure são revistas pela equipa de segurança e conformidade da Azure e por equipas de serviço. Uma revisão da equipa de serviço faz parte do teste que ocorre antes da implantação do seu serviço de produção.

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades
A gestão de atualizações de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. O Azure utiliza sistemas de implementação integrados para gerir a distribuição e instalação de atualizações de segurança para o software da Microsoft. O Azure também é capaz de recorrer aos recursos do Microsoft Security Response Center (MSRC). O MSRC identifica, monitoriza, responde e resolve incidentes de segurança e vulnerabilidades na nuvem 24 horas por dia, todos os dias do ano.

## <a name="vulnerability-scanning"></a>Análise de vulnerabilidades
A verificação de vulnerabilidades é realizada em sistemas operativos de servidores, bases de dados e dispositivos de rede. As análises de vulnerabilidade são realizadas trimestralmente no mínimo. A Azure contrata com avaliadores independentes para realizar testes de penetração da fronteira de Azure. Os exercícios da equipa vermelha também são realizados rotineiramente e os resultados são usados para fazer melhorias de segurança.

## <a name="protective-monitoring"></a>Monitorização protetora
A segurança Azure definiu requisitos para monitorização ativa. As equipas de serviço configuram ferramentas de monitorização ativas de acordo com estes requisitos. As ferramentas de monitorização ativas incluem o Microsoft Monitoring Agent (MMA) e o System Center Operations Manager. Estas ferramentas estão configuradas para fornecer alertas de tempo ao pessoal de segurança da Azure em situações que requerem ação imediata.

## <a name="incident-management"></a>Gestão de Incidentes
A Microsoft implementa um processo de gestão de incidentes de segurança para facilitar uma resposta coordenada aos incidentes, caso ocorra.

Se a Microsoft tomar conhecimento do acesso não autorizado aos dados dos clientes armazenados nos seus equipamentos ou nas suas instalações, ou tomar conhecimento de um acesso não autorizado a esses equipamentos ou instalações, resultando em perda, divulgação ou alteração dos dados dos clientes, a Microsoft toma as seguintes ações:

- Notifica prontamente o cliente do incidente de segurança.
- Investiga prontamente o incidente de segurança e fornece aos clientes informações detalhadas sobre o incidente de segurança.
- Toma medidas razoáveis e rápidas para mitigar os efeitos e minimizar quaisquer danos resultantes do incidente de segurança.

Foi estabelecido um quadro de gestão de incidentes que define funções e atribui responsabilidades. A equipa de gestão de incidentes de segurança da Azure é responsável pela gestão de incidentes de segurança, incluindo a escalada, e garantir o envolvimento de equipas especializadas quando necessário. Os gestores de operações da Azure são responsáveis pela supervisão da investigação e resolução de incidentes de segurança e privacidade.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
