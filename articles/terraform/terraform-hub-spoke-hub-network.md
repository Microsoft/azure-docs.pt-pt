---
title: Tutorial-criar uma rede virtual de Hub no Azure usando o Terraform
description: Tutorial que ilustra como criar uma rede virtual de Hub no Azure que atua como um ponto de conexão comum entre outras redes
ms.service: terraform
author: tomarchermsft
ms.author: tarcher
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 25c4d6fa881f7ec6c96dd5ea7c935544374bc57d
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74077708"
---
# <a name="tutorial-create-a-hub-virtual-network-in-azure-by-using-terraform"></a>Tutorial: criar uma rede virtual de Hub no Azure usando o Terraform

A rede virtual do Hub atua como o ponto central de conectividade para a rede local. A rede virtual hospeda serviços compartilhados consumidos por cargas de trabalho hospedadas nas redes virtuais spoke. Para fins de demonstração, nenhum serviço compartilhado é implementado neste tutorial.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Use a HCL (linguagem de configuração do HashiCorp) para implementar a rede virtual do Hub em uma topologia hub e spoke.
> * Use Terraform para criar uma máquina virtual Jumpbox de Hub.
> * Use Terraform para criar um gateway de rede virtual privada de Hub.
> * Use Terraform para criar conexões de gateway local e de Hub.

## <a name="prerequisites"></a>Pré-requisitos

1. [Crie uma topologia de rede híbrida Hub e spoke com Terraform no Azure](./terraform-hub-spoke-introduction.md).
1. [Crie uma rede virtual local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md).

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

A rede de Hub consiste nos seguintes componentes:

- Uma rede virtual do Hub
- Um gateway de rede virtual do Hub
- Conexões de gateway de Hub 

O arquivo de configuração Terraform a seguir define os recursos:

1. Navegue para o [portal do Azure](https://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se não tiver selecionado um ambiente anteriormente, selecione **Bash** como o seu ambiente.

    ![Comandos do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Altere os diretórios para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Altere os diretórios para o novo diretório.

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-hub-virtual-network"></a>Declarar a rede virtual do Hub

Crie o arquivo de configuração Terraform que declara a rede virtual do Hub.

1. No Cloud Shell, crie um ficheiro com o nome `hub-vnet.tf`.

    ```bash
    code hub-vnet.tf
    ```

1. Cole o seguinte código no editor:

    ```hcl
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }

    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = local.hub-resource-group
      location = local.hub-location
    }

    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name
      address_space       = ["10.0.0.0/16"]

      tags {
        environment = "hub-spoke"
      }
    }

    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.255.224/27"
    }

    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.64/27"
    }

    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      virtual_network_name = azurerm_virtual_network.hub-vnet.name
      address_prefix       = "10.0.0.32/27"
    }

    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name  = azurerm_resource_group.hub-vnet-rg.name
      enable_ip_forwarding = true

      ip_configuration {
        name                          = local.prefix-hub
        subnet_id                     = azurerm_subnet.hub-mgmt.id
        private_ip_address_allocation = "Dynamic"
      }

      tags {
        environment = local.prefix-hub
      }
    }

    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name   = azurerm_resource_group.hub-vnet-rg.name
      network_interface_ids = [azurerm_network_interface.hub-nic.id]
      vm_size               = var.vmsize

      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }

      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }

      os_profile {
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = var.username
        admin_password = var.password
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = local.prefix-hub
      }
    }

    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "hub-vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = azurerm_public_ip.hub-vpn-gateway1-pip.id
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = azurerm_subnet.hub-gateway-subnet.id
      }
      depends_on = ["azurerm_public_ip.hub-vpn-gateway1-pip"]
    }

    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = azurerm_resource_group.hub-vnet-rg.location
      resource_group_name = azurerm_resource_group.hub-vnet-rg.name

      type           = "Vnet2Vnet"
      routing_weight = 1

      virtual_network_gateway_id      = azurerm_virtual_network_gateway.hub-vnet-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.onprem-vpn-gateway.id

      shared_key = local.shared-key
    }

    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = azurerm_resource_group.onprem-vnet-rg.location
      resource_group_name = azurerm_resource_group.onprem-vnet-rg.name
      type                            = "Vnet2Vnet"
      routing_weight = 1
      virtual_network_gateway_id      = azurerm_virtual_network_gateway.onprem-vpn-gateway.id
      peer_virtual_network_gateway_id = azurerm_virtual_network_gateway.hub-vnet-gateway.id

      shared_key = local.shared-key
    }
    ```
    
3. Salve o arquivo e saia do editor.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"] 
> [Criar um dispositivo de rede virtual de Hub com o Terraform no Azure](./terraform-hub-spoke-hub-nva.md)