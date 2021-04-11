---
title: Redimensione o pool de capacidade ou um volume para os ficheiros Azure NetApp | Microsoft Docs
description: Aprenda a mudar o tamanho de uma piscina de capacidade ou de um volume. A redimensionamento da capacidade muda a capacidade adquirida dos Ficheiros Azure NetApp.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104594483"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Redimensionar um conjunto de capacidade ou um volume
Pode alterar o tamanho de uma piscina de capacidade ou de um volume, se necessário. 

## <a name="resize-the-capacity-pool"></a>Redimensionar a piscina de capacidade 

Pode alterar o tamanho da piscina em incrementos ou decréscimos de 1-TiB. No entanto, o tamanho da piscina de capacidade não pode ser inferior a 4 TiB. A redimensionamento da capacidade muda a capacidade adquirida dos Ficheiros Azure NetApp.

1. A partir da lâmina 'Gerir a Conta NetApp', clique na capacidade que pretende redimensionar. 
2. Clique com o botão direito no nome do pool de capacidade ou clique no "..." ícone no final da linha da piscina de capacidade para exibir o menu de contexto. 
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o pool de capacidade.

## <a name="resize-a-volume"></a>Redimensionar um volume

Pode alterar o tamanho de um volume se necessário. O consumo de capacidade de um volume é contabilizado para a capacidade aprovisionada do seu conjunto.

1. A partir da lâmina de conta Manage NetApp, clique em **Volumes**. 
2. Clique com o botão direito no nome do volume que pretende redimensionar ou clicar no "..." ícone no final da linha do volume para exibir o menu de contexto.
3. Utilize as opções do menu de contexto para redimensionar ou eliminar o volume.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Redimensionar um volume de destino de replicação transversal 

Numa relação [de replicação entre regiões,](cross-region-replication-introduction.md) um volume de destino é automaticamente redimensionado com base no tamanho do volume de origem. Como tal, não precisa de redimensionar o volume de destino separadamente. Este comportamento de redimensionamento automático é aplicável quando os volumes estão numa relação de replicação ativa, ou quando o espreitamento de replicação é quebrado com a [operação de ressínc .](cross-region-replication-manage-disaster-recovery.md#resync-replication) 

A tabela seguinte descreve o comportamento de redimensionamento do volume de destino com base no [estado do Mirror:](cross-region-replication-display-health-status.md)

|  Estado do espelho  | Comportamento de redimensionamento do volume de destino |
|-|-|
| *Espelhada* | Quando o volume de destino foi inicializado e está pronto para receber atualizações espelhadas, o redimensionamento do volume de origem redimensiona automaticamente os volumes de destino. |
| *Quebrado* | Quando redimensionar o volume de origem e o estado do Espelho estiver *quebrado,* o volume de destino é automaticamente redimensionado com a [operação de resync](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Não inicializada* | Quando redimensionar o volume de origem e o estado do Espelho ainda *não estiver ininicializado,* o redimensionamento do volume de destino tem de ser feito manualmente. Como tal, recomenda-se que aguarde que a inicialização esteja concluída (isto é, quando o estado do Espelho fica *espelhado*) para redimensionar o volume de origem. | 

> [!IMPORTANT]
> Certifique-se de que tem espaço suficiente nas piscinas de capacidade tanto para a fonte como para os volumes de destino da replicação entre regiões. Quando redimensiona o volume de origem, o volume de destino é automaticamente redimensionado. Mas se a capacidade de alojamento do volume de destino não tiver espaço suficiente, o redimensionamento da fonte e dos volumes de destino falhará.

## <a name="next-steps"></a>Passos seguintes

- [Configurar um conjunto de capacidade](azure-netapp-files-set-up-capacity-pool.md)
- [Gerir um conjunto de capacidade QoS manual](manage-manual-qos-capacity-pool.md)
- [Alterar o nível de serviço de um volume de forma dinâmica](dynamic-change-volume-service-level.md) 