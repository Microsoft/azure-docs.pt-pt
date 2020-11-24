---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 8a442b5de7a256dd8bcf47da741bd41894709c3a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95562544"
---
Diagnosticar problemas com um serviço de nuvem Microsoft Azure requer a recolha dos ficheiros de registo do serviço em máquinas virtuais à medida que os problemas ocorrem. Pode utilizar a extensão AzureLogCollector a pedido para executar uma recolha única de registos a partir de um ou mais VMs do Cloud Service (tanto a partir de funções web como de trabalhador) e transferir os ficheiros recolhidos para uma conta de armazenamento Azure – tudo sem iniciar sessão remota em qualquer um dos VMs.

> [!NOTE]
> As descrições da maioria das informações registadas podem ser encontradas em https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Existem dois modos de recolha dependentes dos tipos de ficheiros a serem recolhidos.

* **Registos de agente convidado azure apenas (GA)**. Este modo de recolha inclui todos os registos relacionados com agentes convidados Azure e outros componentes Azure.
* **Todos os registos (completos)**. Este modo de recolha recolhe todos os ficheiros no modo GA mais:
  
  * registos de eventos de sistema e aplicação
  * Registos de erros HTTP
  * Registos do IIS
  * Registos de configuração
  * outros registos do sistema

Em ambos os modos de recolha, as pastas adicionais de recolha de dados podem ser especificadas utilizando uma recolha da seguinte estrutura:

* **Nome**: O nome da coleção, utilizada como nome da sub-página dentro do ficheiro zip com os ficheiros recolhidos.
* **Localização**: O caminho para a pasta na máquina virtual onde estão localizados os ficheiros a recolher.
* **SearchPattern**: O padrão dos nomes dos ficheiros a recolher. O padrão é \* "
* **Recursivo:** se os ficheiros a recolher estiverem novamente localizados no local especificado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Tenha uma conta de armazenamento para a extensão para guardar ficheiros zip gerados.
* Azure PowerShell. Consulte [a Instalação Azure PowerShell](/powershell/azure/install-az-ps)] para obter instruções de instalação.

## <a name="add-the-extension"></a>Adicionar a extensão
Pode utilizar cmdlets [Microsoft Azure PowerShell](/previous-versions/azure/dn495240(v=azure.100)) ou [SERVICE Management REST APIs](/previous-versions/azure/ee460799(v=azure.100)) para adicionar a extensão AzureLogCollector.

Para os Serviços cloud, o cmdlet Azure Powershell existente, **Set-AzureServiceExtension,** pode ser utilizado para permitir a extensão em instâncias de funções do Cloud Service. Sempre que esta extensão é ativada através deste cmdlet, a recolha de registos é ativada nas instâncias de função selecionadas de funções selecionadas.

Para máquinas virtuais, o cmdlet Azure Powershell existente, **Set-AzureVMExtension,** pode ser utilizado para permitir a extensão em Máquinas Virtuais. Sempre que esta extensão é ativada através dos cmdlets, a recolha de registos é ativada em cada instância.

Internamente, esta extensão utiliza a Configuração Pública baseada na JSON e a Configuração Privada. Segue-se o layout de uma amostra JSON para configuração pública e privada.

### <a name="publicconfiguration"></a>Configuação pública

```json
{
    "Instances":  "*",
    "Mode":  "Full",
    "SasUri":  "SasUri to your storage account with sp=wl",
    "AdditionalData":
    [
      {
              "Name":  "StorageData",
              "Location":  "%roleroot%storage",
              "SearchPattern":  "*.*",
              "Recursive":  "true"
      },
      {
            "Name":  "CustomDataFolder2",
            "Location":  "c:\customFolder",
            "SearchPattern":  "*.log",
            "Recursive":  "false"
      },
    ]
}
```

### <a name="privateconfiguration"></a>Configuação privada

```json
{

}
```

