---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105138"
---
1. Copie o instalador para uma pasta local (por exemplo, C:\Temp) no servidor que pretende proteger. Execute os seguintes comandos como administrador num prompt de comando:

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. Para instalar o serviço de mobilidade, execute o seguinte comando:

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Agora o agente tem de ser registado com o servidor de configuração.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Argumentos da linha de comandos de instalador do serviço de mobilidade

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parâmetro|Type|Descrição|Valores possíveis|
|-|-|-|-|
|/Role|Obrigatório|Especifica se deve ser instalado o serviço de mobilidade (MS) ou o destino mestre (TA) deve ser instalado.|MS </br> MT|
|/InstallLocation|Opcional|Localização onde o serviço de mobilidade está instalado.|Qualquer pasta no computador|
|/ Plataforma|Obrigatório|Especifica a plataforma em que o serviço de mobilidade está instalado. </br> </br>- **VMware**: Utilize este valor se instalar o serviço de mobilidade numa VM em execução no *anfitriões do VMware vSphere ESXi*, *anfitriões Hyper-V*, e *servidores físicos*. </br> - **Azure**: Utilize este valor se instalar um agente numa VM de IaaS do Azure. | VMware </br> Azure|
|/Silent|Opcional|Especifica a executar o instalador em modo silencioso.| N/A|

>[!TIP]
> Os registos de configuração podem ser encontrados em % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Argumentos da linha de comandos de registo do serviço de mobilidade

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parâmetro|Type|Descrição|Valores possíveis|
  |-|-|-|-|
  |/CSEndPoint |Obrigatório|Endereço IP do servidor de configuração| Qualquer endereço IP válido|
  |/PassphraseFilePath|Obrigatório|Localização da frase de acesso |Qualquer válido UNC ou caminho de ficheiro local|


>[!TIP]
> Os registos de configuração do agente podem ser encontrados em % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
