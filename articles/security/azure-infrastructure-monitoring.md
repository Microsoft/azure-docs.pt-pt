---
title: Monitorização de infraestrutura do Azure
description: Este artigo aborda a monitorização de rede de produção do Azure.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 88bc76116392140c533f67402642c68d714227c0
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60591645"
---
# <a name="azure-infrastructure-monitoring"></a>Monitorização de infraestrutura do Azure   

## <a name="configuration-and-change-management"></a>Gestão de configurações e alterações
O Azure analisa e atualiza as definições de configuração e configurações de linha de base de hardware, software e dispositivos de rede anualmente. As alterações são desenvolvidas, testadas e comprovadas antes de entrar no ambiente de produção de um ambiente de desenvolvimento e/ou de teste.

As configurações de linha de base que são necessárias para serviços baseados no Azure são revisadas pela equipa de segurança e conformidade do Azure e as equipas de serviço. Uma revisão da equipe de serviço faz parte dos testes de que ocorre antes da implantação do seu serviço de produção.

## <a name="vulnerability-management"></a>Gestão de vulnerabilidades
Gestão de atualizações de segurança ajuda a proteger os sistemas de vulnerabilidades conhecidas. Sistemas de implementação integrada do Azure utiliza para gerir a distribuição e instalação de atualizações de segurança de software da Microsoft. O Azure também é capaz de aproveitar os recursos do Microsoft Security Response Center (MSRC). O MSRC identifica, monitoriza, responde a e resolve incidentes de segurança e na cloud vulnerabilidades ininterruptamente, todos os dias do ano.

## <a name="vulnerability-scanning"></a>Análise de vulnerabilidades
Análise de vulnerabilidade é realizada em sistemas operativos de servidor, bases de dados e dispositivos de rede. As análises de vulnerabilidade são executadas de forma trimestral, no mínimo. Contratos do Azure com assessores independentes para executar testes de penetração do limite do Azure. Red-team exercícios rotineiramente também são executados e os resultados são utilizados para fazer melhorias de segurança.

## <a name="protective-monitoring"></a>Monitorização de proteção
Segurança do Azure tiver definido os requisitos para monitorização do Active Directory. As equipas de serviço configure Active Directory ferramentas de monitorização em conformidade com esses requisitos. Ferramentas de monitorização de Active Directory incluem o Microsoft Monitoring Agent (MMA) e o System Center Operations Manager. Essas ferramentas são configuradas para fornecer alertas em tempo ao pessoal de segurança do Azure em situações que exijam uma ação imediata.

## <a name="incident-management"></a>gestão de incidentes
Microsoft implementa um processo de gestão de incidentes de segurança para facilitar uma coordenada de resposta a incidentes, um deve ocorrer.

Se a Microsoft torna-se atento ao acesso não autorizado aos dados dos clientes, que são armazenados em seu equipamento ou em suas instalações ou torna-se atento ao acesso não autorizado a esse equipamentos ou instalações, resultando em perda, a divulgação ou a alteração dos dados dos clientes, A Microsoft leva as seguintes ações:

- Notifica imediatamente o cliente de incidente de segurança.
- Imediatamente investiga o incidente de segurança e fornece aos clientes informações detalhadas sobre o incidente de segurança.
- Demora passos razoáveis e de linha de comandos para atenuar os efeitos e minimizar qualquer dano resultante do incidente de segurança.

É estabelecida uma estrutura de gestão de incidentes que define as funções e responsabilidades a atribui. A equipe de gerenciamento de incidentes de segurança do Azure é responsável por gerir incidentes de segurança, incluindo o escalonamento e assegurar o envolvimento das equipes de especialista, quando necessário. Gerentes de operações do Azure são responsáveis por supervisionar a investigação e a resolução de incidentes de segurança e privacidade.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)
