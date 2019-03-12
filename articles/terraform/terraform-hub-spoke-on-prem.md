---
title: Criar rede virtual no local com o Terraform no Azure
description: Tutorial que ilustra como implementar uma VNet no local no Azure que hospeda a recursos locais
services: terraform
ms.service: azure
keywords: terraform, hub- and -spoke, redes, redes de híbridas, devops, máquina virtual, do azure, o VNet peering, no local
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: fedd83276a0182ecf2d39e43382bc264c6f88de5
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761690"
---
# <a name="tutorial-create-on-premises-virtual-network-with-terraform-in-azure"></a>Tutorial: Criar rede virtual no local com o Terraform no Azure

Neste tutorial, implementa uma rede no local com uma rede Virtual do Azure (VNet). Uma VNet do Azure pode ser substituída por sua própria rede privada virtual. Para tal, mapear os endereços IP apropriados nas sub-redes.

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Utilize o HCL (HashiCorp Language) para implementar uma VNet no local na topologia hub-and-spoke
> * Utilize o Terraform para criar recursos de aplicação de rede hub
> * Utilize o Terraform para criar a máquina virtual no local
> * Utilize o Terraform para criar o gateway de rede privada virtual no local

## <a name="prerequisites"></a>Pré-requisitos

1. [Criar um hub- and -spoke topologia de rede híbrida com o Terraform no Azure](./terraform-hub-spoke-introduction.md).

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

Para simular uma rede no local, crie uma rede virtual do Azure. A demonstração VNet ocupa o lugar de uma rede privada no local real. Para fazer o mesmo com a sua rede no local existentes, mapear os endereços IP apropriados nas sub-redes.

1. Navegue para o [portal do Azure](http://portal.azure.com).

1. Abra o [Azure Cloud Shell](/azure/cloud-shell/overview). Se ainda não tiver selecionado um ambiente, selecione **Bash** como o seu ambiente.

    ![Comando do Cloud Shell](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Mude para o diretório `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Mude para o diretório novo:

    ```bash
    cd hub-spoke
    ```

## <a name="declare-the-on-premises-vnet"></a>Declarar a VNet no local

Crie o ficheiro de configuração do Terraform que declara uma VNet no local.

1. No Cloud Shell, abra um novo ficheiro designado `on-prem.tf`.

    ```bash
    code on-prem.tf
    ```

1. Cole o seguinte código no editor:

    ```JSON
    locals {
      onprem-location       = "SouthCentralUS"
      onprem-resource-group = "onprem-vnet-rg"
      prefix-onprem         = "onprem"
    }

    resource "azurerm_resource_group" "onprem-vnet-rg" {
      name     = "${local.onprem-resource-group}"
      location = "${local.onprem-location}"
    }

    resource "azurerm_virtual_network" "onprem-vnet" {
      name                = "onprem-vnet"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
      address_space       = ["192.168.0.0/16"]

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_subnet" "onprem-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.255.224/27"
    }

    resource "azurerm_subnet" "onprem-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.onprem-vnet.name}"
      address_prefix       = "192.168.1.128/25"
    }

    resource "azurerm_public_ip" "onprem-pip" {
        name                         = "${local.prefix-onprem}-pip"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
        allocation_method   = "Dynamic"

        tags {
            environment = "${local.prefix-onprem}"
        }
    }

    resource "azurerm_network_interface" "onprem-nic" {
      name                 = "${local.prefix-onprem}-nic"
      location             = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.onprem-vnet-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-onprem}"
        subnet_id                     = "${azurerm_subnet.onprem-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
        public_ip_address_id          = "${azurerm_public_ip.onprem-pip.id}"
      }
    }

    # Create Network Security Group and rule
    resource "azurerm_network_security_group" "onprem-nsg" {
        name                = "${local.prefix-onprem}-nsg"
        location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
        resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

        security_rule {
            name                       = "SSH"
            priority                   = 1001
            direction                  = "Inbound"
            access                     = "Allow"
            protocol                   = "Tcp"
            source_port_range          = "*"
            destination_port_range     = "22"
          source_address_prefix      = "*"
            destination_address_prefix = "*"
        }

        tags {
            environment = "onprem"
        }
    }

    resource "azurerm_subnet_network_security_group_association" "mgmt-nsg-association" {
      subnet_id                 = "${azurerm_subnet.onprem-mgmt.id}"
      network_security_group_id = "${azurerm_network_security_group.onprem-nsg.id}"
    }

    resource "azurerm_virtual_machine" "onprem-vm" {
      name                  = "${local.prefix-onprem}-vm"
      location              = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.onprem-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.onprem-nic.id}"]
      vm_size               = "${var.vmsize}"

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
        computer_name  = "${local.prefix-onprem}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-onprem}"
      }
    }

    resource "azurerm_public_ip" "onprem-vpn-gateway1-pip" {
      name                = "${local.prefix-onprem}-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      allocation_method = "Dynamic"
    }

    resource "azurerm_virtual_network_gateway" "onprem-vpn-gateway" {
      name                = "onprem-vpn-gateway1"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"

      type     = "Vpn"
      vpn_type = "RouteBased"

      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"

      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.onprem-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.onprem-gateway-subnet.id}"
      }
      depends_on = ["azurerm_public_ip.onprem-vpn-gateway1-pip"]

    }
    ```

1. Guarde o ficheiro e saia do editor.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Criar uma rede virtual do concentrador com o Terraform no Azure](./terraform-hub-spoke-hub-network.md)