---
title: Desired State Configuration para descrição geral do Azure
description: Saiba como utilizar o processador de extensão do Microsoft Azure para PowerShell Desired State Configuration (DSC). O artigo inclui os pré-requisitos, arquitetura e cmdlets.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: ba5baa928e60729aa128ca5097646768cf5656e8
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57441923"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Introdução ao manipulador de extensão Azure Desired State Configuration

O agente da VM do Azure e ramais associados são parte dos serviços de infraestrutura do Microsoft Azure. Extensões de VM são componentes de software que expandem a funcionalidade de VM e simplificam a várias operações de gestão da VM.

O principal caso de utilização para a extensão Azure Desired State Configuration (DSC) é inicializar uma VM para o [serviço de configuração de estado de automatização do Azure (DSC)](../../automation/automation-dsc-overview.md).
O serviço fornece [benefícios](/powershell/dsc/metaconfig#pull-service) que incluem o gerenciamento contínuo da configuração da VM e integração com outras ferramentas operacionais, tais como a monitorização do Azure.
Com a extensão para registar a da VM para o serviço fornece uma solução flexível que funciona até mesmo entre subscrições do Azure.

Pode utilizar a extensão DSC independentemente do serviço de DSC de automatização.
No entanto, isto irá enviar apenas uma configuração para a VM.
Não existem relatórios em andamento estão disponíveis, diferente do localmente na VM.

Este artigo fornece informações sobre os dois cenários: utilizar a extensão de DSC para a integração da automatização e usando a extensão DSC como uma ferramenta para atribuição de configurações para VMs com o SDK do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- **Computador local**: Para interagir com a extensão de VM do Azure, tem de utilizar o portal do Azure ou o SDK do PowerShell do Azure.
- **Agente convidado**: VM do Azure que está configurado na configuração de DSC tem de ser um sistema operacional que oferece suporte ao Windows Management Framework (WMF) 4.0 ou posterior. Para obter a lista completa das versões de SO suportadas, consulte a [histórico de versões de extensão do DSC](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Termos e conceitos

Este guia pressupõe que está familiarizado com os seguintes conceitos:

- **Configuração**: Um documento de configuração de DSC.
- **Nó**: Um destino para uma configuração de DSC. Este documento *nó* sempre se refere a uma VM do Azure.
- **Dados de configuração**: Um ficheiro. psd1 que tem dados ambientais para uma configuração.

## <a name="architecture"></a>Arquitetura

A extensão DSC do Azure utiliza a estrutura do agente de VM do Azure para entregar, aplicá e gerar relatórios sobre configurações de DSC em execução em VMs do Azure. A extensão DSC aceita um documento de configuração e um conjunto de parâmetros. Se não for fornecido nenhum ficheiro, um [predefinida de script de configuração](#default-configuration-script) é incorporado com a extensão. O script de configuração padrão é utilizado apenas para os metadados do conjunto [Gestor de configuração Local](/powershell/dsc/metaconfig).

Quando a extensão é chamada pela primeira vez, instala uma versão do WMF, utilizando a seguinte lógica:

- Se o SO da VM do Azure é o Windows Server 2016, foi efetuada nenhuma ação. Windows Server 2016 já tem a versão mais recente do PowerShell instalado.
- Se o **wmfVersion** é especificada a propriedade, essa versão do WMF estiver instalado, a menos que essa versão é incompatível com o SO da VM.
- Se nenhum **wmfVersion** propriedade for especificada, a versão aplicável mais recente do WMF está instalada.

A instalação de WMF requer um reinício. Após o reinício, a extensão transfere o ficheiro. zip que é especificado na **modulesUrl** propriedade, se fornecido. Se esta localização estiver no armazenamento de Blobs do Azure, pode especificar um token SAS no **sasToken** propriedade para aceder ao ficheiro. Depois da. zip é baixado e descompactado, a função de configuração definida no **configurationFunction** é executado para gerar um arquivo. MOF. Em seguida, executa a extensão `Start-DscConfiguration -Force` usando o arquivo. MOF gerado. A extensão de saída de captura e escreve-a para o canal de estado do Azure.

### <a name="default-configuration-script"></a>Script de configuração predefinida

A extensão DSC do Azure inclui um script de configuração predefinidas que tem se destina a ser utilizado quando carregar uma VM para o serviço de DSC de automatização do Azure. Os parâmetros do script estão alinhados com as propriedades configuráveis do [Gestor de configuração Local](/powershell/dsc/metaconfig). Para os parâmetros de script, consulte [predefinida de script de configuração](dsc-template.md#default-configuration-script) na [extensão Desired State Configuration do com modelos Azure Resource Manager](dsc-template.md). Para o script completo, consulte a [modelo de início rápido do Azure no GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informações para registar com o serviço de configuração de estado de automatização do Azure (DSC)

Quando utilizar a extensão de DSC para registar um nó com o serviço de configuração de estado, os três valores terá de ser fornecido.

- RegistrationUrl - o endereço de https da conta de automatização do Azure
- RegistrationKey - um segredo partilhado utilizado para registar nós com o serviço
- NodeConfigurationName - o nome da configuração de nó (MOF) para solicitar a partir do serviço para configurar a função de servidor

Estas informações podem ser vistas na [portal do Azure](../../automation/automation-dsc-onboarding.md#azure-portal) ou pode utilizar o PowerShell.

```PowerShell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Para o nome de configuração do nó, certifique-se de que está a utilizar o nome da *configuração do nó* e não a configuração.
Uma configuração é definida num script que é utilizado [para compilar a configuração de nó (arquivo MOF)](https://docs.microsoft.com/en-us/azure/automation/automation-dsc-compile).
O nome será sempre a configuração seguida de ponto final `.` e qualquer um dos `localhost` ou um nome de computador específico.

## <a name="dsc-extension-in-resource-manager-templates"></a>Extensão DSC em modelos do Resource Manager

Na maioria dos cenários, os modelos de implementação do Resource Manager estão da forma esperada para trabalhar com a extensão de DSC. Para obter mais informações e exemplos de como incluir a extensão DSC em modelos de implementação do Resource Manager, veja [extensão de Desired State Configuration com modelos Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Extensão de DSC cmdlets do PowerShell

Os cmdlets do PowerShell que são utilizados para gerir a extensão de DSC são recomendados para interativa de resolução de problemas e cenários de coleta de informações. Pode utilizar os cmdlets do empacotamento, publicar e monitorizar as implementações de extensão do DSC. Cmdlets para a extensão DSC ainda não são atualizados para funcionar com o [predefinida de script de configuração](#default-configuration-script).

O **Publish-AzVMDscConfiguration** cmdlet aceita um ficheiro de configuração, ele procura recursos de DSC dependentes e, em seguida, cria um ficheiro. zip. O ficheiro. zip contém a configuração e os recursos de DSC que são necessários adotar a configuração. O cmdlet também pode criar o pacote localmente ao utilizar o *- OutputArchivePath* parâmetro. Caso contrário, o cmdlet publica o arquivo. zip para o armazenamento de BLOBs e, em seguida, protege-la com um token SAS.

O script de configuração. ps1 que o cmdlet cria está no arquivo. zip na raiz da pasta de arquivo. A pasta de módulo é colocada na pasta de arquivo nos recursos.

O **Set-AzVMDscExtension** cmdlet injeta as definições que requer a extensão DSC do PowerShell num objeto de configuração de VM.

O **Get-AzVMDscExtension** cmdlet obtém o estado da extensão DSC de uma VM específica.

O **Get-AzVMDscExtensionStatus** cmdlet obtém o estado da configuração do DSC que é elaborada pelo processador de extensão de DSC. Esta ação pode ser efetuada numa única VM ou num grupo de VMs.

O **Remove-AzVMDscExtension** cmdlet Remove o manipulador de extensão de uma VM específica. Este cmdlet faz *não* remover a configuração, desinstale o WMF ou alterar as definições aplicadas na VM. Ele apenas remove o manipulador de extensão. 

Obter informações importantes sobre os cmdlets de extensão DSC do Gestor de recursos:

- Cmdlets do Azure Resource Manager são síncronos.
- O *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *versão*, e *localização*são necessários todos os parâmetros.
- *ArchiveResourceGroupName* é um parâmetro opcional. Pode especificar este parâmetro quando a sua conta de armazenamento pertence a um grupo de recursos diferente em que a VM é criada.
- Utilize o **AutoUpdate** comutador para atualizar automaticamente o manipulador de extensão para a versão mais recente, quando estiver disponível. Este parâmetro tem o potencial de causar reinícios na VM quando for lançada uma nova versão do WMF.

### <a name="get-started-with-cmdlets"></a>Introdução aos cmdlets

A extensão DSC do Azure pode utilizar os documentos de configuração de DSC para configurar diretamente as VMs do Azure durante a implementação. Este passo não registar o nó para a automatização. O nó está *não* gerenciado centralmente.

O exemplo seguinte mostra um exemplo simples de uma configuração. Guarde a configuração de localmente como IisInstall.ps1.

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

Os seguintes comandos colocam o script de IisInstall.ps1 na VM especificada. Os comandos também executar a configuração e, em seguida, comunicam de volta no estado.

```powershell
$resourceGroup = 'dscVmDemo'
$location = 'westus'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate $true -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Implementação de CLI do Azure

A CLI do Azure pode ser utilizada para implementar a extensão de DSC para uma máquina virtual existente.

Para uma máquina virtual com o Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Para uma máquina virtual em execução no Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funcionalidade de portal do Azure

Para configurar o DSC no portal do:

1. Aceda a uma VM.
2. Em **Definições**, selecione **Extensões**.
3. Na página nova é criada, selecione **+ adicionar**e, em seguida, selecione **PowerShell Desired State Configuration**.
4. Clique em **criar** na parte inferior da página de informações de extensão.

O portal recolhe a entrada seguinte:

- **Módulos de configuração ou Script**: Este campo é obrigatório (o formulário não tiver sido atualizado para o [predefinida de script de configuração](#default-configuration-script)). Scripts e módulos de configuração requerem um ficheiro. ps1, que tem um script de configuração ou um ficheiro. zip com um script de configuração. ps1 na raiz. Se utilizar um ficheiro. zip, todos os recursos dependentes têm de ser incluídos em pastas de módulo na. zip. Pode criar o ficheiro. zip com o **Publish-AzureVMDscConfiguration - OutputArchivePath** cmdlet que está incluído no SDK do PowerShell do Azure. O ficheiro. zip é carregado para o armazenamento de Blobs do utilizador e protegido por um token SAS.

- **O nome qualificado do módulo de configuração**: Pode incluir várias funções de configuração num arquivo. ps1. Introduza o nome do script. ps1 configuração seguido \\ e o nome da função de configuração. Por exemplo, se o seu script. ps1 tiver configuration.ps1 o nome e a configuração é **IisInstall**, introduza **configuration.ps1\IisInstall**.

- **Argumentos de configuração**: Se a função de configuração aceita argumentos, insira-os aqui no formato **argumentName1 = value1, argumentName2 = value2**. Esse formato é um formato diferente em que os argumentos de configuração são aceites em cmdlets do PowerShell ou modelos do Resource Manager.

- **Ficheiro de configuração de dados PSD1**: Este campo é opcional. Se a configuração necessitar de um ficheiro de dados de configuração. psd1, utilize este campo para selecionar o campo de dados e carregá-lo para seu armazenamento de Blobs do utilizador. O ficheiro de dados de configuração está protegido por um token SAS no armazenamento de Blobs.

- **Versão do WMF**: Especifica a versão do Windows Management Framework (WMF) que deve ser instalado na sua VM. A definição dessa propriedade para instalações mais recente a versão mais recente do WMF. Atualmente, os valores possíveis apenas para esta propriedade são 4.0, 5.0, 5.1 e mais recentes. Estes valores possíveis são sujeitos a atualizações. O valor predefinido é **mais recente**.

- **Recolha de dados**: Determina se a extensão irá recolher telemetria. Para obter mais informações, consulte [recolha de dados de extensão DSC do Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Versão**: Especifica a versão da extensão do DSC para instalar. Para obter informações sobre as versões, veja [histórico de versões de extensão do DSC](/powershell/dsc/azuredscexthistory).

- **Automaticamente a atualização de versão secundária**: Este campo é mapeado para o **AutoUpdate** mudar nos cmdlets e permite a extensão atualizar automaticamente para a versão mais recente durante a instalação. **Sim** instruirá o manipulador de extensão para utilizar a versão mais recente disponível e **não** irá forçar o **versão** especificada para ser instalado. Selecionar nenhum deles **Sim** nem **não** é igual a selecionando **não**.

## <a name="logs"></a>Registos

Os registos para a extensão são armazenados na seguinte localização: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações sobre o DSC de PowerShell, vá para o [Centro de documentação do PowerShell](/powershell/dsc/overview).
- Examine os [modelo do Resource Manager para a extensão DSC](dsc-template.md).
- Para mais funcionalidades que pode gerir utilizando o PowerShell DSC e para mais recursos de DSC, procurar as [galeria do PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Para obter detalhes sobre passando parâmetros confidenciais em configurações, consulte [gerir credenciais com segurança com o processador de extensão DSC](dsc-credentials.md).
