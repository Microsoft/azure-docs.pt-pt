---
title: Azure Cloud Shell Quickstart - Bash
description: Aprenda a usar a linha de comando Bash no seu navegador com a Azure Cloud Shell.
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 91b7c58890518559c046023bd78c9248e9840f9f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89468754"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Quickstart para Bash em Azure Cloud Shell

Este documento detalha como utilizar a Bash in Azure Cloud Shell no [portal Azure](https://ms.portal.azure.com/).

> [!NOTE]
> Um [PowerShell em Azure Cloud Shell](quickstart-powershell.md) Quickstart também está disponível.

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell
1. Lançar **Cloud Shell** a partir da navegação superior do portal Azure. <br>
![Screenshot mostrando como iniciar Azure Cloud Shell no portal Azure.](media/quickstart/shell-icon.png)

2. Selecione uma subscrição para criar uma conta de armazenamento e partilha de Ficheiros Microsoft Azure.
3. Selecione "Criar armazenamento"

> [!TIP]
> É autenticado automaticamente para O Azure CLI em todas as sessões.

### <a name="select-the-bash-environment"></a>Selecione o ambiente Bash
Verifique se o ambiente desce do lado esquerdo da janela da `Bash` concha. <br>
![Screenshot mostrando como selecionar o ambiente Bash para a Azure Cloud Shell.](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Desaça a sua subscrição
1. Listar subscrições a que tem acesso.
   ```azurecli-interactive
   az account list
   ```

2. Desaça a sua subscrição preferida:

   ```azurecli-interactive
   az account set --subscription 'my-subscription-name'
   ```

> [!TIP]
> A sua subscrição será lembrada para futuras sessões utilizando `/home/<user>/.azure/azureProfile.json` .

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Criar um novo grupo de recursos no WestUS chamado "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Crie um VM Ubuntu no seu novo grupo de recursos. O CLI Azure criará chaves SSH e configurará o VM com eles. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Utilizando `--generate-ssh-keys` instruções Azure CLI para criar e configurar chaves públicas e privadas no seu VM e `$Home` diretório. Por predefinição, as teclas são colocadas na Cloud Shell `/home/<user>/.ssh/id_rsa` e `/home/<user>/.ssh/id_rsa.pub` . A sua `.ssh` pasta é persistiu na imagem de 5 GB da sua partilha de ficheiros anexada utilizada para persistir `$Home` .

O seu nome de utilizador neste VM será o seu nome de utilizador utilizado na Cloud Shell ($ User@Azure: ).

### <a name="ssh-into-your-linux-vm"></a>SSH no seu Linux VM
1. Procure o seu nome VM na barra de pesquisa do portal Azure.
2. Clique em "Conecte-se" para obter o nome VM e o endereço IP público. <br>
   ![Screenshot mostrando como ligar a um Linux V M usando S H.](media/quickstart/sshcmd-copy.png)

3. SSH no seu VM com o `ssh` cmd.
   ```
   ssh username@ipaddress
   ```

Ao estabelecer a ligação SSH, você deve ver o pedido de boas-vindas Ubuntu. <br>
![Screenshot mostrando a inicialização ubuntu e pronta de boas-vindas depois de estabelecer uma ligação S H.](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpeza 
1. Saia da sessão de ssh.
   ```
   exit
   ```

2. Elimine o seu grupo de recursos e quaisquer recursos dentro dele.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Passos seguintes
[Saiba mais sobre os ficheiros persistentes para Bash in Cloud Shell](persisting-shell-storage.md) <br>
[Saiba mais sobre a Azure CLI](/cli/azure/) <br>
[Saiba mais sobre o armazenamento de Ficheiros Azure](../storage/files/storage-files-introduction.md) <br>