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
ms.openlocfilehash: 6df6bb5c0be4bf1779541a815bd933965024809f
ms.sourcegitcommit: 4d177e6d273bba8af03a00e8bb9fe51a447196d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71960397"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão de DSC para Linux (Microsoft. OSTCExtensions. DSCForLinux)

A DSC (configuração de estado desejado) é uma plataforma de gerenciamento que permite gerenciar sua infraestrutura de ti e de desenvolvimento com a configuração como código.

> ! Observe que a extensão de DSC para Linux e a [extensão de máquina virtual Azure monitor para Linux](/virtual-machines/extensions/oms-linux) atualmente apresentam um conflito e não tem suporte em uma configuração lado a lado.  Isso significa que você não deve usar as duas soluções juntas na mesma VM.

A extensão DSCForLinux é publicada e tem suporte da Microsoft. A extensão instala o OMI e o agente DSC em máquinas virtuais do Azure. A extensão DSC também pode executar as seguintes ações


- Registrar a VM do Linux na conta de automação do Azure para efetuar pull das configurações do serviço de automação do Azure (registrar Extensionaction)
- Enviar por push configurações do MOF para a VM do Linux (extensão de Pushaction)
- Aplicar a configuração do metamof à VM do Linux para configurar o servidor de pull para efetuar pull da configuração de nó (extensão de Pullaction)
- Instalar módulos DSC personalizados para a VM do Linux (instalar Extensionaction)
- Remover módulos DSC personalizados para a VM do Linux (remover Extensionaction)

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão do Linux do DSC dá suporte a todas as [distribuições do Linux endossadas no Azure](/virtual-machines/linux/endorsed-distros) , exceto:

| Distribuição | Version |
|---|---|
| Debian | todas as versões |
| Ubuntu| 18, 4 |
 
### <a name="internet-connectivity"></a>Conectividade Internet

A extensão DSCForLinux requer que a máquina virtual de destino esteja conectada à Internet. Por exemplo, a extensão de registro requer conectividade com o serviço de automação. Para outras ações, como pull, pull, install requer conectividade com o armazenamento do Azure/github. Depende das configurações fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="11-public-configuration"></a>configuração pública do 1,1

Aqui estão todos os parâmetros de configuração pública com suporte:

* `FileUri`: (opcional, Cadeia de caracteres) o URI do arquivo MOF/arquivo de metadados do metamof/recurso personalizado.
* `ResourceName`: (opcional, Cadeia de caracteres) o nome do módulo de recurso personalizado
* `ExtensionAction`: (opcional, Cadeia de caracteres) especifica o que uma extensão faz. valores válidos: Registrar, enviar por push, efetuar pull, instalar, remover. Se não for especificado, ele será considerado como ação de envio por Push por padrão.
* `NodeConfigurationName`: (opcional, Cadeia de caracteres) o nome de uma configuração de nó a ser aplicada.
* `RefreshFrequencyMins`: (opcional, int) especifica com que frequência (em minutos) a DSC tenta obter a configuração do servidor de pull. 
       Se a configuração no servidor de pull for diferente da atual no nó de destino, ela será copiada para o repositório pendente e aplicada.
* `ConfigurationMode`: (opcional, Cadeia de caracteres) especifica como a DSC deve aplicar a configuração. Valores válidos são: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) especifica com que frequência (em minutos) a DSC garante que a configuração esteja no estado desejado.

> [!NOTE]
> Se você estiver usando uma versão < 2,3, o parâmetro mode será o mesmo que Extensionaction. O modo parece ser um termo sobrecarregado. Portanto, para evitar a confusão, a Extensãoaction está sendo usada da versão 2,3 em diante. Para compatibilidade com versões anteriores, a extensão dá suporte a Mode e Extensionaction. 
>

### <a name="12-protected-configuration"></a>1,2 configuração protegida

Aqui estão todos os parâmetros de configuração protegidos com suporte:

* `StorageAccountName`: (opcional, Cadeia de caracteres) o nome da conta de armazenamento que contém o arquivo
* `StorageAccountKey`: (opcional, Cadeia de caracteres) a chave da conta de armazenamento que contém o arquivo
* `RegistrationUrl`: (opcional, Cadeia de caracteres) a URL da conta de automação do Azure
* `RegistrationKey`: (opcional, Cadeia de caracteres) a chave de acesso da conta de automação do Azure


