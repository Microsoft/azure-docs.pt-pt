---
title: Recomendações de proteção de pontos finais nos Centros de Segurança Azure
description: Como as soluções de proteção de ponto final são descobertas e identificadas como saudáveis.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561233"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Avaliação e recomendações de proteção de pontos finais no Centro de Segurança Azure

O Azure Security Center fornece avaliações de saúde de versões [suportadas](security-center-services.md#endpoint-supported) de soluções de proteção Endpoint. Este artigo explica os cenários que levam o Centro de Segurança a gerar as seguintes duas recomendações:

* **Instale soluções de proteção de pontos finais na sua máquina virtual**
* **Resolva problemas de saúde de proteção de ponto final nas suas máquinas**

## <a name="windows-defender"></a>Windows Defender

* O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando o [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) for executado e o resultado é **AMServiceEnabled: Falso**

* O Security Center recomenda que **"Resolva problemas de saúde de proteção de ponto final nas suas máquinas"** quando o [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) for executado e qualquer um dos seguintes ocorre:

  * Qualquer uma das seguintes propriedades são falsas:

    - **AMServiceEnabled**
    - **Anti-spywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Se uma ou ambas as seguintes propriedades forem 7 ou mais:

    - **Anti-artigoSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Proteção de ponto final do Microsoft System Center

* O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** ao importar **o SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** e executar **resultados get-MProtComputerStatus** em **AMServiceEnabled = falso**.

* O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando o **Get-MprotComputerStatus** for executado e ocorrer qualquer um dos seguintes:

  * Pelo menos uma das seguintes propriedades é falsa:

    - **AMServiceEnabled**
    - **Anti-spywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Se uma ou ambas as seguintes Atualizações de Assinatura forem maiores ou iguais a 7:

    * **Anti-artigoSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Tendência Micro

* O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for cumprida:
    - **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** existe
    - **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existe
    - O **ficheiro dsa_query.cmd** encontra-se na Pasta de Instalação
    - Executar resultados **de dsa_query.cmd** com **component.AM.mode: on - Trend Micro Deep Security Agent detetado**

## <a name="symantec-endpoint-protection"></a>Proteção de ponto final Symantec
O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for cumprida:

- **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

- **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for cumprida:

- Verifique versão Symantec >= 12: Localização do registo: **HKLM:\Software\Symantec\Symantec\Symantec Endpoint Protection\CurrentVersion" -Valor "PRODUCTVERSION"**
- Verifique Real-Time Estado de proteção: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**
- Ver Estado de Atualização de Assinatura: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dias**
- Verifique o estado da verificação completa: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulCanDateTime <= 7 dias**
- Localizar número de versão de assinatura Caminho para versão de assinatura para Symantec 12: **Caminhos de registo+ "CurrentVersion\SharedDefs" -Valor "SRTSP"** 
- Caminho para a versão de assinatura para Symantec 14: **Caminhos de registo+ "CurrentVersion\SharedDefs\SDSDefs" -Valor "SRTSP"**

Caminhos do Registo:
- **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Proteção do ponto final da McAfee para Windows

O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for cumprida:

- **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe
- **HKLM:\SOFTWARE\MCAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\ENABLEoas = 1**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for cumprida:

- Versão McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- Versão assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Valor "dwContentMajorVersion"**
- Localizar data de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Valor "szContentCreationDate" >= 7 dias**
- Localizar a data de digitalização: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Valor "LastFullScanOdsRunTime" >= 7 dias**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>Segurança de ponto final da McAfee para prevenção de ameaças linux 

O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for cumprida:

- Arquivo **/opt/isec/ens/threatprevention/bin/isecav** existe
- **Saída "/opt/isec/ens/threatprevention/bin/isecav --version"** é: **Nome mcAfee = McAfee Endpoint Security para a prevenção de ameaças linux e versão mcAfee >= 10**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for cumprida:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** devolve **a digitalização rápida, a varredura completa** e ambos os exames <= 7 dias
- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** devolve o tempo de **atualização do DAT e** do motor e ambos <= 7 dias
- **"/opt/isec/ens/threatprevention/bin/isecav --getoasconfig --resumo"** **devoluções no** estado do Acesso Scan

## <a name="sophos-antivirus-for-linux"></a>Sophos Antivírus para Linux 

O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for cumprida:
- Saídas de arquivo **/opt/sophos-av/bin/savdstatus** ou procurar por localização personalizada **"readlink $(que savscan)"**
- **"/opt/sophos-av/bin/savdstatus --versão"** devolve nome de Sophos = **Versão Sophos Anti-Vírus e Sophos >= 9**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for cumprida:
- **"/opt/sophos-av/bin/savlog --maxage=7 | grep -i "Scan agendado. \* concluído " | cauda -1",** devolve um valor
- **"/opt/sophos-av/bin/savlog --maxage=7 | grep "scan terminado"** | cauda -1", devolve um valor
- **"/opt/sophos-av/bin/savdstatus --lastupdate"** regressa no passadoupda, que deve ser <= 7 dias 
- **"/opt/sophos-av/bin/savdstatus -v"** é igual a **"A digitalização no acesso está em execução"** 
- **Devoluções "/opt/sophos-av/bin/savconfig get LiveProtection"** ativadas

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os registos de extensão antimalware da Microsoft estão disponíveis em: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (Ou PaaSAntimalware)\1.5.5.x (versão#)\CommandExecution.log**

### <a name="support"></a>Suporte

Para obter mais ajuda, contacte os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou arquivar um incidente de apoio ao Azure. Vá ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o [suporte do Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).