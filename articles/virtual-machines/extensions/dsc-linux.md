---
title: Extensão de DSC do Azure para Linux
description: Instala os pacotes OMI e DSC para permitir que uma VM Linux do Azure seja configurada usando a configuração de estado desejado.
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
ms.openlocfilehash: b631a370c64522c201f1208819b5a76895d83b09
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457516"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão de DSC para Linux (Microsoft. OSTCExtensions. DSCForLinux)

A DSC (configuração de estado desejado) é uma plataforma de gerenciamento que você pode usar para gerenciar sua infraestrutura de ti e de desenvolvimento com a configuração como código.

> [!NOTE]
> A extensão de DSC para Linux e a [extensão de máquina virtual Azure monitor para Linux](/azure/virtual-machines/extensions/oms-linux) atualmente apresentam um conflito e não tem suporte em uma configuração lado a lado. Não use as duas soluções juntas na mesma VM.

A extensão DSCForLinux é publicada e tem suporte da Microsoft. A extensão instala o OMI e o agente DSC em máquinas virtuais do Azure. A extensão de DSC também pode executar as seguintes ações:


- Registre a VM do Linux em uma conta de automação do Azure para efetuar pull das configurações do serviço de automação do Azure (registrar Extensionaction).
- Envie por push configurações do MOF para a VM do Linux (extensão Pushaction).
- Aplique a configuração do metamof à VM do Linux para configurar um servidor de pull para efetuar pull da configuração do nó (extensão Pullaction).
- Instale módulos DSC personalizados para a VM do Linux (instalar Extensionaction).
- Remova os módulos DSC personalizados da VM do Linux (remover Extensionaction).

 

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do Linux do DSC dá suporte a todas as [distribuições do Linux endossadas no Azure](/azure/virtual-machines/linux/endorsed-distros) , exceto:

| Distribuição | Versão |
|---|---|
| Debian | Todas as versões |
| Ubuntu| 18, 4 |
 
### <a name="internet-connectivity"></a>Conectividade Internet

A extensão DSCForLinux requer que a máquina virtual de destino esteja conectada à Internet. Por exemplo, a extensão de registro requer conectividade com o serviço de automação. Para outras ações como pull, pull, install requer conectividade com o armazenamento do Azure e o GitHub. Depende das configurações fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="public-configuration"></a>Configuração pública

Aqui estão todos os parâmetros de configuração pública com suporte:

* `FileUri`: (opcional, Cadeia de caracteres) o URI do arquivo MOF, o arquivo meta MOF ou o arquivo zip de recurso personalizado.
* `ResourceName`: (opcional, Cadeia de caracteres) o nome do módulo de recurso personalizado.
* `ExtensionAction`: (opcional, Cadeia de caracteres) especifica o que uma extensão faz. Os valores válidos são registrar, enviar por push, efetuar pull, instalar e remover. Se não for especificado, ele será considerado uma ação de envio por Push por padrão.
* `NodeConfigurationName`: (opcional, Cadeia de caracteres) o nome de uma configuração de nó a ser aplicada.
* `RefreshFrequencyMins`: (opcional, int) especifica com que frequência (em minutos) o DSC tenta obter a configuração do servidor de pull. 
       Se a configuração no servidor de pull for diferente da atual no nó de destino, ela será copiada para a loja pendente e aplicada.
* `ConfigurationMode`: (opcional, Cadeia de caracteres) especifica como a DSC deve aplicar a configuração. Os valores válidos são ApplyOnly, ApplyAndMonitor e ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) especifica com que frequência (em minutos) a DSC garante que a configuração esteja no estado desejado.

> [!NOTE]
> Se você usar uma versão anterior à 2,3, o parâmetro mode será o mesmo que Extensionaction. O modo parece ser um termo sobrecarregado. Para evitar confusão, Extensionaction é usado da versão 2,3 em diante. Para compatibilidade com versões anteriores, a extensão dá suporte a Mode e Extensionaction. 
>

### <a name="protected-configuration"></a>Configuração protegida

Aqui estão todos os parâmetros de configuração protegidos com suporte:

