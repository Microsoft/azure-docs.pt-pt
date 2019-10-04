---
title: Solução do Azure VMware por CloudSimple-criar nuvem privada do CloudSimple
description: Descreve como criar uma nuvem privada do CloudSimple para estender cargas de trabalho do VMware para a nuvem com flexibilidade operacional e continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a6e3d466321fcd8f32f46359c97f67400a8f86c6
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828165"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma Cloud Privada da CloudSimple

Uma nuvem privada é uma pilha VMware isolada que dá suporte a hosts ESXi, vCenter, vSAN e NSX. As nuvens privadas são gerenciadas por meio do portal do CloudSimple. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

A criação de uma nuvem privada ajuda a lidar com uma variedade de necessidades comuns de infraestrutura de rede:

* **Crescimento**. Se você atingiu um ponto de atualização de hardware para sua infraestrutura existente, uma nuvem privada permite que você se expanda sem nenhum novo investimento de hardware necessário.

* **Expansão rápida**. Se alguma necessidade de capacidade temporária ou não planejada surgir, uma nuvem privada permitirá que você crie a capacidade adicional sem atraso.

* **Maior proteção**. Com uma nuvem privada de três ou mais nós, você obtém redundância automática e proteção de alta disponibilidade.

* **Necessidades de infraestrutura de longo prazo**. Se os data centers estiverem na capacidade ou se você quiser reestruturar para reduzir os custos, uma nuvem privada permitirá que você desative os datacenters e migre para uma solução baseada em nuvem e, ao mesmo tempo, seja compatível com suas operações corporativas.

Ao criar uma nuvem privada, você obtém um único cluster vSphere e todas as VMs de gerenciamento criadas nesse cluster.

## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Acesse o [portal do CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nova nuvem privada

1. Selecione **Todos os serviços**.
2. Procure **Serviços CloudSimples**.
3. Selecione o serviço CloudSimple no qual você deseja criar sua nuvem privada.
4. Em **visão geral**, clique em **criar nuvem privada** para abrir uma nova guia do navegador para o portal do CloudSimple. Se solicitado, entre com suas credenciais de entrada do Azure.

    ![Criar nuvem privada do Azure](media/create-private-cloud-from-azure.png)

5. No portal do CloudSimple, forneça um nome para sua nuvem privada.
6. Selecione o **local** para sua nuvem privada.
7. Selecione o **tipo de nó**, consistente com o que você comprou no Azure.
8. Especifique a **contagem de nós**.  Pelo menos três nós são necessários para criar uma nuvem privada.

    ![Criar nuvem privada-informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Seguinte: Opções**avançadas.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Um intervalo CIDR/24 dá suporte a até nove nós, um intervalo CIDR/23 dá suporte a até 41 nós, e um intervalo CIDR/22 e/21 dá suporte a até 64 nós (o número máximo de nós em uma nuvem privada).

    > [!IMPORTANT]
    > Os endereços IP no intervalo CIDR vSphere/vSAN são reservados para uso pela infraestrutura de nuvem privada.  Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Seguinte: Revise e**crie.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

O processo de provisionamento de nuvem particular é iniciado. Pode levar até duas horas para que a nuvem privada seja provisionada.

Para obter instruções sobre como expandir uma nuvem privada existente, consulte [expandir uma nuvem privada](expand-private-cloud.md).
