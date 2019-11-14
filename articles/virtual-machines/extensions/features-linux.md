---
title: Recursos e extensões de VM do Azure para Linux
description: Saiba quais extensões estão disponíveis para máquinas virtuais do Azure, agrupadas pelo que elas fornecem ou aperfeiçoadas.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 52f5d0ec-8f75-49e7-9e15-88d46b420e63
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.openlocfilehash: 67df46742be52b03bd91af19654fbfac5df29646
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073755"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Recursos e extensões de máquina virtual para Linux

As extensões de VM (máquina virtual) do Azure são aplicativos pequenos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure. Por exemplo, se uma máquina virtual requer instalação de software, proteção antivírus ou para executar um script dentro dela, uma extensão de VM pode ser usada. As extensões de VM do Azure podem ser executadas com o CLI do Azure, o PowerShell, os modelos de Azure Resource Manager e o portal do Azure. As extensões podem ser agrupadas com uma nova implantação de VM ou executadas em qualquer sistema existente.

Este artigo fornece uma visão geral das extensões de VM, pré-requisitos para usar extensões de VM do Azure e orientação sobre como detectar, gerenciar e remover extensões de VM. Este artigo fornece informações generalizadas porque muitas extensões de VM estão disponíveis, cada uma com uma configuração potencialmente exclusiva. Detalhes específicos da extensão podem ser encontrados em cada documento específico para a extensão individual.

## <a name="use-cases-and-samples"></a>Casos de uso e amostras

Várias extensões de VM do Azure diferentes estão disponíveis, cada uma com um caso de uso específico. Alguns exemplos incluem:

