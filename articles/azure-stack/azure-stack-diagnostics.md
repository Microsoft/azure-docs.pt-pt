---
title: Diagnóstico no Azure Stack
description: Como recolher ficheiros de registo para obter um diagnóstico no Azure Stack
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 11/20/2018
ms.author: jeffgilb
ms.reviewer: adshar
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 41609a75efec6c15d894b61867fb9d3ae727dc8c
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57440561"
---
# <a name="azure-stack-diagnostics-tools"></a>Ferramentas de diagnóstico do Azure Stack

O Azure Stack é uma grande coleção de componentes a trabalhar em conjunto e interagir entre si. Todos estes componentes geram seus próprios registos exclusivos. Isso pode tornar o diagnóstico de problemas uma tarefa desafiadora, especialmente para erros provenientes de múltiplos, interação de componentes do Azure Stack. 

Nossas ferramentas de diagnóstico ajudam a garantir que o mecanismo de coleta de log é fácil e eficiente. O diagrama seguinte mostra como fazer logon ferramentas de coleta em projetos de Azure Stack:

![Ferramentas de diagnóstico do Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)
 
 
## <a name="trace-collector"></a>Recoletor do rastreio
 
O Recoletor do rastreio está ativado por predefinição e seja executada continuamente em segundo plano para recolher todos os registos de rastreio de eventos para Windows (ETW) de serviços de componentes do Azure Stack. Os registos ETW são armazenados numa partilha local comum com um limite de idade de cinco dias. Quando o limite é alcançado, os ficheiros mais antigos são eliminados à medida que são criados novos. O tamanho máximo de predefinição permitido para cada arquivo é 200 MB. Uma verificação de tamanho ocorre a cada 2 minutos, e se o ficheiro atual for > = 200 MB, é guardado e um novo ficheiro é gerado. Também existe um limite de 8 GB no tamanho total do ficheiro gerado por sessão do evento. 

## <a name="log-collection-tool"></a>Ferramenta de registo de coleção
 
O cmdlet do PowerShell **Get-AzureStackLog** pode ser utilizado para recolher registos de todos os componentes num ambiente do Azure Stack. Salva-os nos arquivos zip numa localização definido pelo utilizador. Se a equipe de suporte técnico do Azure Stack tem os seus registos para ajudar a resolver um problema, eles poderão pedir-lhe para executar esta ferramenta.

> [!CAUTION]
> Estes ficheiros de registo podem conter informações de identificação pessoal (PII). Tenha isso em conta antes de publicar publicamente quaisquer ficheiros de registo.
 
Seguem-se alguns tipos de registo de exemplo que são recolhidos:
*   **Registos de implementação do Azure Stack**
*   **Registos de eventos do Windows**
*   **Registos de Panther**
*   **Registos do cluster**
*   **Registos de diagnóstico de armazenamento**
*   **Registos ETW**

Estes ficheiros são recolhidos e guardados numa partilha pelo coletor de rastreio. O **Get-AzureStackLog** cmdlet do PowerShell, em seguida, pode ser utilizado para recolhê-los quando necessário.

### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Para executar Get-AzureStackLog no Azure Stack de sistemas integrados 
Para executar a ferramenta de coleção de log num sistema integrado, tem de ter acesso ao ponto final com privilégios (PEP). Eis um script de exemplo, pode executar usando o PEP para recolher registos num sistema integrado:

```powershell
$ip = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.
 
$pwd= ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $pwd)
 
$shareCred = Get-Credential
 
$s = New-PSSession -ComputerName $ip -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2)  #provide the time that includes the period for your issue
 
Invoke-Command -Session $s {    Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if($s)
{
    Remove-PSSession $s
}
```



### <a name="to-run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Para executar Get-AzureStackLog num sistema de Azure Stack Development Kit (ASDK)
Utilize estes passos para executar Get-AzureStackLog num computador anfitrião ASDK.

1. Inicie sessão como **AzureStack\CloudAdmin** no computador anfitrião ASDK.
2. Abra uma nova janela do PowerShell como administrador.
3. Executar o **Get-AzureStackLog** cmdlet do PowerShell.

**Exemplos:**

  Recolha todos os registos para todas as funções:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred
  ```

  Recolha registos de funções VirtualMachines e bare-metal:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

  Recolha registos de funções VirtualMachines e bare-metal, com data de filtragem para ficheiros de registo para as últimos 8 horas:
    
  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

  Recolha registos de funções VirtualMachines e bare-metal, com data de filtragem para ficheiros de registo para o período de tempo entre 8 horas atrás e 2 horas atrás:

  ```powershell
  Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Considerações de parâmetro para ASDK e sistemas integrados

- Os parâmetros **OutputSharePath** e **OutputShareCredential** são utilizados para armazenar os registos de sessão de um utilizador especificado localização.

