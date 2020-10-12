---
title: Configuração de estado desejada para visão geral do Azure
description: Saiba como utilizar o controlador de extensão Microsoft Azure para configuração de estado desejada por PowerShell (DSC). O artigo inclui pré-requisitos, arquitetura e cmdlets.
services: virtual-machines-windows
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 900273ec48c71e6f88d28bccff6f1e2abd412c1d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89079579"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao processador da extensão Desired State Configuration do Azure

O Agente VM Azure e as extensões associadas fazem parte dos serviços de infraestrutura da Microsoft Azure. As extensões VM são componentes de software que alargam a funcionalidade VM e simplificam várias operações de gestão de VM.

O caso de utilização primária para a extensão de Configuração do Estado Azure Desired (DSC) é o de arrancar um VM ao [serviço de Configuração do Estado de Automação Azure (DSC).](../../automation/automation-dsc-overview.md)
O serviço proporciona [benefícios](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) que incluem a gestão contínua da configuração vm e integração com outras ferramentas operacionais, como a Monitorização do Azure.
A utilização da extensão para registar vM's ao serviço fornece uma solução flexível que até funciona em todas as subscrições do Azure.

Pode utilizar a extensão DSC independentemente do serviço DSC automation.
No entanto, isto só irá empurrar uma configuração para o VM.
Não há relatórios em curso, a não ser localmente no VM.

Este artigo fornece informações sobre ambos os cenários: utilizando a extensão DSC para o embarque de automatização, e utilizando a extensão DSC como uma ferramenta para atribuir configurações a VMs utilizando o Azure SDK.

## <a name="prerequisites"></a>Pré-requisitos

