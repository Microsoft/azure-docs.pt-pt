---
title: Use etiquetas de serviço
titleSuffix: Azure SignalR Service
description: Utilize etiquetas de serviço para permitir o tráfego de saída para o seu Serviço Azure SignalR
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152324"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Utilize etiquetas de serviço para o Serviço Azure SignalR

Pode utilizar [tags de serviço](../virtual-network/network-security-groups-overview.md#service-tags) para o serviço Azure SignalR ao configurar o Grupo de Segurança da [Rede](../virtual-network/network-security-groups-overview.md#network-security-groups). Permite-lhe definir a regra de segurança da rede de saída para os pontos finais do Serviço Azure SignalR sem necessidade de codificar endereços IP.

O Serviço Azure SignalR gere estas etiquetas de serviço. Não é possível criar a sua própria etiqueta de serviço ou modificar uma existente. A Microsoft gere estes prefixos de endereço que correspondem à etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

## <a name="use-service-tag-on-portal"></a>Use a etiqueta de serviço no portal

Pode permitir o tráfego de saída para o Serviço Azure SignalR adicionando uma nova regra de segurança da rede de saída:

1. Vai ao grupo de segurança da rede.

1. Clique no menu de definições chamado **Regras de segurança outbound**.

1. Clique no botão **+ Adicione** na parte superior.

1. Escolha a **Etiqueta de Serviço** em **Destino.**

1. Escolha **AzureSignalR** na **etiqueta de serviço Destino**.

1. Preencha **443** nas **gamas portuárias destination.**

    ![Criar uma regra de segurança de saída](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Ajuste outros campos de acordo com as suas necessidades.

1. Clique em **Adicionar**.


## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança da rede: tags de serviço](../virtual-network/network-security-groups-overview.md#security-rules)