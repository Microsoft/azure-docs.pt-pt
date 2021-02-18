---
title: Resolver problemas de CPU elevada das máquinas virtuais do Windows no Azure
description: .
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: mnanda
manager: dcscontentpm
editor: v-jesits
tags: azure-resource-manager
ms.assetid: 9971cc26-916f-4e49-83cd-71eca74804f0
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 9/24/2020
ms.author: mnanda
ms.openlocfilehash: 124650f4570608efabba3d8002c14ad06c4782ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571507"
---
# <a name="troubleshoot-high-cpu-issues-for-azure-windows-virtual-machines"></a>Resolver problemas de CPU elevada das máquinas virtuais do Windows no Azure

## <a name="summary"></a>Resumo

Problemas de desempenho ocorrem em diferentes sistemas operativos ou aplicações, e cada problema requer uma abordagem única para a resolução de problemas. A maioria destes problemas gira em torno do CPU, Memória, Networking e entrada/saída (I/O) como locais-chave onde o problema ocorre. Cada uma destas áreas gera sintomas diferentes (às vezes simultaneamente) e requer um diagnóstico e solução diferentes.

Este artigo discute problemas de utilização de alto CPU que ocorrem em Máquinas Virtuais Azure (VMs) que executam o sistema operativo Windows.

### <a name="high-cpu-issues-on-azure-windows-vms"></a>Problemas de alto CPU em VMs do Azure Windows

Além dos problemas de I/O e de latência da rede, a resolução de problemas do CPU e da Memória requer as mesmas ferramentas e passos que os servidores no local. Uma das ferramentas que a Microsoft normalmente suporta é o PerfInsights (disponível tanto para Windows como linux). PerfInsights pode fornecer um diagnóstico de boas práticas Azure VM num relatório amigável. PerfInsights é também uma ferramenta de invólucro que pode ajudar a recolher dados de Perfmon, Xperf e Netmon, dependendo das bandeiras selecionadas dentro da ferramenta.

A maioria das ferramentas existentes de resolução de problemas de desempenho, como Perfmon ou Procmon, que são usadas para servidores no local funcionarão em VMs do Azure Windows. No entanto, perfInsights é explicitamente projetado para VMs Azure para fornecer mais informações, incluindo Azure Best Practices, SQL Best Practices, gráficos de latência de alta resolução, cpu e separadores de memória, e assim por diante.

Quer seja executado como User-Mode ou Kernel-Mode, qualquer fio de um processo ativo requer ciclos de CPU para executar o código a partir do qual é construído. Muitas questões estão diretamente relacionadas com a carga de trabalho. O tipo de carga de trabalho que existe no servidor impulsiona o consumo de recursos, incluindo CPU.

#### <a name="common-factors"></a>Fatores comuns

Os seguintes fatores são comuns numa situação de elevado TEOR:

- Uma recente alteração de código ou implementação que é principalmente aplicável a apps como Serviços de Informação de Internet (IIS), Microsoft SharePoint, Microsoft SQL Server ou aplicações de terceiros.

- Uma atualização recente que pode estar relacionada com uma atualização ao nível do SO, ou com atualizações e correções cumulativas ao nível da aplicação.

- Uma alteração de consulta ou índices desatualizados. As aplicações de nível de dados SQL Server e Oracle também têm uma otimização de plano de consulta como outro fator. Alterações de dados ou falta de índices adequados podem fazer com que várias consultas se tornem mais computacionalmente intensivas.

- Azure VM específico. Existem certos processos como RDAgent, e processos específicos de extensão, tais como Monitoring Agent, MMA agent, ou Cliente de Segurança, que podem causar alto consumo de CPU. Estes processos devem ser vistos a partir de uma perspetiva de configuração ou de questões conhecidas.

## <a name="troubleshoot-the-issue"></a>Resolver problemas

Este artigo centra-se em isolar o processo problemático. Uma análise mais aprofundada será específica para o processo que está a impulsionar o consumo elevado de CPU.

Por exemplo, se o processo for SQL Server (sqlservr.exe), os próximos passos serão analisar qual a consulta que estava a utilizar os ciclos mais CPU num período de tempo específico.

