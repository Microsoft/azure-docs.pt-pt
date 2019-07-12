---
title: Extensões de VM e recursos para Linux do Azure | Documentos da Microsoft
description: Saiba que extensões estão disponíveis para máquinas virtuais do Azure, agrupadas pelo que fornecer ou melhorar.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 3f22da9eabc6f539ef37009f565f073b9de89319
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706749"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensões de máquina virtual e funcionalidades para Linux

As extensões de máquina virtual do Azure (VM) são aplicativos pequenos que fornecem as tarefas de automatização e configuração de pós-implementação em VMs do Azure. Por exemplo, se uma máquina virtual requer a instalação de software, proteção de software antivírus, ou para executar um script dentro dela, uma extensão de VM pode ser utilizada. Extensões VM do Azure podem ser executadas com a CLI do Azure, PowerShell, modelos do Azure Resource Manager e o portal do Azure. As extensões podem ser agrupadas com uma nova implementação de VM ou executar qualquer sistema existente.

Este artigo fornece uma descrição geral das extensões de VM, pré-requisitos para utilizar extensões de VM do Azure, e obter orientações sobre como detetar, gerir e remover extensões de VM. Este artigo fornece informações generalizadas, porque muitas extensões VM estão disponíveis, cada um com uma configuração potencialmente exclusiva. Extensão específica pode encontrar detalhes de cada documento específicos para a extensão individual.

## <a name="use-cases-and-samples"></a>Casos de utilização e exemplos

Várias extensões de VM do Azure diferentes estão disponíveis, cada um com uma específicas do caso de utilização. Alguns exemplos incluem:

