---
title: Expandir soluções Azure VMware (AVS) Private Cloud
description: Descreve como expandir uma Nuvem Privada AVS existente para adicionar capacidade num cluster existente ou novo
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3286b7537056a6c2f282533aa629ebbe47612690
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025304"
---
# <a name="expand-an-avs-private-cloud"></a>Expandir uma Nuvem Privada AVS

A AVS proporciona a flexibilidade para expandir dinamicamente uma Nuvem Privada AVS. Pode começar com uma configuração menor e depois expandir-se à medida que necessita de uma maior capacidade. Ou pode criar uma Nuvem Privada AVS com base nas necessidades atuais e depois expandir-se à medida que o consumo cresce.

Uma Nuvem Privada AVS é composta por um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós. Ao expandir uma Nuvem Privada AVS, adicione nós ao cluster existente ou crie um novo cluster. Para expandir um cluster existente, os nós adicionais devem ser do mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para obter mais informações sobre os limites da Nuvem Privada AVS, consulte a secção de limites no artigo de visão geral da [nuvem privada AVS.](cloudsimple-private-cloud.md)

Uma Nuvem Privada AVS é criada com um **Datacenter** padrão no vCenter. Cada datacenter serve como uma entidade de gestão de alto nível. Para um novo cluster, a AVS oferece a opção de adicionar ao datacenter existente ou criar um novo datacenter.

Como parte da nova configuração do cluster, a AVS configura a infraestrutura VMware. As definições incluem configurações de armazenamento para grupos de discos vSAN, Alta Disponibilidade VMware e Programador de Recursos Distribuídos (DRS).

Uma Nuvem Privada AVS pode ser expandida várias vezes. A expansão só pode ser feita quando se fica dentro dos limites globais do nó. Cada vez que expande uma Nuvem Privada AVS, adiciona-se ao cluster existente ou cria uma nova.

## <a name="before-you-begin"></a>Antes de começar

Os nódosos devem ser provisionados antes de poder expandir a sua Nuvem Privada AVS. Para obter mais informações sobre o fornecimento de nós, consulte [os nós de provisionamento para a Solução VMware por avs - artigo azure.](create-nodes.md) Para criar um novo cluster, você deve ter pelo menos três nós disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-an-avs-private-cloud"></a>Expandir uma Nuvem Privada AVS

1. [Aceda ao portal AVS.](access-cloudsimple-portal.md)

2. Abra a página **Recursos** e selecione a Nuvem Privada AVS para a qual pretende expandir.

3. Na secção resumo, clique em **Expandir**.

    ![Expandir nuvem privada AVS](media/resources-expand-private-cloud.png)

4. Escolha se expande o seu cluster existente ou crie um novo cluster vSphere. À medida que faz alterações, a informação sumária na página é atualizada.

    * Para expandir o seu cluster existente, clique em Expandir o **cluster existente**. Selecione o cluster que pretende expandir e introduza o número de nós para adicionar. Cada aglomerado pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique **em Criar um novo cluster**. Introduza um nome para o cluster. Selecione um datacenter existente ou introduza um nome para criar um novo datacenter. Escolha o tipo de nó. Você pode escolher um tipo de nó diferente ao criar um novo cluster vSphere, mas não quando expandir um cluster vSphere existente. Selecione o número de nós. Cada novo aglomerado deve ter pelo menos três nós.

    ![Expandir AVS Private Cloud - adicione nódosos](media/resources-expand-private-cloud-add-nodes.png)

5. Clique **em Submeter** para expandir a Nuvem Privada AVS.

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [as Nuvens Privadas aVS](cloudsimple-private-cloud.md)