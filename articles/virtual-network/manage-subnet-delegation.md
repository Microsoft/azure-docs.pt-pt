---
title: Adicionar ou remover uma delegação de sub-rede em uma rede virtual do Azure
titlesuffix: Azure Virtual Network
description: Saiba como adicionar ou remover uma sub-rede delegada para um serviço no Azure no Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 9bf44aa5ab18f94ec91650548dc13360ce1b1f3d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938502"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Adicionar ou remover uma delegação de sub-rede

A delegação de sub-rede fornece permissões explícitas ao serviço para criar recursos específicos do serviço na sub-rede usando um identificador exclusivo ao implantar o serviço. Este artigo descreve como adicionar ou remover uma sub-rede delegada para um serviço do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Criar a rede virtual

Nesta seção, você criará uma rede virtual e a sub-rede que será delegada posteriormente a um serviço do Azure.

1. No lado superior esquerdo da tela, selecione **criar um recurso** >  > rede redes**virtuais**.
1. Em **criar rede virtual**, insira ou selecione estas informações:

    | Definição | Valor |
    | ------- | ----- |
    | Name | Insira *MyVirtualNetwork*. |
    | Espaço de endereços | Digite *10.0.0.0/16*. |
    | Subscription | Selecione a sua subscrição.|
    | Resource group | Selecione **criar novo**, insira *MyResource*e, em seguida, selecione **OK**. |
    | Location | Selecione **lesteus**.|
    | Nome da sub-rede | Insira *mysubnet*. |
    | Sub-rede - Intervalo de endereços | Digite *10.0.0.0/24*. |
    |||
1. Deixe o restante como padrão e, em seguida, selecione **criar**.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Delegar uma sub-rede a um serviço do Azure

Nesta seção, você delega a sub-rede que criou na seção anterior para um serviço do Azure.

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **configurações**e, em seguida, selecione **mysubnet**.
4. Na página *mysubnet* , para a lista **delegação de sub-rede** , selecione os serviços listados em **delegar sub-rede para um serviço** (por exemplo, **Microsoft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Remover a delegação de sub-rede de um serviço do Azure

1. Na barra de pesquisa do portal, insira *myVirtualNetwork*. Quando **myVirtualNetwork** aparecer nos resultados da pesquisa, selecione-a.
2. Nos resultados da pesquisa, selecione *myVirtualNetwork*.
3. Selecione **sub-redes**, em **configurações**e, em seguida, selecione **mysubnet**.
4. Na página *mysubnet* , para a lista **delegação de sub-rede** , selecione **nenhum** dos serviços listados em **delegar sub-rede a um serviço**. 

## <a name="next-steps"></a>Passos seguintes
- Saiba como [gerenciar sub-redes no Azure](virtual-network-manage-subnet.md).
