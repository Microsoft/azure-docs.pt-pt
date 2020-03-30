---
title: Expanda a solução Azure VMware pela CloudSimple Private Cloud
description: Descreve como expandir uma CloudSimple Private Cloud existente para adicionar capacidade num cluster existente ou novo
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a82ba1b433e62ed1c4b72b8e942d4ade29f26c4a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77025304"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expandir uma nuvem privada CloudSimple

A CloudSimple proporciona a flexibilidade para expandir dinamicamente uma Nuvem Privada. Pode começar com uma configuração menor e depois expandir-se à medida que necessita de uma maior capacidade. Ou pode criar uma Nuvem Privada com base nas necessidades atuais e depois expandir-se à medida que o consumo cresce.

Uma Nuvem Privada consiste em um ou mais aglomerados de vSphere. Cada aglomerado pode ter 3 a 16 nós.  Ao expandir uma Nuvem Privada, adicione nós ao cluster existente ou crie um novo cluster. Para expandir um cluster existente, os nós adicionais devem ser do mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para mais informações sobre os limites da Cloud Privada, consulte a secção de limites no artigo de visão geral da [cloudSimple.](cloudsimple-private-cloud.md)

Uma nuvem privada é criada com um **Datacenter** padrão no vCenter.  Cada datacenter serve como uma entidade de gestão de alto nível.  Para um novo cluster, a CloudSimple oferece a opção de adicionar ao datacenter existente ou criar um novo datacenter.

Como parte da nova configuração do cluster, a CloudSimple configura a infraestrutura VMware.  As definições incluem configurações de armazenamento para grupos de discos vSAN, Alta Disponibilidade VMware e Programador de Recursos Distribuídos (DRS).

Uma Nuvem Privada pode ser expandida várias vezes. A expansão só pode ser feita quando se fica dentro dos limites globais do nó. Cada vez que expande uma Nuvem Privada, adicione ao cluster existente ou crie uma nova.

## <a name="before-you-begin"></a>Antes de começar

Os nódosos devem ser provisionados antes de poder expandir a sua Nuvem Privada.  Para obter mais informações sobre o fornecimento de nós, consulte [os nós de Provision para A Solução VMware pela CloudSimple - artigo azure.](create-nodes.md)  Para criar um novo cluster, você deve ter pelo menos três nós disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="expand-a-private-cloud"></a>Expandir uma Cloud Privada

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos** e selecione a Cloud Privada para a qual pretende expandir.

3. Na secção resumo, clique em **Expandir**.

    ![Expandir nuvem privada](media/resources-expand-private-cloud.png)

4. Escolha se expande o seu cluster existente ou crie um novo cluster vSphere. À medida que faz alterações, a informação sumária na página é atualizada.

    * Para expandir o seu cluster existente, clique em Expandir o **cluster existente**. Selecione o cluster que pretende expandir e introduza o número de nós para adicionar. Cada aglomerado pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique **em Criar um novo cluster**. Introduza um nome para o cluster. Selecione um datacenter existente ou introduza um nome para criar um novo datacenter. Escolha o tipo de nó. Você pode escolher um tipo de nó diferente ao criar um novo cluster vSphere, mas não quando expandir um cluster vSphere existente. Selecione o número de nós. Cada novo aglomerado deve ter pelo menos três nós.

    ![Expandir nuvem privada - adicione nódosos](media/resources-expand-private-cloud-add-nodes.png)

5. Clique em **Submeter** para expandir a nuvem privada.

## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)