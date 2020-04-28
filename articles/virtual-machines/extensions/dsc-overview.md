---
title: Configuração do Estado desejada para visão geral de Azure
description: Saiba como utilizar o manipulador de extensão Microsoft Azure para a Configuração do Estado Desejado pela PowerShell (DSC). O artigo inclui pré-requisitos, arquitetura e cmdlets.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: 82d268eedd73b8de670da93ad3a601b5e75e6444
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188540"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao processador da extensão Desired State Configuration do Azure

O Agente Azure VM e as extensões associadas fazem parte dos serviços de infraestrutura do Microsoft Azure. As extensões VM são componentes de software que alargam a funcionalidade VM e simplificam várias operações de gestão vm.

O principal caso de utilização para a extensão de Configuração do Estado Pretendido (DSC) é a de aperte um VM ao serviço de Configuração do Estado de [Automação Azure (DSC).](../../automation/automation-dsc-overview.md)
O serviço oferece [benefícios](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) que incluem a gestão contínua da configuração vM e integração com outras ferramentas operacionais, como o Azure Monitoring.
A utilização da extensão para registar VM's no serviço fornece uma solução flexível que até funciona através das subscrições do Azure.

Pode utilizar a extensão DSC independentemente do serviço Automation DSC.
No entanto, isto só irá empurrar uma configuração para o VM.
Não há relatórios em curso, a não ser localmente no VM.

