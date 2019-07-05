---
title: Reduzir a solução de VMware do Azure pela nuvem privada de CloudSimple
description: Descreve como reduzir uma nuvem privada CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e639feb603f1654b4dcd40f16d8e3094307839a
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544520"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Reduzir uma nuvem privada de CloudSimple

CloudSimple fornece a flexibilidade para reduzir dinamicamente uma nuvem privada.  Uma nuvem privada consiste num ou mais clusters vSphere. Cada cluster pode ter 3 a 16 nós. Ao reduzir uma nuvem privada, ao remove um nó do cluster existente ou eliminar um cluster completo. 

## <a name="before-you-begin"></a>Antes de começar

Condições a seguir deve ser cumprida para redução de uma nuvem privada.  Gestão de cluster (primeiro cluster) criou quando foi criada uma nuvem privada não pode ser eliminada.

* Um cluster vSphere tem de ter três nós.  Não é possível encolher um cluster com apenas três nós.
* Armazenamento total consumido não deve exceder a capacidade total após a redução do cluster. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Reduzir uma nuvem privada 

1. [Aceder ao portal de CloudSimple](access-cloudsimple-portal.md).

2. Abra o **recursos** página.

3. Clique na nuvem privada que deseja reduzir

4. Na página Resumo, clique em **diminuir**.

    ![Redução de nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que pretende reduzir ou eliminar. 

    ![Reduzir a nuvem privada - select cluster](media/shrink-private-cloud-select-cluster.png)

6. Selecione **remover um nó** ou **eliminar o cluster inteiro**. 

7. Certifique-se a capacidade de cluster

8. Clique em **submeter** para reduzir a nuvem privada.

Inicia a redução da nuvem privada.  Pode monitorizar o progresso em tarefas.  O processo de redução pode demorar algumas horas, consoante os dados, que tem de ser resynced no vSAN.

## <a name="next-steps"></a>Passos Seguintes

* [Consumir as VMs de VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)