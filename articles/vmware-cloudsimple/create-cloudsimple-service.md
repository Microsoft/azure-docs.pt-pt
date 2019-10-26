---
title: Solução do Azure VMware por CloudSimple-criar serviço CloudSimple
description: Descreve como criar o serviço CloudSimple no portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2019
ms.locfileid: "72893950"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Criar a solução do Azure VMware pelo serviço CloudSimple

Para começar a usar a solução Azure VMware por CloudSimple, crie a solução Azure VMware pelo serviço CloudSimple no portal do Azure.

## <a name="before-you-begin"></a>Antes de começar

Aloque um bloco CIDR/28 para a sub-rede de gateway. Uma sub-rede de gateway é necessária por serviço CloudSimple e é exclusiva para a região na qual ela é criada. A sub-rede de gateway é usada para serviços de rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple. As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
    ](media/create-cloudsimple-service-search.png) do serviço CloudSimple Search ![
3. Selecione **Serviços CloudSimples**.
4. Clique em **Adicionar** para criar um novo serviço.
    ![adicionar](media/create-cloudsimple-service-add.png) do serviço CloudSimple
5. Selecione a assinatura na qual você deseja criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede/28 que não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-redes CloudSimple planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)
9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Passos seguintes

* Saiba como [provisionar nós](create-nodes.md)
* Saiba como [criar uma nuvem privada](create-private-cloud.md)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
