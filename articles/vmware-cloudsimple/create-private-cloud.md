---
title: Criar a solução de VMware do Azure pela nuvem privada de CloudSimple
description: Descreve como criar uma nuvem privada CloudSimple para ampliar cargas de trabalho do VMware para a cloud com flexibilidade operacional e a continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e5c03c1d8a865b792ce79e3e2b576a629b71e02c
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332610"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma nuvem privada de CloudSimple

Criando uma nuvem privada ajuda-o a uma variedade de necessidades comuns para a infraestrutura de rede de endereços:

* **Crescimento**. Se atingir um ponto de atualização de hardware para a sua infraestrutura existente, uma nuvem privada permite-lhe expandir sem investimento de hardware novo necessário.

* **Rápida expansão**. Se surgem necessidades de qualquer capacidade temporária ou não planeada, uma nuvem privada permite-lhe criar a capacidade adicional sem atrasos.

* **Maior proteção**. Com uma nuvem privada de três ou mais nós, tem de redundância automática e proteção de elevada disponibilidade.

* **Precisa de infraestrutura de longo prazo**. Se seus data Centers estão atingiu o limite ou é desejável reestruturar para reduzir os custos, uma nuvem privada permite-lhe extinguir a centros de dados e migrar para uma solução baseada na cloud enquanto permanecem compatível com suas operações de enterprise.

Ao criar uma nuvem privada, obtém um cluster único vSphere e todos os das VMs de gestão que são criados desse cluster.

## <a name="before-you-begin"></a>Antes de começar

Nós tem de ser aprovisionados antes de poder criar sua nuvem privada.  Para obter mais informações sobre o aprovisionamento de nós, consulte [aprovisionar nós para solução de VMware ao CloudSimple - Azure](create-nodes.md) artigo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Acesso a [CloudSimple portal](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nuvem privada novo

1. Sobre o **recursos** página, clique em **nova nuvem privada**.

    ![Criar uma nuvem privada - como começar](media/create-pc-button.png)

2. Selecione a localização para alojar os recursos de nuvem privada.

3. Escolha o you'ev de tipo de nó CS28 ou CS36 aprovisionado para a nuvem privada. A última opção inclui a capacidade de computação e memória máxima.

4. Selecione o número de nós para a nuvem privada. Pode selecionar até o número disponível de nós que you'ev [aprovisionado](create-nodes.md).

    ![Criar uma nuvem privada - definições básicas](media/create-private-cloud-basic-info.png)

5. Clique em **seguinte: Opções Avançadas**.

6. Introduza o intervalo CIDR para sub-redes de vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobrepõem com qualquer uma das suas instalações ou outras sub-redes do Azure (redes virtuais) ou com a sub-rede do gateway.  Não utilize qualquer intervalo CIDR definido em redes virtuais do Azure.
    
    **Opções de intervalo CIDR:** /24, /23, /22 ou /21. R/24 intervalo CIDR suporta até nove nós, uma /23 intervalo CIDR suporta um máximo de 41 nós e um /22 e /21 intervalo CIDR suporta até 64 nós (o número máximo de nós numa nuvem privada).

    > [!CAUTION]
    > Endereços IP no intervalo CIDR do vSphere/vSAN está reservado para utilização pela infraestrutura de nuvem privada.  Não utilize o endereço IP neste intervalo em qualquer máquina virtual.

7. Clique em **seguinte: Reveja e criar**.

8. Reveja as definições. Se precisar de alterar quaisquer definições, clique em **Previous**.

9. Clique em **Criar**.

Nuvem privada, o aprovisionamento será iniciado depois de clicar em criar.  Pode monitorizar o progresso da [tarefas](https://docs.azure.cloudsimple.com/activity/#tasks) página no portal de CloudSimple.  O aprovisionamento pode demorar 30 minutos a duas horas.  Receberá um e-mail assim que o aprovisionamento estiver concluído.

## <a name="next-steps"></a>Passos Seguintes

* [Expanda a nuvem privada](expand-private-cloud.md)