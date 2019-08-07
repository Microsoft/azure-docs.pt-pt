---
title: Guia de início rápido do Azure Cloud Shell | Microsoft Docs
description: Início rápido para Azure Cloud Shell
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 03/12/2018
ms.author: damaerte
ms.openlocfilehash: 8151013f263c6cf2f90e89fa1c3b0b3025f2ea38
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/02/2019
ms.locfileid: "68741994"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Início rápido para Bash no Azure Cloud Shell

Este documento detalha como usar o bash em Azure Cloud Shell no [portal do Azure](https://ms.portal.azure.com/).

> [!NOTE]
> Um [PowerShell no guia de início rápido do Azure cloud Shell](quickstart-powershell.md) também está disponível.

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell
1. Inicie o **Cloud Shell** na navegação superior do portal do Azure. <br>
![](media/quickstart/shell-icon.png)

2. Selecione uma assinatura para criar uma conta de armazenamento e Microsoft Azure compartilhamento de arquivos.
3. Selecione "criar armazenamento"

> [!TIP]
> Você é autenticado automaticamente para CLI do Azure em cada sessão.

### <a name="select-the-bash-environment"></a>Selecionar o ambiente bash
Verifique se a lista suspensa ambiente do lado esquerdo da janela do shell diz `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Definir sua assinatura
1. Listar assinaturas às quais você tem acesso.
   ```azurecli-interactive
   az account list
   ```

2. Defina sua assinatura preferida: <br>
```azurecli-interactive
az account set --subscription 'my-subscription-name'
```

> [!TIP]
> Sua assinatura será lembrada para sessões futuras usando `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos em Westus chamado "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Crie uma VM do Ubuntu em seu novo grupo de recursos. O CLI do Azure criará chaves SSH e configurará a VM com elas. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> O `--generate-ssh-keys` uso instrui CLI do Azure a criar e configurar chaves públicas e privadas em sua VM e `$Home` diretório. Por padrão, as chaves são colocadas em `/home/<user>/.ssh/id_rsa` Cloud Shell `/home/<user>/.ssh/id_rsa.pub`em e. Sua `.ssh` pasta é mantida na imagem de 5 GB do compartilhamento de arquivos anexado usada para persistir `$Home`.

Seu nome de usuário nessa VM será seu nome de usuário usado em Cloud ShellUser@Azure:($).

### <a name="ssh-into-your-linux-vm"></a>SSH em sua VM Linux
1. Pesquise o nome da VM na barra de pesquisa portal do Azure.
2. Clique em "conectar" para obter o nome da VM e o endereço IP público. <br>
   ![](media/quickstart/sshcmd-copy.png)

3. SSH em sua VM com o `ssh` cmd.
   ```
   ssh username@ipaddress
   ```

Após estabelecer a conexão SSH, você deverá ver o prompt de boas-vindas do Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpeza 
1. Saia da sessão SSH.
   ```azurecli-interactive
   exit
   ```

2. Exclua seu grupo de recursos e todos os recursos dentro dele.
   ```azurecli-interactive
   az group delete -n MyRG
   ```

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre a persistência de arquivos para Bash no Cloud Shell](persisting-shell-storage.md) <br>
[Saiba mais sobre o CLI do Azure](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre o armazenamento de arquivos do Azure](../storage/files/storage-files-introduction.md) <br>
