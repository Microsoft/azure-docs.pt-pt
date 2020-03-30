---
title: Controlos de segurança para armazenamento azure
description: Uma lista de controlos de segurança para avaliar o armazenamento do Azure
services: storage
author: msmbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 379acaf48c02f0a579c07773cd48366d962a44f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061119"
---
# <a name="security-controls-for-azure-storage"></a>Controlos de segurança para armazenamento azure

Este artigo documenta os controlos de segurança incorporados no Armazenamento Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: Chaves geridas pela Microsoft | Sim |  |
| Encriptação do lado do servidor em repouso: chaves geridas pelo cliente (BYOK) | Sim | Consulte a encriptação do serviço de [armazenamento utilizando chaves geridas pelo cliente no Cofre](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)de Chaves Azure .|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, encriptação VNet e encriptação VNet-VNet)| Sim | Suporte mecanismos PADRÃO HTTPS/TLS.  Os utilizadores também podem encriptar dados antes de serem transmitidos ao serviço. |
| Chamadas api encriptadas| Sim |  |

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte final de serviço| Sim |  |
| Suporte à injeção VNet| N/D |  |
| Isolamento da rede e suporte de firewalling| Sim | |
| Apoio de túnel forçado| N/D |  |

## <a name="monitoring--logging"></a>Monitorização & exploração madeireira

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (Análise de registo, insights de aplicações, etc.)| Sim | Métricas do Monitor Azure|
| Registo e auditoria de planos de controlo e gestão | Sim | Registo de Atividades do Gestor de Recursos Azure |
| Registo e auditoria de planos de dados| Sim | Registos de Diagnóstico de Serviço.|

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Diretório Azure Ative, Chave Partilhada, Ficha de Acesso Partilhado. |
| Autorização| Sim | Autorização de Apoio via RBAC, POSIX ACLs e SAS Tokens |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão de configuração, etc.)| Sim | Suporte fornecedor de recursos versão através de APIs do Gestor de Recursos Azure |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados em todos os serviços do Azure.](../../security/fundamentals/security-controls.md)