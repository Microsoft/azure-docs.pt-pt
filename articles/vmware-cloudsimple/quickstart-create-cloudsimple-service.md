---
title: 'Quickstart: Criar serviço VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Saiba como criar o serviço CloudSimple, comprar nómadas e reservar nosdes
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81868023"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Quickstart - Criar Solução VMware Azure por serviço CloudSimple

Para começar, crie a Solução Azure VMware by CloudSimple no portal Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solução VMware by CloudSimple - Visão geral do serviço

O serviço CloudSimple permite-lhe consumir Azure VMware Solution by CloudSimple.  A criação do serviço permite-lhe a provisionar nós, reservar nós e criar nuvens privadas.  Você adiciona o serviço CloudSimple em cada região de Azure onde o serviço CloudSimple está disponível.  O serviço define a rede de bordas da Azure VMware Solution by CloudSimple.  Esta rede de bordas é usada para serviços que incluem VPN, ExpressRoute e conectividade internet às suas nuvens privadas.

Para adicionar o serviço CloudSimple, tem de criar uma sub-rede gateway. A sub-rede gateway é utilizada ao criar a rede de arestas e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Não pode sobrepor-se a nenhum dos seus espaços de endereço de rede no local ou ao espaço de endereço de rede virtual Azure.

## <a name="before-you-begin"></a>Before you begin

Aloque um bloco CIDR /28 para a sub-rede gateway.  Uma sub-rede de gateway é necessária por serviço CloudSimple e é única na região em que é criada. A sub-rede gateway é utilizada para a Solução VMware Azure pelos serviços de rede de ponta CloudSimple e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple.  As redes que comunicam com o CloudSimple incluem redes no local e redes virtuais Azure.

Rever [pré-requisitos de rede.](cloudsimple-network-checklist.md) 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure o **Serviço CloudSimple**.

    ![Procurar Serviço CloudSimple](media/create-cloudsimple-service-search.png)

3. Selecione **Serviços CloudSimple**.
4. Clique **em Adicionar** para criar um novo serviço.

    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a subscrição onde pretende criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **Criar Novo**.
7. Insira o nome para identificar o serviço.
8. Introduza o CIDR para o gateway de serviço. Especifique uma sub-rede /28 que não se sobreponha a nenhuma das suas sub-redes no local, sub-redes Azure ou sub-redes CloudSimple planeadas. Não é possível alterar o CIDR após a criação do serviço.

    ![Criar o serviço CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="provision-nodes"></a>Nós de aprovisionamento

Para configurar a capacidade pay-as-you-go para um ambiente CloudSimple Private Cloud, primeiro provisiona nós no portal Azure.

1. Selecione **Todos os serviços**.
2. Procure por **Nóns CloudSimple**.

    ![Pesquisar CloudSimple Nosdes](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple Nós**.
4. Clique **em Adicionar** para criar nós.

    ![Adicionar Nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição onde pretender a provisionar os nós CloudSimple.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **Criar Novo**.
7. Introduza o prefixo para identificar os nós.
8. Selecione a localização para os recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o [tipo de nó](cloudsimple-node.md).
11. Selecione o número de nós para provisão.
12. Selecione **Review + Criar**.
13. Reveja as definições. Para modificar quaisquer definições, clique em **Anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Próximos passos

* [Criar nuvem privada e configurar ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre [o serviço CloudSimple](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service)
