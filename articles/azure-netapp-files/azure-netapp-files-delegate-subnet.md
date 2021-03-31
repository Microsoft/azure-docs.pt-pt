---
title: Delege uma sub-rede para Azure NetApp Files | Microsoft Docs
description: Saiba como delegar uma sub-rede para ficheiros Azure NetApp. Especifique a sub-rede delegada quando criar um volume.
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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: bb3d1fd49c2623ff6dcbe8a19ae8c8ca3b46425a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006581"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede para os Azure NetApp Files 

Tem de delegar uma sub-rede para ficheiros Azure NetApp.   Quando cria um volume, tem de especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações

* O assistente para criar uma nova sub-rede predefini a uma máscara de rede /24, que fornece 251 endereços IP disponíveis. A utilização de uma máscara de rede /28, que prevê 11 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada Rede Virtual Azure (VNet), apenas uma sub-rede pode ser delegada em Ficheiros Azure NetApp.   
   O Azure permite-lhe criar várias sub-redes delegadas num VNet.  No entanto, qualquer tentativa de criar um novo volume falhará se utilizar mais de uma sub-rede delegada.  
   Só pode ter uma única sub-rede delegada num VNet. Uma conta NetApp pode implantar volumes em vários VNets, cada um com a sua própria sub-rede delegada.  
* Não é possível designar um grupo de segurança de rede ou ponto final de serviço na sub-rede delegada. Ao fazê-lo, a delegação da sub-rede falha.
* O acesso a um volume a partir de uma rede virtual globalmente espreitada não é suportado atualmente.
* [As rotas definidas pelo utilizador](../virtual-network/virtual-networks-udr-overview.md#custom-routes) (UDRs) e os grupos de segurança da rede (NSGs) não são suportados em sub-redes delegadas para ficheiros Azure NetApp. No entanto, pode aplicar UDRs e NSGs a outras sub-redes, mesmo dentro do mesmo VNet que a sub-rede delegada nos Ficheiros Azure NetApp.  
   O Azure NetApp Files cria uma rota de sistema para a sub-rede delegada. A rota é mostrada em **rotas eficazes** na tabela de rotas se precisar dela para a resolução de problemas.

## <a name="steps"></a>Passos

1.  Vá à lâmina de **redes Virtuais** no portal Azure e selecione a rede virtual que pretende utilizar para ficheiros Azure NetApp.    

1. Selecione **sub-redes** a partir da lâmina de rede Virtual e clique no botão **+Subnet.** 

1. Criar uma nova sub-rede para utilizar para ficheiros Azure NetApp, completando os seguintes campos necessários na página Add Subnet:
    * **Nome**: Especificar o nome da sub-rede.
    * **Intervalo de endereços**: Especifique o intervalo de endereços IP.
    * **Delegação de sub-redes**: Selecione **Microsoft.NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também pode criar e delegar uma sub-rede quando [criar um volume para ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passos seguintes

* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Conheça a integração de redes virtuais para serviços Azure](../virtual-network/virtual-network-for-azure-services.md)