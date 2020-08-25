---
title: Configurar vRealize Operations for Azure VMware Solution
description: Saiba como configurar as operações vRealize para a sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 08/06/2020
ms.openlocfilehash: 729ee5c64776d7d04f702af62451175f7c53421b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750396"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Configurar vRealize Operations for Azure VMware Solution


vRealize Operations Manager é uma plataforma de gestão de operações que permite aos administradores de infraestruturas VMware monitorizar os recursos do sistema. Estes recursos do sistema podem ser objetos de nível de aplicação ou de infraestrutura (tanto físicos como virtuais). Historicamente, a maioria dos administradores da VMware usou as operações vRealize para monitorizar e gerir os componentes de nuvem privada VMware – vCenter, ESXi, NSX-T, vSAN e Hybrid Cloud Extension (HCX). Cada nuvem privada Azure VMware Solution é a fornecida com uma implementação dedicada vCenter, NSX-T, vSAN e HCX. 

Reveja minuciosamente [Antes de começar](#before-you-begin) e [pré-requisitos](#prerequisites) primeiro. Em seguida, vamos acompanhá-lo através das duas topologias típicas de implementação para vRealize Operations Manager com Azure VMware Solution:

> [!div class="checklist"]
> * [No local vRealize Operations gerindo a implementação da Solução VMware Azure](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize Operações em execução na implementação da Solução VMware Azure](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Before you begin
* Reveja a [documentação do produto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para aprender mais implementação vRealize Operations. 
* Reveja a [série tutorial](tutorial-network-checklist.md)básica do Software Defined Datacenter (SDDC) do Azure VMware Solution .
* Opcionalmente, reveja a documentação do produto do controlador remoto de [operações vRealize Operações](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) para gerir a opção de implementação da Solução Azure VMware. 



## <a name="prerequisites"></a>Pré-requisitos
* A VPN ou Azure ExpressRoute devem ser configuradas entre as instalações e a Azure VMware Solution SDDC.
* Uma nuvem privada Azure VMware Solution foi implantada em Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>No local vRealize Operations gerindo a implementação da Solução VMware Azure
A maioria dos clientes tem uma implantação existente no local de operações vRealize usadas para gerir um ou mais domínios vCenters no local. Quando os clientes disponibilizam uma nova nuvem privada Azure VMware Solution em Azure, normalmente ligam o seu ambiente no local com a Azure VMware Solution utilizando uma Solução Azure ExpressRoute ou utilizando uma solução VPN Layer 3 – como mostrado abaixo.   

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="No local vRealize Operations gerindo a implementação da Solução VMware Azure"  border="false":::

Para alargar as capacidades de operações vRealize à nuvem privada Azure VMware Solution, cria uma instância adaptadora [para os recursos de nuvem privada Azure VMware Solution](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html) - para recolher dados da nuvem privada Azure VMware Solution e trazê-lo para o local vRealize Operations. A instância do Gestor de Operações do Local vRealize pode ligar-se diretamente ao gestor vCenter e NSX-T na Solução VMware Azure ou pode implantar opcionalmente um vRealize Operations Remote Collector na nuvem privada Azure VMware Solution. Um vRealize Operations Remote Collector comprime e encripta os dados recolhidos a partir da nuvem privada Azure VMware Solution antes de ser enviado pela rede ExpressRoute ou VPN para o vRealize Operations Manager que está a funcionar no local. 

> [!TIP]
> Consulte a [documentação VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize Operações em execução na implementação da Solução VMware Azure

Outra opção de implementação é implementar uma instância de vRealize Operations Manager num dos clusters vSphere na nuvem privada Azure VMware Solution – como mostrado abaixo. 

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize Operações em execução na Solução VMware Azure" border="false":::

Depois de implementar a instância Azure VMware Solution de vRealize Operations pode configurar vRealize Operations para recolher dados de vCenter, ESXi, NSX-T, vSAN e HCX. 

> [!TIP]
> Consulte a [documentação VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager.




<!-- LINKS - external -->


<!-- LINKS - internal -->




