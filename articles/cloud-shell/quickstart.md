---
title: Azure Cloud Shell Quickstart - Bash
description: Saiba como usar a linha de comando Bash no seu navegador com a Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 58d795a5aee79e4149864a79a923ce34950b31d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79458074"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Quickstart para Bash em Azure Cloud Shell

Este documento detalha como usar bash em Azure Cloud Shell no [portal Azure](https://ms.portal.azure.com/).

> [!NOTE]
> Também está disponível um [PowerShell em Azure Cloud Shell](quickstart-powershell.md) Quickstart.

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell
1. Lance **cloud shell** a partir da navegação superior do portal Azure. <br>
![](media/quickstart/shell-icon.png)

2. Selecione uma subscrição para criar uma conta de armazenamento e partilhar o Microsoft Azure Files.
3. Selecione "Criar armazenamento"

> [!TIP]
> É automaticamente autenticado para o Azure CLI em todas as sessões.

### <a name="select-the-bash-environment"></a>Selecione o ambiente Bash
Verifique se o ambiente cai do lado esquerdo da `Bash`janela da concha diz . <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Detete a sua subscrição
1. Lista as assinaturas a que tem acesso.
   ```azurecli-interactive
   az account list
   ```

2. Detete a sua subscrição preferida: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> A sua subscrição será `/home/<user>/.azure/azureProfile.json`lembrada para futuras sessões utilizando .

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos na WestUS chamado "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Crie um Ubuntu VM no seu novo grupo de recursos. O Azure CLI criará chaves SSH e configurará o VM com elas. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> A `--generate-ssh-keys` utilização instrui o Azure CLI para criar e `$Home` configurar chaves públicas e privadas no seu VM e diretório. Por predefinição, as `/home/<user>/.ssh/id_rsa` teclas são colocadas na Cloud Shell a e `/home/<user>/.ssh/id_rsa.pub`. A `.ssh` sua pasta é persistente na imagem de 5 GB `$Home`da sua partilha de ficheiros anexada utilizada para persistir .

O seu nome de utilizador neste VM será oUser@Azure:seu nome de utilizador usado na Cloud Shell ($).

### <a name="ssh-into-your-linux-vm"></a>SSH no seu Linux VM
1. Procure o seu nome VM na barra de pesquisa do portal Azure.
2. Clique em "Connect" para obter o nome VM e endereço IP público. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH no seu VM com o `ssh` cmd.
   ```
   ssh username@ipaddress
   ```

Ao estabelecer a ligação SSH, deve ver o pedido de boas-vindas de Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpeza 
1. Saia da sua sessão de ssh.
   ```
   exit
   ```

2. Elimine o seu grupo de recursos e quaisquer recursos dentro dele.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre os ficheiros persistindo para Bash na Cloud Shell](persisting-shell-storage.md) <br>
[Conheça o Azure CLI](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o armazenamento de Ficheiros Azure](../storage/files/storage-files-introduction.md) <br>
