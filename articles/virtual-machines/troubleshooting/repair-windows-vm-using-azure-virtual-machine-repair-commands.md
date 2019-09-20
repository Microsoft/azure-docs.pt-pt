---
title: Reparar uma VM do Windows usando os comandos de reparo da máquina virtual do Azure | Microsoft Docs
description: Este artigo fornece detalhes sobre como usar os comandos de reparo da VM do Azure para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, recriar sua VM original.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: d942f3861eb2fcc4e096248d495b2db2d8119ea1
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/19/2019
ms.locfileid: "71132107"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Windows usando os comandos de reparo da máquina virtual do Azure

Se sua VM (máquina virtual) do Windows no Azure encontrar um erro de disco ou de inicialização, talvez seja necessário executar a mitigação no próprio disco. Um exemplo comum seria uma atualização de aplicativo com falha que impede que a VM seja capaz de inicializar com êxito. Este artigo fornece detalhes sobre como usar os comandos de reparo da VM do Azure para conectar o disco a outra VM do Windows para corrigir erros e, em seguida, recriar sua VM original.

> [!IMPORTANT]
> Os scripts neste artigo se aplicam somente às VMs que usam [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Visão geral do processo de reparo

Agora você pode usar os comandos de reparo da VM do Azure para alterar o disco do sistema operacional de uma VM e não precisa mais excluir e recriar a VM.

Siga estas etapas para solucionar o problema da VM:

1. Iniciar o Azure Cloud Shell
2. Execute adicionar/atualizar extensão AZ.
3. Execute AZ VM Repair Create.
4. Execute AZ VM Repair Run.
5. Execute AZ VM Repair Restore.

Para obter mais informações e documentação, consulte [AZ VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo de processo de reparo

> [!NOTE]
> * A conectividade de saída da VM (porta 443) é necessária para que o script seja executado.
> * Somente um script pode ser executado de cada vez.
> * Um script em execução não pode ser cancelado.
> * O tempo máximo que um script pode executar é de 90 minutos, após o qual atingirá o tempo limite.

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Ele inclui ferramentas comuns do Azure pré-instalados e configuradas para usar com sua conta.

   Para abrir o Cloud Shell, selecione **Experimente** no canto superior direito de um bloco de código. Você também pode abrir Cloud Shell em uma guia separada do navegador visitando [https://shell.azure.com](https://shell.azure.com).

   Selecione **copiar** para copiar os blocos de código e, em seguida, Cole o código no Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.30 ou posterior da CLI do Azure. Executar ``az --version`` para localizar a versão. Se você precisar instalar ou atualizar seu CLI do Azure, consulte [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Se esta for a primeira vez que você usou os `az vm repair` comandos, adicione a extensão da CLI de reparo de VM.

   ```azurepowershell-interactive
   az extension add -n vm-repair
   ```

   Se você tiver usado os `az vm repair` comandos anteriormente, aplique todas as atualizações à extensão de reparo de VM.

   ```azurepowershell-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Esse comando criará uma cópia do disco do sistema operacional para a VM não funcional, criará uma VM de reparo e anexará o disco.

   ```azurepowershell-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Execute `az vm repair run`. Esse comando executará o script de reparo especificado no disco anexado por meio da VM de reparo.

   ```azurepowershell-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Execute `az vm repair restore`. Esse comando alternará o disco do sistema operacional reparado com o disco do sistema operacional original da VM.

   ```azurepowershell-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verificar e habilitar o diagnóstico de inicialização

O exemplo a seguir habilita a extensão de diagnóstico na VM ``myVMDeployed`` chamada no grupo de recursos ``myResourceGroup``chamado:

CLI do Azure

```azurepowershell-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Passos seguintes

* Se você estiver tendo problemas para se conectar à sua VM, consulte [solucionar problemas de conexões RDP para uma VM do Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Para problemas com o acesso a aplicativos executados em sua VM, consulte [solucionar problemas de conectividade de aplicativos em máquinas virtuais no Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Para obter mais informações sobre como usar o Gerenciador de recursos, consulte [Azure Resource Manager visão geral](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
