---
title: Solução Azure VMware by CloudSimple - Criar cloudSimple Private Cloud
description: Descreve como criar uma CloudSimple Private Cloud para estender as cargas de trabalho vMware à nuvem com flexibilidade operacional e continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f700ac34b6c6e2a651366bee7dd1785c608064f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024794"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma nuvem privada CloudSimple

Uma Nuvem Privada é uma pilha de VMware isolada que suporta anfitriões ESXi, vCenter, vSAN e NSX. As Nuvens Privadas são geridas através do portal CloudSimple. Eles têm o seu próprio servidor vCenter no seu próprio domínio de gestão. A pilha funciona em nódos os desdedicados e nos nódosos de hardware de metal isolados.

Criar uma Nuvem Privada ajuda-o a responder a uma variedade de necessidades comuns para a infraestrutura de rede:

* **Crescimento.** Se chegou a um ponto de atualização de hardware para a sua infraestrutura existente, uma Cloud Privada permite-lhe expandir sem o necessário novo investimento de hardware.

* **Expansão rápida.** Se surgir alguma capacidade temporária ou não planeada, uma Nuvem Privada permite-lhe criar a capacidade adicional sem demora.

* **Maior proteção**. Com uma Nuvem Privada de três ou mais nós, obtém-se redundância automática e alta proteção de disponibilidade.

* **Necessidades de infraestruturas a longo prazo.** Se os seus datacenters estiverem na capacidade ou quiser reestruturar para baixar os seus custos, uma Nuvem Privada permite-lhe retirar centros de dados e migrar para uma solução baseada na nuvem, mantendo-se compatível com as suas operações empresariais.

Quando se cria uma Nuvem Privada, obtém-se um único cluster vSphere e todos os VMs de gestão que são criados nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nódosos devem ser provisionados antes de poder criar a sua Nuvem Privada. Para obter mais informações sobre os nós de provisionamento, consulte os nós de provisionamento para a [Solução Azure VMware pela CloudSimple](create-nodes.md).

Aloque uma gama CIDR para subredes vSphere/vSAN para a Nuvem Privada. Uma Nuvem Privada é criada como um ambiente de pilha de VMware isolado (com anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter. Os componentes de gestão são implantados na rede selecionada para as subnets cidr vSphere/vSAN. A gama CIDR da rede é dividida em diferentes subredes durante a implantação. O espaço de endereço de sub-rede vSphere/vSAN deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple. As redes que comunicam com a CloudSimple incluem redes no local e redes virtuais Azure. Para obter mais informações sobre as subredes vSphere/vSAN, consulte vlaNs e subnets.

* Prefixo mínimo de sub-prefixo de sub-prefixo de vSphere/vSAN cidr: /24
* Prefixo máximo de sub-nets da vSphere/vSAN CIDR: /21


## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nova nuvem privada

1. Selecione **Todos os serviços**.
2. Pesquisa por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir de **uma visão geral,** clique **em Criar Cloud Privada** para abrir um novo separador de navegador para o portal CloudSimple. Se solicitado, inscreva-se com o seu sinal Azure em credenciais.

    ![Criar nuvem privada de Azure](media/create-private-cloud-from-azure.png)

5. No portal CloudSimple, forneça um nome para a sua Nuvem Privada.
6. Selecione **Localização** para a sua Nuvem Privada.
7. Selecione **tipo nó,** consistente com o que forprovisionou no Azure.
8. Especificar **contagem do nó**.  Pelo menos três nódosos são necessários para criar uma Nuvem Privada.

    ![Criar Nuvem Privada - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique **em Seguir: Opções avançadas**.
10. Introduza a gama CIDR para as subredes vSphere/vSAN. Certifique-se de que a gama CIDR não se sobrepõe a nenhuma das suas subredes Azure (redes virtuais) ou com a sub-rede gateway.

    **Opções de gama CIDR:** /24, /23, /22, ou /21. Uma gama DE /24 CIDR suporta até nove nós, uma gama DE ICDR /23 suporta até 41 nós, e uma gama de /22 e /21 CIDR suporta até 64 nós (o número máximo de nós numa Nuvem Privada).

    > [!IMPORTANT]
    > Os endereços IP na gama icdr vSphere/vSAN são reservados para utilização pela infraestrutura Cloud Privada.  Não utilize o endereço IP nesta gama em nenhuma máquina virtual.

11. Clique em **Seguir: Rever e criar**.
12. Reveja as definições. Se precisar de alterar quaisquer definições, clique em **Anterior**.
13. Clique em **Criar**.

O processo de provisionamento da Nuvem Privada começa. Pode levar até duas horas para a Nuvem Privada ser aprovisionada.

Para obter instruções sobre a expansão de uma nuvem privada existente, consulte [Expandir uma Nuvem Privada](expand-private-cloud.md).
