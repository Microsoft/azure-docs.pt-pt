---
title: Reduzir a solução VMware do Azure da nuvem privada CloudSimple
description: Descreve como reduzir uma nuvem privada CloudSimple.
author: sharaths-cs
ms.author: b-shsury
ms.date: 07/01/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 00d0067fe51efef8efffe31860e16f4530adf3ac
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69972374"
---
# <a name="shrink-a-cloudsimple-private-cloud"></a>Reduzir uma nuvem privada CloudSimple

O CloudSimple fornece a flexibilidade para reduzir dinamicamente uma nuvem privada.  Uma nuvem privada consiste em um ou mais clusters vSphere. Cada cluster pode ter de 3 a 16 nós. Ao reduzir uma nuvem privada, você remove um nó do cluster existente ou exclui um cluster inteiro. 

## <a name="before-you-begin"></a>Antes de começar

As condições a seguir devem ser atendidas para a redução de uma nuvem privada.  O cluster de gerenciamento (primeiro cluster) criado quando uma nuvem privada foi criada não pode ser excluído.

* Um cluster vSphere deve ter três nós.  Um cluster com apenas três nós não pode ser reduzido.
* O armazenamento total consumido não deve exceder a capacidade total após a redução do cluster. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="shrink-a-private-cloud"></a>Reduzir uma nuvem privada

1. [Acesse o portal do CloudSimple](access-cloudsimple-portal.md).

2. Abra a página **recursos** .

3. Clique na nuvem privada que você deseja reduzir

4. Na página Resumo, clique em **reduzir**.

    ![Reduzir nuvem privada](media/shrink-private-cloud.png)

5. Selecione o cluster que você deseja reduzir ou excluir. 

    ![Reduzir nuvem privada-selecionar cluster](media/shrink-private-cloud-select-cluster.png)

6. Selecione **remover um nó** ou **excluir o cluster inteiro**. 

7. Verificar a capacidade do cluster

8. Clique em **Enviar** para reduzir a nuvem privada.

A redução da nuvem privada é iniciada.  Você pode monitorar o progresso em tarefas.  O processo de redução pode levar algumas horas, dependendo dos dados, que precisam ser ressincronizados no vSAN.

## <a name="next-steps"></a>Passos Seguintes

* [Consumir VMs VMware no Azure](quickstart-create-vmware-virtual-machine.md)
* Saiba mais sobre [nuvens privadas](cloudsimple-private-cloud.md)