- Aplica configurações de estado de pretendido do PowerShell para uma VM com a extensão de DSC para Linux. Para obter mais informações, consulte [extensão de configuração de estado pretendido do Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure a monitorização de uma VM com a extensão de VM de agente de monitorização Microsoft. Para obter mais informações, consulte [como monitorizar uma VM do Linux](../linux/tutorial-monitoring.md).
- Configure a monitorização da sua infraestrutura do Azure com a extensão Chef ou Datadog. Para obter mais informações, consulte a [Chef docs](https://docs.chef.io/azure_portal.html) ou [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Além de extensões específicas do processo, uma extensão de Script personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de Script personalizado para Linux permite que qualquer script de Bash ser executado numa VM. Scripts personalizados são úteis para a criação de implementações do Azure que necessitam de configuração para além dos quais as de ferramentas do Azure nativa podem fornecer. Para obter mais informações, consulte [extensão de Script de personalizado de VM do Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão na VM, terá do agente do Linux do Azure instalado. Algumas extensões individuais têm pré-requisitos, tais como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente de VM do Azure gere as interações entre uma VM do Azure e o controlador de recursos de infraestrutura do Azure. O agente da VM é responsável por muitos aspetos funcionais de implementar e gerir VMs do Azure, incluindo a execução de extensões de VM. O agente de VM do Azure é previamente instalado nas imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operativos suportados. O agente da VM do Azure para Linux é conhecido como o agente do Linux.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte [agente da máquina virtual do Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versões de agentes suportados

Para fornecer a melhor experiência possível, existem versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemas operacionais suportados

O agente do Linux é executado em vários sos, no entanto, a estrutura de extensões tem um limite para os sistemas operacionais que extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não são suportadas em todos os sos e pode emitir *51 de código de erro, o "SO não suportada"* . Consulte a documentação de extensão individuais para a capacidade de suporte.

#### <a name="network-access"></a>Acesso à rede

Pacotes de extensão são transferidos a partir do repositório de extensão do armazenamento do Azure e carregamentos de estado de extensão são lançados para o armazenamento do Azure. Se usar [suportado](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) versão dos agentes, não é necessário permitir o acesso ao armazenamento do Azure na região da VM, como pode utilizar o agente para redirecionar a comunicação para o controlador de malha do Azure para comunicações de agente. Se estiver numa versão não suportada do agente, terá de permitir o acesso de saída para o armazenamento do Azure na região da VM.

> [!IMPORTANT]
> Se bloqueou o acesso a *168.63.129.16* utilizando a firewall de convidado, em seguida, extensões falharem independentemente acima.

Agentes só podem ser utilizados para transferir pacotes de extensão e comunicar o estado. Por exemplo, se uma instalação da extensão tem de transferir um script a partir do GitHub (Script personalizado) ou tem de aceder ao armazenamento do Azure (Azure Backup), em seguida, adicionais/rede de firewall segurança portas de grupo tem de ser aberto. Extensões diferentes têm requisitos diferentes, uma vez que são aplicativos por si mesmos. Para extensões que necessitam de acesso ao armazenamento do Azure, pode permitir acesso utilizando as etiquetas de serviço do Azure NSG para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Para redirecionar os pedidos de tráfego do agente, o agente do Linux tem suporte de servidor proxy. No entanto, esse suporte de servidor proxy não é aplicável a extensões. Tem de configurar cada extensão individual para trabalhar com um proxy.

## <a name="discover-vm-extensions"></a>Descubra as extensões de VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, utilize [lista de imagens de extensão de vm de az](/cli/azure/vm/extension/image#az-vm-extension-image-list). O exemplo seguinte lista todas as extensões disponíveis no *westus* localização:

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Executar as extensões de VM

Extensões VM do Azure, execute em VMs existentes, que é útil quando precisa fazer alterações de configuração ou recuperar a conectividade numa VM já implementada. Extensões de VM também podem ser agrupadas com implementações de modelo do Azure Resource Manager. Ao utilizar extensões com modelos do Resource Manager, VMs do Azure pode ser implementadas e configuradas sem a intervenção de pós-implementação.

Os seguintes métodos podem ser utilizados para executar uma extensão em relação a uma VM existente.

### <a name="azure-cli"></a>CLI do Azure

Extensões VM do Azure podem ser executadas numa VM existente com o [conjunto de extensão az vm](/cli/azure/vm/extension#az-vm-extension-set) comando. O exemplo seguinte executa a extensão de Script personalizado em relação a uma VM com o nome *myVM* num grupo de recursos com o nome *myResourceGroup*:

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Quando a extensão for executado corretamente, o resultado é semelhante ao seguinte exemplo:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

Extensões de VM podem ser aplicadas a uma VM existente através do portal do Azure. Selecione a VM no portal, escolha **extensões**, em seguida, selecione **Add**. Escolha a extensão de que pretende na lista de extensões disponíveis e siga as instruções no assistente.

A imagem seguinte mostra a instalação da extensão de Script personalizado do Linux no portal do Azure:

![Instalar a extensão de script personalizado](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

Extensões de VM podem ser adicionadas a um modelo Azure Resource Manager e executadas com a implementação do modelo. Quando implementa uma extensão com um modelo, pode criar implementações do Azure totalmente configuradas. Por exemplo, o seguinte JSON é obtido a partir de um modelo do Resource Manager que implementa um conjunto de VMs com balanceamento de carga e uma base de dados SQL do Azure, em seguida, instala uma aplicação .NET Core em cada VM. A extensão da VM se encarrega da instalação de software.

Para obter mais informações, consulte o completo [modelo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
    }
}
```

Para obter mais informações sobre a criação de modelos do Resource Manager, consulte [modelos Authoring Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger os dados de extensão VM

Quando executa uma extensão de VM, poderá ser necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Várias extensões VM incluem uma configuração protegida que encripta os dados e só descriptografa-a no interior da VM de destino. Cada extensão tem um esquema de configuração protegida específica, e cada está detalhada na documentação de extensão específica.

O exemplo seguinte mostra uma instância da extensão de Script personalizado para Linux. O comando a executar inclui um conjunto de credenciais. Neste exemplo, não está encriptado o comando a executar:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ],
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

Mover o **comando para executar** propriedade para o **protegidos** configuração protege a cadeia de caracteres de execução, conforme mostrado no exemplo a seguir:

```json
{
  "apiVersion": "2015-06-15",
  "type": "extensions",
  "name": "config-app",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', concat(variables('vmName'),copyindex()))]"
  ],
  "tags": {
    "displayName": "config-app"
  },
  "properties": {
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
      "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
      ]
    },
    "protectedSettings": {
      "commandToExecute": "[concat('sudo sh config-music.sh ',variables('musicStoreSqlName'), ' ', parameters('adminUsername'), ' ', parameters('sqlAdminPassword'))]"
    }
  }
}
```

### <a name="how-do-agents-and-extensions-get-updated"></a>Como e extensões do agentes atualizadas?

Os agentes e extensões partilham o mesmo mecanismo de atualização. Algumas atualizações não necessitam de regras de firewall adicionais.

Quando uma atualização está disponível, ele só é instalado na VM quando existe uma alteração às extensões e outras alterações de modelo de VM, tais como:

- Discos de dados
- Extensões
- Contentor de diagnóstico de arranque
- Segredos do SO convidado
- Tamanho da VM
- Perfil de rede

Os publicadores disponibilizar as atualizações à regiões em alturas diferentes, portanto, é possível que pode ter VMs em diferentes regiões em versões diferentes.

#### <a name="agent-updates"></a>Atualizações do agente

O agente de VM do Linux contém *código do agente de aprovisionamento* e *código de processamento de extensão* num único pacote, que não podem ser separado. Pode desativar a *aprovisionamento agente* quando pretender aprovisionar no Azure com o cloud-init. Para tal, veja [com o cloud-init](../linux/using-cloud-init.md).

Versões suportadas dos agentes podem utilizar as atualizações automáticas. É o único código que pode ser atualizado a *código de processamento de extensão*, não o código de provisionamento. O *código do agente de aprovisionamento* é o código de execução única.

O *código de processamento de extensão* é responsável por comunicar com os recursos de infraestrutura do Azure e o processamento de como as operações de extensões VM instala, comunicar o estado, a instalação das extensões individuais e removê-los. Atualizações incluem correções de segurança, correções de erros e melhorias para o *código de processamento de extensão*.

Quando o agente está instalado, é criado um daemon principal. Este elemento principal, em seguida, gera um processo filho que é usado para lidar com as extensões. Se uma atualização está disponível para o agente, é transferido, o elemento principal para o processo filho, atualiza-lo, em seguida, reinicia-lo. Deve haver um problema com a atualização, o processo pai reverte para a versão anterior do filho.

O processo principal não pode ser automaticamente atualizado. O principal só pode ser atualizada por uma atualização do pacote de distribuição.

Para verificar qual estiver a executar a versão, verifique o `waagent` da seguinte forma:

```bash
waagent --version
```

O resultado é semelhante ao seguinte exemplo:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Na saída do exemplo anterior, é o principal ou o 'pacote implementado versão' *WALinuxAgent 2.2.17*

O "agente de estado do objetivo" é a versão de atualização automática.

É altamente recomendável que tenha sempre para o agente, a atualização automática [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Não ter isso significa ativada que necessita para manter a atualizar manualmente o agente e não obter correções de bugs e de segurança.

#### <a name="extension-updates"></a>Atualizações de extensão

Está disponível uma atualização de extensão, o agente Linux transferem e atualiza a extensão. Atualizações automáticas de extensão são *pequenas* ou *correção*. Pode optar ativamente por participar no ou opção de desativar as extensões *pequenas* atualiza quando Aprovisiona a extensão. O exemplo seguinte mostra como atualizar automaticamente as versões secundárias num modelo do Resource Manager com *autoUpgradeMinorVersion ": true,'* :

```json
    "publisher": "Microsoft.Azure.Extensions",
    "type": "CustomScript",
    "typeHandlerVersion": "2.0",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-linux/scripts/config-music.sh"
        ]
    },