### <a name="scope-the-issue"></a>Âmbito da questão

Aqui ficam algumas perguntas a fazer quando resolver o problema:

- Há um padrão para o problema? Por exemplo, a questão da alta CPU ocorre a uma determinada hora todos os dias, semana ou mês? Em caso afirmativo, pode correlacionar este problema com um trabalho, relatório ou login do utilizador?

- A questão da alta CPU começou depois de uma recente mudança de código? Aplicou uma atualização no Windows ou numa aplicação?

- A questão da alta CPU começou após uma alteração da carga de trabalho, como o aumento do número de utilizadores, um maior afluxo de dados ou um maior número de relatórios?

- Para Azure, a questão da alta CPU começou em alguma das seguintes condições?

  - Após uma recente redistribuição ou reinício
  - Quando um tipo SKU ou VM mudou
  - Quando uma nova extensão foi adicionada
  - Após alterações do balançador de carga foram feitas

### <a name="azure-caveats"></a>Ressalvas de Azure

Compreenda a sua carga de trabalho. Ao selecionar um VM, poderá subestimar as contagens de CPU virtuais (vCPU) quando se olha para o custo geral do alojamento mensal. Se a sua carga de trabalho for computacionalmente intensiva, selecionar um SKU VM menor que tenha um ou dois vCPUs pode causar problemas de carga de trabalho. Teste diferentes configurações para a sua carga de trabalho para determinar a melhor capacidade de computação necessária.

Existem certas séries VM, tais como série B (Modo De Explosão), que são recomendadas para garantia de qualidade (QA) e testes. A utilização destas séries no ambiente de produção limita a capacidade de computação após o esgotamento dos créditos do CPU.

Para aplicações conhecidas como SQL Server, Oracle, RDS (Remote Desktop Services), Windows Virtual Desktop, IIS ou SharePoint, existem artigos de Boas Práticas do Azure que incluem recomendações para configuração mínima para estas cargas de trabalho.

### <a name="ongoing-high-cpu-issues"></a>Questões de alto CPU em curso

Se o problema está a ocorrer neste momento, esta é a melhor oportunidade para capturar os vestígios do processo para determinar o que está a causar o problema. Pode utilizar as ferramentas existentes que tem vindo a utilizar para servidores Windows no local para localizar o processo. As seguintes ferramentas são recomendadas pelo Suporte Azure para VMs Azure.

### <a name="perfinsights"></a>PerfInsights

PerfInsights é a ferramenta recomendada do suporte Azure para problemas de desempenho em VM. É projetado para cobrir as melhores práticas e separadores de análise dedicados para cpu, memória e gráficos de I/O de alta resolução. Pode executá-lo OnDemand através do portal Azure, ou de dentro do VM. Pode partilhar os dados com a equipa de apoio do Azure.

#### <a name="run-perfinsights"></a>Executar PerfInsights

PerfInsights está disponível tanto para o [Windows](./how-to-use-perfinsights.md) como para o [Linux](./how-to-use-perfinsights-linux.md) OS. Para o Windows, aqui estão as opções.

#### <a name="run-and-analyze-reports-through-azure-portal"></a>Executar e analisar relatórios através do portal Azure

Quando é [instalado através do portal Azure,](./performance-diagnostics.md)na verdade instala uma extensão no VM. Os utilizadores também podem instalar PerfInsights como uma extensão, indo diretamente para [Extensões na lâmina VM](./performance-diagnostics-vm-extension.md), e, em seguida, escolher uma opção de diagnóstico de desempenho.

#### <a name="azure-portal-option-1"></a>Portal Azure Opção 1

Navegue pela lâmina VM e selecione a opção **de diagnóstico de desempenho.** Ser-lhe-á pedido que instale a opção (utiliza extensões) no VM para o qual a selecionou.

  ![Instalar diagnósticos de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/1-install-performance-diagnostics.png)

#### <a name="azure-portal-option-2"></a>Opção do portal Azure 2

Navegue para **diagnosticar e resolver problemas** na lâmina VM e procure **por Problemas de Desempenho VM**.

  ![Problemas de desempenho em VM de resolução de problemas](./media/troubleshoot-high-cpu-issues-azure-windows-vm/2-troubleshoot-vm-performance-issues.png)

