---
title: Visão geral da configuração de estado desejado para o Azure
description: Saiba como usar o manipulador de extensão Microsoft Azure para a DSC (configuração de estado desejado) do PowerShell. O artigo inclui pré-requisitos, arquitetura e cmdlets.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 7e309237589dfaf037114401172fc8f928a30077
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72176654"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão de configuração de estado desejado do Azure

O agente de VM do Azure e as extensões associadas fazem parte dos serviços de infraestrutura Microsoft Azure. As extensões de VM são componentes de software que estendem a funcionalidade da VM e simplificam várias operações de gerenciamento de VM.

O caso de uso primário para a extensão DSC (configuração de estado desejado) do Azure é inicializar uma VM para o [serviço de configuração de estado da automação do Azure (DSC)](../../automation/automation-dsc-overview.md).
O serviço fornece [benefícios](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) que incluem o gerenciamento contínuo da configuração da VM e a integração com outras ferramentas operacionais, como o monitoramento do Azure.
Usar a extensão para registrar VMs no serviço fornece uma solução flexível que até funciona em assinaturas do Azure.

Você pode usar a extensão DSC independentemente do serviço DSC de Automação.
No entanto, isso enviará por push apenas uma configuração para a VM.
Nenhum relatório contínuo está disponível, a não ser localmente na VM.