- O **FromDate** e **ToDate** parâmetros podem ser utilizados para recolher registos de um período de tempo específico. Esses parâmetros são se não for especificados, os registos são recolhidos para as últimos quatro horas por predefinição.

- Utilize o **FilterByNode** parâmetro para filtrar registos por nome do computador. Por exemplo:

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```
- Utilize o **FilterByLogType** parâmetro para filtrar registos por tipo. Pode optar por filtrar por ficheiro, a partilha ou WindowsEvent. Por exemplo:

    ```powershell
    Get-AzureStackLog -OutputSharePath “<path>” -OutputShareCredential $cred -FilterByLogType File
    ```
- Pode utilizar o **TimeOutInMinutes** parâmetro para definir o tempo limite para a recolha de registos. Ele é definido como 150 (2,5 horas) por predefinição.
- Recolha de informação de registo de ficheiros está desativada por predefinição. Para ativá-la, utilize o **IncludeDumpFile** mudar o parâmetro. 
- Atualmente, pode utilizar o **FilterByRole** parâmetro para a recolha de registos de filtro para as seguintes funções:

 |   |   |   |    |
 | - | - | - | -  |   
 |ACS                   |CacheService                   |IBC                            |OEM|
 |ACSDownloadService    |Computação                        |InfraServiceController         |OnboardRP|
 |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE|
 |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator|
 |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker|
 |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing|
 |ACSMonitoringService  |Domain                         |KeyVaultInternalDataPlane      |SeedRingServices|
 |ACSSettingsService    |ECE                            |KeyVaultNamingService          |SLB|
 |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL|
 |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP   |
 |ACSWac                |ExternalDNS                    |MetricsRP                      |Armazenamento|
 |ADFS                  |FabricRing                     |MetricsServer                  |StorageController   |
 |ApplicationController |FabricRingServices             |MetricsStoreService            |URP   |
 |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |UsageBridge|
 |AzureBridge           |FRP                            |MonRP                          |VirtualMachines   |
 |AzureMonitor          |Gateway                        |NC                             |WAS|
 |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |WASPUBLIC|
 |BRP                   |HintingServiceV2               |NRP                            |   |
 |CA                    |HRP                            |OboService                     |   |
 |   |   |   |    |

### <a name="additional-considerations"></a>Considerações adicionais

* O comando demora algum tempo para ser executado com base no qual estão a recolher os registos de funções. Fatores também incluem a duração de tempo especificada para a recolha de registos e os números de nós no ambiente do Azure Stack.
* Como iniciar a sessão de execuções de coleção, verifique a nova pasta criada no **OutputSharePath** parâmetro especificado no comando.
* Cada função tem os respetivos registos de dentro de arquivos zip individuais. Dependendo do tamanho dos registos recolhidos, uma função pode ter seus registos divididos em vários arquivos zip. Para uma função, se quiser que todos os arquivos de log descompactei para uma única pasta, utilize uma ferramenta que pode descompactar em massa (por exemplo, 7zip). Selecione todos os arquivos compactados para a função e selecione **extrair aqui**. Isso unzips todos os ficheiros de registo para essa função numa única pasta intercalada.
* Um arquivo chamado **Get-AzureStackLog_Output.log** também é criado na pasta que contém os ficheiros de registo zipados. Este ficheiro é um log de saída do comando, o que pode ser utilizado para resolução de problemas durante a recolha de registos. Por vezes, o ficheiro de registo inclui `PS>TerminatingError` entradas que podem ser ignoradas com segurança, a menos que os ficheiros de registo esperado estão em falta após as execuções de coleção de registo.
* Para investigar uma falha específica, os registos podem ser necessárias de mais de um componente.
    -   Sistema e registos de eventos para todas as VMs de infraestrutura são recolhidos no *VirtualMachines* função.
    -   Sistema e registos de eventos para todos os anfitriões são recolhidos no *bare-metal* função.
    -   Registos de eventos de Cluster de ativação pós-falha e Hyper-V são recolhidos no *armazenamento* função.
    -   Registos de ACS são recolhidos no *armazenamento* e *ACS* funções.

> [!NOTE]
> Limites de tamanho e age são impostos no registos recolhidos conforme é essencial para garantir uma utilização eficiente do seu espaço de armazenamento para garantir que não obtém inundado de registos. No entanto, quando diagnosticar o problema, às vezes, terá de registos que poderão já não existir devido a estes limites. Portanto, é **vivamente recomendado** que descarregar os registos para um espaço de armazenamento externo (uma conta de armazenamento no Azure, adicional no dispositivo de armazenamento local etc.) a cada 8 a 12 horas e mantenha-os lá para 1 a 3 meses, dependendo da sua requisitos. Além disso, certifique-se que esta localização de armazenamento está encriptada.

## <a name="next-steps"></a>Passos Seguintes
[Resolução de problemas do Microsoft Azure Stack](azure-stack-troubleshooting.md)

