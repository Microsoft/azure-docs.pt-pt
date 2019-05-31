---
title: Endpoint protection soluções deteção e o estado de funcionamento avaliação no Centro de segurança do Azure | Documentos da Microsoft
description: Como as soluções de proteção de ponto de extremidade são detetadas e identificadas saudável.
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
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66247968"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Avaliação de proteção de ponto final e recomendações no Centro de segurança do Azure

Avaliação de proteção de ponto final e recomendações no Centro de segurança do Azure Deteta e fornece avaliação de estado de funcionamento da [suportado](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) versões de soluções de proteção de ponto final. Este tópico explica os cenários que geram as seguintes duas recomendações para soluções de proteção de ponto final pelo centro de segurança do Azure.

* **Instalar soluções de proteção de ponto final na sua máquina virtual**
* **Resolver problemas de estado de funcionamento do endpoint protection nos seus computadores**

## <a name="windows-defender"></a>Windows Defender

* O **"Instalar soluções de proteção de ponto de extremidade na máquina virtual"** recomendação é gerada quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) execuções e o resultado é **AMServiceEnabled: FALSO**

* O **"Resolver problemas de estado de funcionamento do endpoint protection nas suas máquinas"** recomendação é gerada quando [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) ocorre de execuções e um ou ambos dos seguintes procedimentos:

  * Pelo menos uma das seguintes propriedades é falso:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Se uma ou ambas das seguintes propriedades for maior ou igual a 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Proteção de ponto final do Microsoft System Center

* O **"Instalar soluções de proteção de ponto de extremidade na máquina virtual"** recomendação é gerada quando importar **SCEPMpModule ("$env: ProgramFiles\Microsoft segurança Client\MpProvider\MpProvider.psd1")** e em execução **Get-MProtComputerStatus** resulta com **AMServiceEnabled = false**

* O **"Resolver problemas de estado de funcionamento do endpoint protection nas suas máquinas"** recomendação é gerada quando **Get-MprotComputerStatus** ocorre de execuções e um ou ambos dos seguintes procedimentos:

    * Pelo menos uma das seguintes propriedades é falso:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Se uma ou ambas as seguintes atualizações de assinatura é maior ou igual a 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* O **"Instalar soluções de proteção de ponto de extremidade na máquina virtual"** recomendação é gerada se um ou mais dos seguintes verificações não forem cumpridas:
    * **Agente de segurança de HKLM:\SOFTWARE\TrendMicro\Deep** existe
    * **HKLM:\SOFTWARE\TrendMicro\Deep segurança Agent\InstallationFolder** existe
    * O **dsq_query.cmd** ficheiro está localizado na pasta de instalação
    * Em execução **dsa_query.cmd** resulta com **Component.AM.mode: no - Trend Micro Deep agente de segurança detetado**

## <a name="symantec-endpoint-protection"></a>Proteção de ponto final da Symantec
O **"Instalar soluções de proteção de ponto de extremidade na máquina virtual"** recomendação é gerada se qualquer um dos seguintes verificações não forem cumpridas:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

O **"Resolver problemas de estado de funcionamento do endpoint protection nas suas máquinas"** recomendação é gerada se qualquer um dos seguintes verificações não forem cumpridas:  

* Check Symantec Version >= 12:  Localização do registo: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Verificar o estado de proteção em Tempo Real: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Verificar o estado de atualização de assinatura: **Ponto final de HKLM\Software\Symantec\Symantec Protection\CurrentVersion\public-opstate\LatestVirusDefsDate < = 7 dias**

* Verifique o estado de análise completa: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 days**

* Localize o número de versão de assinatura caminho para a versão de assinatura para 12 da Symantec: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Caminho para a versão de assinatura para 14 da Symantec: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Caminhos de registo:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee endpoint protection para Windows

O **"Instalar soluções de proteção de ponto de extremidade na máquina virtual"** recomendação é gerada se não forem cumpridas as seguintes verificações:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

O **"Resolver problemas de estado de funcionamento do endpoint protection nas suas máquinas"** recomendação é gerada se não forem cumpridas as seguintes verificações:

* Versão do McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Localize a versão de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Localize a data de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 days**

* Localize a data da análise: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 days**

## <a name="troubleshoot-and-support"></a>Resolução de problemas e suporte

### <a name="troubleshoot"></a>Resolução de problemas

Registos de extensão de Antimalware da Microsoft estão disponíveis em:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Suporte

Se precisar de mais ajuda a qualquer momento neste artigo, pode contactar os especialistas do Azure sobre o [fóruns do Azure do MSDN e Stack Overflow](https://azure.microsoft.com/support/forums/). Em alternativa, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione o suporte de Get. Para informações sobre como utilizar o suporte do Azure, leia os [FAQ do suporte Microsoft Azure](https://azure.microsoft.com/support/faq/).
