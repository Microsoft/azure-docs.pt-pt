---
title: Configurar vRealize Operations for Azure VMware Solution
description: Saiba como configurar as operações vRealize para a sua nuvem privada Azure VMware Solution.
ms.topic: how-to
ms.date: 01/26/2021
ms.openlocfilehash: c2470ecde0874b46da1236ca6e99e6b0b3eb990d
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880696"
---
# <a name="set-up-vrealize-operations-for-azure-vmware-solution"></a>Configurar vRealize Operations for Azure VMware Solution


vRealize Operations Manager é uma plataforma de gestão de operações que permite aos administradores de infraestruturas VMware monitorizar os recursos do sistema. Estes recursos do sistema podem ser objetos de nível de aplicação ou de infraestrutura (tanto físicos como virtuais). A maioria dos administradores da VMware utilizou vRealize Operations para monitorizar e gerir os componentes de nuvem privada VMware - vCenter, ESXi, NSX-T, vSAN e VMware HCX.  Cada nuvem privada Azure VMware Solution inclui uma implementação dedicada vCenter, NSX-T, vSAN e HCX. 

Reveja minuciosamente [Antes de começar](#before-you-begin) e [pré-requisitos](#prerequisites) primeiro. Depois, vamos acompanhá-lo através das duas topologias típicas de implantação:

> [!div class="checklist"]
> * [No local vRealize Operations gerindo a implementação da Solução VMware Azure](#on-premises-vrealize-operations-managing-azure-vmware-solution-deployment)
> * [vRealize Operações em execução na implementação da Solução VMware Azure](#vrealize-operations-running-on-azure-vmware-solution-deployment)

## <a name="before-you-begin"></a>Antes de começar
* Reveja a [documentação do produto vRealize Operations Manager](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para saber mais sobre a implementação da vRealize Operations. 
* Reveja a base da Solução Azure VMware Software-Defined [séries tutoriais](tutorial-network-checklist.md)datacenter (SDDC).
* Opcionalmente, reveja a documentação do produto do controlador remoto de [operações vRealize Operações](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-263F9219-E801-4383-8A59-E84F3D01ED6B.html) para gerir a opção de implementação da Solução Azure VMware. 


## <a name="prerequisites"></a>Pré-requisitos
* [vRealize Gestor de Operações](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) instalado.
* Uma VPN ou um Azure ExpressRoute configurado entre as instalações e a Azure VMware Solution SDDC.
* Uma nuvem privada Azure VMware Solution foi implantada em Azure.



## <a name="on-premises-vrealize-operations-managing-azure-vmware-solution-deployment"></a>No local vRealize Operations gerindo a implementação da Solução VMware Azure
A maioria dos clientes tem uma implantação existente no local de operações vRealize para gerir um ou mais domínios vCenters no local. Quando fornecem uma nuvem privada Azure VMware Solution, conectam o seu ambiente no local com a sua nuvem privada utilizando uma solução Azure ExpressRoute ou uma solução VPN Layer 3.  

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-1.png" alt-text="No local vRealize Operations gerindo a implementação da Solução VMware Azure"  border="false":::

Para alargar as capacidades de operações vRealize à nuvem privada Azure VMware Solution, cria uma instância adaptadora [para os recursos de nuvem privada](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.config.doc/GUID-640AD750-301E-4D36-8293-1BFEB67E2600.html). Recolhe dados da nuvem privada Azure VMware Solution e traz-os para o local vRealize Operations. A instância do Gestor de Operações do Local vRealize pode ligar-se diretamente ao gestor vCenter e NSX-T na Azure VMware Solution. Opcionalmente, pode implementar um vRealize Operations Remote Collector na nuvem privada Azure VMware Solution. O colecionador comprime e encripta os dados recolhidos da nuvem privada antes de ser enviado pela rede ExpressRoute ou VPN para o vRealize Operations Manager que está a decorrer no local. 

> [!TIP]
> Consulte a [documentação VMware](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.vapp.doc/GUID-7FFC61A0-7562-465C-A0DC-46D092533984.html) para obter um guia passo a passo para a instalação do vRealize Operations Manager. 



## <a name="vrealize-operations-running-on-azure-vmware-solution-deployment"></a>vRealize Operações em execução na implementação da Solução VMware Azure

Outra opção é implementar uma instância de vRealize Operations Manager em um cluster vSphere na nuvem privada. 

>[!IMPORTANT]
>Esta opção não é suportada atualmente pela VMware.

:::image type="content" source="media/vrealize-operations-manager/vrealize-operations-deployment-option-2.png" alt-text="vRealize Operações em execução na Solução VMware Azure" border="false":::

Uma vez implementada a ocorrência, pode configurar vRealize Operations para recolher dados de vCenter, ESXi, NSX-T, vSAN e HCX. 



## <a name="known-limitations"></a>Limitações conhecidas

- O **utilizador cloudadmin \@ vsphere.local** na Azure VMware Solution tem [privilégios limitados.](concepts-role-based-access-control.md)  As máquinas virtuais (VMs) na Azure VMware Solution não suportam a recolha de memórias no hóspede utilizando ferramentas VMware.  A utilização ativa e consumida da memória continua a funcionar neste caso.
- A otimização da carga de trabalho para a intenção de negócios baseada em hospedeiros não funciona porque a Azure VMware Solutions gere configurações de cluster, incluindo definições de DRS.
- A otimização da carga de trabalho para a colocação de clusters cruzadas dentro do SDDC utilizando a intenção de negócio baseada no cluster é totalmente suportada com vRealize Operations Manager 8.0 e em diante. No entanto, a otimização da carga de trabalho não está ciente das piscinas de recursos e coloca os VMs ao nível do cluster. Um utilizador pode corrigi-lo manualmente na interface Azure VMware Solution vCenter Server.
- Não é possível iniciar sação no vRealize Operations Manager utilizando as credenciais do Servidor VMware Solução VMware Azure. 
- A Azure VMware Solution não suporta o plugin vRealize Operations Manager.

Quando ligar a Solução VMware Azure vCenter ao vRealize Operations Manager utilizando uma conta de nuvem de servidor vCenter, verá um aviso:

:::image type="content" source="./media/vrealize-operations-manager/warning-adapter-instance-creation-succeeded.png" alt-text="Criação de instância adaptador de aviso conseguiu":::

O aviso ocorre porque o utilizador **cloudadmin \@ vsphere.local** na Azure VMware Solution não tem privilégios suficientes para fazer todas as ações do servidor vCenter necessárias para o registo. No entanto, os privilégios são suficientes para que a instância do adaptador faça a recolha de dados, como se vê abaixo:

:::image type="content" source="./media/vrealize-operations-manager/adapter-instance-to-perform-data-collection.png" alt-text="Instância adaptador para realizar a recolha de dados":::

Para obter mais informações, consulte [os privilégios necessários para configurar uma instância do adaptador vCenter](https://docs.vmware.com/en/vRealize-Operations-Manager/8.1/com.vmware.vcom.core.doc/GUID-3BFFC92A-9902-4CF2-945E-EA453733B426.html).

<!-- LINKS - external -->


<!-- LINKS - internal -->