- **Máquina local**: Para interagir com a extensão Azure VM, deve utilizar o portal Azure ou o Azure PowerShell SDK.
- **Agente Convidado**: O VM Azure que é configurado pela configuração DSC deve ser um SISTEMA que suporte o Windows Management Framework (WMF) 4.0 ou mais tarde. Para obter a lista completa das versões de SISTEMA suportadas, consulte o histórico da [versão de extensão DSC](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Termos e conceitos

Este guia pressupõe familiaridade com os seguintes conceitos:

- **Configuração**: Um documento de configuração DSC.
- **Nó**: Um alvo para uma configuração DSC. Neste documento, *o nó* refere-se sempre a um VM Azure.
- **Dados de configuração**: Um ficheiro .psd1 que tem dados ambientais para uma configuração.

## <a name="architecture"></a>Arquitetura

A extensão Azure DSC utiliza o quadro do Agente VM Azure para entregar, decretar e reportar sobre as configurações do DSC em execução em VMs Azure. A extensão DSC aceita um documento de configuração e um conjunto de parâmetros. Se nenhum ficheiro for fornecido, um [script de configuração predefinido](#default-configuration-script) é incorporado com a extensão. O script de configuração predefinido é utilizado apenas para definir metadados no [Gestor de Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

Quando a extensão é chamada pela primeira vez, instala uma versão do WMF utilizando a seguinte lógica:

- Se o Azure VM OS for o Windows Server 2016, não serão tomadas medidas. O Windows Server 2016 já tem a versão mais recente do PowerShell instalada.
- Se a propriedade **wmfVersion** for especificada, esta versão do WMF é instalada, a menos que essa versão seja incompatível com o SISTEMA do VM.
- Se não for especificada nenhuma propriedade **wmfVersion,** a versão mais recente aplicável do WMF é instalada.

A instalação do WMF requer um reinício. Após reiniciar, a extensão descarrega o ficheiro .zip especificado na propriedade **modulesUrl,** se fornecido. Se esta localização estiver no armazenamento do Azure Blob, pode especificar um token SAS na propriedade **sasToken** para aceder ao ficheiro. Depois de o .zip ser descarregado e desembalado, a função de configuração definida na **configuraçãoFunction** funciona para gerar um ficheiro .mof[(Formato de Objeto Gerido).](/windows/win32/wmisdk/managed-object-format--mof-) A extensão é então executado `Start-DscConfiguration -Force` utilizando o ficheiro .mof gerado. A extensão captura a saída e escreve-a para o canal de estado do Azure.

### <a name="default-configuration-script"></a>Script de configuração padrão

A extensão Azure DSC inclui um script de configuração padrão que se destina a ser usado quando está a bordo de um VM para o serviço DSC da Automação Azure. Os parâmetros do script estão alinhados com as propriedades configuráveis do [Gestor de Configuração Local.](/powershell/scripting/dsc/managing-nodes/metaConfig) Para parâmetros de script, consulte [o script de configuração padrão](dsc-template.md#default-configuration-script) na extensão de [configuração do estado desejado com os modelos do Gestor de Recursos Azure](dsc-template.md). Para obter o script completo, consulte o [modelo de arranque rápido Azure no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informações para registo com o serviço de Configuração do Estado da Automação Azure (DSC)

Ao utilizar a Extensão DSC para registar um nó com o serviço de Configuração do Estado, terão de ser fornecidos três valores.

- RegistrationUrl - o endereço https da conta Azure Automation
- RegistrationKey - um segredo partilhado usado para registar os nómdes com o serviço
- NodeConfigurationName - o nome da Configuração do Nó (MOF) para puxar do serviço para configurar a função do servidor

Esta informação pode ser vista no portal Azure ou pode utilizar o PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Para o nome de configuração do nó, certifique-se de que a configuração do nó existe na Configuração do Estado de Azure.  Se não o fizer, a extensão retornará uma falha.  Certifique-se também de que está a utilizar o nome da *Configuração* do Nó e não da Configuração.
Uma configuração é definida num script que é utilizado [para compilar a Configuração do Nó (ficheiro MOF)](../../automation/automation-dsc-compile.md).
O nome será sempre a Configuração seguida de um período e um `.` ou um nome de computador `localhost` específico.

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensão DSC em modelos de gestor de recursos

Na maioria dos cenários, os modelos de implementação do Gestor de Recursos são a forma esperada de trabalhar com a extensão DSC. Para obter mais informações e, por exemplo, como incluir a extensão DSC nos modelos de implementação do Gestor de Recursos, consulte [a extensão de configuração do estado desejada com os modelos do Gestor de Recursos Azure](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets powerShell de extensão DSC

Os cmdlets PowerShell que são usados para gerir a extensão DSC são melhor utilizados em cenários interativos de resolução de problemas e recolha de informações. Pode utilizar os cmdlets para embalar, publicar e monitorizar as extensões do DSC. Os cmdlets para a extensão DSC ainda não estão atualizados para funcionar com o [script de configuração padrão](#default-configuration-script).

O **cmdlet Publish-AzVMDscConfiguration** recebe um ficheiro de configuração, digitaliza-o para obter recursos DSC dependentes e, em seguida, cria um ficheiro .zip. O ficheiro .zip contém a configuração e os recursos DSC necessários para decretar a configuração. O cmdlet também pode criar o pacote localmente usando o parâmetro *-OutputArchivePath.* Caso contrário, o cmdlet publica o ficheiro .zip para o armazenamento de bolhas e, em seguida, fixa-o com um token SAS.

O script de configuração .ps1 que o cmdlet cria está no ficheiro .zip na raiz da pasta de arquivo. A pasta do módulo é colocada na pasta de arquivo em recursos.

O **cmdlet Set-AzVMDscExtension** injeta as definições que a extensão DSC powerShell requer num objeto de configuração VM.

O **cmdlet Get-AzVMDscExtension** recupera o estado de extensão DSC de um VM específico.

O **Get-AzVMDscExtensionStatus** cmdlet recupera o estado da configuração DSC que é decretada pelo manipulador de extensão DSC. Esta ação pode ser realizada num único VM ou num grupo de VMs.

O **cmdlet Remove-AzVMDscExtension** remove o manipulador de extensão de um VM específico. Este cmdlet *não* remove a configuração, desinstale o WMF, nem altere as definições aplicadas no VM. Só remove o manipulador de extensão.

Informações importantes sobre cmdlets de extensão DSC gestor de recursos:

- Os cmdlets do Azure Resource Manager são sincronizados.
- São necessários os parâmetros *ResourceGroupName*, *VMName,* *ArchiveStorageAccountName*, *Versão*e *Localização.*
- *ArchiveResourceGroupName* é um parâmetro opcional. Pode especificar este parâmetro quando a sua conta de armazenamento pertence a um grupo de recursos diferente daquele em que o VM é criado.
- Utilize o interruptor **AutoUpdate** para atualizar automaticamente o controlador de extensão para a versão mais recente quando estiver disponível. Este parâmetro tem o potencial de provocar o reinício no VM quando uma nova versão do WMF é lançada.

### <a name="get-started-with-cmdlets"></a>Começar com cmdlets

A extensão Azure DSC pode usar documentos de configuração DSC para configurar diretamente VMs Azure durante a implementação. Este passo não regista o nó para a Automação. O nó *não* é gerido centralmente.

O exemplo a seguir mostra um exemplo simples de uma configuração. Guarde a configuração localmente como iisInstall.ps1.

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

Os seguintes comandos colocam o iisInstall.ps1 script no VM especificado. Os comandos também executam a configuração e, em seguida, reportam de novo o estado.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Implantação do Azure CLI

O CLI Azure pode ser usado para implantar a extensão DSC a uma máquina virtual existente.

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

Para uma máquina virtual em funcionamento linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funcionalidade do portal Azure

Para configurar o DSC no portal:

1. Vai a um VM.
2. Em **Definições**, selecione **Extensões**.
3. Na nova página criada, selecione **+ Adicionar**e, em seguida, selecione **PowerShell Desired State Configuration**.
4. Clique em **Criar** na parte inferior da página de informações de extensão.

O portal recolhe a seguinte entrada:

- **Módulos de configuração ou Script**: Este campo é obrigatório (o formulário não foi atualizado para o [script de configuração predefinido).](#default-configuration-script) Os módulos de configuração e scripts requerem um ficheiro .ps1 que tenha um script de configuração ou um ficheiro .zip com um script de configuração .ps1 na raiz. Se utilizar um ficheiro .zip, todos os recursos dependentes devem ser incluídos nas pastas do módulo no .zip. Pode criar o ficheiro .zip utilizando a **Publish-AzureVMDscConfiguration -OutputArchivePath** cmdlet que está incluído no Azure PowerShell SDK. O ficheiro .zip é carregado para o armazenamento de bolhas do utilizador e protegido por um token SAS.

- **Nome de configuração qualificado por módulos**: Pode incluir múltiplas funções de configuração num ficheiro .ps1. Introduza o nome da configuração .escrita ps1 seguida \\ e o nome da função de configuração. Por exemplo, se o seu script .ps1 tiver o nome configuration.ps1 e a configuração for **IisInstall**, insira **configuration.ps1\IisInstall**.

- **Argumentos de configuração**: Se a função de configuração tiver argumentos, insira-os aqui no argumento de **formatoName1=valor1,argumentName2=value2**. Este formato é um formato diferente no qual os argumentos de configuração são aceites em cmdlets ou modelos de Gestor de Recursos PowerShell.

- **Ficheiro de dados de configuração PSD1**: Se a sua configuração necessitar de um ficheiro de dados de configuração, `.psd1` utilize este campo para selecionar o ficheiro de dados e carregá-lo para o armazenamento da bolha do utilizador. O ficheiro de dados de configuração é protegido por um símbolo SAS no armazenamento de bolhas.

- **Versão WMF**: Especifica a versão do Quadro de Gestão do Windows (WMF) que deve ser instalada no seu VM. Definir esta propriedade para as mais recentes instala a versão mais recente do WMF. Atualmente, os únicos valores possíveis para este imóvel são 4.0, 5.0, 5.1, e os mais recentes. Estes valores possíveis estão sujeitos a atualizações. O valor predefinido é **o mais recente**.

- **Recolha de Dados**: Determina se a extensão irá recolher telemetria. Para obter mais informações, consulte [a recolha de dados de extensão Azure DSC.](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/)

- **Versão**: Especifica a versão da extensão DSC para instalar. Para obter informações sobre versões, consulte [o histórico da versão de extensão DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Versão menor de atualização automática**: Este campo mapeia para o interruptor **AutoUpdate** nos cmdlets e permite que a extensão seja atualizada automaticamente para a versão mais recente durante a instalação. **Sim** instruirá o manipulador de extensão a utilizar a versão mais recente disponível e **o No** forçará a **versão** especificada para ser instalada. Selecionar nem **Sim** nem **Não** é o mesmo que selecionar **o Nº**.

## <a name="logs"></a>Registos

Os registos para a extensão são armazenados no seguinte local: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Passos seguintes

- Para obter mais informações sobre o PowerShell DSC, aceda ao [centro de documentação PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o [modelo de Gestor de Recursos para a extensão DSC](dsc-template.md).
- Para obter mais funcionalidades que possa gerir utilizando o PowerShell DSC e para obter mais recursos DSC, consulte a [galeria PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Para obter detalhes sobre a passagem de parâmetros sensíveis para as configurações, consulte [Gerir as credenciais de forma segura com o manipulador de extensão DSC](dsc-credentials.md).
