---
title: Configurar vRealize Operations for Azure VMware Solution (AVS)
description: ''
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 57b513b681c4d2522b1c92946aab80fe8ba95746
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87476073"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution-avs"></a>Configurar vRealize Operations for Azure VMware Solution (AVS)


vRealize Operations Manager é uma plataforma de gestão de operações que permite aos administradores de infraestruturas VMware monitorizar os recursos do sistema. Estes recursos do sistema podem ser objetos de nível de aplicação ou de infraestrutura (tanto físicos como virtuais). Historicamente, a maioria dos administradores da VMware usou as operações vRealize para monitorizar e gerir os componentes de nuvem privada VMware – vCenter, ESXi, NSX-T, vSAN e Hybrid Cloud Extension (HCX). Cada nuvem privada AVS é a fornecida com uma implementação dedicada vCenter, NSX-T, vSAN e HCX. 

Reveja minuciosamente [Antes de começar](#before-you-begin) e [pré-requisitos](#prerequisites) primeiro. Em seguida, vamos acompanhá-lo através das duas topologias típicas de implantação para vRealize Operations Manager com AVS:

> [!div class="checklist"]
> * [No local vRealize Operations gerindo a implantação de AVS](#on-premises-vrealize-operations-managing-avs-deployment)
> * [vRealize Operações em execução em implantação avs](#vrealize-operations-running-on-avs-deployment)

## <a name="before-you-begin"></a>Before you begin
* Reveja a [documentação do produto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para aprender mais implementação vRealize Operations. 
* Reveja a [série tutorial](tutorial-network-checklist.md)básica do AVS Software Defined Datacenter (SDDC).
* Opcionalmente, reveja a documentação do produto do controlador remoto de [operações vRealize Operações](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) para gerir a opção de implantação do AVS. 



## <a name="prerequisites"></a>Pré-requisitos
* A VPN ou Azure ExpressRoute devem ser configuradas entre as instalações e a AVS SDDC.
* Uma nuvem privada AVS foi implantada em Azure.



## <a name="on-premises-vrealize-operations-managing-avs-deployment"></a>No local vRealize Operations gerindo a implantação de AVS
A maioria dos clientes tem uma implantação existente no local de operações vRealize usadas para gerir um ou mais domínios vCenters no local. Quando os clientes disponibilizam uma nova nuvem privada AVS em Azure, normalmente ligam o seu ambiente no local com o AVS usando um Azure ExpressRoute ou utilizando uma solução VPN Layer 3 – como mostrado abaixo.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="No local vRealize Operations gerindo a implantação de AVS"  border="false":::

Para alargar as capacidades de operações vRealize para a nuvem privada AVS, cria-se uma instância adaptadora[ para os recursos de nuvem privada AVS](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) - para recolher dados da nuvem privada AVS e trazê-lo para o local vRealize Operations. A instância do Gestor de Operações do Local vRealize pode ligar-se diretamente ao gestor vCenter e NSX-T no AVS ou pode opcionalmente implantar um vRealize Operations Remote Collector na nuvem privada AVS. Um vRealize Operations Remote Collector comprime e encripta os dados recolhidos da nuvem privada AVS antes de ser enviado pela rede ExpressRoute ou VPN para o vRealize Operations Manager que está a decorrer no local. 

> [!TIP]
> Consulte a [documentação VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-avs-deployment"></a>vRealize Operações em execução em implantação avs

Outra opção de implementação é implementar uma instância de vRealize Operations Manager em um dos clusters vSphere na nuvem privada AVS – como mostrado abaixo. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize Operações em execução em AVS" border="false":::

Depois de implementar a instância AVS de vRealize Operations pode configurar vRealize Operations para recolher dados de vCenter, ESXi, NSX-T, vSAN e HCX. 

> [!TIP]
> Consulte a [documentação VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




