---
title: Criar um hub- and -spoke topologia de rede híbrida com o Terraform no Azure
description: Tutorial que ilustra como criar uma arquitetura de referência de rede híbrida todo no Azure com o Terraform
services: terraform
ms.service: terraform
keywords: terraform, hub- and -spoke, redes, redes de híbridas, devops, máquina virtual, do azure, o vnet peering, aplicação de rede virtual
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: 25ba7016c10fc13357b403efae6e4996de59b624
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411502"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-with-terraform-in-azure"></a>Tutorial: Criar um hub- and -spoke topologia de rede híbrida com o Terraform no Azure

Nesta série de tutoriais mostra como usar o Terraform para implementar no Azure um [topologia de rede hub- and -spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Uma topologia hub- and -spoke é uma forma para isolar cargas de trabalho, compartilhando serviços comuns. Esses serviços incluem a identidade e segurança. O hub é uma rede virtual (VNet) que atua como um ponto de ligação central a uma rede no local. Os spokes são VNets que configuram o peering com o hub. Serviços partilhados são implementados no hub, enquanto as cargas de trabalho individuais são implementadas dentro das redes do spoke.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Utilize o HCL (HashiCorp Language) para criar o layout hub- and -spoke recursos da arquitetura híbrida rede referência
> * Utilize o Terraform para criar recursos de aplicação de rede hub
> * Utilize o Terraform para criar a rede de hub no Azure para atuar como um ponto comum para todos os recursos
> * Utilize o Terraform para criar cargas de trabalho individuais como spoke VNets no Azure
> * Utilize o Terraform para estabelecer gateways e ligações no local e redes do Azure
> * Utilize o Terraform para criar VNet peerings de redes do spoke

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição do Azure**: Se ainda não tiver uma subscrição do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Instalar e configurar o Terraform**: Aprovisionar VMs e outras infraestruturas no Azure, [instalar e configurar o Terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Arquitetura de topologia hub- and -spoke

Topologia hub- and -spoke, o hub é uma VNet. A VNet atua como um ponto central de conectividade à sua rede no local. Os spokes são VNets que partilham com o hub e podem ser utilizados para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação de gateway ExpressRoute ou de VPN. A imagem seguinte demonstra os componentes numa topologia hub- and -spoke:

![Arquitetura de topologia hub- and -spoke no Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Benefícios da topologia hub- and -spoke

Uma topologia de rede hub- and -spoke é uma forma para isolar cargas de trabalho, compartilhando serviços comuns. Esses serviços incluem a identidade e segurança. O hub é uma VNet que age como um ponto de ligação central a uma rede no local. Os spokes são VNets que configuram o peering com o hub. Serviços partilhados são implementados no hub, enquanto as cargas de trabalho individuais são implementadas dentro das redes do spoke. Aqui estão algumas das vantagens da topologia de rede hub- and -spoke:

- **Economia de custos** ao centralizar os serviços num único local que podem ser partilhadas por várias cargas de trabalho. Estas cargas de trabalho incluem aplicações virtuais de rede e servidores DNS.
- **Ultrapassar os limites de subscrição** ao configurar o peering das VNets de subscrições diferentes para o hub central.
- **Separação das preocupações** entre o TI central (SecOps, InfraOps) e as cargas de trabalho (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Utilizações típicas de arquitetura hub- and -spoke

Alguns dos usos de uma arquitetura hub- and -spoke incluem:

- Muitos clientes têm cargas de trabalho que são implementadas em ambientes diferentes. Estes ambientes incluem o desenvolvimento, teste e produção. Muitas vezes, estas cargas de trabalho precisam compartilhar serviços como o DNS, IDS, NTP ou AD DS. Estes serviços partilhados podem ser colocados na VNet do hub. Dessa forma, cada ambiente é implementado num spoke para manter o isolamento.
- Cargas de trabalho que não necessitam de conectividade entre si, mas exigem acesso a serviços compartilhados.
- Empresas que exigem controlo central sobre os aspectos de segurança.
- Empresas que exigem gestão segregada para as cargas de trabalho em cada spoke.

## <a name="preview-the-demo-components"></a>Os componentes de demonstração de pré-visualização

À medida que percorre cada tutorial nesta série, vários componentes são definidos nos scripts de Terraform distintos. A arquitetura de demonstração foi criada e implantada é composta pelos seguintes componentes:

- **Rede no local**. Uma rede de área local privada em execução com uma organização. Arquitetura de referência do hub- and -spoke, uma VNet no Azure é utilizada para simular uma rede no local.

- **Dispositivo VPN**. Um dispositivo VPN ou o serviço fornece conectividade externa à rede no local. O dispositivo VPN pode ser um dispositivo de hardware ou uma solução de software. 

- **VNet do Hub**. O hub é o ponto central de conectividade à sua rede no local e um local para alojar serviços. Estes serviços podem ser consumidos pelas diferentes cargas de trabalho alojadas nas spoke VNets.

- **Sub-rede do gateway**. Os gateways de VNet são guardados na mesma sub-rede.

- **VNets Spoke**. Os spokes podem ser utilizados para isolar cargas de trabalho nas suas próprias VNets, geridas separadamente dos outros spokes. Cada carga de trabalho pode incluir várias camadas com várias sub-redes ligadas através de balanceadores de carga do Azure. 

- **VNet peering**. Duas VNets podem ser ligadas através de uma ligação de peering. As ligações de peering são ligações não transitivas de baixa latência entre as VNets. Uma vez executado o peering, as VNets trocam tráfego com o backbone do Azure, sem precisar de um roteador. Numa topologia de rede hub- and -spoke, o VNet peering é utilizado para ligar o hub a cada spoke. Pode configurar o peering entre VNets na mesma região ou regiões diferentes.

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

Crie o diretório que contém os ficheiros de configuração do Terraform na demonstração.

1. Navegue para o [portal do Azure](http://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Crie um diretório com o nome `hub-spoke`.

    ```bash
    mkdir hub-spoke
    ```

1. Mude para o diretório novo:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-azure-provider"></a>Declarar o fornecedor do Azure

Crie o ficheiro de configuração Terraform que declara o fornecedor do Azure.

1. No Cloud Shell, abra um novo ficheiro designado `main.tf`.

    ```bash
    code main.tf
    ```

1. Cole o seguinte código no editor:

    ```JSON
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Guarde o ficheiro e saia do editor.

## <a name="create-the-variables-file"></a>Criar o ficheiro de variáveis

Crie o ficheiro de configuração do Terraform para variáveis comuns que são utilizados em todos os scripts diferentes.

1. No Cloud Shell, abra um novo ficheiro designado `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o seguinte código no editor:

    ```JSON
    variable "location" {
      description = "Location of the network"
      default     = "centralus"
    }
    
    variable "username" {
      description = "Username for Virtual Machines"
      default     = "testadmin"
    }
    
    variable "password" {
      description = "Password for Virtual Machines"
      default     = "Password1234!"
    }
    
    variable "vmsize" {
      description = "Size of the VMs"
      default     = "Standard_DS1_v2"
    }
    ```

1. Guarde o ficheiro e saia do editor.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"] 
> [Criar rede virtual no local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md)