```

Para obter as correções de erros de versão secundária mais recente, é altamente recomendável que selecione sempre a atualização automática das implementações de extensão. Atualizações de correção que vão ser correções de bugs de segurança ou a chave não podem ser excluídas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificando se a extensão estiver definida com o autoUpgradeMinorVersion numa VM

Pode ver o modelo de VM se a extensão foi aprovisionada com o "autoUpgradeMinorVersion". Para verificar, utilize [show de vm de az](/cli/azure/vm#az-vm-show) e forneça o nome do grupo de recursos e a VM da seguinte forma:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

O resultado de exemplo seguinte mostra que *autoUpgradeMinorVersion* está definida como *verdadeiro*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificar quando ocorreu uma autoUpgradeMinorVersion

Para ver em que ocorreu uma atualização para a extensão, reveja o agente de registos na VM no */var/log/waagent.log*.

No exemplo abaixo, a VM tinha *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9025* instalado. Uma correção estava disponível para *Microsoft.OSTCExtensions.LinuxDiagnostic 2.3.9027*:

```bash
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Expected handler state: enabled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Decide which version to use
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Use version: 2.3.9027
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Current handler state is: NotInstalled
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Download extension package
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Unpack extension package
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Download, message=Download succeeded
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Initialize extension directory
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update settings file: 0.settings
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Disable extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025] Launch command:diagnostic.py -disable
...
INFO Event: name=Microsoft.OSTCExtensions.LinuxDiagnostic, op=Disable, message=Launch command succeeded: diagnostic.py -disable
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Update extension.
INFO [Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027] Launch command:diagnostic.py -update
2017/08/14 20:21:57 LinuxAzureDiagnostic started to handle.
```

## <a name="agent-permissions"></a>Permissões de agente

Para realizar suas tarefas, o agente tem de executar como *raiz*.

## <a name="troubleshoot-vm-extensions"></a>Resolver problemas de extensões de VM

Cada extensão VM pode ter passos específicos para a extensão de resolução de problemas. Por exemplo, quando utiliza a extensão de Script personalizado, detalhes da execução do script podem ser encontrados localmente na VM em que a extensão foi executada. Quaisquer passos de resolução de problemas específicos de extensão são detalhados na documentação de extensão específica.

Os passos de resolução de problemas seguintes aplicam-se a todas as extensões VM.

1. Para verificar o registo do agente do Linux, examinar a atividade quando sua extensão foi a ser aprovisionada no */var/log/waagent.log*

2. Verifique os registos de extensão real para obter mais detalhes no */var/iniciar/azure/\<extensionName >*

3. Verifique a documentação de extensão específica secções para códigos de erro, problemas etc conhecidos de resolução de problemas.

3. Consulte os registos de sistema. Verificar a existência de outras operações que podem ter violado extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso de Gestor do pacote exclusivo.

### <a name="common-reasons-for-extension-failures"></a>Motivos comuns para falhas de extensões

1. As extensões de ter 20 minutos para ser executado (as exceções são as extensões CustomScript, o Chef e o DSC que tenham a 90 minutos). Se a implementação exceder neste momento, está marcada como um tempo limite. A causa isso pode ser devido a poucos recursos VMs, outras VM configurações/tarefas de arranque consumir quantidades elevadas de recursos, lidando simultaneamente e a extensão está a tentar aprovisionar.

2. Pré-requisitos mínimos não cumpridos. Algumas extensões têm dependências em SKUs de VM, como imagens HPC. As extensões podem exigir a determinados requisitos de acesso à rede, como comunicar com o armazenamento do Azure ou serviços públicos. Outros exemplos podem ser o acesso a repositórios de pacote, a ficar sem espaço em disco ou restrições de segurança.

3. Acesso de Gestor do pacote exclusivo. Em alguns casos, pode encontrar uma configuração de VM de longa execução e em conflito, a instalação da extensão onde precisam de acesso exclusivo para o Gestor de pacotes.

### <a name="view-extension-status"></a>Ver o estado de extensão

Depois de executar uma extensão de VM em relação a uma VM, utilize [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) para devolver o estado da extensão da seguinte forma:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

O resultado é semelhante à saída de exemplo seguinte:

```bash
  {
    "name": "customScript",
    "statuses": [
      {
        "code": "ProvisioningState/failed/0",
        "displayStatus": "Provisioning failed",
        "level": "Error",
        "message": "Enable failed: failed to execute command: command terminated with exit status=127\n[stdout]\n\n[stderr]\n/bin/sh: 1: ech: not found\n",
        "time": null
      }
    ],
    "substatuses": null,
    "type": "Microsoft.Azure.Extensions.customScript",
    "typeHandlerVersion": "2.0.6"
  }
