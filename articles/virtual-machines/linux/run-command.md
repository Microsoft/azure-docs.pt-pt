---
title: Executar scripts de conchas em um Linux VM em Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual Azure Linux usando a funcionalidade Comando de Execução
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: a14fafde8ecea0370c74cdbfd39a85d8dfb15612
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "83651082"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>Executar scripts de concha no seu Linux VM usando o Comando de Execução

A função Comando de Execução utiliza o agente de máquina virtual (VM) para executar scripts de concha dentro de um VM Azure Linux. Pode utilizar estes scripts para a gestão geral de máquinas ou aplicações. Eles podem ajudá-lo a diagnosticar e remediar rapidamente o acesso vm e problemas de rede e levar o VM de volta a um bom estado.

## <a name="benefits"></a>Benefícios

Pode aceder às suas máquinas virtuais de várias formas. O Comando de Execução pode executar scripts nas suas máquinas virtuais remotamente utilizando o agente VM. Utilize o Comando run através do portal Azure, [REST API,](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para Os VMs Linux.

Esta capacidade é útil em todos os cenários onde pretende executar um script dentro de uma máquina virtual. É uma das únicas formas de resolver problemas e remediar uma máquina virtual que não tem a porta RDP ou SSH aberta devido à configuração inadequada da rede ou do utilizador administrativo.

## <a name="restrictions"></a>Restrições

Aplicam-se as seguintes restrições quando utiliza o Comando de Execução:

* A produção está limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um guião é de cerca de 20 segundos.
* Scripts executados por padrão como um utilizador elevado em Linux.
* Podes fazer um guião de cada vez.
* Os scripts que solicitam informações (modo interativo) não são suportados.
* Não podes cancelar um guião de corrida.
* O tempo máximo que um script pode executar é de 90 minutos. Depois disso, o guião vai acabar.
* A conectividade de saída do VM é necessária para devolver os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o Comando de Execução requer conectividade (porta 443) para endereços IP públicos Azure. Se a extensão não tiver acesso a estes pontos finais, os scripts podem ser executados com sucesso, mas não devolvem os resultados. Se estiver a bloquear o tráfego na máquina virtual, pode utilizar [tags de serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para endereços IP públicos Azure utilizando a `AzureCloud` etiqueta.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para Os VMs Linux. Pode utilizar o comando **RunShellScript** para executar qualquer script personalizado que pretenda. Quando estiver a utilizar o Azure CLI ou o PowerShell para executar um comando, o valor que fornece para o `--command-id` ou parâmetro deve ser um dos `-CommandId` seguintes valores listados. Quando especifica um valor que não é um comando disponível, recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um guião de conchas Linux.|
|**seconfig**| Obtém a configuração de todas as interfaces de rede.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir utiliza o comando [de comando de execução az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar uma script de concha num VM Azure Linux.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um utilizador diferente, insira `sudo -u` para especificar uma conta de utilizador.

## <a name="azure-portal"></a>Portal do Azure

Vá a um VM no [portal Azure](https://portal.azure.com) e selecione **comando executar** em **operações**. Vê uma lista dos comandos disponíveis para ser executado no VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando para executar. Alguns dos comandos podem ter parâmetros de entrada opcionais ou necessários. Para esses comandos, os parâmetros são apresentados como campos de texto para fornecer os valores de entrada. Para cada comando, pode ver o script que está a ser executado através da expansão **do script 'Ver'.** **O RunShellScript** é diferente dos outros comandos, pois permite-lhe fornecer o seu próprio script personalizado.

> [!NOTE]
> Os comandos incorporados não são editáveis.

Depois de escolher o comando, selecione **Executar** para executar o script. Após o fim do script, retorna a saída e quaisquer erros na janela de saída. A imagem que se segue mostra uma saída de exemplo da execução do comando **ifconfig.**

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

O exemplo a seguir utiliza o [cmdlet Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script PowerShell num Azure VM. O cmdlet espera que o script referenciado no `-ScriptPath` parâmetro seja local para onde o cmdlet está sendo executado.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitação do acesso ao Comando de Execução

A listagem dos comandos de execução ou a apresentação dos detalhes de um comando requer a `Microsoft.Compute/locations/runCommands/read` permissão. O papel de [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporado e os níveis mais altos têm esta permissão.

Executar um comando requer a `Microsoft.Compute/virtualMachines/runCommand/action` permissão. O papel [de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e níveis mais altos têm esta permissão.

Pode utilizar uma das [funções incorporadas](../../role-based-access-control/built-in-roles.md) ou criar um [papel personalizado](../../role-based-access-control/custom-roles.md) para utilizar o Comando Run.

## <a name="next-steps"></a>Próximos passos

Para aprender sobre outras formas de executar scripts e comandos remotamente no seu VM, consulte [scripts Run no seu Linux VM](run-scripts-in-vm.md).