> [!NOTE]
> Esta extensão não necessita de **configuração privada.** Você pode apenas fornecer uma estrutura vazia para o argumento **de Configuração Privada.**
> 
> 

Pode seguir um dos dois passos seguintes para adicionar o AzureLogCollector a uma ou mais instâncias de um Serviço de Cloud ou Máquina Virtual de funções selecionadas, o que desencadeia as coleções em cada VM para executar e enviar os ficheiros recolhidos para a conta Azure especificada.

## <a name="adding-as-a-service-extension"></a>Adicionar como extensão de serviço
1. Siga as instruções para ligar a Azure PowerShell à sua subscrição.
2. Especifique o nome de serviço, slot, funções e instâncias de função às quais pretende adicionar e ativar a extensão AzureLogCollector.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'extensiontest2'

   #Specify the slot. 'Production' or 'Staging'
   $slot = 'Production'

   #Specified the roles on which the extension will be installed and enabled
   $roles = @("WorkerRole1","WebRole1")

   #Specify the instances on which extension will be installed and enabled.  Use wildcard * for all instances
   $instances = @("*")

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"
   ```

3. Especifique a pasta de dados adicional para a qual os ficheiros serão recolhidos (este passo é opcional).

   ```powershell
   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
   #more locations can be added....
   ```

   > [!NOTE]
   > Pode utilizar o token `%roleroot%` para especificar a unidade de raiz da função, uma vez que não utiliza uma unidade fixa.
   > 
   > 
4. Forneça o nome da conta de armazenamento Azure e a chave para a qual os ficheiros recolhidos serão carregados.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Ligue para o SetAzureServiceLogCollector.ps1 (incluído no final do artigo) da seguinte forma para ativar a extensão AzureLogCollector para um Serviço de Cloud. Uma vez concluída a execução, pode encontrar o ficheiro carregado em `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

Segue-se a definição dos parâmetros passados para o guião. (Isto é copiado abaixo também.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances,

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **Nome de serviço**: O nome do serviço na nuvem.
* **Funções**: Uma lista de funções, tais como "WebRole1" ou "WorkerRole1".
* **Instâncias**: Uma lista dos nomes de instâncias separadas por vírgula -- use a corda wildcard ("*") para todos os casos de função.
* **Ranhura**: Nome da ranhura. "Produção" ou "Encenação".
* **Modo**: Modo de recolha. "Full" ou "GA".
* **ArmazenamentoCocountName**: Nome da conta de armazenamento Azure para armazenar dados recolhidos.
* **ArmazenamentoColhokey**: Chave da conta de armazenamento Azure.
* **Adicional DataLocationList**: Uma lista da seguinte estrutura:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Adicionar como extensão VM
Siga as instruções para ligar a Azure PowerShell à sua subscrição.

1. Especifique o nome de serviço, VM e o modo de recolha.

   ```powershell
   #Specify your cloud service name
   $ServiceName = 'YourCloudServiceName'

   #Specify the VM name
   $VMName = "'YourVMName'"

   #Specify the collection mode, "Full" or "GA"
   $mode = "GA"

   Specify the additional data folder for which files will be collected (this step is optional).

   #add one location
   $a1 = New-Object PSObject

   $a1 | Add-Member -MemberType NoteProperty -Name "Name" -Value "StorageData"
   $a1 | Add-Member -MemberType NoteProperty -Name "SearchPattern" -Value "*"
   $a1 | Add-Member -MemberType NoteProperty -Name "Location" -Value "%roleroot%storage"  #%roleroot% is normally E: or F: drive
   $a1 | Add-Member -MemberType NoteProperty -Name "Recursive" -Value "true"

   $AdditionalDataList+= $a1
        #more locations can be added....
   ```
  
2. Forneça o nome da conta de armazenamento Azure e a chave para a qual os ficheiros recolhidos serão carregados.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Ligue para o SetAzureVMLogCollector.ps1 (incluído no final do artigo) da seguinte forma para ativar a extensão AzureLogCollector para um Serviço de Cloud. Uma vez concluída a execução, pode encontrar o ficheiro carregado em `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

Segue-se a definição dos parâmetros passados para o guião. (Isto é copiado abaixo também.)

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string] $VMName ,

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)
```

* **Nome de serviço**: O nome do serviço na nuvem.
* **VMName**: O nome do VM.
* **Modo**: Modo de recolha. "Full" ou "GA".
* **ArmazenamentoCocountName**: Nome da conta de armazenamento Azure para armazenar dados recolhidos.
* **ArmazenamentoColhokey**: Chave da conta de armazenamento Azure.
* **Adicional DataLocationList**: Uma lista da seguinte estrutura:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Extension PowerShell Script files
### <a name="setazureservicelogcollectorps1"></a>SetAzureServiceLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
  [Parameter(Mandatory=$true)]
  [string]   $ServiceName,

  [Parameter(Mandatory=$false)]
  [string[]] $Roles ,

  [Parameter(Mandatory=$false)]
  [string[]] $Instances = '*',

  [Parameter(Mandatory=$false)]
  [string]   $Slot = 'Production',

  [Parameter(Mandatory=$false)]
  [string]   $Mode = 'Full',

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountName,

  [Parameter(Mandatory=$false)]
  [string]   $StorageAccountKey,

  [Parameter(Mandatory=$false)]
  [PSObject[]] $AdditionDataLocationList = $null
)

