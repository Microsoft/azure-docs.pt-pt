---
title: Recomendações de proteção de pontofinal nos Centros de Segurança do Azure
description: Como as soluções de proteção do ponto final são descobertas e identificadas como saudáveis.
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
ms.openlocfilehash: dcf7df501665ea3885d00b9f7668a95cbbf02428
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78208547"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Avaliação e recomendações de proteção de pontos finais no Centro de Segurança Azure

O Azure Security Center fornece avaliações de saúde de versões [suportadas](security-center-services.md#endpoint-supported) de soluções de proteção endpoint. Este artigo explica os cenários que levam o Centro de Segurança a gerar as seguintes duas recomendações:

* **Instale soluções de proteção de pontofinal na sua máquina virtual**
* **Resolva problemas de saúde de proteção de pontos finais nas suas máquinas**

## <a name="windows-defender"></a>Windows Defender

* O Security Center recomenda que **"Instale soluções de proteção de pontos finais na máquina virtual"** quando o [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) estiver em funcionação e o resultado for **AMServiceEnabled: False**

* O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando o [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) estiver em funcionação e ocorre qualquer um dos seguintes:

  * Qualquer uma das seguintes propriedades é falsa:

    **AMServiceEnabled**

    **AntispywareEnabled**

    **Proteção real**

    **BehaviorMonitorEnabled**

    **Proteção IoavEnabled**

    **OnAccessProtectionEnabled**

  * Se uma ou ambas as propriedades seguintes forem 7 ou mais.

    **AntispywareSignatureAge**

    **AntivírusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Proteção de ponto final do Microsoft System Center

* O Security Center recomenda que **"instale soluções de proteção de pontos finais na máquina virtual"** ao importar **SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1")** e executar resultados **get-MProtComputerStatus** com **AMServiceEnabled = falso**

* O Security Center recomenda-lhe **"Resolver problemas de saúde de proteção de pontos finais nas suas máquinas"** quando o **Get-MprotComputerStatus** for executado e qualquer um dos seguintes ocorre:

    * Pelo menos uma das seguintes propriedades é falsa:

            **AMServiceEnabled**

            **AntispywareEnabled**
    
            **RealTimeProtectionEnabled**
    
            **BehaviorMonitorEnabled**
    
            **IoavProtectionEnabled**
    
            **OnAccessProtectionEnabled**
          
    * Se uma ou ambas as seguintes Atualizações de Assinatura forem maiores ou iguais a 7. 

            **AntispywareSignatureAge**
    
            **AntivirusSignatureAge**

## <a name="trend-micro"></a>Tendência Micro

* O Security Center recomenda **que "Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for em adoção:
    * **HKLM:\SOFTWARE\TrendMicro\Agente de Segurança Profunda** existe
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** existe
    * O ficheiro **dsa_query.cmd encontra-se** na pasta de instalação
    * Executar **resultados dsa_query.cmd** com **Component.AM.mode: on - Trend Micro Deep Security Agent detetado**

## <a name="symantec-endpoint-protection"></a>Proteção do ponto final symantec
O Security Center recomenda **que "Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for em adoção:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Ou

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for em adotada:

* Verifique a versão Symantec >= 12: Localização do registo: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Verifique o estado da proteção em tempo real: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Verifique o estado da atualização da assinatura: **HKLM\Software\Symantec\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 dias**

* Verifique o estado completo da digitalização: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 dias**

* Encontre o número da versão de assinatura Path para a versão de assinatura para symantec 12: **Caminhos de Registo+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Caminho para a versão de assinatura para Symantec 14: **Caminhos de Registo+ "CurrentVersion\SharedDefs\SDSDefs" -Valor "SRTSP"**

Caminhos do Registo:

* **"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;**
* **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>Proteção de ponto final da McAfee para Windows

O Security Center recomenda **que "Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for em adoção:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** existe

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for em adotada:

* Versão McAfee: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Ver versão de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Encontrar data de assinatura: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 dias**

* Encontrar data de digitalização: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 dias**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>McAfee Endpoint Security para a Prevenção de Ameaças linux 

O Security Center recomenda **que "Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for em adoção:

- File **/opt/isec/ens/threatprevention/bin/isecav** saídas 

- **"/opt/isec/ens/threatprevention/bin/isecav --version"** é: **nome McAfee = McAfee Endpoint Security for Linux Threat Prevention and McAfee version >= 10**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for em adotada:

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** devolve **sondagens rápidas, digitalização completa** e ambos os exames <= 7 dias

- **"/opt/isec/ens/threatprevention/bin/isecav --listtask"** devolve **o DAT e** o tempo de atualização do motor e ambos <= 7 dias

- **"/opt/isec/ens/threatprevention/bin/isecav -getoasconfig --resumo"** devoluções no estado do **acesso**

## <a name="sophos-antivirus-for-linux"></a>Antivírus sophos para Linux 

O Security Center recomenda **que "Instale soluções de proteção de pontos finais na máquina virtual"** quando qualquer uma das seguintes verificações não for em adoção:

- File **/opt/sophos-av/bin/savdstatus** exits ou search for customd location **"readlink $(que savscan)"**

- **"/opt/sophos-av/bin/savdstatus --versão"** devolve o nome Sophos = **Versão Sophos Anti-Vírus e Sophos >= 9**

O Security Center recomenda que **"Resolva problemas de saúde de proteção de pontos finais nas suas máquinas"** quando qualquer uma das seguintes verificações não for em adotada:

- **"/opt/sophos-av/bin/savlog --maxage=7 ] grep -i "Scan agendado . \* concluído" [ cauda -1",** devolve um valor

- **"/opt/sophos-av/bin/savlog --maxage=7 ] grep "scan terminado"** [ cauda -1", devolve um valor

- **"/opt/sophos-av/bin/savdstatus --última atualização"** devoluções no último Update, que deve ser <= 7 dias 

- **"/opt/sophos-av/bin/savdstatus -v"** é igual a **"A digitalização no acesso está em execução"** 

- **Devoluções "/opt/sophos-av/bin/savconfig get LiveProtection"**

## <a name="troubleshoot-and-support"></a>Resolução de problemas e apoio

### <a name="troubleshoot"></a>Resolução de problemas

Os registos de extensão do Microsoft Antimalware estão disponíveis em: **%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x (versão#)\CommandExecution.log**

### <a name="support"></a>Suporte

Para obter mais ajuda, contacte os especialistas do Azure nos [fóruns MSDN Azure e Stack Overflow](https://azure.microsoft.com/support/forums/). Ou arquivar um incidente de apoio azure. Vá ao site de [suporte azure](https://azure.microsoft.com/support/options/) e selecione Obter suporte. Para obter informações sobre a utilização do Suporte Azure, leia o suporte do [Microsoft Azure FAQ](https://azure.microsoft.com/support/faq/).