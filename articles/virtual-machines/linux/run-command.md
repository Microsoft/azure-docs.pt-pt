---
title: Executar scripts de shell numa VM do Linux no Azure
description: Este tópico descreve como executar scripts dentro de uma máquina virtual de Linux do Azure com o comando de execução
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: fe0547c1ddb89b8d9ab3e876e83bbdea17a58743
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64570593"
---
# <a name="run-shell-scripts-in-your-linux-vm-with-run-command"></a>Executar scripts de shell na VM do Linux com o comando Executar

Execute o agente da VM para executar scripts de shell dentro de uma VM do Linux do Azure de usos de comando. Esses scripts podem ser utilizados para a máquina geral ou gestão de aplicações e podem ser utilizados para diagnosticar rapidamente e remediar problemas de acesso e de rede VM e obter a VM de volta para um bom estado.

## <a name="benefits"></a>Benefícios

Existem várias opções que podem ser utilizadas para aceder às suas máquinas virtuais. Execute o comando pode executar scripts em suas máquinas virtuais utilizando remotamente o agente da VM. Execute o comando pode ser utilizado através do portal do Azure, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), ou [CLI do Azure](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) para VMs do Linux.

Esta funcionalidade é útil em todos os cenários em que pretende executar um script dentro de uma máquina virtual e, é um das únicas formas de resolver problemas e remediar uma máquina virtual que não tem o RDP ou abrir o porta SSH devido a rede impróprias ou utilizador administrativo configuração.

## <a name="restrictions"></a>Restrições

Seguem-se uma lista de restrições que estão presentes quando utilizar o comando Executar.

* Saída é limitada para a última 4096 bytes
* O tempo mínimo para executar um script cerca de 20 segundos
* Os scripts executados, por padrão, como utilizador elevados no Linux
* Pode executar um script ao mesmo tempo
* Scripts que pedem informações (modo interativo) não são suportadas.
* Não é possível cancelar um script em execução
* O tempo máximo que pode executar um script é 90 minutos, após o qual será o tempo limite
* Conectividade de saída da VM é necessário para devolver os resultados do script.

> [!NOTE]
> Para funcionar corretamente, o comando Executar necessita de conectividade (porta 443) para os endereços IP públicos do Azure. Se a extensão não tiver acesso a estes pontos finais, os scripts podem ser executada com êxito mas não devolver os resultados. Se estiver a bloquear o tráfego na máquina virtual, pode utilizar [etiquetas de serviço](../../virtual-network/security-overview.md#service-tags) para permitir o tráfego para os endereços IP públicos do Azure utilizando o `AzureCloud` marca.

## <a name="azure-cli"></a>CLI do Azure

Segue-se um exemplo com o [run-comando az vm](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) comando para executar um script de shell na VM do Linux do Azure.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> Para executar comandos como um utilizador diferente, pode utilizar `sudo -u` para especificar uma conta de utilizador para utilizar.

## <a name="azure-portal"></a>Portal do Azure

Navegue para uma VM no [Azure](https://portal.azure.com) e selecione **execute o comando** sob **operações**. É apresentada uma lista dos comandos disponíveis para executar na VM.

![Executar a lista de comandos](./media/run-command/run-command-list.png)

Escolha um comando para executar. Alguns dos comandos podem ter parâmetros de entrada obrigatórios ou opcionais. Para esses comandos, os parâmetros são apresentados como campos de texto para lhe fornecer os valores de entrada. Para cada comando que pode ver o script que está a ser executado ao expandir **Ver script**. **RunShellScript** é diferente nos outros comandos, como pode fornecer seu próprio script personalizado.

> [!NOTE]
> Os comandos internos não são editáveis.

Assim que o comando for escolhido, clique em **executar** para executar o script. O script é executado e quando concluída, devolve o resultado e os erros na janela de saída. Captura de ecrã seguinte mostra um exemplo da saída da execução de **ifconfig** comando.

![Execute a saída do comando de script](./media/run-command/run-command-script-output.png)

## <a name="available-commands"></a>Comandos disponíveis

Esta tabela mostra a lista de comandos disponíveis para VMs do Linux. O **RunShellScript** comando pode ser utilizado para executar qualquer script personalizado que pretende.

|**Nome**|**Descrição**|
|---|---|
|**RunShellScript**|Executa um script de shell do Linux.|
|**ifconfig**| Obter a configuração de todas as interfaces de rede.|

## <a name="limiting-access-to-run-command"></a>Limitar o acesso a executar o comando

Listagem os comandos de execução ou que mostra os detalhes de um comando de exigir a `Microsoft.Compute/locations/runCommands/read` permissão ao nível da subscrição, que incorporada [leitor](../../role-based-access-control/built-in-roles.md#reader) função e superior.

A execução de um comando requer o `Microsoft.Compute/virtualMachines/runCommand/action` permissão ao nível da subscrição, que o [contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) função e superior.

Pode utilizar um da [incorporadas](../../role-based-access-control/built-in-roles.md) funções ou criar um [personalizado](../../role-based-access-control/custom-roles.md) função para utilizar o comando Executar.

## <a name="next-steps"></a>Passos Seguintes

Ver, [executem scripts na VM do Linux](run-scripts-in-vm.md) para saber mais sobre outras formas de executar scripts e comandos remotamente na sua VM.
