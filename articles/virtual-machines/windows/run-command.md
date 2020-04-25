---
title: Executar scripts PowerShell em um VM Windows em Azure
description: Este tópico descreve como executar scripts PowerShell dentro de uma máquina virtual Do Windows Azure utilizando a funcionalidade Executar Command
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
manager: carmonm
ms.openlocfilehash: f4e318281da5cd704d9fbf13c96cbec0a2d1b1b6
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/24/2020
ms.locfileid: "82143787"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Executar scripts PowerShell no seu Windows VM utilizando o Comando de Execução

A função Executar Command utiliza o agente da máquina virtual (VM) para executar scripts PowerShell dentro de um VM Do Windows Azure. Pode utilizar estes scripts para uma máquina geral ou gestão de aplicações. Podem ajudá-lo a diagnosticar e remediar rapidamente os problemas de acesso e rede vM e levar o VM de volta a um bom estado.

 

## <a name="benefits"></a>Benefícios

Pode aceder às suas máquinas virtuais de várias maneiras. Executar o Comando pode executar scripts nas suas máquinas virtuais remotamente utilizando o agente VM. Utiliza o Comando de Execução através do portal Azure, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para VMs windows.

Esta capacidade é útil em todos os cenários onde você quer executar um script dentro de uma máquina virtual. É uma das únicas formas de resolver problemas e remediar uma máquina virtual que não tem a porta RDP ou SSH aberta devido a uma configuração inadequada da rede ou do utilizador administrativo.

## <a name="restrictions"></a>Restrições

As seguintes restrições aplicam-se quando estiver a utilizar o Comando de Execução:

* A produção está limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um guião é de cerca de 20 segundos.
* Os scripts funcionam como Sistema no Windows.
* Um guião de cada vez pode ser executado.
* Os scripts que solicitam informações (modo interativo) não são suportados.
* Não pode cancelar um guião.
* O tempo máximo que um guião pode executar é de 90 minutos. Depois disso, vai passar o tempo.
* A conectividade de saída do VM é necessária para devolver os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o Comando de Execução requer conectividade (porta 443) para endereços IP públicos do Azure. Se a extensão não tiver acesso a estes pontos finais, os scripts podem funcionar com sucesso, mas não devolver os resultados. Se estiver a bloquear o tráfego na máquina virtual, pode utilizar [etiquetas](../../virtual-network/security-overview.md#service-tags) de serviço para `AzureCloud` permitir o tráfego para endereços IP públicos do Azure utilizando a etiqueta.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs do Windows. Pode utilizar o comando **RunPowerShellScript** para executar qualquer script personalizado que pretenda. Quando estiver a utilizar o Azure CLI ou powerShell para executar `--command-id` um `-CommandId` comando, o valor que fornece para o ou para-quedista deve ser um dos seguintes valores listados. Quando especifica um valor que não é um comando disponível, recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunPowerShellScript**|Executa um guião powerShell.|
|**Ativar RemotePS**|Configura a máquina para ativar o PowerShell remoto.|
|**Conta EnableAdmin**|Verifique se a conta de administrador local está desativada e, em caso afirmativo, permite-a.|
|**IPConfig**| Mostra informações detalhadas para o endereço IP, máscara de sub-rede e porta de entrada padrão para cada adaptador ligado ao TCP/IP.|
|**RDPDefinições**|Verifica as definições de registo e as definições de política de domínio. Sugere ações de política se a máquina fizer parte de um domínio ou modificar as definições para valores padrão.|
|**ResetRDPCert**|Remove o certificado TLS/SSL ligado ao ouvinte RDP e restaura a segurança do ouvinte RDP à defeito. Utilize este script se vir algum problema com o certificado.|
|**SetRDPPort**|Define o número de porta predefinido ou especificado pelo utilizador para ligações remotas de ambiente de trabalho. Permite regras de firewall para acesso de entrada à porta.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo seguinte utiliza o comando [de comando de execução az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script de concha num VM Do Windows Azure.

```azurecli-interactive
# script.ps1
#   param(
#       [string]$arg1,
#       [string]$arg2
#   )
#   Write-Host This is a sample script with parameters $arg1 and $arg2

az vm run-command invoke  --command-id RunPowerShellScript --name win-vm -g my-resource-group \
    --scripts @script.ps1 --parameters "arg1=somefoo" "arg2=somebar"
```

## <a name="azure-portal"></a>Portal do Azure

Vá a um VM no [portal Azure](https://portal.azure.com) e selecione **comando de corrida** sob **OPERAÇÕES**. Vê uma lista dos comandos disponíveis para executar na VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando para correr. Alguns dos comandos podem ter parâmetros de entrada opcionais ou necessários. Para esses comandos, os parâmetros são apresentados como campos de texto para que forneça os valores de entrada. Para cada comando, pode ver o script que está a ser executado expandindo o **script View**. **RunPowerShellScript** é diferente dos outros comandos, pois permite-lhe fornecer o seu próprio script personalizado.

> [!NOTE]
> Os comandos incorporados não são editáveis.

Depois de escolher o comando, selecione **Executar** para executar o script. Após o acabamento do script, devolve a saída e quaisquer erros na janela de saída. A imagem que se segue mostra uma saída de exemplo de execução do comando **RDPSettings.**

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

O exemplo seguinte utiliza o cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script PowerShell num VM Azure. O cmdlet espera que o `-ScriptPath` guião referenciado no parâmetro seja local para onde o cmdlet está a ser executado.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao Comando de Execução

A listagem dos comandos de execução ou `Microsoft.Compute/locations/runCommands/read` a apresentação dos detalhes de um comando requer a permissão ao nível de subscrição. O papel do [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporado e os níveis mais altos têm esta permissão.

Executar um comando `Microsoft.Compute/virtualMachines/runCommand/action` requer a permissão ao nível de subscrição. O papel de [Colaborador de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e níveis mais elevados têm esta permissão.

Você pode usar uma das [funções incorporadas](../../role-based-access-control/built-in-roles.md) ou criar um [papel personalizado](../../role-based-access-control/custom-roles.md) para usar o Comando de Execução.

## <a name="next-steps"></a>Passos seguintes

Para aprender sobre outras formas de executar scripts e comandos remotamente no seu VM, consulte [scripts Run no seu Windows VM](run-scripts-in-vm.md).
