---
title: Extensão DSC do Azure para Linux
description: Instala os pacotes de OMI e DSC para permitir que uma VM de Linux do Azure ser configurada através de do Desired State Configuration.
services: virtual-machines-linux
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/12/2018
ms.author: robreed
ms.openlocfilehash: 01c3e9c82959629dc3e43fb6b0833d4dbb05e0aa
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56823013"
---
# <a name="dsc-extension-for-linux-microsoftostcextensionsdscforlinux"></a>Extensão DSC para Linux (Microsoft.OSTCExtensions.DSCForLinux)

Desired State Configuration (DSC) é uma plataforma de gestão que permite-lhe gerir o departamento de TI e a infraestrutura de desenvolvimento com a configuração como código.

Extensão de DSCForLinux é publicado e suportado pela Microsoft. A extensão instala o agente do OMI e DSC em máquinas virtuais do Azure. Extensão DSC também o pode fazer as seguintes ações


- Registe-se a VM do Linux para a conta de automatização do Azure, a fim de extrair as configurações do serviço de automatização do Azure (registar ExtensionAction)
- Configurações de MOF de push para a VM do Linux (Push ExtensionAction)
- Aplicar a configuração de Meta MOF a VM do Linux para configurar o servidor de solicitação a fim de extrair a configuração do nó (Pull ExtensionAction)
- Instalar módulos personalizados do DSC à VM do Linux (instalar ExtensionAction)
- Remover módulos personalizados do DSC à VM do Linux (remover ExtensionAction)

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

## <a name="prerequisites"></a>Pré-requisitos

### <a name="operating-system"></a>Sistema operativo

A extensão DSC Linux oferece suporte a todos os [distribuições do Linux apoiadas no Azure](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros) , exceto:

| Distribuição | Versão |
|---|---|
| Debian | Todas as versões |
| Ubuntu| 18.04 |
 
### <a name="internet-connectivity"></a>Conectividade Internet

A extensão de DSCForLinux requer que a máquina virtual de destino está ligada à internet. Por exemplo, registre-se extensão necessita de conectividade ao serviço de automatização. Para outras ações, como o Pull ', ' Pull, a instalação da atualização requer conectividade para o armazenamento do azure/github. Depende das definições fornecidas pelo cliente.

## <a name="extension-schema"></a>Esquema de extensão

### <a name="11-public-configuration"></a>1.1 configuração pública de

Seguem-se todos os parâmetros de configuração pública suportados:

