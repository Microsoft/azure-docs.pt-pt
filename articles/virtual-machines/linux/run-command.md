---
title: Executar scripts de concha em um Linux VM em Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual Azure Linux usando a funcionalidade Comando de Execução
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758599"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Executar scripts de concha no seu VM Linux usando o Comando de Execução

A função Executar Command utiliza o agente da máquina virtual (VM) para executar scripts de concha dentro de um VM Azure Linux. Pode utilizar estes scripts para uma máquina geral ou gestão de aplicações. Podem ajudá-lo a diagnosticar e remediar rapidamente os problemas de acesso e rede vM e levar o VM de volta a um bom estado.

## <a name="benefits"></a>Vantagens

Pode aceder às suas máquinas virtuais de várias maneiras. Executar o Comando pode executar scripts nas suas máquinas virtuais remotamente utilizando o agente VM. Utiliza o Comando de Execução através do portal Azure, [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para VMs Linux.

Esta capacidade é útil em todos os cenários onde você quer executar um script dentro de uma máquina virtual. É uma das únicas formas de resolver problemas e remediar uma máquina virtual que não tem a porta RDP ou SSH aberta devido a uma configuração inadequada da rede ou do utilizador administrativo.

## <a name="restrictions"></a>Restrições

As seguintes restrições aplicam-se quando estiver a utilizar o Comando de Execução:

* A produção está limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um guião é de cerca de 20 segundos.
* Scripts executados por padrão como um utilizador elevado no Linux.
* Podes executar um guião de cada vez.
* Os scripts que solicitam informações (modo interativo) não são suportados.
* Não pode cancelar um guião.
* O tempo máximo que um guião pode executar é de 90 minutos. Depois disso, o guião vai passar.
* A conectividade de saída do VM é necessária para devolver os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o Comando de Execução requer conectividade (porta 443) para endereços IP públicos do Azure. Se a extensão não tiver acesso a estes pontos finais, os scripts podem funcionar com sucesso, mas não devolver os resultados. Se estiver a bloquear o tráfego na máquina virtual, pode utilizar [etiquetas](../../virtual-network/security-overview.md#service-tags) de serviço para `AzureCloud` permitir o tráfego para endereços IP públicos do Azure utilizando a etiqueta.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs Linux. Pode utilizar o comando **RunShellScript** para executar qualquer script personalizado que pretenda. Quando estiver a utilizar o Azure CLI ou powerShell para executar `--command-id` um `-CommandId` comando, o valor que fornece para o ou para-quedista deve ser um dos seguintes valores listados. Quando especifica um valor que não é um comando disponível, recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um guião linux.|
|**ifconfig**| Obtém a configuração de todas as interfaces de rede.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo seguinte usa o comando de [comando de corrida az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script de concha num VM Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um `sudo -u` utilizador diferente, insira para especificar uma conta de utilizador.

## <a name="azure-portal"></a>Portal do Azure

Vá a um VM no [portal Azure](https://portal.azure.com) e selecione **comando de corrida** sob **OPERAÇÕES**. Vê uma lista dos comandos disponíveis para executar na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando para correr. Alguns dos comandos podem ter parâmetros de entrada opcionais ou necessários. Para esses comandos, os parâmetros são apresentados como campos de texto para que forneça os valores de entrada. Para cada comando, pode ver o script que está a ser executado expandindo o **script View**. **RunShellScript** é diferente dos outros comandos, pois permite-lhe fornecer o seu próprio script personalizado.

> [!NOTE]
> Os comandos incorporados não são editáveis.

Depois de escolher o comando, selecione **Executar** para executar o script. Após o acabamento do script, devolve a saída e quaisquer erros na janela de saída. A imagem que se segue mostra uma saída de exemplo de execução do comando **ifconfig.**

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

O exemplo seguinte utiliza o cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script PowerShell num VM Azure. O cmdlet espera que o `-ScriptPath` guião referenciado no parâmetro seja local para onde o cmdlet está a ser executado.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao Comando de Execução

A listagem dos comandos de execução ou `Microsoft.Compute/locations/runCommands/read` a apresentação dos detalhes de um comando requer a permissão ao nível de subscrição. O papel do [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporado e os níveis mais altos têm esta permissão.

Executar um comando `Microsoft.Compute/virtualMachines/runCommand/action` requer a permissão ao nível de subscrição. O papel de [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e níveis mais elevados têm esta permissão.

Você pode usar uma das [funções incorporadas](../../role-based-access-control/built-in-roles.md) ou criar um [papel personalizado](../../role-based-access-control/custom-roles.md) para usar o Comando de Execução.

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre outras formas de executar scripts e comandos remotamente no seu VM, consulte [scripts Run no seu VM Linux](run-scripts-in-vm.md).