$publicConfig = New-Object PSObject

if ($Instances -ne $null -and $Instances.Count -gt 0)  #Instances should be separated by ,
{
  $instanceText = $Instances[0]
  for ($i = 1;$i -lt $Instances.Count;$i++)
  {
    $instanceText = $instanceText+ "," + $Instances[$i]
  }
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value $instanceText
}
else  #For all instances if not specified.  The value should be a space or *
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value " "
}

if ($Mode -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
  "Container ($ContainerName) doesn't exist. Creating it now.."
  New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json
"publicConfig is:  $publicConfigJSON"

#we just provide an empty privateConfig object
$privateconfig = "{
}"

if ($Roles -ne $null)
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot -Role $Roles -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}
else
{
  Set-AzureServiceExtension -Service $ServiceName -Slot $Slot  -ExtensionName 'AzureLogCollector' -ProviderNamespace Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.0 -Verbose
}

#
#This is an optional step: generate a sasUri to the container so it can be shared with other people if needed.
#
$SasExpireTime = [DateTime]::Now.AddMinutes(120).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rl -Context $context
$SasUri = $SasUri + "&restype=container&comp=list"
Write-Output "The container for uploaded file can be accessed using this link:`r`n$sasuri"
```

### <a name="setazurevmlogcollectorps1"></a>SetAzureVMLogCollector.ps1

```powershell
[CmdletBinding(SupportsShouldProcess = $true)]

param (
              [Parameter(Mandatory=$true)]
              [string]   $ServiceName,

              [Parameter(Mandatory=$false)]
              [string] $VMName ,

              [Parameter(Mandatory=$false)]
              [string]   $Mode = 'Full',

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountName,

              [Parameter(Mandatory=$false)]
              [string]   $StorageAccountKey,

              [Parameter(Mandatory=$false)]
              [PSObject[]] $AdditionDataLocationList = $null
        )

$publicConfig = New-Object PSObject
$publicConfig | Add-Member -MemberType NoteProperty -Name "Instances" -Value "*"

if ($Mode -ne $null )
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value $Mode
}
else
{
    $publicConfig | Add-Member -MemberType NoteProperty -Name "Mode" -Value "Full"
}

#
#we need to get the Sasuri from StorageAccount and containers
#
$context = New-AzStorageContext -Protocol https -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

$ContainerName = "azurelogcollectordata"
$existingContainer = Get-AzStorageContainer -Context $context |  Where-Object { $_.Name -like $ContainerName}
if ($existingContainer -eq $null)
{
    "Container ($ContainerName) doesn't exist. Creating it now.."
    New-AzStorageContainer -Context $context -Name $ContainerName -Permission off
}

