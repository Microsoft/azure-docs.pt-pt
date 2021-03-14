---
title: Planeamento da implementação da Solução VMware Azure
description: Este artigo descreve um fluxo de trabalho de implementação de Solução VMware Azure.  O resultado final é um ambiente pronto para a criação e migração de máquinas virtuais (VM).
ms.topic: tutorial
ms.date: 03/13/2021
ms.openlocfilehash: f1895f14361b7121ae0d78950cdf8eca3cf7eb52
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462427"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planeamento da implementação da Solução VMware Azure

Este artigo fornece-lhe o processo de planeamento para identificar e recolher as informações que utilizará durante a implementação. Ao planear a sua implantação, certifique-se de documentar as informações que recolhe para uma referência fácil durante a implementação.

Os passos delineados neste início rápido dão-lhe um ambiente pronto para a produção para a criação de máquinas virtuais (VMs) e migração. 

>[!IMPORTANT]
>Antes de criar o seu recurso Azure VMware Solution, siga o como permitir que o artigo [de recursos da Azure VMware Solution](enable-azure-vmware-solution.md) envie um bilhete de apoio para que os seus anfitriões tenham os seus anfitriões atribuídos. Uma vez que a equipa de apoio recebe o seu pedido, leva até cinco dias úteis para confirmar o seu pedido e alocar os seus anfitriões. Se tiver uma nuvem privada Azure VMware Solution e quiser mais anfitriões, passará pelo mesmo processo. 


## <a name="subscription"></a>Subscrição

Identifique a subscrição que pretende utilizar para implementar a Solução VMware Azure.  Pode criar uma nova subscrição ou reutilizar uma existente.

>[!NOTE]
>A subscrição deve ser associada a um Acordo de Empresa da Microsoft ou a um plano Azure do Fornecedor de Soluções Cloud. Para obter mais informações, consulte [Como ativar o recurso Azure VMware Solution](enable-azure-vmware-solution.md).

## <a name="resource-group"></a>Grupo de recursos

Identifique o grupo de recursos que pretende utilizar para a sua Solução Azure VMware.  Geralmente, um grupo de recursos é criado especificamente para a Azure VMware Solution, mas você pode usar um grupo de recursos existente.

## <a name="region"></a>Region

Identifique a região que pretende que a Azure VMware Solution seja implementada.  Para mais informações, consulte os [Produtos Azure Disponíveis por Guia da Região.](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=azure-vmware)

## <a name="resource-name"></a>Nome do recurso

Defina o nome de recurso que utilizará durante a implantação.  O nome do recurso é um nome amigável e descritivo no qual intitula a sua nuvem privada Azure VMware Solution.

>[!IMPORTANT]
>O nome não deve exceder 40 caracteres. Se o nome exceder este limite, não poderá criar endereços IP públicos para utilização com a nuvem privada. 

## <a name="size-hosts"></a>Hospedeiros de tamanho

