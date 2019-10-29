---
title: Tutorial-criar uma topologia de rede híbrida de Hub e spoke no Azure usando o Terraform
description: Tutorial ilustrando como criar uma arquitetura de referência de rede híbrida inteira no Azure usando o Terraform
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 8d85163e746f1d2d0713a9a4f247a2061e0029b8
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2019
ms.locfileid: "72969403"
---
# <a name="tutorial-create-a-hub-and-spoke-hybrid-network-topology-in-azure-using-terraform"></a>Tutorial: criar uma topologia de rede híbrida de Hub e spoke no Azure usando o Terraform

Esta série de tutoriais mostra como usar o Terraform para implementar no Azure uma [topologia de rede de Hub e spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke). 

Uma topologia de Hub e spoke é uma maneira de isolar cargas de trabalho ao compartilhar serviços comuns. Esses serviços incluem identidade e segurança. O Hub é uma rede virtual (VNet) que atua como um ponto de conexão central para uma rede local. Os spokes são VNets que configuram o peering com o hub. Os serviços compartilhados são implantados no Hub, enquanto as cargas de trabalho individuais são implantadas dentro de redes spoke.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Use a HCL (linguagem HashiCorp) para dispor os recursos de arquitetura de referência de rede híbrida Hub e spoke
> * Usar o Terraform para criar recursos de dispositivo de rede de Hub
> * Use Terraform para criar a rede de Hub no Azure para atuar como um ponto comum para todos os recursos
> * Use Terraform para criar cargas de trabalho individuais como spoke VNets no Azure
> * Usar o Terraform para estabelecer gateways e conexões entre o local e as redes do Azure
> * Usar o Terraform para criar emparelhamentos de VNet para redes de spoke

## <a name="prerequisites"></a>Pré-requisitos

- **Assinatura do Azure**: se você ainda não tiver uma assinatura do Azure, crie uma [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.

- **Instalar e configurar o Terraform**: para provisionar VMs e outras infraestruturas no Azure, [Instale e configure o Terraform](/azure/virtual-machines/linux/terraform-install-configure)

## <a name="hub-and-spoke-topology-architecture"></a>Arquitetura de topologia hub e spoke

Na topologia hub e spoke, o Hub é uma VNet. A VNet atua como um ponto central de conectividade para sua rede local. Os spokes são VNets que partilham com o hub e podem ser utilizados para isolar cargas de trabalho. O tráfego flui entre o datacenter no local e o hub através de uma ligação de gateway do ExpressRoute ou de VPN. A imagem a seguir demonstra os componentes em uma topologia hub e spoke:

![Arquitetura de topologia hub e spoke no Azure](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-architecture.png)

## <a name="benefits-of-the-hub-and-spoke-topology"></a>Benefícios da topologia hub e spoke

Uma topologia de rede hub e spoke é uma maneira de isolar cargas de trabalho ao compartilhar serviços comuns. Esses serviços incluem identidade e segurança. O Hub é uma VNet que atua como um ponto de conexão central para uma rede local. Os spokes são VNets que configuram o peering com o hub. Os serviços compartilhados são implantados no Hub, enquanto as cargas de trabalho individuais são implantadas dentro de redes spoke. Aqui estão alguns benefícios da topologia de rede hub e spoke:

- **Economia de custos** centralizando serviços em um único local que pode ser compartilhado por várias cargas de trabalho. Essas cargas de trabalho incluem dispositivos de virtualização de rede e servidores DNS.
- **Ultrapassar os limites de subscrição** ao configurar o peering das VNets de subscrições diferentes para o hub central.
- **Separação das preocupações** entre o TI central (SecOps, InfraOps) e as cargas de trabalho (DevOps).

## <a name="typical-uses-for-the-hub-and-spoke-architecture"></a>Usos típicos para a arquitetura Hub e spoke

Alguns dos usos típicos para uma arquitetura de Hub e spoke incluem:

- Muitos clientes têm cargas de trabalho implantadas em ambientes diferentes. Esses ambientes incluem desenvolvimento, teste e produção. Muitas vezes, essas cargas de trabalho precisam compartilhar serviços como DNS, IDS, NTP ou AD DS. Esses serviços compartilhados podem ser colocados na VNet do Hub. Dessa forma, cada ambiente é implantado em um spoke para manter o isolamento.
- Cargas de trabalho que não exigem conectividade entre si, mas exigem acesso aos serviços compartilhados.
- Empresas que exigem controle central sobre aspectos de segurança.
- Empresas que exigem gerenciamento segregado para as cargas de trabalho em cada spoke.

## <a name="preview-the-demo-components"></a>Visualizar os componentes de demonstração

Conforme você trabalha em cada tutorial desta série, vários componentes são definidos em scripts Terraform distintos. A arquitetura de demonstração criada e implantada consiste nos seguintes componentes:

- **Rede no local**. Uma rede de área local privada em execução com uma organização. Para a arquitetura de referência de Hub e spoke, uma VNet no Azure é usada para simular uma rede local.

- **Dispositivo VPN**. Um dispositivo ou serviço VPN fornece conectividade externa à rede local. O dispositivo VPN pode ser um dispositivo de hardware ou uma solução de software. 

- **VNet do Hub**. O Hub é o ponto central de conectividade com sua rede local e um local para hospedar serviços. Esses serviços podem ser consumidos pelas diferentes cargas de trabalho hospedadas no VNets do spoke.

- **Sub-rede do gateway**. Os gateways de VNet são mantidos na mesma sub-rede.

- **VNets Spoke**. Os spokes podem ser utilizados para isolar cargas de trabalho nas suas próprias VNets, geridas separadamente dos outros spokes. Cada carga de trabalho pode incluir várias camadas com várias sub-redes ligadas através de balanceadores de carga do Azure. 

- **VNet peering**. Dois VNets podem ser conectados usando uma conexão de emparelhamento. As ligações de peering são ligações não transitivas de baixa latência entre as VNets. Depois de emparelhado, o tráfego do VNets troca usando o backbone do Azure, sem precisar de um roteador. Em uma topologia de rede hub e spoke, o emparelhamento VNet é usado para conectar o hub a cada spoke. Você pode emparelhar VNets na mesma região ou em regiões diferentes.

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

Crie o diretório que contém os arquivos de configuração do Terraform para a demonstração.

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se não tiver selecionado um ambiente anteriormente, selecione **Bash** como o seu ambiente.

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

1. Em Cloud Shell, abra um novo arquivo chamado `main.tf`.

    ```bash
    code main.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    provider "azurerm" {
        version = "~>1.22"
    }
    ```

1. Salve o arquivo e saia do editor.

## <a name="create-the-variables-file"></a>Criar o arquivo de variáveis

Crie o arquivo de configuração Terraform para variáveis comuns que são usadas em diferentes scripts.

1. Em Cloud Shell, abra um novo arquivo chamado `variables.tf`.

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

1. Salve o arquivo e saia do editor.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Criar uma rede virtual local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md)