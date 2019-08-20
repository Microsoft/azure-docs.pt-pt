---
title: Solução do Azure VMware por CloudSimple início rápido – criar serviço
description: Saiba como criar o serviço CloudSimple, nós de compra e nós de reserva
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b20ff261939dd97a74d27f5ec7f21eae2665f474
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/19/2019
ms.locfileid: "69574519"
---
# <a name="quickstart---create-cloudsimple-service"></a>Início rápido-criar serviço CloudSimple

Para começar, crie a solução Azure VMware de CloudSimple no portal do Azure.

O serviço CloudSimple permite que você consuma a solução do Azure VMware pelo CloudSimple.  A criação do serviço permite que você compre nós, Reserve nós e crie nuvens privadas.  Você adiciona o serviço CloudSimple em cada região do Azure onde o serviço CloudSimple está disponível.  O serviço define a rede de borda da solução do Azure VMware por CloudSimple.  Essa rede de borda é usada para serviços que incluem conectividade VPN, ExpressRoute e Internet para suas nuvens privadas.

Para adicionar o serviço CloudSimple, você deve criar uma sub-rede de gateway. A sub-rede de gateway é usada ao criar a rede de borda e requer um bloco CIDR/28. O espaço de endereço de sub-rede de gateway deve ser exclusivo. Ele não pode se sobrepor a nenhum dos espaços de endereço de rede local nem do espaço de endereço de rede virtual do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Habilitar o provedor de recursos Microsoft. VMwareCloudSimple

Siga as etapas abaixo para habilitar o provedor de recursos para o serviço CloudSimple.

1. Selecione **Todos os serviços**.
2. Procure e selecione **assinaturas**.

    ![Selecionar subscrições](media/cloudsimple-service-select-subscriptions.png)

3. Selecione a assinatura na qual você deseja habilitar o serviço CloudSimple.
4. Clique em **provedores de recursos** para a assinatura.
5. Use **Microsoft. VMwareCloudSimple** para filtrar o provedor de recursos.
6. Selecione o provedor de recursos **Microsoft. VMwareCloudSimple** e clique em **registrar**.

    ![Registar fornecedor de recursos](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Criar o serviço

1. Selecione **Todos os serviços**.
2. Procure o **serviço CloudSimple**.

    ![Pesquisar serviço CloudSimple](media/create-cloudsimple-service-search.png)

3. Selecione **Serviços CloudSimples**.
4. Clique em **Adicionar** para criar um novo serviço.

    ![Adicionar serviço CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a assinatura na qual você deseja criar o serviço CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o nome para identificar o serviço.
8. Insira o CIDR para o gateway de serviço. Especifique uma sub-rede/28 que não se sobreponha a nenhuma de suas sub-redes locais, sub-redes do Azure ou sub-redes CloudSimple planejadas. Você não pode alterar o CIDR depois que o serviço é criado.

    ![Criando o serviço CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="purchase-nodes"></a>Comprar nós

Para configurar a capacidade paga conforme o uso para um ambiente de nuvem privada do CloudSimple, primeiro provisionar nós na portal do Azure.

1. Selecione **Todos os serviços**.
2. Procure **nós CloudSimple**.

    ![Pesquisar nós do CloudSimple](media/create-cloudsimple-node-search.png)

3. Selecione **nós CloudSimple**.
4. Clique em **Adicionar** para criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a assinatura em que você deseja comprar nós CloudSimple.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Insira o prefixo para identificar os nós.
8. Selecione o local dos recursos do nó.
9. Selecione o local dedicado para hospedar os recursos do nó.
10. Selecione o tipo de nó. Você pode escolher a [opção CS28 ou CS36](cloudsimple-node.md). A última opção inclui a capacidade máxima de computação e memória.
11. Selecione o número de nós a serem provisionados.
12. Selecione **revisão + criar**.
13. Examine as configurações. Para modificar as configurações, clique em **anterior**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos Seguintes

* [Criar nuvem privada e configurar o ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre o [serviço CloudSimple](cloudsimple-service.md)
