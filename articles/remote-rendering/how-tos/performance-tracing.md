---
title: Criar rastreios de desempenho do lado do cliente
description: Melhores práticas para perfis de desempenho do lado do cliente usando O WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 1d4ce68bdda5fbc3dfdb7396141289a58dab5bd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "92204100"
---
# <a name="create-client-side-performance-traces"></a>Criar rastreios de desempenho do lado do cliente

Existem muitas razões pelas quais o desempenho da Renderização Remota Azure pode não ser tão bom quanto desejado. Além do desempenho puro de renderização no servidor de nuvem, especialmente a qualidade da ligação de rede tem uma influência significativa na experiência. Para perfilar o desempenho do servidor, consulte as [consultas de desempenho do lado do servidor](../overview/features/performance-queries.md)do capítulo .

Este capítulo centra-se em como identificar potenciais estrangulamentos do lado do cliente através *:::no-loc text="performance traces":::* de .

## <a name="getting-started"></a>Introdução

Se é novo na funcionalidade do :::no-loc text="performance tracing"::: Windows, esta secção irá mencionar os termos e aplicações mais fundamentais para começar.

### <a name="installation"></a>Instalação

As aplicações utilizadas para fazer rastreios com ARR são ferramentas de finalidade geral que podem ser usadas para todo o desenvolvimento do Windows. São fornecidos através do [Windows Performance Toolkit](/windows-hardware/test/wpt/). Para obter este kit de ferramentas, descarregue o [Kit de Avaliação e Implementação](/windows-hardware/get-started/adk-install)do Windows .

### <a name="terminology"></a>Terminologia

Ao procurar informações sobre vestígios de desempenho, irá inevitavelmente encontrar uma série de termos. As mais importantes são:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

**ETW** significa [ **E** vent **T** corrida para **W** indows](/windows/win32/etw/about-event-tracing). É simplesmente o nome geral para a eficiente instalação de rastreio de nível kernel que é incorporada no Windows. Chama-se rastreio de *eventos,* porque as aplicações que suportam a ETW emitirão eventos para registar ações que podem ajudar a rastrear problemas de desempenho. Por predefinição, o sistema operativo já emite eventos para coisas como acessos a discos, interruptores de tarefas e tal. Aplicações como ARR também emitem eventos personalizados, por exemplo sobre quadros abandonados, lag de rede, etc.

**ETL** significa **E** vent **T** race **L** ogging. Significa simplesmente que foi recolhido um vestígio (registado) e, portanto, é normalmente usado como extensão de ficheiros para ficheiros que armazenam os dados de rastreio. Assim, quando faz um rastreio, normalmente terá um \* ficheiro .etl depois.

**WPR** significa [ **W** indows **P** erformance **R** ecorder](/windows-hardware/test/wpt/windows-performance-recorder) e é o nome da aplicação que inicia e impede a gravação de vestígios de eventos. O WPR toma um ficheiro de perfil \* (.wprp) que configura quais os eventos exatos para registar. Tal `wprp` ficheiro é fornecido com o ARR SDK. Ao fazer rastreios num pc de secretária, pode lançar o WPR diretamente. Ao fazer um rastreio nos HoloLens, normalmente passa-se pela interface web.

**WPA** significa [ **W** indows **P** erformance **A** nalyzer](/windows-hardware/test/wpt/windows-performance-analyzer) e é o nome da aplicação GUI que é usada para abrir \* ficheiros .etl e vasculhar os dados para identificar problemas de desempenho. O WPA permite-lhe classificar dados por vários critérios, exibir os dados de várias formas, pesquisar detalhes e correlacionar informações.

