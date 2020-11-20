---
title: Extensão Azure DSC para Linux
description: Instala pacotes OMI e DSC para permitir que um VM Azure Linux seja configurado utilizando a Configuração do Estado Desejado.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 1d1a5cf67a10a83a227f240fc31d25abfe9c7dd0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955944"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão DSC para Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) é uma plataforma de gestão que pode utilizar para gerir a sua infraestrutura de TI e desenvolvimento com configuração como código.

> [!NOTE]
> A extensão DSC para Linux e a [extensão da máquina virtual Azure Monitor para o Linux](./oms-linux.md) apresentam atualmente um conflito e não são suportadas numa configuração lado a lado. Não utilize as duas soluções juntas no mesmo VM.

A extensão DSCForLinux é publicada e suportada pela Microsoft. A extensão instala o agente OMI e DSC em máquinas virtuais Azure. A extensão do DSC também pode fazer as seguintes ações:

- Registe o Linux VM numa conta Azure Automation para retirar configurações do serviço Azure Automation (Register ExtensionAction).
- Empurre as configurações MOF para o Linux VM (Push ExtensionAction).
- Aplique a configuração meta MOF no Linux VM para configurar um servidor de puxar para puxar a configuração do nó (Pull ExtensionAction).
- Instale módulos DSC personalizados no Linux VM (Instalar ExtensionAction).
- Remova os módulos DSC personalizados do Linux VM (Remover ExtensionAction).

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

Para os nós que executam o Linux, a extensão DSC Linux suporta todas as distribuições Linux listadas na [documentação do DSC powerShell](/powershell/scripting/dsc/getting-started/lnxgettingstarted).

### <a name="internet-connectivity"></a>Conectividade Internet

A extensão DSCForLinux requer que a máquina virtual alvo seja ligada à internet. Por exemplo, a extensão Registo requer conectividade com o serviço Dem automação.
Para outras ações como Pull, Pull, Install requer conectividade com o Azure Storage e o GitHub. Depende das definições fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="public-configuration"></a>Configuração pública

Aqui estão todos os parâmetros de configuração pública suportados:

* `FileUri`: (opcional, string) O uri do ficheiro MOF, o ficheiro meta MOF ou o ficheiro zip de recurso personalizado.
* `ResourceName`: (opcional, cadeia) O nome do módulo de recurso personalizado.
* `ExtensionAction`: (opcional, cadeia) Especifica o que uma extensão faz. Os valores válidos são Registar, Empurrar, Puxar, Instalar e Remover. Se não for especificado, é considerado uma Ação push por defeito.
* `NodeConfigurationName`: (opcional, cadeia) O nome de uma configuração de nó a aplicar.
* `RefreshFrequencyMins`: (opcional, int) Especifica com que frequência (em minutos) o DSC tenta obter a configuração a partir do servidor de puxar.
       Se a configuração no servidor de puxar difere da atual no nó-alvo, é copiada para a loja pendente e aplicada.
* `ConfigurationMode`: (opcional, cadeia) Especifica como o DSC deve aplicar a configuração. Os valores válidos são ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) Especifica com que frequência (em minutos) o DSC assegura que a configuração está no estado pretendido.

> [!NOTE]
> Se utilizar uma versão anterior a 2.3, o parâmetro do modo é o mesmo que a ExtensionAction. O modo parece ser um termo sobrecarregado. Para evitar confusões, a ExtensionAction é utilizada a partir da versão 2.3. Para uma retrocompatibilidade, a extensão suporta o modo e a ExtensionAction.
>

### <a name="protected-configuration"></a>Configuração protegida

Aqui estão todos os parâmetros de configuração protegidos suportados:

* `StorageAccountName`: (opcional, cadeia) O nome da conta de armazenamento que contém o ficheiro
* `StorageAccountKey`: (opcional, cadeia) A chave da conta de armazenamento que contém o ficheiro
* `RegistrationUrl`: (opcional, string) O URL da conta Azure Automation
* `RegistrationKey`: (opcional, string) A chave de acesso da conta Azure Automation


