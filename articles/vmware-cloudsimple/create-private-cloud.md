---
title: Criar uma solução do Azure VMware pela nuvem privada CloudSimple
description: Descreve como criar uma nuvem privada do CloudSimple para estender cargas de trabalho do VMware para a nuvem com flexibilidade operacional e continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/10/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 02a2bd311ea1e89a49eb12ef57a167a08eea5f98
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68812247"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma nuvem privada do CloudSimple

A criação de uma nuvem privada ajuda a lidar com uma variedade de necessidades comuns de infraestrutura de rede:

* **Crescimento**. Se você atingiu um ponto de atualização de hardware para sua infraestrutura existente, uma nuvem privada permite que você se expanda sem nenhum novo investimento de hardware necessário.

* **Expansão rápida**. Se alguma necessidade de capacidade temporária ou não planejada surgir, uma nuvem privada permitirá que você crie a capacidade adicional sem atraso.

* **Maior proteção**. Com uma nuvem privada de três ou mais nós, você obtém redundância automática e proteção de alta disponibilidade.

* **Necessidades de infraestrutura de longo prazo**. Se os data centers estiverem na capacidade ou se você quiser reestruturar para reduzir os custos, uma nuvem privada permitirá que você desative os datacenters e migre para uma solução baseada em nuvem e, ao mesmo tempo, seja compatível com suas operações corporativas.

Ao criar uma nuvem privada, você obtém um único cluster vSphere e todas as VMs de gerenciamento criadas nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser provisionados antes que você possa criar sua nuvem privada.  Para obter mais informações sobre os nós de provisionamento, consulte o artigo provisionar [nós para solução VMware por CloudSimple-Azure](create-nodes.md) .

Aloque um intervalo CIDR para sub-redes vSphere/vSAN para a nuvem privada. Uma nuvem privada é criada como um ambiente isolado do VMware Stack (hosts ESXi, vCenter, vSAN e NSX) gerenciado por um servidor vCenter. Os componentes de gerenciamento são implantados na rede selecionada para o CIDR de sub-redes vSphere/vSAN. O intervalo de CIDR de rede é dividido em sub-redes diferentes durante a implantação.  O espaço de endereço de sub-rede vSphere/vSAN deve ser exclusivo. Ele não deve se sobrepor a nenhuma rede que se comunica com o ambiente CloudSimple.  As redes que se comunicam com o CloudSimple incluem redes locais e redes virtuais do Azure.  Para obter mais informações sobre sub-redes vSphere/vSAN, consulte [visão geral de VLANs e sub-redes](cloudsimple-vlans-subnets.md).

* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN mínima:/24 
* Prefixo de intervalo CIDR de sub-redes vSphere/vSAN máxima:/21

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nova nuvem privada

1. Na página **recursos** , clique em **Nova nuvem privada**.

    ![Criar uma nuvem privada-como iniciar](media/create-pc-button.png)

2. Selecione o local para hospedar os recursos de nuvem privada.

3. Escolha o tipo de nó CS28 ou CS36 you'ev provisionado para a nuvem privada. A última opção inclui a capacidade máxima de computação e memória.

4. Selecione o número de nós para a nuvem privada. Você pode selecionar no máximo o número disponível de nós que o you'ev [provisionado](create-nodes.md).

    ![Criar uma nuvem privada-configurações básicas](media/create-private-cloud-basic-info.png)

5. Clique **em Avançar: Opções**avançadas.

6. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.  Não use nenhum intervalo CIDR definido em redes virtuais do Azure.
    
    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Um intervalo CIDR/24 dá suporte a até nove nós, um intervalo CIDR/23 dá suporte a até 41 nós, e um intervalo CIDR/22 e/21 dá suporte a até 64 nós (o número máximo de nós em uma nuvem privada).

    > [!CAUTION]
    > Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada.  Não use o endereço IP nesse intervalo em qualquer máquina virtual.

7. Clique **em Avançar: Revise e**crie.

8. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.

9. Clique em **Criar**.

O provisionamento de nuvem particular será iniciado depois que você clicar em criar.  Você pode monitorar o progresso da página [tarefas](https://docs.azure.cloudsimple.com/activity/#tasks) no portal do CloudSimple.  O provisionamento pode levar de 30 minutos a duas horas.  Você receberá um email quando o provisionamento for concluído.

## <a name="next-steps"></a>Passos Seguintes

* [Expandir nuvem privada](expand-private-cloud.md)