$ExpiryTime =  [DateTime]::Now.AddMinutes(90).ToString("o")
$SasUri = New-AzStorageContainerSASToken -ExpiryTime $ExpiryTime -FullUri -Name $ContainerName -Permission rwl -Context $context
$publicConfig | Add-Member -MemberType NoteProperty -Name "SasUri" -Value $SasUri

#
#Add AdditionalData to collect data from additional folders
#
if ($AdditionDataLocationList -ne $null )
{
  $publicConfig | Add-Member -MemberType NoteProperty -Name "AdditionalData" -Value $AdditionDataLocationList
}

#
# Convert it to JSON format
#
$publicConfigJSON = $publicConfig | ConvertTo-Json

Write-Output "PublicConfiguration is: \r\n$publicConfigJSON"

#
#we just provide an empty privateConfig object
#
$privateconfig = "{
}"

if ($VMName -ne $null )
{
      $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
      $VM.VM.OSVirtualHardDisk.OS

      if ($VM.VM.OSVirtualHardDisk.OS -like '*Windows*')
      {
            Set-AzureVMExtension -VM $VM -ExtensionName "AzureLogCollector" -Publisher Microsoft.WindowsAzure.Compute -PublicConfiguration $publicConfigJSON -PrivateConfiguration $privateconfig -Version 1.* | Update-AzureVM -Verbose

            #
            #We will check the VM status to find if operation by extension has been completed or not. The completion of the operation,either succeed or fail, can be indicated by
            #the presence of SubstatusList field.
            #
            $Completed = $false
            while ($Completed -ne $true)
            {
                    $VM = Get-AzureVM -ServiceName $ServiceName -Name $VMName
                    $status = $VM.ResourceExtensionStatusList | Where-Object {$_.HandlerName -eq "Microsoft.WindowsAzure.Compute.AzureLogCollector"}

                    if ( ($status.Code -ne 0) -and ($status.Status -like '*error*'))
                    {
                        Write-Output "Error status is returned: $($Status.ExtensionSettingStatus.FormattedMessage.Message)."
                          $Completed = $true
                    }
                    elseif (($status.ExtensionSettingStatus.SubstatusList -eq $null -or $status.ExtensionSettingStatus.SubstatusList.Count -lt 1))
                    {
                          $Completed = $false
                          Write-Output "Waiting for operation to complete..."
                    }
                    else
                    {
                          $Completed = $true
                          Write-Output "Operation completed."

                    $UploadedFileUri = $Status.ExtensionSettingStatus.SubStatusList[0].FormattedMessage.Message
                          $blob = New-Object Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob($UploadedFileUri)

                  #
                        # This is an optional step:  For easier access to the file, we can generate a read-only SasUri directly to the file
                          #
                          $ExpiryTimeRead =  [DateTime]::Now.AddMinutes(120).ToString("o")
                          $ReadSasUri = New-AzStorageBlobSASToken -ExpiryTime $ExpiryTimeRead  -FullUri  -Blob  $blob.name -Container $blob.Container.Name -Permission r -Context $context

                        Write-Output "The uploaded file can be accessed using this link: $ReadSasUri"

                          #
                          #This is an optional step:  Remove the extension after we are done
                          #
                          Get-AzureVM -ServiceName $ServiceName -Name $VMName | Set-AzureVMExtension -Publisher Microsoft.WindowsAzure.Compute -ExtensionName "AzureLogCollector" -Version 1.* -Uninstall | Update-AzureVM -Verbose

                    }
                    Start-Sleep -s 5
            }
      }
      else
      {
          Write-Output "VM OS Type is not Windows, the extension cannot be enabled"
      }

}
else
{
  Write-Output "VM name is not specified, the extension cannot be enabled"
}
```

## <a name="next-steps"></a>Passos Seguintes
Agora pode examinar ou copiar os seus registos a partir de um simples local.