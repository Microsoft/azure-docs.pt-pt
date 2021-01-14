---
title: Repare um Linux VM utilizando os comandos de reparação da Máquina Virtual Azure | Microsoft Docs
description: Este artigo detalha como utilizar comandos de reparação de máquinas virtuais Azure para ligar o disco a outro Linux VM para corrigir eventuais erros e, em seguida, reconstruir o seu VM original.
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
ms.openlocfilehash: d3db1c3cdfc87cedc0ba24fadc0271a8af44a279
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201914"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Reparar uma VM do Linux com os comandos de reparação da Máquina Virtual do Azure

Se a sua máquina virtual Linux (VM) em Azure encontrar um erro de arranque ou disco, poderá ter de efetuar uma mitigação no próprio disco. Um exemplo comum seria uma atualização de aplicação falhada que impede o VM de ser capaz de arrancar com sucesso. Este artigo detalha como utilizar comandos de reparação de máquinas virtuais Azure para ligar o disco a outro Linux VM para corrigir eventuais erros e, em seguida, reconstruir o seu VM original.

> [!IMPORTANT]
> * Os scripts deste artigo aplicam-se apenas aos VMs que utilizam [o Gestor de Recursos Azure.](../../azure-resource-manager/management/overview.md)
> * A conectividade de saída do VM (porta 443) é necessária para que o script seja executado.
> * Só um guião pode ser executado de cada vez.
> * Um roteiro de execução não pode ser cancelado.
> * O tempo máximo que um script pode executar é de 90 minutos, após o qual irá esgotar-se.
> * Para VMs que usam encriptação de disco Azure, apenas os discos geridos encriptados com encriptação de passe único (com ou sem KEK) são suportados.

## <a name="repair-process-overview"></a>Visão geral do processo de reparação

Agora pode utilizar comandos de reparação da Máquina Virtual Azure para alterar o disco DE para um VM, e já não precisa de eliminar e recriar o VM.

Siga estes passos para resolver problemas na questão dos VM:

1. Iniciar o Azure Cloud Shell
2. Executar az adicionar/atualizar extensão
3. Executar az vm reparação criar
4. Execute o funcionaamento de reparação az vm ou execute passos de mitigação.
5. Executar restauro de reparação az vm

Para obter documentação e instruções adicionais, consulte [a reparação az vm](/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Exemplo do processo de reparação

1. Iniciar o Azure Cloud Shell

   O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Inclui ferramentas Azure comuns pré-instaladas e configuradas para usar com a sua conta.

   Para abrir a Cloud Shell, selecione **Experimente-a** a partir do canto superior direito de um bloco de código. Também pode abrir o Cloud Shell num separador de navegador indo para [https://shell.azure.com](https://shell.azure.com) .

   Selecione **Copy** para copiar os blocos de código, em seguida, cole o código na Cloud Shell e selecione **Enter** para executá-lo.

   Se preferir instalar e utilizar a CLI localmente, este início rápido requer a versão 2.0.30 ou posterior da CLI do Azure. Executar ``az --version`` para localizar a versão. Se necessitar de instalar ou atualizar o seu Azure CLI, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).
   
   Se precisar de iniciar sessão na Cloud Shell com uma conta diferente da que está atualmente a iniciar sessão no Portal Azure com a ``az login`` [sua referência de login az.](/cli/azure/reference-index#az-login&preserve-view=true)  Para alternar entre subscrições associadas à sua conta pode utilizar ``az account set --subscription`` [referência definida por conta az](/cli/azure/account#az-account-set&preserve-view=true).

2. Se esta for a primeira vez que utiliza os `az vm repair` comandos, adicione a extensão CLI de reparação de VM.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Se já utilizou previamente os `az vm repair` comandos, aplique quaisquer atualizações na extensão de reparação de VM.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Execute `az vm repair create`. Este comando criará uma cópia do disco DE para o VM não funcional, criará um VM de reparação num novo Grupo de Recursos e anexará a cópia do disco OS.  A VM de reparação terá o mesmo tamanho e região que o VM não funcional especificado. O grupo de recursos e o nome VM utilizados em todas as etapas serão para o VM não funcional. Se o seu VM estiver a utilizar a Encriptação do Disco Azure, o comando tentará desbloquear o disco encriptado de modo a que fique acessível quando ligado ao VM de reparação.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Execute `az vm repair run`. Este comando executará o script de reparação especificado no disco anexo através do VM de reparação. Se o guia de resolução de problemas que está a utilizar estiver a utilizar um id de execução, utilize-o aqui, caso contrário poderá utilizar scripts de lista de reparação az vm para ver scripts de reparação disponíveis. O grupo de recursos e o nome VM utilizados aqui são para o VM não funcional utilizado no passo 3. Informações adicionais sobre os scripts de reparação podem ser encontradas na biblioteca de [scripts de reparação](https://github.com/Azure/repair-script-library).

   ```azurecli-interactive
   az vm repair run -g MyResourceGroup -n MyVM --run-on-repair --run-id lin-hello-world --verbose
   ```

   Opcionalmente, pode executar as etapas de mitigação manual necessárias utilizando o VM de reparação e, em seguida, proceder ao passo 5.

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

* Se tiver problemas de ligação ao seu VM, consulte [as ligações RDP de resolução de problemas a uma Máquina Virtual Azure](./troubleshoot-rdp-connection.md).
* Para problemas com o acesso a aplicações em execução no seu VM, consulte [problemas de conectividade da aplicação Troubleshoot em máquinas virtuais em Azure](./troubleshoot-app-connection.md).
* Para obter mais informações sobre a utilização do Gestor de Recursos, consulte [a visão geral do Gestor de Recursos do Azure](../../azure-resource-manager/management/overview.md).
