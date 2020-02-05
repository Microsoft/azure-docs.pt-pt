---
title: Soluções Azure VMware (AVS) - Criar nuvem privada AVS
description: Descreve como criar uma Nuvem Privada AVS para estender as cargas de trabalho vMware à nuvem com flexibilidade operacional e continuidade
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 128a0a1eec03878d0deba93048c54324aab7d888
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024794"
---
# <a name="create-an-avs-private-cloud"></a>Criar uma Nuvem Privada AVS

Uma Nuvem Privada AVS é uma pilha de VMware isolada que suporta anfitriões ESXi, vCenter, vSAN e NSX. As Nuvens Privadas AVS são geridas através do portal AVS. Eles têm seu próprio servidor vCenter em seu próprio domínio de gerenciamento. A pilha é executada em nós dedicados e nós de hardware bare-metal isolados.

A criação de uma Nuvem Privada AVS ajuda-o a responder a uma variedade de necessidades comuns para a infraestrutura de rede:

* **Crescimento.** Se chegou a um ponto de atualização de hardware para a sua infraestrutura existente, uma Nuvem Privada AVS permite-lhe expandir sem o novo investimento de hardware necessário.

* **Expansão rápida.** Se surgir alguma capacidade temporária ou não planeada, uma Nuvem Privada AVS permite-lhe criar a capacidade adicional sem demora.

* **Maior proteção**. Com uma Nuvem Privada AVS de três ou mais nós, obtém-se redundância automática e alta proteção de disponibilidade.

* **Necessidades de infraestruturas a longo prazo.** Se os seus datacenters estiverem na capacidade ou quiser reestruturar para baixar os seus custos, uma Nuvem Privada AVS permite-lhe retirar centros de dados e migrar para uma solução baseada na nuvem, mantendo-se compatível com as suas operações empresariais.

Quando crias uma Nuvem Privada AVS, obtém-se um único cluster vSphere e todos os VMs de gestão que são criados nesse cluster.

## <a name="before-you-begin"></a>Antes de começar

Os nódosos devem ser provisionados antes de poder criar a sua Nuvem Privada AVS. Para obter mais informações sobre os nós de provisionamento, consulte [os nós de provisionamento para soluções De VMware Azure (AVS)](create-nodes.md).

Aloque uma gama CIDR para subredes vSphere/vSAN para a Nuvem Privada AVS. Uma Nuvem Privada AVS é criada como um ambiente de pilha de VMware isolado (com anfitriões ESXi, vCenter, vSAN e NSX) gerido por um servidor vCenter. Os componentes de gestão são implantados na rede selecionada para as subnets cidr vSphere/vSAN. O intervalo de CIDR de rede é dividido em sub-redes diferentes durante a implantação. O espaço de endereço de sub-rede vSphere/vSAN deve ser único. Não deve sobrepor-se a qualquer rede que comunique com o ambiente AVS. As redes que comunicam com a AVS incluem redes no local e redes virtuais Azure. Para obter mais informações sobre as subredes vSphere/vSAN, consulte vlaNs e subnets.

* Prefixo mínimo de sub-prefixo de sub-prefixo de vSphere/vSAN cidr: /24
* Prefixo máximo de sub-nets da vSphere/vSAN CIDR: /21


## <a name="access-the-avs-portal"></a>Aceda ao portal AVS

Aceda ao [portal AVS.](access-cloudsimple-portal.md)

## <a name="create-a-new-avs-private-cloud"></a>Criar uma nova Nuvem Privada AVS

1. Selecione **Todos os serviços**.
2. Pesquisa de **Serviços AVS**.
3. Selecione o serviço AVS no qual pretende criar a sua Nuvem Privada AVS.
4. A partir de **uma visão geral,** clique **em Criar Nuvem Privada AVS** para abrir um novo separador de navegador para portal AVS. Se solicitado, entre com suas credenciais de entrada do Azure.

    ![Criar nuvem privada AVS a partir de Azure](media/create-private-cloud-from-azure.png)

5. No portal AVS, forneça um nome para a sua Nuvem Privada AVS.
6. Selecione **Localização** para a sua Nuvem Privada AVS.
7. Selecione **tipo nó,** consistente com o que comprou no Azure.
8. Especifique a **contagem de nós**. Pelo menos três nódosos são necessários para criar uma Nuvem Privada AVS.
5. No portal do CloudSimple, forneça um nome para sua nuvem privada.
6. Selecione **Localização** para a sua Nuvem Privada.
7. Selecione o **tipo de nó**, consistente com o que você provisionou no Azure.
8. Especifique a **contagem de nós**.  Pelo menos três nós são necessários para criar uma nuvem privada.

    ![Criar Nuvem Privada AVS - Informações básicas](media/create-private-cloud-basic-info.png)

9. Clique em **Avançar: opções avançadas**.
10. Insira o intervalo CIDR para sub-redes vSphere/vSAN. Certifique-se de que o intervalo CIDR não se sobreponha a nenhuma das suas sub-redes do Azure locais ou outras (redes virtuais) ou com a sub-rede de gateway.

    **Opções de intervalo CIDR:** /24,/23,/22 ou/21. Uma gama DE /24 CIDR suporta até nove nós, uma gama DE ICDR /23 suporta até 41 nós, e uma gama de /22 e /21 CIDR suporta até 64 nós (o número máximo de nós numa Nuvem Privada AVS).

    > [!IMPORTANT]
    > Os endereços IP na gama vSphere/vSAN CIDR são reservados para utilização pela infraestrutura AVS Private Cloud. Não use o endereço IP nesse intervalo em qualquer máquina virtual.

11. Clique em **Avançar: examinar e criar**.
12. Examine as configurações. Se você precisar alterar as configurações, clique em **anterior**.
13. Clique em **Criar**.

Inicia-se o processo de provisionamento da Nuvem Privada AVS. Pode levar até duas horas para que a Nuvem Privada AVS seja aprovisionada.

Para obter instruções sobre a expansão de uma Nuvem Privada AVS existente, consulte [Expandir uma Nuvem Privada AVS](expand-private-cloud.md).
