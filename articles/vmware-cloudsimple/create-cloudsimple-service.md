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
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173512"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Criar a solução do Azure VMware pelo serviço CloudSimple

Para começar a usar a solução Azure VMware por CloudSimple, crie a solução Azure VMware pelo serviço CloudSimple no portal do Azure.

> [!IMPORTANT]
> Antes de criar o serviço CloudSimple, você deve registrar o provedor de recursos Microsoft. VMwareCloudSimple em sua assinatura do Azure. Siga as etapas em [habilitar o provedor de recursos Microsoft. VMwareCloudSimple em sua assinatura do Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
    ![Search CloudSimple serviço @ no__t-1
3. Selecione **Serviços CloudSimples**.
4. Clique em **Adicionar** para criar um novo serviço.
    ![Add CloudSimple serviço @ no__t-1
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
