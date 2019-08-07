---
title: incluir ficheiro
description: incluir ficheiro
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: a58e408feadd10e6dbc9d6878b82a4d045918ea6
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68781484"
---
## <a name="access-the-virtual-machine"></a>Acessar a máquina virtual

As etapas a seguir usam o CLI do Azure no Azure Cloud Shell. Se preferir, você pode [instalar o CLI do Azure](/cli/azure/install-azure-cli) em seu computador de desenvolvimento e executar os comandos localmente.

As etapas a seguir mostram como configurar a máquina virtual do Azure para permitir o acesso **SSH** . As etapas mostradas pressupõem que o nome escolhido para o Solution Accelerator é **contoso-Simulation** --Substitua esse valor pelo nome da sua implantação:

1. Liste o conteúdo do grupo de recursos que contém os recursos do Solution Accelerator:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Anote o nome da máquina virtual, o endereço IP público e o grupo de segurança de rede-você precisará desses valores posteriormente.

1. Atualize o grupo de segurança de rede para permitir o acesso SSH. O comando a seguir pressupõe que o nome do grupo de segurança de rede é **contoso-Simulation-NSG** --Substitua esse valor pelo nome do seu grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Habilite somente o acesso SSH durante o teste e o desenvolvimento. Se você habilitar o SSH, [deverá desabilitá-lo novamente](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines)assim que possível.

1. Atualize a senha da conta **azureuser** na máquina virtual para uma senha que você conhece. Escolha sua própria senha ao executar o seguinte comando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Localize o endereço IP público de sua máquina virtual. O comando a seguir pressupõe que o nome da máquina virtual seja **VM-vikxv** --Substitua esse valor pelo nome da máquina virtual anotada anteriormente:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Anote o endereço IP público da sua máquina virtual.
