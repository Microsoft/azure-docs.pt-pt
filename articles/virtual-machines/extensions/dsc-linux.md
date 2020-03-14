---
title: Extensão Azure DSC para Linux
description: Instala pacotes OMI e DSC para permitir configurar um VM Azure Linux utilizando a Configuração do Estado Desejado.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 2f04b5ecb2019a77bbb38e97c3869cc0a9447955
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79250624"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão DSC para Linux (Microsoft.OSTCExtensions.DSCForLinux)

A Configuração do Estado Desejada (DSC) é uma plataforma de gestão que pode utilizar para gerir a sua infraestrutura de TI e desenvolvimento com a configuração como código.

> [!NOTE]
> A extensão DSC para linux e a [extensão da máquina virtual Azure Monitor para linux](/azure/virtual-machines/extensions/oms-linux) apresentam atualmente um conflito e não são suportadas numa configuração lado a lado. Não use as duas soluções juntas no mesmo VM.

A extensão DSCForLinux é publicada e suportada pela Microsoft. A extensão instala o agente OMI e DSC em máquinas virtuais Azure. A extensão dSC também pode fazer as seguintes ações:


- Registe o VM Linux numa conta Azure Automation para retirar as configurações do serviço De automação Azure (Register ExtensionAction).
- Empurre as configurações mof para o Linux VM (Push ExtensionAction).
- Aplique a configuração meta MOF no VM Linux para configurar um servidor de puxar para puxar a configuração do nó (Pull ExtensionAction).
- Instale módulos DSC personalizados no Linux VM (Instalação ExtensionAction).
- Remova os módulos DSC personalizados do VM Linux (Remover a Extensão).

 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão DSC Linux suporta todas as [distribuições Linux endossadas no Azure,](/azure/virtual-machines/linux/endorsed-distros) exceto:

| Distribuição | Versão |
|---|---|
| Debian | Todas as versões |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Conectividade Internet

A extensão DSCForLinux requer que a máquina virtual alvo seja ligada à internet. Por exemplo, a extensão Do Registo requer conectividade com o serviço de Automação. Para outras ações como Pull, Pull, Install requer conectividade com o Armazenamento Azure e o GitHub. Depende das definições fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="public-configuration"></a>Configuração pública

Aqui estão todos os parâmetros de configuração pública suportados:

* `FileUri`: (opcional, corda) O uri do ficheiro MOF, ficheiro meta MOF ou ficheiro zip de recurso personalizado.
* `ResourceName`: (opcional, corda) O nome do módulo de recursos personalizados.
* `ExtensionAction`: (opcional, corda) Especifica o que uma extensão faz. Os valores válidos são Registar, Empurrar, Puxar, Instalar e Remover. Se não especificado, é considerado uma Ação push por padrão.
* `NodeConfigurationName`: (opcional, corda) O nome de uma configuração do nó a aplicar.
* `RefreshFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) que o DSC tenta obter a configuração do servidor de puxar. 
       Se a configuração no servidor de puxar difere da atual no nó alvo, é copiada para a loja pendente e aplicada.
* `ConfigurationMode`: (opcional, corda) Especifica como o DSC deve aplicar a configuração. Os valores válidos são ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) dSC garante que a configuração está no estado desejado.

> [!NOTE]
> Se utilizar uma versão mais cedo do que 2.3, o parâmetro de modo é o mesmo que extensionAction. O modo parece ser um termo sobrecarregado. Para evitar confusões, a ExtensionAction é utilizada a partir da versão 2.3. Para a retrocompatibilidade, a extensão suporta tanto o modo como a ExtensionAction. 
>

### <a name="protected-configuration"></a>Configuração protegida

Aqui estão todos os parâmetros de configuração protegidos suportados:

* `StorageAccountName`: (opcional, cadeia) O nome da conta de armazenamento que contém o ficheiro
* `StorageAccountKey`: (opcional, cadeia) A chave da conta de armazenamento que contém o ficheiro
* `RegistrationUrl`: (opcional, cadeia) O URL da conta Azure Automation
* `RegistrationKey`: (opcional, cadeia) A chave de acesso da conta Azure Automation


## <a name="scenarios"></a>Cenários

### <a name="register-an-azure-automation-account"></a>Registe uma conta Azure Automation
protected.json
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.json
```json
{
  "ExtensionAction" : "Register",
  "NodeConfigurationName" : "<node-configuration-name>",
  "RefreshFrequencyMins" : "<value>",
  "ConfigurationMode" : "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins" : "<value>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}'

$publicConfig = '{
  "ExtensionAction" : "Register",
  "NodeConfigurationName": "<node-configuration-name>",
  "RefreshFrequencyMins": "<value>",
  "ConfigurationMode": "<ApplyAndMonitor | ApplyAndAutoCorrect | ApplyOnly>",
  "ConfigurationModeFrequencyMins": "<value>"
}'
```

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um ficheiro de configuração MOF (numa conta de armazenamento Azure) ao VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "FileUri": "<mof-file-uri>",
  "ExtensionAction": "Push"
}'
```


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um ficheiro de configuração MOF (em armazenamento público) ao VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formato PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um ficheiro de configuração meta MOF (numa conta de armazenamento Azure) ao VM

protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.json
```json
{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Pull",
  "FileUri": "<meta-mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um ficheiro de configuração meta MOF (em armazenamento público) ao VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instale um módulo de recursos personalizados (um ficheiro zip numa conta de armazenamento Azure) para o VM
protected.json
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```

Formato PowerShell
```powershell
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'

$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instale um módulo de recursos personalizados (um ficheiro zip em armazenamento público) para o VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Remova um módulo de recursos personalizado sacar do VM
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Formato PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando se implanta uma ou mais máquinas virtuais que requerem configuração pós-implantação, como o embarque na Automação Azure. 

O modelo de Gestor de Recursos da amostra é [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais informações sobre o modelo do Gestor de Recursos Azure, consulte [os modelos de Gestor de Recursos Do Azure.](../../azure-resource-manager/templates/template-syntax.md)


## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

### <a name="use-azure-cliazure-cli"></a>Utilizar [Azure CLI][azure-cli]
Antes de implementar a extensão DSCForLinux, configure o seu `public.json` e `protected.json` de acordo com os diferentes cenários da secção 3.

#### <a name="classic"></a>Clássica

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O modo de implementação clássico também é chamado de modo de Gestão de Serviços Azure. Pode mudar para ele executando:
```
$ azure config mode asm
```

Pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber a versão mais recente de extensão disponível, execute:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Pode mudar para o modo Gestor de Recursos Azure executando:
```
$ azure config mode arm
```

Pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> No modo Azure Resource Manager, `azure vm extension list` não está disponível por enquanto.
>

### <a name="use-azure-powershellazure-powershell"></a>Utilizar [Azure PowerShell][casca de potência azul]

#### <a name="classic"></a>Clássica

Pode iniciar sessão na sua conta Azure no modo de Gestão de Serviços Azure, executando:

```powershell>
Add-AzureAccount
```

E implementar a extensão DSCForLinux executando:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Altere o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários na secção anterior.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzureVMExtension -ExtensionName $extensionName -VM $vm -Publisher $publisher `
  -Version $version -PrivateConfiguration $privateConfig `
  -PublicConfiguration $publicConfig | Update-AzureVM
```

#### <a name="resource-manager"></a>Resource Manager

Pode iniciar sessão na sua conta Azure no modo Azure Resource Manager executando:

```powershell>
Login-AzAccount
```

Para saber mais sobre como usar o Azure PowerShell com o Azure Resource Manager, consulte a Manage Azure recursos utilizando o [Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Pode implantar a extensão DSCForLinux executando:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Altere o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários na secção anterior.
```
$privateConfig = '{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}'
```

```
$publicConfig = '{
  "ExtensionAction": "Push",
  "FileUri": "<mof-file-uri>"
}'
```

```
Set-AzVMExtension -ResourceGroupName $rgName -VMName $vmName -Location $location `
  -Name $extensionName -Publisher $publisher -ExtensionType $extensionName `
  -TypeHandlerVersion $version -SettingString $publicConfig -ProtectedSettingString $privateConfig
```

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o Azure CLI. Para ver o estado de implantação das extensões para um dado VM, execute o seguinte comando utilizando o Azure CLI.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa distribuição não suportada ou ação de extensão não suportada.
Em alguns casos, a extensão DSC Linux não instala o OMI quando já existe uma versão mais alta do OMI na máquina. [resposta ao erro: (000003)Downgrade não permitido]



### <a name="support"></a>Suporte

Se precisar de mais ajuda em algum ponto deste artigo, contacte os especialistas da Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode apresentar um incidente de Suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **suporte**. Para obter informações sobre a utilização do Suporte Azure, leia as FAQ de suporte do [Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades da máquina virtual para Linux](features-linux.md).
