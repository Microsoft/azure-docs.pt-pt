---
title: Planeamento da implementação da Solução VMware Azure
description: Este artigo descreve um fluxo de trabalho de implementação de Solução VMware Azure.  O resultado final é um ambiente pronto para a criação e migração de máquinas virtuais (VM).
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: cdf4ddd6166920fa7461bfd85e01ef0efd6dfbb9
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704572"
---
# <a name="planning-the-azure-vmware-solution-deployment"></a>Planeamento da implementação da Solução VMware Azure

Este artigo fornece-lhe o processo de planeamento para identificar e recolher dados utilizados durante a implementação. Ao planear a sua implantação, certifique-se de documentar as informações que recolhe para uma referência fácil durante a implementação.

Os processos deste arranque rápido resultam num ambiente pronto para a produção para a criação de máquinas virtuais (VMs) e migração. 

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

## <a name="number-of-hosts"></a>Número de anfitriões

Defina o número de anfitriões que pretende implantar na nuvem privada Azure VMware Solution.  O número mínimo de hospedeiros é de três, e o máximo é de 16 por agrupamento.  Para mais informações, consulte a documentação [privada de nuvem e clusters Azure VMware Solution.](concepts-private-clouds-clusters.md#clusters)

Pode sempre estender o cluster mais tarde se precisar de ir além do número inicial de implantação.

## <a name="vcenter-admin-password"></a>vCenter senha de administração
Defina a palavra-passe de administração vCenter.  Durante a implementação, irá criar uma palavra-passe de administração vCenter. A palavra-passe é para a cloudadmin@vsphere.local conta de administração durante a construção do vCenter. Vai usá-lo para entrar no vCenter.

## <a name="nsx-t-admin-password"></a>Senha de administração NSX-T
Defina a palavra-passe de administração NSX-T.  Durante a implementação, irá criar uma senha de administração NSX-T. A palavra-passe é atribuída ao utilizador administrativo na conta NSX durante a construção do NSX. Vai usá-lo para entrar no NSX-T Manager.

## <a name="ip-address-segment"></a>Segmento de endereço IP

O primeiro passo para planear a implantação é planear a segmentação de IP.  A Azure VMware Solution ingere uma rede /22 que fornece. Em seguida, esculpe-o em segmentos menores e, em seguida, usa esses segmentos IP para vCenter, VMware HCX, NSX-T e vMotion.

A Azure VMware Solution conecta-se à sua Rede Virtual Microsoft Azure através de um circuito ExpressRoute interno. Na maioria dos casos, conecta-se ao seu centro de dados através do ExpressRoute Global Reach. 

Azure VMware Solution, o seu ambiente Azure existente e o ambiente no local em todas as rotas de troca (normalmente). Sendo assim, o bloco de endereços de rede CIDR /22 que define neste passo não deve sobrepor-se a nada que já tenha no local ou a Azure.

**Exemplo:** 10.0.0.0/22

Para obter mais informações, consulte a [lista de verificação de planeamento da Rede.](tutorial-network-checklist.md#routing-and-subnet-considerations)

:::image type="content" source="media/pre-deployment/management-vmotion-vsan-network-ip-diagram.png" alt-text="Identificar - Segmento de endereço IP" border="false":::  

## <a name="ip-address-segment-for-virtual-machine-workloads"></a>Segmento de endereço IP para cargas de trabalho de máquinas virtuais

Identifique um segmento IP para criar a sua primeira rede (segmento NSX) na sua nuvem privada.  Por outras palavras, pretende criar um segmento de rede na Solução VMware Azure para que possa implantar VMs na Solução VMware Azure.   

Mesmo que planeie apenas alargar as redes L2, crie um segmento de rede que valide o ambiente.

Lembre-se, quaisquer segmentos IP criados devem ser únicos em todo o seu Azure e pegada no local.  

**Exemplo:** 10.0.4.0/24

:::image type="content" source="media/pre-deployment/nsx-segment-diagram.png" alt-text="Identificar - Segmento de endereço IP para cargas de trabalho de máquinas virtuais" border="false":::     

## <a name="optional-extend-networks"></a>(Opcional) Alargar redes

Pode estender os segmentos de rede desde as instalações até à Azure VMware Solution, e se o fizer, identifique essas redes agora.  

Tenha em mente que:

- Se pretender estender as redes a partir do local, essas redes devem ligar-se a um [vSphere Distributed Switch (vDS)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-B15C6A13-797E-4BCB-B9D9-5CBC5A60C3A6.html) no seu ambiente VMware no local.  
- Se a rede(s) pretender prolongar ao vivo num [vSphere Standard Switch,](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.networking.doc/GUID-350344DE-483A-42ED-B0E2-C811EE927D59.html)então não pode ser estendida.

## <a name="azure-virtual-network-to-attach-azure-vmware-solution"></a>Rede Virtual Azure para anexar Solução VMware Azure

Neste passo, irá identificar um gateway de rede virtual ExpressRoute e uma rede virtual de suporte Azure utilizada para ligar o circuito Azure VMware Solution ExpressRoute.  O circuito ExpressRoute facilita a conectividade de e para a nuvem privada Azure VMware Solution para outros serviços Azure, recursos Azure e ambientes no local.

Pode utilizar um *novo* gateway *de* rede virtual ExpressRoute existente.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-expressroute-diagram.png" alt-text="Identidade - Rede Virtual Azure para anexar Solução VMware Azure" border="false":::

### <a name="use-an-existing-expressroute-virtual-network-gateway"></a>Utilize um gateway de rede virtual ExpressRoute existente

Se utilizar um gateway de rede virtual ExpressRoute *existente,* o circuito Azure VMware Solution ExpressRoute é estabelecido depois de implantar a nuvem privada.  Então, não precisas de preencher o campo **da Rede Virtual.**  

Tome nota do gateway de rede virtual ExpressRoute que utilizará e continue até ao próximo passo.

### <a name="create-a-new-expressroute-virtual-network-gateway"></a>Criar um novo gateway de rede virtual ExpressRoute

Se criar um *novo* gateway de rede virtual ExpressRoute, pode ser utilizada uma Rede Virtual Azure existente, ou pode ser criada uma nova Rede Virtual Azure.  

Se a escolha for utilizar uma Rede Virtual Azure existente, verifique se não existem gateways de rede virtual ExpressRoute pré-existentes na rede virtual e selecione-o no dropdown da Rede Virtual do ecrã de implementação de nuvem privada.

Se a escolha for criar uma nova Rede Virtual Azure, pode ser criada antes do tempo ou durante a implementação clicando na nova opção de Criar a secção de Rede Virtual do ecrã de implementação de nuvem privada.

Para referência, abaixo está uma imagem do Create um ecrã de implementação **de nuvem privada** e delineado a vermelho é o campo **Azure Virtual Network** que foi referido ao longo desta secção.

:::image type="content" source="media/pre-deployment/azure-vmware-solution-deployment-screen-vnet-circle.png" alt-text="Screenshot do ecrã de implementação da Solução VMware Azure com gateway de rede virtual em círculo.":::

>[!NOTE]
>Qualquer rede virtual que seja usada ou criada pode ser vista pelo seu ambiente no local e pela Solução Azure VMware, por isso certifique-se de que qualquer segmento IP que utilize nesta rede virtual e sub-redes não se sobreponha.

## <a name="vmware-hcx-network-segments"></a>Segmentos de rede VMware HCX

VMware HCX é uma tecnologia agregada com Azure VMware Solution. Os casos de utilização primária para VMware HCX são migrações de carga de trabalho e recuperação de desastres. Se planeia fazer qualquer um dos dois, é melhor planear a rede agora.   Caso contrário, pode saltar e continuar até ao próximo passo.

[!INCLUDE [hcx-network-segments](includes/hcx-network-segments.md)]

## <a name="next-steps"></a>Próximos passos
Agora que recolheu e documentou as informações necessárias continuam para a secção seguinte para criar a sua nuvem privada Azure VMware Solution.

> [!div class="nextstepaction"]
> [Implementar o Azure VMware Solution](deploy-azure-vmware-solution.md)
