---
title: Criar a solução da VMware por CloudSimple - serviço do Azure
description: Descreve como criar o serviço de CloudSimple no portal do Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a0ccce6f298270b2751307868fdf85697cb7e8ee
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154961"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Criar a solução de VMware do Azure por CloudSimple - serviço

Para iniciar a solução de VMware do Azure por CloudSimple, crie a solução de VMware Azure pelo serviço CloudSimple no portal do Azure.

> [!NOTE]
> Antes de criar o serviço de CloudSimple, tem de registar o fornecedor de recursos de Microsoft.VMwareCloudSimple na sua subscrição do Azure. Siga os passos em [ativar o fornecedor de recursos na sua subscrição do Azure Microsoft.VMwareCloudSimple](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.

2. Procure **CloudSimple serviços**.

    ![CloudSimple serviço de pesquisa](media/create-cloudsimple-service-search.png)

3. Selecione **CloudSimple serviços**.

4. Clique em **adicionar** para criar um novo serviço.

    ![Adicionar serviço de CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a subscrição em que pretende criar o serviço de CloudSimple.

6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.

7. Introduza o nome para identificar o serviço.

8. Introduza o CIDR para o gateway de serviço. Especificar/28 sub-rede que não se sobreponha com qualquer uma das suas sub-redes existentes.  Estes incluem sub-redes no local, sub-redes do Azure, ou qualquer planeada CloudSimple sub-redes. Não é possível alterar o CIDR depois do serviço é criado.

    ![Criação do serviço de CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="next-steps"></a>Passos Seguintes

* Saiba como [criar uma nuvem privada](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Saiba como [configurar um ambiente de nuvem privada](quickstart-create-private-cloud.md)
