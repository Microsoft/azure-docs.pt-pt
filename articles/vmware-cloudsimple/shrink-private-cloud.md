---
title: Shrink Azure VMware Solutions (AVS) Private Cloud
description: Descreve como encolher uma Nuvem Privada AVS.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0ea764081cd0b4d5c6d44cd7364d1e9a89a3cec3
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014271"
---
# <a name="shrink-an-avs-private-cloud"></a>Encolher uma Nuvem Privada AVS

A AVS proporciona a flexibilidade para encolher dinamicamente uma Nuvem Privada AVS. Uma Nuvem Privada AVS é composta por um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós. Ao encolher uma Nuvem Privada AVS, remova um nó do cluster existente ou elimine todo um cluster. 

## <a name="before-you-begin"></a>Antes de começar

As seguintes condições devem ser satisfeitas antes de encolher uma Nuvem Privada AVS. O cluster de Gestão (primeiro cluster) é criado quando a Nuvem Privada AVS foi criada. Não pode ser apagado.

* Um aglomerado de vSphere deve ter três nós. Um aglomerado com apenas três nós não pode ser encolhido.
* O armazenamento total consumido não deve exceder a capacidade total após a redução do cluster.
* Verifique se as regras do Programador de Recursos Distribuídos (DRS) impedem a vMoção de uma máquina virtual. Se as regras estiverem presentes, desative ou elimine as regras. As regras de DRS incluem máquina virtual para acolher regras de afinidade.


## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrinking-an-avs-private-cloud"></a>Encolher uma nuvem privada AVS

1. [Aceda ao portal AVS.](access-cloudsimple-portal.md)

2. Abra a página **recursos.**

3. Clique na Nuvem Privada AVS que pretende encolher

4. Na página de resumo, clique **em Encolher**.

    ![Encolher a Nuvem Privada AVS](media/shrink-private-cloud.png)

5. Selecione o cluster que pretende encolher ou apagar. 

    ![Encolher a Nuvem Privada AVS - selecione cluster](media/shrink-private-cloud-select-cluster.png)

6. **Selecione Remover um nó** ou apagar todo o **cluster**. 

7. Verifique a capacidade do cluster

8. Clique **em Submeter** para encolher a Nuvem Privada AVS.

Começa a encolher a Nuvem Privada da AVS. Pode monitorizar o progresso das tarefas. O processo de redução pode demorar algumas horas dependendo dos dados, que precisam de ser resincronizados na vSAN.

> [!NOTE]
> 1. Se encolher uma nuvem privada eliminando o último ou único cluster no datacenter, o datacenter não será eliminado.
> 2. Se ocorrer alguma violação da regra DRS, o nó não será removido do cluster e a descrição da tarefa mostra que remover um nó violará as regras de DRS no cluster.    


## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [as Nuvens Privadas aVS](cloudsimple-private-cloud.md)
