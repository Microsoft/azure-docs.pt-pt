---
title: Azure VMware Solutions (AVS) - Criar serviço AVS
description: Descreve como criar o serviço AVS no portal Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024828"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Criar o serviço Azure VMware Solutions (AVS)

Para começar com a Azure VMware Solutions (AVS), crie o serviço Azure VMware Solutions (AVS) no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR /28 para a sub-rede de porta de entrada. Uma subnet gateway é necessária por serviço AVS e é única para a região em que é criada. A sub-rede gateway é utilizada para serviços de rede de bordae requer um bloco CIDR /28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Não deve sobrepor-se a qualquer rede que comunique com o ambiente AVS. As redes que comunicam com a AVS incluem redes no local e redes virtuais Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Pesquisa de **Serviços AVS**.
    ![pesquisa de](media/create-cloudsimple-service-search.png) de serviço saque
3. Selecione **Serviços AVS**.
4. Clique em **Adicionar** para criar um novo serviço.
    ![adicionar](media/create-cloudsimple-service-add.png) de serviço AVS
5. Selecione a subscrição onde pretende criar o serviço AVS.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede /28 que não se sobreponha a nenhuma das suas subredes no local, subnets Azure ou subredes AVS planeadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criação do serviço AVS](media/create-cloudsimple-service.png)
9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [fornecer nódosos](create-nodes.md)
* Saiba como [criar uma Nuvem Privada AVS](create-private-cloud.md)
* Saiba como [configurar um ambiente de Nuvem Privada AVS](quickstart-create-private-cloud.md)
