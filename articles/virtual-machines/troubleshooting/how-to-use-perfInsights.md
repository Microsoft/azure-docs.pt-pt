---
title: Como utilizar o PerfInsights no Microsoft Azure. Microsoft Docs
description: Aprende a usar o PerfInsights para resolver problemas de desempenho do Windows VM.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: dcscontentpm
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 783b479dd3e5f429516799d7d3ea82f363cac2ec
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79250130"
---
# <a name="how-to-use-perfinsights"></a>Como utilizar o PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) é uma ferramenta de diagnóstico de autoajuda que recolhe e analisa os dados de diagnóstico, e fornece um relatório para ajudar a resolver problemas de desempenho da máquina virtual do Windows em Azure. PerfInsights pode ser executado em máquinas virtuais como uma ferramenta autónoma, diretamente a partir do portal utilizando diagnósticos de [desempenho para máquinas virtuais Azure,](performance-diagnostics.md)ou instalando a [extensão VM](performance-diagnostics-vm-extension.md)de Diagnóstico de Desempenho Azure .

Se estiver a ter problemas de desempenho com máquinas virtuais, antes de contactar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de resolução de problemas suportados

PerfInsights pode recolher e analisar vários tipos de informação. As seguintes secções abrangem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida do desempenho

Este cenário recolhe a configuração do disco e outras informações importantes, incluindo:

-   Registos de eventos

-   Estado da rede para todas as ligações recebidas e saídas

-   Definições de configuração de rede e firewall

-   Lista de tarefas para todas as aplicações que estão atualmente em execução no sistema

-   Definições de configuração da base de dados do Microsoft SQL Server (se o VM for identificado como um servidor que está a executar o Servidor SQL)

-   Contadores de fiabilidade de armazenamento

-   Hotfixes importantes do Windows

-   Controladores de filtro instalados

Esta é uma recolha passiva de informação que não deve afetar o sistema. 

>[!Note]
>Este cenário é automaticamente incluído em cada um dos seguintes cenários:

### <a name="benchmarking"></a>Benchmarking

