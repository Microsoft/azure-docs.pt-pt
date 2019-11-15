---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 09c4420647043fccc408631fec75854667923721
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74085268"
---
O diagnóstico de problemas com um serviço de nuvem Microsoft Azure requer a coleta de arquivos de log do serviço em máquinas virtuais à medida que os problemas ocorrem. Você pode usar a extensão AzureLogCollector sob demanda para executar uma coleção única de logs de uma ou mais VMs de serviço de nuvem (de funções Web e funções de trabalho) e transferir os arquivos coletados para uma conta de armazenamento do Azure – tudo sem fazer logon remotamente em nenhum das VMs.

> [!NOTE]
> As descrições para a maioria das informações registradas podem ser encontradas em https://blogs.msdn.microsoft.com/kwill/2013/08/09/windows-azure-paas-compute-diagnostics-data/
> 
> 

Há dois modos de coleta dependentes dos tipos de arquivos a serem coletados.

* **Somente logs do agente convidado do Azure (GA)** . Esse modo de coleta inclui todos os logs relacionados aos agentes convidados do Azure e outros componentes do Azure.
* **Todos os logs (completo)** . Este modo de coleta coleta todos os arquivos no modo GA mais:
  
  * logs de eventos do sistema e do aplicativo
  * Logs de erros de HTTP
  * Registos do IIS
  * Logs de instalação
  * outros logs do sistema

Em ambos os modos de coleta, pastas de coleta de dados adicionais podem ser especificadas usando uma coleção da seguinte estrutura:

* **Nome**: o nome da coleção, usado como o nome da subpasta dentro do arquivo zip com os arquivos coletados.
* **Local**: o caminho para a pasta na máquina virtual onde os arquivos a serem coletados estão localizados.
* **SearchPattern**: o padrão dos nomes de arquivos a serem coletados. O padrão é "\*"
* **Recursivo**: se os arquivos a serem coletados estiverem localizados recursivamente sob o local especificado.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [updated-for-az](./updated-for-az.md)]

* Ter uma conta de armazenamento para a extensão para salvar os arquivos zip gerados.
* Azure PowerShell. Consulte [instalar Azure PowerShell](/powershell/azure/install-az-ps)] para obter instruções de instalação.

## <a name="add-the-extension"></a>Adicionar a extensão
Você pode usar cmdlets [Microsoft Azure PowerShell](https://msdn.microsoft.com/library/dn495240.aspx) ou [APIs REST de gerenciamento de serviço](https://msdn.microsoft.com/library/ee460799.aspx) para adicionar a extensão AzureLogCollector.

Para serviços de nuvem, o cmdlet do Azure PowerShell existente, **set-AzureServiceExtension**, pode ser usado para habilitar a extensão em instâncias de função de serviço de nuvem. Sempre que essa extensão é habilitada por meio desse cmdlet, a coleta de log é disparada nas instâncias de função selecionadas das funções selecionadas.

Para máquinas virtuais, o cmdlet do Azure PowerShell existente, **set-AzureVMExtension**, pode ser usado para habilitar a extensão em máquinas virtuais. Sempre que essa extensão é habilitada por meio dos cmdlets, a coleta de log é disparada em cada instância.

Internamente, essa extensão usa o PublicConfiguration e o PrivateConfiguration baseados em JSON. A seguir está o layout de um exemplo de JSON para configuração pública e privada.

### <a name="publicconfiguration"></a>PublicConfiguration

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

### <a name="privateconfiguration"></a>PrivateConfiguration

```json
{

}
```

> [!NOTE]
> Essa extensão não precisa de **privateConfiguration**. Você pode fornecer apenas uma estrutura vazia para o argumento **– PrivateConfiguration** .
> 
> 

Você pode seguir uma das duas etapas a seguir para adicionar o AzureLogCollector a uma ou mais instâncias de um serviço de nuvem ou máquina virtual de funções selecionadas, que dispara as coleções em cada VM para executar e enviar os arquivos coletados para a conta do Azure especificada.

## <a name="adding-as-a-service-extension"></a>Adicionando como uma extensão de serviço
1. Siga as instruções para se conectar Azure PowerShell à sua assinatura.
2. Especifique o nome do serviço, o slot, as funções e as instâncias de função aos quais você deseja adicionar e habilitar a extensão AzureLogCollector.

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

3. Especifique a pasta de dados adicional para a qual os arquivos serão coletados (essa etapa é opcional).

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
   > Você pode usar o token `%roleroot%` para especificar a unidade raiz da função, pois ela não usa uma unidade fixa.
   > 
   > 
4. Forneça o nome da conta de armazenamento do Azure e a chave para a qual os arquivos coletados serão carregados.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

5. Chame o SetAzureServiceLogCollector. ps1 (incluído no final do artigo) da seguinte maneira para habilitar a extensão AzureLogCollector para um serviço de nuvem. Quando a execução for concluída, você poderá encontrar o arquivo carregado em `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

   ```powershell
   .\SetAzureServiceLogCollector.ps1 -ServiceName YourCloudServiceName  -Roles $roles  -Instances $instances –Mode $mode -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey -AdditionDataLocationList $AdditionalDataList
   ```

A seguir está a definição dos parâmetros passados para o script. (Isso também é copiado abaixo.)

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

* **ServiceName: o**nome do serviço de nuvem.
* **Roles**: uma lista de funções, como "WebRole1" ou "WorkerRole1".
* **Instances**: uma lista de nomes de instâncias de função separadas por vírgula – use a cadeia de caracteres curinga ("*") para todas as instâncias de função.
* **Slot**: nome do slot. "Produção" ou "preparo".
* **Modo**: modo de coleta. "Completo" ou "GA".
* **StorageAccountName**: nome da conta de armazenamento do Azure para armazenar os dados coletados.
* **StorageAccountKey**: nome da chave de conta de armazenamento do Azure.
* **AdditionalDataLocationList**: uma lista da seguinte estrutura:

  ```powershell
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="adding-as-a-vm-extension"></a>Adicionando como uma extensão de VM
Siga as instruções para se conectar Azure PowerShell à sua assinatura.

1. Especifique o nome do serviço, a VM e o modo de coleta.

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
  
2. Forneça o nome da conta de armazenamento do Azure e a chave para a qual os arquivos coletados serão carregados.

   ```powershell
   $StorageAccountName = 'YourStorageAccountName'
   $StorageAccountKey  = 'YourStorageAccountKey'
   ```

3. Chame o Setazureservicelogcollector. ps1 (incluído no final do artigo) da seguinte maneira para habilitar a extensão AzureLogCollector para um serviço de nuvem. Quando a execução for concluída, você poderá encontrar o arquivo carregado em `https://YourStorageAccountName.blob.core.windows.net/vmlogs`

A seguir está a definição dos parâmetros passados para o script. (Isso também é copiado abaixo.)

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

* **ServiceName: o**nome do serviço de nuvem.
* **VMName**: o nome da VM.
* **Modo**: modo de coleta. "Completo" ou "GA".
* **StorageAccountName**: nome da conta de armazenamento do Azure para armazenar os dados coletados.
* **StorageAccountKey**: nome da chave de conta de armazenamento do Azure.
* **AdditionalDataLocationList**: uma lista da seguinte estrutura:

  ```
  {
    String Name,
    String Location,
    String SearchPattern,
    Bool   Recursive
  }
  ```

## <a name="extention-powershell-script-files"></a>Arquivos de script do PowerShell de extensão
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
Agora você pode examinar ou copiar os logs de um local simples.

