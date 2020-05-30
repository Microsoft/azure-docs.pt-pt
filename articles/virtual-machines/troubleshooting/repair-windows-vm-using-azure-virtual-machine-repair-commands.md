---
title: Reparar um VM do Windows utilizando os comandos de reparação da Máquina Virtual Azure Microsoft Docs
description: Este artigo detalha como utilizar comandos de reparação Azure VM para ligar o disco a outro VM do Windows para corrigir eventuais erros e, em seguida, reconstruir o seu VM original.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: b754c9e02567939569bf2ef59359dbb2614a6647
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219899"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Windows com os comandos de reparação da Máquina Virtual do Azure

Se a sua máquina virtual Windows (VM) em Azure encontrar um erro de arranque ou disco, poderá ter de efetuar uma mitigação no próprio disco. Um exemplo comum seria uma atualização de aplicação falhada que impede o VM de ser capaz de arrancar com sucesso. Este artigo detalha como utilizar comandos de reparação Azure VM para ligar o disco a outro VM do Windows para corrigir eventuais erros e, em seguida, reconstruir o seu VM original.

> [!IMPORTANT]
> Os scripts deste artigo aplicam-se apenas aos VMs que utilizam [o Gestor de Recursos Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="repair-process-overview"></a>Visão geral do processo de reparação

Agora pode utilizar comandos de reparação Azure VM para alterar o disco DE para um VM, e já não precisa de eliminar e recriar o VM.

Siga estes passos para resolver problemas na questão dos VM:

1. Iniciar o Azure Cloud Shell
2. Executar az adicionar/atualização de extensão az.
3. Executar az vm reparo criar.
4. Executar a az vm reparos.
5. Executar az vm reparação restauração.

Para obter documentação e instruções adicionais, consulte [a reparação az vm](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo do processo de reparação

> [!NOTE]
> * A conectividade de saída do VM (porta 443) é necessária para que o script seja executado.
> * Só um guião pode ser executado de cada vez.
> * Um roteiro de execução não pode ser cancelado.
> * O tempo máximo que um script pode executar é de 90 minutos, após o qual irá esgotar-se.

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Inclui ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta.

   Para abrir a Cloud Shell, selecione **Experimente-a** a partir do canto superior direito de um bloco de código. Também pode abrir o Cloud Shell num separador de navegador [https://shell.azure.com](https://shell.azure.com) visitando.

   Selecione **Copy** para copiar os blocos de código, em seguida, cole o código na Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.30 ou posterior da CLI do Azure. Executar ``az --version`` para localizar a versão. Se necessitar de instalar ou atualizar o seu Azure CLI, consulte [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Se esta for a primeira vez que utiliza os `az vm repair` comandos, adicione a extensão CLI de reparação de VM.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se já utilizou previamente os `az vm repair` comandos, aplique quaisquer atualizações na extensão de reparação de VM.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Este comando criará uma cópia do disco DE para o VM não funcional, criará um VM de reparação num novo Grupo de Recursos e anexará a cópia do disco OS.  A VM de reparação terá o mesmo tamanho e região que o VM não funcional especificado. O grupo de recursos e o nome VM utilizados em todas as etapas serão para o VM não funcional.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Execute `az vm repair run`. Este comando executará o script de reparação especificado no disco anexo através do VM de reparação. Se o guia de resolução de problemas que está a utilizar estiver a utilizar, por favor, utilize-o aqui, caso contrário poderá utilizar `az vm repair list-scripts` para ver os scripts de reparação disponíveis. O grupo de recursos e o nome VM utilizados aqui são para o VM não funcional utilizado no passo 3.

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id win-hello-world --verbose
   ```

5. Execute `az vm repair restore`. Este comando trocará o disco de oss reparado com o disco operativo original do VM. O grupo de recursos e o nome VM utilizados aqui são para o VM não funcional utilizado no passo 3.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Verifique e ative os diagnósticos de arranque

O exemplo a seguir permite a extensão de diagnóstico do VM nomeado ``myVMDeployed`` no grupo de recursos ``myResourceGroup`` denominado:

CLI do Azure

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Passos seguintes

* Se tiver problemas de ligação ao seu VM, consulte [as ligações RDP de resolução de problemas a um Azure VM](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot em máquinas virtuais em Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Para obter mais informações sobre a utilização do Gestor de Recursos, consulte [a visão geral do Gestor de Recursos do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
