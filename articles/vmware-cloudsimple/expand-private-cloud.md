---
title: Expandir Solução VMware Azure por CloudSimple Private Cloud
description: Descreve como expandir uma Nuvem Privada CloudSimple existente para adicionar capacidade num cluster existente ou novo
author: Ajayan1008
ms.author: v-hborys
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: c469ea3f104e71eb80010d4a5cd421eab6184fcf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895863"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expandir uma nuvem Simple Nuvem

O CloudSimple proporciona a flexibilidade para expandir dinamicamente uma Nuvem Privada. Pode começar com uma configuração menor e depois expandir à medida que necessitar de maior capacidade. Ou pode criar uma Nuvem Privada baseada nas necessidades atuais e depois expandir-se à medida que o consumo cresce.

Uma Nuvem Privada consiste de um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós.  Ao expandir uma Nuvem Privada, adicione nós ao cluster existente ou crie um novo cluster. Para expandir um cluster existente, os nós adicionais devem ser do mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para obter mais informações sobre os limites da Nuvem Privada, consulte a secção limites no artigo [de visão geral da nuvem privada CloudSimple.](cloudsimple-private-cloud.md)

Uma nuvem privada é criada com um **Datacenter** predefinido no vCenter.  Cada datacenter serve como uma entidade de gestão de alto nível.  Para um novo cluster, o CloudSimple fornece a opção de adicionar ao datacenter existente ou criar um novo datacenter.

Como parte da nova configuração do cluster, o CloudSimple configura a infraestrutura VMware.  As definições incluem definições de armazenamento para grupos de discos vSAN, VMware High Availability e Distributed Resource Scheduler (DRS).

Uma Nuvem Privada pode ser expandida várias vezes. A expansão só pode ser feita quando se mantém dentro dos limites globais do nó. Cada vez que expande uma Nuvem Privada, adiciona-se ao cluster existente ou cria-se um novo.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser a provisionados antes de poder expandir a sua Nuvem Privada.  Para obter mais informações sobre os nós de provisionamento, consulte [nós de provisão para Solução VMware por CloudSimple - Artigo Azure.](create-nodes.md)  Para criar um novo cluster, você deve ter pelo menos três nós disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expandir uma Cloud Privada

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos** e selecione a Nuvem Privada para a qual pretende expandir.

3. Na secção resumo, clique **em Expandir**.

    ![Expandir a nuvem privada](media/resources-expand-private-cloud.png)

4. Escolha se expande o seu cluster existente ou crie um novo cluster vSphere. Ao es fazer alterações, as informações resumidas na página são atualizadas.

    * Para expandir o seu cluster existente, clique em **Expandir o cluster existente.** Selecione o cluster que pretende expandir e introduza o número de nós a adicionar. Cada cluster pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique em **Criar novo cluster.** Introduza um nome para o cluster. Selecione um datacenter existente ou introduza um nome para criar um novo datacenter. Escolha o tipo de nó. Você pode escolher um tipo diferente de nó ao criar um novo cluster vSphere, mas não ao expandir um cluster vSphere existente. Selecione o número de nós. Cada novo aglomerado deve ter pelo menos três nós.

    ![Expandir a nuvem privada - adicione os nóns](media/resources-expand-private-cloud-add-nodes.png)

5. Clique **em Enviar** para expandir a nuvem privada.

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)