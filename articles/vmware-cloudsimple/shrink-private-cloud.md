---
title: Solução De VMware Shrink Azure pela CloudSimple Private Cloud
description: Descreve como encolher uma Nuvem Privada CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 602dca105e91c55c591388a833a36e71f951da8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77014271"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Encolher uma nuvem privada cloudsimple

A CloudSimple proporciona a flexibilidade para encolher dinamicamente uma Nuvem Privada.  Uma Nuvem Privada consiste em um ou mais aglomerados de vSphere. Cada aglomerado pode ter 3 a 16 nós. Ao encolher uma Nuvem Privada, remova um nó do cluster existente ou elimine todo um cluster. 

## <a name="before-you-begin"></a>Antes de começar

As seguintes condições devem ser satisfeitas para encolher uma Nuvem Privada.  O cluster de gestão (primeiro cluster) criado quando uma Nuvem Privada foi criada não pode ser eliminado.

* Um aglomerado de vSphere deve ter três nós.  Um aglomerado com apenas três nós não pode ser encolhido.
* O armazenamento total consumido não deve exceder a capacidade total após a redução do cluster.
* Verifique se as regras do Programador de Recursos Distribuídos (DRS) impedem a vMoção de uma máquina virtual.  Se as regras estiverem presentes, desative ou elimine as regras.  As regras de DRS incluem máquina virtual para acolher regras de afinidade.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inscreva-se no portal [https://portal.azure.com](https://portal.azure.com)Azure em .

## <a name="shrink-a-private-cloud"></a>Encolher uma Cloud Privada

1. [Aceda ao portal CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos.**

3. Clique na Nuvem Privada que quer encolher

4. Na página de resumo, clique **em Encolher**.

    ![Encolher nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que pretende encolher ou apagar. 

    ![Encolher nuvem privada - selecione cluster](media/shrink-private-cloud-select-cluster.png)

6. **Selecione Remover um nó** ou apagar todo o **cluster**. 

7. Verifique a capacidade do cluster

8. Clique **em Submeter** para encolher a Nuvem Privada.

O psiquiatra da Nuvem Privada começa.  Pode monitorizar o progresso das tarefas.  O processo de redução pode demorar algumas horas dependendo dos dados, que precisam de ser resincronizados na vSAN.

> [!NOTE]
> 1. Se encolher uma nuvem privada eliminando o último ou único cluster no datacenter, o datacenter não será eliminado.
> 2. Se ocorrer alguma violação da regra DRS, o nó não será removido do cluster e a descrição da tarefa mostra que remover um nó violará as regras de DRS no cluster.    


## <a name="next-steps"></a>Passos seguintes

* [Consumir VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
