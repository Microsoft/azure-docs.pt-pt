---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d14cfb82ae74f85425dbd3e8a365e8b99969641d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485192"
---
## <a name="using-vm-extensions"></a>Utilizar extensões VM
Extensões de VM do Azure implementar a comportamentos ou recursos que o ajudam a outros programas funcionam em VMs do Azure (por exemplo, o **WebDeployForVSDevTest** extensão permite que o Visual Studio para Web Deploy soluções na sua VM do Azure) ou indique o capacidade para interagir com a VM para oferecer suporte a alguns outros comportamentos (por exemplo, pode utilizar as extensões de acesso à VM de clientes de PowerShell, CLI do Azure e REST para repor ou modificar os valores de acesso remoto na sua VM do Azure).

> [!IMPORTANT]
> Para obter uma lista completa das extensões pelos recursos que suportam, consulte [extensões de VM do Azure e as funcionalidades](../articles/virtual-machines/windows/extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Uma vez que cada extensão VM suporta uma funcionalidade específica, exatamente o que pode e não pode fazer com uma extensão depende a extensão. Por conseguinte, antes de modificar a sua VM, certifique-se de que leu a documentação para a extensão de VM que pretende utilizar. Remover algumas extensões de VM não é suportada; outros têm propriedades que podem ser definidas e alterar o comportamento VM radicalmente.
> 
> 

As tarefas mais comuns são:

1. Localizar extensões disponíveis
2. Atualizar extensões carregadas
3. Adicionar extensões
4. Remover extensões

## <a name="find-available-extensions"></a>Localizar extensões disponíveis
Pode localizar a extensão e a utilização de informações expandidas:

* PowerShell
* Interface de linha de comandos para várias plataformas do Azure (CLI do Azure)
* API REST da Gestão de Serviços

### <a name="azure-powershell"></a>Azure PowerShell
Algumas extensões têm de cmdlets do PowerShell que são específicas para eles, que podem facilitar suas configurações do PowerShell; mas os seguintes cmdlets funciona para todas as extensões VM.

Pode utilizar os seguintes cmdlets para obter informações sobre as extensões disponíveis:

* Para instâncias de funções da web ou funções de trabalho, pode utilizar o [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) cmdlet.
* Para instâncias de máquinas virtuais, pode utilizar o [Get-AzureVMAvailableExtension](https://msdn.microsoft.com/library/azure/dn722480.aspx) cmdlet.
  
   Por exemplo, o exemplo de código seguinte mostra como listar as informações para o **IaaSDiagnostics** extensão com o PowerShell.
  
      PS C:\> Get-AzureVMAvailableExtension -ExtensionName IaaSDiagnostics
  
      Publisher                   : Microsoft.Azure.Diagnostics
      ExtensionName               : IaaSDiagnostics
      Version                     : 1.2
      Label                       : Microsoft Monitoring Agent Diagnostics
      Description                 : Microsoft Monitoring Agent Extension
      PublicConfigurationSchema   :
      PrivateConfigurationSchema  :
      IsInternalExtension         : False
      SampleConfig                :
      ReplicationCompleted        : True
      Eula                        :
      PrivacyUri                  :
      HomepageUri                 :
      IsJsonExtension             : True
      DisallowMajorVersionUpgrade : False
      SupportedOS                 :
      PublishedDate               :
      CompanyName                 :

### <a name="azure-command-line-interface-azure-cli"></a>Interface de linha de comandos do Azure (CLI do Azure)
Tem de algumas extensões de comandos da CLI do Azure específicos a eles (a extensão de VM do Docker é um exemplo), que podem facilitar suas configurações; mas os comandos seguintes funcionam para todas as extensões VM.

Pode utilizar o **lista de extensão de vm do azure** comando para obter informações sobre as extensões disponíveis e utilizar os **–-json** opção para apresentar todas as informações disponíveis sobre uma ou mais extensões. Se não utilizar um nome de extensão, o comando devolve uma descrição de JSON de todas as extensões disponíveis.

Por exemplo, o exemplo de código seguinte mostra como listar as informações para o **IaaSDiagnostics** extensão com a CLI do Azure **lista de extensão de vm do azure** comando e utiliza o **–-json**  opção para devolver informações completas.

    $ azure vm extension list -n IaaSDiagnostics --json
    [
      {
        "publisher": "Microsoft.Azure.Diagnostics",
        "name": "IaaSDiagnostics",
        "version": "1.2",
        "label": "Microsoft Monitoring Agent Diagnostics",
        "description": "Microsoft Monitoring Agent Extension",
        "replicationCompleted": true,
        "isJsonExtension": true
      }
    ]



### <a name="service-management-rest-apis"></a>APIs REST de Gestão de Serviço
Pode utilizar as seguintes APIs de REST para obter informações sobre as extensões disponíveis:

* Para instâncias de funções da web ou funções de trabalho, pode utilizar o [lista de extensões disponíveis](https://msdn.microsoft.com/library/dn169559.aspx) operação. Para listar as versões das extensões disponíveis, pode usar [lista as versões de extensão](https://msdn.microsoft.com/library/dn495437.aspx).
* Para instâncias de máquinas virtuais, pode utilizar o [extensões de recursos lista](https://msdn.microsoft.com/library/dn495441.aspx) operação. Para listar as versões das extensões disponíveis, pode usar [versões de extensão do recurso de lista](https://msdn.microsoft.com/library/dn495440.aspx).

## <a name="add-update-or-disable-extensions"></a>Adicionar, atualizar ou desativar as extensões
As extensões podem ser adicionadas quando é criada uma instância ou podem ser adicionados a uma instância em execução. As extensões podem ser atualizadas, desativadas ou removidas. Pode efetuar estas ações utilizando cmdlets do PowerShell do Azure ou usando as operações de API de REST de gestão do serviço. Parâmetros são necessários para instalar e configurar algumas extensões. Parâmetros de públicos e privados são suportados para extensões.

### <a name="azure-powershell"></a>Azure PowerShell
Com os cmdlets do PowerShell do Azure é a maneira mais fácil para adicionar e atualizar as extensões. Ao utilizar os cmdlets de extensão, a maior parte da configuração da extensão é feito para. Às vezes, terá de adicionar programaticamente uma extensão. Quando tiver de fazer isso, tem de fornecer a configuração da extensão.

Pode utilizar os seguintes cmdlets para saber se uma extensão requer uma configuração de parâmetros públicas e privadas:

* Para instâncias de funções da web ou funções de trabalho, pode utilizar o **Get-AzureServiceAvailableExtension** cmdlet.
* Para instâncias de máquinas virtuais, pode utilizar o **Get-AzureVMAvailableExtension** cmdlet.

### <a name="service-management-rest-apis"></a>APIs REST de Gestão de Serviço
Quando recupera uma lista de extensões disponíveis ao utilizar as APIs REST, receber informações sobre como a extensão está a ser configurado. As informações que são devolvidas poderão mostrar informações de parâmetro representadas por um esquema pública e privada esquema. Valores de parâmetro público são devolvidos em consultas sobre as instâncias. Valores de parâmetro privadas não são devolvidas.

Pode utilizar as APIs REST do seguinte para saber se uma extensão requer uma configuração de parâmetros públicas e privadas:

* Para instâncias de funções da web ou funções de trabalho, o **PublicConfigurationSchema** e **PrivateConfigurationSchema** elementos contêm as informações na resposta do [lista disponível Extensões](https://msdn.microsoft.com/library/dn169559.aspx) operação.
* Para instâncias de máquinas virtuais, o **PublicConfigurationSchema** e **PrivateConfigurationSchema** elementos contêm as informações na resposta do [lista recursos Extensões](https://msdn.microsoft.com/library/dn495441.aspx) operação.

> [!NOTE]
> As extensões também podem utilizar as configurações que são definidas com JSON. Quando esses tipos de extensões são utilizados, apenas os **SampleConfig** elemento é usado.
> 
> 

