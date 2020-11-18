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
ms.openlocfilehash: a6be3a7fd19b43bd1b18af05d0dbfaf5053fb181
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94682966"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residência de dados para Azure Network Watcher
Com exceção do serviço Connection Monitor (Preview), o Azure Network Watcher não armazena os dados do cliente.


## <a name="connection-monitor-preview-data-residency"></a>Residência de dados do Monitor de Ligação (Pré-visualização)
O serviço Connection Monitor (Preview) armazena os dados do cliente. Estes dados são automaticamente armazenados pelo Network Watcher numa única região. Assim, o Monitor de Ligação (Pré-visualização) satisfaz automaticamente os requisitos de residência de dados na região, incluindo requisitos especificados no [Trust Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="data-residency"></a>Residência dos dados
Em Azure, a funcionalidade que permite armazenar dados de clientes numa única região está atualmente disponível apenas na Região do Sudeste Asiático (Singapura) da Região Geo do Pacífico Asiático e Do Brasil Sul (Estado de São Paulo) do geo brasileiro. Para todas as outras regiões, os dados dos clientes são armazenados na Geo. Para mais informações, consulte o [Trust Center.](https://azuredatacentermap.azurewebsites.net/)

## <a name="next-steps"></a>Próximos passos

* Leia uma visão geral do [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