Enquanto os vestígios ETL podem ser criados em qualquer dispositivo Windows (PC local, HoloLens, servidor de nuvem, etc.), são normalmente guardados para o disco e analisados com WPA num PC de secretária. Os ficheiros ETL podem ser enviados para outros desenvolvedores para que possam dar uma vista de olhos. Esteja ciente de que informações sensíveis, tais como caminhos de ficheiros e endereços IP, podem ser capturadas em vestígios ETL, no entanto. Pode utilizar a ETW de duas formas: gravar vestígios ou analisar vestígios. Os vestígios de gravação são diretos e requer uma configuração mínima. Analisar vestígios, por outro lado, requer uma compreensão decente tanto da ferramenta WPA como do problema que está a investigar. O material geral para a aprendizagem do WPA será dado abaixo, bem como diretrizes para como interpretar vestígios específicos do ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Gravando um vestígio em um PC local

Para identificar problemas de desempenho do ARR, deve preferir fazer um rastreio diretamente num HoloLens, porque essa é a única maneira de obter uma imagem das verdadeiras características de desempenho. No entanto, se você especificamente quer fazer um rastreio sem as restrições de desempenho HoloLens ou você apenas quer aprender a usar WPA e não precisa de um traço realista, eis como fazê-lo.

### <a name="wpr-configuration"></a>Configuração WPR

1. Lance o menu [:::no-loc text="Windows Performance Recorder":::](/windows-hardware/test/wpt/windows-performance-recorder) inicial. 
1. Expandir **Mais Opções**
1. Clique **em Adicionar Perfis...**
1. Selecione o ficheiro *AzureRemoteRenderingNetworkProfiling.wprp*. Pode encontrar este ficheiro no ARR SDK em *Ferramentas/ETLProfiles*.
   O perfil será agora listado no WPR sob *medições personalizadas*. Certifique-se de que é o único perfil ativado.
1. Expandir *triagem de primeiro nível:*
    * Se tudo o que pretende fazer é capturar um traço rápido dos eventos de networking ARR, **desative** esta opção.
    * Se precisar de correlacionar eventos de rede ARR com outras características do sistema, como CPU ou utilização da memória, então **ative** esta opção.
    * Se ativar esta opção, o traço provavelmente será de vários gigabytes de tamanho e demorará muito tempo a guardar e a abrir no WPA.

Em seguida, a sua configuração WPR deve ficar assim:

![Configuração WPR](./media/wpr.png)

### <a name="recording"></a>Gravação

Clique **Em Iniciar** a gravação de um rastreio. Pode iniciar e parar de gravar a qualquer momento; não precisa de fechar a sua aplicação antes de o fazer. Como pode ver, não precisa de especificar qual a aplicação a localizar, uma vez que a ETW irá sempre registar um rastreio para todo o sistema. O `wprp` ficheiro especifica quais os tipos de eventos a registar.

Clique em **Guardar** para parar a gravação e especificar onde guardar o ficheiro ETL.

Tem agora um ficheiro ETL que pode abrir diretamente no WPA ou enviar a outra pessoa.

## <a name="recording-a-trace-on-a-hololens"></a>Gravando um vestígio num HoloLens

Para registar vestígios num HoloLens, insira o seu dispositivo e introduza o seu endereço IP num browser para abrir o Portal do *Dispositivo*.

![Portal do Dispositivo](./media/wpr-hl.png)

1. À esquerda, navegue para *Performance > Performance Tracing*.
1. Selecione **perfis personalizados**
1. Clique **:::no-loc text="Browse...":::**
1. Selecione o ficheiro *AzureRemoteRenderingNetworkProfiling.wprp*. Pode encontrar este ficheiro no ARR SDK em *Ferramentas/ETLProfiles*.
1. Clique **no rastreio de início**
1. Os HoloLens estão agora a registar um rasto. Certifique-se de desencadear os problemas de desempenho que pretende investigar. Em seguida, clique em **Stop Trace**.
1. Os vestígios serão listados na parte inferior da página. Clique no ícone do disco no lado direito para descarregar o ficheiro ETL.

Tem agora um ficheiro ETL que pode abrir diretamente no WPA ou enviar a outra pessoa.

## <a name="analyzing-traces-with-wpa"></a>Analisar vestígios com WPA

### <a name="wpa-basics"></a>Básicos do WPA

