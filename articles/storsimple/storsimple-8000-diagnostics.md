---
title: Ferramenta de Diagnóstico para resolver problemas dispositivo StorSimple 8000 Microsoft Docs
description: Descreve os modos do dispositivo StorSimple e explica como utilizar o Windows PowerShell para o StorSimple para alterar o modo de dispositivo.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 48bd909eefbaea15cf6ca2427e106ad9bc0ffbb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298751"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Use a Ferramenta de Diagnóstico StorSimple para resolver problemas com dispositivos da série 8000

## <a name="overview"></a>Descrição geral

A ferramenta StorSimple Diagnostics diagnostica problemas relacionados com a saúde do sistema, desempenho, rede e componente de hardware para um dispositivo StorSimple. A ferramenta de diagnóstico pode ser usada em vários cenários. Estes cenários incluem o planeamento da carga de trabalho, a implementação de um dispositivo StorSimple, a avaliação do ambiente da rede e a determinação do desempenho de um dispositivo operacional. Este artigo fornece uma visão geral da ferramenta de diagnóstico e descreve como a ferramenta pode ser usada com um dispositivo StorSimple.

A ferramenta de diagnóstico destina-se principalmente a dispositivos storSimple 8000 séries no local (8100 e 8600).

## <a name="run-diagnostics-tool"></a>Executar ferramenta de diagnóstico

Esta ferramenta pode ser executada através da interface Windows PowerShell do seu dispositivo StorSimple. Existem duas formas de aceder à interface local do seu dispositivo:

* [Utilize o PuTTY para se ligar à consola em série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Aceda remotamente à ferramenta através do Windows PowerShell para StorSimple](storsimple-8000-remote-connect.md).

Neste artigo, assumimos que ligou à consola em série do dispositivo via PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Para executar a ferramenta de diagnóstico

