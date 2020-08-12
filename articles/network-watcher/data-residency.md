---
title: Residência de dados para Azure Network Watcher Microsoft Docs
description: Compreender a residência de dados para o serviço Network Watcher
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
ms.openlocfilehash: dff5519c1b36a0a7738cb2529c2f4460ecf61e48
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/11/2020
ms.locfileid: "88117840"
---
# <a name="data-residency-for-azure-network-watcher"></a>Residência de dados para Azure Network Watcher
O Azure Network Watcher não armazena os dados do cliente exceto o serviço Connection Monitor (Preview).


## <a name="connection-monitor-preview-data-residency"></a>Residência de dados do Monitor de Ligação (pré-visualização)
O serviço *Connection Monitor (pré-visualização)* armazena os dados do cliente. Estes dados são automaticamente armazenados pelo Network Watcher numa única região. Assim, *o Monitor de Ligação (pré-visualização)* satisfaz automaticamente os requisitos de residência de dados na região, incluindo conforme especificado no [Trust Center](https://azuredatacentermap.azurewebsites.net/).

## <a name="singapore-data-residency"></a>Residência de dados de Singapura

Em Azure, a funcionalidade para permitir o armazenamento de dados de clientes numa única região está atualmente apenas disponível na Região do Sudeste Asiático (Singapura) da Asia Pacific Geo. Para todas as outras regiões, os dados dos clientes são armazenados na Geo. Para mais informações, consulte [o Trust Center.](https://azuredatacentermap.azurewebsites.net/)

> [!Note]
> **Replicação prévia** Os clientes têm a opção de armazenar endereços IP de utilizador final com a sua instância de Observador de Rede para que o Observador de Rede possa monitorizar alterações de alcance, latência e topologia de rede relacionadas com os endereços IP do utilizador final. Estes endereços IP do utilizador final podem ser classificados como Dados do Cliente. A partir de 15 de julho de 2020, o Network Watcher armazenou estes dados numa única região. (Os dados do cliente já não estão a ser replicados em HK.) Estes dados já não estão a ser replicados em HK. Este Dado do Cliente é e foi encriptado em repouso.
> 
> Se estes Dados do Cliente fossem acessíveis por terceiros, isso permitiria que terceiros soubessem o endereço IP, mas não permitiriam ao terceiro o acesso à máquina ou dispositivo associado ao endereço IP. O Network Watcher acredita que nenhum terceiro acedeu a estes Dados do Cliente em HK.

## <a name="next-steps"></a>Passos seguintes

* Leia uma visão geral do [Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).