Se selecionar **Resolução de Problemas,** as cargas do ecrã de instalação PerfInsights.

Se selecionar **instalar,** a instalação fornece diferentes opções de recolha.

  ![Análise de Desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/3-performance-analysis.png)

As opções numeradas na imagem dizem respeito aos seguintes comentários:

1. Para a opção de **High-CPU,** selecione Análise de **Desempenho** ou **Avançado**.

2. Quando adicionar sintomas aqui, eles serão adicionados ao relatório, o que o ajuda a partilhar informações com o Suporte Azure.

3. Selecione a duração da recolha de dados. Para a opção High-CPU, selecione pelo menos 15 minutos ou mais. No modo portal Azure, pode recolher até 15 minutos de dados. Por períodos mais longos de recolha, deve executar o programa como executável dentro do VM.

4. Se lhe for solicitado pelo Azure Support para recolher estes dados, pode adicionar o número do bilhete aqui. Este campo é opcional.

5. Selecione este campo para aceitar o Contrato de Licença de Utilizador Final (EULA).

6. Selecione este campo se pretender disponibilizar este relatório à equipa de Suporte da Azure ajudando neste caso.

O relatório é armazenado numa das Contas de Armazenamento sob a sua assinatura. Está disponível para ver e baixar mais tarde.

#### <a name="run-perfinsights-from-within-the-vm"></a>Executar PerfInsights de dentro do VM

