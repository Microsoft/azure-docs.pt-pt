---
title: Extensões e funcionalidades azure VM para Linux
description: Saiba quais as extensões disponíveis para máquinas virtuais Azure, agrupando supérum pelo que fornecem ou melhoram.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250520"
---
# <a name="virtual-machine-extensions-and-features-for-linux"></a>Extensões e funcionalidades da máquina virtual para Linux

As extensões da máquina virtual Azure (VM) são pequenas aplicações que fornecem tarefas de configuração pós-implantação e automação em VMs Azure. Por exemplo, se uma máquina virtual necessitar de instalação de software, proteção antivírus ou executar um script no seu interior, pode ser utilizada uma extensão VM. As extensões Azure VM podem ser executadas com os modelos Azure CLI, PowerShell, Azure Resource Manager e o portal Azure. As extensões podem ser agregadas com uma nova implementação vm, ou correr contra qualquer sistema existente.

Este artigo fornece uma visão geral das extensões VM, pré-requisitos para a utilização de extensões VM Azure e orientações sobre como detetar, gerir e remover extensões VM. Este artigo fornece informações generalizadas porque muitas extensões VM estão disponíveis, cada uma com uma configuração potencialmente única. Detalhes específicos da extensão podem ser encontrados em cada documento específico da extensão individual.

## <a name="use-cases-and-samples"></a>Utilizar casos e amostras

Várias extensões Azure VM diferentes estão disponíveis, cada uma com um caso de utilização específico. Alguns exemplos incluem:

