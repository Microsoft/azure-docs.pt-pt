---
title: Solução Azure VMware by CloudSimple - Criar CloudSimple Private Cloud
description: Descreve como criar uma CloudSimple Private Cloud para estender as cargas de trabalho do VMware à nuvem com flexibilidade operacional e continuidade
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2f4af4a36e719cbf15b3f0af77db81a32f2f2e42
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97896282"
---
# <a name="create-a-cloudsimple-private-cloud"></a>Criar uma nuvem simple nuvem privada

A Private Cloud é uma pilha de VMware isolada que suporta anfitriões ESXi, vCenter, vSAN e NSX. Nuvens Privadas são geridas através do portal CloudSimple. Têm o seu próprio servidor vCenter no seu próprio domínio de gestão. A pilha funciona com nós dedicados e nós de hardware de metal nu isolados.

A criação de uma Nuvem Privada ajuda-o a responder a uma variedade de necessidades comuns para a infraestrutura de rede:

* **Crescimento.** Se alcançou um ponto de atualização de hardware para a sua infraestrutura existente, uma Nuvem Privada permite-lhe expandir sem necessidade de novo investimento em hardware.

* **Expansão rápida.** Se surgirem necessidades temporárias ou não planeadas, uma Nuvem Privada permite-lhe criar a capacidade adicional sem demora.

* **Proteção acrescida**. Com uma Nuvem Privada de três ou mais nós, obtém-se redundância automática e proteção de alta disponibilidade.

* **Necessidades de infraestruturas a longo prazo.** Se os seus centros de dados estiverem na capacidade ou pretender reestruturar-se para reduzir os seus custos, uma Nuvem Privada permite-lhe retirar centros de dados e migrar para uma solução baseada na nuvem, mantendo-se compatível com as suas operações empresariais.

Quando crias uma Nuvem Privada, obtém-se um único cluster vSphere e todos os VMs de gestão que são criados nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nós devem ser a provisionados antes de poder criar a sua Nuvem Privada. Para obter mais informações sobre os nós de a provisionamento, consulte [nós de provisão para Azure VMware Solution by CloudSimple](create-nodes.md).

Aloque uma gama CIDR para sub-redes vSphere/vSAN para a Nuvem Privada. Uma Nuvem Privada é criada como um ambiente isolado de pilha de VMware (com anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter. Os componentes de gestão são implantados na rede selecionada para sub-redes vSphere/vSAN CIDR. A gama CIDR de rede é dividida em diferentes sub-redes durante a implantação. O espaço de endereço da sub-rede vSphere/vSAN deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente CloudSimple. As redes que comunicam com o CloudSimple incluem redes no local e redes virtuais Azure. Para obter mais informações sobre sub-redes vSphere/vSAN, consulte VLANs e sub-redes.

* Mínimo vSphere/vSAN sub-redes CIDR gama prefixo: /24
* Máximo vSphere/vSAN sub-redes CIDR prefixo de gama CIDR: /21


## <a name="access-the-cloudsimple-portal"></a>Aceder ao portal da CloudSimple

Aceda ao [portal CloudSimple](access-cloudsimple-portal.md).

## <a name="create-a-new-private-cloud"></a>Criar uma nova nuvem privada

1. Selecione **Todos os serviços**.
2. Procure por **Serviços CloudSimple**.
3. Selecione o serviço CloudSimple no qual pretende criar a sua Cloud Privada.
4. A partir **do resumo,** clique em **Criar Cloud Privada** para abrir um novo separador de navegador para o portal CloudSimple. Se solicitado, inscreva-se com o seu sinal de Azure em credenciais.

    ![Criar Nuvem Privada a partir de Azure](media/create-private-cloud-from-azure.png)

5. No portal CloudSimple, forneça um nome para a sua Nuvem Privada.
6. Selecione **Localização** para a sua Nuvem Privada.
7. Selecione **o tipo de nó,** consistente com o que provisionou no Azure.
8. Especificar **a contagem de nós**.  Pelo menos três nós são necessários para criar uma Nuvem Privada.

    ![Criar Nuvem Privada - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique **em seguida: Opções avançadas**.
10. Introduza a gama CIDR para sub-redes vSphere/vSAN. Certifique-se de que a gama CIDR não se sobrepõe a nenhuma das suas sub-redes Azure (redes virtuais) ou com a sub-rede gateway.

    **Opções de gama CIDR:** /24, /23, /22, ou /21. Uma gama CIDR /24 suporta até nove nós, uma gama CIDR /23 suporta até 41 nós, e uma gama CIDR /22 e/21 suporta até 64 nós (o número máximo de nós numa Nuvem Privada).

    > [!IMPORTANT]
    > Os endereços IP na gama vSphere/vSAN CIDR são reservados para utilização pela infraestrutura Private Cloud.  Não utilize o endereço IP nesta gama em qualquer máquina virtual.

11. Clique **em seguida: Reveja e crie**.
12. Reveja as definições. Se precisar de alterar quaisquer definições, clique em **Anterior**.
13. Clique em **Criar**.

O processo de provisionamento de nuvens privadas começa. Pode levar até duas horas para que a Nuvem Privada seja a provisionada.

Para obter instruções sobre a expansão de uma nuvem privada existente, consulte [Expandir uma Nuvem Privada](expand-private-cloud.md).
