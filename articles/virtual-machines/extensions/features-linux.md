---
title: Extensões e funcionalidades do Azure VM para o Linux
description: Saiba quais as extensões disponíveis para máquinas virtuais Azure no Linux, agrupadas pelo que fornecem ou melhoram.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 03/30/2018
ms.openlocfilehash: b6a834ede381ee319d3ff7678f49febd11e34a5d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559776"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensões e funcionalidades de máquina virtual para Linux

As extensões de máquina virtual (VM) do Azure são pequenas aplicações que proporcionam tarefas de automação e configuração pós-implementação nas VMs do Azure. Por exemplo, se uma máquina virtual exigir a instalação de software, a proteção antivírus ou a execução de um script, poderá ser utilizada uma extensão de VM. As extensões de VM do Azure podem ser executadas na CLI do Azure, no PowerShell, nos modelos do Azure Resource Manager e no portal do Azure. As extensões podem ser agrupadas com uma nova implementação da VM ou executadas em qualquer sistema existente.

Este artigo fornece uma visão geral das extensões VM, pré-requisitos para a utilização de extensões VM Azure, e orientações sobre como detetar, gerir e remover extensões VM. Este artigo fornece informações generalizadas porque muitas extensões VM estão disponíveis, cada uma com uma configuração potencialmente única. Detalhes específicos da extensão podem ser encontrados em cada documento específico da extensão individual.

## <a name="use-cases-and-samples"></a>Utilizar caixas e amostras

Estão disponíveis várias extensões VM Azure diferentes, cada uma com um caso de utilização específico. Alguns exemplos incluem:

- Aplicar as configurações do Estado Desejado PowerShell a um VM com a extensão DSC para Linux. Para obter mais informações, consulte [a extensão de configuração do Estado Desejado Azure](https://github.com/Azure/azure-linux-extensions/tree/master/DSC).
- Configure a monitorização de um VM com a extensão VM do Agente de Monitorização da Microsoft. Para mais informações, consulte [Como monitorizar um Linux VM](/previous-versions/azure/virtual-machines/linux/tutorial-monitor).
- Configure a monitorização da sua infraestrutura Azure com a extensão do Chef ou Datadog. Para mais informações, consulte o [chef docs](https://docs.chef.io/azure_portal.html) ou [o blog Datadog.](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)

Além de extensões específicas do processo, está disponível uma extensão de Script Personalizado para máquinas virtuais Windows e Linux. A extensão de Script Personalizado para Linux permite que qualquer script Bash seja executado em um VM. Scripts personalizados são úteis para projetar implementações Azure que requerem configuração além do que a ferramenta nativa Azure pode fornecer. Para mais informações, consulte a [extensão do Script Personalizado Linux VM](custom-script-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão do VM, precisa do Agente Azure Linux instalado. Algumas extensões individuais têm pré-requisitos, tais como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente Azure VM gere interações entre um Azure VM e o controlador de tecido Azure. O agente VM é responsável por muitos aspetos funcionais da implantação e gestão de VMs Azure, incluindo extensões VM em execução. O agente Azure VM está pré-instalado em imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operativos suportados. O agente Azure VM da Linux é conhecido como o agente Linux.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte [o agente de máquinas virtuais Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versões de agente apoiado

Para proporcionar a melhor experiência possível, existem versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>OSes apoiados

O agente Linux funciona em várias OSes, no entanto o quadro de extensões tem um limite para as OSes que extensões. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não são suportadas em todas as OSes e podem emitir *código de erro 51, "OS não suportados".* Verifique a documentação de extensão individual para obter suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são descarregados do repositório de extensão de armazenamento Azure, e os uploads do estado de extensão são publicados no Azure Storage. Se utilizar a versão [suportada](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, não precisa de permitir o acesso ao Azure Storage na região VM, como pode utilizar o agente para redirecionar a comunicação para o controlador de tecido Azure para comunicações de agentes. Se estiver numa versão não suportada do agente, tem de permitir o acesso de saída ao armazenamento Azure naquela região a partir do VM.

> [!IMPORTANT]
> Se bloqueou o acesso a *168.63.129.16* utilizando a firewall do hóspede, então as extensões falham independentemente do acima.

Os agentes só podem ser usados para descarregar pacotes de extensão e estado de reporte. Por exemplo, se uma instalação de extensão precisar de descarregar um script do GitHub (Script Personalizado) ou precisar de acesso ao Azure Storage (Azure Backup), então devem ser abertas portas adicionais do Firewall/Network Security Group. As extensões diferentes têm requisitos diferentes, uma vez que são aplicações por si só. Para extensões que requerem acesso ao Azure Storage, pode permitir o acesso usando tags de serviço Azure NSG para [armazenamento.](../../virtual-network/network-security-groups-overview.md#service-tags)

Para redirecionar os pedidos de tráfego de agentes, o Agente Linux tem suporte para servidor de procuração. No entanto, este suporte ao servidor proxy não aplica extensões. Deve configurar cada extensão individual para trabalhar com um representante.

## <a name="discover-vm-extensions"></a>Descubra extensões VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, utilize [a lista de imagens de extensão az vm](/cli/azure/vm/extension/image#az-vm-extension-image-list). O exemplo a seguir enumera todas as extensões disponíveis na localização *westus:*

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Executar extensões VM

As extensões Azure VM funcionam em VMs existentes, o que é útil quando é necessário fazer alterações de configuração ou recuperar a conectividade num VM já implantado. As extensões VM também podem ser agregadas com implementações de modelos do Azure Resource Manager. Ao utilizar extensões com modelos de Gestor de Recursos, os VMs Azure podem ser implantados e configurados sem intervenção pós-implantação.

Os seguintes métodos podem ser utilizados para executar uma extensão contra um VM existente.

### <a name="azure-cli"></a>CLI do Azure

As extensões Azure VM podem ser executadas contra um VM existente com o comando [conjunto de extensão az vm.](/cli/azure/vm/extension#az-vm-extension-set) O exemplo seguinte executa a extensão de Script Personalizado contra um VM nomeado *myVM* em um grupo de recursos chamado *myResourceGroup*. Substitua o nome do grupo de recursos de exemplo, nome VM e script para executar (https: \/ /raw.githubusercontent.com/me/project/hello.sh) com as suas próprias informações. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Quando a extensão é correta, a saída é semelhante ao seguinte exemplo:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

As extensões VM podem ser aplicadas a um VM existente através do portal Azure. Selecione o VM no portal, escolha **Extensões** e, em seguida, selecione **Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

A imagem a seguir mostra a instalação da extensão de Script Personalizado Linux a partir do portal Azure:

![Instale extensão de script personalizado](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As extensões VM podem ser adicionadas a um modelo de Gestor de Recursos Azure e executadas com a implementação do modelo. Quando implementar uma extensão com um modelo, pode criar implementações Azure totalmente configuradas. Por exemplo, o seguinte JSON é retirado de um modelo de Gestor de Recursos que implementa um conjunto de VMs equilibrados de carga e Base de Dados Azure SQL, em seguida, instala uma aplicação .NET Core em cada VM. A extensão VM cuida da instalação do software.

Para obter mais informações, consulte o modelo completo [do Gestor de Recursos.](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux)

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

Para obter mais informações sobre a criação de modelos de Gestor de Recursos, consulte [os modelos do Gestor de Recursos Azure.](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Dados de extensão VM seguros

Quando executar uma extensão VM, pode ser necessário incluir informações sensíveis, tais como credenciais, nomes de contas de armazenamento e chaves de acesso à conta de armazenamento. Muitas extensões VM incluem uma configuração protegida que encripta dados e apenas o desencripta dentro do VM alvo. Cada extensão tem um esquema de configuração protegido específico, e cada uma é detalhada em documentação específica de extensão.

O exemplo a seguir mostra uma instância da extensão de Script Personalizado para Linux. O comando para executar inclui um conjunto de credenciais. Neste exemplo, o comando a executar não é encriptado:

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

Mover o **comando para executar a** propriedade para a configuração **protegida** assegura a cadeia de execução, como mostra o exemplo seguinte:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Como é que os agentes e as extensões são atualizados?

Os agentes e extensões partilham o mesmo mecanismo de atualização. Algumas atualizações não requerem regras adicionais de firewall.

Quando uma atualização está disponível, só é instalada no VM quando há uma alteração nas extensões, e outras alterações do Modelo VM tais como:

- Discos de dados
- Extensões
- Recipiente de diagnóstico de arranque
- Os segredos do OS convidado
- Tamanho da VM
- Perfil de rede

Os editores disponibilizam atualizações às regiões em momentos diferentes, pelo que é possível que possa ter VMs em diferentes regiões em diferentes versões.

#### <a name="agent-updates"></a>Atualizações de agentes

O Agente VM Linux contém *código de agente de provisionamento* e código de *manuseamento de extensões*  num único pacote, que não pode ser separado. Pode desativar o *Agente de Provisionamento* quando pretender provisão no Azure utilizando o cloud-init. Para isso, consulte [usando a cloud-init](../linux/using-cloud-init.md).

As versões suportadas dos Agentes podem utilizar atualizações automáticas. O único código que pode ser atualizado é o *código de tratamento de extensões,* não o código de provisionamento. O *código do Agente De provisionamento* é um código de execução.

O *código de tratamento de extensões* é responsável pela comunicação com o tecido Azure e pelo manuseamento das operações de extensões VM, tais como instalações, estado de reporte, atualização das extensões individuais e remoção das extensões. As atualizações contêm correções de segurança, correções de erros e melhorias no *código de tratamento de extensões*.

Quando o agente é instalado, um daemon-mãe é criado. Este progenitor gera então um processo infantil que é usado para lidar com extensões. Se uma atualização estiver disponível para o agente, é descarregada, o progenitor para o processo da criança, atualiza-o e reinicia-o. Caso haja algum problema com a atualização, o processo dos pais volta para a versão anterior do filho.

O processo dos pais não pode ser atualizado automaticamente. O progenitor só pode ser atualizado através de uma atualização de pacote distro.

Para verificar a versão que está a executar, verifique o `waagent` seguinte:

```bash
waagent --version
```

O resultado é semelhante ao seguinte exemplo:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Na saída de exemplo anterior, a versão principal ou "versão distribuída por pacotes" é *WALinuxAgent-2.2.17*

O 'Agente estatal goal' é a versão de atualização automática.

Recomenda-se vivamente que tenha sempre uma atualização automática para o agente, [AutoUpdate.Enabled=y](./update-linux-agent.md). Não ter isto ativado significa que precisa de continuar a atualizar manualmente o agente e não obter correções de erros e segurança.

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização de extensão está disponível, o Agente Linux descarrega e atualiza a extensão. As atualizações automáticas de extensão são *minor* ou *Hotfix*. Pode optar por não fazer extensões *Atualizações menores* quando forres a extensão. O exemplo a seguir mostra como atualizar automaticamente as versões menores num modelo de Gestor de Recursos com *automaticamenteUpgradeMinorVersion": verdadeiro,»*

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

Para obter as mais recentes correções de erro de desbloqueio menores, é altamente recomendável que selecione sempre a atualização automática nas suas implementações de extensão. As atualizações de hotfix que transportam correções de segurança ou de erros chave não podem ser desativadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão é definida com autoUpgradeMinorVersion em um VM

Pode ver no modelo VM se a extensão foi prevista com "autoUpgradeMinorVersion". Para verificar, use [az vm show](/cli/azure/vm#az-vm-show) e forneça o grupo de recursos e o nome VM da seguinte forma:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

A saída de exemplo a seguir mostra que *a autoUpgradeMinorVersion* é definida como *verdadeira:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificação quando ocorreu uma autoUpgradeMinorVersion

Para ver quando ocorreu uma atualização da extensão, reveja os registos do agente no VM em */var/log/waagent.log*.

No exemplo abaixo, o VM tinha *microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9025* instalado. Um hotfix estava disponível para *Microsoft.OSTCExtensions.LinuxDiagnostic-2.3.9027*:

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

Para executar as suas tarefas, o agente tem de funcionar como *raiz.*

## <a name="troubleshoot-vm-extensions"></a>Extensões VM de resolução de problemas

Cada extensão VM pode ter etapas de resolução de problemas específicas da extensão. Por exemplo, quando utiliza a extensão do Script Personalizado, os detalhes da execução do script podem ser encontrados localmente no VM onde a extensão foi executada. Quaisquer etapas específicas de resolução de problemas de extensão são detalhadas em documentação específica de extensão.

As seguintes etapas de resolução de problemas aplicam-se a todas as extensões de VM.

1. Para verificar o Registo do Agente Linux, consulte a atividade quando a sua extensão estava a ser disponibilizada em */var/log/waagent.log*

2. Verifique os registos de extensão reais para obter mais detalhes em */var/log/azure/ \<extensionName>*

3. Verifique se secções de resolução de problemas de documentação específica de extensão para códigos de erro, problemas conhecidos, etc.

3. Olhe para os registos do sistema. Verifique outras operações que possam ter interferido com a extensão, como uma instalação de longa duração de outra aplicação que exigia acesso exclusivo ao gestor de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Razões comuns para falhas de extensão

1. As extensões têm 20 minutos para executar (exceções são as extensões CustomScript, Chef e DSC que têm 90 minutos). Se a sua implantação exceder desta vez, está marcada como um intervalo. A causa deste pode ser devido a VMs de baixo recurso, outras configurações VM/tarefas de arranque que consomem elevadas quantidades de recursos enquanto a extensão está a tentar proviser.

2. Pré-requisitos mínimos não cumpridos. Algumas extensões têm dependências de SKUs VM, como imagens HPC. As extensões podem exigir certos requisitos de acesso em rede, tais como a comunicação com o Azure Storage ou serviços públicos. Outros exemplos poderiam ser o acesso a repositórios de pacotes, ficando sem espaço em disco ou restrições de segurança.

3. Acesso exclusivo do gestor de pacotes. Em alguns casos, poderá encontrar uma configuração VM de longa duração e instalação de extensão conflituosa, onde ambos precisam de acesso exclusivo ao gestor do pacote.

### <a name="view-extension-status"></a>Ver estado de extensão

Depois de uma extensão VM ter sido executada contra um VM, use [a visão de instância az vm](/cli/azure/vm#az-vm-get-instance-view) para devolver o estado da extensão da seguinte forma:

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

O estado de execução da extensão também pode ser encontrado no portal Azure. Para visualizar o estado de uma extensão, selecione o VM, escolha **Extensões** e, em seguida, selecione a extensão desejada.

### <a name="rerun-a-vm-extension"></a>Reensundirem uma extensão VM

Pode haver casos em que uma extensão VM precisa de ser reexecutada. Pode refazer uma extensão removendo-a e, em seguida, repetindo a extensão com um método de execução à sua escolha. Para remover uma extensão, utilize [a extensão az vm eliminar](/cli/azure/vm/extension#az-vm-extension-delete) da seguinte forma:

```azurecli
az vm extension delete \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name customScript
```

Também pode remover uma extensão no portal Azure da seguinte forma:

1. Selecione um VM.
2. Escolha **extensões**.
3. Selecione a extensão desejada.
4. Escolha **Desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência comum de extensão VM

| Nome de extensão | Description | Mais informações |
| --- | --- | --- |
| Extensão de script personalizado para Linux |Executar scripts contra uma máquina virtual Azure |[Extensão de script personalizado para Linux](custom-script-linux.md) |
| Extensão de acesso da VM |Recuperar o acesso a uma máquina virtual Azure |[Extensão de acesso da VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão do Diagnóstico do Azure |Gerir diagnósticos Azure |[Extensão do Diagnóstico do Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso Azure VM |Gerir utilizadores e credenciais |[Extensão de acesso VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as extensões de VM, veja [Descrição geral das funcionalidades e extensões de máquinas virtuais do Azure](overview.md).