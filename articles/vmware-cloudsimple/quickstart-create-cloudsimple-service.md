---
title: Soluções Azure VMware (AVS) Quickstart - Criar serviço
description: Saiba como criar o serviço AVS, comprar nós e reservar nós
titleSuffix: Azure VMware Solutions (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e7eb414e51ca38f524ab83bfb51f80f771524287
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024437"
---
# <a name="quickstart---create-azure-vmware-solutions-avs-service"></a>Quickstart - Criar o serviço Azure VMware Solutions (AVS)

Para começar, crie as Soluções Azure VMware (AVS) no portal Azure.

## <a name="vmware-solutions-avs---service-overview"></a>Soluções VMware (AVS) - Visão geral do serviço

O serviço AVS permite-lhe consumir a Solução Azure VMware pela AVS. A criação do serviço permite-lhe fornecer nós, reservar nós e criar Nuvens Privadas AVS. Adicione o serviço AVS em cada região do Azure onde o serviço AVS está disponível. O serviço define a rede de bordas da Azure VMware Solution pela AVS. Esta rede de bordas é usada para serviços que incluem VPN, ExpressRoute e conectividade de Internet com as suas Nuvens Privadas AVS.

Para adicionar o serviço AVS, tem de criar uma sub-rede de gateway. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não pode se sobrepor a nenhum dos espaços de endereço de rede local nem do espaço de endereço de rede virtual do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR/28 para a sub-rede de gateway. Uma subnet gateway é necessária por serviço AVS e é única para a região em que é criada. A sub-rede gateway é utilizada para a Solução Azure VMware pelos serviços de rede de borda AVS e requer um bloco CIDR /28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Não deve sobrepor-se a qualquer rede que comunique com o ambiente AVS. As redes que comunicam com a AVS incluem redes no local e redes virtuais Azure.

Examine os [pré-requisitos de rede](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Pesquisa por **Serviço AVS**.

    ![Pesquisar Serviço AVS](media/create-cloudsimple-service-search.png)

3. Selecione **Serviços AVS**.
4. Clique em **Adicionar** para criar um novo serviço.

    ![Adicionar Serviço AVS](media/create-cloudsimple-service-add.png)

5. Selecione a subscrição onde pretende criar o serviço AVS.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede /28 que não se sobreponha a nenhuma das suas subredes no local, subnets Azure ou subredes AVS planeadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criação do serviço AVS](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="provision-nodes"></a>Nós de aprovisionamento

Para configurar a capacidade de pagamento para um ambiente AVS Private Cloud, os primeiros nós de provisão no portal Azure.

1. Selecione **Todos os serviços**.
2. Pesquisa por **Nódos AVS**.

    ![Pesquisar avs nodes](media/create-cloudsimple-node-search.png)

3. Selecione **Nódosos AVS**.
4. Clique em **Adicionar** para criar nós.

    ![Adicionar nódosos AVS](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição onde pretende fornecer os nódosos AVS.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local dos recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o [tipo de nó](cloudsimple-node.md).
11. Selecione o número de nós a serem provisionados.
12. Selecione **revisão + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes

* [Criar Nuvem Privada AVS e configurar ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre [o serviço AVS](https://docs.azure.cloudsimple.com/cloudsimple-service)