Este cenário executa o teste de referência [diskspd](https://github.com/Microsoft/diskspd) (IOPS e MBPS) para todas as unidades que estão ligadas ao VM. 

> [!Note]
> Este cenário pode afetar o sistema, e não deve ser executado num sistema de produção ao vivo. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Um aumento da carga de trabalho causado por um rastreio ou teste de referência pode afetar negativamente o desempenho do seu VM.
>

### <a name="performance-analysis"></a>Análise de desempenho

Este cenário executa um traço de contador de [desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) utilizando os contadores especificados no ficheiro RuleEngineConfig.json. Se o VM for identificado como um servidor que está a executar o SQL Server, é executado um contra-traço de desempenho. Fá-lo utilizando os balcões encontrados no ficheiro RuleEngineConfig.json. Este cenário também inclui dados de diagnóstico de desempenho.

### <a name="azure-files-analysis"></a>Análise de Ficheiros Azure

Este cenário executa uma captura especial de contra-desempenho juntamente com um traço de rede. A captura inclui todos os balcões de partilha do Bloco de Mensagens do Servidor (SMB). Seguem-se alguns contadores de desempenho de partilha de clientes SMB que fazem parte da captura:

| **Tipo**     | **Contador de ações do cliente SMB** |
|--------------|-------------------------------|
| IOPS         | Pedidos de Dados/seg             |
|              | Ler Pedidos/seg             |
|              | Escreva Pedidos/seg            |
| Latência      | Avg. sec/Pedido de Dados         |
|              | Avg. sec/Ler                 |
|              | Avg. sec/Write                |
| Tamanho IO      | Avg. Bytes/Pedido de Dados       |
|              | Avg. Bytes/Ler               |
|              | Avg. Bytes/Write              |
| Débito   | Bytes/sede de dados                |
|              | Bytes Lidos/seg                |
|              | Bytes Escritos/seg               |
| Comprimento da Fila | Avg. Leia o comprimento da fila        |
|              | Avg. Escreva comprimento da fila       |
|              | Avg. Comprimento da fila de dados        |

### <a name="advanced-performance-analysis"></a>Análise avançada de desempenho

Quando executa uma análise avançada de desempenho, selecione vestígios para correr em paralelo. Se quiser, pode executá-los todos (Performance Counter, Xperf, Network e StorPort).  

> [!Note]
> Este cenário pode afetar o sistema, e não deve ser executado num sistema de produção ao vivo. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Um aumento da carga de trabalho causado por um rastreio ou teste de referência pode afetar negativamente o desempenho do seu VM.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é recolhida pela PerfInsights?

São recolhidas informações sobre o Windows VM, disquetes ou configuração de piscinas de armazenamento, contadores de desempenho, registos e vários vestígios. Depende do cenário de desempenho que está a usar. A tabela seguinte fornece detalhes:

|Dados recolhidos                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Análise rápida do desempenho | Benchmarking | Análise de desempenho | Análise de Ficheiros Azure | Análise avançada de desempenho |
| Informações dos registos de eventos       | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informação do sistema                | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Mapa de volume                        | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Mapa do disco                          | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Tarefas de execução                     | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Contadores de fiabilidade de armazenamento      | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações de armazenamento               | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Saída Fsutil                     | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Filtrar informações do condutor                | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Saída de Netstat                    | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração da rede             | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração da firewall            | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração do SQL Server          | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Rastreios de diagnóstico de desempenho *  | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Rastreio do contador de desempenho **      |                            |                                    | Sim                      |                      | Sim                  |
| Traço de contador SMB **              |                            |                                    |                          | Sim                  |                      |
| Rastreio do contador do Servidor SQL **       |                            |                                    | Sim                      |                      | Sim                  |
| Traço xperf                       |                            |                                    |                          |                      | Sim                  |
| Vestígios de StorPort                    |                            |                                    |                          |                      | Sim                  |
| Traço de rede                     |                            |                                    |                          | Sim                  | Sim                  |
| Traço de referência diskspd ***       |                            | Sim                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreio de diagnóstico de desempenho (*)

Executa um motor baseado em regras em segundo plano para recolher dados e diagnosticar problemas de desempenho em curso. Atualmente, são apoiadas as seguintes regras:

- Regra highCpuUsage: Deteta períodos elevados de utilização de CPU e mostra os principais consumidores de utilização de CPU durante esses períodos.
- Regra highDiskUsage: Deteta períodos elevados de utilização do disco em discos físicos e mostra os consumidores de utilização de discos superiores durante esses períodos.
- Regra highResolutionDiskMetric: Mostra iOPS, entrada e métricas de latência Em/O por 50 milissegundos para cada disco físico. Ajuda a identificar rapidamente os períodos de estrangulamento do disco.
- Regra de Utilização de Memória Alta: Deteta períodos elevados de utilização da memória e mostra os consumidores de utilização da memória de topo durante esses períodos.

> [!NOTE] 
> Atualmente, são suportadas versões do Windows que incluem as versões .NET Framework 4.5 ou posteriores.

### <a name="performance-counter-trace-"></a>Traço de contador de desempenho (**)

Recolhe os seguintes contadores de desempenho:

- \Process, \Processador, \Memória, \Thread, \PhysicalDisk e \LogicalDisk
- \Cache\Dirty Pages, \Cache\Lazy Write Flushes/seg, \Server\Pool Nonpaged, Failures, and \Server\Pool Paged Failures
- Contadores selecionados em \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segmentos, \TCPv6\Segmentos, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processor Network Interface Card Activity, e \Microsoft WinsBSP

#### <a name="for-sql-server-instances"></a>Para casos de Servidor SQL
- \SQL Server:Buffer Manager, \SQLServer:Estatísticas da piscina de recursos e \SQLServer:Estatísticas SQL\
- \SQLServer:Fechaduras, \SQLServer:Geral, Estatísticas
- \SQLServer:Métodos de acesso

#### <a name="for-azure-files"></a>Para ficheiros Azure
\SMB Ações de Clientes

### <a name="diskspd-benchmark-trace-"></a>Traço de referência diskspd (***)
Testes de carga de trabalho diskspd I/O (OS Disk [write] e unidades de piscina [ler/escrever])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Execute a ferramenta PerfInsights no seu VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que tenho de saber antes de gerir a ferramenta? 

#### <a name="tool-requirements"></a>Requisitos de ferramentas

-  Esta ferramenta deve ser executada no VM que tem o problema de desempenho. 

-  Os seguintes sistemas operativos são suportados: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Possíveis problemas quando executa a ferramenta em VMs de produção

-  Para o cenário de benchmarking ou o cenário de "Análise avançada de desempenho" configurado para utilizar Xperf ou Diskspd, a ferramenta pode afetar negativamente o desempenho do VM. Estes cenários não devem ser executados num ambiente de produção ao vivo.

-  Para o cenário de benchmarking ou o cenário de "Análise avançada de desempenho" configurado para utilizar o Diskspd, certifique-se de que nenhuma outra atividade de fundo interfere com a carga de trabalho em I/S.

-  Por predefinição, a ferramenta utiliza a unidade de armazenamento temporário para recolher dados. Se o rastreio permanecer ativado por mais tempo, a quantidade de dados recolhidos pode ser relevante. Isto pode reduzir a disponibilidade de espaço no disco temporário, podendo, portanto, afetar qualquer aplicação que dependa desta unidade.

### <a name="how-do-i-run-perfinsights"></a>Como dirijo a PerfInsights? 

Pode executar perfInsights numa máquina virtual instalando a [extensão VM](performance-diagnostics-vm-extension.md)de Diagnóstico de Desempenho do Azure . Também pode executá-lo como uma ferramenta autónoma. 

**Instale e execute perfInsights a partir do portal Azure**

Para mais informações sobre esta opção, consulte [Instalação de Extensão VM](performance-diagnostics-vm-extension.md#install-the-extension)de Diagnóstico de Desempenho Azure .  

**Executar PerfInsights em modo autónomo**

Para executar a ferramenta PerfInsights, siga estes passos:


1. Baixe [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Desbloqueie o ficheiro PerfInsights.zip. Para isso, clique no ficheiro PerfInsights.zip e selecione **Properties**. No separador **Geral,** selecione **Desbloquear**, e, em seguida, selecione **OK**. Isto garante que a ferramenta funciona sem quaisquer solicitações de segurança adicionais.  

    ![Screenshot das Propriedades PerfInsights, com Unblock em destaque](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Expanda o ficheiro PerfInsights.zip comprimido para a sua unidade temporária (por defeito, esta é normalmente a unidade D). 

4.  Abra o pedido de comando windows como administrador e, em seguida, executar PerfInsights.exe para visualizar os parâmetros de linha de comando disponíveis.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Screenshot da saída da linha de comando PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    A sintaxe básica para executar cenários PerfInsights é:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Pode usar o exemplo abaixo para executar o cenário de análise de desempenho durante 5 minutos:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pode utilizar o seguinte exemplo para executar o cenário avançado com traços de contador Xperf e Performance durante 5 minutos:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pode utilizar o exemplo abaixo para executar o cenário de análise de desempenho durante 5 minutos e enviar o ficheiro zip do resultado para a conta de armazenamento:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Pode pesquisar todos os cenários e opções disponíveis utilizando o comando **/lista:**
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Antes de executar um cenário, a PerfInsights leva o utilizador a concordar em partilhar informações de diagnóstico e a concordar com o EULA. Utilize **/AcceptDisclaimerAndShareDiagnostics** para ignorar estas solicitações. 
    >
    >Se tiver um bilhete de suporte ativo com a Microsoft e executar o PerfInsights por pedido do engenheiro de suporte com o qual está a trabalhar, certifique-se de fornecer o número do bilhete de suporte utilizando a opção **/sr.**
    >
    >Por padrão, a PerfInsights tentará atualizar-se para a versão mais recente, se disponível. Utilize **/SkipAutoUpdate** ou **/sau** para não atualizar automaticamente.  
    >
    >Se o interruptor de duração **/d** não for especificado, a PerfInsights irá pedir-lhe para repropro o problema enquanto executa cenários vmslow, azurefiles e avançados. 

Quando os vestígios ou operações estiverem concluídos, um novo ficheiro aparece na mesma pasta que o PerfInsights. O nome do ficheiro é **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip.** Pode enviar este ficheiro ao agente de suporte para análise ou abrir o relatório dentro do ficheiro zip para rever as conclusões e recomendações.

## <a name="review-the-diagnostics-report"></a>Reveja o relatório de diagnóstico

Dentro do ficheiro **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip,** pode encontrar um relatório HTML que detalha as conclusões da PerfInsights. Para rever o relatório, expanda o ficheiro **\_PerformanceDiagnostics\_yyyy-MM-dd hh-mm-ss-fff.zip** e, em seguida, abra o ficheiro **PerfInsights Report.html.**

Selecione o separador **Resultados.**

![Screenshot de PerfInsights Report](media/how-to-use-perfInsights/pi-finding-tab.png)
![Screenshot do Relatório PerfInsights](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> As descobertas classificadas como altas são questões conhecidas que podem causar problemas de desempenho. As descobertas categorizadas como médias representam configurações não ótimas que não causam necessariamente problemas de desempenho. Os resultados classificados como baixos são apenas declarações informativas.

Reveja as recomendações e os links para todas as conclusões altas e médias. Saiba como podem afetar o desempenho, e também sobre as melhores práticas para configurações otimizadas pelo desempenho.

### <a name="storage-tab"></a>Separador de armazenamento

A secção **Resultados** apresenta várias conclusões e recomendações relacionadas com o armazenamento.

As secções do **Mapa do Disco** e do Mapa de **Volume** descrevem como volumes lógicos e discos físicos estão relacionados uns com os outros.

Na perspetiva do disco físico (Mapa do Disco), a tabela mostra todos os volumes lógicos que estão a correr no disco. No exemplo seguinte, **o PhysicalDrive2** executa dois volumes lógicos criados em múltiplas divisórias (J e H):

![Screenshot do separador de disco](media/how-to-use-perfInsights/pi-disk-tab.png)

Na perspetiva do volume (Mapa do Volume), as tabelas mostram todos os discos físicos em cada volume lógico. Note que para discos RAID/Dynamic, poderá executar um volume lógico em vários discos físicos. No exemplo seguinte, *C:\\o suporte* é um ponto de montagem configurado como *SpannedDisk* nos discos físicos 2 e 3:

![Screenshot do separador de volume](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Separador SQL

Se o VM alvo acolher quaisquer instâncias do Servidor SQL, verá um separador adicional no relatório, denominado **SQL:**

![Screenshot do separador SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Esta secção contém um separador **Findings** e separadores adicionais para cada uma das instâncias do Servidor SQL alojadas no VM.

O separador **Resultados** contém uma lista de todos os problemas de desempenho relacionados com o SQL encontrados, juntamente com as recomendações.

No exemplo seguinte, é apresentado **O PhysicalDrive0** (acionamento da unidade C). Isto porque tanto os ficheiros **modelados** como os ficheiros **modelais** estão localizados na unidade C, e são de diferentes tipos (como ficheiros de dados e registo de transações, respectivamente).

![Screenshot de informações de log](media/how-to-use-perfInsights/pi-log-info.png)

Os separadores para instâncias específicas do Servidor SQL contêm uma secção geral que apresenta informações básicas sobre a instância selecionada. Os separadores também contêm secções adicionais para informações avançadas, incluindo configurações, configurações e opções de utilizador.

### <a name="diagnostic-tab"></a>Separador de diagnóstico
O separador **Diagnóstico** contém informações sobre os principais consumidores de CPU, disco e memória no computador durante a duração do funcionamento do PerfInsights. Também pode encontrar informações sobre patches críticos que o sistema pode estar em falta, a lista de tarefas e eventos importantes do sistema. 

## <a name="references-to-the-external-tools-used"></a>Referências às ferramentas externas utilizadas

### <a name="diskspd"></a>Diskspd

Diskspd é um gerador de carga de armazenamento e ferramenta de teste de desempenho da Microsoft. Para mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf é uma ferramenta de linha de comando para capturar vestígios do Windows Performance Toolkit. Para mais informações, consulte [Windows Performance Toolkit – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Passos seguintes

Pode fazer o upload de registos e relatórios de diagnóstico para o Microsoft Support para posterior revisão. O suporte pode solicitar que transmita a saída gerada pela PerfInsights para ajudar no processo de resolução de problemas.

A imagem que se segue mostra uma mensagem semelhante à que poderá receber:

![Screenshot da mensagem da amostra do Suporte da Microsoft](media/how-to-use-perfInsights/pi-support-email.png)

Siga as instruções na mensagem para aceder ao espaço de trabalho de transferência de ficheiros. Para obter segurança adicional, tem de alterar a sua palavra-passe na primeira utilização.

Depois de iniciar sessão, encontrará uma caixa de diálogo para carregar o ficheiro **PerformanceDiagnostics\_yyyy-MM-dd\_hh-mm-ss-fff.zip** que foi recolhido pela PerfInsights.