- Aplique as configurações de estado desejado do PowerShell a uma VM com a extensão de DSC para Linux. Para obter mais informações, consulte [extensão de configuração de estado desejado do Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure o monitoramento de uma VM com a extensão de VM Microsoft Monitoring Agent. Para obter mais informações, consulte [como monitorar uma VM Linux](../linux/tutorial-monitoring.md).
- Configure o monitoramento de sua infraestrutura do Azure com a extensão chefe ou Datadog. Para obter mais informações, consulte o blog [chefe docs](https://docs.chef.io/azure_portal.html) ou [Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).

Além das extensões específicas do processo, uma extensão de script personalizado está disponível para máquinas virtuais Windows e Linux. A extensão de script personalizado para Linux permite que qualquer script de bash seja executado em uma VM. Os scripts personalizados são úteis para criar implantações do Azure que exigem configuração além das ferramentas nativas do Azure que podem ser fornecidas. Para obter mais informações, consulte [extensão de script personalizado da VM do Linux](custom-script-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão na VM, você precisará do agente Linux do Azure instalado. Algumas extensões individuais têm pré-requisitos, como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente de VM do Azure gerencia as interações entre uma VM do Azure e o controlador de malha do Azure. O agente de VM é responsável por muitos aspectos funcionais da implantação e do gerenciamento de VMs do Azure, incluindo a execução de extensões de VM. O agente de VM do Azure é pré-instalado em imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operacionais com suporte. O agente de VM do Azure para Linux é conhecido como agente do Linux.

Para obter informações sobre sistemas operacionais e instruções de instalação com suporte, consulte [agente de máquina virtual do Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versões de agente com suporte

Para fornecer a melhor experiência possível, há versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Sistemas operacionais com suporte

O agente do Linux é executado em vários SOS, no entanto, o Framework de extensões tem um limite para os SOS que são extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não têm suporte em todos os sistemas operacionais e podem emitir o *código de erro 51, ' sistema operacional sem suporte '* . Verifique a documentação de extensão individual para obter suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são baixados do repositório de extensões de armazenamento do Azure, e os carregamentos de status de extensão são postados no armazenamento do Azure. Se você usar a versão [com suporte](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, não será necessário permitir o acesso ao armazenamento do Azure na região da VM, uma vez que o pode usar o agente para redirecionar a comunicação para o controlador de malha do Azure para comunicações do agente. Se você estiver em uma versão sem suporte do agente, precisará permitir o acesso de saída ao armazenamento do Azure nessa região da VM.

> [!IMPORTANT]
> Se você tiver bloqueado o acesso ao *168.63.129.16* usando o firewall convidado, as extensões falharão independentemente das anteriores.

Os agentes só podem ser usados para baixar pacotes de extensão e status de relatório. Por exemplo, se uma instalação de extensão precisar baixar um script do GitHub (script personalizado) ou precisar de acesso ao armazenamento do Azure (backup do Azure), as portas de grupo de segurança de rede/firewall adicionais precisarão ser abertas. Extensões diferentes têm requisitos diferentes, pois são aplicativos por conta própria. Para extensões que exigem acesso ao armazenamento do Azure, você pode permitir o acesso usando as marcas do serviço NSG do Azure para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Para redirecionar solicitações de tráfego de agente, o agente Linux tem suporte para servidor proxy. No entanto, esse suporte ao servidor proxy não aplica extensões. Você deve configurar cada extensão individual para trabalhar com um proxy.

## <a name="discover-vm-extensions"></a>Descobrir extensões de VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, use [AZ VM Extension Image List](/cli/azure/vm/extension/image#az-vm-extension-image-list). O exemplo a seguir lista todas as extensões disponíveis no local *westus* :

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Executar extensões de VM

As extensões de VM do Azure são executadas em VMs existentes, o que é útil quando você precisa fazer alterações de configuração ou recuperar a conectividade em uma VM já implantada. As extensões de VM também podem ser agrupadas com Azure Resource Manager implantações de modelo. Usando extensões com modelos do Resource Manager, as VMs do Azure podem ser implantadas e configuradas sem intervenção pós-implantação.

Os métodos a seguir podem ser usados para executar uma extensão em uma VM existente.

### <a name="azure-cli"></a>CLI do Azure

As extensões de VM do Azure podem ser executadas em uma VM existente com o comando [AZ VM Extension Set](/cli/azure/vm/extension#az-vm-extension-set) . O exemplo a seguir executa a extensão de script personalizado em uma VM chamada *myVM* em um grupo de recursos chamado *MyResource*Group. Substitua o nome do grupo de recursos de exemplo, o nome da VM e o script a ser executado (https:\//raw.githubusercontent.com/me/project/hello.sh) com suas próprias informações. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Quando a extensão é executada corretamente, a saída é semelhante ao exemplo a seguir:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

As extensões de VM podem ser aplicadas a uma VM existente por meio do portal do Azure. Selecione a VM no portal, escolha **extensões**e, em seguida, selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

A imagem a seguir mostra a instalação da extensão de script personalizado do Linux do portal do Azure:

![Instalar extensão de script personalizado](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As extensões de VM podem ser adicionadas a um modelo de Azure Resource Manager e executadas com a implantação do modelo. Ao implantar uma extensão com um modelo, você pode criar implantações do Azure totalmente configuradas. Por exemplo, o JSON a seguir é obtido de um modelo do Resource Manager que implanta um conjunto de VMs com balanceamento de carga e um banco de dados SQL do Azure e, em seguida, instala um aplicativo .NET Core em cada VM. A extensão de VM cuida da instalação do software.

Para obter mais informações, consulte o [modelo completo do Resource Manager](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Para obter mais informações sobre como criar modelos do Resource Manager, consulte Criando [modelos de Azure Resource Manager](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Proteger dados de extensão de VM

Quando você executa uma extensão de VM, pode ser necessário incluir informações confidenciais, como credenciais, nomes de conta de armazenamento e chaves de acesso da conta de armazenamento. Muitas extensões de VM incluem uma configuração protegida que criptografa dados e apenas descriptografa-os dentro da VM de destino. Cada extensão tem um esquema de configuração protegida específico e cada uma é detalhada na documentação específica da extensão.

O exemplo a seguir mostra uma instância da extensão de script personalizado para Linux. O comando a ser executado inclui um conjunto de credenciais. Neste exemplo, o comando a ser executado não é criptografado:

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

Mover o **comando para executar** a propriedade para a configuração **protegida** protege a cadeia de caracteres de execução, conforme mostrado no exemplo a seguir:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Como os agentes e as extensões são atualizados?

Os agentes e as extensões compartilham o mesmo mecanismo de atualização. Algumas atualizações não exigem regras de firewall adicionais.

Quando uma atualização está disponível, ela só é instalada na VM quando há uma alteração nas extensões e outras alterações de modelo de VM, como:

- Discos de dados
- Extensões
- Contêiner de diagnóstico de inicialização
- Segredos do SO convidado
- Tamanho da VM
- Perfil de rede

Os Publicadores disponibilizam atualizações para regiões em momentos diferentes, para que seja possível ter VMs em diferentes regiões em diferentes versões.

#### <a name="agent-updates"></a>Atualizações do agente

O agente de VM do Linux contém o código do *agente de provisionamento* e o código de *manipulação de extensão* em um pacote, que não pode ser separado. Você pode desabilitar o *agente de provisionamento* quando desejar provisionar no Azure usando o Cloud-init. Para fazer isso, consulte [usando Cloud-init](../linux/using-cloud-init.md).

As versões com suporte dos agentes podem usar as atualizações automáticas. O único código que pode ser atualizado é o *código de manipulação de extensão*, não o código de provisionamento. O *código do agente de provisionamento* é um código de execução única.

O *código de manipulação de extensão* é responsável pela comunicação com a malha do Azure e pela manipulação das operações de extensões de VM, como instalações, status de relatórios, atualização de extensões individuais e remoção delas. As atualizações contêm correções de segurança, correções de bugs e aprimoramentos no *código de manipulação de extensão*.

Quando o agente é instalado, um daemon pai é criado. Esse pai então gera um processo filho que é usado para manipular extensões. Se uma atualização estiver disponível para o agente, ela será baixada, o pai interromperá o processo filho, o atualizará e, em seguida, o reiniciará. Se houver um problema com a atualização, o processo pai será revertido para a versão filho anterior.

O processo pai não pode ser atualizado automaticamente. O pai só pode ser atualizado por uma atualização de pacote distribuição.

Para verificar qual versão está sendo executada, verifique o `waagent` da seguinte maneira:

```bash
waagent --version
```

O resultado é semelhante ao seguinte exemplo:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Na saída de exemplo anterior, o pai ou "pacote implantado versão" é *WALinuxAgent-2.2.17*

O ' agente de estado de meta ' é a versão de atualização automática.

É altamente recomendável que você sempre tenha a atualização automática para o agente, [AutoUpdate. Enabled = y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Não ter essa habilitação significa que você precisa manter a atualização manual do agente e não obter correções de bug e de segurança.

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização de extensão está disponível, o agente do Linux baixa e atualiza a extensão. As atualizações de extensão automáticas são *secundária* ou de *hotfix*. Você pode aceitar ou recusar atualizações *secundárias* de extensões ao provisionar a extensão. O exemplo a seguir mostra como atualizar as versões secundárias automaticamente em um modelo do Resource Manager com *autoUpgradeMinorVersion ": true,"* :

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

Para obter as correções mais recentes do bug de lançamento secundário, é altamente recomendável que você sempre Selecione atualização automática em suas implantações de extensão. Atualizações de hotfix que contêm correções de bug de chave ou segurança não podem ser recusadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão está definida com autoUpgradeMinorVersion em uma VM

Você poderá ver no modelo de VM se a extensão tiver sido provisionada com ' autoUpgradeMinorVersion '. Para verificar, use [AZ VM show](/cli/azure/vm#az-vm-show) e forneça o grupo de recursos e o nome da VM da seguinte maneira:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

A saída de exemplo a seguir mostra que *autoUpgradeMinorVersion* está definido como *true*:

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificando quando ocorreu um autoUpgradeMinorVersion

Para ver quando uma atualização da extensão ocorreu, examine os logs de agente na VM em */var/log/waagent.log*.

No exemplo a seguir, a VM tinha *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9025* instalado. Um hotfix estava disponível para *Microsoft. OSTCExtensions. LinuxDiagnostic-2.3.9027*:

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

## <a name="agent-permissions"></a>Permissões do agente

Para executar suas tarefas, o agente precisa ser executado como *raiz*.

## <a name="troubleshoot-vm-extensions"></a>Solucionar problemas de extensões de VM

Cada extensão de VM pode ter etapas de solução de problemas específicas para a extensão. Por exemplo, quando você usa a extensão de script personalizado, os detalhes de execução de script podem ser encontrados localmente na VM em que a extensão foi executada. Qualquer etapa de solução de problemas específica de extensão é detalhada na documentação específica da extensão.

As etapas de solução de problemas a seguir se aplicam a todas as extensões de VM.

1. Para verificar o log do agente do Linux, examine a atividade quando sua extensão estava sendo provisionada no */var/log/waagent.log*

2. Verifique os logs de extensão reais para obter mais detalhes em */var/log/azure/\<extensionname >*

3. Verifique as seções de solução de problemas de documentação específica da extensão para obter códigos de erro, problemas conhecidos etc.

3. Examine os logs do sistema. Verifique se há outras operações que possam interferir na extensão, como uma instalação de longa execução de outro aplicativo que exigia acesso exclusivo ao Gerenciador de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Motivos comuns para falhas de extensão

1. As extensões têm 20 minutos para serem executadas (as exceções são as extensões CustomScript, chefe e DSC que têm 90 minutos). Se sua implantação exceder esse tempo, ela será marcada como um tempo limite. A causa disso pode ser devido a pequenas VMs de recursos, outras configurações de VM/inicialização de tarefas que consomem grandes quantidades de recursos, enquanto a extensão está tentando provisionar.

2. Pré-requisitos mínimos não atendidos. Algumas extensões têm dependências em SKUs de VM, como imagens do HPC. As extensões podem exigir certos requisitos de acesso à rede, como a comunicação com o armazenamento do Azure ou serviços públicos. Outros exemplos podem ser acesso a repositórios de pacotes, ficando sem espaço em disco ou restrições de segurança.

3. Acesso exclusivo do Gerenciador de pacotes. Em alguns casos, você pode encontrar uma configuração de VM de longa execução e uma instalação de extensão em conflito, onde ambas precisam de acesso exclusivo ao Gerenciador de pacotes.

### <a name="view-extension-status"></a>Exibir status da extensão

Depois que uma extensão de VM for executada em uma VM, use [AZ VM Get-Instance-View](/cli/azure/vm#az-vm-get-instance-view) para retornar o status da extensão da seguinte maneira:

```azurecli
az vm get-instance-view \
    --resource-group rgName \
    --name myVM \
    --query "instanceView.extensions"
```

A saída é semelhante à seguinte saída de exemplo:

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

O status de execução da extensão também pode ser encontrado no portal do Azure. Para exibir o status de uma extensão, selecione a VM, escolha **extensões**e, em seguida, selecione a extensão desejada.

### <a name="rerun-a-vm-extension"></a>Executar novamente uma extensão de VM

Pode haver casos em que uma extensão de VM precisa ser executada novamente. Você pode executar novamente uma extensão removendo-a e, em seguida, executando novamente a extensão com um método de execução de sua escolha. Para remover uma extensão, use [AZ VM Extension Delete](/cli/azure/vm/extension#az-vm-extension-delete) da seguinte maneira:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Você também pode remover uma extensão na portal do Azure da seguinte maneira:

1. Selecione uma VM.
2. Escolha **extensões**.
3. Selecione a extensão desejada.
4. Escolha **desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência de extensão de VM comum

| Nome da extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de script personalizado para Linux |Executar scripts em uma máquina virtual do Azure |[Extensão de script personalizado para Linux](custom-script-linux.md) |
| Extensão de acesso da VM |Restabelecer o acesso a uma máquina virtual do Azure |[Extensão de acesso da VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão do Diagnóstico do Azure |Gerenciar Diagnóstico do Azure |[Extensão de Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso à VM do Azure |Gerenciar usuários e credenciais |[Extensão de acesso à VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre extensões de VM, consulte [visão geral de extensões e recursos de máquinas virtuais do Azure](overview.md).
