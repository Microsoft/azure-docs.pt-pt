---
title: Avaliação de integridade e descoberta de soluções do Endpoint Protection na central de segurança do Azure | Microsoft Docs
description: Como as soluções do Endpoint Protection são descobertas e identificadas como íntegras.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/08/2019
ms.author: v-mohabe
ms.openlocfilehash: a5cd0f88173abb65a120aa305206505af51d9f9e
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/10/2019
ms.locfileid: "70861369"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Avaliação e recomendações do Endpoint Protection na central de segurança do Azure

A avaliação e recomendações do Endpoint Protection na central de segurança do Azure detecta e fornece avaliação de integridade de versões [com suporte](https://docs.microsoft.com/azure/security-center/security-center-os-coverage) das soluções do Endpoint Protection. Este tópico explica os cenários que geram as duas recomendações a seguir para soluções do Endpoint Protection pela central de segurança do Azure.

* **Instalar soluções do Endpoint Protection em sua máquina virtual**
* **Resolver problemas de integridade do Endpoint Protection em seus computadores**

## <a name="windows-defender"></a>Windows Defender

* A recomendação **"instalar soluções de proteção de ponto de extremidade na máquina virtual"** é gerada quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) é **executado e o resultado é AMServiceEnabled: For**

* A recomendação **"resolver problemas de integridade do Endpoint Protection em suas máquinas"** é gerada quando o [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) é executado e uma ou ambas as situações a seguir ocorrem:

  * Pelo menos uma das seguintes propriedades é false:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Se uma ou ambas as propriedades a seguir forem maiores ou iguais a 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Proteção de ponto de extremidade do Microsoft System Center

* A recomendação **"instalar soluções de proteção de ponto de extremidade na máquina virtual"** é gerada ao importar **SCEPMpModule ("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** e em execução  **Resultados de Get-MProtComputerStatus** com **AMServiceEnabled = false**

* A recomendação **"resolver problemas de integridade do Endpoint Protection em suas máquinas"** é gerada quando o **Get-MprotComputerStatus** é executado e uma ou ambas as situações a seguir ocorrem:

    * Pelo menos uma das seguintes propriedades é false:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Se uma ou ambas as atualizações de assinatura a seguir forem maiores ou iguais a 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* A recomendação **"instalar soluções de proteção de ponto de extremidade na máquina virtual"** será gerada se uma ou mais das seguintes verificações não forem atendidas:
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent** existe
    * **HKLM: \ SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existe
    * O arquivo **dsq_query. cmd** é encontrado na pasta de instalação
    * Executando os resultados de **dsa_query. cmd** com o **componente. am. Mode: o agente de segurança na Trend Micro Deep Security detectado**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
A recomendação **"instalar soluções de proteção de ponto de extremidade na máquina virtual"** será gerada se qualquer uma das seguintes verificações não for atendida:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

A recomendação **"resolver problemas de integridade do Endpoint Protection em suas máquinas"** será gerada se qualquer uma das seguintes verificações não for atendida:  

* Verifique a versão do Symantec > = 12:  Local do registro: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-value" PRODUCTVERSION "**

* Verificar status da proteção em tempo real: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Verificar status de atualização da assinatura: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 dias**

* Verificar o status da verificação completa: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime < = 7 dias**

* Localize o caminho do número de versão da assinatura para a versão da assinatura do Symantec 12: **Caminhos de registro + "CurrentVersion\SharedDefs"-valor "SRTSP"** 

* Caminho para a versão da assinatura do Symantec 14: **Caminhos de registro + "CurrentVersion\SharedDefs\SDSDefs"-valor "SRTSP"**

Caminhos do registro:

* **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection para Windows

A recomendação **"instalar soluções de proteção de ponto de extremidade na máquina virtual"** será gerada se as seguintes verificações não forem atendidas:

* **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

A recomendação **"resolver problemas de integridade do Endpoint Protection em suas máquinas"** será gerada se as seguintes verificações não forem atendidas:

* Versão da McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Localizar versão da assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Localizar data da assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Localizar data de verificação: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" > = 7 dias**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Prevenção contra ameaças do McAfee Endpoint Security para Linux 

A recomendação **instalar soluções de proteção de ponto de extremidade na máquina virtual** será gerada se uma ou ambas as verificações a seguir não forem atendidas:  

- Saídas do arquivo **/opt/ISEC/ENS/threatprevention/bin/isecav** 

- **a saída "/opt/ISEC/ENS/threatprevention/bin/isecav-Version"** é: **Nome da McAfee = McAfee Endpoint Security para prevenção contra ameaças do Linux e McAfee versão > = 10**

A recomendação **resolver problemas de integridade do Endpoint Protection em suas máquinas** será gerada se uma ou mais das seguintes verificações não forem atendidas:

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** retorna **verificação rápida, verificação completa** e ambas as verificações < = 7 dias

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** retorna o **dat e o tempo de atualização do mecanismo** e ambos < = 7 dias

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** retorna **no status da verificação de acesso**

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivirus para Linux 

A recomendação **instalar soluções de proteção de ponto de extremidade na máquina virtual** será gerada se uma ou ambas as verificações a seguir não forem atendidas:

- O arquivo **/opt/Sophos-AV/bin/savdstatus** sai ou pesquisa o local personalizado **"readlink $ (que SAVScan)"**

- **"/opt/Sophos-AV/bin/savdstatus--Version"** retorna o Sophos name = **Sophos Anti-Virus e o Sophos versão > = 9**

A recomendação **resolver problemas de integridade do Endpoint Protection em suas máquinas** será gerada se uma ou mais das seguintes verificações não forem atendidas:

- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | grep-i "verificação agendada. concluído\* "| cauda-1"** , retorna um valor   

- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | grep "Verificação concluída"** | Tail-1 ", retorna um valor   

- **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** retorna LastUpdate que deve ser < = 7 dias 

- **"/opt/Sophos-AV/bin/savdstatus-v"** é igual a **"a verificação no acesso está em execução"** 

- **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** retorna habilitado  

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Os logs de extensão antimalware da Microsoft estão disponíveis em:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou, você pode arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
