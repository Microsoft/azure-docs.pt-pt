---
title: Controlos de segurança
titleSuffix: Azure Storage
description: Uma lista de controlos de segurança para avaliar o Armazenamento Azure.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 96fde15eb5071e157fedcff6154e6b0635a34721
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82128027"
---
# <a name="security-controls-for-azure-storage"></a>Controlos de segurança para armazenamento Azure

Este artigo documenta os controlos de segurança incorporados no Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Yes |  |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Yes | Consulte [a encriptação do serviço de armazenamento utilizando as chaves geridas pelo cliente no Cofre da Chave Azure](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Yes | Suportar mecanismos HTTPS/TLS padrão.  Os utilizadores também podem encriptar dados antes de serem transmitidos ao serviço. |
| Chamadas da API encriptadas| Yes |  |

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Yes |  |
| Suporte de tags de serviço| Yes | Consulte [as etiquetas de serviço Azure](../../virtual-network/service-tags-overview.md) para obter mais informações sobre etiquetas de serviço suportadas pelo Azure Storage. |
| Suporte à injeção VNet| N/D |  |
| Isolamento de rede e suporte a firewall| Yes | |
| Suporte de túneis forçados| N/D |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Yes | Métricas do Monitor Azure|
| Registo e auditoria de avião de controlo e gestão | Yes | Registo de Atividades do Azure |
| Registo e auditoria de planos de dados| Yes | Registos de recursos do monitor Azure |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Yes | Azure Ative Directory, Chave Partilhada, Ficha de Acesso Partilhada. |
| Autorização| Yes | Autorização de Apoio via RBAC, POSIX ACLs e Sas Tokens |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Yes | Versões do Fornecedor de Recursos de Suporte através de APIs do Gestor de Recursos Azure |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)