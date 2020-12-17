---
title: Como utilizar perfInsights no Microsoft Azure Microsoft Docs
description: Aprende a usar PerfInsights para resolver problemas de desempenho do Windows VM.
services: virtual-machines-windows
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
ms.openlocfilehash: 9e298bf39446024f384b9af142fe3000e936bb6d
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656857"
---
# <a name="how-to-use-perfinsights-in-azure"></a>Como usar PerfInsights em Azure

[PerfInsights](https://aka.ms/perfinsightsdownload) é uma ferramenta de diagnóstico de autoajuda que recolhe e analisa os dados de diagnóstico, e fornece um relatório para ajudar a resolver problemas de desempenho da máquina virtual do Windows em Azure. PerfInsights pode ser executado em máquinas virtuais como uma ferramenta autónoma, diretamente do portal, utilizando diagnósticos de [desempenho para máquinas virtuais Azure](performance-diagnostics.md), ou instalando [extensão VM de diagnóstico de desempenho Azure](performance-diagnostics-vm-extension.md).

Se tiver problemas de desempenho com máquinas virtuais, antes de contactar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de resolução de problemas apoiados

PerfInsights pode recolher e analisar vários tipos de informação. As seguintes secções abrangem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida do desempenho

Este cenário recolhe a configuração do disco e outras informações importantes, incluindo:

-   Registos de eventos

-   Estado da rede para todas as ligações recebidas e de saída

-   Definições de configuração de rede e firewall

-   Lista de tarefas para todas as aplicações que estão atualmente em execução no sistema

-   Definições de configuração da base de dados do Microsoft SQL Server (se o VM for identificado como um servidor que está a executar o SQL Server)

-   Balcões de fiabilidade do armazenamento

-   Hotfixes importantes do Windows

-   Controladores de filtro instalados

Esta é uma recolha passiva de informação que não deve afetar o sistema. 

>[!Note]
>Este cenário é automaticamente incluído em cada um dos seguintes cenários:

### <a name="benchmarking"></a>Benchmarking

Este cenário executa o teste de benchmark [diskspd](https://github.com/Microsoft/diskspd) (IOPS e MBPS) para todas as unidades que estão ligadas ao VM. 

> [!Note]
> Este cenário pode afetar o sistema, e não deve ser executado num sistema de produção ao vivo. Se necessário, executar este cenário numa janela de manutenção dedicada para evitar problemas. Uma carga de trabalho aumentada causada por um teste de rastreio ou benchmark pode afetar negativamente o desempenho do seu VM.
>

### <a name="performance-analysis"></a>Análise de desempenho

Este cenário executa um [contador de desempenho](/windows/win32/perfctrs/performance-counters-portal) utilizando os contadores especificados no RuleEngineConfig.jsem ficheiro. Se o VM for identificado como um servidor que está a executar o SQL Server, é executado um rastreio de contador de desempenho. Fá-lo utilizando os contadores que se encontram no RuleEngineConfig.jsarquivado. Este cenário também inclui dados de diagnóstico de desempenho.

### <a name="azure-files-analysis"></a>Análise de Ficheiros Azure

Este cenário executa uma contra-captura de desempenho especial juntamente com um traço de rede. A captura inclui todos os contadores de partilha de clientes do Bloco de Mensagens do Servidor (SMB). Seguem-se alguns dos principais contadores de desempenho de partilha de clientes SMB que fazem parte da captura:

| **Tipo**     | **Contador de ações do cliente SMB** |
|--------------|-------------------------------|
| IOPS         | Pedidos de Dados/seg             |
|              | Ler Pedidos/seg             |
|              | Escrever Pedidos/seg            |
| Latência      | Avg. sec/Pedido de Dados         |
|              | Avg. sec/Read                 |
|              | Avg. sec/Write                |
| Tamanho IO      | Avg. Bytes/Pedido de Dados       |
|              | Avg. Bytes/Read               |
|              | Avg. Bytes/Write              |
| Débito   | Data Bytes/seg                |
|              | Bytes Lidos/seg                |
|              | Bytes Escritos/seg               |
| Comprimento da Fila | Avg. Ler comprimento da fila        |
|              | Avg. Escreva comprimento da fila       |
|              | Avg. Comprimento da fila de dados        |

### <a name="advanced-performance-analysis"></a>Análise avançada de desempenho

Quando se faz uma análise de desempenho avançada, seleciona-se vestígios para correr em paralelo. Se quiser, pode executá-los todos (Contador de Desempenho, Xperf, Rede e StorPort).  

> [!Note]
> Este cenário pode afetar o sistema, e não deve ser executado num sistema de produção ao vivo. Se necessário, executar este cenário numa janela de manutenção dedicada para evitar problemas. Uma carga de trabalho aumentada causada por um teste de rastreio ou benchmark pode afetar negativamente o desempenho do seu VM.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é recolhida pela PerfInsights?

São recolhidas informações sobre a configuração de conjuntos de discos ou piscinas de armazenamento, contadores de desempenho, registos e vários vestígios. Depende do cenário de desempenho que está a usar. A tabela seguinte fornece detalhes:

| Dados recolhidos | Análise rápida do desempenho | Benchmarking | Análise de desempenho | Análise de Ficheiros Azure | Análise avançada de desempenho |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|
| Informações dos registos de eventos       | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informações do sistema                | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Mapa de volume                        | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Mapa do disco                          | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Tarefas de execução                     | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Balcões de fiabilidade do armazenamento      | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informação de armazenamento               | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Saída Fsutil                     | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Informação do controlador                | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Saída de netstat                    | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Configuração de rede             | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Configuração da firewall            | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Configuração do SQL Server          | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Traços de diagnóstico de desempenho *  | Yes                        | Yes                                | Yes                      | Yes                  | Yes                  |
| Rastreio do contador de desempenho **      |                            |                                    | Yes                      |                      | Yes                  |
| Rastreio de contador SMB **              |                            |                                    |                          | Yes                  |                      |
| Rastreio do contador do SQL Server **       |                            |                                    | Yes                      |                      | Yes                  |
| Vestígio de Xperf                       |                            |                                    |                          |                      | Yes                  |
| Vestígios de StorPort                    |                            |                                    |                          |                      | Yes                  |
| Vestígios de rede                     |                            |                                    |                          | Yes                  | Yes                  |
| Traço de referência de discos **_       |                            | Yes                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-_"></a>Traço de diagnóstico de desempenho (_)

Executa um motor baseado em regras em segundo plano para recolher dados e diagnosticar problemas de desempenho em curso. As seguintes regras são atualmente apoiadas:

- Regra highCpuUsage: Deteta elevados períodos de utilização do CPU e mostra os principais consumidores de utilização de CPU durante esses períodos.
- Regra highDiskUsage: Deteta períodos de utilização de discos elevados em discos físicos e mostra os consumidores de utilização de discos superiores durante esses períodos.
- Regra HighResolutionDiskMetric: Mostra métricas de IOPS, produção e métricas de latência de I/O por 50 milissegundos para cada disco físico. Ajuda a identificar rapidamente os períodos de aceleração do disco.
- Regra highMemoryUsage: Deteta períodos de utilização de memória elevada e mostra os consumidores de uso de memória superior durante esses períodos.

> [!NOTE] 
> Atualmente, as versões do Windows que incluem as versões .NET Framework 4.5 ou posteriores são suportadas.

### <a name="performance-counter-trace-"></a>Rastreio do contador de desempenho (**)

Recolhe os seguintes contadores de desempenho:

- \Processo, \Processador, \Memória, \Thread, \PhysicalDisk e \LogicalDisk
- \Cache\Páginas sujas, \Cache\Preguiçoso Escrever Flushes/seg, \Server\Pool Nonpaged, Fails e \Server\Pool Paged Failures
- Contadores selecionados em \Interface de Rede, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segmentos, \TCPv6\Segmentos, \Adaptador de rede, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \Network QoS Policy\Packets, \Per Processador Interface Card Activity, e \Microsoftock Wins BSP

#### <a name="for-sql-server-instances"></a>Para instâncias do SQL Server
- \SQL Server:Buffer Manager, \SQLServer:Resource Pool Stats e \SQLServer:SQL Statistics\
- \SQLServer:Locks, \SQLServer:General, Estatísticas
- \SQLServer:Métodos de acesso

#### <a name="for-azure-files"></a>Para ficheiros Azure
\SMB Ações do Cliente

### <a name="diskspd-benchmark-trace-_"></a>Traço de referência de discos (**_)
Diskspd Testes de carga de trabalho de I/O (DISCO DE OS [escrita] e unidades de piscina [ler/escrever])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Executar a ferramenta PerfInsights no seu VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que tenho de saber antes de executar a ferramenta? 

#### <a name="tool-requirements"></a>Requisitos de ferramentas

-  Esta ferramenta deve ser executada no VM que tem o problema de desempenho. 

-  Os seguintes sistemas operativos são suportados: _ Windows Server 2019
   * Windows Server 2016
   * Windows Server 2012 R2
   * Windows Server 2012
   * Windows Server 2008 R2
   * Windows 10
   * Windows 8.1
   * Windows 8

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Possíveis problemas quando funciona a ferramenta em VMs de produção

-  Para o cenário de benchmarking ou o cenário de "análise avançada de desempenho" configurado para utilizar Xperf ou Diskspd, a ferramenta pode afetar negativamente o desempenho do VM. Estes cenários não devem ser executados num ambiente de produção ao vivo.

-  Para o cenário de benchmarking ou o cenário de "análise avançada de desempenho" configurado para utilizar Diskspd, certifique-se de que nenhuma outra atividade de fundo interfere com a carga de trabalho de I/S.

-  Por predefinição, a ferramenta utiliza a unidade de armazenamento temporária para recolher dados. Se o rastreio de estadias ativado por um período mais longo, a quantidade de dados que são recolhidos pode ser relevante. Isto pode reduzir a disponibilidade de espaço no disco temporário, podendo, portanto, afetar qualquer aplicação que dependa desta unidade.

### <a name="how-do-i-run-perfinsights"></a>Como dirijo PerfInsights? 

Pode executar PerfInsights numa máquina virtual instalando [extensão VM de diagnóstico de desempenho Azure.](performance-diagnostics-vm-extension.md) Também pode executá-lo como uma ferramenta autónoma. 

**Instale e execute PerfInsights a partir do portal Azure**

Para obter mais informações sobre esta opção, consulte [instalar a extensão VM de diagnóstico de desempenho do Azure.](performance-diagnostics-vm-extension.md#install-the-extension)  

**Executar PerfInsights em modo autónomo**

Para executar a ferramenta PerfInsights, siga estes passos:


1. Descarregue [PerfInsights.zip. ](https://aka.ms/perfinsightsdownload)

2. Desbloqueie o ficheiro PerfInsights.zip. Para isso, clique com PerfInsights.zip e selecione **Propriedades**. No **separador Geral,** selecione **Desbloqueie** e, em seguida, selecione **OK**. Isto garante que a ferramenta funciona sem quaisquer indicações de segurança adicionais.  

    ![Screenshot da PerfInsights Properties, com Desbloqueio em destaque](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Expanda o ficheiro PerfInsights.zip comprimido para a sua unidade temporária (por defeito, esta é normalmente a unidade D). 

4.  Abrir o comando do Windows como administrador e, em seguida, executar PerfInsights.exe para ver os parâmetros da linha de comando disponíveis.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Screenshot da saída da linha de comando PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    A sintaxe básica para executar cenários PerfInsights é:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Pode utilizar o exemplo abaixo para executar o cenário de análise de desempenho durante 5 minutos:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pode utilizar o seguinte exemplo para executar o cenário avançado com os traços do contador Xperf e Performance durante 5 minutos:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Pode utilizar o exemplo abaixo para executar o cenário de análise de desempenho durante 5 minutos e carregar o ficheiro zip de resultados para a conta de armazenamento:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Pode procurar todos os cenários e opções disponíveis utilizando o comando **/lista:**
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Antes de executar um cenário, a PerfInsights solicita ao utilizador que aceite partilhar informações de diagnóstico e concordar com a EULA. Use **/AcceptDisclaimerAndShareDiagnostics** option to skip these prompts. 
    >
    >Se tiver um bilhete de suporte ativo com a Microsoft e executar PerfInsights a pedido do engenheiro de suporte com o qual está a trabalhar, certifique-se de fornecer o número do bilhete de suporte utilizando a opção **/sr.**
    >
    >Por padrão, a PerfInsights tentará atualizar-se para a versão mais recente, se disponível. Utilize **/SkipAutoUpdate** ou **/saus** para saltar a atualização automática.  
    >
    >Se o interruptor de duração **/d** não for especificado, o PerfInsights irá pedir-lhe para reprovar o problema durante a execução de vmslow, azurefiles e cenários avançados. 

Quando os vestígios ou operações estiverem concluídos, um novo ficheiro aparece na mesma pasta que o PerfInsights. O nome do ficheiro é **PerformanceDiagnostics \_ yyyy-MM-dd \_hh-mm-ss-fff.zip.** Pode enviar este ficheiro ao agente de suporte para análise ou abrir o relatório dentro do ficheiro zip para rever as conclusões e recomendações.

## <a name="review-the-diagnostics-report"></a>Reveja o relatório de diagnósticos

Dentro do ficheiro **PerformanceDiagnostics \_ yy-MM-dd \_hh-mm-ss-fff.zip,** pode encontrar um relatório HTML que detalha as conclusões do PerfInsights. Para rever o relatório, expanda o ficheiro **PerformanceDiagnostics \_ yyyy-MM-dd \_hh-mm-ss-fff.zipe,** em seguida, abra o ficheiro **PerfInsights Report.html.**

Selecione o **separador Resultados.**

![Screenshot do separador De visão geral do Relatório PerfInsights. ](media/how-to-use-perfInsights/pi-finding-tab.png)
 ![ Screenshot do separador de armazenamento do Relatório PerfInsights.](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> As descobertas classificadas como altas são questões conhecidas que podem causar problemas de desempenho. As descobertas classificadas como médias representam configurações não ótimas que não causam necessariamente problemas de desempenho. Os resultados classificados como baixos são apenas declarações informativas.

Reveja as recomendações e ligações para todas as conclusões de alto e médio. Saiba como podem afetar o desempenho, e também sobre as melhores práticas para configurações otimizadas pelo desempenho.

### <a name="storage-tab"></a>Separador de armazenamento

A secção **Resultados** apresenta várias conclusões e recomendações relacionadas com o armazenamento.

As secções **do Mapa do Disco** e do Mapa de **Volume** descrevem como os volumes lógicos e os discos físicos estão relacionados entre si.

Na perspetiva do disco físico (Mapa do Disco), a tabela mostra todos os volumes lógicos que estão a ser em execução no disco. No exemplo seguinte, **o PhysicalDrive2** executa dois volumes lógicos criados em múltiplas divisórias (J e H):

![Screenshot do separador de disco](media/how-to-use-perfInsights/pi-disk-tab.png)

Na perspetiva do volume (Mapa de Volume), as tabelas mostram todos os discos físicos em cada volume lógico. Note que para discos RAID/Dynamic, pode executar um volume lógico em vários discos físicos. No exemplo seguinte, *C: \\ montagem* é um ponto de montagem configurado como *SpannedDisk* nos discos físicos 2 e 3:

![Screenshot do separador de volume](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Separador SQL

Se o VM alvo hospedar quaisquer instâncias do SQL Server, você vê um separador adicional no relatório, chamado **SQL**:

![Screenshot do separador SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Esta secção contém um separador **'Resultados'** e separadores adicionais para cada uma das instâncias do SQL Server hospedadas no VM.

O separador **Resultados** contém uma lista de todos os problemas de desempenho relacionados com o SQL encontrados, juntamente com as recomendações.

No exemplo seguinte, é apresentado **PhysicalDrive0** (que executa a unidade C). Isto porque tanto os ficheiros **modeldev** como **modellog** estão localizados na unidade C, e são de diferentes tipos (como ficheiros de dados e registo de transações, respectivamente).

![Screenshot de informações de registo](media/how-to-use-perfInsights/pi-log-info.png)

Os separadores para instâncias específicas do SQL Server contêm uma secção geral que exibe informações básicas sobre a instância selecionada. Os separadores também contêm secções adicionais para informações avançadas, incluindo configurações, configurações e opções do utilizador.

### <a name="diagnostic-tab"></a>Separador de diagnóstico
O separador **De diagnóstico** contém informações sobre os principais consumidores de CPU, disco e memória no computador durante o funcionamento de PerfInsights. Também pode encontrar informações sobre patches críticos que o sistema pode estar em falta, a lista de tarefas e eventos importantes do sistema. 

## <a name="references-to-the-external-tools-used"></a>Referências às ferramentas externas utilizadas

### <a name="diskspd"></a>Diskspd

Diskspd é um gerador de carga de armazenamento e uma ferramenta de teste de desempenho da Microsoft. Para mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf é uma ferramenta de linha de comando para capturar vestígios do Conjunto de Ferramentas de Desempenho do Windows. Para mais informações, consulte [o Windows Performance Toolkit – Xperf](/archive/blogs/ntdebugging/windows-performance-toolkit-xperf).

## <a name="next-steps"></a>Passos seguintes

Pode fazer o upload de registos e relatórios de diagnóstico para o Microsoft Support para posterior revisão. O suporte poderá solicitar que transmita a saída gerada pela PerfInsights para ajudar no processo de resolução de problemas.

A imagem que se segue mostra uma mensagem semelhante à que pode receber:

![Screenshot da mensagem da amostra do Microsoft Support](media/how-to-use-perfInsights/pi-support-email.png)

Siga as instruções na mensagem para aceder ao espaço de trabalho de transferência de ficheiros. Para obter segurança adicional, tem de alterar a sua palavra-passe na primeira utilização.

Depois de iniciar sôs, encontrará uma caixa de diálogo para carregar o ficheiro **performanceDiagnostics \_ yy-MM-d \_hh-mm-ss-fff.zipd** que foi recolhido pela PerfInsights.
