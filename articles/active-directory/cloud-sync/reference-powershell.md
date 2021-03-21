---
title: Módulo AADCloudSyncTools PowerShell para sincronização de nuvem AD Connect Azure
description: Este artigo descreve como instalar o agente de provisão de nuvem Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99593185"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Módulo AADCloudSyncTools PowerShell para sincronização de nuvem AD Connect Azure

O módulo AADCloudSyncTools fornece um conjunto de ferramentas úteis que pode usar para ajudar a gerir as suas implementações Azure AD Connect Cloud Sync.

## <a name="pre-requisites"></a>Pré-requisitos
São necessários os seguintes requisitos:

- Todos os pré-requisitos para este módulo podem ser automaticamente instalados usando `Install-AADCloudSyncToolsPrerequisites`
- Este módulo utiliza a autenticação MSAL, pelo que requer MSAL.PS módulo instalado. Para verificar, numa janela PowerShell, execute `Get-module MSAL.PS -ListAvailable` . Se o módulo estiver instalado corretamente, obterá uma resposta. Pode utilizar `Install-AADCloudSyncToolsPrerequisites` para instalar a versão mais recente do MSAL.PS
- Embora o módulo AzureAD PowerShell não seja um pré-requisito para qualquer funcionalidade deste módulo, é útil ter assim que também seja automaticamente instalado com a `Install-AADCloudSyncToolsPrerequisites` utilização.
- A instalação manual de módulos da PowerShell requer a aplicação do TLS 1.2. Para garantir que pode instalar módulos, desate o seguinte na sessão PowerShell antes de utilizar
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Instale o módulo AADCloudSyncTools PowerShell
Para instalar e utilizar o módulo AADCloudSyncTools utilize os seguintes passos:

1. Open Windows PowerShell com privilégios administrativos
2. Digite ou copie e cole o seguinte: `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Acerte entrar.
4. Para verificar se o módulo foi importado, introduza ou copie e cole o seguinte: `Get-module AADCloudSyncTools`
5. Deve agora ver informações sobre o módulo.
6. Em seguida, para instalar o módulo AADCloudSyncTools pré-requisitos executados: `Install-AADCloudSyncToolsPrerequisites`
7. Na primeira execução, o módulo PoweShellGet será instalado se não estiver presente. Para carregar o novo módulo PowershellGet feche a Janela PowerShell e abra uma nova sessão PowerShell com privilégios administrativos. 
8. Importe o módulo novamente usando o passo 3.
9. Corra `Install-AADCloudSyncToolsPrerequisites` para instalar os módulos MSAL e AzureAD
11. Todos os pré-reqs devem ser instalados com sucesso ![ Módulo de instalação](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>AADCloudSyncTools Cmdlets
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Usa o módulo MSAL.PS para solicitar um token para o administrador AD do Azure aceder ao Microsoft Graph 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exporta e embala todos os dados de resolução de problemas num ficheiro comprimido, da seguinte forma:
 1. Inicia um rastreio verboso com Start-AADCloudSyncToolsVerboseLogs.  Pode encontrar estes registos de rastreio na pasta C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Recolhe um registo de vestígios durante 3 minutos.
   Pode especificar um tempo diferente com -TracingDurationMins ou saltar o rastreio verboso com -SkipVerboseTrace
 3. Para o rastreio verboso com Stop-AADCloudSyncToolsVerboseLogs
 4. Recolhe registos de espectadores de eventos nas últimas 24 horas
 5. Comprime todos os registos de agentes, registos verbosos e registos de visualização de eventos num ficheiro zip comprimido sob a pasta documentos do utilizador. 
 </br>Pode especificar uma pasta de saída diferente com -OutputPath \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Mostra detalhes do inquilino AZure AD e estado de variáveis internas

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Usa o Gráfico para obter os principais do serviço AD2AAD e devolve as informações de Trabalho de Sincronização.
Também pode ser chamado usando o ID de trabalho de sync específico como um parâmetro.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Usa o Gráfico para obter os principais do serviço AD2AAD e devolve a Agenda do Trabalho de Sincronização.
Também pode ser chamado usando o ID de trabalho de sync específico como um parâmetro.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Usa o Gráfico para obter os principais dos serviços AD2AAD e devolve o Esquema do Trabalho de Sincronização.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Utiliza o Gráfico para obter o Esquema do Trabalho de Sincronização para o ID de Trabalho de Sincronização fornecido e produz todos os âmbitos do grupo de filtros.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Utiliza gráfico para obter diretores de serviço AD2AAD e devolve as Definições do Trabalho de Sincronização.
Também pode ser chamado usando o ID de trabalho de sync específico como um parâmetro.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Usa o Gráfico para obter os principais dos serviços AD2AAD e devolve o Estado do Trabalho de Sincronização.
Também pode ser chamado usando o ID de trabalho de sync específico como um parâmetro.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Usa o Gráfico para obter o(s) Principal de Serviço(s) para AD2AAD e/ou SyncFabric.
Sem parâmetros, apenas devolverá o (s) principal do serviço AD2AAD.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Verifica a presença de PowerShellGet v2.2.4.1 ou mais tarde e Azure AD e MSAL.PS módulos e instala-os em caso de falta.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Invoca um pedido web para o URI, Método e Corpo especificados como parâmetros

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Utiliza a Azure AD PowerShell para eliminar a conta corrente (se presente) e reinicia a autenticação da Conta Desincronizada com uma nova conta de sincronização em Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Reinicia uma sincronização completa.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Continua a sincronização da marca de água anterior.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Modifica o 'AADConnectProvisioningAgent.exe.config' para permitir o rastreio verboso e reinicia o serviço AADConnectProvisioningAgent Pode utilizar -SkipServiceRestart para impedir o reinício do serviço, mas quaisquer alterações config não produzirão efeito.  Pode encontrar estes registos de rastreio na pasta C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Modifica o 'AADConnectProvisioningAgent.exe.config' para desativar o rastreio verboso e reinicia o serviço AADConnectProvisioningAgent. Pode utilizar -SkipServiceRestart para evitar o reinício do serviço, mas quaisquer alterações config não produzirão efeito.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Pausa sincronização.

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)

