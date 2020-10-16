---
title: Solução Azure VMware by CloudSimple - Criar serviço CloudSimple
description: Saiba como criar o serviço CloudSimple no portal Azure. Reveja a configuração necessária antes de começar.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2a196e717dd6e02570e4bdf830a24ce342b47ece
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88140552"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Crie a Solução Azure VMware by CloudSimple

Para começar com a Azure VMware Solution by CloudSimple, crie o serviço Azure VMware Solution by CloudSimple no portal Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR /28 para a sub-rede gateway. Uma sub-rede de gateway é necessária por serviço CloudSimple e é única na região em que é criada. A sub-rede gateway é utilizada para serviços de rede de borda e requer um bloco CIDR /28. O espaço de endereço da sub-rede gateway deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple. As redes que comunicam com o CloudSimple incluem redes no local e redes virtuais Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
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

## <a name="next-steps"></a>Passos seguintes

* Saiba como [providenciar os nóns](create-nodes.md)
* Saiba como [criar uma nuvem privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
