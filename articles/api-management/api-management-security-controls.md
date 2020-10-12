---
title: Controlos de segurança para a Azure API Management
description: Reveja uma lista de controlos de segurança para avaliar a Gestão da API. Estes incluem controlos de rede, identidade e proteção de dados.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: vlvinogr
ms.openlocfilehash: a147179f7b55e43379b3c3fa3a7a0767cc97b198
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87902580"
---
# <a name="security-controls-for-api-management"></a>Controlos de segurança para a Gestão da API

Este artigo documenta os controlos de segurança incorporados na API Management.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Suporte ao ponto final de serviço| Não | |  |
| Suporte à injeção VNet| Sim | |  |
| Isolamento de rede e suporte de firewall| Sim | Utilizando grupos de segurança de rede (NSG) e Azure Application Gateway (ou outro aparelho de software) respectivamente. |  |
| Suporte de túneis forçados| Sim | A ligação em rede Azure fornece túneis forçados. |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | | |
| Registo e auditoria de avião de controlo e gestão| Sim | [Registos de atividade do Monitor Azure](../azure-monitor/platform/platform-logs-overview.md) | |
| Registo e auditoria de planos de dados| Sim | [Registos de diagnóstico do Azure Monitor](../azure-monitor/platform/platform-logs-overview.md) e (opcionalmente) [Azure Application Insights](../azure-monitor/app/app-insights-overview.md).  | |


## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Autenticação| Sim | |  |
| Autorização| Sim | |  |

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas | Documentação |
|---|---|--|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim | Dados sensíveis, tais como certificados, chaves e valores de nome secreto são encriptados com chaves geridas pelo serviço, por instância de serviço. |  |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Não | Todas as chaves de encriptação são por instância de serviço e são geridas pelo serviço. |  |
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D | |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | [A encriptação Express Route](../expressroute/index.yml) e VNet é fornecida pela [rede Azure](../virtual-network/index.yml). |  |
| Chamadas da API encriptadas| Sim | As chamadas de avião de gestão são feitas através [do Azure Resource Manager](../azure-resource-manager/index.yml) sobre o TLS. É necessário um token web JSON válido (JWT).  As chamadas de plano de dados podem ser asseguradas com TLS e um dos mecanismos de autenticação suportados (por exemplo, certificado de cliente ou JWT). |   |
 |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas| Documentação |
|---|---|--|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | Utilizando o [Kit de Recursos de Gestão de API da Azure](https://aka.ms/apimdevops) |  |

## <a name="vulnerability-scans-false-positives"></a>Vulnerabilidade digitaliza falsos positivos

Esta secção documenta vulnerabilidades comuns que não afetam a Azure API Management.

| Vulnerabilidade               | Descrição                                                                                                                                                                                                                                                                                                               |
|-----------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ticketbleed (CVE-2016-9244) | A ticketbleed é uma vulnerabilidade na implementação da extensão TLS SessionTicket encontrada em alguns produtos F5. Permite a fuga ("sangramento") de até 31 bytes de dados de memória não iniializada. Isto é causado pela pilha TLS acolchoando um ID de sessão, passado do cliente, com dados para torná-lo 32 bits de comprimento. |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../security/fundamentals/security-controls.md)