```

Estado de execução da extensão também pode ser encontrado no portal do Azure. Para ver o estado de uma extensão, selecione a VM, escolha **extensões**, em seguida, selecione a extensão desejada.

### <a name="rerun-a-vm-extension"></a>Executar novamente uma extensão de VM

Pode haver casos em que uma extensão de VM tem de ser executados novamente. Pode executar novamente uma extensão, removendo-a e, em seguida, executar novamente a extensão com um método de execução da sua preferência. Para remover uma extensão, utilize [delete de extensão az vm](/cli/azure/vm/extension#az-vm-extension-delete) da seguinte forma:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Também pode remover uma extensão no portal do Azure da seguinte forma:

1. Selecione uma VM.
2. Escolher **extensões**.
3. Selecione a extensão desejada.
4. Escolher **desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência de extensão VM comuns

| Nome da extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de Script personalizado para Linux |Executar scripts numa máquina virtual do Azure |[Extensão de Script personalizado para Linux](custom-script-linux.md) |
| Extensão de acesso da VM |Recuperar o acesso a uma máquina virtual do Azure |[Extensão de acesso da VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão do Diagnóstico do Azure |Gerir o diagnóstico do Azure |[Extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso à VM do Azure |Gerir utilizadores e as credenciais |[Extensão de acesso da VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as extensões de VM, consulte [descrição geral de extensões e funcionalidades de máquina virtual do Azure](overview.md).
