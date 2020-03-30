---
title: Solução Azure VMware by CloudSimple - Criar serviço CloudSimple
description: Descreve como criar o serviço CloudSimple no portal Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024828"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Crie a Solução Azure VMware por cloudSimple

Para começar com a Azure VMware Solution by CloudSimple, crie a Solução Azure VMware por CloudSimple no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR /28 para a sub-rede de porta de entrada. Uma subnet gateway é necessária por serviço CloudSimple e é única para a região em que é criada. A sub-rede gateway é utilizada para serviços de rede de bordae requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple. As redes que comunicam com a CloudSimple incluem redes no local e redes virtuais Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Pesquisa por **Serviços CloudSimple**.
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

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [fornecer nódosos](create-nodes.md)
* Saiba como [criar uma nuvem privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
