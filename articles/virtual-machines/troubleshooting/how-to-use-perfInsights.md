---
title: Como utilizar o PerfInsights no Microsoft Azure | Documentos da Microsoft
description: Fica a saber como utilizar o PerfInsights para resolver problemas de desempenho da VM do Windows.
services: virtual-machines-windows'
documentationcenter: ''
author: anandhms
manager: cshepard
editor: na
tags: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: cb414abcbbf2db7b7cd6a3d724e50010beeef647
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60318418"
---
# <a name="how-to-use-perfinsights"></a>Como utilizar o PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) é uma ferramenta de diagnóstico de ajuda autónoma que recolhe e analisa os dados de diagnóstico e fornece um relatório para ajudar a resolver problemas de desempenho de máquina virtual do Windows no Azure. PerfInsights podem ser executados em máquinas virtuais como uma ferramenta autônoma, diretamente no portal usando [diagnóstico de desempenho para máquinas virtuais do Azure](performance-diagnostics.md), ou instalando [extensão de VM de diagnóstico de desempenho do Azure ](performance-diagnostics-vm-extension.md).

Se estiver tendo problemas de desempenho com máquinas virtuais, antes de contactar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Resolução de problemas de cenários suportados

PerfInsights pode recolher e analisar vários tipos de informação. As secções seguintes abrangem os cenários comuns.

### <a name="quick-performance-analysis"></a>Análise de desempenho rápido

Este cenário recolhe a configuração do disco e outras informações importantes, incluindo:

-   Registos de eventos

-   Estado da rede para todas as ligações de entrada e saídas

-   Definições de configuração de rede e de firewall

-   Lista de tarefas para todos os aplicativos atualmente em execução no sistema

-   Definições de configuração de base de dados Microsoft SQL Server (se a VM é identificada como um servidor que está a executar o SQL Server)

-   Contadores de confiabilidade do armazenamento

-   Correções importantes do Windows

-   Controladores de filtro instalado

Esta é uma coleção passiva de informações que não devem afetar o sistema. 

>[!Note]
>Este cenário é automaticamente incluído em cada um dos seguintes cenários:

### <a name="benchmarking"></a>Padrão de referência