* `FileUri`: (opcional, cadeia de caracteres) o uri do ficheiro ZIP de recursos de arquivo/personalizado MOF Meta/ficheiro MOF.
* `ResourceName`: (opcional, cadeia de caracteres) o nome do módulo de recurso personalizado
* `ExtensionAction`: (opcional, cadeia de caracteres) Especifica o que faz uma extensão. Valores válidos: Registar, enviar por Push, obter, instalar, remover. Se não for especificado, é considerado como ação Push por predefinição.
* `NodeConfigurationName`: (opcional, cadeia de caracteres) o nome de uma configuração de nó para aplicar.
* `RefreshFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) tenta obter a configuração do servidor de solicitação do DSC. 
       Se a configuração no servidor de solicitação é diferente do atual no nó de destino, é copiado para o arquivo pendente e aplicada.
* `ConfigurationMode`: (opcional, cadeia de caracteres) Especifica como o DSC deve aplicar a configuração. Valores válidos são: ApplyOnly, ApplyAndMonitor, ApplyAndAutoCorrect.
* `ConfigurationModeFrequencyMins`: (opcional, int) Especifica a frequência (em minutos) DSC garante que a configuração está no estado pretendido.

> [!NOTE]
> Se estiver a utilizar uma versão < 2.3, o parâmetro de modo é igual a ExtensionAction. Modo parece ser um termo sobrecarregado. Portanto, para evitar a confusão, ExtensionAction estiver sendo usado por versão 2.3 e posteriores. Para compatibilidade com versões anteriores, a extensão suporta o modo e ExtensionAction. 
>

### <a name="12-protected-configuration"></a>1.2 configuração protegida

Seguem-se todos os parâmetros de configuração protegida suportados:

* `StorageAccountName`: (opcional, cadeia de caracteres) o nome da conta de armazenamento que contém o ficheiro.
* `StorageAccountKey`: (opcional, cadeia) a chave da conta de armazenamento que contém o ficheiro.
* `RegistrationUrl`: (opcional, cadeia de caracteres) o URL da conta de automatização do Azure
* `RegistrationKey`: (opcional, cadeia) a chave de acesso da conta de automatização do Azure


## <a name="scenarios"></a>Cenários

### <a name="register-to-azure-automation-account"></a>Registre-se para a conta de automatização do Azure
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

### <a name="apply-a-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Aplicar um arquivo de configuração do MOF (na conta de armazenamento do Azure) para a VM

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


### <a name="apply-a-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicar um arquivo de configuração do MOF (em armazenamento público) à VM

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

### <a name="apply-a-meta-mof-configuration-file-in-azure-storage-account-to-the-vm"></a>Aplicam-se um ficheiro de configuração de MOF meta (na conta de armazenamento do Azure) para a VM

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

### <a name="apply-a-meta-mof-configuration-file-in-public-storage-to-the-vm"></a>Aplicam-se um ficheiro de configuração de MOF meta (em armazenamento público) à VM
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

### <a name="install-a-custom-resource-module-zip-file-in-public-storage-to-the-vm"></a>Instalar um módulo de recurso personalizado (arquivo ZIP no armazenamento público) para a VM
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

### <a name="remove-a-custom-resource-module-from-the-vm"></a>Remover um módulo de recurso personalizado a partir da VM
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

Extensões VM do Azure podem ser implementadas com modelos Azure Resource Manager. Os modelos são ideais quando implementar um ou mais máquinas virtuais que necessitam de configuração pós-implementação, tais como a integração à automatização do Azure. 

É o modelo do Resource Manager de exemplo [201-dsc-linux-azure-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-azure-storage-on-ubuntu) e [201-dsc-linux-public-storage-on-ubuntu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-dsc-linux-public-storage-on-ubuntu).

Para obter mais detalhes sobre o modelo Azure Resource Manager, visite [modelos Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).


## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

### <a name="21-using-azure-cliazure-cli"></a>2.1. Com [**CLI do Azure**] [azure-cli]
Antes de implementar a extensão de DSCForLinux, deve configurar seu `public.json` e `protected.json`, de acordo com os diferentes cenários da seção 3.

#### <a name="211-classic"></a>2.1.1. Clássica
O modo clássico também é denominado o modo de gestão de serviço do Azure. Pode alternar para o mesmo ao executar:
```
$ azure config mode asm
```

Pode implantar DSCForLinux extensão ao executar:
```
$ azure vm extension set <vm-name> DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```

Para saber a versão de extensão mais recente disponível, execute:
```
$ azure vm extension list
```

#### <a name="212-resource-manager"></a>2.1.2. Resource Manager
Pode mudar para o modo Azure Resource Manager ao executar:
```
$ azure config mode arm
```

Pode implantar DSCForLinux extensão ao executar:
```
$ azure vm extension set <resource-group> <vm-name> \
DSCForLinux Microsoft.OSTCExtensions <version> \
--private-config-path protected.json --public-config-path public.json
```
> [!NOTE]
> No modo Azure Resource Manager, `azure vm extension list` não está disponível por agora.
>

### <a name="22-using-azure-powershellazure-powershell"></a>2.2. Com [**do Azure PowerShell**] [azure-powershell]

#### <a name="221-classic"></a>2.2.1 clássico

Pode iniciar sessão sua conta do Azure (modo de gestão de serviço do Azure) ao executar:

```powershell>
Add-AzureAccount
```

E implemente a extensão de DSCForLinux ao executar:

```powershell>
$vmname = '<vm-name>'
$vm = Get-AzureVM -ServiceName $vmname -Name $vmname
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Tem de alterar o conteúdo do $privateConfig e $publicConfig, de acordo com diferentes cenários em acima secção 
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

#### <a name="222resource-manager"></a>2.2.2.Resource Manager

Pode iniciar sessão sua conta do Azure (modo Azure Resource Manager) ao executar:

```powershell>
Login-AzAccount
```

Clique em [ **aqui** ](../../azure-resource-manager/manage-resources-powershell.md) para saber mais sobre como utilizar o Azure PowerShell com o Azure Resource Manager.

Pode implantar DSCForLinux extensão ao executar:

```powershell>
$rgName = '<resource-group-name>'
$vmName = '<vm-name>'
$location = '< location>'
$extensionName = 'DSCForLinux'
$publisher = 'Microsoft.OSTCExtensions'
$version = '< version>'
```

Tem de alterar o conteúdo do $privateConfig e $publicConfig, de acordo com diferentes cenários em acima secção 
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

Código do erro: 51 representa distro não suportada ou ação de extensão não suportado.
Em alguns casos, o Linux DSC extensão não consegue instalar OMI, quando uma versão posterior da OMI é já existe na máquina. [resposta de erro: (000003) Mudança para versão anterior não permitida]



### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/community/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações sobre as extensões, consulte [extensões de Máquina Virtual e funcionalidades para Linux](features-linux.md).