Este método pode ser utilizado se pretender executar PerfInsights por períodos mais longos. O [artigo perfInsights](./how-to-use-perfinsights.md#how-do-i-run-perfinsights) dá uma passagem detalhada dos diferentes comandos e bandeiras que são necessários para executar PerfInsights como um executável. Para fins de alta utilização do CPU, você precisará de qualquer um dos seguintes modos:

- Cenário Avançado

  - `PerfInsights /run advanced xp /d 300 /AcceptDisclaimerAndShareDiagnostics`

- VM Slow (Performance) Cenário

  - `PerfInsights /run vmslow /d 300 /AcceptDisclaimerAndShareDiagnostics /sa <StorageAccountName> /sk <StorageAccountKey>`

A saída do comando será na mesma pasta onde guardou os PerfInsights executáveis.

#### <a name="what-to-look-for-in-the-report"></a>O que procurar no relatório

Depois de executar o relatório, a localização do conteúdo depende se foi executado através do portal Azure ou como um executável. Para qualquer uma das opções, aceda à pasta de registo gerada ou descarregue (se o portal Azure) localmente para análise.

### <a name="run-through-the-azure-portal"></a>Corra através do portal Azure

  ![Diagnósticos de desempenho de alto impacto](./media/troubleshoot-high-cpu-issues-azure-windows-vm/4-high-impact-performance-diagnostics.png)

  ![Transferir relatório](./media/troubleshoot-high-cpu-issues-azure-windows-vm/5-download-report.png)

#### <a name="run-from-within-vm"></a>Correr a partir de dentro de VM

A sua estrutura de pasta deve assemelhar-se às seguintes imagens:

  ![Selecione saída](./media/troubleshoot-high-cpu-issues-azure-windows-vm/6-select-output.png)

  ![Estrutura de pasta](./media/troubleshoot-high-cpu-issues-azure-windows-vm/7-folder-structure.png)

1. Quaisquer coleções adicionais como Perfmon, Xperf, Netmon, registos SMB, registos de eventos, e assim por diante, podem ser encontradas na pasta de Saída.

1. O relatório atual, juntamente com análises e recomendações.

1. Tanto para o Desempenho (VMslow) como para o Advanced, o relatório recolhe informações **perfmon** durante a duração dos PerfInsights.

1. Os registos do evento mostram uma visão rápida de detalhes úteis de falhas de sistema ou de processo.

#### <a name="where-to-start"></a>Onde começar

Abra o relatório PerfInsights. O separador **Descobertas** regista quaisquer outliers em termos de consumo de recursos. Se houver casos de utilização de ALTO CPU, o **separador Resultados** irá categorizá-lo como Alto Impacto ou Impacto Médio.

  ![Recursos de nível de impacto](./media/troubleshoot-high-cpu-issues-azure-windows-vm/8-impact-level-resources.png)

À semelhança do exemplo anterior, PerfInsights foi executado durante 30 minutos. Durante metade desse tempo, o processo destacado foi o sCP esgotado no lado superior. Se o mesmo processo tivesse sido decorrer ao longo do tempo de recolha, o nível de impacto teria mudado para **HIGH**.

Se expandir o evento **Descobertas,** verá vários detalhes chave. O separador lista os processos em ordem descendente, por consumo médio de **CPU,** e mostra se o processo estava relacionado com o sistema, uma aplicação detida pela Microsoft (SQL, IIS) ou um processo de terceiros.

#### <a name="more-details"></a>Mais detalhes

Existe um subtab dedicado ao abrigo **do CPU** que pode ser usado para análise de padrões detalhados, por núcleo ou por processo.

O separador **Top CPU Consumers** tem duas secções de interesse separadas, e você pode ver por processador estatísticas aqui. O design da aplicação é frequentemente Single-Threaded ou fixa-se a um único processador. Neste cenário, um ou alguns núcleos funcionam a 100%, enquanto outros núcleos correm em níveis esperados. Estes cenários são mais complexos porque o CPU médio do servidor parece funcionar como esperado, mas os processos que estão presos em núcleos que têm alta utilização serão mais lentos do que o esperado.

  ![uso de alto CPU](./media/troubleshoot-high-cpu-issues-azure-windows-vm/9-high-cpu-usage.png)

A segunda secção (igualmente importante), é **os consumidores de CPU de topo.** Esta secção mostra tanto os detalhes do processo como o seu padrão de utilização do CPU. A lista é ordenada por ter consumidores de CPU médios de alta média no topo.

  ![Tom longo prazo CONSUMIDORES CPU](./media/troubleshoot-high-cpu-issues-azure-windows-vm/10-top-long-running-cpu-consumers.png)

Estes dois separadores serão suficientes para definir o caminho para os próximos passos de resolução de problemas. Dependendo do processo que está a impulsionar a condição de ALTA CPU, terá de responder às perguntas que foram feitas anteriormente. Processos como SQL Server (sqlservr.exe) ou IIS (w3wp.exe) requerem uma perfuração específica na consulta ou alterações de código que estão a causar esta condição. Para processos do sistema como OMI ou Lsass.exe, tem de seguir um caminho diferente.

Para processos relacionados com Azure VM, tais como RDAgent, OMS e execuções de extensões de monitorização, poderá ter de corrigir uma nova construção ou versão, obtendo ajuda da equipa de Suporte Azure.

### <a name="perfmon"></a>Perfmon

**Perfmon** é uma das primeiras ferramentas para resolver problemas num problema de recursos no Windows Server. Não dá um relatório claro que tenha recomendações ou conclusões. Em vez disso, requer que o utilizador explore os dados recolhidos e utilize um filtro específico nas diferentes categorias de contadores.

PerfInsights recolhe Perfmon como um registo extra para VMSlow e cenários avançados. No entanto, a Perfmon pode ser recolhida de forma independente, e tem estes benefícios adicionais:

- Pode ser recolhido remotamente.

- Pode ser programado através **de Tarefas.**

- Pode ser recolhido por períodos mais longos ou em modo contínuo utilizando a função roll-over.

Considere o mesmo exemplo que é mostrado em PerfInsights para ver como Perfmon mostra estes dados. As categorias de contadores necessárias são as seguintes:

- Informação do processador > %> _Total tempo do processador

- Processo > %ProcessadorTime > todas as instâncias

#### <a name="where-to-start"></a>Onde começar

Os nomes dos ficheiros de saída de Perfmon têm uma `.blg` extensão. Pode recolher estes ficheiros de forma independente ou utilizando PerfInsights. Para esta discussão, você estará usando o Perfmon `.blg` que está incluído nos dados de PerfInsights e que foram recolhidos de acordo com o exemplo anterior.

Não existem relatórios padrão disponíveis no Perfmon. Existem diferentes pontos de vista que alteram o tipo de gráfico, mas a filtração do processo (ou o trabalho que é necessário para identificar processos culpados) é manual.

> [!NOTE]
> A [Ferramenta PAL](https://github.com/clinthuffman/PAL) pode consumir `.blg` ficheiros e gerar relatórios detalhados.

Para começar, selecione a categoria **Add Counters.**

1. Nos **contadores disponíveis,** selecione o contador **%ProcessorTime** na categoria **de informação** do processador.

1. Selecione **_Total**, que lhe dá as estatísticas de todos os núcleos combinados.

1. Selecione **Adicionar**. A janela mostra **%ProcessadorTime** em  **contadores adicionados**.

  ![Adicione tempo de processador](./media/troubleshoot-high-cpu-issues-azure-windows-vm/11-add-processor-time.png)

Depois de os contadores estarem carregados, verás as tendências de linha no período de tempo da recolha. Pode selecionar ou limpar os balcões. Até agora, só acrescentou um balcão.

  ![Definições do Monitor de Desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/12-performance-monitor-1.png)

Cada contador terá valores **médios,** **mínimos** e **máximos.** Foco tanto nos valores **Médio** como **Máximo,** uma vez que o valor médio pode variar dependendo da duração da recolha de dados. Se a atividade de ALTA CPU foi vista durante 10 minutos enquanto a recolha global foi de 40 minutos, os valores médios serão muito mais baixos.

O gráfico de tendência anterior mostra que o **Processador Total** foi de cerca de 80% durante aproximadamente 15 minutos.

#### <a name="identify-the-process"></a>Identificar o processo

Identificámos que o servidor tinha um alto consumo de CPU durante um determinado período de tempo, mas ainda não identificámos o condutor. Ao contrário da utilização de PerfInsights, deve procurar manualmente o processo de culpado neste caso.

Para esta tarefa, deve limpar ou remover os contadores **%ProcessorTime** previamente adicionados e, em seguida, adicionar uma nova categoria:

- Processo > %ProcessadorTime > todas as instâncias

Esta categoria carregará contadores para todos os processos em execução naquele momento.

  ![Adicionar balcões](./media/troubleshoot-high-cpu-issues-azure-windows-vm/13-add-counters.png)

Num computador de produção típico, centenas ou processos podem estar em execução. Portanto, pode demorar algum tempo a limpar todos os balcões que parecem ter um gráfico de tendência baixo ou plano.

Para acelerar este processo, use a vista **Histograma** e altere o tipo de visualização da **Linha** para **o Histogramo,** que lhe dará um gráfico de barras. Você vai descobrir que é mais fácil escolher os processos que experimentam o uso de ALTO CPU durante o tempo de recolha.

Porque haverá sempre um bar para **a Total,** concentre-se em bares que mostram uma alta taxa de exaustão. Pode apagar as outras barras para limpar a vista. Agora, volte para a vista **da linha.**

  ![Indicadores de monitor de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/14-performance-monitor-2.png)

Agora é mais fácil apanhar o processo de culpado. Por predefinição, os valores **Max** e **Min** são múltiplos do número de núcleos no servidor ou fios do processo.

  ![Resultados do monitor de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/15-performance-monitor-3.png)

A lista de ferramentas disponíveis não termina na PerfInsights para o Perfmon. Tem acesso a outras ferramentas, como **ProcessMonitor** (ProcMon) ou **Xperf**. Existem muitas ferramentas de terceiros disponíveis para usar conforme necessário.

### <a name="azure-monitoring-tools"></a>Ferramentas de monitorização do Azure

Os VMs Azure têm métricas fiáveis que incluem informações básicas como CPU, Rede I/O e bytes de E/O. Para métricas avançadas, como o Azure Monitor, terá de fazer apenas algumas seleções para configurar e utilizar uma conta de armazenamento que especifique.

#### <a name="basic-default-counters"></a>Contadores básicos (predefinidos)

  ![Métricas de gráfico](./media/troubleshoot-high-cpu-issues-azure-windows-vm/16-chart-metrics.png)

#### <a name="enabling-azure-monitor"></a>Ativar o Monitor Azure

Depois de ativar as métricas do Azure Monitor, o software instala uma extensão no VM e, em seguida, começa a recolher métricas granulares, que inclui contadores Perfmon.

  ![Conta de armazenamento de diagnóstico](./media/troubleshoot-high-cpu-issues-azure-windows-vm/17-diagnostics-storage-account.png)

As categorias **básicas** de contador são definidas como **padrão.** No entanto, também pode definir uma coleção **Personalizada.**

  ![Contadores de desempenho](./media/troubleshoot-high-cpu-issues-azure-windows-vm/18-performance-counters.png)

Depois de ativadas as definições, pode ver estes contadores **De hóspedes** na secção **Métricas.** Também pode definir **Alertas** (incluindo mensagens de correio e-mail) se as métricas atingirem um determinado limiar.

  ![Espaço de nome de métricas](./media/troubleshoot-high-cpu-issues-azure-windows-vm/19-metrics-namespace.png)

Para obter mais informações sobre como utilizar o monitor Azure para gerir os VMs do Azure, consulte [as máquinas virtuais do Azure monitor com o Azure Monitor](../../azure-monitor/vm/monitor-vm-azure.md).

### <a name="reactive-troubleshooting"></a>Resolução de problemas reativos

Se o problema já ocorreu, tem de descobrir o que causou a questão da CPU em primeiro lugar. A posição reativa pode ser complicada. O modo de recolha de dados não será tão útil porque o problema já ocorreu.

Se este problema foi uma ocorrência única, pode ser difícil determinar qual a aplicação que o causou. Se o Azure VM foi configurado para utilizar o OMS ou outros rastreios de diagnóstico, ainda pode obter informações sobre o que causou o problema.

Se estiver a lidar com um padrão de repetição, recolha os dados durante o tempo em que o problema provavelmente ocorrerá a seguir.

PerfInsights ainda não tem uma capacidade **de execução programada.** No entanto, Perfmon pode ser executado e programado através da linha de comando.

### <a name="logman-command"></a>Comando Logman

O comando **Logman Create Counter** é utilizado para executar a recolha perfmon através da linha de comando, para o agendar através **do Task Manager,** ou para executá-lo remotamente.

**Amostra** (inclui modo de recolha remota)

`Logman create counter LOGNAME -u DOMAIN\USERNAME * -f bincirc -v mmddhhmm -max 300 -c "\\SERVERNAME\LogicalDisk(*)\*" "\\SERVERNAME\Memory\*" "\\SERVERNAME\Network Interface(*)\*" "\\SERVERNAME\Paging File(*)\*" "\\SERVERNAME\PhysicalDisk(*)\*" "\\SERVERNAME\Process(*)\*" "\\SERVERNAME\Redirector\*" "\\SERVERNAME\Server\*" "\\SERVERNAME\System\*" "\\SERVERNAME\Terminal Services\*" "\\SERVERNAME\Processor(*)\*" "\\SERVERNAME\Cache\*" -si 00:01:00`

Logman.exe também podem ser iniciados a partir de um computador Azure VM no mesmo VNET.

Para saber mais sobre estes parâmetros, consulte [o logman criar contador](/windows-server/administration/windows-commands/logman-create-counter).

Após a recolha dos dados do Perfmon enquanto o problema está a ocorrer, os passos restantes para analisar os dados são os mesmos que discutidos anteriormente.

## <a name="conclusion"></a>Conclusão

Para qualquer problema de desempenho, compreender a sua carga de trabalho é fundamental para resolver o problema. As opções sobre diferentes SKUs VM e diferentes opções de armazenamento de discos devem ser avaliadas mantendo o foco na carga de trabalho de produção. O processo de teste de soluções em diferentes VMs pode ajudá-lo a tomar a melhor decisão.

Como as operações do utilizador e a quantidade de dados variam, mantenha sempre um tampão nas capacidades de computação, rede e I/O do VM. Qualquer mudança repentina na carga de trabalho não tem um efeito tão grande.

Se prevê que a carga de trabalho cresça em breve, mude-se para um SKU mais elevado que tenha mais poder de computação. Se a carga de trabalho for computacionalmente intensiva, escolha os SKUs VM sabiamente.