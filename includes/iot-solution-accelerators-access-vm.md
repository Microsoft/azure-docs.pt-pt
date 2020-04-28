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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68781484"
---
## <a name="access-the-virtual-machine"></a>Aceda à máquina virtual

Os seguintes passos utilizam o Azure CLI em Azure Cloud Shell. Se preferir, pode [instalar o Azure CLI](/cli/azure/install-azure-cli) na sua máquina de desenvolvimento e executar os comandos localmente.

Os seguintes passos mostram-lhe como configurar a máquina virtual Azure para permitir o acesso ao **SSH.** Os passos mostrados assumem que o nome que escolheu para o acelerador de solução é **contoso-simulação** -- substitua este valor com o nome da sua implantação:

1. Enumerar o conteúdo do grupo de recursos que contém os recursos aceleradores de solução:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Tome nota do nome da máquina virtual, do endereço IP público e do grupo de segurança da rede - precisa destes valores mais tarde.

1. Atualize o grupo de segurança da rede para permitir o acesso ao SSH. O seguinte comando pressupõe que o nome do grupo de segurança da rede é **contoso-simulação-nsg** -- substitua este valor com o nome do seu grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Só permite o acesso sSH durante o teste e desenvolvimento. Se ativar o SSH, [deve desativá-lo novamente o mais rapidamente possível](https://docs.microsoft.com/azure/security/fundamentals/network-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Atualize a palavra-passe da conta **azureuser** na máquina virtual para uma palavra-passe que você conhece. Escolha a sua própria palavra-passe quando executar o seguinte comando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Encontre o endereço IP público da sua máquina virtual. O seguinte comando pressupõe que o nome da máquina virtual é **vm-vikxv** -- substitua este valor pelo nome da máquina virtual que fez uma nota anteriormente:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Tome nota do endereço IP público da sua máquina virtual.