Este cenário é executado o [Diskspd](https://github.com/Microsoft/diskspd) teste de benchmark (IOPS e MBPS) para todas as unidades que estão ligados à VM. 

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado num sistema de produção em direto. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Maior carga de trabalho que seja causada por um teste de rastreio ou parâmetro de comparação pode afetar negativamente o desempenho da sua VM.
>

### <a name="performance-analysis"></a>Análise de desempenho

Este cenário é executada uma [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) rastreio utilizando os contadores que são especificados no ficheiro RuleEngineConfig.json. Se a VM for identificada como um servidor que está a executar o SQL Server, é executado um rastreio de contador de desempenho. Ele faz isso usando os contadores que se encontram no arquivo RuleEngineConfig.json. Este cenário também inclui dados de diagnóstico de desempenho.

### <a name="azure-files-analysis"></a>Análise de ficheiros do Azure

Este cenário é executada uma captura de contador de desempenho especial, juntamente com um rastreio de rede. A captura inclui todos os serviços de informação Internet (SMB, Server Message Block) cliente partilhas contadores. Seguem-se algumas chaves SMB cliente partilha contadores de desempenho que fazem parte da captura:

| **Tipo**     | **Contador de partilhas de cliente do SMB** |
|--------------|-------------------------------|
| IOPS         | Pedidos de dados/seg             |
|              | Pedidos de leitura por segundo             |
|              | Escrever pedidos/seg            |
| Latência      | Média de pedidos de dados/seg         |
|              | Média de seg/leitura                 |
|              | Média de seg/escrita                |
| Tamanho de e/s      | Média Pedido de bytes/dados       |
|              | Média Bytes/Read               |
|              | Média Bytes/Write              |
| Débito   | Bytes de dados/seg                |
|              | Bytes lidos/seg                |
|              | Bytes escritos/seg               |
| Comprimento da fila | Média Comprimento da fila de leitura        |
|              | Média Comprimento da fila de escrita       |
|              | Média Comprimento da fila de dados        |

### <a name="advanced-performance-analysis"></a>Análise de desempenho avançadas

Quando executar uma análise de desempenho avançadas, selecione rastreios para executar em paralelo. Se quiser, pode executá-los todos os (contador de desempenho, Xperf, rede e StorPort).  

> [!Note]
> Este cenário pode afetar o sistema e não deve ser executado num sistema de produção em direto. Se necessário, execute este cenário numa janela de manutenção dedicada para evitar quaisquer problemas. Maior carga de trabalho que seja causada por um teste de rastreio ou parâmetro de comparação pode afetar negativamente o desempenho da sua VM.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informações é recolhido pelo PerfInsights?

Informações sobre a VM do Windows, discos ou configuração de agrupamentos de armazenamento, contadores de desempenho, registos e vários de coleta de rastreamentos. Depende do cenário de desempenho que está a utilizar. A tabela seguinte fornece detalhes:

|Dados recolhidos                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Análise de desempenho rápido | Padrão de referência | Análise de desempenho | Análise de ficheiros do Azure | Análise de desempenho avançadas |
| Informações dos logs de eventos       | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações do sistema                | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Mapa de volume                        | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Mapa de disco                          | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Tarefas em execução                     | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Contadores de confiabilidade do armazenamento      | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações de armazenamento               | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Saída de fsutil                     | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações do driver de filtro                | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Saída de Netstat                    | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração da rede             | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração da firewall            | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração do SQL Server          | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Rastreios de diagnóstico de desempenho *  | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Rastreio de contador de desempenho * *      |                            |                                    | Sim                      |                      | Sim                  |
| Rastreio de contador do SMB * *              |                            |                                    |                          | Sim                  |                      |
| Rastreio de contador do SQL Server * *       |                            |                                    | Sim                      |                      | Sim                  |
| Rastreio de XPerf                       |                            |                                    |                          |                      | Sim                  |
| Rastreio de StorPort                    |                            |                                    |                          |                      | Sim                  |
| Rastreio de rede                     |                            |                                    |                          | Sim                  | Sim                  |
| Rastreio de benchmark de Diskspd ***       |                            | Sim                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreio de diagnóstico de desempenho (*)

Executa um mecanismo baseado em regras em segundo plano para recolher dados e diagnosticar problemas de desempenho em curso. Atualmente são suportadas as seguintes regras:

- Regra de HighCpuUsage: Deteta os períodos de utilização elevados da CPU e mostra os principais consumidores de utilização da CPU durante esses períodos.
- Regra de HighDiskUsage: Deteta os períodos de utilização de disco elevados em discos físicos e mostra os consumidores de utilização de disco superior durante esses períodos.
- Regra de HighResolutionDiskMetric: Mostra as métricas de latência IOPS, débito e e/s por 50 milissegundos para cada disco físico. Ele ajuda a identificar rapidamente os períodos de limitação do disco.
- Regra de HighMemoryUsage: Deteta os períodos de utilização de memória elevada e mostra os consumidores de utilização de memória superior durante esses períodos.

> [!NOTE] 
> Atualmente, são suportadas versões do Windows que incluem o .NET Framework 4.5 ou versões posteriores.

### <a name="performance-counter-trace-"></a>Rastreio de contador de desempenho (*)

Recolhe os seguintes contadores de desempenho:

- \Process, \Processor, \Memory, \Thread, \PhysicalDisk e \LogicalDisk
- \Cache\Dirty páginas, \Cache\Lazy escrita esvaziamentos da/seg, \Server\Pool não paginável, falhas e falhas de \Server\Pool bloco paginado
- Selecionar contadores em \Network Interface, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network adaptador, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ Policy\Packets de QoS de rede, atividade de cartão de Interface de rede de processador de \Per e \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Para instâncias do SQL Server
- Gestor de servidor: memória intermédia \SQL, estatísticas de conjunto de \SQLServer:Resource e \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Statistics
- Métodos de \SQLServer:Access

#### <a name="for-azure-files"></a>Para os ficheiros do Azure
Partilhas de cliente \SMB

### <a name="diskspd-benchmark-trace-"></a>Rastreio de benchmark de Diskspd (*)
Testes de carga de trabalho de e/s de Diskspd (disco do SO [escrita] e unidades de agrupamento [leitura/escrita])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Execute a ferramenta de PerfInsights na sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que é necessário saber antes de eu executar a ferramenta? 

#### <a name="tool-requirements"></a>Requisitos de ferramenta

-  Esta ferramenta deve ser executada na VM que tenha o problema de desempenho. 

-  São suportados os seguintes sistemas operativos: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Informações sobre problemas possíveis quando a ferramenta é executada em VMs de produção

-  Para o cenário de benchmark ou o cenário de "Análise de desempenho avançadas" que está configurado para utilizar o Xperf ou Diskspd, a ferramenta poderá afetar negativamente o desempenho da VM. Estes cenários não devem ser executados num ambiente de produção em direto.

-  Para o cenário de benchmark ou o cenário de "Análise de desempenho avançadas" que está configurado para utilizar Diskspd, certifique-se de que nenhuma outra atividade de segundo plano interfere com a carga de trabalho de e/s.

-  Por predefinição, a ferramenta utiliza a unidade de armazenamento temporário para recolher dados. Se o rastreio permanece ativada durante mais tempo, a quantidade de dados que são recolhidos pode ser relevante. Isso pode reduzir a disponibilidade de espaço em disco temporário e, portanto, pode afetar a qualquer aplicativo que baseia-se nesta unidade.

### <a name="how-do-i-run-perfinsights"></a>Como posso executar o PerfInsights? 

Pode executar o PerfInsights numa máquina virtual através da instalação [extensão de VM de diagnóstico de desempenho do Azure](performance-diagnostics-vm-extension.md). Também pode executá-lo como uma ferramenta autônoma. 

**Instalar e executar o PerfInsights do portal do Azure**

Para obter mais informações sobre esta opção, consulte [instalar extensão do Azure desempenho diagnósticos de VM](performance-diagnostics-vm-extension.md#install-the-extension).  

**Execute o PerfInsights no modo autónomo**

Para executar a ferramenta de PerfInsights, siga estes passos:


1. Baixe [PerfInsights.zip](https://aka.ms/perfinsightsdownload).

2. Desbloquear o ficheiro de PerfInsights.zip. Para fazer isso, o ficheiro de PerfInsights.zip com o botão direito e selecione **propriedades**. Na **gerais** separador, selecione **desbloqueio**e, em seguida, selecione **OK**. Isto garante que a ferramenta é executada sem nenhuma segurança adicional pede-lhe.  

    ![Captura de ecrã do PerfInsights de propriedades, com o desbloqueio realçado](media/how-to-use-perfInsights/unlock-file.png)

3.  Expandir o ficheiro de PerfInsights.zip comprimido para a unidade temporária (por predefinição, é geralmente a unidade D). 

4.  Abrir linha de comandos do Windows como administrador e, em seguida, execute PerfInsights.exe para ver os parâmetros de linha de comandos disponíveis.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Saída da linha de comandos de captura de ecrã do PerfInsights](media/how-to-use-perfInsights/PerfInsightsCommandline.png)
    
    A sintaxe básica para a execução de cenários de PerfInsights é:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Pode utilizar o exemplo para executar o cenário de análise de desempenho para 5 minutos abaixo:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pode utilizar o exemplo seguinte para executar o cenário avançado com o Xperf e desempenho rastreios de contador para 5 minutos:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pode utilizar o exemplo para executar o cenário de análise de desempenho para 5 minutos e carregar o ficheiro zip de resultado para a conta de armazenamento abaixo:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Pode procurar todos os cenários disponíveis e as opções utilizando o **/lista** comando:
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Antes de executar um cenário, o PerfInsights pede ao utilizador para aceitar para partilhar informações de diagnóstico e aceitar o EULA. Uso **/AcceptDisclaimerAndShareDiagnostics** opção para ignorar esses prompts. 
    >
    >Se tiver um pedido de suporte de Active Directory com a Microsoft e em execução PerfInsights por pedido de estiver a trabalhar com o engenheiro de suporte, certifique-se fornecer o suporte de pedido de suporte número utilizando a **/sr** opção.
    >
    >Por predefinição, o PerfInsights tentará se atualizar para a versão mais recente se estiver disponível. Uso **/SkipAutoUpdate** ou **/sau** parâmetro para ignorar a atualização automática.  
    >
    >Se mudar a duração **/d** não for especificada, PerfInsights irá solicitar-lhe para reprodução o problema ao executar vmslow, azurefiles e cenários avançados. 

Quando os rastreios ou operações forem concluídas, é apresentado um novo ficheiro na mesma pasta que o PerfInsights. O nome do ficheiro é **PerformanceDiagnostics\_aaaa-MM-dd\_hh-mm-ss-fff.zip.** Pode enviar esse arquivo para o agente de suporte para análise ou abrir o relatório dentro do arquivo zip para rever as descobertas e recomendações.

## <a name="review-the-diagnostics-report"></a>Reveja o relatório de diagnóstico

Dentro de **PerformanceDiagnostics\_aaaa-MM-dd\_hh-mm-ss-fff.zip** arquivo, pode encontrar um relatório em HTML que explica em detalhe as descobertas do PerfInsights. Para rever o relatório, expanda o **PerformanceDiagnostics\_aaaa-MM-dd\_hh-mm-ss-fff.zip** de ficheiros e, em seguida, abra a **PerfInsights Report.html** ficheiro.

Selecione o **descobertas** separador.

![Captura de ecrã do relatório de PerfInsights](media/how-to-use-perfInsights/findingtab.png)
![captura de ecrã do relatório de PerfInsights](media/how-to-use-perfInsights/findings.PNG)

> [!NOTE] 
> Descobertas categorizadas como alta são problemas conhecidas que podem causar problemas de desempenho. As descobertas categorizadas como média representam configurações não ideal, que não necessariamente a causar problemas de desempenho. As descobertas categorizadas como baixa são apenas instruções informativas.

Reveja as recomendações e ligações para todas as descobertas de médias e alta. Saiba mais sobre como eles podem afetar o desempenho e também sobre as melhores práticas para configurações com otimização de desempenho.

### <a name="storage-tab"></a>Guia de armazenamento

O **descobertas** secção apresenta várias descobertas e recomendações relacionadas para o armazenamento.

O **mapa de disco** e **mapa de Volume** secções descrevem como lógica físicas de volumes e discos estão relacionados entre si.

O ponto de vista do disco físico (mapa de disco), a tabela mostra todos os volumes de lógicos que estão em execução no disco. No exemplo a seguir **PhysicalDrive2** executa dois volumes lógicos criados em várias partições (J e H):

![Captura de ecrã do separador de disco](media/how-to-use-perfInsights/disktab.png)

O ponto de vista do volume (mapa de Volume), as tabelas de mostram todos os discos físicos em cada volume lógico. Tenha em atenção que para discos RAID/dinâmico, pode executar um volume lógico em vários discos físicos. No exemplo a seguir *c:\\montar* é um ponto de montagem é configurado como *SpannedDisk* em discos físicos, 2 e 3:

![Captura de ecrã do separador de volume](media/how-to-use-perfInsights/volumetab.png)

### <a name="sql-tab"></a>Separador do SQL

Se a VM de destino aloja qualquer instância do SQL Server, verá uma guia adicional no relatório, denominado **SQL**:

![Separador de captura de ecrã de SQL](media/how-to-use-perfInsights/sqltab.png)

Esta secção contém um **descobertas** guia e guias adicionais para cada uma das instâncias do SQL Server alojadas na VM.

O **descobertas** separador contém uma lista de todos os SQL relacionadas com problemas de desempenho encontrados, juntamente com as recomendações.

No exemplo a seguir **PhysicalDrive0** (em execução a unidade C) é apresentado. Isto acontece porque tanto o **modeldev** e **modellog** ficheiros estão localizados na unidade C e são de diferentes tipos (como log de transações e o ficheiro de dados, respectivamente).

![Captura de ecrã de informações de registo](media/how-to-use-perfInsights/loginfo.png)

Os separadores para instâncias específicas do SQL Server contém uma seção geral que apresenta informações básicas sobre a instância selecionada. As guias contêm também as secções adicionais para obter informações avançadas, incluindo as definições, configurações e opções do utilizador.

### <a name="diagnostic-tab"></a>Separador Diagnóstico
O **diagnóstico** guia contém informações sobre principais consumidores de CPU, disco e memória no computador durante a execução do PerfInsights. Também pode encontrar informações sobre correções críticas que o sistema poderá estar em falta, a lista de tarefas e eventos de sistema importante. 

## <a name="references-to-the-external-tools-used"></a>Referências para as ferramentas externas utilizadas

### <a name="diskspd"></a>Diskspd

O Diskspd é um armazenamento load generator e desempenho ferramenta de teste da Microsoft. Para obter mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

XPerf é uma ferramenta de linha de comando para capturar rastreios a partir do Kit de ferramentas de desempenho do Windows. Para obter mais informações, consulte [ferramentas de desempenho do Windows – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Passos Seguintes

Pode carregar os registos de diagnóstico e de relatórios para Support da Microsoft para uma revisão mais aprofundada. Suporte pode pedir que transmite a saída que é gerada pelo PerfInsights para ajudá-lo com o processo de resolução de problemas.

Captura de ecrã seguinte mostra uma mensagem semelhante ao que pode receber:

![Captura de ecrã da mensagem de exemplo do Support da Microsoft](media/how-to-use-perfInsights/supportemail.png)

Siga as instruções na mensagem para acessar a área de trabalho de transferência de ficheiros. Para segurança adicional, terá de alterar a palavra-passe na primeira utilização.

Depois de iniciar sessão, verá uma caixa de diálogo para carregar os **PerformanceDiagnostics\_aaaa-MM-dd\_hh-mm-ss-fff.zip** ficheiro que foi recolhido por PerfInsights.

