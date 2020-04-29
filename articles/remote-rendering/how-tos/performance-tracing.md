---
title: Criar rastreios de desempenho do lado do cliente
description: Boas práticas para perfis de desempenho do lado do cliente usando WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1f4207a11f3ae3664023fccf6178b6db7cf253b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681314"
---
# <a name="create-client-side-performance-traces"></a>Criar rastreios de desempenho do lado do cliente

Existem muitas razões pelas quais o desempenho da Renderização Remota Azure pode não ser tão bom quanto desejado. Além do desempenho de renderização pura no servidor de nuvem, especialmente a qualidade da ligação de rede tem uma influência significativa na experiência. Para perfilar o desempenho do servidor, consulte as consultas de desempenho do [lado do servidor](../overview/features/performance-queries.md)do capítulo .

Este capítulo centra-se na forma de identificar potenciais estrangulamentos do lado do cliente através de *traços*de desempenho .

## <a name="getting-started"></a>Introdução

Se for novidade na funcionalidade de rastreio de desempenho do Windows, esta secção mencionará os termos e aplicações mais fundamentais para começar.

### <a name="installation"></a>Instalação

As aplicações usadas para fazer rastreios com ARR são ferramentas de propósito geral que podem ser usadas para todo o desenvolvimento do Windows. São fornecidos através do Kit de [Ferramentas de Desempenho do Windows](https://docs.microsoft.com/windows-hardware/test/wpt/). Para obter este kit de ferramentas, descarregue o Kit de [Avaliação e Implementação do Windows](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Ao procurar informações sobre traços de desempenho, irá inevitavelmente encontrar uma série de termos. Os mais importantes são:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** significa [ **E**vent **T**correndo para **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). É simplesmente o nome principal para a eficiente instalação de rastreio de nível kernel que é incorporada no Windows. Chama-se rastreio de *eventos,* porque as aplicações que suportam a ETW emitirão eventos para registar ações que possam ajudar a rastrear problemas de desempenho. Por padrão, o sistema operativo já emite eventos para coisas como acessos ao disco, interruptores de tarefas e tal. Aplicações como a ARR emitem ainda eventos personalizados, por exemplo, sobre quadros caídos, atraso de rede, etc.

**ETL** significa **E**vent **T**corrida **L**ogging. Significa simplesmente que um vestígio foi recolhido (registado) e, portanto, é normalmente utilizado como extensão de ficheiros que armazenam os dados de rastreio. Assim, quando fizer um rastreio, normalmente terá um \*ficheiro .etl depois.

**WPR** significa [ **W**indows **P**erformance **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) e é o nome da aplicação que inicia e para a gravação de vestígios de eventos. WPR pega num\*ficheiro de perfil (.wprp) que configura quais eventos exatos para registar. Tal `wprp` ficheiro é fornecido com o ARR SDK. Ao fazer vestígios num pc de secretária, pode lançar WPR diretamente. Ao fazer um rastreio nos HoloLens, você normalmente passa pela interface web em vez disso.

**WPA** significa [ **W**indots **P**erformance **A**nalyzer](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) e é o \*nome da aplicação GUI que é usada para abrir ficheiros .etl e vasculhar através dos dados para identificar problemas de desempenho. O WPA permite-lhe classificar os dados por vários critérios, exibir os dados de várias maneiras, desenterrar detalhes e correlacionar informações.

Embora os vestígios ETL possam ser criados em qualquer dispositivo Windows (PC local, HoloLens, servidor de nuvem, etc.), são normalmente guardados para disco remaecar e analisados com WPA num PC de ambiente de trabalho. Os ficheiros ETL podem ser enviados para outros desenvolvedores para que possam ver. Esteja ciente de que informações sensíveis, tais como ficheiros e endereços IP, podem ser capturadas em vestígios eTL, no entanto. Pode utilizar a ETW de duas formas: gravar vestígios ou analisar vestígios. Os vestígios de gravação são simples e requerem uma configuração mínima. Analisar vestígios por outro lado requer uma compreensão decente tanto da ferramenta WPA como do problema que está a investigar. O material geral para a aprendizagem do WPA será dado abaixo, bem como orientações para a forma de interpretar vestígios específicos de ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Gravando um vestígio num PC local

Para identificar problemas de desempenho do ARR, você deve preferir fazer um traço diretamente em um HoloLens, porque essa é a única maneira de obter uma imagem das verdadeiras características de desempenho. No entanto, se você especificamente quer fazer um rastreio sem as restrições de desempenho holoLens ou apenas quer aprender a usar WPA e não precisa de um traço realista, aqui está como fazê-lo.

### <a name="wpr-configuration"></a>Configuração wpr

1. Lance o Gravador de Desempenho do [Windows](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) a partir do *menu inicial*.
1. Expandir **mais opções**
1. Clique em **Adicionar Perfis...**
1. Selecione o ficheiro *AzureRemoteRenderingNetworkProfiling.wprp*. Pode encontrar este ficheiro no ARR SDK em *Ferramentas/ETLProfiles*.
   O perfil será agora listado em WPR sob *medidas personalizadas*. Certifique-se de que é o único perfil ativado.
1. Expandir *a triagem de primeiro nível:*
    * Se tudo o que você quer fazer é capturar um traço rápido dos eventos de rede ARR, **desative** esta opção.
    * Se necessitar de correlacionar eventos de rede ARR com outras características do sistema, como CPU ou utilização de memória, **então ative** esta opção.
    * Se você ativar esta opção, o traço provavelmente será vários gigabytes de tamanho e demorará muito tempo a economizar e abrir no WPA.

Em seguida, a sua configuração wpr deve ser assim:

![Configuração wpr](./media/wpr.png)

### <a name="recording"></a>Gravação

Clique **em Começar** a gravar um vestígio. Pode começar e parar de gravar a qualquer momento; não precisa de fechar a sua aplicação antes de o fazer. Como pode ver, não precisa especificar qual a aplicação a localizar, uma vez que a ETW registará sempre um vestígio para todo o sistema. O `wprp` ficheiro especifica quais os tipos de eventos a registar.

Clique em **Guardar** para parar de gravar e especificar onde armazenar o ficheiro ETL.

Tem agora um ficheiro ETL que pode abrir diretamente na WPA ou enviar para outra pessoa.

## <a name="recording-a-trace-on-a-hololens"></a>Gravando um vestígio num HoloLens

Para gravar um vestígio num HoloLens, arranque o seu dispositivo e introduza o seu endereço IP num browser para abrir o Portal do *Dispositivo*.

![Portal do Dispositivo](./media/wpr-hl.png)

1. À esquerda, navegue para *performance > Performance Tracing*.
1. Selecione **perfis personalizados**
1. Clique em **Navegar...**
1. Selecione o ficheiro *AzureRemoteRenderingNetworkProfiling.wprp*. Pode encontrar este ficheiro no ARR SDK em *Ferramentas/ETLProfiles*.
1. Clique em **Iniciar Rastreio**
1. Os HoloLens estão agora a gravar um rasto. Certifique-se de desencadear os problemas de desempenho que pretende investigar. Em seguida, clique em **Parar Rastrear**.
1. O vestígio será listado na parte inferior da página web. Clique no ícone do disco no lado direito para descarregar o ficheiro ETL.

Tem agora um ficheiro ETL que pode abrir diretamente na WPA ou enviar para outra pessoa.

## <a name="analyzing-traces-with-wpa"></a>Analisar vestígios com WPA

### <a name="wpa-basics"></a>Bases do WPA

O Windows Performance Analyzer é a ferramenta padrão para abrir ficheiros ETL e inspecionar os vestígios. Uma explicação de como a WPA funciona está fora de âmbito para este artigo. Para começar, olhe para estes recursos:

* Veja os [vídeos introdutórios](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) para uma primeira visão geral.
* A própria WPA tem um separador *Getting Started,* que explica passos comuns. Veja os tópicos disponíveis. Especialmente em "Ver Dados" obtém-se uma introdução rápida como criar gráficos para dados específicos.
* Há excelentes informações [neste site,](https://randomascii.wordpress.com/2015/09/24/etw-central/)no entanto, nem tudo é relevante para principiantes.

### <a name="graphing-data"></a>Dados de grafegrafia

Para começar com o rastreio de ARR, as seguintes peças são boas de saber.

![Gráfico de desempenho](./media/wpa-graph.png)

A imagem acima mostra uma tabela de dados de rastreio e uma representação de gráfico dos mesmos dados.

Na mesa na parte inferior, note a barra amarela (dourada) e a barra azul. Pode arrastar estas barras e colocá-las em qualquer posição.

Todas as **colunas à esquerda da barra amarela** são interpretadas como **chaves**. As chaves são usadas para estruturar a árvore na janela superior esquerda. Aqui temos duas *colunas-chave,* "Nome do Fornecedor" e "Nome de Tarefa". Consequentemente, a estrutura da árvore na janela superior-esquerda tem dois níveis de profundidade. Se reencomendar as colunas ou adicionar ou remover colunas da área-chave, a estrutura na vista da árvore muda.

**As colunas à direita da barra azul** são utilizadas para o **visor** gráfico na janela superior direita. Na maior parte das vezes apenas a primeira coluna é usada, mas alguns modos de gráfico requerem várias colunas de dados. Para que os gráficos de linha funcionem, deve ser definido o modo de *agregação* nessa coluna. Use 'Avg' ou 'Max'. O modo de agregação é utilizado para determinar o valor do gráfico num determinado pixel, quando um pixel cobre uma gama com múltiplos eventos. Isto pode ser observado colocando a agregação em 'Soma' e, em seguida, zooming dentro e fora.

As colunas no meio não têm um significado especial.

![Vista de eventos](./media/wpa-event-view.png)

No *Generic Events View Editor* pode configurar todas as colunas para exibir, o modo de agregação, a triagem e quais as colunas que são usadas como teclas ou para grafismo. No exemplo acima, o **Campo 2** está ativado e o Campo 3 - 6 está desativado. O Campo 2 é tipicamente o primeiro campo de *dados personalizado* de um evento ETW e, portanto, para eventos ARR "FrameStatistics", que representam algum valor de latência da rede. Permitir que outras colunas "Field" vejam outros valores deste evento.

### <a name="presets"></a>Predefinições

Para analisar adequadamente um vestígio, terá de descobrir o seu próprio fluxo de trabalho e o ecrã de dados preferido. No entanto, para podermos obter uma visão geral rápida sobre os eventos específicos do ARR, incluímos o perfil da Plataforma de Proteção de Software windows e predefinimos ficheiros na pasta *Tools/ETLProfiles*. Para carregar um perfil completo, selecione *Perfis > Aplicar...* a partir da barra de menus WPA, ou abrir o painel *My Presets* (*Janela > Minhas Presets)* e selecione *Import*. O primeiro irá configurar uma configuração WPA completa como na imagem abaixo. Este último só disponibilizará predefinições para as várias configurações de visualização e permitirá abrir rapidamente uma vista para ver uma peça específica dos dados do evento ARR.

![Predefinições](./media/wpa-arr-trace.png)

A imagem acima mostra vistas de vários eventos específicos da ARR mais uma visão da utilização global do CPU.

## <a name="next-steps"></a>Passos seguintes

* [Consultas de desempenho do lado do servidor](../overview/features/performance-queries.md)