Este artigo fornece informações sobre ambos os cenários: utilização da extensão DSC para automação no embarque, e utilização da extensão DSC como uma ferramenta para atribuir configurações a VMs utilizando o SDK Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Máquina local**: Para interagir com a extensão Azure VM, deve utilizar o portal Azure ou o Azure PowerShell SDK.
- **Agente convidado**: O VM Azure configurado pela configuração DSC deve ser um OS que suporta a Estrutura de Gestão do Windows (WMF) 4.0 ou mais tarde. Para obter a lista completa de versões de OS suportadas, consulte o histórico da [versão de extensão DSC](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Termos e conceitos

Este guia assume familiaridade com os seguintes conceitos:

- **Configuração**: Um documento de configuração DSC.
- **Nó**: Um alvo para uma configuração DSC. Neste documento, o *nó* refere-se sempre a um VM Azure.
- **Dados de configuração**: Um ficheiro .psd1 que tem dados ambientais para uma configuração.

## <a name="architecture"></a>Arquitetura

A extensão Azure DSC utiliza a estrutura do Agente Azure VM para entregar, promulgar e reportar as configurações de DSC em execução em VMs Azure. A extensão DSC aceita um documento de configuração e um conjunto de parâmetros. Se não for fornecido nenhum ficheiro, um script de [configuração predefinido](#default-configuration-script) está incorporado com a extensão. O script de configuração predefinido é usado apenas para definir metadados no Gestor de [Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig).

Quando a extensão é chamada pela primeira vez, instala uma versão do WMF utilizando a seguinte lógica:

- Se o Sistema Operativo Azure VM for o Windows Server 2016, não são tomadas medidas. O Windows Server 2016 já tem a versão mais recente do PowerShell instalada.
- Se a propriedade **wmfVersion** for especificada, esta versão do WMF é instalada, a menos que essa versão seja incompatível com o OS do VM.
- Se não for especificada nenhuma propriedade **wmfVersion,** a versão mais recente aplicável do WMF é instalada.

A instalação do WMF requer um reinício. Depois de reiniciar, a extensão descarrega o ficheiro .zip especificado na propriedade **módulosUrl,** se fornecido. Se esta localização estiver no armazenamento da Azure Blob, pode especificar um token SAS na propriedade **sasToken** para aceder ao ficheiro. Depois de descarregado e desembalado o .zip, a função de configuração definida na **configuração Function** executa para gerar um ficheiro .mof[(Formato de Objeto Gerido).](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-) A extensão `Start-DscConfiguration -Force` é então executado utilizando o ficheiro .mof gerado. A extensão captura a saída e escreve-a para o canal de estado do Azure.

### <a name="default-configuration-script"></a>Script de configuração padrão

A extensão Azure DSC inclui um script de configuração predefinido que se destina a ser usado quando você a bordo de um VM para o serviço DSC de Automação Azure. Os parâmetros do script estão alinhados com as propriedades configuráveis do Gestor de [Configuração Local](/powershell/scripting/dsc/managing-nodes/metaConfig). Para os parâmetros do script, consulte o script de [configuração predefinido](dsc-template.md#default-configuration-script) na [extensão de configuração do Estado desejado com os modelos do Gestor de Recursos Azure](dsc-template.md). Para o script completo, consulte o [modelo de quickstart Azure no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informações para o registo com o serviço de Configuração do Estado da Automação Azure (DSC)

Ao utilizar a extensão DSC para registar um nó no serviço de Configuração do Estado, terão de ser fornecidos três valores.

- RegistrationUrl - o endereço https da conta Azure Automation
- RegistrationKey - um segredo partilhado usado para registar nódosos com o serviço
- NodeConfigurationName - o nome da Configuração do Nó (MOF) para retirar do serviço para configurar a função do servidor

Estas informações podem ser vistas no portal Azure ou pode utilizar o PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Para o nome de configuração do nó, certifique-se de que a configuração do nó existe na Configuração do Estado do Azure.  Se não o fizer, a extensão devolverá uma falha.  Certifique-se também de que está a usar o nome da *Configuração* do Nó e não a Configuração.
Uma Configuração é definida num script que é usado [para compilar a Configuração do Nó (ficheiro MOF)](https://docs.microsoft.com/azure/automation/automation-dsc-compile).
O nome será sempre a Configuração seguida de um período `.` e um `localhost` nome de computador específico.

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensão DSC nos modelos do Gestor de Recursos

Na maioria dos cenários, os modelos de implementação do Gestor de Recursos são a forma esperada de trabalhar com a extensão DSC. Para mais informações e, por exemplo, de como incluir a extensão DSC nos modelos de implementação do Gestor de Recursos, consulte a extensão de [Configuração do Estado Desejado com modelos](dsc-template.md)de Gestor de Recursos Azure .

## <a name="dsc-extension-powershell-cmdlets"></a>Cmdlets de extensão DSC PowerShell

Os cmdlets PowerShell que são usados para gerir a extensão DSC são melhor utilizados em cenários interativos de resolução de problemas e recolha de informações. Pode utilizar os cmdlets para embalar, publicar e monitorizar as extensões de extensão do DSC. Os cmdlets para a extensão DSC ainda não estão atualizados para funcionar com o script de [configuração padrão](#default-configuration-script).

O **Cmdlet De Configuração Publish-AzVMDsc** Adlet tem um ficheiro de configuração, digitaliza-o para obter recursos DSC dependentes e, em seguida, cria um ficheiro .zip. O ficheiro .zip contém a configuração e os recursos DSC que são necessários para decretar a configuração. O cmdlet também pode criar a embalagem localmente utilizando o parâmetro *-OutputArchivePath.* Caso contrário, o cmdlet publica o ficheiro .zip para o armazenamento de bolhas e, em seguida, fixa-o com um token SAS.

O script de configuração .ps1 que o cmdlet cria está no ficheiro .zip na raiz da pasta de arquivo. A pasta do módulo é colocada na pasta de arquivo em recursos.

O **cmdlet set-AzVMDscExtension** injeta as definições que a extensão DoSC PowerShell necessita num objeto de configuração VM.

O **cmdlet de extensão Get-AzVMDsc** recupera o estado de extensão DSC de um VM específico.

O **Cmdlet Get-AzVMDscExtensionStatus** recupera o estado da configuração DSC que é decretada pelo manipulador de extensão DSC. Esta ação pode ser realizada num único VM ou num grupo de VMs.

O **cmdlet de extensão remove** o manipulador de extensão de um VM específico. Este cmdlet *não* remove a configuração, desinstala o WMF ou altera as definições aplicadas no VM. Só remove o manipulador de extensão.

Informações importantes sobre a extensão do Gestor de Recursos DSC cmdlets:

- Os cmdlets do Azure Resource Manager são sincronizados.
- São todos necessários os parâmetros *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName,* *Versão*e *Localização.*
- *ArchiveResourceGroupName* é um parâmetro opcional. Pode especificar este parâmetro quando a sua conta de armazenamento pertence a um grupo de recursos diferente daquele em que o VM é criado.
- Utilize o interruptor **AutoUpdate** para atualizar automaticamente o manipulador de extensões para a versão mais recente quando estiver disponível. Este parâmetro tem o potencial de causar reinícios no VM quando uma nova versão da WMF for lançada.

### <a name="get-started-with-cmdlets"></a>Começar com cmdlets

A extensão Azure DSC pode utilizar documentos de configuração DSC para configurar diretamente os VMs Azure durante a implantação. Este passo não regista o nó para automação. O nó *não* é gerido centralmente.

O exemplo que se segue mostra um exemplo simples de uma configuração. Guarde a configuração localmente como iisInstall.ps1.

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

Os seguintes comandos coloquem o script iisInstall.ps1 no VM especificado. Os comandos também executam a configuração, e depois reportam de volta sobre o estado.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Implantação Azure CLI

O Azure CLI pode ser utilizado para implantar a extensão DSC a uma máquina virtual existente.

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

## <a name="azure-portal-functionality"></a>Funcionalidade do portal Azure

Para configurar a DSC no portal:

1. Vai a um VM.
2. Em **Definições**, selecione **Extensões**.
3. Na nova página criada, selecione **+ Adicionar**, e, em seguida, selecione **PowerShell Desired State Configuration**.
4. Clique em **Criar** na parte inferior da página de informação de extensão.

O portal recolhe a seguinte entrada:

- **Módulos de configuração ou Script**: Este campo é obrigatório (o formulário não foi atualizado para o script de [configuração predefinido](#default-configuration-script)). Os módulos de configuração e scripts requerem um ficheiro .ps1 que tenha um script de configuração ou um ficheiro .zip com um script de configuração .ps1 na raiz. Se utilizar um ficheiro .zip, todos os recursos dependentes devem ser incluídos em pastas de módulos no .zip. Pode criar o ficheiro .zip utilizando o **Cmdlet Publish-AzureVMDscConfiguration -OutputArchivePath** que está incluído no SDK Azure PowerShell. O ficheiro .zip é enviado para o armazenamento de bolhas do utilizador e protegido por um token SAS.

- **Nome de configuração qualificado para módulos:** Pode incluir múltiplas funções de configuração num ficheiro .ps1. Introduza o nome do script de \\ configuração .ps1 seguido e o nome da função de configuração. Por exemplo, se o seu script .ps1 tiver a configuração do nome.ps1 e a configuração for **IisInstall**, **introduza configuração.ps1\IisInstalar**.

- **Configuração Argumentos**: Se a função de configuração tiver argumentos, introduza-os aqui no **formato nome1=valor1,argumentName2=value2**. Este formato é um formato diferente no qual os argumentos de configuração são aceites em cmdlets PowerShell ou modelos de Gestor de Recursos.

- **Arquivo De Dados de Configuração PSD1**: A sua configuração requer um ficheiro de dados de configuração em .psd1, utilize este campo para selecionar o ficheiro de dados e carregá-lo para o armazenamento de blob do utilizador. O ficheiro de dados de configuração é protegido por um token SAS no armazenamento de blob.

- **Versão WMF**: Especifica a versão da Estrutura de Gestão do Windows (WMF) que deve ser instalada no seu VM. A definição desta propriedade para a mais recente instala a versão mais recente da WMF. Atualmente, os únicos valores possíveis para este imóvel são 4.0, 5.0, 5.1, e mais recentes. Estes valores possíveis estão sujeitos a atualizações. O valor predefinido é **mais recente.**

- **Recolha de Dados**: Determina se a extensão irá recolher telemetria. Para mais informações, consulte a recolha de dados de [extensão Azure DSC.](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/)

- **Versão**: Especifica a versão da extensão DSC para instalar. Para obter informações sobre versões, consulte o histórico da [versão de extensão DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Atualização automática Versão Menor**: Este campo mapeia o interruptor **AutoUpdate** nos cmdlets e permite que a extensão atualize automaticamente para a versão mais recente durante a instalação. **Sim,** instruirá o manipulador de extensões a utilizar a versão mais recente disponível e **o No** obrigará a **versão** especificada a ser instalada. Selecionar nem **Sim** nem **Não** é o mesmo que selecionar **No**.

## <a name="logs"></a>Registos

Os registos da extensão são armazenados no seguinte local:`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Passos seguintes

- Para mais informações sobre o PowerShell DSC, vá ao centro de [documentação PowerShell](/powershell/scripting/dsc/overview/overview).
- Examine o modelo do Gestor de [Recursos para a extensão DSC](dsc-template.md).
- Para obter mais funcionalidades que possa gerir utilizando o PowerShell DSC e para obter mais recursos DSC, navegue na [galeria PowerShell.](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0)
- Para mais detalhes sobre a passagem de parâmetros sensíveis em configurações, consulte [Gerir as credenciais de forma segura com o manipulador de extensão DSC](dsc-credentials.md).
