---
title: Executar scripts do PowerShell numa VM Windows no Azure
description: Este tópico descreve como executar scripts do PowerShell dentro de uma máquina virtual do Windows Azure utilizando o comando de execução
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 10/25/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: ecf19da59e91edd08953296d96ca68ca62f008e1
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57195358"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>Executar scripts do PowerShell na sua VM do Windows com o comando de execução

Execute o agente da VM para executar scripts do PowerShell dentro de uma VM do Windows Azure de usos de comando. Esses scripts podem ser utilizados para a máquina geral ou gestão de aplicações e podem ser utilizados para diagnosticar rapidamente e remediar problemas de acesso e de rede VM e obter a VM de volta para um bom estado.

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="benefits"></a>Benefícios

Existem várias opções que podem ser utilizadas para aceder às suas máquinas virtuais. Execute o comando pode executar scripts em suas máquinas virtuais utilizando remotamente o agente da VM. Execute o comando pode ser utilizado através do portal do Azure, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), ou [PowerShell](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) para VMs do Windows.

Esta funcionalidade é útil em todos os cenários em que pretende executar um script dentro de uma máquina virtual e, é um das únicas formas de resolver problemas e remediar uma máquina virtual que não tem o RDP ou abrir o porta SSH devido a rede impróprias ou utilizador administrativo configuração.

## <a name="restrictions"></a>Restrições

As seguintes restrições aplicam-se ao utilizar o comando executar:

* Saída está limitada a última 4096 bytes
* O tempo mínimo para executar um script é cerca de 20 segundos
* Scripts executados como sistema no Windows
* Pode executar um script ao mesmo tempo
* Scripts que pedem informações (modo interativo) não são suportadas.
* Não é possível cancelar um script em execução
* O tempo máximo que pode executar um script é 90 minutos, após o qual será o tempo limite
* Conectividade de saída da VM é necessário para devolver os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o comando Executar necessita de conectividade (porta 443) para os endereços IP públicos do Azure. Se a extensão não tiver acesso a estes pontos finais, os scripts podem ser executada com êxito mas não devolver os resultados. Se estiver a bloquear o tráfego na máquina virtual, pode utilizar [etiquetas de serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para os endereços IP públicos do Azure utilizando o `AzureCloud` marca.

## <a name="run-a-command"></a>Executar um comando

Navegue para uma VM no [Azure](https://portal.azure.com) e selecione **execute o comando** sob **operações**. É apresentada uma lista dos comandos disponíveis para executar na VM.

![Executar a lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando para executar. Alguns dos comandos podem ter parâmetros de entrada obrigatórios ou opcionais. Para esses comandos, os parâmetros são apresentados como campos de texto para lhe fornecer os valores de entrada. Para cada comando que pode ver o script que está a ser executado ao expandir **Ver script**. **RunPowerShellScript** é diferente nos outros comandos, como pode fornecer seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não são editáveis.

Assim que o comando for escolhido, clique em **executar** para executar o script. O script é executado e quando concluída, devolve o resultado e os erros na janela de saída. Captura de ecrã seguinte mostra um exemplo da saída da execução de **RDPSettings** comando.

![Execute a saída do comando de script](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>Comandos

Esta tabela mostra a lista de comandos disponíveis para as VMs do Windows. O **RunPowerShellScript** comando pode ser utilizado para executar qualquer script personalizado que pretende.

|**Nome**|**Descrição**|
|---|---|
|**RunPowerShellScript**|Executa um script do PowerShell|
|**EnableRemotePS**|Configura a máquina para ativar o PowerShell remoto.|
|**EnableAdminAccount**|Verifica se a conta de administrador local está desativada e, se for o caso o permite.|
|**IPConfig**| Mostra informações detalhadas para o IP de endereços, gateway predefinido e a máscara de sub-rede para cada adaptador vinculada a TCP/IP.|
|**RDPSettings**|Verifica as definições de registo e definições de política de domínio. Sugere ações de política se máquina faz parte de um domínio ou modifica as definições para os valores predefinidos.|
|**ResetRDPCert**|Remove o certificado SSL vinculado ao serviço de escuta RDP e restaura a segurança de listerner RDP padrão. Utilize este script se identificar quaisquer problemas com o certificado.|
|**SetRDPPort**|Define o padrão ou o utilizador especificado o número de porta para ligações de ambiente de trabalho remoto. Permite que a regra de firewall para acesso de entrada para a porta.|

## <a name="powershell"></a>PowerShell

Segue-se um exemplo com o [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet para executar um script do PowerShell na VM do Azure. O cmdlet espera que o script referenciado no `-ScriptPath` parâmetro seja local para onde está a ser executado o cmdlet.


```azurepowershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>Limitar o acesso a executar o comando

Listagem os comandos de execução ou que mostra os detalhes de um comando de exigir a `Microsoft.Compute/locations/runCommands/read` permissão, que incorporada [leitor](../../role-based-access-control/built-in-roles.md#reader) função e superior.

A execução de um comando requer o `Microsoft.Compute/virtualMachines/runCommand/action` permissão, que o [contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) função e superior.

Pode utilizar um da [incorporadas](../../role-based-access-control/built-in-roles.md) funções ou criar um [personalizado](../../role-based-access-control/custom-roles.md) função para utilizar o comando Executar.

## <a name="next-steps"></a>Passos Seguintes

Ver, [executem scripts na sua VM do Windows](run-scripts-in-vm.md) para saber mais sobre outras formas de executar scripts e comandos remotamente na sua VM.