Depois de ter ligado à interface Do Windows PowerShell do dispositivo, execute os seguintes passos para executar o cmdlet.
1. Inicie o acesso à consola em série do dispositivo seguindo os passos em Utilizar o [PuTTY para se ligar à consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Escreva o seguinte comando:

    `Invoke-HcsDiagnostics`

    Se o parâmetro de alcance não for especificado, o cmdlet executa todos os testes de diagnóstico. Estes testes incluem sistema, saúde de componentes de hardware, rede e desempenho. 
    
    Para executar apenas um teste específico, especifique o parâmetro de âmbito. Por exemplo, para executar apenas o teste de rede, escreva

    `Invoke-HcsDiagnostics -Scope Network`

3. Selecione e copie a saída da janela PuTTY para um ficheiro de texto para análise mais aprofundada.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Cenários para usar a ferramenta de diagnóstico

Utilize a ferramenta de diagnóstico para resolver problemas na rede, desempenho, sistema e saúde de hardware do sistema. Aqui estão alguns cenários possíveis:

* **Dispositivo offline** - O dispositivo da série StorSimple 8000 está offline. No entanto, a partir da interface Do Windows PowerShell, parece que ambos os controladores estão a funcionar.
    * Pode utilizar esta ferramenta para determinar o estado de rede.
         
         > [!NOTE]
         > Não utilize esta ferramenta para avaliar as definições de desempenho e rede num dispositivo antes do registo (ou configurar através do assistente de configuração). Um IP válido é atribuído ao dispositivo durante o assistente de configuração e o registo. Pode executar este cmdlet, num dispositivo que não esteja registado, para saúde e sistema de hardware. Utilize o parâmetro de alcance, por exemplo:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemas persistentes no dispositivo** - Está a ter problemas com dispositivos que parecem persistir. Por exemplo, o registo está a falhar. Também pode estar a ter problemas com o dispositivo depois de o dispositivo estar registado e operacional com sucesso durante algum tempo.
    * Neste caso, utilize esta ferramenta para resolução preliminar de problemas antes de iniciar um pedido de serviço com o Microsoft Support. Recomendamos que execute esta ferramenta e capture a saída desta ferramenta. Em seguida, pode fornecer esta saída para apoiar para acelerar a resolução de problemas.
    * Se houver algum componente de hardware ou falhas de cluster, deverá iniciar sessão num pedido de Suporte.

* **Desempenho baixo do dispositivo** - O seu dispositivo StorSimple é lento.
    * Neste caso, execute este cmdlet com parâmetro de alcance definido para o desempenho. Analise a saída. Obtém-se a nuvem de leitura de llácências. Utilize as latências reportadas como alvo máximo alcançável, fator em alguma sobrecarga para o processamento interno de dados, e, em seguida, implementar as cargas de trabalho no sistema. Para mais informações, vá utilizar [o teste de rede para resolver o desempenho do dispositivo.](#network-test)


## <a name="diagnostics-test-and-sample-outputs"></a>Teste de diagnóstico e saídas de amostras

### <a name="hardware-test"></a>Teste de hardware

Este teste determina o estado dos componentes de hardware, do firmware USM e do firmware de disco em execução no seu sistema.

* Os componentes de hardware relatados são os componentes que falharam no teste ou não estão presentes no sistema.
* As versões de firmware usm e disquete suissão reportadas para o Controlador 0, Controlador 1 e componentes partilhados no seu sistema. Para obter uma lista completa de componentes de hardware, vá a:

    * [Componentes no recinto primário](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componentes no recinto do EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Se os relatórios de teste de hardware falharem, [faça login num pedido](storsimple-8000-contact-microsoft-support.md)de serviço com o Microsoft Support .

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Saída de amostra de teste de hardware executado em um dispositivo 8100

Aqui está uma saída de amostra de um dispositivo StorSimple 8100. No dispositivo modelo 8100, o recinto EBOD não está presente. Por conseguinte, os componentes do controlador EBOD não são reportados.

```
Controller0>Invoke-HcsDiagnostics -Scope Hardware
Running hardware diagnostics ...
--------------------------------------------------
Hardware components failed or not present
----------------------

           Type           State      Controller           Index     EnclosureId
           ----           -----      ----------           -----     -----------
...rVipResource      NotPresent            None               1            None
...rVipResource      NotPresent            None               2            None
...rVipResource      NotPresent            None               3            None
...rVipResource      NotPresent            None               4            None
...rVipResource      NotPresent            None               5            None
...rVipResource      NotPresent            None               6            None
...rVipResource      NotPresent            None               7            None
...rVipResource      NotPresent            None               8            None
...rVipResource      NotPresent            None               9            None
...rVipResource      NotPresent            None              10            None
...rVipResource      NotPresent            None              11            None

Firmware information
----------------------
TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

TalladegaController : ActiveBIOS:0.45.0010
                      BackupBIOS:0.45.0006
                      MainCPLD:17.0.000b
                      ActiveBMCRoot:2.0.001F
                      BackupBMCRoot:2.0.001F
                      BMCBoot:2.0.0002
                      LsiFirmware:20.00.04.00
                      LsiBios:07.37.00.00
                      Battery1Firmware:06.2C
                      Battery2Firmware:06.2C
                      DomFirmware:X231600
                      CanisterFirmware:3.5.0.56
                      CanisterBootloader:5.03
                      CanisterConfigCRC:0x9134777A
                      CanisterVPDStructure:0x06
                      CanisterGEMCPLD:0x19
                      CanisterVPDCRC:0x142F7DC2
                      MidplaneVPDStructure:0x0C
                      MidplaneVPDCRC:0xA6BD4F64
                      MidplaneCPLD:0x10
                      PCM1Firmware:1.00|1.05
                      PCM1VPDStructure:0x05
                      PCM1VPDCRC:0x41BEF99C
                      PCM2Firmware:1.00|1.05
                      PCM2VPDStructure:0x05
                      PCM2VPDCRC:0x41BEF99C

EbodController      :
DisksFirmware       : SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      SmrtStor:TXA2D20400GA6XYR:KZ50
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08
                      WD:WD4001FYYG-01SL3:VR08

--------------------------------------------------
```

### <a name="system-test"></a>Teste do sistema

Este teste informa as informações do sistema, as atualizações disponíveis, as informações do cluster e as informações de serviço para o seu dispositivo.

* As informações do sistema incluem o modelo, o número de série do dispositivo, o fuso horário, o estado do controlador e a versão detalhada do software em execução no sistema. Para compreender os vários parâmetros do sistema relatados como a saída, vá à [informação do sistema de interpretação](#appendix-interpreting-system-information).

* A disponibilidade da atualização informa se os modos regulares e de manutenção estão disponíveis e os seus nomes de pacotes associados. Se `RegularUpdates` `MaintenanceModeUpdates` e `false`forem, isto indica que as atualizações não estão disponíveis. O seu dispositivo está atualizado.
* A informação do cluster contém a informação sobre vários componentes lógicos de todos os grupos de cluster sHC e respetivos estatutos. Se vir um grupo de cluster offline nesta secção do relatório, [contacte](storsimple-8000-contact-microsoft-support.md)o Microsoft Support .
* As informações de serviço incluem os nomes e estados de todos os serviços HCS e CiS em execução no seu dispositivo. Esta informação é útil para o Suporte da Microsoft na resolução de problemas com o problema do dispositivo.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Saída da amostra do teste do sistema executado num dispositivo 8100

Aqui está uma amostra de saída do teste do sistema executado em um dispositivo 8100.

```
Controller0>Invoke-HcsDiagnostics -Scope System
Running system diagnostics ...
--------------------------------------------------

System information
----------------------
Controller0:

InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                : (UTC-08:00) Pacific Time (US & Canada)
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : Disabled
FipsMode                : Enabled

Controller1:
InstanceId              : 7382407f-a56b-4622-8f3f-756fe04cfd38
Name                    : 8100-SHX0991003G467K
Model                   : 8100
SerialNumber            : SHX0991003G467K
TimeZone                :
CurrentController       : Controller0
ActiveController        : Controller0
Controller0Status       : Normal
Controller1Status       : Normal
SystemMode              : Normal
FriendlySoftwareVersion : StorSimple 8000 Series Update 4.0
HcsSoftwareVersion      : 6.3.9600.17820
ApiVersion              : 9.0.0.0
VhdVersion              : 6.3.9600.17759
OSVersion               : 6.3.9600.0
CisAgentVersion         : 1.0.9441.0
MdsAgentVersion         : 35.2.2.0
Lsisas2Version          : 2.0.78.0
Capacity                : 219902325555200
RemoteManagementMode    : HttpsAndHttpEnabled
FipsMode                : Enabled

Update availability
----------------------
RegularUpdates              : False
MaintenanceModeUpdates      : False
RegularUpdatesTitle         : {}
MaintenanceModeUpdatesTitle : {}

Cluster information
----------------------
Name                          State OwnerGroup
----                          ----- ----------
ApplicationHostRLUA           Online HCS Cluster Group
Data0v4                       Online HCS Cluster Group
HCS Vnic Resource             Online HCS Cluster Group
hcs_cloud_connectivity_...    Online HCS Cluster Group
hcs_controller_replacement    Online HCS Cluster Group
hcs_datapath_service          Online HCS Cluster Group
hcs_management_service        Online HCS Cluster Group
hcs_nvram_service             Online HCS Cluster Group
hcs_passive_datapath          Online HCS Passive Cluster Group
hcs_platform_service          Online HCS Cluster Group
hcs_saas_agent_service        Online HCS Cluster Group
HddDataClusterDisk            Online HCS Cluster Group
HddMgmtClusterDisk            Online HCS Cluster Group
HddReplClusterDisk            Online HCS Cluster Group
iSCSI Target Server           Online HCS Cluster Group
NvramClusterDisk              Online HCS Cluster Group
SSAdminRLUA                   Online HCS Cluster Group
SsdDataClusterDisk            Online HCS Cluster Group
SsdNvramClusterDisk           Online HCS Cluster Group

Service information
----------------------
Name                                          Status DisplayName
----                                          ------ -----------
CiSAgentSvc                                   Stopped CiS Service Agent
hcs_cloud_connectivity_...                    Running hcs_cloud_connectivity...
hcs_controller_replacement                    Running HCS Controller Replace...
hcs_datapath_service                          Running HCS Datapath Service
hcs_management_service                        Running HCS Management Service
hcs_minishell                                 Running hcs_minishell
HCS_NVRAM_Service                             Running HCS NVRAM Service
hcs_passive_datapath                          Stopped HCS Passive Datapath S...
hcs_platform_service                          Running HCS Platform Monitor S...
hcs_saas_agent_service                        Running hcs_saas_agent_service
hcs_startup                                   Stopped hcs_startup
--------------------------------------------------
```

### <a name="network-test"></a>Teste de rede

Este teste valida o estado das interfaces de rede, portas, conectividade do servidor DNS e NTP, certificado TLS/SSL, credenciais de conta de armazenamento, conectividade com os servidores Update e conectividade proxy web no seu dispositivo StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Saída da amostra do teste de rede quando apenas O DATA0 está ativado

Aqui está uma amostra de saída do dispositivo 8100. Pode ver na saída que:
* Apenas as interfaces de rede DATA 0 e DATA 1 estão ativadas e configuradas.
* DADOS 2 - 5 não estão ativados no portal.
* A configuração do servidor DNS é válida e o dispositivo pode ligar-se através do servidor DNS.
* A conectividade do servidor NTP também está bem.
* Os portos 80 e 443 estão abertos. No entanto, o porto 9354 está bloqueado. Com base nos requisitos da rede do [sistema,](storsimple-system-requirements.md)é necessário abrir esta porta para a comunicação de autocarros de serviço.
* A certificação TLS/SSL é válida.
* O dispositivo pode ligar-se à conta de armazenamento: _myss8000storageacct_.
* A conectividade com os servidores Update é válida.
* O proxy web não está configurado neste dispositivo.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Saída da amostra do teste de rede quando DATA0 e DATA1 estiverem ativados

```
Controller0>Invoke-HcsDiagnostics -Scope Network
Running network diagnostics ....
--------------------------------------------------
Validating networks .....
Name                Entity              Result              Details
----                ------              ------              -------
Network interface   Data0               Valid
Network interface   Data1               Valid
Network interface   Data2               Not enabled
Network interface   Data3               Not enabled
Network interface   Data4               Not enabled
Network interface   Data5               Not enabled
DNS                 10.222.118.154      Valid
NTP                 time.windows.com    Valid
Port                80                  Open
Port                443                 Open
Port                9354                Blocked
SSL certificate     https://myss8000... Valid
Storage account ... myss8000storageacct Valid
URL                 http://download.... Valid
URL                 http://download.... Valid
Web proxy                               Not enabled         Web proxy is not...
--------------------------------------------------
```

### <a name="performance-test"></a>Teste de desempenho

Este teste relata o desempenho da nuvem através das llátências de leitura de leitura em nuvem para o seu dispositivo. Esta ferramenta pode ser usada para estabelecer uma linha de base do desempenho da nuvem que você pode alcançar com storSimple. A ferramenta relata o desempenho máximo (melhor cenário para as tardios de leitura) que pode obter para a sua ligação.

Como a ferramenta relata o desempenho máximo alcançável, podemos usar as latências de leitura reportadas como alvos na implementação das cargas de trabalho.

O teste simula os tamanhos de bolha associados aos diferentes tipos de volume do dispositivo. Os níveis regulares e as cópias de segurança dos volumes fixados localmente utilizam um tamanho de bolha de 64 KB. Volumes hierárquicos com opção de arquivo verificado utilizam o tamanho de dados de 512 KB blob. Se o seu dispositivo tiver volumes hierárquicos e fixados localmente, apenas o teste correspondente ao tamanho de dados de bolha de 64 KB é executado.

Para utilizar esta ferramenta, execute os seguintes passos:

1.  Em primeiro lugar, crie uma mistura de volumes hierárquicos e volumes hierárquicos com opção arquivada verificada. Esta ação garante que a ferramenta executa os testes para 64 KB e 512 KB tamanhos de bolhas.

2. Executar o cmdlet depois de ter criado e configurado os volumes. Escreva:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Tome nota das llácências de leitura relatadas pela ferramenta. Este teste pode demorar vários minutos a ser executado antes de reportar os resultados.

4. Se as tardios de ligação estiverem todas abaixo do alcance esperado, as tardios comunicadas pela ferramenta podem ser utilizadas como alvo máximo possível na implementação das cargas de trabalho. Fator em alguma sobrecarga para processamento interno de dados.

    Se as llácências de leitura-escrita reportadas pela ferramenta de diagnóstico forem elevadas:

    1. Configure o Storage Analytics para serviços de blob e analise a saída para entender as tardios da conta de armazenamento Azure. Para instruções detalhadas, vá [ativar e configurar](../storage/common/storage-enable-and-view-metrics.md)o Storage Analytics . Se essas tardios também forem altas e comparáveis aos números que recebeu da ferramenta StorSimple Diagnostics, então precisa de registar um pedido de serviço com o armazenamento Azure.

    2. Se as latenciências da conta de armazenamento forem baixas, contacte o administrador da rede para investigar quaisquer problemas de latência na sua rede.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Saída da amostra do teste de desempenho executado num dispositivo 8100

```
Controller0>Invoke-HcsDiagnostics -Scope Performance
Running performance diagnostics...
--------------------------------------------------
Cloud performance: writing blobs
Cloud write latency: 194 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: reading blobs..
Cloud read latency: 544 ms using credential 'myss8000storageacct', blob size '64KB'
Cloud performance: writing blobs.
Cloud write latency: 369 ms using credential 'myss8000storageacct', blob size '512KB'
Cloud performance: reading blobs...
Cloud read latency: 4924 ms using credential 'myss8000storageacct', blob size '512KB'
--------------------------------------------------
Controller0>
```

## <a name="appendix-interpreting-system-information"></a>Apêndice: informação do sistema de interpretação

Aqui está uma tabela que descreve para que os vários parâmetros do Windows PowerShell no mapa de informações do sistema. 

| Parâmetro PowerShell    | Descrição  |
|-------------------------|------------------|
| ID da Instância             | Cada controlador tem um identificador único ou um GUID associado a ele.|
| Nome                    | O nome amigável do dispositivo, tal como configurado através do portal Azure durante a implementação do dispositivo. O nome amigável padrão é o número de série do dispositivo. |
| Modelo                   | O modelo do seu dispositivo da série StorSimple 8000. O modelo pode ser 8100 ou 8600.|
| SerialNumber            | O número de série do dispositivo é atribuído na fábrica e tem 15 caracteres de comprimento. Por exemplo, 8600-SHX0991003G44HT indica:<br> 8600 - É o modelo do dispositivo.<br>SHX – É o local de fabrico.<br> 0991003 - É um produto específico. <br> G44HT - os últimos 5 dígitos são incrementados para criar números de série únicos. Isto pode não ser um conjunto sequencial.|
| TimeZone                | O fuso horário do dispositivo, tal como configurado no portal Azure durante a implementação do dispositivo.|
| Controlador de Corrente       | O controlador a que está ligado através da interface Windows PowerShell do seu dispositivo StorSimple.|
| Controlador Ativo        | O controlador que está ativo no seu dispositivo e está a controlar todas as operações de rede e disco. Isto pode ser controlador 0 ou controlador 1.  |
| Controlador0Status       | O estado do Controlador 0 no seu dispositivo. O estado do controlador pode ser normal, em modo de recuperação, ou inacessível.|
| Controlador1Status       | O estado do Controlador 1 no seu dispositivo.  O estado do controlador pode ser normal, em modo de recuperação, ou inacessível.|
| Modo Sistema              | O estado geral do seu dispositivo StorSimple. O estado do dispositivo pode ser normal, manutenção ou desativado (corresponde a desativado no portal Azure).|
| Versão amigável do software | A corda amigável que corresponde à versão do software do dispositivo. Para um sistema que executa o Update 4, a versão amigável do software seria StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | A versão do software HCS em execução no seu dispositivo. Por exemplo, a versão de software HCS correspondente ao StorSimple 8000 Series Update 4.0 é 6.3.9600.17820. |
| ApiVersion              | A versão de software da API Do Windows PowerShell do dispositivo HCS.|
| VhdVersion              | A versão de software da imagem de fábrica com a que o dispositivo foi enviado. Se redefinir o seu dispositivo para falhas de fábrica, então executa esta versão de software.|
| OSVersion               | A versão de software do sistema operativo Windows Server em execução no dispositivo. O dispositivo StorSimple baseia-se no Windows Server 2012 R2 que corresponde a 6.3.9600.|
| CisAgentVersion         | A versão para o seu agente Cis a funcionar no seu dispositivo StorSimple. Este agente ajuda a comunicar com o serviço StorSimple Manager em funcionamento em Azure.|
| MdsAgentVersion         | A versão correspondente ao agente Mds em execução no seu dispositivo StorSimple. Este agente transfere dados para o Serviço de Monitorização e Diagnóstico (MDS).|
| Lsisas2Version          | A versão correspondente aos controladores LSI no seu dispositivo StorSimple.|
| Capacidade                | A capacidade total do dispositivo em bytes.|
| Modo de Gestão Remota    | Indica se o dispositivo pode ser gerido remotamente através da interface Do Windows PowerShell. |
| FipsMode                | Indica se o modo Padrão de Processamento de Informação Federal (FIPS) dos Estados Unidos está ativado no seu dispositivo. O padrão FIPS 140 define algoritmos criptográficos aprovados para uso por sistemas informáticos do governo federal dos EUA para a proteção de dados sensíveis. Para dispositivos que executam o Update 4 ou posteriormente, o modo FIPS está ativado por defeito. |

## <a name="next-steps"></a>Passos seguintes

* Aprenda a [sintaxe do Invoke-HcsDiagnostics cmdlet](https://technet.microsoft.com/library/mt795371.aspx).

* Saiba mais sobre como [resolver problemas](storsimple-troubleshoot-deployment.md) de implementação no seu dispositivo StorSimple.
