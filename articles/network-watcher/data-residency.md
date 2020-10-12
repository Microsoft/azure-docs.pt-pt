---
title: Residência de dados para Azure Network Watcher Microsoft Docs
description: Este artigo irá ajudá-lo a compreender a residência de dados para o serviço Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
manager: ''
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/20/2020
ms.author: damendo
ms.openlocfilehash: 9451b6636f2f87806e3d1e39fec4e9e4d4390485
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706831"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residência de dados para Azure Network Watcher
Com exceção do serviço Connection Monitor (Preview), o Azure Network Watcher não armazena os dados do cliente.


## <a name="connection-monitor-preview-data-residency"></a>Residência de dados do Monitor de Ligação (Pré-visualização)
O serviço Connection Monitor (Preview) armazena os dados do cliente. Estes dados são automaticamente armazenados pelo Network Watcher numa única região. Assim, o Monitor de Ligação (Pré-visualização) satisfaz automaticamente os requisitos de residência de dados na região, incluindo requisitos especificados no [Trust Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Residência de dados de Singapura

Em Azure, a funcionalidade que permite armazenar dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) do geo da Ásia-Pacífico. Para todas as outras regiões, os dados dos clientes são armazenados em geo. Para mais informações, consulte o [Trust Center.](https://azuredatacentermap.azurewebsites.net/)

## <a name="next-steps"></a>Passos seguintes

* Leia uma visão geral do [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
