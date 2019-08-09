---
title: Como usar o PerfInsights no Microsoft Azure | Microsoft Docs
description: Aprende a usar o PerfInsights para solucionar problemas de desempenho de VM do Windows.
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
ms.openlocfilehash: 26301e9a8aef29f1ff786f4fcd28b806eb10b8df
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68846701"
---
# <a name="how-to-use-perfinsights"></a>Como utilizar o PerfInsights

[PerfInsights](https://aka.ms/perfinsightsdownload) é uma ferramenta de diagnóstico de autoajuda que coleta e analisa os dados de diagnóstico e fornece um relatório para ajudar a solucionar problemas de desempenho de máquina virtual do Windows no Azure. O PerfInsights pode ser executado em máquinas virtuais como uma ferramenta autônoma, diretamente do portal, usando o [diagnóstico de desempenho para máquinas virtuais do Azure](performance-diagnostics.md)ou instalando a [extensão de VM de diagnóstico de desempenho do Azure](performance-diagnostics-vm-extension.md).

Se você estiver enfrentando problemas de desempenho com máquinas virtuais, antes de contatar o suporte, execute esta ferramenta.

## <a name="supported-troubleshooting-scenarios"></a>Cenários de solução de problemas com suporte

O PerfInsights pode coletar e analisar vários tipos de informações. As seções a seguir abrangem cenários comuns.

### <a name="quick-performance-analysis"></a>Análise rápida de desempenho

Esse cenário coleta a configuração de disco e outras informações importantes, incluindo:

-   Registos de eventos

-   Status da rede para todas as conexões de entrada e saída

-   Definições de configuração de firewall e rede

-   Lista de tarefas para todos os aplicativos que estão sendo executados no sistema

-   Microsoft SQL Server definições de configuração do banco de dados (se a VM for identificada como um servidor que está executando o SQL Server)

-   Contadores de confiabilidade de armazenamento

-   Hotfixes importantes do Windows

-   Drivers de filtro instalados

Essa é uma coleção passiva de informações que não devem afetar o sistema. 

>[!Note]
>Esse cenário é incluído automaticamente em cada um dos seguintes cenários:

### <a name="benchmarking"></a>Comparação

Esse cenário executa o teste de parâmetro de comparação [Diskspd](https://github.com/Microsoft/diskspd) (IOPS e Mbps) para todas as unidades anexadas à VM. 

> [!Note]
> Esse cenário pode afetar o sistema e não deve ser executado em um sistema de produção ao vivo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um rastreamento ou teste de benchmark pode afetar negativamente o desempenho da VM.
>

### <a name="performance-analysis"></a>Análise de desempenho

Esse cenário executa um rastreamento de [contador de desempenho](https://msdn.microsoft.com/library/windows/desktop/aa373083(v=vs.85).aspx) usando os contadores especificados no arquivo RuleEngineConfig. JSON. Se a VM for identificada como um servidor que está executando SQL Server, um rastreamento de contador de desempenho será executado. Ele faz isso usando os contadores encontrados no arquivo RuleEngineConfig. JSON. Esse cenário também inclui dados de diagnóstico de desempenho.

### <a name="azure-files-analysis"></a>Análise de arquivos do Azure

Esse cenário executa uma captura de contador de desempenho especial junto com um rastreamento de rede. A captura inclui todos os contadores de compartilhamentos de cliente do protocolo SMB. A seguir estão alguns contadores de desempenho principais de compartilhamento de cliente SMB que fazem parte da captura:

| **Tipo**     | **Contador de compartilhamentos de cliente SMB** |
|--------------|-------------------------------|
| IOPS         | Solicitações de dados/s             |
|              | Solicitações de leitura/s             |
|              | Solicitações de gravação/s            |
| Latência      | Média de solicitações s/dados         |
|              | Média de s/leitura                 |
|              | Média de s/gravação                |
| Tamanho de e/s      | Média Bytes/solicitação de dados       |
|              | Média Bytes/Read               |
|              | Média Bytes/gravação              |
| Débito   | Bytes de dados/s                |
|              | Bytes Lidos/seg                |
|              | Bytes Escritos/seg               |
| Comprimento da fila | Média Comprimento da fila de leitura        |
|              | Média Tamanho da fila de gravação       |
|              | Média Comprimento da fila de dados        |

### <a name="advanced-performance-analysis"></a>Análise de desempenho avançada

Ao executar uma análise de desempenho avançada, você seleciona rastreamentos para execução em paralelo. Se desejar, você pode executar todos eles (contador de desempenho, XPerf, rede e StorPort).  

> [!Note]
> Esse cenário pode afetar o sistema e não deve ser executado em um sistema de produção ao vivo. Se necessário, execute este cenário em uma janela de manutenção dedicada para evitar problemas. Uma carga de trabalho maior causada por um rastreamento ou teste de benchmark pode afetar negativamente o desempenho da VM.
>

## <a name="what-kind-of-information-is-collected-by-perfinsights"></a>Que tipo de informação é coletada pelo PerfInsights?

Informações sobre a VM do Windows, a configuração de discos ou pools de armazenamento, contadores de desempenho, logs e vários rastreamentos são coletados. Depende do cenário de desempenho que você está usando. A tabela a seguir fornece detalhes:

|Dados recolhidos                              |  |  | Cenários de desempenho |  |  | |
|----------------------------------|----------------------------|------------------------------------|--------------------------|--------------------------------|----------------------|----------------------|
|                               | Análise rápida de desempenho | Comparação | Análise de desempenho | Análise de arquivos do Azure | Análise de desempenho avançada |
| Informações de logs de eventos       | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações do sistema                | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Mapa de volume                        | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Mapa de disco                          | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Tarefas em execução                     | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Contadores de confiabilidade de armazenamento      | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações de armazenamento               | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Fsutil output                     | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Informações do driver de filtro                | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Saída do netstat                    | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração de rede             | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração do firewall            | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Configuração de SQL Server          | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Rastreamentos de diagnóstico de desempenho *  | Sim                        | Sim                                | Sim                      | Sim                  | Sim                  |
| Rastreamento do contador de desempenho * *      |                            |                                    | Sim                      |                      | Sim                  |
| Rastreamento de contador SMB * *              |                            |                                    |                          | Sim                  |                      |
| Rastreamento de contador de SQL Server * *       |                            |                                    | Sim                      |                      | Sim                  |
| Rastreamento de Xperf                       |                            |                                    |                          |                      | Sim                  |
| Rastreamento de StorPort                    |                            |                                    |                          |                      | Sim                  |
| Rastreamento de rede                     |                            |                                    |                          | Sim                  | Sim                  |
| Rastreamento de benchmark Diskspd * * *       |                            | Sim                                |                          |                      |                      |
|       |                            |                         |                                                   |                      |                      |

### <a name="performance-diagnostics-trace-"></a>Rastreamento de diagnóstico de desempenho (*)

Executa um mecanismo baseado em regras em segundo plano para coletar dados e diagnosticar problemas de desempenho contínuos. Atualmente, há suporte para as seguintes regras:

- Regra HighCpuUsage: Detecta períodos altos de uso da CPU e mostra os principais consumidores de uso da CPU durante esses períodos.
- Regra HighDiskUsage: Detecta períodos de alto uso do disco em discos físicos e mostra os principais consumidores de uso do disco durante esses períodos.
- Regra HighResolutionDiskMetric: Mostra IOPS, taxa de transferência e métricas de latência de e/s por 50 milissegundos para cada disco físico. Ele ajuda a identificar rapidamente os períodos de limitação de disco.
- Regra HighMemoryUsage: Detecta períodos de alto uso de memória e mostra os principais consumidores de uso de memória durante esses períodos.

> [!NOTE] 
> Atualmente, as versões do Windows que incluem o .NET Framework 4,5 ou versões posteriores têm suporte.

### <a name="performance-counter-trace-"></a>Rastreamento do contador de desempenho (* *)

Coleta os seguintes contadores de desempenho:

- \Process, \Processor, \Memory, \Contagem, \PhysicalDisk e \LogicalDisk
- Páginas \Cache\Dirty, liberações de gravação \Cache\Lazy/s, \Server\Pool não paginável, falhas e \Server\Pool paginadas de falhas
- Os contadores selecionados na interface \Network, \IPv4\Datagrams, \IPv6\Datagrams, \TCPv4\Segments, \TCPv6\Segments, \Network Adapter, \WFPv4\Packets, \WFPv6\Packets, \UDPv4\Datagrams, \UDPv6\Datagrams, \TCPv4\Connection, \TCPv6\Connection, \ Rede QoS Policy\Packets, atividade de placa de interface de rede do processador \Per e \Microsoft Winsock BSP

#### <a name="for-sql-server-instances"></a>Para instâncias de SQL Server
- Servidor \SQL: Gerenciador de buffer, \SQLServer: estatísticas do pool de recursos e \SQLServer: estatísticas do SQL \
- \SQLServer: bloqueios, \SQLServer: geral, estatísticas
- \SQLServer: métodos de acesso

#### <a name="for-azure-files"></a>Para arquivos do Azure
Compartilhamentos de cliente \SMB

### <a name="diskspd-benchmark-trace-"></a>Rastreamento de parâmetro de comparação Diskspd (* * *)
Testes de carga de trabalho de e/s do Diskspd (disco do so [gravação] e unidades de pool [leitura/gravação])

## <a name="run-the-perfinsights-tool-on-your-vm"></a>Executar a ferramenta PerfInsights em sua VM

### <a name="what-do-i-have-to-know-before-i-run-the-tool"></a>O que preciso saber antes de executar a ferramenta? 

#### <a name="tool-requirements"></a>Requisitos da ferramenta

-  Essa ferramenta deve ser executada na VM que tem o problema de desempenho. 

-  São suportados os seguintes sistemas operativos: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 e Windows Server 2016; Windows 8.1 e Windows 10.

#### <a name="possible-problems-when-you-run-the-tool-on-production-vms"></a>Possíveis problemas ao executar a ferramenta em VMs de produção

-  Para o cenário de benchmark ou o cenário de "análise de desempenho avançado" configurado para usar Xperf ou Diskspd, a ferramenta pode afetar negativamente o desempenho da VM. Esses cenários não devem ser executados em um ambiente de produção ativo.

-  Para o cenário de benchmark ou o cenário de "análise de desempenho avançado" configurado para usar o Diskspd, certifique-se de que nenhuma outra atividade em segundo plano interfira na carga de trabalho de e/s.

-  Por padrão, a ferramenta usa a unidade de armazenamento temporário para coletar dados. Se o rastreamento permanecer habilitado por mais tempo, a quantidade de dados coletados poderá ser relevante. Isso pode reduzir a disponibilidade de espaço no disco temporário e, portanto, pode afetar qualquer aplicativo que dependa dessa unidade.

### <a name="how-do-i-run-perfinsights"></a>Como fazer executar PerfInsights? 

Você pode executar o PerfInsights em uma máquina virtual instalando a [extensão de VM de diagnóstico de desempenho do Azure](performance-diagnostics-vm-extension.md). Você também pode executá-lo como uma ferramenta autônoma. 

**Instalar e executar o PerfInsights no portal do Azure**

Para obter mais informações sobre essa opção, consulte [instalar extensão de VM de diagnóstico de desempenho do Azure](performance-diagnostics-vm-extension.md#install-the-extension).  

**Executar PerfInsights no modo autônomo**

Para executar a ferramenta PerfInsights, siga estas etapas:


1. Baixe [PerfInsights. zip](https://aka.ms/perfinsightsdownload).

2. Desbloqueie o arquivo PerfInsights. zip. Para fazer isso, clique com o botão direito do mouse no arquivo PerfInsights. zip e selecione **Propriedades**. Na guia **geral** , selecione **desbloquear**e, em seguida, selecione **OK**. Isso garante que a ferramenta seja executada sem prompts de segurança adicionais.  

    ![Captura de tela das propriedades PerfInsights, com desbloquear realçado](media/how-to-use-perfInsights/pi-unlock-file.png)

3.  Expanda o arquivo compactado PerfInsights. zip para a unidade temporária (por padrão, geralmente é a unidade D). 

4.  Abra o prompt de comando do Windows como administrador e execute PerfInsights. exe para exibir os parâmetros de linha de comando disponíveis.

    ```
    cd <the path of PerfInsights folder>
    PerfInsights
    ```
    ![Captura de tela da saída de linha de comando PerfInsights](media/how-to-use-perfInsights/pi-commandline.png)
    
    A sintaxe básica para executar cenários PerfInsights é:
    
    ```
    PerfInsights /run <ScenarioName> [AdditionalOptions]
    ```

    Você pode usar o exemplo abaixo para executar o cenário de análise de desempenho por 5 minutos:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Você pode usar o exemplo a seguir para executar o cenário avançado com rastreamentos de contador de desempenho e Xperf por 5 minutos:
    
    ```
    PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics
    ```

    Você pode usar o exemplo abaixo para executar o cenário de análise de desempenho de 5 minutos e carregar o arquivo zip de resultado na conta de armazenamento:
    
    ```
    PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>
    ```

    Você pode pesquisar todos os cenários e opções disponíveis usando o comando **/list** :
    
    ```
    PerfInsights /list
    ```

    >[!Note]
    >Antes de executar um cenário, o PerfInsights solicita que o usuário concorde em compartilhar informações de diagnóstico e concorde com o EULA. Use a opção **/AcceptDisclaimerAndShareDiagnostics** para ignorar esses prompts. 
    >
    >Se você tiver um tíquete de suporte ativo com a Microsoft e executando PerfInsights de acordo com a solicitação do engenheiro de suporte com o qual está trabalhando, certifique-se de fornecer o número do tíquete de suporte usando a opção **/Sr** .
    >
    >Por padrão, o PerfInsights tentará fazer a atualização para a versão mais recente, se disponível. Use o parâmetro **/SkipAutoUpdate** ou **/sau** para ignorar a atualização automática.  
    >
    >Se a opção Duration **/d** não for especificada, o PerfInsights solicitará que você reproduza o problema durante a execução de cenários vmslow, azurefiles e avançado. 

Quando os rastreamentos ou as operações forem concluídos, um novo arquivo aparecerá na mesma pasta que PerfInsights. O nome do arquivo é **PerformanceDiagnostics\_aaaa-mm-dd\_FFF. zip.** Você pode enviar esse arquivo para o agente de suporte para análise ou abrir o relatório dentro do arquivo zip para examinar as conclusões e as recomendações.

## <a name="review-the-diagnostics-report"></a>Examinar o relatório de diagnóstico

No arquivo **FFF\_. zip PerformanceDiagnostics aaaa-\_mm-dd** , você pode encontrar um relatório HTML que detalha as conclusões de PerfInsights. Para examinar o relatório, expanda o arquivo **PerformanceDiagnostics\_aaaa-mm\_-DD FFF. zip** e, em seguida, abra o arquivo **PerfInsights Report. html** .

Selecione a guia **conclusões** .

![Captura de tela da](media/how-to-use-perfInsights/pi-finding-tab.png)
captura de tela do relatório![PerfInsights do relatório PerfInsights](media/how-to-use-perfInsights/pi-findings.png)

> [!NOTE] 
> As conclusões categorizadas como altas são problemas conhecidos que podem causar problemas de desempenho. As conclusões categorizadas como média representam configurações não ideais que não necessariamente causam problemas de desempenho. As conclusões categorizadas como baixas são apenas instruções informativas.

Examine as recomendações e os links para todas as descobertas altas e médias. Saiba mais sobre como eles podem afetar o desempenho e também sobre as práticas recomendadas para configurações com otimização de desempenho.

### <a name="storage-tab"></a>Guia armazenamento

A seção **conclusões** exibe várias descobertas e recomendações relacionadas ao armazenamento.

As seções **mapa de disco** e mapa de **volume** descrevem como volumes lógicos e discos físicos estão relacionados entre si.

Na perspectiva do disco físico (mapa do disco), a tabela mostra todos os volumes lógicos que estão em execução no disco. No exemplo a seguir, **PhysicalDrive2** executa dois volumes lógicos criados em várias partições (J e H):

![Captura de tela da guia disco](media/how-to-use-perfInsights/pi-disk-tab.png)

Na perspectiva do volume (mapa de volume), as tabelas mostram todos os discos físicos em cada volume lógico. Observe que para discos RAID/dinâmicos, você pode executar um volume lógico em vários discos físicos. No exemplo a seguir, *C:\\Mount* é um ponto de montagem configurado como *SpannedDisk* nos discos físicos 2 e 3:

![Captura de tela da guia volume](media/how-to-use-perfInsights/pi-volume-tab.png)

### <a name="sql-tab"></a>Guia SQL

Se a VM de destino hospedar quaisquer instâncias de SQL Server, você verá uma guia adicional no relatório, chamada **SQL**:

![Captura de tela da guia SQL](media/how-to-use-perfInsights/pi-sql-tab.png)

Esta seção contém uma guia **conclusões** e guias adicionais para cada uma das instâncias de SQL Server hospedadas na VM.

A guia **conclusões** contém uma lista de todos os problemas de desempenho relacionados ao SQL encontrados, juntamente com as recomendações.

No exemplo a seguir, **PhysicalDrive0** (executando a unidade C) é exibido. Isso ocorre porque os arquivos **modeldev** e **Modellog** estão localizados na unidade C e são de tipos diferentes (como arquivo de dados e log de transações, respectivamente).

![Captura de tela das informações de log](media/how-to-use-perfInsights/pi-log-info.png)

As guias para instâncias específicas do SQL Server contêm uma seção geral que exibe informações básicas sobre a instância selecionada. As guias também contêm seções adicionais para informações avançadas, incluindo configurações, configurações e opções de usuário.

### <a name="diagnostic-tab"></a>Guia diagnóstico
A guia **diagnóstico** contém informações sobre os principais consumidores de CPU, disco e memória no computador durante o tempo de execução de PerfInsights. Você também pode encontrar informações sobre os patches críticos que o sistema pode estar faltando, a lista de tarefas e os eventos de sistema importantes. 

## <a name="references-to-the-external-tools-used"></a>Referências às ferramentas externas usadas

### <a name="diskspd"></a>Diskspd

Diskspd é um gerador de carga de armazenamento e ferramenta de teste de desempenho da Microsoft. Para obter mais informações, consulte [Diskspd](https://github.com/Microsoft/diskspd).

### <a name="xperf"></a>Xperf

Xperf é uma ferramenta de linha de comando para capturar rastreamentos do kit de ferramentas de desempenho do Windows. Para obter mais informações, consulte [Kit de ferramentas de desempenho do Windows – Xperf](https://blogs.msdn.microsoft.com/ntdebugging/2008/04/03/windows-performance-toolkit-xperf/).

## <a name="next-steps"></a>Passos Seguintes

Você pode carregar logs de diagnóstico e relatórios para Suporte da Microsoft para análise adicional. O suporte pode solicitar que você transmita a saída gerada pelo PerfInsights para auxiliar no processo de solução de problemas.

A captura de tela a seguir mostra uma mensagem semelhante à que você pode receber:

![Captura de tela da mensagem de exemplo de Suporte da Microsoft](media/how-to-use-perfInsights/pi-support-email.png)

Siga as instruções na mensagem para acessar o espaço de trabalho de transferência de arquivos. Para obter mais segurança, você precisa alterar sua senha no primeiro uso.

Depois de entrar, você encontrará uma caixa de diálogo para carregar o **arquivo\_FFF. zip PerformanceDiagnostics aaaa\_-mm-dd** que foi coletado pelo PerfInsights.

