---
title: Quickstart - Criar um VM windows utilizando o Azure CLI
description: Neste arranque rápido, aprende-se a usar o CLI Azure para criar uma máquina virtual Windows
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 07/02/2019
ms.author: cynthn
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 769b42133fb894c916ecaa3c42dd7de85206c765
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556252"
---
# <a name="quickstart-create-a-windows-virtual-machine-with-the-azure-cli"></a>Guia de Início Rápido: Criar uma máquina virtual do Windows com a CLI do Azure

A CLI do Azure é utilizada para criar e gerir recursos do Azure a partir da linha de comandos ou em scripts. Este guia de início rápido mostra como utilizar a CLI do Azure para implementar uma máquina virtual (VM) no Azure que executa o Windows Server 2016. Para ver a VM em ação, estabeleça o RDP para a VM e instale o servidor Web IIS.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. 

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copy** para copiar os blocos de código, cole-o na Cloud Shell e prima **Enter** para executá-lo.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-virtual-machine"></a>Criar a máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm). O exemplo seguinte cria uma VM com o nome *myVM*. Este exemplo utiliza *azureuser* para um nome de utilizador administrativo. 

Terá de fornecer uma palavra-passe que satisfaça os requisitos de [senha para VMs Azure](./faq.md#what-are-the-password-requirements-when-creating-a-vm
). Utilizando o exemplo abaixo, será solicitado que introduza uma palavra-passe na linha de comando. Também pode adicionar o `--admin-password` parâmetro com um valor para a sua palavra-passe. O nome de utilizador e a palavra-passe serão usados mais tarde, quando se ligar ao VM.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser 
```

São necessários alguns minutos para criar a VM e os recursos de suporte. O seguinte resultado de exemplo mostra que a operação de criação da VM foi concluída com êxito.

```output
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

Tenha em atenção o seu próprio `publicIpAddress` na saída da VM. Este endereço é utilizado para aceder à VM nos próximos passos.

## <a name="open-port-80-for-web-traffic"></a>Abrir a porta 80 para o tráfego da Web

Por predefinição, apenas as ligações RDP são abertas ao criar uma VM do Windows no Azure. Utilize [az vm open-port](/cli/azure/vm) para abrir a porta TCP 80 para utilização com o servidor Web do IIS:

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroup --name myVM
```

## <a name="connect-to-virtual-machine"></a>Conectar à máquina virtual

Utilize o seguinte comando para criar uma sessão de ambiente de trabalho remoto a partir do computador local. Substitua o endereço IP pelo endereço IP público da VM. Quando lhe for pedido, introduza as credenciais utilizadas quando a VM foi criada:

```powershell
mstsc /v:publicIpAddress
```

## <a name="install-web-server"></a>Instalar o servidor Web

Para ver a VM em ação, instale o servidor Web do IIS. Abra uma janela do PowerShell na VM e execute o comando seguinte:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Quando terminar, feche a ligação RDP à VM.

## <a name="view-the-web-server-in-action"></a>Ver o servidor Web em ação

Com o IIS instalado e a porta 80 aberta na VM a partir da Internet, utilize um browser à sua escolha para ver a página de boas-vindas do IIS predefinida. Utilize o endereço IP público da VM que obteve no passo anterior. O seguinte exemplo mostra o site do IIS predefinido:

![Site predefinido do IIS](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, pode utilizar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos, a VM e todos os recursos relacionados:

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste início rápido, implementou uma máquina virtual simples, abriu uma porta de rede para o tráfego Web e instalou um servidor Web básico. Para saber mais sobre as máquinas virtuais do Azure, continue com o tutorial para VMs do Windows.

> [!div class="nextstepaction"]
> [Tutoriais de máquinas virtuais do Windows do Azure](./tutorial-manage-vm.md)
