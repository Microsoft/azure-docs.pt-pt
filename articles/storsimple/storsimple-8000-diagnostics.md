---
title: Ferramenta de diagnósticos para resolver problemas de dispositivos do StorSimple 8000 | Documentos da Microsoft
description: Descreve os modos de dispositivo do StorSimple e explica como utilizar o Windows PowerShell para StorSimple para alterar o modo de dispositivo.
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
ms.openlocfilehash: 5cce4337e3ef95c6407d46d9b8b6401fe4f6600b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60576191"
---
# <a name="use-the-storsimple-diagnostics-tool-to-troubleshoot-8000-series-device-issues"></a>Utilizar a ferramenta de diagnóstico do StorSimple para resolver problemas de dispositivos de série 8000

## <a name="overview"></a>Descrição geral

A ferramenta de diagnóstico do StorSimple diagnostica problemas relacionados à integridade de componente de sistema, desempenho, rede e hardware para um dispositivo StorSimple. A ferramenta de diagnóstico pode ser utilizada em vários cenários. Estes cenários incluem a carga de trabalho de planejamento, implantação de um dispositivo StorSimple, avaliar o ambiente de rede e determinar o desempenho de um dispositivo operacional. Este artigo fornece uma descrição geral da ferramenta de diagnóstico e descreve como a ferramenta pode ser utilizada com um dispositivo StorSimple.

A ferramenta de diagnóstico destina-se principalmente a dispositivos do StorSimple 8000 series no local (8100 e 8600).

## <a name="run-diagnostics-tool"></a>Execute a ferramenta de diagnóstico

Essa ferramenta pode ser executada através da interface do Windows PowerShell do dispositivo StorSimple. Existem duas formas de acessar a interface local do seu dispositivo:

* [Utilizar o PuTTY para ligar a consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).
* [Aceder remotamente a ferramenta através do Windows PowerShell para StorSimple](storsimple-8000-remote-connect.md).

Neste artigo, partimos do princípio que tiver ligado à consola de série do dispositivo através do PuTTY.

#### <a name="to-run-the-diagnostics-tool"></a>Para executar a ferramenta de diagnóstico

Depois de ter ligado à interface do Windows PowerShell do dispositivo, execute os seguintes passos para executar o cmdlet.
1. Inicie sessão da consola de série do dispositivo ao seguir os passos em [utilizar o PuTTY para ligar a consola de série do dispositivo](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).

2. Escreva o seguinte comando:

    `Invoke-HcsDiagnostics`

    Se não for especificado o parâmetro de âmbito, o cmdlet é executado todos os testes de diagnóstico. Esses testes incluem o sistema, estado de funcionamento de componente de hardware, rede e desempenho. 
    
    Para executar apenas um teste específico, especifique o parâmetro de âmbito. Por exemplo, para executar apenas o teste de rede, escreva

    `Invoke-HcsDiagnostics -Scope Network`

3. Selecione e copie a saída da janela PuTTY num arquivo de texto para análise adicional.

## <a name="scenarios-to-use-the-diagnostics-tool"></a>Cenários para utilizar a ferramenta de diagnóstico

Utilize a ferramenta diagnósticos para resolver o estado de funcionamento de rede, o desempenho, o sistema e o hardware do sistema. Seguem-se alguns cenários possíveis:

* **Dispositivo offline** -Your StorSimple 8000 series dispositivo estiver offline. No entanto, da interface do Windows PowerShell, parece que ambos os controladores estão em execução.
    * Pode utilizar esta ferramenta para, em seguida, determinar o estado de rede.
         
         > [!NOTE]
         > Não utilize esta ferramenta para avaliar as definições de rede e de desempenho num dispositivo antes do registo (ou a configuração através do Assistente de configuração). Um IP válido é atribuído ao dispositivo durante o Assistente de configuração e registo. Pode executar este cmdlet, num dispositivo que não está registrado para o estado de funcionamento do hardware e sistema. Utilize o parâmetro de âmbito, por exemplo:
         >
         > `Invoke-HcsDiagnostics -Scope Hardware`
         >
         > `Invoke-HcsDiagnostics -Scope System`

