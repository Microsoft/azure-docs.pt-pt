---
title: Delege uma subnet para ficheiros Azure NetApp [ Microsoft Docs
description: Descreve como delegar uma subnet a Ficheiros Azure NetApp.
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
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: b-juche
ms.openlocfilehash: b83f530549ffa43789963fd0c95b4982f5289356
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054472"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede para os Azure NetApp Files 

Deve delegar uma subnet a Ficheiros Azure NetApp.   Quando cria um volume, precisa especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações
* O assistente para criar uma nova sub-rede falha numa máscara de rede /24, que fornece 251 endereços IP disponíveis. A utilização de uma máscara de rede /28, que fornece 16 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada Rede Virtual Azure (VNet), apenas uma subnet pode ser delegada em Ficheiros Azure NetApp.   
   O Azure permite-lhe criar várias subredes delegadas num VNet.  No entanto, qualquer tentativa de criar um novo volume falhará se utilizar mais do que uma sub-rede delegada.
* Não é possível designar um grupo de segurança de rede ou ponto final de serviço na sub-rede delegada. Fazê-lo faz com que a delegação da sub-rede falhe.
* O acesso a um volume de uma rede virtual globalmente peered não é atualmente suportado.
* A criação de [rotas personalizadas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) em subnets VM com prefixo de endereço (destino) para uma subnetdelegada nos Ficheiros Azure NetApp não é suportada. Ao fazê-lo, a conectividade VM terá impacto.

## <a name="steps"></a>Passos 
1.  Vá à lâmina de **redes Virtuais** a partir do portal Azure e selecione a rede virtual que pretende utilizar para ficheiros Azure NetApp.    

1. Selecione **Subnets** da lâmina de rede Virtual e clique no botão **+Subnet.** 

1. Crie uma nova subnet para utilizar para ficheiros Azure NetApp, completando os seguintes campos necessários na página Add Subnet:
    * **Nome**: Especificar o nome da sub-rede.
    * **Intervalo de endereços:** Especifique o intervalo de endereçoIP.
    * **Delegação subnet**: Selecione **Microsoft.NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também pode criar e delegar uma subnet quando [criar um volume para Ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passos seguintes  
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


