---
title: Delegar uma sub-rede para ficheiros de NetApp do Azure | Documentos da Microsoft
description: Descreve como delegar uma sub-rede para ficheiros de NetApp do Azure.
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
ms.date: 03/25/2019
ms.author: b-juche
ms.openlocfilehash: fd8e380ad68b86b9ffd0f1e40efde8bdadfb19c5
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763307"
---
# <a name="delegate-a-subnet-to-azure-netapp-files"></a>Delegar uma sub-rede para os Azure NetApp Files 

Deve delegar uma sub-rede para ficheiros de NetApp do Azure.   Quando cria um volume, tem de especificar a sub-rede do delegado.

## <a name="considerations"></a>Considerações
* O Assistente para criar uma nova sub-rede é predefinido como um /24 máscara de rede, que fornece para 251 endereços IP disponíveis. Usando/28 máscara de rede, que fornece para 16 endereços IP utilizáveis, é suficiente para o serviço.
* Cada rede Virtual do Azure (Vnet), apenas uma sub-rede pode ser delegada a ficheiros do Azure NetApp.
* Não é possível designar um grupo de segurança de rede ou a sub-rede do delegado de ponto de extremidade de serviço. Isso faz com que a delegação de sub-rede efetuar a ativação.
* Acesso a um volume a partir de uma rede virtual em modo de peering globalmente não é atualmente suportado.
* Criando [rotas personalizadas definidas pelo utilizador](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#custom-routes) na sub-redes VM com o endereço de prefixo (destino) a uma sub-rede de delegado ao serviço ficheiros do Azure NetApp é suportado. Se o fizer, irá afetar a conectividade de VMS.

## <a name="steps"></a>Passos 
1.  Vá para o **redes virtuais** painel do portal do Azure e selecione a rede virtual que pretende utilizar para os ficheiros do Azure NetApp.    

1. Selecione **sub-redes** no painel de rede Virtual e clique a **+ sub-rede** botão. 

1. Crie uma nova sub-rede a utilizar para ficheiros do Azure NetApp, concluindo os seguintes campos obrigatórios na página Adicionar sub-rede:
    * **Nome**: Especifique o nome da sub-rede.
    * **Intervalo de endereços**: Especifique o intervalo de endereços IP.
    * **Delegação de sub-rede**: Selecione **Microsoft.NetApp/volumes**. 

      ![Delegação de sub-rede](../media/azure-netapp-files/azure-netapp-files-subnet-delegation.png)
    
Também pode criar e delegar uma sub-rede quando [criar um volume para os ficheiros do Azure NetApp](azure-netapp-files-create-volumes.md). 

## <a name="next-steps"></a>Passos Seguintes  
* [Criar um volume para o Azure NetApp Files](azure-netapp-files-create-volumes.md)
* [Saiba mais sobre a integração da rede virtual para serviços do Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)