* **Problemas de dispositivos persistente** -ocorrerem problemas de dispositivo que parecem manter. Por exemplo, registo está a falhar. Pode também estar com problemas de dispositivos depois do dispositivo está operacional e registado com êxito durante algum tempo.
    * Neste caso, utilize esta ferramenta para resolução de problemas preliminar antes de iniciar um pedido de serviço com Support da Microsoft. Recomendamos que execute essa ferramenta e capturar a saída dessa ferramenta. Em seguida, pode fornecer esta saída ao suporte para agilizar a resolução de problemas.
    * Se houver qualquer falha de componente ou cluster de hardware, deve iniciar sessão um pedido de suporte.

* **Desempenho do dispositivo de baixa** -dispositivo Your StorSimple está lenta.
    * Neste caso, execute este cmdlet com o parâmetro de âmbito definido para o desempenho. Analise a saída. Obter cloud latências de leitura / escrita. Utilize as latências reportadas como destino alcançável máximo, fatorar em alguma sobrecarga para o processamento de dados interno e, em seguida, implementar as cargas de trabalho no sistema. Para obter mais informações, aceda a [usar o teste de rede para resolver problemas de desempenho do dispositivo](#network-test).


## <a name="diagnostics-test-and-sample-outputs"></a>Saídas de exemplo e teste de diagnóstico

### <a name="hardware-test"></a>Teste de hardware

Este teste determina o estado de componentes de hardware, o firmware USM e o firmware do disco em execução no seu sistema.

* Os componentes de hardware reportados são esses componentes que falhou o teste ou não estão presentes no sistema.
* As versões de firmware USM firmware e do disco são relatadas para o controlador 0, 1 do controlador e componentes partilhados no seu sistema. Para obter uma lista completa dos componentes de hardware, aceda a:

    * [Componentes no bastidor principal](storsimple-8000-monitor-hardware-status.md#component-list-for-primary-enclosure-of-storsimple-device)
    * [Componentes do bastidor EBOD](storsimple-8000-monitor-hardware-status.md#component-list-for-ebod-enclosure-of-storsimple-device)

> [!NOTE]
> Se o teste de hardware comunica componentes falhados, [iniciar sessão um pedido de serviço com o Microsoft Support](storsimple-8000-contact-microsoft-support.md).

#### <a name="sample-output-of-hardware-test-run-on-an-8100-device"></a>Saída de exemplo de teste de hardware num dispositivo 8100

Aqui está uma saída de exemplo de um dispositivo StorSimple 8100. No dispositivo de modelo 8100, a inclusão EBOD não está presente. Por conseguinte, o controlador de ebod não é reportados.

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

### <a name="system-test"></a>Teste de sistema

Este teste comunica as informações do sistema, as atualizações disponíveis, as informações de cluster e as informações de serviço para o seu dispositivo.

* As informações do sistema incluem o modelo, número de série do dispositivo, fuso horário, o estado de controlador e a versão de software detalhado em execução no sistema. Para compreender os vários parâmetros de sistema comunicados como a saída, aceda a [interpretar as informações do sistema](#appendix-interpreting-system-information).

* A disponibilidade de atualização de relatórios se os modos normais e de manutenção estão disponíveis e seus nomes de pacote associado. Se `RegularUpdates` e `MaintenanceModeUpdates` são `false`, isto indica que as atualizações não estão disponíveis. O dispositivo está atualizado.
* As informações de cluster contém as informações em vários componentes de lógicas de todos os grupos HCS do cluster e os respetivos Estados respectivos. Se vir um grupo de offline cluster nesta secção do relatório, [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).
* As informações de serviço incluem os nomes e os Estados de todos os serviços HCS e itens de configuração em execução no seu dispositivo. Estas informações são úteis para o Support da Microsoft na resolução do problema de dispositivo.

#### <a name="sample-output-of-system-test-run-on-an-8100-device"></a>Saída de exemplo de teste de sistema num dispositivo 8100

Aqui está uma saída de exemplo do teste de sistema executada num dispositivo 8100.

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

Este teste valida o estado das interfaces de rede, portas, DNS e NTP conetividade do servidor, SSL certificado, as credenciais da conta de armazenamento, conectividade com os servidores de atualização e conectividade de proxy da web no dispositivo StorSimple.

#### <a name="sample-output-of-network-test-when-only-data0-is-enabled"></a>Saída de exemplo da rede de teste quando apenas DATA0 está ativada

Aqui está uma saída de exemplo do dispositivo 8100. Pode ver na saída que:
* Apenas os dados 0 e interfaces de rede de 1 de dados estiverem ativadas e configuradas.
* 2 a 5 de dados não estão ativados no portal.
* A configuração do servidor DNS é válida e o dispositivo consegue ligar através do servidor DNS.
* Também constitui a conetividade do servidor NTP.
* As portas 80 e 443 estão abertas. No entanto, a porta 9354 está bloqueada. Com base na [requisitos de sistema de rede](storsimple-system-requirements.md), precisa abrir essa porta para a comunicação de barramento de serviço.
* A certificação de SSL é válida.
* O dispositivo consegue ligar à conta de armazenamento: _myss8000storageacct_.
* A conectividade aos servidores de atualização é válida.
* O proxy da web não está configurado neste dispositivo.

#### <a name="sample-output-of-network-test-when-data0-and-data1-are-enabled"></a>Saída de exemplo de teste de rede quando DATA0 e dados1 estão ativados

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

Este teste comunica o desempenho na cloud através de latências de leitura / escrita na cloud para o seu dispositivo. Essa ferramenta pode ser usada para estabelecer uma linha de base do desempenho na cloud que pode alcançar com o StorSimple. A ferramenta comunica o desempenho máximo (melhor hipótese de cenário para latências de leitura / escrita) que pode obter para a sua ligação.

Como a ferramenta comunica o máximo desempenho alcançável, podemos usar as latências reportadas de leitura / escrita como destinos ao implementar as cargas de trabalho.

O teste simula os tamanhos de BLOBs associados com os tipos de outro volume no dispositivo. Regular em camadas e cópias de segurança de volumes localmente afixados utilizam um tamanho de blob de 64 KB. Volumes em camadas com a opção de arquivo selecionado utilizam tamanho de dados do blob de 512 KB. Se o seu dispositivo tem volumes em camadas e afixados localmente configurada, apenas o teste correspondente a executar o tamanho dos dados de blob de 64 KB.

Para utilizar esta ferramenta, execute os seguintes passos:

1.  Primeiro, crie uma combinação de volumes em camadas e volumes em camadas com a opção arquivada marcada. Esta ação garante que a ferramenta executa os testes de 64 KB e 512 KB tamanhos de Blobs.

2. Execute o cmdlet depois de ter criado e configurado os volumes. Escreva:

    `Invoke-HcsDiagnostics -Scope Performance`

3. Anote as latências de leitura / escrita comunicado pela ferramenta. Esse teste pode demorar vários minutos a ser executada antes de ele reporta os resultados.

4. Se as latências de ligação são tudo sob o intervalo esperado, em seguida, as latências comunicadas pela ferramenta podem ser utilizadas como destino alcançável máximo ao implementar as cargas de trabalho. Avaliar alguma sobrecarga de processamento de dados interno.

    Se as latências de leitura / escrita comunicadas pela ferramenta de diagnóstico são elevadas:

    1. Configure a análise de armazenamento para os serviços de blob e analisar a saída para compreender as latências para a conta de armazenamento do Azure. Para obter instruções detalhadas, aceda a [ative e configure a análise de armazenamento](../storage/common/storage-enable-and-view-metrics.md). Se as latências também estão alta e comparáveis aos números que recebeu a partir da ferramenta de diagnóstico do StorSimple, terá de iniciar um pedido de serviço com o armazenamento do Azure.

    2. Se as latências de conta de armazenamento são baixas, contacte o administrador de rede para investigar os problemas de latência na sua rede.

#### <a name="sample-output-of-performance-test-run-on-an-8100-device"></a>Saída de exemplo de teste de desempenho num dispositivo 8100

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

## <a name="appendix-interpreting-system-information"></a>Apêndice: interpretar informações do sistema

Aqui está uma tabela que descreve quais os parâmetros Windows PowerShell várias no mapa de informações do sistema para. 

| Parâmetro do PowerShell    | Descrição  |
|-------------------------|------------------|
| ID da Instância             | Cada controlador tem um identificador exclusivo ou um GUID associado ao mesmo.|
| Name                    | O nome amigável do dispositivo, conforme configurado através do portal do Azure durante a implementação do dispositivo. O nome amigável do padrão é o número de série do dispositivo. |
| Modelo                   | O modelo do dispositivo da série StorSimple 8000. O modelo pode ser 8100 ou 8600.|
| serialNumber            | O número de série do dispositivo é atribuído na fábrica e é de 15 carateres de comprimento. Por exemplo, indica 8600 SHX0991003G44HT:<br> 8600 – é o modelo do dispositivo.<br>SHX – é o site de produção.<br> 0991003 - é um produto específico. <br> G44HT-os últimos 5 dígitos são aumentados para criar os números de série exclusivos. Isso pode não ser um conjunto seqüencial.|
| fuso horário                | O fuso horário do dispositivo como configurado no portal do Azure durante a implementação do dispositivo.|
| CurrentController       | O controlador de que está ligado por meio da interface do Windows PowerShell do dispositivo StorSimple.|
| ActiveController        | O controlador que está ativa no seu dispositivo e é o controle todas as operações de rede e disco. Isso pode ser controlador 0 ou 1 do controlador.  |
| Controller0Status       | O estado do controlador 0 no seu dispositivo. O estado de controlador pode ser normal, no modo de recuperação ou inacessível.|
| Controller1Status       | O estado do controlador 1 no seu dispositivo.  O estado de controlador pode ser normal, no modo de recuperação ou inacessível.|
| SystemMode              | O estado geral do dispositivo StorSimple. O estado do dispositivo pode ser normal, manutenção, ou desativados (corresponde à desativado no portal do Azure).|
| FriendlySoftwareVersion | A cadeia de caracteres amigável que corresponde à versão de software do dispositivo. Para um sistema a executar a atualização 4, a versão do software amigável seria StorSimple 8000 Series Update 4.0.|
| HcsSoftwareVersion      | A versão de software HCS em execução no seu dispositivo. Por exemplo, a versão do software HCS correspondente a StorSimple 8000 Series Update 4.0 é 6.3.9600.17820. |
| ApiVersion              | A versão do software da API Windows PowerShell do dispositivo HCS.|
| VhdVersion              | A versão do software da imagem de fábrica que o dispositivo foi lançado com. Se repuser o seu dispositivo para as predefinições de fábrica, em seguida, executar esta versão do software.|
| OSVersion               | A versão do software do sistema operativo Windows Server em execução no dispositivo. O dispositivo StorSimple baseia-se desativar o Windows Server 2012 R2, que corresponde ao 6.3.9600.|
| CisAgentVersion         | A versão para o agente de itens de configuração em execução no dispositivo StorSimple. Esse agente ajuda a comunicar com o serviço StorSimple Manager em execução no Azure.|
| MdsAgentVersion         | A versão correspondente para o agente de Mds em execução no dispositivo StorSimple. Este agente move os dados para a monitorização e diagnóstico. o serviço (MDS).|
| Lsisas2Version          | A versão correspondente para os controladores de LSI no dispositivo StorSimple.|
| Capacidade                | A capacidade total do dispositivo em bytes.|
| RemoteManagementMode    | Indica se o dispositivo pode ser gerido remotamente por meio de sua interface do Windows PowerShell. |
| FipsMode                | Indica se o modo de Estados Unidos Federal Information Processing Standard (FIPS) está ativado no seu dispositivo. A norma FIPS 140 define os algoritmos criptográficos aprovados para utilização pelos sistemas de computador do Governo Federal para a proteção de dados confidenciais. Para dispositivos que executam a atualização 4 ou posterior, o modo FIPS está ativado por predefinição. |

## <a name="next-steps"></a>Passos Seguintes

* Saiba os [sintaxe do cmdlet Invoke-HcsDiagnostics](https://technet.microsoft.com/library/mt795371.aspx).

* Saiba mais sobre como [resolver problemas de implementação](storsimple-troubleshoot-deployment.md) no dispositivo StorSimple.