## <a name="scenarios"></a>Cenários

### <a name="register-to-azure-automation-account"></a>Registrar-se na conta de automação do Azure
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

formato do PowerShell
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração MOF (na conta de armazenamento do Azure) à VM

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

formato do PowerShell
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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração MOF (no armazenamento público) à VM

public.json
```json
{
  "FileUri": "<mof-file-uri>"
}
```

formato do PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<mof-file-uri>"
}'
```

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração meta MOF (na conta de armazenamento do Azure) à VM

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

formato do PowerShell
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
formato do PowerShell
```powershell
$publicConfig = '{
  "FileUri": "<meta-mof-file-uri>",
  "ExtensionAction": "Pull"
}'
```

### <a name="install-a-custom-resource-module-zip-file-in-azure-storage-account-to-the-vm"></a>Instalar um módulo de recurso personalizado (arquivo ZIP na conta de armazenamento do Azure) para a VM
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

formato do PowerShell
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Instalar um módulo de recurso personalizado (arquivo ZIP no armazenamento público) na VM
public.json
```json
{
  "ExtensionAction": "Install",
  "FileUri": "<resource-zip-file-uri>"
}
```
formato do PowerShell
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
formato do PowerShell
```powershell
$publicConfig = '{
  "ResourceName": "<resource-name>",
  "ExtensionAction": "Remove"
}'
```

## <a name="template-deployment"></a>Implementação de modelos

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais ao implantar uma ou mais máquinas virtuais que exigem a configuração pós-implantação, como a integração à automação do Azure. 

O modelo do Resource Manager de exemplo é [201-DSC-Linux-Azure-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-DSC-Linux-Public-Storage-on-Ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais detalhes sobre Azure Resource Manager modelo, visite [criação de modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

### <a name="21-using-azure-cliazure-cli"></a>2.1. Usando [**CLI do Azure**] [Azure-CLI]
Antes de implantar a extensão DSCForLinux, você deve configurar o `public.json` e o `protected.json`, de acordo com os diferentes cenários na seção 3.

#### <a name="211-classic"></a>2.1.1. Clássica
O modo clássico também é chamado de modo de gerenciamento de serviços do Azure. Você pode alternar para ele executando:
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

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
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
> No modo de Azure Resource Manager, o `azure vm extension list` não está disponível por enquanto.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Usando [**Azure PowerShell**] [Azure-PowerShell]

#### <a name="221-classic"></a>2.2.1 clássico

Você pode fazer logon em sua conta do Azure (modo de gerenciamento de serviços do Azure) executando:

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

Você precisa alterar o conteúdo do $privateConfig e $publicConfig de acordo com diferentes cenários na seção acima 
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

#### <a name="222resource-manager"></a>Gerenciador de recursos 2.2.2.

Você pode fazer logon em sua conta do Azure (modo de Azure Resource Manager) executando:

```powershell>
Login-AzAccount
```

Clique [**aqui**](../../azure-resource-manager/manage-resources-powershell.md) para saber mais sobre como usar Azure PowerShell com Azure Resource Manager.

Você pode implantar a extensão DSCForLinux executando:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Você precisa alterar o conteúdo do $privateConfig e $publicConfig de acordo com diferentes cenários na seção acima 
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

Podem ser obtidos dados sobre o estado das implementações de extensão do portal do Azure e com a CLI do Azure. Para ver o estado de implementação de extensões para uma determinada VM, execute o seguinte comando com a CLI do Azure.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Resultado da execução de extensão é registado para o ficheiro seguinte:

```
/var/log/azure/<extension-name>/<version>/extension.log file.
```

Código de erro: 51 representa uma ação de extensão sem suporte distribuição ou sem suporte.
Em alguns casos, a extensão do DSC do Linux falha ao instalar o OMI quando a versão mais recente do OMI já existe no computador. [resposta de erro: (000003) Downgrade não permitido]



### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre extensões, consulte [recursos e extensões de máquina virtual para Linux](features-linux.md).
