---
title: Monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios | Documentos da Microsoft
description: Saiba como utilizar o ponto final com privilégios para monitorizar o estado de atualização para os sistemas integrados do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 9d66227d131a36da420fe5d4e461e738c5ee0911
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777826"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorizar atualizações no Azure Stack, utilizando o ponto final com privilégios

*Aplica-se a: Sistemas integrados do Azure Stack*

Pode utilizar o [ponto final com privilégios](azure-stack-privileged-endpoint.md) execução da atualização para monitorizar o progresso de uma pilha do Azure e retomar uma atualização falhada, execute a partir do último passo com êxito no portal do Azure Stack de tornar indisponível.  Com o portal do Azure Stack é o método recomendado para gerir atualizações no Azure Stack.

Os seguintes novos cmdlets de PowerShell para gestão de atualizações são incluídos na atualização 1710 para sistemas integrados do Azure Stack.

| Cmdlet  | Descrição  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Devolve o estado da atualização atualmente em execução, concluído ou falhado. Fornece o estado de alto nível da operação de atualização e um documento XML que descreve o passo atual e o estado correspondente. |
| `Resume-AzureStackUpdate` | Retoma uma atualização falhada no ponto em que falhou. Em determinados cenários, poderá ter de concluir os passos de mitigação antes de retomar a atualização.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Certifique-se de que os cmdlets estão disponíveis
Como os cmdlets são novos no pacote de atualização de 1710 para o Azure Stack, o processo de atualização 1710 precisa obter até um certo ponto antes da capacidade de monitorização está disponível. Normalmente, os cmdlets estão disponíveis se o estado no portal do administrador indica que a atualização 1710 está no **reiniciar anfitriões de armazenamento** passo. Especificamente, a atualização de cmdlet ocorre durante **passo: Executar passo 2.6 - lista de permissões de atualização PrivilegedEndpoint**.

Também pode determinar se os cmdlets estão disponíveis por meio de programação ao consultar a lista de comandos a partir do ponto final com privilégios. Para tal, execute os seguintes comandos do host de ciclo de vida do hardware ou a partir de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto final privilegiado é um anfitrião fidedigno. Para obter mais informações, consulte o passo 1 de [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS no seu ambiente do Azure Stack (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03). Substitua *prefixo* com a cadeia de prefixo de máquina virtual específica para seu ambiente.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Quando lhe forem pedidas credenciais, utilize o &lt; *domínio do Azure Stack*&gt;\cloudadmin conta ou uma conta que seja um membro do grupo CloudAdmins. Para a conta de CloudAdmin, introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.

2. Obter a lista completa de comandos que estão disponíveis no ponto de extremidade com privilégios. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Determine se o ponto final com privilégios foi atualizado.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Liste os comandos específicos para o módulo de Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Por exemplo:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Utilize os cmdlets de gestão de atualização

> [!NOTE]
> Execute os seguintes comandos do host de ciclo de vida do hardware ou a partir de uma estação de trabalho de acesso privilegiado. Além disso, certifique-se de que o ponto final privilegiado é um anfitrião fidedigno. Para obter mais informações, consulte o passo 1 de [aceder ao ponto final com privilégios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Ligue para o ponto final com privilégios e atribua a variável de sessão

Execute os seguintes comandos para criar uma sessão do PowerShell em qualquer uma das máquinas virtuais ERCS no seu ambiente do Azure Stack (*prefixo*-ERCS01, *prefixo*-ERCS02, ou *prefixo*-ERCS03) e para atribuir uma variável de sessão.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Quando lhe forem pedidas credenciais, utilize o &lt; *domínio do Azure Stack*&gt;\cloudadmin conta ou uma conta que seja um membro do grupo CloudAdmins. Para a conta de CloudAdmin, introduza a mesma palavra-passe que foi fornecido durante a instalação para a conta de administrador de domínio AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Obter o estado de alto nível da execução de atualização atual 

Para obter um Estado de alto nível de execução de atualização atual, execute os seguintes comandos: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Os valores possíveis incluem:

- Em Execução
- Concluído
- Com Falhas 
- Cancelado

Pode executar estes comandos repetidamente para ver o estado mais atualizado. Não tem de voltar a estabelecer uma ligação para verificar novamente.

### <a name="get-the-full-update-run-status-with-details"></a>Obtenha a atualização completa de estado de execução com os detalhes 

Pode obter a atualização completa para executar resumo como uma cadeia de caracteres XML. Pode escrever a cadeia de caracteres num arquivo para exame, ou converta-a num documento XML e utilizar o PowerShell para analisá-lo. O seguinte comando analisa o XML para obter uma lista hierárquica dos passos que está em execução.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

No exemplo seguinte, a etapa de nível superior (atualização de Cloud) tem um plano de subordinados para atualizar e reiniciar os anfitriões de armazenamento. Ela mostra que o plano de reiniciar anfitriões de armazenamento está a atualizar o serviço de armazenamento de BLOBs em um dos anfitriões.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>Retomar uma operação de atualização falhada

Se a atualização falhar, pode retomar a execução de onde parou da atualização.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Resolução de problemas

O ponto final com privilégios está disponível em todas as máquinas de virtuais ERCS no ambiente do Azure Stack. Uma vez que a conexão é feita para um ponto de final de elevada disponibilidade, poderá notar interrupções ocasionais, avisos ou mensagens de erro. Essas mensagens podem indicar que a sessão foi desligada ou que não havia um erro ao comunicar com o serviço de ECE. Este comportamento é esperado. Pode repetir a operação dentro de alguns minutos ou criar uma nova sessão de ponto final com privilégios em uma das outras máquinas de virtuais ERCS. 

## <a name="next-steps"></a>Passos Seguintes

- [Gerir atualizações no Azure Stack](azure-stack-updates.md) 