Identifique os anfitriões de tamanho que pretende utilizar ao implementar a Solução VMware Azure.  Para obter uma lista completa, consulte a documentação [privada Azure VMware Solution e clusters.](concepts-private-clouds-clusters.md#hosts)

## <a name="number-of-clusters-and-hosts"></a>Número de agrupamentos e anfitriões

Na Solução Azure VMware, irá implementar uma nuvem privada e criar vários clusters. Para a sua implantação, terá de definir o número de clusters e os anfitriões f que pretende implantar em cada cluster. O número mínimo de hospedeiros por agrupamento é de três, e o máximo é de 16. O número máximo de aglomerados por nuvem privada é de quatro. O número máximo de nós por nuvem privada é de 64.

Para mais informações, consulte a documentação [privada de nuvem e clusters Azure VMware Solution.](concepts-private-clouds-clusters.md#clusters)

>[!TIP]
>Pode sempre estender o cluster mais tarde se precisar de ir além do número inicial de implantação.

## <a name="vcenter-admin-password"></a>vCenter senha de administração
Defina a palavra-passe de administração vCenter. Durante a implementação, irá criar uma palavra-passe de administração vCenter. A palavra-passe é atribuída à cloudadmin@vsphere.local conta de administração durante a construção do vCenter. Vai usar estas credenciais para se inscrever no vCenter.

## <a name="nsx-t-admin-password"></a>Senha de administração NSX-T
Defina a palavra-passe de administração NSX-T. Durante a implementação, irá criar uma senha de administração NSX-T. A palavra-passe é atribuída ao utilizador administrativo na conta NSX durante a construção do NSX. Usará estas credenciais para iniciar sposição no NSX-T Manager.

## <a name="ip-address-segment-for-private-cloud-management"></a>Segmento de endereço IP para gestão de nuvem privada

O primeiro passo para planear a implantação é planear a segmentação de IP. A Azure VMware Solution requer uma rede CIDR /22. Este espaço de endereço esculpe-o em segmentos de rede mais pequenos (subnetas) e utilizados para a funcionalidade vCenter, VMware HCX, NSX-T e vMotion.

Este bloco de endereços de rede CIDR /22 não deve sobrepor-se a qualquer segmento de rede existente que já tenha no local ou no Azure.

**Exemplo:** 10.0.0.0/22

A Azure VMware Solution conecta-se à sua Rede Virtual Microsoft Azure através de um circuito interno ExpressRoute Global Reach (D-MSEE na visualização abaixo). Esta funcionalidade faz parte do serviço Azure VMware Solution e não será carregada.

Para obter mais informações, consulte a [lista de verificação de planeamento da Rede.](tutorial-network-checklist.md#routing-and-subnet-considerations)

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificar - Segmento de endereço IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segmento de endereço IP para cargas de trabalho de máquinas virtuais

Identifique um segmento IP para criar a sua primeira rede para cargas de trabalho (segmento NSX) na sua nuvem privada. Por outras palavras, terá de criar um segmento de rede na Solução VMware Azure para poder implementar VMs na Solução VMware Azure.

Mesmo que planeie estender as redes das instalações para a Azure VMware Solution (L2), ainda precisa de criar um segmento de rede que valide o ambiente.

Lembre-se, quaisquer segmentos IP criados devem ser únicos em todo o seu Azure e pegada no local.
  
**Exemplo:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificar - Segmento de endereço IP para cargas de trabalho de máquinas virtuais" border="false":::     

## <a name="optional-extend-networks"></a>(Opcional) Alargar redes

Pode estender os segmentos de rede desde as instalações até à Azure VMware Solution, e se o fizer, identifique essas redes agora.  

Tenha em mente que:

- Se pretender estender as redes a partir do local, essas redes devem ligar-se a um [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) no seu ambiente VMware no local.  
- Se a rede(s) pretender prolongar ao vivo num [vSphere Standard Switch,](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)então não pode ser estendida.

## <a name="attach-azure-virtual-network-to-azure-vmware-solution"></a>Anexar a rede virtual Azure à Solução VMware Azure

Neste passo, irá identificar um gateway de rede virtual ExpressRoute e a rede virtual Azure suportada utilizada para ligar o circuito Azure VMware Solution ExpressRoute.  O circuito ExpressRoute facilita a conectividade de e para a nuvem privada Azure VMware Solution para outros serviços Azure, recursos Azure e ambientes no local.

Pode utilizar um *novo* gateway *de* rede virtual ExpressRoute existente.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identidade - Rede Virtual Azure para anexar Solução VMware Azure" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Utilize um gateway de rede virtual ExpressRoute existente

Se utilizar um gateway de rede virtual ExpressRoute *existente,* o circuito Azure VMware Solution ExpressRoute é estabelecido depois de implantar a nuvem privada. Neste caso, deixe o campo **de Rede Virtual** em branco.  

Tome nota do gateway de rede virtual ExpressRoute que utilizará e continue até ao próximo passo.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Criar um novo gateway de rede virtual ExpressRoute

Quando criar um *novo* gateway de rede virtual ExpressRoute, pode utilizar uma Rede Virtual Azure existente ou criar uma nova.  

- Para uma rede virtual Azure existente:
   1. Verifique se não existem gateways de rede virtual ExpressRoute pré-existentes na rede virtual. 
   1. Selecione a rede virtual Azure existente na lista **de Rede Virtual.**

- Para uma nova Rede Virtual Azure, pode criá-la com antecedência ou durante a implementação. Selecione o novo link **Criar** na lista **de Rede Virtual.**

A imagem abaixo mostra o Criar um ecrã de implementação **em nuvem privada** com o campo de Rede **Virtual** realçado.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Screenshot do ecrã de implementação da Solução VMware Azure com campo de Rede Virtual em destaque.":::

>[!NOTE]
>Qualquer rede virtual que seja usada ou criada pode ser vista pelo seu ambiente no local e pela Solução Azure VMware, por isso certifique-se de que qualquer segmento IP que utilize nesta rede virtual e sub-redes não se sobreponha.

## <a name="vmware-hcx-network-segments"></a>Segmentos de rede VMware HCX

VMware HCX é uma tecnologia agregada com Azure VMware Solution. Os casos de utilização primária para VMware HCX são migrações de carga de trabalho e recuperação de desastres. Se planeia fazer qualquer um dos dois, é melhor planear a rede agora.   Caso contrário, pode saltar e continuar até ao próximo passo.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Passos seguintes
Agora que recolheu e documentou as informações necessárias continuam para a secção seguinte para criar a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Implementar o Azure VMware Solution](deploy-azure-vmware-solution.md)
