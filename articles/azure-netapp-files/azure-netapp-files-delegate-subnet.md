---
title: Delege uma sub-rede para Azure NetApp Files Microsoft Docs
description: Descreve como delegar uma sub-rede para ficheiros Azure NetApp.
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
ms.date: 05/04/2020
ms.author: b-juche
ms.openlocfilehash: 713a72b0a406d2038d56dc6fcc41e169d02c54eb
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/27/2020
ms.locfileid: "85483623"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede para os Azure NetApp Files 

Tem de delegar uma sub-rede para ficheiros Azure NetApp.   Quando cria um volume, tem de especificar a sub-rede delegada.

## <a name="considerations"></a>Considerações
* O assistente para criar uma nova sub-rede predefini a uma máscara de rede /24, que fornece 251 endereços IP disponíveis. A utilização de uma máscara de rede /28, que prevê 16 endereços IP utilizáveis, é suficiente para o serviço.
* Em cada Rede Virtual Azure (VNet), apenas uma sub-rede pode ser delegada em Ficheiros Azure NetApp.   
   O Azure permite-lhe criar várias sub-redes delegadas num VNet.  No entanto, qualquer tentativa de criar um novo volume falhará se utilizar mais de uma sub-rede delegada.  
   Só pode ter uma única sub-rede delegada num VNet. Uma conta NetApp pode implantar volumes em vários VNets, cada um com a sua própria sub-rede delegada.  
* Não é possível designar um grupo de segurança de rede ou ponto final de serviço na sub-rede delegada. Ao fazê-lo, a delegação da sub-rede falha.
* O acesso a um volume a partir de uma rede virtual globalmente espreitada não é suportado atualmente.
* A criação [de rotas personalizadas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) nas sub-redes VM com prefixo de endereço (destino) para uma sub-rede delegada no Azure NetApp Files não é suportada. Ao fazê-lo, terá impacto na conectividade VM.

## <a name="steps"></a>Passos 
1.  Vá à lâmina de **redes Virtuais** a partir do portal Azure e selecione a rede virtual que pretende utilizar para ficheiros Azure NetApp.    

1. Selecione **sub-redes** a partir da lâmina de rede Virtual e clique no botão **+Subnet.** 

1. Criar uma nova sub-rede para utilizar para ficheiros Azure NetApp, completando os seguintes campos necessários na página Add Subnet:
    * **Nome**: Especificar o nome da sub-rede.
    * **Intervalo de endereços**: Especifique o intervalo de endereços IP.
    * **Delegação de sub-redes**: Selecione **Microsoft.NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também pode criar e delegar uma sub-rede quando [criar um volume para ficheiros Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passos seguintes  
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Conheça a integração de redes virtuais para serviços Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


