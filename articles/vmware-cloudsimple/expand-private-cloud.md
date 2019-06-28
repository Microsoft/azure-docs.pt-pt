---
title: Expanda a solução de VMware do Azure pela nuvem privada de CloudSimple
description: Descreve como expandir uma Cloud privada CloudSimple existente para adicionar capacidade num cluster novo ou existente
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/06/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 293a09c57ca95e2774e44ff4bc9f9f2c31be2f49
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332613"
---
# <a name="expand-a-cloudsimple-private-cloud"></a>Expanda uma nuvem privada de CloudSimple

CloudSimple fornece a flexibilidade para expandir dinamicamente de uma nuvem privada. Pode começar com uma configuração de menor e, em seguida, expandir à medida que necessitar de maior capacidade. Ou pode criar uma nuvem privada com base nas necessidades do atuais e, em seguida, expandir à medida que aumenta de consumo.

Uma nuvem privada consiste num ou mais clusters vSphere. Cada cluster pode ter 3 a 16 nós.  Ao expandir uma nuvem privada, adicionar nós ao cluster existente ou criar um novo cluster. Para expandir um cluster existente, nós adicionais tem de ser o mesmo tipo (SKU) que os nós existentes. Para criar um novo cluster, os nós podem ser de um tipo diferente. Para obter mais informações sobre os limites de nuvem privada, consulte limita a secção [descrição geral de nuvem privada CloudSimple](cloudsimple-private-cloud.md) artigo.

Uma nuvem privada é criada com uma predefinição **Datacenter** no vCenter.  Cada datacenter serve como uma entidade de nível superior de gestão.  Para um novo cluster, CloudSimple fornece a opção de adicionar ao centro de dados existente ou criar um novo datacenter.

Como parte da configuração do cluster novo, CloudSimple configura a infraestrutura de VMware.  As definições incluem definições de armazenamento para grupos de disco de vSAN, o VMware elevada disponibilidade e o Distributed Resource Scheduler (DRS).

Uma nuvem privada pode ser expandida várias vezes. Expansão pode ser feita apenas quando se manter dentro dos limites de nó geral. Sempre que expandir uma nuvem privada que adiciona ao cluster existente ou criar um novo.

## <a name="before-you-begin"></a>Antes de começar

Nós tem de ser aprovisionados antes de poder expandir sua nuvem privada.  Para obter mais informações sobre o aprovisionamento de nós, consulte [aprovisionar nós para solução de VMware ao CloudSimple - Azure](create-nodes.md) artigo.  Para criar um novo cluster, tem de ter, pelo menos, três nós disponíveis do mesmo SKU.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="expand-a-private-cloud"></a>Expanda uma nuvem privada

1. [Aceder ao portal de CloudSimple](access-cloudsimple-portal.md).

2. Abra o **recursos** página e selecione a nuvem privada para o qual pretende expandir.

3. Na secção de resumo, clique em **expandir**.

    ![Expanda a nuvem privada](media/resources-expand-private-cloud.png)

4. Escolha se pretende expandir o seu cluster existente ou criar um novo cluster vSphere. Como efetuar alterações, as informações de resumo na página são atualizadas.

    * Para expandir o seu cluster existente, clique em **expanda o cluster existente**. Selecione o cluster que pretende expandir e introduza o número de nós a adicionar. Cada cluster pode ter um máximo de 16 nós.
    * Para adicionar um novo cluster, clique em **criar um novo cluster**. Introduza um nome para o cluster. Selecione um datacenter existente ou introduza um nome para criar um novo datacenter. Escolha o tipo de nó. Pode escolher um tipo de nó diferente quando criar um novo cluster vSphere, mas não quando expandir um cluster existente do vSphere. Selecione o número de nós. Cada novo cluster tem de ter, pelo menos, três nós.

    ![Expanda a nuvem privada – adicionar nós](media/resources-expand-private-cloud-add-nodes.png)

5. Clique em **submeter** para expandir a nuvem privada.

## <a name="next-steps"></a>Passos Seguintes

* [Consumir as VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)