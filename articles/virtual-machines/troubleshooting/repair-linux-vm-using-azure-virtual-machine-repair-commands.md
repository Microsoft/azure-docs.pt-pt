---
title: Repare um VM Linux utilizando os comandos de reparação da Máquina Virtual Azure [ Microsoft Docs
description: Este artigo detalha como utilizar comandos de reparação da Máquina Virtual Azure para ligar o disco a outro VM Linux para corrigir quaisquer erros e, em seguida, reconstruir o seu VM original.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: da40deb4df55a63f5fecc380500a507b374ca63d
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711147"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Linux com os comandos de reparação da Máquina Virtual do Azure

Se a sua máquina virtual Linux (VM) em Azure encontrar um erro de arranque ou disco, poderá ter de efetuar mitigação no próprio disco. Um exemplo comum seria uma atualização de aplicação falhada que impede que o VM seja capaz de arrancar com sucesso. Este artigo detalha como utilizar comandos de reparação da Máquina Virtual Azure para ligar o disco a outro VM Linux para corrigir quaisquer erros e, em seguida, reconstruir o seu VM original.

> [!IMPORTANT]
> Os scripts deste artigo aplicam-se apenas aos VMs que utilizam o Gestor de [Recursos Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Visão geral do processo de reparação

Agora pode utilizar comandos de reparação da Máquina Virtual Azure para alterar o disco OS para um VM, e já não precisa de eliminar e recriar o VM.

Siga estes passos para resolver problemas com a questão da VM:

1. Iniciar o Azure Cloud Shell
2. Executar az extensão adicionar/atualizar
3. Executar az vm criar
4. Realizar passos de mitigação
5. Executar restauro de reparação az vm

Para obter documentação e instruções adicionais, consulte [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo de processo de reparação

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Inclui ferramentas azure comuns pré-instaladas e configuradas para usar com a sua conta.

   Para abrir a Cloud Shell, selecione **Experimente a** partir do canto superior direito de um bloco de código. Você também pode abrir Cloud Shell em um separado de navegador indo para [https://shell.azure.com](https://shell.azure.com) .

   Selecione **Copiar** para copiar os blocos de código, em seguida, cole o código na Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.30 ou posterior da CLI do Azure. Executar ``az --version`` para localizar a versão. Se precisar de instalar ou atualizar o seu Azure CLI, consulte [Instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Se for a primeira vez que utilizar os `az vm repair` comandos, adicione a extensão CLI de reparação vm.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se já utilizou previamente os `az vm repair` comandos, aplique quaisquer atualizações na extensão de reparação vm.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Este comando criará uma cópia do disco OS para o VM não funcional, criará um VM de reparação num novo Grupo de Recursos e anexará a cópia do disco OS.  O VM de reparação terá o mesmo tamanho e região especificado.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Execute quaisquer passos de mitigação necessários no VM de reparação criado e, em seguida, proceda ao passo 5.

5. Execute `az vm repair restore`. Este comando trocará o disco OS reparado com o disco osso original do VM.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e ativar diagnósticos de arranque

O exemplo seguinte permite a extensão de diagnóstico no VM nomeado ``myVMDeployed`` no grupo de recursos ``myResourceGroup`` denominado:

CLI do Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Próximos passos

* Se tiver problemas de ligação ao seu VM, consulte [ligações RDP de Troubleshoot a uma Máquina Virtual Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Para problemas com o acesso a aplicações em execução no seu VM, consulte problemas de conectividade de [aplicações troubleshoot em máquinas virtuais em Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Para mais informações sobre a utilização do Gestor de Recursos, consulte a [visão geral do Gestor de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
