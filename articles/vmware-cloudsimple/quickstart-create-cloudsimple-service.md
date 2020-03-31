---
title: 'Quickstart: Criar o serviço VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como criar o serviço CloudSimple, comprar nós e reservar nós
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024437"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Quickstart - Create Azure VMware Solution by CloudSimple

Para começar, crie a Solução Azure VMware by CloudSimple no portal Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solução VMware por CloudSimple - Visão geral do serviço

O serviço CloudSimple permite-lhe consumir a Solução Azure VMware pela CloudSimple.  A criação do serviço permite-lhe fornecer nós, reservar nós e criar nuvens privadas.  Adicione o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível.  O serviço define a rede de bordas da Solução Azure VMware pela CloudSimple.  Esta rede de bordas é usada para serviços que incluem VPN, ExpressRoute e conectividade da Internet com as suas nuvens privadas.

Para adicionar o serviço CloudSimple, tem de criar uma sub-rede de gateway. A sub-rede gateway é utilizada ao criar a rede de bordas e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Não pode sobrepor-se a nenhum dos espaços de endereços da rede no local ou ao espaço de endereços da rede virtual Azure.

## <a name="before-you-begin"></a>Antes de começar

Alocar um bloco CIDR /28 para a sub-rede gateway.  Uma subnet gateway é necessária por serviço CloudSimple e é única para a região em que é criada. A sub-rede gateway é utilizada para a Solução Azure VMware pelos serviços de rede de borda CloudSimple e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple.  As redes que comunicam com a CloudSimple incluem redes no local e redes virtuais Azure.

Rever [os pré-requisitos de networking](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Pesquisar por **CloudSimple Service**.

    ![Pesquisar Serviço CloudSimple](media/create-cloudsimple-service-search.png)

3. Selecione **Serviços CloudSimple**.
4. Clique em **Adicionar** para criar um novo serviço.

    ![Adicionar Serviço CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a subscrição onde pretende criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **Criar Novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o portal de serviço. Especifique uma sub-rede /28 que não se sobreponha a nenhuma das suas subredes no local, subnets Azure ou subredes CloudSimple planeadas. Não pode alterar o CIDR depois de o serviço ser criado.

    ![Criação do serviço CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="provision-nodes"></a>Nós de aprovisionamento

Para configurar a capacidade de pagamento para um ambiente CloudSimple Private Cloud, os primeiros nós de fornecimento no portal Azure.

1. Selecione **Todos os serviços**.
2. Procure por **Nodes CloudSimple**.

    ![Pesquisar narizes simples cloudsimple](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple Nodes**.
4. Clique em **Adicionar** para criar nódosos.

    ![Adicione nódosos CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição onde pretende fornecer os nódosos CloudSimple.
6. Selecione o grupo de recursos para os nódosos. Para adicionar um novo grupo de recursos, clique em **Criar Novo**.
7. Introduza o prefixo para identificar os nódosos.
8. Selecione a localização para os recursos do nó.
9. Selecione a localização dedicada para hospedar os recursos do nó.
10. Selecione o tipo de [nó](cloudsimple-node.md).
11. Selecione o número de nós para a provisão.
12. Selecione **Rever + Criar**.
13. Reveja as definições. Para modificar quaisquer definições, clique em **Anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos seguintes

* [Criar nuvem privada e configurar o ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre [o serviço CloudSimple](https://docs.azure.cloudsimple.com/cloudsimple-service)