- Aplique as configurações do Estado desejado powerShell num VM com a extensão DSC para Linux. Para mais informações, consulte a extensão de [configuração do Estado Pretendido.](https://github.com/Azure/azure-linux-extensions/tree/master/DSC)
- Configure a monitorização de um VM com a extensão VM do Monitor do Microsoft. Para mais informações, consulte [Como monitorizar um VM Linux](../linux/tutorial-monitoring.md).
- Configure a monitorização da sua infraestrutura Azure com a extensão Chef ou Datadog. Para mais informações, consulte o [chef docs](https://docs.chef.io/azure_portal.html) ou [blog Datadog.](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/)

Além de extensões específicas do processo, uma extensão de Script Personalizado está disponível tanto para máquinas virtuais Windows como Linux. A extensão custom Script para Linux permite que qualquer script bash seja executado num VM. Scripts personalizados são úteis para projetar implementações Azure que requerem configuração para além do que a ferramenta nativa Azure pode fornecer. Para mais informações, consulte a extensão do [Script Personalizado Linux VM](custom-script-linux.md).

## <a name="prerequisites"></a>Pré-requisitos

Para lidar com a extensão do VM, precisa do Agente Azure Linux instalado. Algumas extensões individuais têm pré-requisitos, tais como o acesso a recursos ou dependências.

### <a name="azure-vm-agent"></a>Agente da VM do Azure

O agente Azure VM gere interações entre um VM Azure e o controlador de tecido Azure. O agente VM é responsável por muitos aspetos funcionais de implantação e gestão de VMs Azure, incluindo extensões VM de execução. O agente Azure VM está pré-instalado nas imagens do Azure Marketplace e pode ser instalado manualmente em sistemas operativos suportados. O Agente Azure VM do Linux é conhecido como o agente Linux.

Para obter informações sobre sistemas operativos suportados e instruções de instalação, consulte o [agente de máquinas virtuais Azure](agent-linux.md).

#### <a name="supported-agent-versions"></a>Versões de agente suportado

Para proporcionar a melhor experiência possível, existem versões mínimas do agente. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>OSes suportados

O agente Linux funciona em várias OSes, no entanto o quadro de extensões tem um limite para os OSes que se prolongam. Para obter mais informações, consulte [este artigo](https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems
).

Algumas extensões não são suportadas em todos os OSes e podem emitir código de *erro 51, "Os Não Suportado".* Verifique a documentação individual de extensão para obter suporte.

#### <a name="network-access"></a>Acesso à rede

Os pacotes de extensão são descarregados do repositório de extensão de armazenamento Azure, e os uploads de estado de extensão são publicados no Armazenamento Azure. Se utilizar a versão [suportada](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) dos agentes, não precisa de permitir o acesso ao Armazenamento Azure na região vM, assim como pode utilizar o agente para redirecionar a comunicação para o controlador de tecido Azure para comunicações de agentes. Se estiver numa versão não suportada do agente, tem de permitir o acesso de saída ao armazenamento Azure naquela região a partir do VM.

> [!IMPORTANT]
> Se bloqueou o acesso a *168.63.129.16* utilizando a firewall do hóspede, as extensões falham independentemente do acima referido.

Os agentes só podem ser usados para descarregar pacotes de extensão e o estado de reporte. Por exemplo, se uma instalação de extensão precisar de descarregar um script do GitHub (Script Personalizado) ou precisar de acesso ao Armazenamento Azure (Backup Azure), então as portas adicionais de firewall/Network Security Group precisam de ser abertas. As diferentes extensões têm requisitos diferentes, uma vez que são aplicações por si só. Para extensões que requerem acesso ao Armazenamento Azure, pode permitir o acesso utilizando etiquetas de serviço Azure NSG para [armazenamento](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Para redirecionar os pedidos de tráfego do agente, o Agente Linux tem suporte ao servidor proxy. No entanto, este suporte proxy servidor não aplica extensões. Deve configurar cada extensão individual para trabalhar com um representante.

## <a name="discover-vm-extensions"></a>Descubra extensões VM

Estão disponíveis muitas VMs diferentes para utilização com as VMs do Azure. Para ver uma lista completa, utilize a lista de imagens de [extensão az vm](/cli/azure/vm/extension/image#az-vm-extension-image-list). O exemplo seguinte lista todas as extensões disponíveis na localização *westus:*

```azurecli
az vm extension image list --location westus --output table
```

## <a name="run-vm-extensions"></a>Executar extensões VM

As extensões De VM Azure funcionam em VMs existentes, o que é útil quando precisa de fazer alterações de configuração ou recuperar a conectividade num VM já implantado. As extensões VM também podem ser agregadas com implementações de modelos do Gestor de Recursos Azure. Utilizando extensões com modelos de Gestor de Recursos, os VMs Azure podem ser implantados e configurados sem intervenção pós-implantação.

Os seguintes métodos podem ser utilizados para executar uma extensão contra um VM existente.

### <a name="azure-cli"></a>CLI do Azure

As extensões De VM Azure podem ser executadas contra um VM existente com o comando conjunto de [extensão az vm.](/cli/azure/vm/extension#az-vm-extension-set) O exemplo seguinte executa a extensão do Script Personalizado contra um VM chamado *myVM* num grupo de recursos chamado *myResourceGroup*. Substitua o nome do grupo de recursos, nome VM e script para executar (https:\//raw.githubusercontent.com/me/project/hello.sh) com as suas próprias informações. 

```azurecli
az vm extension set `
  --resource-group myResourceGroup `
  --vm-name myVM `
  --name customScript `
  --publisher Microsoft.Azure.Extensions `
  --settings '{"fileUris": ["https://raw.githubusercontent.com/me/project/hello.sh"],"commandToExecute": "./hello.sh"}'
```

Quando a extensão funciona corretamente, a saída é semelhante ao seguinte exemplo:

```bash
info:    Executing command vm extension set
+ Looking up the VM "myVM"
+ Installing extension "CustomScript", VM: "mvVM"
info:    vm extension set command OK
```

### <a name="azure-portal"></a>Portal do Azure

As extensões VM podem ser aplicadas a um VM existente através do portal Azure. Selecione o VM no portal, escolha **extensões**e, em seguida, **selecione Adicionar**. Escolha a extensão desejada na lista de extensões disponíveis e siga as instruções no assistente.

A imagem seguinte mostra a instalação da extensão do Script Personalizado Linux do portal Azure:

![Instale extensão de script personalizada](./media/features-linux/installscriptextensionlinux.png)

### <a name="azure-resource-manager-templates"></a>Modelos do Azure Resource Manager

As extensões VM podem ser adicionadas a um modelo de Gestor de Recursos Azure e executadas com a implementação do modelo. Quando implementa uma extensão com um modelo, pode criar implementações Azure totalmente configuradas. Por exemplo, o seguinte JSON é retirado de um modelo de Gestor de Recursos que implementa um conjunto de VMs equilibrados em carga e uma base de dados Azure SQL, e depois instala uma aplicação .NET Core em cada VM. A extensão VM cuida da instalação do software.

Para mais informações, consulte o modelo completo [do Gestor de Recursos](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-linux).

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

Para obter mais informações sobre a criação de modelos de Gestor de Recursos, consulte [os modelos de Gestor de Recursos Do Azure.](../windows/template-description.md#extensions)

## <a name="secure-vm-extension-data"></a>Dados de extensão VM seguros

Quando executa uma extensão VM, pode ser necessário incluir informações sensíveis, tais como credenciais, nomes de contas de armazenamento e chaves de acesso à conta de armazenamento. Muitas extensões VM incluem uma configuração protegida que encripta dados e apenas os desencripta dentro do VM alvo. Cada extensão tem um esquema de configuração protegido específico, e cada uma é detalhada em documentação específica de extensão.

O exemplo que se segue mostra uma instância da extensão do Script Personalizado para Linux. O comando para executar inclui um conjunto de credenciais. Neste exemplo, o comando a executar não é encriptado:

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

Mover o **comando para executar** a propriedade para a configuração **protegida** segura a cadeia de execução, como mostra o seguinte exemplo:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Como é que os agentes e extensões são atualizados?

Os Agentes e Extensões partilham o mesmo mecanismo de atualização. Algumas atualizações não requerem regras adicionais de firewall.

Quando uma atualização está disponível, só é instalada no VM quando há uma alteração às extensões, e outras alterações do Modelo VM, tais como:

- Discos de dados
- Extensões
- Recipiente de diagnóstico de arranque
- Segredos do Os convidado
- Tamanho da VM
- Perfil de rede

As editoras disponibilizam atualizações para regiões em diferentes momentos, pelo que é possível que possa ter VMs em diferentes regiões em diferentes versões.

#### <a name="agent-updates"></a>Atualizações de agente

O Agente Linux VM contém código de código e tratamento de *extensão* do agente de *provisionamento* numa embalagem, que não pode ser separada. Pode desativar o *Agente de Provisionamento* quando quiser fornecer o Azure utilizando cloud-init. Para isso, veja [o uso de cloud-init](../linux/using-cloud-init.md).

As versões suportadas dos Agentes podem utilizar atualizações automáticas. O único código que pode ser atualizado é o código de manuseamento de *extensões,* não o código de provisionamento. O código do *Agente de Provisionamento* é um código de execução.

O código de tratamento de *extensões* é responsável pela comunicação com o tecido Azure e manuseamento das operações de extensões VM, tais como instalações, estado de reporte, atualização das extensões individuais e remoção das mesmas. As atualizações contêm correções de segurança, correções de erros e melhorias no código de manuseamento de *extensões*.

Quando o agente é instalado, um daemon-mãe é criado. Este progenitor gera então um processo infantil que é usado para lidar com extensões. Se estiver disponível uma atualização para o agente, é descarregada, o progenitor para o processo da criança, atualiza-o e reinicia-o. Caso haja algum problema com a atualização, o processo dos pais volta para a versão infantil anterior.

O processo dos pais não pode ser atualizado automaticamente. O progenitor só pode ser atualizado através de uma atualização do pacote de distro.

Para verificar qual a versão que está a executar, verifique a `waagent` da seguinte forma:

```bash
waagent --version
```

O resultado é semelhante ao seguinte exemplo:

```bash
WALinuxAgent-2.2.17 running on ubuntu 16.04
Python: 3.5.2
Goal state agent: 2.2.18
```

Na saída de exemplo anterior, o progenitor ou "versão implantada em pacote" é *WALinuxAgent-2.2.17*

O 'Agente estatal goal' é a versão de atualização automática.

É altamente recomendável que tenha sempre atualização automática para o agente, [AutoUpdate.Enabled=y](https://docs.microsoft.com/azure/virtual-machines/linux/update-agent). Não ter isto ativado significa que tem de continuar a atualizar manualmente o agente e não obter correções de segurança e bugs.

#### <a name="extension-updates"></a>Atualizações de extensão

Quando uma atualização de extensão está disponível, o Agente Linux descarrega e atualiza a extensão. As atualizações automáticas de extensão são *Menor* ou *Hotfix*. Pode optar por não prorrogar as atualizações *menores* quando fornecer a extensão. O exemplo seguinte mostra como atualizar automaticamente versões menores num modelo de Gestor de Recursos com *autoUpgradeMinorVersion": verdadeiro,':*

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

Para obter as mais recentes correções de erros de desbloqueio, é altamente recomendável que selecione sempre a atualização automática nas suas implementações de extensão. As atualizações de hotfix que transportam correções de segurança ou de erros chave não podem ser optadas.

### <a name="how-to-identify-extension-updates"></a>Como identificar atualizações de extensão

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identificar se a extensão é definida com autoUpgradeMinorVersion num VM

Pode ver no modelo VM se a extensão foi aprovisionada com 'autoUpgradeMinorVersion'. Para verificar, utilize [az vm show](/cli/azure/vm#az-vm-show) e forneça o grupo de recursos e o nome VM da seguinte forma:

```azurecli
az vm show --resource-group myResourceGroup --name myVM
```

A saída de exemplo a seguir mostra que a *versão autoUpgradeMinorVersion* está definida como *verdadeira:*

```json
  "resources": [
    {
      "autoUpgradeMinorVersion": true,
      "forceUpdateTag": null,
      "id": "/subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM/extensions/CustomScriptExtension",
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identificar quando ocorreu uma versão automáticaDaMenor

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

## <a name="agent-permissions"></a>Permissões do agente

Para executar as suas tarefas, o agente precisa de funcionar como *raiz*.

## <a name="troubleshoot-vm-extensions"></a>Extensões VM de resolução de problemas

Cada extensão VM pode ter passos de resolução de problemas específicos da extensão. Por exemplo, quando utiliza a extensão do Script Personalizado, os detalhes de execução do script podem ser encontrados localmente no VM onde a extensão foi executada. Quaisquer etapas específicas de resolução de problemas específicas para a extensão são detalhadas em documentação específica de extensão.

As seguintes etapas de resolução de problemas aplicam-se a todas as extensões VM.

1. Para verificar o Registo do Agente Linux, veja a atividade quando a sua extensão estava a ser aprovisionada em */var/log/waagent.log*

2. Verifique os registos de extensão reais para obter mais detalhes em */var/log/azure/\<extensãoName>*

3. Verifique as secções de resolução de problemas de documentação específicas para a extensão de códigos de erro, questões conhecidas, etc.

3. Olhe para os registos do sistema. Verifique se existem outras operações que possam ter interferido com a extensão, como uma instalação de longa duração de outra aplicação que exigia acesso exclusivo ao gestor de pacotes.

### <a name="common-reasons-for-extension-failures"></a>Razões comuns para falhas na extensão

1. As extensões têm 20 minutos para executar (exceções são as extensões CustomScript, Chef e DSC que têm 90 minutos). Se a sua implantação exceder este tempo, está marcada como um tempo limite. A causa disto pode ser devido a VMs de baixo recurso, outras configurações vm/tarefas de arranque que consomem elevadas quantidades de recursos enquanto a extensão está a tentar fornecer.

2. Pré-requisitos mínimos não cumpridos. Algumas extensões têm dependências de VM SKUs, como imagens HPC. As extensões podem exigir determinados requisitos de acesso em rede, tais como a comunicação ao Armazenamento Azure ou serviços públicos. Outros exemplos poderiam ser o acesso a repositórios de pacotes, ficando sem espaço de disco, ou restrições de segurança.

3. Acesso exclusivo ao gestor de pacotes. Em alguns casos, poderá encontrar uma configuração vm de longa duração e instalação de extensão conflituosa, onde ambos precisam de acesso exclusivo ao gestor do pacote.

### <a name="view-extension-status"></a>Ver estado de extensão

Depois de uma extensão VM ter sido executada contra um VM, utilize [az vm get-instance-view](/cli/azure/vm#az-vm-get-instance-view) para devolver o estado de extensão da seguinte forma:

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

O estado de execução da extensão também pode ser encontrado no portal Azure. Para ver o estado de uma extensão, selecione o VM, escolha **extensões**e, em seguida, selecione a extensão desejada.

### <a name="rerun-a-vm-extension"></a>Reexecutar uma extensão VM

Pode haver casos em que uma extensão VM precisa de ser reexecutada. Pode reproduzir uma extensão removendo-a e, em seguida, reexecutar a extensão com um método de execução à sua escolha. Para remover uma extensão, utilize a [extensão az vm a eliminar](/cli/azure/vm/extension#az-vm-extension-delete) da seguinte forma:

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
4. Escolha **desinstalar**.

## <a name="common-vm-extension-reference"></a>Referência de extensão VM comum

| Nome de extensão | Descrição | Mais informações |
| --- | --- | --- |
| Extensão de script personalizado para Linux |Executar scripts contra uma máquina virtual Azure |[Extensão de script personalizado para Linux](custom-script-linux.md) |
| Extensão de acesso da VM |Recuperar o acesso a uma máquina virtual Azure |[Extensão de acesso da VM](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) |
| Extensão do Diagnóstico do Azure |Gerir diagnósticos azure |[Extensão de Diagnóstico Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Extensão de acesso Azure VM |Gerir utilizadores e credenciais |[Extensão de acesso VM para Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre extensões VM, consulte [extensões de máquinas virtuais Azure e funcionalidades de visão geral](overview.md).
