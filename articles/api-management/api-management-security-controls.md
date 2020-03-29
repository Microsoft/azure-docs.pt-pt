---
title: Controlos de segurança para a Gestão da API Azure
description: Uma lista de controlos de segurança para avaliar a Gestão da API
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: 670050efe01fb658fab52a43914f193e9798b828
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75751134"
---
# <a name="security-controls-for-api-management"></a>Controlos de segurança para gestão da API

Este artigo documenta os controlos de segurança incorporados na Gestão API.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte final de serviço| Não | |  |
| Suporte à injeção VNet| Sim | |  |
| Isolamento da rede e suporte de firewalling| Sim | Utilizando grupos de segurança em rede (NSG) e Gateway de Aplicações Azure (ou outro aparelho de software) respectivamente. |  |
| Apoio de túnel forçado| Sim | A rede Azure fornece túneis forçados. |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | | |
| Registo e auditoria de planos de controlo e gestão| Sim | [Registos de atividade do Monitor Azure](../azure-monitor/platform/platform-logs-overview.md) | |
| Registo e auditoria de planos de dados| Sim | [Registos de diagnóstico do Monitor Azure](../azure-monitor/platform/platform-logs-overview.md) e (opcionalmente) [Insights de aplicação azure](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | |  |
| Autorização| Sim | |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim | Dados sensíveis, tais como certificados, chaves e valores de nome secreto, são encriptados com chaves geridas pelo serviço, por chave de instância de serviço. |  |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Não | Todas as chaves de encriptação são por instância de serviço e são geridas pelo serviço. |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | [A](../expressroute/index.yml) encriptação Express Route e VNet é fornecida pela [rede Azure.](../virtual-network/index.yml) |  |
| Chamadas api encriptadas| Sim | As chamadas de avião de gestão são feitas através [do Azure Resource Manager](../azure-resource-manager/index.yml) sobre o TLS. É necessário um token web JSON válido (JWT).  As chamadas de avião de dados podem ser asseguradas com TLS e um dos mecanismos de autenticação suportados (por exemplo, certificado de cliente ou JWT). |   |
 |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Utilização do Kit de [Recursos DevOps de Gestão API Azure](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Vulnerabilidade digitaliza falsos positivos

Esta secção documenta vulnerabilidades comuns que não afetam a Gestão da API azure.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sangramento de bilhete (CVE-2016-9244) | Ticketbleed é vulnerabilidade na implementação da extensão TLS SessionTicket encontrada em alguns produtos F5. Permite a fuga ("sangramento") de até 31 bytes de dados provenientes de memória não inicializada. Isto é causado pela pilha TLS acolchoando um ID de sessão, passado do cliente, com dados para torná-lo 32 bits de comprimento. |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../security/fundamentals/security-controls.md)