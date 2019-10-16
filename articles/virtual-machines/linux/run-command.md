---
title: Executar scripts de Shell em uma VM do Linux no Azure
description: Este tópico descreve como executar scripts em uma máquina virtual Linux do Azure usando o comando executar
services: automation
ms.service: automation
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 6550b6e3f59ff7e6bac39dfc1abcf829256122d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2019
ms.locfileid: "72376358"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Executar scripts de Shell em sua VM do Linux com o comando executar

O comando executar usa o agente de VM para executar scripts de Shell em uma VM Linux do Azure. Esses scripts podem ser usados para gerenciamento geral de computadores ou aplicativos e podem ser usados para diagnosticar e corrigir rapidamente problemas de rede e acesso à VM e colocar a VM de volta em um bom estado.

## <a name="benefits"></a>Vantagens

Há várias opções que podem ser usadas para acessar suas máquinas virtuais. O comando executar pode executar scripts em suas máquinas virtuais remotamente usando o agente de VM. O comando executar pode ser usado por meio da [API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)portal do Azure, REST ou [CLI do Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para VMs do Linux.

Esse recurso é útil em todos os cenários em que você deseja executar um script em máquinas virtuais e é uma das únicas maneiras de solucionar problemas e corrigir uma máquina virtual que não tem a porta RDP ou SSH aberta devido à rede inadequada ou ao usuário administrativo configuração.

## <a name="restrictions"></a>Restrições

Veja a seguir uma lista de restrições que estão presentes ao usar o comando executar.

* A saída é limitada aos últimos 4096 bytes
* O tempo mínimo para executar um script cerca de 20 segundos
* Os scripts são executados por padrão como usuário elevado no Linux
* Um script de cada vez pode ser executado
* Não há suporte para scripts que solicitam informações (modo interativo).
* Não é possível cancelar um script em execução
* O tempo máximo que um script pode executar é de 90 minutos, após o qual atingirá o tempo limite
* A conectividade de saída da VM é necessária para retornar os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o comando executar requer conectividade (porta 443) para endereços IP públicos do Azure. Se a extensão não tiver acesso a esses pontos de extremidade, os scripts poderão ser executados com êxito, mas não retornar os resultados. Se você estiver bloqueando o tráfego na máquina virtual, poderá usar [marcas de serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para endereços IP públicos do Azure usando a marca `AzureCloud`.

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs do Linux. O comando **RunShellScript** pode ser usado para executar qualquer script personalizado desejado. Ao usar o CLI do Azure ou o PowerShell para executar um comando, o valor que você fornece para o parâmetro `--command-id` ou `-CommandId` deve ser um dos valores listados abaixo. Quando você especifica um valor que não é um comando disponível, você recebe o erro.

```error
The entity was not found in this Azure location
```

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um script de shell do Linux.|
|**ifconfig**| Obtenha a configuração de todas as interfaces de rede.|

## <a name="azure-cli"></a>CLI do Azure

Veja a seguir um exemplo de como usar o comando [AZ VM execute-Command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para executar um script de Shell em uma VM Linux do Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um usuário diferente, você pode usar `sudo -u` para especificar uma conta de usuário a ser usada.

## <a name="azure-portal"></a>Portal do Azure

Navegue até uma VM no [Azure](https://portal.azure.com) e selecione **executar comando** em **operações**. Você verá uma lista dos comandos disponíveis a serem executados na VM.

![Executar lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando a ser executado. Alguns dos comandos podem ter parâmetros de entrada opcionais ou obrigatórios. Para esses comandos, os parâmetros são apresentados como campos de texto para que você forneça os valores de entrada. Para cada comando, você pode exibir o script que está sendo executado expandindo o **script de exibição**. O **RunShellScript** é diferente dos outros comandos, pois permite que você forneça seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não são editáveis.

Depois que o comando for escolhido, clique em **executar** para executar o script. O script é executado e, quando concluído, retorna a saída e todos os erros na janela de saída. A captura de tela a seguir mostra um exemplo de saída da execução do comando **ifconfig** .

![Executar saída de script de comando](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

Veja a seguir um exemplo que usa o cmdlet [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para executar um script do PowerShell em uma VM do Azure. O cmdlet espera que o script referenciado no parâmetro `-ScriptPath` seja local no qual o cmdlet está sendo executado.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitando o acesso ao comando executar

Listar os comandos de execução ou mostrar os detalhes de um comando requer a permissão `Microsoft.Compute/locations/runCommands/read` no nível da assinatura, que a função [leitor](../../role-based-access-control/built-in-roles.md#reader) interna e superior tem.

A execução de um comando requer a permissão `Microsoft.Compute/virtualMachines/runCommand/action` no nível da assinatura, que a função [colaborador da máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) e superior tem.

Você pode usar uma das funções [internas](../../role-based-access-control/built-in-roles.md) ou criar uma função [personalizada](../../role-based-access-control/custom-roles.md) para usar o comando executar.

## <a name="next-steps"></a>Passos seguintes

Consulte [executar scripts em sua VM do Linux](run-scripts-in-vm.md) para saber mais sobre outras maneiras de executar scripts e comandos remotamente em sua VM.
