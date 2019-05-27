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
ms.openlocfilehash: db1af4f046bd8849fddee299e949d6edbdaae86a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143408"
---
## <a name="access-the-virtual-machine"></a>Aceder à máquina virtual

Os passos seguintes utilizam o `az` comando no Azure Cloud Shell. Se preferir, pode [instalar o Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) sobre o desenvolvimento de seu computador e execute os comandos localmente.

Os passos seguintes mostram como configurar a sua máquina virtual do Azure para permitir **SSH** acesso. O nome que escolheu para o solution accelerator é partem do princípio das etapas mostradas **contoso-simulação** – substitua este valor com o nome da sua implementação:

1. Liste o conteúdo do grupo de recursos que contém os recursos de acelerador de solução:

    ```azurecli-interactive
    az resource list -g contoso-simulation -o table
    ```

    Tome nota do nome da máquina virtual, o endereço IP público e o grupo de segurança de rede - precisa destes valores mais tarde.

1. Atualize o grupo de segurança de rede para permitir o acesso SSH. O comando seguinte assume o nome do grupo de segurança de rede é **contoso-simulação-nsg** – substitua este valor com o nome do seu grupo de segurança de rede:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name contoso-simulation-nsg -g contoso-simulation --access Allow -o table
    ```

    Ative o acesso SSH apenas durante teste e desenvolvimento. Se ativar o SSH, [deve desabilitá-la novamente logo que possível](https://docs.microsoft.com/azure/security/azure-security-network-security-best-practices#disable-rdpssh-access-to-virtual-machines).

1. Atualizar a palavra-passe para o **azureuser** conta na máquina virtual para uma palavra-passe, sabe. Escolha sua própria palavra-passe ao executar o seguinte comando:

    ```azurecli-interactive
    az vm user update --name vm-vikxv --username azureuser --password YOURSECRETPASSWORD  -g contoso-simulation
    ```

1. Encontre o endereço IP público da sua máquina virtual. O comando seguinte assume o nome da máquina virtual é **vm vikxv** – substitua este valor com o nome da máquina virtual que anotou anteriormente:

    ```azurecli-interactive
    az vm list-ip-addresses --name vm-vikxv -g contoso-simulation -o table
    ```

    Tome nota do endereço IP público da sua máquina virtual.
