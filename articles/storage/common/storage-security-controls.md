---
title: Controlos de segurança
titleSuffix: Azure Storage
description: Consulte as listas de verificação do controlo de segurança para avaliar o Azure Storage. As áreas abrangidas são a proteção de dados, a rede, a monitorização e o registo, identidade e configuração.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91715717"
---
# <a name="security-controls-for-azure-storage"></a>Controlos de segurança para armazenamento Azure

Este artigo documenta os controlos de segurança incorporados no Azure Storage.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Proteção de dados

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Encriptação do lado do servidor em repouso: teclas geridas pela Microsoft | Sim |  |
| Encriptação do lado do servidor em repouso: teclas geridas pelo cliente (BYOK) | Sim | Consulte [a encriptação do serviço de armazenamento utilizando as chaves geridas pelo cliente no Cofre da Chave Azure](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Encriptação de nível de coluna (Serviços de Dados Azure)| N/D |  |
| Encriptação em trânsito (como encriptação ExpressRoute, na encriptação VNet e encriptação VNet-VNet)| Sim | Suportar mecanismos HTTPS/TLS padrão.  Os utilizadores também podem encriptar dados antes de serem transmitidos ao serviço. |
| Chamadas da API encriptadas| Sim |  |

## <a name="network"></a>Rede

| Controlo de segurança | Sim/Não | Notas |
|---|---|--|
| Suporte ao ponto final de serviço| Sim |  |
| Suporte de tags de serviço| Sim | Consulte [as etiquetas de serviço Azure](../../virtual-network/service-tags-overview.md) para obter mais informações sobre etiquetas de serviço suportadas pelo Azure Storage. |
| Suporte à injeção VNet| N/D |  |
| Isolamento de rede e suporte a firewall| Sim | |
| Suporte de túneis forçados| N/D |  |

## <a name="monitoring--logging"></a>Monitorização & registos

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de monitorização Azure (análise de log, insights de aplicativo, etc.)| Sim | Métricas do Monitor Azure|
| Registo e auditoria de avião de controlo e gestão | Sim | Registo de Atividades do Azure |
| Registo e auditoria de planos de dados| Sim | Registos de recursos do monitor Azure |

## <a name="identity"></a>Identidade

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Autenticação| Sim | Azure Ative Directory, Chave Partilhada, Ficha de Acesso Partilhada. |
| Autorização| Sim | Autorização de Apoio via Azure RBAC, POSIX ACLs e SAS Tokens |

## <a name="configuration-management"></a>Gestão da configuração

| Controlo de segurança | Sim/Não | Notas|
|---|---|--|
| Suporte de gestão de configuração (versão da configuração, etc.)| Sim | Versões do Fornecedor de Recursos de Suporte através de APIs do Gestor de Recursos Azure |

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre os [controlos de segurança incorporados nos serviços da Azure.](../../security/fundamentals/security-controls.md)