## <a name="scenarios"></a>Cenários

### <a name="register-an-azure-automation-account"></a>Registar uma conta de Automação Azure
protected.jsem
```json
{
  "RegistrationUrl": "<azure-automation-account-url>",
  "RegistrationKey": "<azure-automation-account-key>"
}
```
public.jsem
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um ficheiro de configuração MOF (numa conta de armazenamento Azure) no VM

protected.jsem
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsem
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um ficheiro de configuração MOF (em armazenamento público) no VM

public.jsem
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

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um ficheiro de configuração meta MOF (numa conta de armazenamento Azure) no VM

protected.jsem
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```

public.jsem
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um ficheiro de configuração meta MOF (em armazenamento público) no VM
public.jsem
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

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instale um módulo de recurso personalizado (um ficheiro zip numa conta de armazenamento Azure) para o VM
protected.jsem
```json
{
  "StorageAccountName": "<storage-account-name>",
  "StorageAccountKey": "<storage-account-key>"
}
```
public.jsem
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instale um módulo de recurso personalizado (um ficheiro zip no armazenamento público) para o VM
public.jsem
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Remova um módulo de recurso personalizado do VM
public.jsem
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

As extensões Azure VM podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando implementa uma ou mais máquinas virtuais que requerem configuração pós-implantação, como a bordo da Azure Automation.

O modelo do Gestor de Recursos da amostra é [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais informações sobre o modelo do Gestor de Recursos Azure, consulte os modelos do [Gestor de Recursos Azure.](../../azure-resource-manager/templates/template-syntax.md)


## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

### <a name="use-azure-cliazure-cli"></a>Use [Azure CLI][azure-cli]
Antes de implementar a extensão DSCForLinux, configuure o seu `public.json` e de acordo com os `protected.json` diferentes cenários na secção 3.

#### <a name="classic"></a>Clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O modo de implementação clássico também é chamado de modo Azure Service Management. Pode mudar para ele correndo:
```
$ azure config mode asm
```

Pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber a versão de extensão mais recente disponível, corra:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Pode mudar para o modo Azure Resource Manager executando:
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

### <a name="use-azure-powershellazure-powershell"></a>Use [Azure PowerShell][azure-powershell]

#### <a name="classic"></a>Clássico

Pode iniciar sôms na sua conta Azure no modo Azure Service Management, executando:

```powershell
Add-AzureAccount
```

E implementar a extensão DSCForLinux em execução:

```powershell
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

Pode iniciar sôms na sua conta Azure no modo Azure Resource Manager executando:

```powershell
Login-AzAccount
```

Para saber mais sobre como utilizar a Azure PowerShell com o Azure Resource Manager, consulte [gerir os recursos do Azure utilizando a Azure PowerShell](../../azure-resource-manager/management/manage-resources-powershell.md).

Pode implantar a extensão DSCForLinux executando:

```powershell
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

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os dados sobre o estado das extensões podem ser recuperados a partir do portal Azure e utilizando o CLI Azure. Para ver o estado de implantação das extensões para um determinado VM, executar o seguinte comando utilizando o CLI Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

A saída de execução de extensão é registada no seguinte ficheiro:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa uma distribuição não suportada ou uma ação de extensão não apoiada.
Em alguns casos, a extensão DSC Linux não instala o OMI quando já existe uma versão mais alta do OMI na máquina. [resposta de erro: (000003)Downgrade não permitida]



### <a name="support"></a>Suporte

Se precisar de mais ajuda em qualquer ponto deste artigo, contacte os especialistas da Azure nos [fóruns msdn Azure e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode arquivar um incidente de Suporte Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione Obter **suporte**. Para obter informações sobre a utilização do Suporte Azure, leia o [Microsoft Azure Support FAQ](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [extensões e funcionalidades de máquina virtual para Linux.](features-linux.md)
