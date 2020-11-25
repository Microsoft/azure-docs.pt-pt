---
title: incluir ficheiro
description: incluir ficheiro
services: iot-accelerators
author: dominicbetts
ms.service: iot-accelerators
ms.topic: include
ms.date: 08/16/2018
ms.author: dobett
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: e3eed0e7378f513e45e0eb9ac719a92e87ec1d53
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025711"
---
## <a name="access-the-virtual-machine"></a>Aceda à máquina virtual

Os passos seguintes utilizam o Azure CLI em Azure Cloud Shell. Se preferir, pode [instalar o CLI Azure](/cli/azure/install-azure-cli) na sua máquina de desenvolvimento e executar os comandos localmente.

Os passos seguintes mostram-lhe como configurar a máquina virtual Azure para permitir o acesso ao **SSH.** Os passos mostrados assumem que o nome que escolheu para o acelerador de solução é **contoso-simulação** -- substitua este valor pelo nome da sua implantação:

1. Listar os conteúdos do grupo de recursos que contém os recursos aceleradores de solução:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Tome nota do nome da máquina virtual, do endereço IP público e do grupo de segurança da rede - precisa destes valores mais tarde.

1. Atualize o grupo de segurança da rede para permitir o acesso ao SSH. O seguinte comando pressupõe que o nome do grupo de segurança da rede é **contoso-simulação-nsg** -- substitua este valor pelo nome do seu grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Apenas permitir o acesso ao SSH durante o teste e o desenvolvimento. Se ativar o SSH, [deve desativá-lo o mais rapidamente possível](../articles/security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Atualize a palavra-passe da conta **azureuser** na máquina virtual para uma palavra-passe que conhece. Escolha a sua própria senha quando executar o seguinte comando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Encontre o endereço IP público da sua máquina virtual. O seguinte comando pressupõe que o nome da máquina virtual é **vm-vikxv** -- substitua este valor pelo nome da máquina virtual que fez uma nota de anteriormente:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Tome nota do endereço IP público da sua máquina virtual.