O Windows Performance Analyzer é a ferramenta padrão para abrir ficheiros ETL e inspecionar os vestígios. Uma explicação para o funcionamento do WPA está fora de alcance para este artigo. Para começar, veja estes recursos:

* Veja os [vídeos introdutórios](/windows-hardware/test/wpt/windows-performance-analyzer) para uma primeira visão geral.
* O próprio WPA tem um *separador Getting Started,* o que explica os passos comuns. Veja os tópicos disponíveis. Especialmente em "Ver Dados" obtém-se uma introdução rápida como criar gráficos para dados específicos.
* Existe excelente informação [neste site](https://randomascii.wordpress.com/2015/09/24/etw-central/), no entanto, nem tudo é relevante para principiantes.

### <a name="graphing-data"></a>Dados de gráficos

Para começar com o rastreio de ARR, as seguintes peças são boas de saber.

![Gráfico de desempenho](./media/wpa-graph.png)

A imagem acima mostra uma tabela de dados de rastreio e uma representação de gráfico dos mesmos dados.

Na mesa na parte inferior, note a barra amarela (dourada) e a barra azul. Pode arrastar estas barras e colocá-las em qualquer posição.

Todas as **colunas à esquerda da barra amarela** são interpretadas como **teclas**. As chaves são usadas para estruturar a árvore na janela superior esquerda. Aqui temos duas *colunas-chave,* "Nome do Provedor" e "Nome de Tarefa". Consequentemente, a estrutura da árvore na janela superior esquerda tem dois níveis de profundidade. Se reencomendar as colunas ou adicionar ou remover colunas da área de tecla, a estrutura na vista da árvore muda.

**As colunas à direita da barra azul** são utilizadas para o **visor de gráfico** na janela superior direita. Na maior parte das vezes apenas a primeira coluna é usada, mas alguns modos gráficos requerem várias colunas de dados. Para que os gráficos de linha funcionem, o *modo de agregação* nessa coluna deve ser definido. Utilize 'Avg' ou 'Max'. O modo de agregação é utilizado para determinar o valor do gráfico num dado pixel, quando um pixel cobre uma gama com vários eventos. Isto pode ser observado definindo a agregação para 'Sum' e, em seguida, fazendo zoom para dentro e para fora.

As colunas no meio não têm um significado especial.

![Vista de eventos](./media/wpa-event-view.png)

No *Editor de Visualização de Eventos Genéricos* pode configurar todas as colunas a exibir, o modo de agregação, a triagem e quais as colunas utilizadas como teclas ou para grafização. No exemplo acima, o **Campo 2** está ativado e o Campo 3 - 6 está desativado. O Field 2 é tipicamente o primeiro campo de *dados personalizado* de um evento ETW e, portanto, para eventos ARR "FrameStatistics", que representam algum valor de latência da rede. Permitir que outras colunas "Field" vejam mais valores deste evento.

### <a name="presets"></a>Predefinições

Para analisar corretamente um traço, terá de descobrir o seu próprio fluxo de trabalho e a exibição de dados preferidos. No entanto, para obter uma visão geral rápida sobre os eventos específicos do ARR, incluímos o perfil da Plataforma de Proteção de Software do Windows e os ficheiros de predefinição na pasta *Tools/ETLProfiles*. Para carregar um perfil completo, selecione *Perfis > Aplicar...* na barra de menus WPA, ou abrir o painel *My Presets* *(Janela > As Minhas Predefinições)* e selecionar *Import*. O primeiro irá configurar uma configuração completa do WPA como na imagem abaixo. Este último apenas disponibilizará predefinições para as várias configurações de visualização e permitirá que abra rapidamente uma visão para olhar para uma peça específica de dados do evento ARR.

![Predefinições](./media/wpa-arr-trace.png)

A imagem acima mostra vistas de vários eventos específicos do ARR mais uma visão da utilização geral do CPU.

## <a name="next-steps"></a>Passos seguintes

* [Consultas de desempenho do lado do servidor](../overview/features/performance-queries.md)