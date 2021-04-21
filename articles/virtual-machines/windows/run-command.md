---
title: Executar scripts PowerShell num Windows VM em Azure
description: Este tópico descreve como executar scripts PowerShell dentro de uma máquina virtual Azure Windows utilizando a funcionalidade Comando run
services: automation
ms.service: virtual-machines
ms.collection: windows
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: carmonm
ms.openlocfilehash: 3271f5461447439772b656b8927a54057c8b0c7e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786410"
---
# <a name="run-powershell-scripts-in-your-windows-vm-by-using-run-command"></a>Executar scripts PowerShell no seu Windows VM utilizando o Comando Run

A função 'Comando de Execução' utiliza o agente da máquina virtual (VM) para executar scripts PowerShell dentro de um Azure Windows VM. Pode utilizar estes scripts para a gestão geral de computadores ou aplicações. Eles podem ajudá-lo a diagnosticar e remediar rapidamente o acesso vm e problemas de rede e levar o VM de volta a um bom estado.



## <a name="benefits"></a>Benefícios

Pode aceder às suas máquinas virtuais de várias formas. O Comando de Execução pode executar scripts nas suas máquinas virtuais remotamente utilizando o agente VM. Utilize o Comando Run através do portal Azure, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)ou [PowerShell](/powershell/module/az.compute/invoke-azvmruncommand) para VMs windows.

Esta capacidade é útil em todos os cenários onde pretende executar um script dentro de uma máquina virtual. É uma das únicas formas de resolver problemas e remediar uma máquina virtual que não tem a porta RDP ou SSH aberta devido à configuração inadequada da rede ou do utilizador administrativo.

## <a name="restrictions"></a>Restrições

Aplicam-se as seguintes restrições quando utiliza o Comando de Execução:

* A produção está limitada aos últimos 4.096 bytes.
* O tempo mínimo para executar um guião é de cerca de 20 segundos.
* Os scripts funcionam como Sistema no Windows.
* Um guião de cada vez pode ser executado.
* Os scripts que solicitam informações (modo interativo) não são suportados.
* Não podes cancelar um guião de corrida.
* O tempo máximo que um script pode executar é de 90 minutos. Depois disso, vai ficar sem tempo.
* A conectividade de saída do VM é necessária para devolver os resultados do script.
* Não é aconselhável executar um script que irá causar uma paragem ou atualização do Agente VM. Isto pode permitir a extensão em um estado de transição, levando a um tempo limite.

> [!NOTE]
> Para funcionar corretamente, o Comando de Execução requer conectividade (porta 443) para endereços IP públicos Azure. Se a extensão não tiver acesso a estes pontos finais, os scripts podem ser executados com sucesso, mas não devolvem os resultados. Se estiver a bloquear o tráfego na máquina virtual, pode utilizar [tags de serviço](../../virtual-network/network-security-groups-overview.md#service-tags) para permitir o tráfego para endereços IP públicos Azure utilizando a `AzureCloud` etiqueta.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs windows. Pode utilizar o comando **RunPowerShellScript** para executar qualquer script personalizado que pretenda. Quando estiver a utilizar o Azure CLI ou o PowerShell para executar um comando, o valor que fornece para o `--command-id` ou parâmetro deve ser um dos `-CommandId` seguintes valores listados. Quando especifica um valor que não é um comando disponível, recebe este erro:

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunPowerShellScript**|Executa um guião PowerShell.|
|**EnableRemotePS**|Configura a máquina para ativar o PowerShell remoto.|
|**EnableAdminAccount**|Verifica se a conta do administrador local está desativada e, em caso afirmativo.|
|**IPConfig**| Mostra informações detalhadas para o endereço IP, máscara de sub-rede e porta de entrada predefinido para cada adaptador ligado a TCP/IP.|
|**RDPSettings**|Verifica as definições de registo e definições de política de domínio. Sugere ações de política se a máquina fizer parte de um domínio ou modificar as definições para valores predefinidos.|
|**ResetRDPCert**|Remove o certificado TLS/SSL ligado ao ouvinte RDP e restaura a segurança do ouvinte RDP por defeito. Utilize este script se vir algum problema com o certificado.|
|**SetRDPPort**|Define o número de porta predefinido ou especificado pelo utilizador para ligações de ambiente de trabalho remoto. Permite regras de firewall para acesso à entrada na porta.|

## <a name="azure-cli"></a>CLI do Azure

O exemplo a seguir utiliza o comando [de comando de execução az vm](/cli/azure/vm/run-command#az_vm_run_command_invoke) para executar uma script de concha num Azure Windows VM.

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

Vá a um VM no [portal Azure](https://portal.azure.com) e selecione **comando executar** em **operações**. Vê uma lista dos comandos disponíveis para ser executado no VM.

![Lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando para executar. Alguns dos comandos podem ter parâmetros de entrada opcionais ou necessários. Para esses comandos, os parâmetros são apresentados como campos de texto para fornecer os valores de entrada. Para cada comando, pode ver o script que está a ser executado através da expansão **do script 'Ver'.** **O RunPowerShellScript** é diferente dos outros comandos, pois permite-lhe fornecer o seu próprio script personalizado.

> [!NOTE]
> Os comandos incorporados não são editáveis.

Depois de escolher o comando, selecione **Executar** para executar o script. Após o fim do script, retorna a saída e quaisquer erros na janela de saída. A imagem que se segue mostra uma saída de exemplo da execução do comando **RDPSettings.**

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

## <a name="powershell"></a>PowerShell

O exemplo a seguir utiliza o [cmdlet Invoke-AzVMRunCommand](/powershell/module/az.compute/invoke-azvmruncommand) para executar um script PowerShell num Azure VM. O cmdlet espera que o script referenciado no `-ScriptPath` parâmetro seja local para onde o cmdlet está sendo executado.

```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitar o acesso ao Comando Executar

A listagem dos comandos de execução ou a apresentação dos detalhes de um comando requer a `Microsoft.Compute/locations/runCommands/read` permissão no Nível de Subscrição. O papel de [Leitor](../../role-based-access-control/built-in-roles.md#reader) incorporado e os níveis mais altos têm esta permissão.

Executar um comando requer a `Microsoft.Compute/virtualMachines/runCommand/action` permissão. O papel [de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e níveis mais altos têm esta permissão.

Pode utilizar uma das [funções incorporadas](../../role-based-access-control/built-in-roles.md) ou criar um [papel personalizado](../../role-based-access-control/custom-roles.md) para utilizar o Comando Run.

## <a name="next-steps"></a>Passos seguintes

Para saber sobre outras formas de executar scripts e comandos remotamente no seu VM, consulte [scripts executar no seu VM do Windows](run-scripts-in-vm.md).
