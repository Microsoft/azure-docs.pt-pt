---
title: Criar um dispositivo de rede virtual de Hub com o Terraform no Azure
description: O tutorial implementa a criação de VNet de Hub que atua como um ponto de conexão comum entre todas as outras redes
services: terraform
ms.service: azure
keywords: Terraform, Hub e spoke, redes, redes híbridas, DevOps, máquina virtual, Azure, emparelhamento de VNet, hub-spoke, Hub.
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 09/20/2019
ms.openlocfilehash: 1fae21e9a60f533533607e74609853ef68348daf
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71173410"
---
# <a name="tutorial-create-a-hub-virtual-network-appliance-with-terraform-in-azure"></a>Tutorial: Criar um dispositivo de rede virtual de Hub com o Terraform no Azure

Um **dispositivo VPN** é um dispositivo que fornece conectividade externa a uma rede local. O dispositivo VPN pode ser um dispositivo de hardware ou uma solução de software. Um exemplo de uma solução de software é o RRAS (serviço de roteamento e acesso remoto) no Windows Server 2012. Para obter mais informações sobre os dispositivos VPN, consulte [sobre dispositivos VPN para conexões de gateway de VPN site a site](/azure/vpn-gateway/vpn-gateway-about-vpn-devices).

O Azure dá suporte a uma ampla variedade de soluções de virtualização de rede a partir da qual selecionar. Para este tutorial, é usada uma imagem do Ubuntu. Para saber mais sobre a ampla variedade de soluções de dispositivo com suporte no Azure, consulte os [dispositivos de rede Home Page](https://azure.microsoft.com/solutions/network-appliances/).

Este tutorial abrange as seguintes tarefas:

> [!div class="checklist"]
> * Use a HCL (linguagem HashiCorp) para implementar a VNet do Hub na topologia hub-spoke
> * Use Terraform para criar uma máquina virtual de rede de Hub que atue como dispositivo
> * Usar Terraform para habilitar rotas usando extensões CustomScript
> * Usar Terraform para criar tabelas de rotas de Hub e de gateway de spoke

## <a name="prerequisites"></a>Pré-requisitos

1. [Crie uma topologia de rede híbrida de Hub e spoke com Terraform no Azure](./terraform-hub-spoke-introduction.md).
1. [Crie uma rede virtual local com o Terraform no Azure](./terraform-hub-spoke-on-prem.md).
1. [Crie uma rede virtual de Hub com Terraform no Azure](./terraform-hub-spoke-hub-network.md).

## <a name="create-the-directory-structure"></a>Criar a estrutura de diretórios

1. Navegue para o [portal do Azure](https://portal.azure.com).

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

## <a name="declare-the-hub-network-appliance"></a>Declarar o dispositivo de rede de Hub

Crie o arquivo de configuração Terraform que declara a rede virtual local.

1. Em Cloud Shell, crie um novo arquivo chamado `hub-nva.tf`.

    ```bash
    code hub-nva.tf
    ```

1. Cole o seguinte código no editor:
    
    ```hcl
    locals {
      prefix-hub-nva         = "hub-nva"
      hub-nva-location       = "CentralUS"
      hub-nva-resource-group = "hub-nva-rg"
    }

    resource "azurerm_resource_group" "hub-nva-rg" {
      name     = "${local.prefix-hub-nva}-rg"
      location = "${local.hub-nva-location}"

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_network_interface" "hub-nva-nic" {
      name                 = "${local.prefix-hub-nva}-nic"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      enable_ip_forwarding = true

      ip_configuration {
        name                          = "${local.prefix-hub-nva}"
        subnet_id                     = "${azurerm_subnet.hub-dmz.id}"
        private_ip_address_allocation = "Static"
        private_ip_address            = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine" "hub-nva-vm" {
      name                  = "${local.prefix-hub-nva}-vm"
      location              = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-nva-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nva-nic.id}"]
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
        computer_name  = "${local.prefix-hub-nva}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }

      os_profile_linux_config {
        disable_password_authentication = false
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_virtual_machine_extension" "enable-routes" {
      name                 = "enable-iptables-routes"
      location             = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-nva-rg.name}"
      virtual_machine_name = "${azurerm_virtual_machine.hub-nva-vm.name}"
      publisher            = "Microsoft.Azure.Extensions"
      type                 = "CustomScript"
      type_handler_version = "2.0"

      settings = <<SETTINGS
        {
            "fileUris": [
            "https://raw.githubusercontent.com/mspnp/reference-architectures/master/scripts/linux/enable-ip-forwarding.sh"
            ],
            "commandToExecute": "bash enable-ip-forwarding.sh"
        }
    SETTINGS

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_route_table" "hub-gateway-rt" {
      name                          = "hub-gateway-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name           = "toHub"
        address_prefix = "10.0.0.0/16"
        next_hop_type  = "VnetLocal"
      }

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "hub-gateway-rt-hub-vnet-gateway-subnet" {
      subnet_id      = "${azurerm_subnet.hub-gateway-subnet.id}"
      route_table_id = "${azurerm_route_table.hub-gateway-rt.id}"
      depends_on = ["azurerm_subnet.hub-gateway-subnet"]
    }

    resource "azurerm_route_table" "spoke1-rt" {
      name                          = "spoke1-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke2"
        address_prefix         = "10.2.0.0/16"
        next_hop_type          = "VirtualAppliance"
        next_hop_in_ip_address = "10.0.0.36"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke1-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke1-rt-spoke1-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke1-workload.id}"
      route_table_id = "${azurerm_route_table.spoke1-rt.id}"
      depends_on = ["azurerm_subnet.spoke1-workload"]
    }

    resource "azurerm_route_table" "spoke2-rt" {
      name                          = "spoke2-rt"
      location                      = "${azurerm_resource_group.hub-nva-rg.location}"
      resource_group_name           = "${azurerm_resource_group.hub-nva-rg.name}"
      disable_bgp_route_propagation = false

      route {
        name                   = "toSpoke1"
        address_prefix         = "10.1.0.0/16"
        next_hop_in_ip_address = "10.0.0.36"
        next_hop_type          = "VirtualAppliance"
      }

      route {
        name           = "default"
        address_prefix = "0.0.0.0/0"
        next_hop_type  = "vnetlocal"
      }

      tags {
        environment = "${local.prefix-hub-nva}"
      }
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-mgmt" {
      subnet_id      = "${azurerm_subnet.spoke2-mgmt.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-mgmt"]
    }

    resource "azurerm_subnet_route_table_association" "spoke2-rt-spoke2-vnet-workload" {
      subnet_id      = "${azurerm_subnet.spoke2-workload.id}"
      route_table_id = "${azurerm_route_table.spoke2-rt.id}"
      depends_on = ["azurerm_subnet.spoke2-workload"]
    }

    ```

1. Salve o arquivo e saia do editor.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Criar redes virtuais spoke com Terraform no Azure](./terraform-hub-spoke-spoke-network.md)