* `StorageAccountName`: (opcional, Cadeia de caracteres) o nome da conta de armazenamento que contém o arquivo
* `StorageAccountKey`: (opcional, Cadeia de caracteres) a chave da conta de armazenamento que contém o arquivo
* `RegistrationUrl`: (opcional, Cadeia de caracteres) a URL da conta de automação do Azure
* `RegistrationKey`: (opcional, Cadeia de caracteres) a chave de acesso da conta de automação do Azure


## <a name="scenarios"></a>Cenários

### <a name="register-an-azure-automation-account"></a>Registrar uma conta de automação do Azure
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

Formato do PowerShell
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

### <a name="apply-an-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração MOF (em uma conta de armazenamento do Azure) à VM

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

Formato do PowerShell
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


### <a name="apply-an-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração MOF (no armazenamento público) à VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

Formato do PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-an-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração meta MOF (em uma conta de armazenamento do Azure) à VM

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

Formato do PowerShell
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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração meta MOF (no armazenamento público) à VM
public.json
```json
{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}
```
Formato do PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-a-zip-file-in-an-azure-storage-account-to-the-vm"></a>Instalar um módulo de recurso personalizado (um arquivo zip em uma conta de armazenamento do Azure) para a VM
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

Formato do PowerShell
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

### <a name="install-a-custom-resource-module-a-zip-file-in-public-storage-to-the-vm"></a>Instalar um módulo de recurso personalizado (um arquivo zip no armazenamento público) para a VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
Formato do PowerShell
```powershell
$publicConfig = '{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}'
```

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Remover um módulo de recurso personalizado da VM
public.json
```json
{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}
```
Formato do PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando você implanta uma ou mais máquinas virtuais que exigem a configuração pós-implantação, como a integração à automação do Azure. 

O modelo do Resource Manager de exemplo é [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais informações sobre o modelo de Azure Resource Manager, consulte [criando modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

### <a name="use-azure-cliazure-cli"></a>Usar [CLI do Azure] [Azure-CLI]
Antes de implantar a extensão DSCForLinux, configure seu `public.json` e `protected.json` de acordo com os diferentes cenários na seção 3.

#### <a name="classic"></a>Clássica
O modo de implantação clássico também é chamado de modo de gerenciamento de serviços do Azure. Você pode alternar para ele executando:
```
$ azure config mode asm
```

Você pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber mais sobre a versão de extensão mais recente disponível, execute:
```
$ azure vm extension list
```

#### <a name="resource-manager"></a>Resource Manager
Você pode alternar para o modo de Azure Resource Manager executando:
```
$ azure config mode arm
```

Você pode implantar a extensão DSCForLinux executando:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> No modo de Azure Resource Manager, `azure vm extension list` não está disponível por enquanto.
>

### <a name="use-azure-powershellazure-powershell"></a>Usar [Azure PowerShell] [Azure-PowerShell]

#### <a name="classic"></a>Clássica

Você pode entrar em sua conta do Azure no modo de gerenciamento de serviços do Azure executando:

```powershell>
Add-AzureAccount
```

E implante a extensão DSCForLinux executando:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Altere o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários na seção anterior.
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

Você pode entrar em sua conta do Azure no modo de Azure Resource Manager executando:

```powershell>
Login-AzAccount
```

Para saber mais sobre como usar Azure PowerShell com Azure Resource Manager, consulte [gerenciar recursos do Azure usando Azure PowerShell](../../azure-resource-manager/manage-resources-powershell.md).

Você pode implantar a extensão DSCForLinux executando:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Altere o conteúdo de $privateConfig e $publicConfig de acordo com diferentes cenários na seção anterior.
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

Os dados sobre o estado das implantações de extensão podem ser recuperados do portal do Azure e usando o CLI do Azure. Para ver o estado de implantação das extensões de uma determinada VM, execute o seguinte comando usando o CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa a distribuição sem suporte ou a ação de extensão sem suporte.
Em alguns casos, a extensão do DSC do Linux falha ao instalar o OMI quando uma versão mais recente do OMI já existe no computador. [resposta de erro: downgrade de (000003) não permitido]



### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, entre em contato com os especialistas do Azure nos [fóruns do Azure e do Stack Overflow do MSDN](https://azure.microsoft.com/support/community/). Como alternativa, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/)e selecione **obter suporte**. Para obter informações sobre como usar o suporte do Azure, leia as [perguntas frequentes sobre suporte do Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [recursos e extensões de máquina virtual para Linux](features-linux.md).