Este artigo fornece informações sobre os dois cenários: usando a extensão de DSC para integração de automação e usando a extensão de DSC como uma ferramenta para atribuir configurações a VMs usando o SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Computador local**: para interagir com a extensão de VM do Azure, você deve usar o portal do Azure ou o SDK do Azure PowerShell.
- **Agente convidado**: a VM do Azure configurada pela configuração DSC deve ser um sistema operacional compatível com o Windows Management Framework (WMF) 4,0 ou posterior. Para obter a lista completa de versões de sistema operacional com suporte, consulte o [histórico de versão da extensão DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

## <a name="terms-and-concepts"></a>Termos e conceitos

Este guia pressupõe familiaridade com os seguintes conceitos:

- **Configuração**: um documento de configuração DSC.
- **Node**: um destino para uma configuração DSC. Neste documento, o *nó* sempre se refere a uma VM do Azure.
- **Dados de configuração**: um arquivo. psd1 que tem dados ambientais para uma configuração.

## <a name="architecture"></a>Arquitetura

A extensão DSC do Azure usa a estrutura do agente de VM do Azure para fornecer, aplicar e relatar configurações de DSC em execução em VMs do Azure. A extensão DSC aceita um documento de configuração e um conjunto de parâmetros. Se nenhum arquivo for fornecido, um [script de configuração padrão](#default-configuration-script) será inserido com a extensão. O script de configuração padrão é usado somente para definir metadados no [Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig).

Quando a extensão é chamada pela primeira vez, ela instala uma versão do WMF usando a seguinte lógica:

- Se o sistema operacional da VM do Azure for o Windows Server 2016, nenhuma ação será executada. O Windows Server 2016 já tem a versão mais recente do PowerShell instalada.
- Se a propriedade **wmfVersion** for especificada, essa versão do WMF será instalada, a menos que essa versão seja incompatível com o sistema operacional da VM.
- Se nenhuma propriedade **wmfVersion** for especificada, a versão mais recente aplicável do WMF será instalada.

A instalação do WMF requer uma reinicialização. Após a reinicialização, a extensão baixa o arquivo. zip especificado na propriedade **modulesUrl** , se fornecido. Se esse local estiver no armazenamento de BLOBs do Azure, você poderá especificar um token SAS na propriedade **sasToken** para acessar o arquivo. Depois que o. zip é baixado e desempacotado, a função de configuração definida em **configurationFunction** é executada para gerar um arquivo. mof. Em seguida, a extensão é executada `Start-DscConfiguration -Force` usando o arquivo. mof gerado. A extensão captura a saída e a grava no canal de status do Azure.

### <a name="default-configuration-script"></a>Script de configuração padrão

A extensão DSC do Azure inclui um script de configuração padrão que deve ser usado quando você carrega uma VM para o serviço de DSC de Automação do Azure. Os parâmetros de script são alinhados com as propriedades configuráveis do [Configuration Manager local](/powershell/scripting/dsc/managing-nodes/metaConfig). Para parâmetros de script, consulte [script de configuração padrão](dsc-template.md#default-configuration-script) na [extensão de configuração de estado desejado com modelos de Azure Resource Manager](dsc-template.md). Para obter o script completo, consulte o [modelo de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informações para registrar com o serviço de configuração de estado da automação do Azure (DSC)

Ao usar a extensão de DSC para registrar um nó com o serviço de configuração de estado, será necessário fornecer três valores.

- RegistrationUrl-o endereço https da conta de automação do Azure
- RegistrationKey-um segredo compartilhado usado para registrar nós com o serviço
- NodeConfigurationName-o nome da configuração de nó (MOF) a ser extraída do serviço para configurar a função de servidor

Essas informações podem ser vistas na [portal do Azure](../../automation/automation-dsc-onboarding.md#azure-portal) ou você pode usar o PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Para o nome da configuração do nó, verifique se a configuração do nó existe na configuração de estado do Azure.  Caso contrário, a implantação da extensão retornará uma falha.  Verifique também se você está usando o nome da *configuração do nó* e não a configuração.
Uma configuração é definida em um script que é usado [para compilar a configuração de nó (arquivo MOF)](https://docs.microsoft.com/azure/automation/automation-dsc-compile).
O nome será sempre a configuração seguida por um período `.` e `localhost` ou um nome de computador específico.

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensão de DSC em modelos do Resource Manager

Na maioria dos cenários, os modelos de implantação do Resource Manager são a maneira esperada de trabalhar com a extensão de DSC. Para obter mais informações e exemplos de como incluir a extensão de DSC em modelos de implantação do Resource Manager, consulte [extensão de configuração de estado desejado com modelos de Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets do PowerShell de extensão DSC

Os cmdlets do PowerShell que são usados para gerenciar a extensão de DSC são melhor usados em cenários interativos de solução de problemas e coleta de informações. Você pode usar os cmdlets para empacotar, publicar e monitorar implantações de extensão de DSC. Os cmdlets da extensão DSC ainda não foram atualizados para funcionar com o [script de configuração padrão](#default-configuration-script).

O cmdlet **Publish-AzVMDscConfiguration** usa um arquivo de configuração, examina-o em busca de recursos de DSC dependentes e, em seguida, cria um arquivo. zip. O arquivo. zip contém os recursos de configuração e DSC que são necessários para aplicar a configuração. O cmdlet também pode criar o pacote localmente usando o parâmetro *-OutputArchivePath* . Caso contrário, o cmdlet publicará o arquivo. zip no armazenamento de BLOBs e, em seguida, o protegerá com um token SAS.

O script de configuração. ps1 que o cmdlet cria está no arquivo. zip na raiz da pasta de arquivo morto. A pasta de módulo é colocada na pasta arquivo morto em recursos.

O cmdlet **set-AzVMDscExtension** injeta as configurações que a extensão DSC do PowerShell requer em um objeto de configuração de VM.

O cmdlet **Get-AzVMDscExtension** recupera o status da extensão de DSC de uma VM específica.

O cmdlet **Get-AzVMDscExtensionStatus** recupera o status da configuração DSC que é imposta pelo manipulador de extensão de DSC. Essa ação pode ser executada em uma única VM ou em um grupo de VMs.

O cmdlet **Remove-AzVMDscExtension** remove o manipulador de extensão de uma VM específica. Esse cmdlet não *Remove a* configuração, desinstala o WMF ou altera as configurações aplicadas na VM. Ele remove apenas o manipulador de extensão. 

Informações importantes sobre os cmdlets de extensão DSC do Resource Manager:

- Os cmdlets Azure Resource Manager são síncronos.
- Os parâmetros *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *version*e *Location* são todos necessários.
- *ArchiveResourceGroupName* é um parâmetro opcional. Você pode especificar esse parâmetro quando sua conta de armazenamento pertencer a um grupo de recursos diferente daquele em que a VM é criada.
- Use a opção **AutoUpdate** para atualizar automaticamente o manipulador de extensão para a versão mais recente quando ele estiver disponível. Esse parâmetro tem o potencial de causar reinicializações na VM quando uma nova versão do WMF é lançada.

### <a name="get-started-with-cmdlets"></a>Introdução aos cmdlets

A extensão DSC do Azure pode usar documentos de configuração DSC para configurar diretamente as VMs do Azure durante a implantação. Esta etapa não registra o nó na automação. O nó *não* é gerenciado centralmente.

O exemplo a seguir mostra um exemplo simples de uma configuração. Salve a configuração localmente como IisInstall. ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Os comandos a seguir colocam o script IisInstall. ps1 na VM especificada. Os comandos também executam a configuração e relatam o status.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Implantação de CLI do Azure

O CLI do Azure pode ser usado para implantar a extensão de DSC em uma máquina virtual existente.

Para uma máquina virtual que executa o Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Para uma máquina virtual que executa o Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>portal do Azure funcionalidade

Para configurar o DSC no Portal:

1. Vá para uma VM.
2. Em **Definições**, selecione **Extensões**.
3. Na nova página criada, selecione **+ Adicionar**e selecione **configuração de estado desejado do PowerShell**.
4. Clique em **criar** na parte inferior da página informações de extensão.

O portal coleta a seguinte entrada:

- **Módulos de configuração ou script**: Este campo é obrigatório (o formulário não foi atualizado para o [script de configuração padrão](#default-configuration-script)). Os módulos de configuração e os scripts exigem um arquivo. ps1 que tenha um script de configuração ou um arquivo. zip com um script de configuração. ps1 na raiz. Se você usar um arquivo. zip, todos os recursos dependentes deverão ser incluídos nas pastas de módulo no. zip. Você pode criar o arquivo. zip usando o cmdlet **Publish-AzureVMDscConfiguration-OutputArchivePath** que está incluído no SDK do Azure PowerShell. O arquivo. zip é carregado no armazenamento de BLOBs do usuário e protegido por um token SAS.

- **Nome qualificado do módulo da configuração**: você pode incluir várias funções de configuração em um arquivo. ps1. Insira o nome do script Configuration. ps1 seguido por \\ e o nome da função de configuração. Por exemplo, se o seu script. ps1 tiver o nome Configuration. ps1 e a configuração for **IisInstall**, insira **Configuration. ps1\IisInstall**.

- **Argumentos de configuração**: se a função de configuração usa argumentos, insira-os aqui no formato **argumentName1 = value1, argumentName2 = value2**. Esse formato é um formato diferente no qual argumentos de configuração são aceitos em cmdlets do PowerShell ou modelos do Resource Manager.

- **Arquivo de PSD1 de dados de configuração**: esse campo é opcional. Se sua configuração exigir um arquivo de dados de configuração no. psd1, use esse campo para selecionar o campo de dados e carregá-lo no armazenamento de BLOBs do usuário. O arquivo de dados de configuração é protegido por um token SAS no armazenamento de BLOBs.

- **Versão do WMF**: especifica a versão do Windows Management Framework (WMF) que deve ser instalada em sua VM. Definir essa propriedade como mais recente instala a versão mais recente do WMF. Atualmente, os únicos valores possíveis para essa propriedade são 4,0, 5,0, 5,1 e mais recente. Esses valores possíveis estão sujeitos a atualizações. O valor padrão é **mais recente**.

- **Coleta de dados**: determina se a extensão irá coletar telemetria. Para obter mais informações, consulte [coleta de dados de extensão de DSC do Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Versão**: especifica a versão da extensão DSC a ser instalada. Para obter informações sobre versões, consulte [histórico de versão da extensão DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Versão secundária de atualização automática**: esse campo é mapeado para a opção **AutoUpdate** nos cmdlets e permite que a extensão seja atualizada automaticamente para a versão mais recente durante a instalação. **Sim** instruirá o manipulador de extensão a usar a versão mais recente disponível e **não** forçará a **versão** especificada a ser instalada. Selecionar **Sim** nem **não** é o mesmo que selecionar **não**.

## <a name="logs"></a>Registos

Os logs para a extensão são armazenados no seguinte local: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o DSC do PowerShell, acesse o [centro de documentação do PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o [modelo do Resource Manager para a extensão de DSC](dsc-template.md).
- Para obter mais funcionalidade que você pode gerenciar usando a DSC do PowerShell e para obter mais recursos de DSC, procure a [Galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Para obter detalhes sobre como passar parâmetros confidenciais em configurações, consulte [gerenciar credenciais com segurança com o manipulador de extensão de DSC](dsc-credentials.md).
