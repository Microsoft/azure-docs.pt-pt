---
title: Tutorial - Criar um hub e falou de topologia de rede híbrida em Azure usando terraforma
description: Tutorial ilustrando como criar toda uma arquitetura híbrida de referência de rede em Azure usando terrafora
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 6f156dd90b83ceaf5749c8c2acebae35bcb54a92
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472184"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Tutorial: Criar um hub e falou de topologia de rede híbrida em Azure usando terraforma

Esta série tutorial mostra como usar a Terraform para implementar em Azure um [hub e topologia de rede falada.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 

Um hub e topologia falada é uma forma de isolar as cargas de trabalho enquanto partilha serviços comuns. Estes serviços incluem identidade e segurança. O hub é uma rede virtual (VNet) que funciona como um ponto de ligação central para uma rede no local. Os spokes são VNets que configuram o peering com o hub. Os serviços partilhados são implantados no centro, enquanto as cargas de trabalho individuais são implantadas dentro de redes de fala.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Use hCL (HashiCorp Language) para estabelecer hub e recursos de arquitetura de referência de rede híbrida
> * Use terrafora para criar recursos de eletrodomésticos de rede hub
> * Use a Terraform para criar rede de hub em Azure para agir como ponto comum para todos os recursos
> * Use terrafora para criar cargas de trabalho individuais como VNets falados em Azure
> * Utilizar a Terraform para estabelecer gateways e ligações entre as instalações e as redes Azure
> * Use terrafora para criar vNet peerings para redes faladas

## <a name="prerequisites"></a>Pré-requisitos

- **Subscrição Azure**: Se ainda não tiver uma subscrição Azure, crie uma [conta Azure gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Instalar e configurar terrafora:** Para fornecer VMs e outras infraestruturas em Azure, [instalar e configurar terraforma](terraform-install-configure.md)

## <a name="hub-and-spoke-topology-architecture"></a>Hub e arquitetura de topologia falada

No centro e falou topologia, o centro é um VNet. O VNet funciona como um ponto central de conectividade com a sua rede no local. Os spokes são VNets que partilham com o hub e podem ser utilizados para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação de gateway ExpressRoute ou de VPN. A imagem seguinte demonstra os componentes num hub e a topologia falada:

![Hub e falou arquitetura de topologia em Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Benefícios do hub e topologia falada

Um hub e topologia de rede falada é uma forma de isolar as cargas de trabalho enquanto partilha serviços comuns. Estes serviços incluem identidade e segurança. O hub é um VNet que funciona como um ponto de ligação central para uma rede no local. Os spokes são VNets que configuram o peering com o hub. Os serviços partilhados são implantados no centro, enquanto as cargas de trabalho individuais são implantadas dentro de redes de fala. Aqui estão alguns benefícios do hub e da topologia da rede falada:

- **Economia de custos** centralizando serviços num único local que pode ser partilhado por várias cargas de trabalho. Estas cargas de trabalho incluem aparelhos virtuais de rede e servidores DNS.
- **Ultrapassar os limites de subscrição** ao configurar o peering das VNets de subscrições diferentes para o hub central.
- **Separação das preocupações** entre o TI central (SecOps, InfraOps) e as cargas de trabalho (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Usos típicos para o centro e arquitetura falada

Alguns dos usos típicos para um hub e arquitetura falada incluem:

- Muitos clientes têm cargas de trabalho que são implantadas em diferentes ambientes. Estes ambientes incluem desenvolvimento, testes e produção. Muitas vezes, estas cargas de trabalho precisam de partilhar serviços como DNS, IDS, NTP ou AD DS. Estes serviços partilhados podem ser colocados no centro VNet. Dessa forma, cada ambiente é implantado para um discurso para manter o isolamento.
- Cargas de trabalho que não requerem conectividade entre si, mas que requerem acesso a serviços partilhados.
- Empresas que exigem controlo central sobre aspetos de segurança.
- Empresas que exigem uma gestão segregada para as cargas de trabalho em cada um dos seus discursos.

## <a name="preview-the-demo-components"></a>Pré-visualizar os componentes da demonstração

À medida que trabalha através de cada tutorial desta série, vários componentes são definidos em scripts terraformedistintos. A arquitetura de demonstração criada e implantada consiste nos seguintes componentes:

- **Rede no local**. Uma rede privada de área local que funciona com uma organização. Para a arquitetura de referência do hub e falou, um VNet em Azure é usado para simular uma rede no local.

- **Dispositivo VPN**. Um dispositivo ou serviço VPN fornece conectividade externa à rede no local. O dispositivo VPN pode ser um aparelho de hardware ou uma solução de software. 

- **VNet do Hub**. O centro é o ponto central de conectividade com a sua rede no local e um local para hospedar serviços. Estes serviços podem ser consumidos pelas diferentes cargas de trabalho alojadas nos VNets falados.

- **Sub-rede do gateway**. Os portões VNet são mantidos na mesma sub-rede.

- **VNets Spoke**. Os spokes podem ser utilizados para isolar cargas de trabalho nas suas próprias VNets, geridas separadamente dos outros spokes. Cada carga de trabalho pode incluir várias camadas com várias sub-redes ligadas através de balanceadores de carga do Azure. 

- **VNet peering**. Dois VNets podem ser ligados usando uma ligação de observação. As ligações de peering são ligações não transitivas de baixa latência entre as VNets. Uma vez espreitados, os VNets trocam o tráfego utilizando a espinha dorsal Azure, sem precisar em router. Num hub e na topologia da rede, o vNet peering é usado para ligar o hub a cada um dos discursos. Você pode peer VNets na mesma região, ou regiões diferentes.

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

Crie o diretório que detém os seus ficheiros de configuração Terraform para a demonstração.

1. Navegue para o [portal do Azure](https://portal.azure.com).

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

1. Na Cloud Shell, abra um novo ficheiro chamado `main.tf`.

    ```bash
    code main.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Guarde o ficheiro e saia do editor.

## <a name="create-the-variables-file"></a>Criar o ficheiro de variáveis

Crie o ficheiro de configuração Terraform para variáveis comuns que são usadas em diferentes scripts.

1. Na Cloud Shell, abra um novo ficheiro chamado `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
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

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Criar rede virtual no local com terraforma em Azure](./terraform-hub-spoke-on-prem.md)