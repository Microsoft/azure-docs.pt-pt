---
title: Guia de introdução do Azure Cloud Shell de bash | Documentos da Microsoft
description: Início rápido para o Bash no Cloud Shell
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
ms.openlocfilehash: 56a4645c5f86e9f95e82533b7f5f81b7bca51e7b
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245323"
---
# <a name="quickstart-for-bash-in-azure-cloud-shell"></a>Início rápido para o Bash no Azure Cloud Shell

Este documento fornece detalhes sobre como utilizar o Bash no Azure Cloud Shell no [portal do Azure](https://ms.portal.azure.com/).

> [!NOTE]
> R [PowerShell no Azure Cloud Shell](quickstart-powershell.md) início rápido também está disponível.

## <a name="start-cloud-shell"></a>Iniciar Cloud Shell
1. Inicie **Cloud Shell** no painel de navegação superior do portal do Azure. <br>
![](media/quickstart/shell-icon.png)

2. Selecione uma subscrição para criar uma conta de armazenamento e partilha de ficheiros do Microsoft Azure.
3. Selecione "Criar armazenamento"

> [!TIP]
> São autenticados automaticamente para a CLI do Azure em cada sessão.

### <a name="select-the-bash-environment"></a>Selecione o ambiente de Bash
Verifique se o ambiente de baixo no lado esquerdo da janela do shell diz `Bash`. <br>
![](media/quickstart/env-selector.png)

### <a name="set-your-subscription"></a>Definir a subscrição
1. Subscrições de lista que tem acesso.
```azurecli-interactive
az account list
```

2. Defina a sua subscrição preferencial: <br>
```azurecli-interactive
az account set --subscription my-subscription-name`
```

> [!TIP]
> A sua subscrição vai ser memorizada para futuras sessões usando `/home/<user>/.azure/azureProfile.json`.

### <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um novo grupo de recursos no WestUS com o nome "MyRG".
```azurecli-interactive
az group create --location westus --name MyRG
```

### <a name="create-a-linux-vm"></a>Criar uma VM do Linux
Crie uma VM do Ubuntu no seu novo grupo de recursos. A CLI do Azure irá criar chaves SSH e configurar uma VM com eles. <br>

```azurecli-interactive
az vm create -n myVM -g MyRG --image UbuntuLTS --generate-ssh-keys
```

> [!NOTE]
> Usando `--generate-ssh-keys` instrui a CLI do Azure para criar e configurar as chaves públicas e privadas na VM e `$Home` diretório. Por predefinição, as chaves são colocadas no Cloud Shell no `/home/<user>/.ssh/id_rsa` e `/home/<user>/.ssh/id_rsa.pub`. Sua `.ssh` pasta é mantida na imagem de 5 GB de seu compartilhamento de arquivo anexo usada para manter `$Home`.

O nome de utilizador nesta VM é o nome de utilizador utilizado no Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>SSH à VM do Linux
1. Procure o nome da VM na barra de pesquisa de portal do Azure.
2. Clique em "Ligar" para obter o nome da VM e o endereço IP público. <br>
![](media/quickstart/sshcmd-copy.png)

3. SSH à VM com o `ssh` cmd.
```
ssh username@ipaddress
```

Após estabelecer a ligação de SSH, deverá ver a linha de comandos de boas-vindas da Ubuntu. <br>
![](media/quickstart/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Limpeza 
1. Saída do ssh sessão.
```azurecli-interactive
exit
```

2. Elimine o grupo de recursos e todos os recursos dentro da mesma.
```azurecli-interactive
az group delete -n MyRG
```

## <a name="next-steps"></a>Passos Seguintes
[Saiba mais sobre ficheiros persistentes para Bash no Cloud Shell](persisting-shell-storage.md) <br>
[Saiba mais sobre a CLI do Azure](https://docs.microsoft.com/cli/azure/) <br>
[Saiba mais sobre armazenamento de ficheiros do Azure](../storage/files/storage-files-introduction.md) <br>
