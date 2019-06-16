---
title: Solução de VMware do Azure por CloudSimple guia de introdução - criar serviço
description: Aprenda a criar o serviço de CloudSimple, nós de compra e reserva de nós
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9b3b95db24f4b0f9a0cf8f5102dfeea5dc51e29f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64577574"
---
# <a name="quickstart---create-service"></a>Início rápido - criar serviço

Para começar, crie a solução de VMware do Azure por CloudSimple no portal do Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Solução de VMware ao CloudSimple - descrição geral do serviço

O serviço de CloudSimple permite-lhe consumir a solução de VMware do Azure por CloudSimple.  Criação do serviço permite-lhe adquirir nós, reserva de nós e criar nuvens privadas.  Adicione o serviço de CloudSimple em cada região do Azure onde o serviço de CloudSimple está disponível.  O serviço define a rede de borda da solução de VMware do Azure por CloudSimple.  Esta rede de borda é utilizada para serviços que incluem a conectividade VPN, ExpressRoute e de Internet para nuvens privadas.

Para adicionar o serviço de CloudSimple, tem de criar uma sub-rede de gateway. A sub-rede do gateway é utilizada quando criar a rede de borda e necessita de/28 bloco CIDR. O espaço de endereços da sub-rede de gateway tem de ser exclusivo. Não pode sobrepor a nenhum dos seus espaços de endereços de rede no local ou o espaço de endereços de rede virtual do Azure.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Ativar fornecedor de recursos de Microsoft.VMwareCloudSimple

Siga os passos abaixo para ativar o fornecedor de recursos para o serviço de CloudSimple.

1. Selecione **Todos os serviços**.
2. Procure e selecione **subscrições**.

    ![Selecionar subscrições](media/cloudsimple-service-select-subscriptions.png)

3. Selecione a subscrição na qual pretende ativar o serviço CloudSimple
4. Clique em **fornecedores de recursos** para a subscrição
5. Uso **Microsoft.VMwareCloudSimple** para filtrar o fornecedor de recursos
6. Selecione o **Microsoft.VMwareCloudSimple** fornecedor de recursos e clique em **registar**

    ![Registar o fornecedor de recursos](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Criar o serviço

>[!NOTE]
> Serviço de CloudSimple tem de estar ativado na sua subscrição. Se a sua subscrição não ativada, receberá um erro ao tentar criar o serviço.  Siga os passos em [CloudSimple ativar o serviço](https://docs.azure.cloudsimple.com/enable-cloudsimple-service) artigo para ativar o serviço.

1. Selecione **Todos os serviços**.
2. Procure **CloudSimple serviço**.

    ![CloudSimple serviço de pesquisa](media/create-cloudsimple-service-search.png)

3. Selecione **CloudSimple serviços**.
4. Clique em **adicionar** para criar um novo serviço.

    ![Adicionar serviço de CloudSimple](media/create-cloudsimple-service-add.png)

5. Selecione a subscrição em que pretende criar o serviço de CloudSimple.
6. Selecione o grupo de recursos para o serviço. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Introduza o nome para identificar o serviço.
8. Introduza o CIDR para o gateway de serviço. Especificar/28 sub-rede que não se sobrepõe a qualquer uma das suas sub-redes no local, sub-redes do Azure ou planeadas CloudSimple sub-redes. Não é possível alterar o CIDR depois do serviço é criado.

    ![Criação do serviço de CloudSimple](media/create-cloudsimple-service.png)

9. Clique em **OK**.

O serviço é criado e adicionado à lista de serviços.

## <a name="purchase-nodes"></a>Comprar nós

Para configurar a opção pay as you aceda capacidade para um ambiente de nuvem privada CloudSimple, primeiro aprovisionar nós no portal do Azure.

1. Selecione **Todos os serviços**.
2. Procure **CloudSimple nós**.

    ![Procurar CloudSimple nós](media/create-cloudsimple-node-search.png)

3. Selecione **CloudSimple nós**.
4. Clique em **adicionar** criar nós.

    ![Adicionar nós CloudSimple](media/create-cloudsimple-node-add.png)

5. Selecione a subscrição em que deseja comprar CloudSimple nós.
6. Selecione o grupo de recursos para os nós. Para adicionar um novo grupo de recursos, clique em **criar novo**.
7. Introduza o prefixo para identificar os nós.
8. Selecione a localização para os recursos de nó.
9. Selecione a localização dedicada para alojar os recursos de nó.
10. Selecione o tipo de nó. Pode escolher o [opção CS28 ou CS36](cloudsimple-node.md). A última opção inclui a capacidade de computação e memória máxima.
11. Selecione o número de nós para aprovisionar.
12. Selecione **rever + criar**.
13. Reveja as definições. Para alterar as definições, clique em **Previous**.
14. Selecione **Criar**.

## <a name="next-steps"></a>Passos Seguintes

* [Criar nuvem privada e configurar o ambiente](quickstart-create-private-cloud.md)
* Saiba mais sobre [CloudSimple serviço](https://docs.azure.cloudsimple.com/cloudsimple-service)
