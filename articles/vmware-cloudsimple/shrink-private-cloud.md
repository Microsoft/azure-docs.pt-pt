---
title: Shrink Azure VMware Solution by CloudSimple Private Cloud
description: Descreve como encolher uma Nuvem Privada CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77014271"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Encolher uma nuvem Simple Nuvem

O CloudSimple proporciona a flexibilidade para encolher dinamicamente uma Nuvem Privada.  Uma Nuvem Privada consiste de um ou mais aglomerados vSphere. Cada aglomerado pode ter 3 a 16 nós. Ao encolher uma Nuvem Privada, retire um nó do cluster existente ou elimine um aglomerado inteiro. 

## <a name="before-you-begin"></a>Before you begin

As condições devem ser satisfeitas para encolher uma Nuvem Privada.  O cluster de gestão (primeiro cluster) criado quando uma Nuvem Privada foi criada não pode ser eliminado.

* Um aglomerado vSphere deve ter três nós.  Um aglomerado com apenas três nós não pode ser encolhido.
* O armazenamento total consumido não deve exceder a capacidade total após a redução do cluster.
* Verifique se as regras do Programador de Recursos Distribuídos (DRS) impedem a vMotion de uma máquina virtual.  Se houver regras, desative ou elimine as regras.  As regras de DRS incluem máquina virtual para acolher regras de afinidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Encolher uma Cloud Privada

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **Recursos.**

3. Clique na Nuvem Privada que pretende encolher

4. Na página do resumo, clique **em Shrink**.

    ![Encolher nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que pretende encolher ou apagar. 

    ![Shrink private cloud - selecione cluster](media/shrink-private-cloud-select-cluster.png)

6. **Selecione Remover um nó** ou eliminar todo o **cluster**. 

7. Verifique a capacidade do cluster

8. Clique **em Submeter** para encolher a Nuvem Privada.

O psiquiatra da Nuvem Privada começa.  Pode monitorizar o progresso nas tarefas.  O processo de redução pode demorar algumas horas dependendo dos dados, que precisam de ser ressíndidos em vSAN.

> [!NOTE]
> 1. Se encolher uma nuvem privada eliminando o último ou o único cluster no datacenter, o datacenter não será eliminado.
> 2. Se ocorrer qualquer violação da regra drs, o nó não será removido do cluster e a descrição da tarefa mostra que remover um nó violará as regras de DRS no cluster.    


## <a name="next-steps"></a>Próximos passos

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [Nuvens Privadas](cloudsimple-private-cloud.md)
