---
title: Guia de Azure AI para soluções de manutenção preditiva - Processo de Ciência de Dados de Equipa
description: Uma descrição abrangente da ciência dos dados que alimenta soluções de manutenção preditiva em múltiplas indústrias verticais.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 9b6cf2abfa797965d75bacd0b9c5e2eaf02b57a4
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308579"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Guia de IA da Azure para soluções de manutenção preditiva

## <a name="summary"></a>Resumo

A manutenção preditiva **(PdM)** é uma aplicação popular de análise preditiva que pode ajudar as empresas de várias indústrias a alcançar uma elevada utilização de ativos e poupanças em custos operacionais. Este guia reúne as diretrizes e as melhores práticas empresariais e analíticas para desenvolver e implementar com sucesso soluções PdM utilizando a tecnologia da [plataforma Microsoft Azure AI.](https://azure.microsoft.com/overview/ai-platform)

Para começar, este guia introduz cenários de negócio específicos do setor e o processo de qualificação destes cenários para o PdM. São também fornecidos os requisitos de dados e técnicas de modelação para a construção de soluções PdM. O principal conteúdo do guia está no processo de ciência de dados - incluindo os passos de preparação de dados, engenharia de recursos, criação de modelos e operacionalização de modelos. Para complementar estes conceitos-chave, este guia lista um conjunto de modelos de solução para ajudar a acelerar o desenvolvimento de aplicações PdM. O guia aponta ainda recursos de formação úteis para que o praticante saiba mais sobre a IA por detrás da ciência dos dados. 

### <a name="data-science-guide-overview-and-target-audience"></a>Visão geral do guia de ciência de dados e público-alvo
A primeira metade deste guia descreve problemas comerciais típicos, os benefícios da implementação do PdM para resolver estes problemas, e enumera alguns casos de uso comum. Os decisores empresariais (BDMs) beneficiarão deste conteúdo. A segunda metade explica a ciência dos dados por trás do PdM, e fornece uma lista de soluções PdM construídas usando os princípios descritos neste guia. Também fornece caminhos de aprendizagem e ponteiros para material de treino. Os decisores técnicos (TDMs) acharão este conteúdo útil.

| Comece com... | Se estiver... |
|:---------------|:---------------|
| [Caso de negócio para manutenção preditiva](#business-case-for-predictive-maintenance) |um decisor empresarial (BDM) que procura reduzir o tempo de inatividade e os custos operacionais, e melhorar a utilização dos equipamentos |
| [Ciência de Dados para manutenção preditiva](#data-science-for-predictive-maintenance) |um decisor técnico (TDM) avaliando as tecnologias PdM para entender o processamento único de dados e os requisitos de IA para a manutenção preditiva |
| [Modelos de solução para manutenção preditiva](#solution-templates-for-predictive-maintenance)|um arquiteto de software ou um desenvolvedor de IA que procura levantar rapidamente uma demonstração ou uma prova de conceito |
| [Recursos de formação para manutenção preditiva](#training-resources-for-predictive-maintenance) | qualquer ou todos os acima, e quer aprender os conceitos fundamentais por trás da ciência de dados, ferramentas e técnicas.

### <a name="prerequisite-knowledge"></a>Conhecimento dos pré-requisitos
O conteúdo do BDM não espera que o leitor tenha qualquer conhecimento prévio da ciência dos dados. Para o conteúdo da TDM, o conhecimento básico das estatísticas e da ciência dos dados é útil. É recomendado conhecimento dos serviços Azure Data e IA, Python, R, XML e JSON. As técnicas de IA são implementadas em pacotes Python e R. Os modelos de solução são implementados usando serviços Azure, ferramentas de desenvolvimento e SDKs.

## <a name="business-case-for-predictive-maintenance"></a>Caso de negócio para manutenção preditiva

As empresas exigem que os equipamentos críticos estejam a funcionar no pico da eficiência e utilização para concretizarem o seu retorno sobre os investimentos de capital. Estes ativos podem ir desde motores de aeronaves, turbinas, elevadores ou refrigeradores industriais - que custam milhões - até aparelhos do dia-a-dia, como fotocopiadoras, máquinas de café ou refrigeradores de água.
- Por defeito, a maioria das empresas depende da _manutenção corretiva,_ onde as peças são substituídas à medida que falham. A manutenção corretiva garante que as peças são utilizadas completamente (portanto, não desperdiçam a vida útil dos componentes), mas custa ao negócio em tempo de inatividade, mão de obra e requisitos de manutenção não programados (horas de folga ou locais inconvenientes).
- No nível seguinte, as empresas praticam  _a manutenção preventiva,_ onde determinam o tempo de vida útil de uma parte, e mantêm-na ou substituem-na antes de uma falha. A manutenção preventiva evita falhas não programadas e catastróficas. Mas os elevados custos de tempo de inatividade programado, subutilização do componente durante a sua vida útil, e mão de obra ainda permanecem.
- O objetivo da _manutenção preditiva_ é otimizar o equilíbrio entre manutenção corretiva e preventiva, permitindo _apenas a tempo_ a substituição dos componentes. Esta abordagem só substitui os componentes quando estão perto de uma falha. Ao prolongar o tempo de vida dos componentes (em comparação com a manutenção preventiva) e à redução dos custos de manutenção e mão de obra não programados (sobre a manutenção corretiva), as empresas podem obter poupanças de custos e vantagens competitivas.

## <a name="business-problems-in-pdm"></a>Problemas de negócios no PdM
As empresas enfrentam um elevado risco operacional devido a falhas inesperadas e têm uma visão limitada da causa principal de problemas em sistemas complexos. Algumas das principais questões de negócio são:

- Detetar anomalias no desempenho ou funcionalidade do equipamento ou do sistema.
- Preveja se um ativo pode falhar num futuro próximo.
- Estimar a vida útil restante de um ativo.
- Identifique as principais causas de falha de um ativo.
- Identifique que ações de manutenção devem ser feitas, quando, num ativo.

As declarações típicas de objetivos do PdM são:

- Reduzir o risco operacional de equipamento crítico da missão.
- Aumentar a taxa de rentabilidade dos ativos prevendo falhas antes de ocorrerem.
- Controlar o custo de manutenção, permitindo operações de manutenção just-in-time.
- Menor atrito do cliente, melhorar a imagem da marca e perder vendas.
- Reduzir os custos de inventário reduzindo os níveis de inventário, prevendo o ponto de reencomenda.
- Descubra padrões ligados a vários problemas de manutenção.
- Forneça KPI's (indicadores-chave de desempenho) tais como pontuações de saúde para as condições do ativo.
- Estimar o tempo de vida remanescente dos ativos.
- Recomendar atividades de manutenção em tempo oportuno.
- Ativar o inventário a tempo, estimando as datas de encomenda para a substituição de peças.

Estas declarações de golos são os pontos de partida para:

- _cientistas de dados_ para analisar e resolver problemas preditivos específicos.
- _arquitetos e desenvolvedores de nuvem_ para juntar uma solução final.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Problemas de qualificação para manutenção preditiva
É importante salientar que nem todos os casos de utilização ou problemas de negócio podem ser efetivamente resolvidos pelo PdM. Existem três critérios de qualificação importantes que devem ser considerados durante a seleção de problemas:

- O problema tem de ser preditivo na natureza; ou seja, deve haver um alvo ou um resultado a prever. O problema deve também ter um caminho de ação claro para evitar falhas quando estas são detetadas.
- O problema deve ter um registo do histórico operacional do equipamento que contenha _resultados bons e maus._ O conjunto de ações tomadas para mitigar os maus resultados também deve estar disponível como parte destes registos. Os relatórios de erro, os registos de manutenção da degradação do desempenho, a reparação e a substituição de registos também são importantes. Além disso, as reparações efetuadas para melhorá-las, e os registos de substituição também são úteis.
- O histórico registado deve refletir-se em dados _relevantes_ que sejam de qualidade _suficiente_ para apoiar o caso de utilização. Para obter mais informações sobre a relevância e suficiência dos [dados, consulte os requisitos de Dados para a manutenção preditiva.](#data-requirements-for-predictive-maintenance)
- Finalmente, o negócio deve ter peritos de domínio que tenham uma compreensão clara do problema. Devem estar cientes dos processos e práticas internas para poderem ajudar o analista a compreender e interpretar os dados. Devem também poder fazer as alterações necessárias aos processos de negócio existentes para ajudar a recolher os dados certos para os problemas, se necessário.

## <a name="sample-pdm-use-cases"></a>Casos de utilização de PdM de amostra
Esta secção centra-se numa coleção de casos de utilização de PDM de várias indústrias como Aeroespacial, Utilitários e Transportes. Cada secção começa com um problema de negócio, e discute os benefícios da PdM, os dados relevantes em torno do problema do negócio, e finalmente os benefícios de uma solução PdM.

| Problema de negócios | Benefícios do PdM |
|:-----------------|-------------------|
|**Aviação**      |                   |
|_Atraso de voo e cancelamentos_ devido a problemas mecânicos. Falhas que não podem ser reparadas a tempo podem fazer com que os voos sejam cancelados e perturbar o agendamento e as operações. |As soluções PDM podem prever a probabilidade de uma aeronave ser atrasada ou cancelada devido a falhas mecânicas.|
|_Falha nas peças dos motores das aeronaves_: As substituições das peças do motor dos aviões estão entre as tarefas de manutenção mais comuns no seio da indústria aeronáutica. As soluções de manutenção requerem uma gestão cuidadosa da disponibilidade, entrega e planeamento de stock de componentes|Ser capaz de recolher informações sobre a fiabilidade dos componentes leva a uma redução substancial dos custos de investimento.|
|**Departamento Financeiro** |                         |
|_A falência do multibanco_ é um problema comum no sector bancário. O problema aqui é relatar a probabilidade de uma transação de levantamento de dinheiro multibanco ser interrompida devido a um engarrafamento de papel ou falha parcial no distribuidor de dinheiro. Com base nas previsões de falhas de transação, as caixas multibanco podem ser servidas proactivamente para evitar falhas.| Em vez de permitir que a máquina falhe a meio de uma transação, a alternativa desejável é programar a máquina para negar o serviço com base na previsão.|
|**Energia** |                          |
|_Falhas nas turbinas eólicas_: As turbinas eólicas são a principal fonte de energia em países/regiões ambientalmente responsáveis e implicam elevados custos de capital. Um componente chave nas turbinas eólicas é o motor gerador, cuja falha torna a turbina ineficaz. Também é muito caro de reparar.|Prever KPI's como mTTF (tempo médio para falhar) pode ajudar as empresas de energia a prevenir falhas de turbinas e garantir o mínimo de tempo de inatividade. As probabilidades de falha informarão os técnicos para monitorizarem as turbinas que provavelmente falharão em breve e programarão regimes de manutenção baseados no tempo. Os modelos preditivos fornecem insights sobre diferentes fatores que contribuem para a falha, o que ajuda os técnicos a entender melhor as causas dos problemas.|
|_Falhas nos disjuntores_: A distribuição de eletricidade a casas e empresas exige que as linhas elétricas estejam sempre operacionais para garantir o fornecimento de energia. Os disjuntores ajudam a limitar ou evitar danos nas linhas elétricas durante a sobrecarga ou condições atmosféricas adversas. O problema do negócio aqui é prever falhas nos disjuntores.| As soluções PDM ajudam a reduzir os custos de reparação e a aumentar o tempo de vida útil de equipamentos como disjuntores. Ajudam a melhorar a qualidade da rede de energia reduzindo falhas inesperadas e interrupções de serviço.|
|**Transporte e logística** |    |
|_Falhas na porta do elevador_: As grandes empresas de elevadores fornecem um serviço de stack completo para milhões de elevadores funcionais em todo o mundo. Segurança dos elevadores, fiabilidade e uptime são as principais preocupações para os seus clientes. Estas empresas rastreiam estes e vários outros atributos através de sensores, para os ajudar com a manutenção corretiva e preventiva. Num elevador, o problema mais proeminente do cliente é o mau funcionamento das portas do elevador. O problema do negócio neste caso é fornecer uma aplicação preditiva de base de conhecimento que preveja as potenciais causas de falhas na porta.| Elevadores são investimentos de capital para uma vida útil de 20 a 30 anos. Assim, cada venda potencial pode ser altamente competitiva; daí as expectativas de serviço e apoio são elevadas. A manutenção preditiva pode proporcionar a estas empresas uma vantagem sobre os seus concorrentes nas suas ofertas de produtos e serviços.|
|_Falhas nas rodas_: As falhas das rodas representam metade de todos os descarrilamentos dos comboios e custam milhares de milhões à indústria ferroviária global. As falhas das rodas também fazem com que os carris se deteriorem, fazendo com que o carril se parta prematuramente. As quebras ferroviárias levam a eventos catastróficos, como descarrilamentos. Para evitar tais situações, os caminhos de ferro monitorizam o desempenho das rodas e substituem-nas de forma preventiva. O problema do negócio aqui é a previsão de falhas nas rodas.| A manutenção preditiva das rodas ajudará na substituição do tempo das rodas |
|_Falhas nas portas dos comboios do metro_: Uma das principais razões para os atrasos nas operações do metro são as falhas das portas dos vagões de comboio. O problema do negócio aqui é prever falhas nas portas dos comboios.|A consciência precoce de uma falha na porta, ou o número de dias até uma falha na porta, ajudará o negócio a otimizar os horários de manutenção da porta do comboio.|

A próxima secção entra nos detalhes de como realizar os benefícios do PDM discutidos acima.

## <a name="data-science-for-predictive-maintenance"></a>Ciência de Dados para manutenção preditiva

Esta secção fornece orientações gerais sobre princípios e práticas da ciência dos dados para o PdM. Destina-se a ajudar um TDM, um arquiteto de soluções ou um desenvolvedor a compreender os pré-requisitos e o processo de construção de aplicações de IA de ponta a ponta para pdM. Você pode ler esta secção juntamente com uma revisão das demos e modelos de prova de conceito listados em Modelos de [Solução para manutenção preditiva](#solution-templates-for-predictive-maintenance). Em seguida, pode utilizar estes princípios e as melhores práticas para implementar a sua solução PdM em Azure.

> [!NOTE]
> Este guia NÃO se destina a ensinar ao leitor Data Science. Várias fontes úteis são fornecidas para uma leitura mais aprofundada na secção de [recursos de formação para manutenção preditiva](#training-resources-for-predictive-maintenance). Os [modelos de solução listados](#solution-templates-for-predictive-maintenance) no guia demonstram algumas destas técnicas de IA para problemas específicos de PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Requisitos de dados para manutenção preditiva

O sucesso de qualquer aprendizagem depende da (a) qualidade do que está a ser ensinado, e (b) da capacidade do aluno. Os modelos preditivos aprendem padrões a partir de dados históricos, e prevêem resultados futuros com certas probabilidades com base nestes padrões observados. A precisão preditiva de um modelo depende da relevância, suficiência e qualidade dos dados de treino e teste. Os novos dados que são 'pontuados' utilizando este modelo devem ter as mesmas funcionalidades e esquemas que os dados de treino/teste. As características da característica (tipo, densidade, distribuição, e assim por diante) de novos dados devem corresponder às dos conjuntos de dados de treino e teste. O foco desta secção está nesses requisitos de dados.

### <a name="relevant-data"></a>Dados relevantes

Em primeiro lugar, os dados têm de ser _relevantes para o problema._ Considere o caso de utilização _da falha_ da roda discutido acima - os dados de treino devem conter características relacionadas com as operações das rodas. Se o problema era prever a falha do sistema de  _tração,_ os dados de formação têm de abranger todos os diferentes componentes para o sistema de tração. O primeiro caso visa um componente específico, enquanto o segundo caso visa a falha de um subsistema maior. A recomendação geral é a conceção de sistemas de previsão sobre componentes específicos e não subsistemas maiores, uma vez que estes últimos terão dados mais dispersos. O perito em domínios (ver [problemas de qualificação para manutenção preditiva)](#qualifying-problems-for-predictive-maintenance)deve ajudar na seleção dos subconjuntos de dados mais relevantes para a análise. As fontes de dados relevantes são discutidas mais detalhadamente na [preparação de dados para a manutenção preditiva.](#data-preparation-for-predictive-maintenance)

### <a name="sufficient-data"></a>Dados suficientes
São frequentemente feitas duas questões no que diz respeito aos dados históricos de falhas: (1) "Quantos eventos de falha são necessários para treinar um modelo?" (2) "Quantos registos são considerados "suficientes"? Não há respostas definitivas, mas apenas regras do polegar. Para (1), mais o número de eventos de falha, melhor o modelo. Para (2), e o número exato de eventos de falha depende dos dados e do contexto do problema que está a ser resolvido. Mas por outro lado, se uma máquina falhar com demasiada frequência, então o negócio irá substituí-la, o que reduzirá as situações de falha. Aqui, mais uma vez, a orientação do perito em domínios é importante. No entanto, existem métodos para lidar com a questão dos _acontecimentos raros._ São discutidos na secção [Handling dados desequilibrados](#handling-imbalanced-data).

### <a name="quality-data"></a>Dados de qualidade
A qualidade dos dados é fundamental - cada valor de atributo predileto deve ser _preciso_ em conjunto com o valor da variável-alvo. A qualidade dos dados é uma área bem estudada em estatísticas e gestão de dados, e, portanto, fora do âmbito deste guia.

> [!NOTE]
> Existem vários recursos e produtos empresariais para entregar dados de qualidade. A seguir é fornecida uma amostra de referências:
> - Dasu, T, Johnson, T., Exploratório Data Mining e Data Cleaning, Wiley, 2003.
> - [Análise de Dados Exploratórios, Wikipédia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Limpeza quantitativa de dados para grandes bases de dados](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Introdução à Limpeza de Dados com R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Preparação de dados para manutenção preditiva

### <a name="data-sources"></a>Origens de dados

As fontes de dados relevantes para a manutenção preditiva incluem, mas não se limitam a:
- História do fracasso
- Histórico de manutenção/reparação
- Condições de funcionamento da máquina
- Metadados de equipamento

#### <a name="failure-history"></a>História do fracasso
Os eventos de falha são raros nas aplicações PdM. No entanto, ao construir modelos de previsão, o algoritmo precisa de aprender sobre o padrão operacional normal de um componente, bem como os seus padrões de falha. Por conseguinte, os dados da formação devem conter um número suficiente de exemplos de ambas as categorias. Os registos de manutenção e o histórico de substituição de peças são boas fontes para encontrar eventos de falha. Com a ajuda de alguns conhecimentos de domínio, as anomalias nos dados de formação também podem ser definidas como falhas.

#### <a name="maintenancerepair-history"></a>Histórico de manutenção/reparação
O histórico de manutenção de um ativo contém detalhes sobre componentes substituídos, atividades de reparação realizadas, etc. Estes eventos registam padrões de degradação. A ausência desta informação crucial nos dados de formação pode levar a resultados enganadores do modelo. O histórico de falhas também pode ser encontrado dentro do histórico de manutenção como códigos de erro especiais, ou datas de encomenda para peças. Fontes de dados adicionais que influenciam padrões de falha devem ser investigadas e fornecidas por peritos de domínio.

#### <a name="machine-operating-conditions"></a>Condições de funcionamento da máquina
Os dados de streaming baseados em sensores (ou outros) do equipamento em funcionamento são uma importante fonte de dados. Um pressuposto chave no PdM é que o estado de saúde de uma máquina degrada-se ao longo do tempo durante o seu funcionamento de rotina. Espera-se que os dados contenham características que capturem este padrão de envelhecimento e quaisquer anomalias que conduzam à degradação. O aspeto temporal dos dados é necessário para que o algoritmo aprenda os padrões de falha e não-falha ao longo do tempo. Com base nestes pontos de dados, o algoritmo aprende a prever quantas mais unidades de tempo uma máquina pode continuar a funcionar antes de falhar.

#### <a name="static-feature-data"></a>Dados de recurso estático
As funcionalidades estáticas são metadados sobre o equipamento. Exemplos disso são a localização do equipamento, modelo, data manufaturada, data de início de serviço, localização do sistema e outras especificações técnicas.

Exemplos de dados relevantes para os [casos de utilização do PdM](#sample-pdm-use-cases) da amostra são tabulados abaixo:

| Caso de Utilização | Exemplos de dados relevantes |
|:---------|---------------------------|
|_Atraso de voo e cancelamentos_ | Informações sobre a rota de voo sob a forma de pernas de voo e registos de página. Os dados das pernas de voo incluem detalhes de encaminhamento, tais como data de partida/chegada, hora, aeroporto, escalas, etc. O registo de página inclui uma série de códigos de erro e manutenção registados pelo pessoal de manutenção no solo.|
|_Falha nas peças do motor do motor da aeronave_ | Dados recolhidos de sensores na aeronave que fornecem informações sobre o estado das várias partes. Os registos de manutenção ajudam a identificar quando ocorreram falhas nos componentes e quando foram substituídos.|
|_Falha no multibanco_ | Leituras de sensores para cada transação (depositando dinheiro/cheque) e distribuição de dinheiro. Informação sobre a medição de lacunas entre notas, espessura de nota, distância de chegada de nota, verificar atributos, etc. Registos de manutenção que fornecem códigos de erro, informações de reparação, da última vez que o distribuidor de caixa foi reabastecido.|
|_Falha na turbina eólica_ | Os sensores monitorizam as condições da turbina, tais como temperatura, direção do vento, energia gerada, velocidade do gerador, etc. Os dados são recolhidos a partir de várias turbinas eólicas de parques eólicos localizados em várias regiões. Normalmente, cada turbina terá múltiplas leituras de sensores transmitindo medições num intervalo de tempo fixo.|
|_Falhas nos disjuntores_ | Registos de manutenção que incluem ações corretivas, preventivas e sistemáticas. Dados operacionais que incluem comandos automáticos e manuais enviados para disjuntores, tais como para ações abertas e próximas. Metadados do dispositivo, tais como data de fabrico, localização, modelo, etc. Especificações do disjuntor tais como níveis de tensão, geolocalização, condições ambientais.|
|_Falhas na porta do elevador_| Metadados de elevador como tipo de elevador, data fabricada, frequência de manutenção, tipo de construção, e assim por diante. Informações operacionais como o número de ciclos de porta, tempo médio de fecho da porta. Histórico de falhanços com causas.|
|_Falhas nas rodas_ | Dados do sensor que medem a aceleração da roda, instâncias de travagem, distância de condução, velocidade, etc. Informações estáticas sobre rodas como fabricante, data fabricada. Dados de falha inferidos na base de dados de encomendas de peças que rastreiam datas e quantidades de encomenda.|
|_Falhas na porta do metro_ | Abertura de portas e horários de fecho, outros dados operacionais, como o estado atual das portas dos comboios. Os dados estáticos incluem o identificador de ativos, o tempo e as colunas de valor da condição.|

### <a name="data-types"></a>Tipos de dados
Tendo em conta as fontes de dados acima, os dois principais tipos de dados observados no domínio PdM são:

- _Dados temporais_: Telemetria operacional, condições da máquina, tipos de ordem de trabalho, códigos prioritários que terão datatamps no momento da gravação. Falha, manutenção/reparação e histórico de utilização também terão os tempos associados a cada evento.
- _Dados estáticos_: As características da máquina e as características do operador em geral são estáticas, uma vez que descrevem as especificações técnicas das máquinas ou atributos do operador. Se estas funcionalidades puderem mudar ao longo do tempo, também deverão ter relógios de tempo associados a elas.

As variáveis prediletas e-alvo devem ser pré-processadas/transformadas em [tipos de dados numéricos, categóricos e outros,](https://www.statsdirect.com/help/basics/measurement_scales.htm) dependendo do algoritmo que está a ser utilizado.

### <a name="data-preprocessing"></a>Pré-processamento de dados
Como pré-requisito para _apresentar engenharia,_ prepare os dados de vários fluxos para compor um esquema a partir do qual é fácil construir funcionalidades. Visualize os dados primeiro como uma tabela de registos. Cada linha da tabela representa um caso de formação, e as colunas representam características _preditivas_ (também chamadas de atributos ou variáveis independentes). Organizar os dados de modo a que a última coluna(s) seja o _alvo_ (variável dependente). Para cada instância de treino, atribua um _rótulo_ como valor desta coluna.

Para os dados temporais, divida a duração dos dados dos sensores em unidades temporais. Cada registo deve pertencer a uma unidade de tempo para um ativo, _e deve oferecer informações distintas_. As unidades de tempo são definidas com base nas necessidades do negócio em múltiplos de segundos, minutos, horas, dias, meses, e assim por diante. A unidade de tempo _não tem de ser a mesma que a frequência da recolha de dados._ Se a frequência for elevada, os dados podem não mostrar qualquer diferença significativa de uma unidade para a outra. Por exemplo, assuma que a temperatura ambiente foi recolhida a cada 10 segundos. A utilização desse mesmo intervalo para os dados de formação apenas inflaciona o número de exemplos sem fornecer qualquer informação adicional. Para este caso, uma melhor estratégia seria utilizar a média dos dados em 10 minutos, ou uma hora com base na justificação do negócio.

Para dados estáticos,
- _Registos de manutenção_: Os dados de manutenção bruta têm um identificador de ativos e um relógio de tempo com informações sobre as atividades de manutenção que foram realizadas num dado momento. Transforme as atividades de manutenção em colunas _categóricas,_ onde cada descritor de categorias mapeia exclusivamente para uma ação de manutenção específica. O esquema para registos de manutenção incluiria o identificador de ativos, o tempo e a ação de manutenção.

- _Registos de avarias_: As falhas ou razões de falha podem ser registadas como códigos de erro específicos ou eventos de falha definidos por condições de negócio específicas. Nos casos em que o equipamento tenha múltiplos códigos de erro, o perito em domínios deve ajudar a identificar os que são pertinentes à variável-alvo. Utilize os códigos ou condições de erro restantes para construir funcionalidades _preditores_ que se correlacionam com estas falhas. O esquema para registos de falhas incluiria o identificador de ativos, o tempo, a falha ou a razão de falha - se disponível.

- _Metadados_ de máquina e operador : Fundir os dados da máquina e do operador num só esquema para associar um ativo ao seu operador, juntamente com os respetivos atributos. O esquema para as condições da máquina incluiria identificador de ativos, características de ativos, identificador de operador e características do operador.

Outras etapas de pré-processamento de dados incluem o manuseamento de _valores em falta_ e a _normalização_ dos valores dos atributos. Uma discussão detalhada está fora do âmbito deste guia - consulte a secção seguinte para algumas referências úteis.

Com as fontes de dados pré-processadas acima em vigor, a transformação final antes da engenharia de recursos é juntar-se às tabelas acima com base no identificador de ativos e no timetamp. A tabela resultante teria valores nulos para a coluna de avaria quando a máquina estiver em funcionamento normal. Estes valores nulos podem ser imputados por um indicador para o funcionamento normal. Utilize esta coluna de falha para criar _etiquetas para o modelo preditivo_. Para obter mais informações, consulte a secção sobre [técnicas de modelação para manutenção preditiva.](#modeling-techniques-for-predictive-maintenance)

## <a name="feature-engineering"></a>Desenvolvimento de funcionalidades
A engenharia de recursos é o primeiro passo antes de modelar os dados. O seu papel no processo de ciência dos dados [é descrito aqui.](./create-features.md) Uma _característica_ é um atributo preditivo para o modelo - como temperatura, pressão, vibração, e assim por diante. Para o PdM, a engenharia de recursos envolve a abstração da saúde de uma máquina sobre dados históricos recolhidos ao longo de uma duração considerável. Nesse sentido, é diferente dos seus pares, tais como monitorização remota, deteção de anomalias e deteção de falhas. 

### <a name="time-windows"></a>Janelas do tempo
A monitorização remota implica reportar os eventos que ocorrem a partir de _pontos no tempo_. Os modelos de deteção de anomalias avaliam (pontuação) fluxos de dados para sinalizar anomalias a partir de pontos no tempo. A deteção de falhas classifica as falhas de serem de tipos específicos à medida que ocorrem pontos no tempo. Em contraste, o PdM envolve prever falhas durante um _período de tempo futuro_, com base em características que representam o comportamento da máquina durante o período _histórico_. No caso do PdM, os dados de funcionalidades de pontos de tempo individuais são demasiado barulhentos para serem preditivos. Assim, os dados para cada recurso precisam de ser _alisados_ agregando pontos de dados ao longo do tempo.

### <a name="lag-features"></a>Recursos de lag
Os requisitos do negócio definem até onde o modelo tem de prever para o futuro. Por sua vez, esta duração ajuda a definir "até onde o modelo tem de olhar" para fazer estas previsões. Este período de "olhar para trás" é chamado de _lag_, e as características projetadas ao longo deste período de lag são chamadas _características de lag_. Esta secção discute funcionalidades de lag que podem ser construídas a partir de fontes de dados com timetamps, e criação de recursos a partir de fontes de dados estáticas. As características do lag são tipicamente _numéricas_ por natureza.

> [!IMPORTANT]
> O tamanho da janela é determinado através de experimentação, e deve ser finalizado com a ajuda de um perito em domínio. A mesma ressalva mantém-se para a seleção e definição de características de lag, suas agregações, e o tipo de janelas.

#### <a name="rolling-aggregates"></a>Agregados rolantes
Para cada registo de um ativo, uma janela rolante de tamanho "W" é escolhida como o número de unidades de tempo para calcular os agregados. As funcionalidades de lag são então calculadas utilizando os períodos W antes da _data_ desse registo. Na Figura 1, as linhas azuis mostram os valores dos sensores registados para um ativo para cada unidade de tempo. Eles denotam uma média rolante de valores de características sobre uma janela de tamanho W=3. A média de rolos é calculada em todos os registos com timetamps na gama t<sub>1</sub> (em laranja) a t<sub>2</sub> (em verde). O valor para W é tipicamente em minutos ou horas dependendo da natureza dos dados. Mas para certos problemas, escolher um grande W (digamos 12 meses) pode fornecer toda a história de um ativo até ao momento do registo.

![Figura 1: Funcionalidades agregadas rolantes](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Figura 1: Funcionalidades agregadas rolantes

Exemplos de agregados rolantes ao longo de uma janela de tempo são as medidas contagens, médias, CUMESUM (soma cumulativa), valores min/max. Além disso, a variação, o desvio padrão e a contagem de outliers para além dos desvios padrão N são frequentemente usados. Exemplos de agregados que podem ser aplicados para os [casos de utilização](#sample-pdm-use-cases) neste guia estão listados abaixo. 
- _Atraso do voo_: contagem de códigos de erro durante o último dia/semana.
- _Falha da parte do motor do motor da aeronave:_ meios de rolamento, desvio padrão, e soma ao longo do dia passado, semana, etc. Esta métrica deve ser determinada juntamente com o perito em domínio de negócios.
- _Falhas no multibanco_: meios de rolamento, mediana, intervalo, desvios padrão, contagem de outliers para além de três desvios padrão, cumesum superior e inferior.
- _Falhas na porta_ do comboio do metro : Contagem de eventos durante o dia passado, semana, duas semanas, etc.
- _Falhas nos disjuntores_: Falhas na última semana, ano, três anos, etc.

Outra técnica útil no PdM é capturar mudanças de tendência, picos e mudanças de nível usando algoritmos que detetam anomalias nos dados.

#### <a name="tumbling-aggregates"></a>Agregados caindo
Para cada registo rotulado de um ativo, é definida uma janela de tamanho _<sub>W-k,</sub>_ onde _k_ é o número de janelas do tamanho _W_. Os agregados são então criados sobre _as janelas_ _k_ tumbling _W-k, W-<sub>(k-1)</sub>... ..., W-2,<sub></sub>W-1 <sub></sub>_ para os períodos antes do tempo de um recorde. _k_ pode ser um pequeno número para capturar efeitos a curto prazo, ou um grande número para capturar padrões de degradação a longo prazo. (ver Figura 2).

![Figura 2. Características agregadas de tumbling](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Figura 2. Características agregadas de tumbling

Por exemplo, as características de lag para a caixa de utilização de turbinas eólicas podem ser criadas com W=1 e k=3. Implicam o atraso para cada um dos últimos três meses usando os outliers superiores e inferiores.

### <a name="static-features"></a>Características estáticas

Especificações técnicas do equipamento, tais como data de fabrico, número de modelo, localização, são alguns exemplos de características estáticas. São tratados como variáveis _categóricas_ para modelação. Alguns exemplos para a caixa de utilização do disjuntor são tensão, corrente, capacidade de potência, tipo transformador e fonte de alimentação. Para as falhas das rodas, o tipo de rodas de pneus (liga vs aço) é um exemplo.

Os esforços de preparação de dados discutidos até agora devem levar à organização dos dados, tal como mostrado a seguir. Os dados de treino, teste e validação devem ter este esquema lógico (este exemplo mostra tempo em unidades de dias).

| ID de ativos | Hora | \<Feature Columns> | Etiqueta |
| ---- | ---- | --- | --- |
| A123 |Dia 1 | . . . | . |
| A123 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dia 1 | . . . | . |
| B234 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |

O último passo na engenharia de recursos é a **rotulagem** da variável-alvo. Este processo depende da técnica de modelação. Por sua vez, a técnica de modelação depende do problema de negócio e da natureza dos dados disponíveis. A rotulagem é discutida na secção seguinte.

> [!IMPORTANT]
> A preparação de dados e a engenharia de recursos são tão importantes como as técnicas de modelação para chegar a soluções PdM bem sucedidas. O perito em domínios e o praticante devem investir um tempo significativo na chegada às características e dados certos para o modelo. Uma pequena amostra de muitos livros sobre engenharia de recursos estão listadas abaixo:
> - Pyle, D. Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems), 1999
> - Zheng, A., Casari, A. Feature Engineering for Machine Learning: Principles and Techniques for Data Scientists, O'Reilly, 2018.
> - Dong, G. Liu, H. (Editores), Longa-Metragem de Engenharia para Machine Learning e Data Analytics (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Técnicas de modelação para manutenção preditiva

Esta secção discute as principais técnicas de modelação para problemas de PdM, juntamente com os seus métodos específicos de construção de etiquetas. Note que uma única técnica de modelação pode ser usada em diferentes indústrias. A técnica de modelação é emparelhada com o problema da ciência dos dados, em vez do contexto dos dados em questão.

> [!IMPORTANT]
> A escolha dos rótulos para os casos de insucesso e a estratégia de rotulagem  
> deve ser determinado em consulta com o perito em domínios.

### <a name="binary-classification"></a>Classificação binária
A classificação binária é usada para _prever a probabilidade de um equipamento falhar num período de tempo futuro_ - chamado período de horizonte futuro _X_. X é determinado pelo problema do negócio e pelos dados em questão, em consulta com o especialista em domínio. Eis alguns exemplos:
- _tempo mínimo_ de chumbo necessário para substituir componentes, implantar recursos de manutenção, efetuar a manutenção para evitar um problema que possa ocorrer nesse período.
- _contagem mínima de eventos_ que podem acontecer antes de um problema ocorrer.

Nesta técnica, são identificados dois tipos de exemplos de treino. Um exemplo positivo, _que indica uma falha,_ com etiqueta = 1. Um exemplo negativo, que indica operações normais, com etiqueta = 0. A variável-alvo, e daí os valores do rótulo, são _categóricos._ O modelo deve identificar cada novo exemplo como suscetível de falhar ou funcionar normalmente ao longo das próximas unidades x time.

#### <a name="label-construction-for-binary-classification"></a>Construção de etiquetas para classificação binária
A questão aqui é: "Qual é a probabilidade de o ativo falhar nas próximas unidades X do tempo?" Para responder a esta pergunta, a etiqueta X regista antes da falha de um ativo como "prestes a falhar" (etiqueta = 1), e rotula todos os outros registos como sendo "normais" (etiqueta =0). (ver Figura 3).

![Figura 3. Rotulagem para classificação binária](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Figura 3. Rotulagem para classificação binária

Exemplos de estratégia de rotulagem para alguns dos casos de utilização são listados abaixo.
- _Atrasos no voo_: X pode ser escolhido como um dia, para prever atrasos nas próximas 24 horas. Em seguida, todos os voos que estão dentro de 24 horas antes de falhas são rotulados como 1.
- _Falhas na distribuição de dinheiro multibanco_: Um objetivo pode ser determinar a probabilidade de falha de uma transação na próxima hora. Nesse caso, todas as transações que ocorreram na última hora da falha são rotuladas como 1. Para prever a probabilidade de falha sobre as próximas notas de moeda N dispensadas, todas as notas dispensadas nas últimas notas N de uma falha são rotuladas como 1.
- _Falhas no disjuntor_: O objetivo pode ser prever a próxima falha no comando do disjuntor. Nesse caso, X é escolhido para ser um futuro comando.
- _Falhas na porta do comboio_: X pode ser escolhido como dois dias.
- _Falhas na turbina eólica:_ X pode ser escolhido como dois meses.

### <a name="regression-for-predictive-maintenance"></a>Regressão para manutenção preditiva
Os modelos de regressão são utilizados para _calcular a vida útil restante (RUL) de um ativo_. A RUL é definida como a quantidade de tempo que um ativo está operacional antes da próxima falha ocorrer. Cada exemplo de treino é um registo que pertence a uma unidade de tempo _nY_ para um ativo, onde _n_ é o múltiplo. O modelo deve calcular o RUL de cada novo exemplo como um _número contínuo_. Este número denota o período de tempo que resta antes da falha.

#### <a name="label-construction-for-regression"></a>Construção de etiquetas para regressão
A questão aqui é: "Qual é a vida útil restante (RUL) do equipamento?" Para cada registo antes da falha, calcule o rótulo como o número de unidades de tempo restantes antes da próxima falha. Neste método, os rótulos são variáveis contínuas. (Ver Figura 4)

![Figura 4. Rotulagem para regressão](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Figura 4. Rotulagem para regressão

Para a regressão, a rotulagem é feita com referência a um ponto de falha. O seu cálculo não é possível sem saber quanto tempo o ativo sobreviveu antes de uma falha. Assim, em contraste com a classificação binária, os ativos sem falhas nos dados não podem ser utilizados para modelação. Esta questão é melhor abordada por outra técnica estatística chamada [Survival Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Mas podem surgir complicações potenciais ao aplicar esta técnica a casos de utilização de PdM que envolvam dados que variam o tempo com intervalos frequentes. Para obter mais informações sobre a Análise de Sobrevivência, consulte [este pager de um página.](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf)

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificação multi-classes para manutenção preditiva
Técnicas de classificação multi-classes podem ser usadas em soluções PdM para dois cenários:
- Prever _dois resultados futuros_: O primeiro resultado é um intervalo de tempo para _falhar_ um ativo. O ativo é atribuído a um dos vários períodos de tempo possíveis. O segundo resultado é a probabilidade de falhas num período futuro devido _a uma das múltiplas causas de raiz._ Esta previsão permite à equipa de manutenção observar os sintomas e planear os horários de manutenção.
- Prever _a causa mais provável_ de uma determinada falha. Este resultado recomenda o conjunto certo de ações de manutenção para corrigir uma falha. Uma lista classificada de causas de raiz e reparações recomendadas pode ajudar os técnicos a priorizar as suas ações de reparação após uma falha.

#### <a name="label-construction-for-multi-class-classification"></a>Construção de etiquetas para classificação multi-classes
A questão aqui é: "Qual é a probabilidade de um ativo falhar nas próximas unidades de _tempo nZ_ onde _n_ é o número de períodos?" Para responder a esta pergunta, marque os registos nZ antes da falha de um ativo utilizando baldes de tempo (3Z, 2Z, Z). Rotular todos os outros registos como "normais" (etiqueta = 0). Neste método, a variável-alvo contém valores _categóricos._ (Ver Figura 5).

![Figura 5. Etiquetas de previsão do tempo de falha para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Figura 5. Rotulagem para classificação multi-classes para previsão do tempo de insucesso

A questão aqui é: "Qual é a probabilidade de o ativo falhar nas próximas unidades X de tempo devido à causa raiz/problema _P <sub>i?"</sub>_ onde _i_ é o número de possíveis causas de raiz. Para responder a esta pergunta, a etiqueta X regista antes da falha de um ativo como "prestes a falhar devido à causa raiz _P <sub>i"</sub>_(etiqueta = _P <sub>i</sub>_). Rotular todos os outros registos como sendo "normais" (etiqueta = 0). Também neste método, as etiquetas são categóricas (Ver Figura 6).

![Figura 6. Etiquetas de previsão de causa raiz para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Figura 6. Rotulagem para classificação multi-classes para previsão de causa de raiz

O modelo atribui uma probabilidade de falha devido a cada _P <sub>i,</sub>_ bem como a probabilidade de não falhar. Estas probabilidades podem ser ordenadas por magnitude para permitir a previsão dos problemas que são mais prováveis de ocorrer no futuro.

A questão aqui é: "Que ações de manutenção recomendas depois de uma falha?" Para responder a esta pergunta, a rotulagem _não requer um horizonte futuro a ser escolhido_, porque o modelo não prevê o fracasso no futuro. É apenas prever a causa de raiz mais provável _uma vez que a falha já tenha acontecido_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Métodos de treino, validação e testes para manutenção preditiva
O [Processo de Ciência de Dados](./overview.md) de Equipa proporciona uma cobertura completa do ciclo de validação de testes de comboio. Esta secção discute aspetos exclusivos do PdM.

### <a name="cross-validation"></a>Validação cruzada
O objetivo da [validação cruzada](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) é definir um conjunto de dados para "testar" o modelo na fase de treino. Este conjunto de dados é chamado de _conjunto de validação_. Esta técnica ajuda a limitar problemas como _o excesso de adaptação_ e dá uma ideia de como o modelo irá generalizar-se a um conjunto de dados independente. Ou seja, um conjunto de dados desconhecido, que pode ser de um problema real. A rotina de treino e teste do PdM deve ter em conta o tempo que varia os aspetos para generalizar melhor os dados futuros invisíveis.

Muitos algoritmos de aprendizagem automática dependem de uma série de hiperparímetros que podem alterar significativamente o desempenho do modelo. Os valores ideais destes hiperparímetros não são calculados automaticamente ao treinar o modelo. Devem ser especificados pelo cientista de dados. Existem várias formas de encontrar bons valores de hiperparímetros.

O mais comum é _a validação cruzada k-fold_ que divide os exemplos aleatoriamente em dobras _k._ Para cada conjunto de valores de hiperparímetros, executar o algoritmo de aprendizagem _k_ vezes. Em cada iteração, use os exemplos na dobra atual como um conjunto de validação, e os restantes exemplos como um conjunto de treino. Treine o algoritmo sobre exemplos de treino e calcule as métricas de desempenho em vez de exemplos de validação. No final deste ciclo, calcule a média das métricas de desempenho _k._ Para cada conjunto de valores de hiperparímetro, escolha os que têm o melhor desempenho médio. A tarefa de escolher hiperparímetros é muitas vezes experimental na natureza.

Nos problemas de PdM, os dados são registados como uma série de eventos que vêm de várias fontes de dados. Estes registos podem ser encomendados de acordo com o momento da rotulagem. Assim, se o conjunto de dados for dividido _aleatoriamente_ em conjunto de treino e validação, _alguns dos exemplos de formação podem ser posteriores a tempo do que alguns dos exemplos de validação_. O desempenho futuro dos valores do hiperparímetro será estimado com base em alguns dados que chegaram _antes da_ formação do modelo. Estas estimativas podem ser demasiado otimistas, especialmente se as séries de tempo não estiverem estacionárias e evoluírem ao longo do tempo. Como resultado, os valores do hiperparmetro escolhido podem ser sub-ideais.

A forma recomendada é dividir os exemplos em formação e validação definidas de forma _dependente do tempo,_ onde todos os exemplos de validação são mais tarde do que todos os exemplos de treino. Para cada conjunto de valores de hiperparímetro, treine o algoritmo sobre o conjunto de dados de treino. Meça o desempenho do modelo sobre o mesmo conjunto de validação. Escolha valores de hiperparmetro que mostrem o melhor desempenho. Os valores do hiperparmetro escolhidos por divisão de comboio/validação resultam num melhor desempenho do modelo futuro do que com os valores escolhidos aleatoriamente por validação cruzada.

O modelo final pode ser gerado treinando um algoritmo de aprendizagem sobre todos os dados de treino usando os melhores valores de hiperparímetro.

### <a name="testing-for-model-performance"></a>Teste para desempenho do modelo
Uma vez construído um modelo, é necessária uma estimativa do seu desempenho futuro em novos dados. Uma boa estimativa é a métrica de desempenho dos valores do hiperparímetro calculados sobre o conjunto de validação, ou uma métrica média de desempenho calculada a partir da validação cruzada. Estas estimativas são muitas vezes demasiado otimistas. O negócio pode muitas vezes ter algumas diretrizes adicionais sobre como gostariam de testar o modelo.

A forma recomendada para o PdM é dividir os exemplos em conjuntos de dados de treino, validação e teste de forma _dependente do tempo._ Todos os exemplos de teste devem ser mais tarde do que todos os exemplos de treino e validação. Após a divisão, gere o modelo e mede o seu desempenho como descrito anteriormente.

Quando as séries de tempo são estacionárias e fáceis de prever, as abordagens aleatórias e dependentes do tempo geram estimativas semelhantes de desempenho futuro. Mas quando as séries temporizadas não são estacionárias e/ou difíceis de prever, a abordagem dependente do tempo gerará estimativas mais realistas do desempenho futuro.

### <a name="time-dependent-split"></a>Divisão dependente do tempo
Esta secção descreve as melhores práticas para implementar a divisão dependente do tempo. Abaixo é descrita uma divisão bidireccionís outra dependente do tempo entre os conjuntos de treino e de teste.

Assuma um fluxo de eventos com tempo, tais como medições de vários sensores. Defina características e etiquetas de exemplos de treino e teste ao longo de períodos de tempo que contenham múltiplos eventos. Por exemplo, para a classificação binária, crie funcionalidades baseadas em eventos passados e crie rótulos baseados em eventos futuros dentro de unidades de tempo "X" no futuro (ver as secções sobre técnicas de engenharia e modelação de [recursos).](#feature-engineering) Assim, o prazo de rotulagem de um exemplo vem mais tarde do que o prazo das suas características.

Para uma divisão dependente do tempo, escolha um _tempo de corte de treino T <sub>c</sub>_ para treinar um modelo, com hiperparímetros sintonizados usando dados históricos até T <sub>c</sub>. Para evitar fugas de futuras etiquetas que estão para além de T<sub>c</sub> nos dados de formação, escolha a hora mais recente para rotular exemplos de treino para serem unidades X antes de T<sub>c</sub>. No exemplo mostrado na Figura 7, cada quadrado representa um recorde no conjunto de dados onde as características e etiquetas são calculadas como descrito acima. O número mostra os registos que devem entrar em conjuntos de treino e teste para X=2 e W=3:

![Figura 7. Divisão dependente do tempo para classificação binária](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Figura 7. Divisão dependente do tempo para classificação binária

Os quadrados verdes representam registos pertencentes às unidades de tempo que podem ser usadas para o treino. Cada exemplo de formação é gerado considerando os últimos três períodos para a geração de recursos, e dois períodos futuros para a rotulagem antes de T<sub>c</sub>. Quando qualquer parte dos dois períodos futuros for além de T<sub>c,</sub>exclua esse exemplo do conjunto de dados de formação porque não se assume visibilidade para além de T<sub>c</sub>.

Os quadrados pretos representam os registos do conjunto final de dados rotulados que não devem ser utilizados no conjunto de dados de formação, dada a restrição acima referida. Estes registos também não serão utilizados em dados de teste, uma vez que são antes de T<sub>c</sub>. Além disso, os seus prazos de rotulagem dependem parcialmente do período de tempo de formação, o que não é o ideal. Os dados de formação e de ensaio devem ter prazos de rotulagem separados para evitar fugas de informação sobre etiquetas.

A técnica discutida até agora permite sobreposição entre exemplos de treino e teste que têm timetamps perto de T<sub>c</sub>. Uma solução para uma maior separação consiste em excluir exemplos que se encontram dentro das unidades de tempo W de T<sub>c</sub> do conjunto de ensaios. Mas tal divisão agressiva depende de uma ampla disponibilidade de dados.

Os modelos de regressão utilizados para prever a RUL são mais severamente afetados pelo problema das fugas. A utilização do método de divisão aleatória leva a uma sobreadesão extrema. Para problemas de regressão, a divisão deve ser tal que os registos pertencentes a ativos com falhas antes de T<sub>c</sub> entrarem no conjunto de treino. Registos de ativos que têm falhas após o corte vão para o conjunto de testes.

Outra das melhores práticas para dividir dados para treino e teste é usar uma divisão por ID de ativo. A cisão deve ser tal que nenhum dos ativos utilizados no conjunto de treino seja utilizado para testar o desempenho do modelo. Utilizando esta abordagem, um modelo tem mais hipóteses de fornecer resultados mais realistas com novos ativos.

### <a name="handling-imbalanced-data"></a>Tratamento de dados desequilibrados
Nos problemas de classificação, se houver mais exemplos de uma classe do que das outras, o conjunto de dados é dito ser _desequilibrado._ Idealmente, são preferidos representantes suficientes de cada classe nos dados de formação para permitir a diferenciação entre diferentes classes. Se uma classe for inferior a 10% dos dados, os dados são considerados desequilibrados. A classe sub-representada é chamada de _classe minoritária._

Muitos problemas de PDM enfrentam conjuntos de dados desequilibrados, onde uma classe está severamente sub-representada em comparação com a outra classe, ou classes. Em algumas situações, a classe minoritária pode constituir apenas 0,001% do total de pontos de dados. O desequilíbrio de classes não é exclusivo do PDM. Outros domínios onde falhas e anomalias são ocorrências raras enfrentam um problema semelhante, por exemplo, deteção de fraudes e intrusão de rede. Estes fracassos constituem os exemplos da classe minoritária.

Com o desequilíbrio de classe nos dados, o desempenho da maioria dos algoritmos de aprendizagem padrão está comprometido, uma vez que visam minimizar a taxa de erro geral. Para um conjunto de dados com exemplos 99% negativos e 1% positivos, um modelo pode ser mostrado ter 99% de precisão rotulando todos os casos como negativos. Mas o modelo classificará mal todos os exemplos positivos; então, mesmo que a sua precisão seja alta, o algoritmo não é útil. Consequentemente, as métricas de avaliação convencionais, tais como _a precisão global da taxa de erro,_ são insuficientes para uma aprendizagem desequilibrada. Quando confrontados com conjuntos de dados desequilibrados, outras métricas são utilizadas para a avaliação do modelo:
- Precisão
- Chamar de volta
- Resultados da F1
- ROC ajustado por custos (características de funcionamento do recetor)

Para obter mais informações sobre estas métricas, consulte [a avaliação do modelo.](#model-evaluation)

No entanto, existem alguns métodos que ajudam a resolver o problema do desequilíbrio de classes. As duas principais são técnicas de _amostragem_ e _aprendizagem sensível ao custo._

#### <a name="sampling-methods"></a>Métodos de amostragem
A aprendizagem desequilibrada envolve a utilização de métodos de amostragem para modificar o conjunto de dados de formação para um conjunto de dados equilibrado. Os métodos de amostragem não devem ser aplicados ao conjunto de ensaios. Embora existam várias técnicas de amostragem, a maioria das simples _esmumadoras são sobresmaluais aleatórias_ e _em amostragem._

_O oversampling aleatório_ envolve selecionar uma amostra aleatória da classe minoritária, replicando estes exemplos e adicionando-os ao conjunto de dados de treino. Consequentemente, o número de exemplos na classe minoritária é aumentado e, eventualmente, equilibra o número de exemplos de diferentes classes. Uma desvantagem da sobresmaldição é que múltiplas instâncias de certos exemplos podem fazer com que o classificador se torne demasiado específico, levando a uma sobreajustagem. O modelo pode apresentar uma elevada precisão de treino, mas o seu desempenho em dados de teste invisíveis pode ser subóptima.

Inversamente, _a amostragem aleatória_ está a selecionar uma amostra aleatória de uma classe maioritária e a remover esses exemplos do conjunto de dados de formação. No entanto, remover exemplos da classe maioritária pode fazer com que o classificador perca conceitos importantes relativos à classe maioritária. _A amostragem híbrida_ em que a classe minoritária é sobre-amostrada e a classe maioritária é sub-amostrada ao mesmo tempo é outra abordagem viável.

Existem muitas técnicas de amostragem sofisticadas. A técnica escolhida depende das propriedades de dados e dos resultados de experiências iterativas pelo cientista de dados.

#### <a name="cost-sensitive-learning"></a>Aprendizagem sensível a custos
No PDM, as falhas que constituem a classe minoritária são mais interessantes do que os exemplos normais. Assim, o foco está principalmente no desempenho do algoritmo em falhas. Prever incorretamente uma classe positiva como classe negativa pode custar mais do que vice-versa. Esta situação é geralmente referida como perda desigual ou custo assimétrico de elementos de classificação errada para diferentes classes. O classificador ideal deve fornecer uma alta precisão de previsão sobre a classe minoritária, sem comprometer a precisão para a classe maioritária.

Há várias formas de alcançar este equilíbrio. Para mitigar o problema da perda desigual, atribuir um custo elevado à classificação errada da classe minoritária e tentar minimizar o custo global. Algoritmos como _SVMs (Máquinas de Vetores de Suporte)_ adotam este método de forma inerente, permitindo que o custo de exemplos positivos e negativos seja especificado durante o treino. Da mesma forma, o reforço de métodos como _as árvores de decisão reforçadas_ geralmente mostram um bom desempenho com dados desequilibrados.

## <a name="model-evaluation"></a>Avaliação do modelo
A classificação errada é um problema significativo para os cenários de PdM em que o custo dos falsos alarmes para o negócio é elevado. Por exemplo, a decisão de aterrar uma aeronave com base numa previsão incorreta da falha do motor pode perturbar os horários e os planos de viagem. Tirar uma máquina offline de uma linha de montagem pode levar à perda de receitas. Assim, a avaliação do modelo com as métricas de desempenho certas contra novos dados de teste é fundamental.

As métricas típicas de desempenho utilizadas para avaliar os modelos PdM são discutidas abaixo:

- [A precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision) é a métrica mais popular usada para descrever o desempenho de um classificador. Mas a precisão é sensível às distribuições de dados, e é uma medida ineficaz para cenários com conjuntos de dados desequilibrados. Em vez disso, são utilizadas outras métricas. Ferramentas como [a matriz de confusão](https://en.wikipedia.org/wiki/Confusion_matrix) são usadas para calcular e raciocinar sobre a precisão do modelo.
- [A precisão](https://en.wikipedia.org/wiki/Precision_and_recall) dos modelos PdM diz respeito à taxa de falsos alarmes. A menor precisão do modelo geralmente corresponde a uma taxa mais elevada de falsos alarmes.
- [A](https://en.wikipedia.org/wiki/Precision_and_recall) taxa de recolha denota quantas falhas no conjunto de testes foram corretamente identificadas pelo modelo. Taxas de recordação mais altas significam que o modelo é bem sucedido na identificação das verdadeiras falhas.
- [A pontuação F1](https://en.wikipedia.org/wiki/F1_score) é a média harmónica de precisão e recordação, com o seu valor a variar entre 0 (pior) e 1 (melhor).

Para a classificação binária,
- [As curvas de funcionamento do recetor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) também são uma métrica popular. Nas curvas ROC, o desempenho do modelo é interpretado com base num ponto de funcionamento fixo no ROC.
- Mas para os problemas de PDM, _as tabelas de decile_ e _os gráficos de elevação_ são mais informativos. Focam-se apenas na classe positiva (falhas), e fornecem uma imagem mais complexa do desempenho do algoritmo do que as curvas ROC.
  - _As tabelas decile_ são criadas usando exemplos de teste numa ordem descendente de probabilidades de falha. As amostras ordenadas são então agrupadas em deciles (10% das amostras com maior probabilidade, depois 20%, 30%, e assim por diante). A relação (taxa positiva verdadeira)/(linha de base aleatória) para cada decile ajuda a estimar o desempenho do algoritmo em cada decile. A linha de base aleatória assume os valores 0.1, 0.2, e assim por diante.
  - [Os gráficos](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) de elevação traçam a taxa positiva dedisíssilo contra uma taxa positiva aleatória para todos os deciles. Os primeiros decis são geralmente o foco dos resultados, uma vez que mostram os maiores ganhos. Os primeiros deciles também podem ser vistos como representativos para "em risco", quando usados para pdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalização do modelo para manutenção preditiva

O benefício do exercício de ciência de dados só se concretiza quando o modelo treinado é tornado operacional. Ou seja, o modelo deve ser implementado nos sistemas empresariais para fazer previsões com base em novos dados, anteriormente invisíveis.  Os novos dados devem estar exatamente em conformidade com a assinatura do _modelo_ treinado de duas formas:
- todas as funcionalidades devem estar presentes em todos os casos lógicos (digamos uma linha numa tabela) dos novos dados.
- os novos dados devem ser pré-processados, e cada uma das funcionalidades concebidas, exatamente da mesma forma que os dados de formação.

O processo acima referido é referido de muitas formas na literatura académica e industrial. Mas todas as seguintes declarações significam a mesma coisa:
- _Marque novos dados_ usando o modelo
- _Aplicar o modelo_ a novos dados
- _Operacionalizar_ o modelo
- _Implementar_ o modelo
- _Executar o modelo_ contra novos dados

Como anteriormente afirmado, a operacionalização do modelo para pdM é diferente dos seus pares. Os cenários que envolvem deteção de anomalias e deteção de falhas normalmente _implementam pontuação online_ (também chamada _pontuação em tempo real)._ Aqui, o modelo _marca_ cada registo de entrada, e devolve uma previsão. Para a deteção de anomalias, a previsão é uma indicação de que ocorreu uma anomalia (Exemplo: SVM de classe única). Para deteção de avarias, seria o tipo ou classe de falha.

Em contrapartida, o PdM envolve _a pontuação do lote._ Para se conformar com a assinatura do modelo, as características dos novos dados devem ser concebidas da mesma forma que os dados de formação. Para os grandes conjuntos de dados que são típicos de novos dados, as funcionalidades são agregadas ao longo do tempo janelas e pontuadas em lote. A pontuação do lote é normalmente feita em sistemas distribuídos como [Spark](https://spark.apache.org/) ou [Azure Batch](../../batch/batch-service-workflow-features.md). Existem algumas alternativas - ambas sub-ideais:
- Os motores de dados de streaming suportam a agregação sobre as janelas na memória. Assim, pode argumentar-se que apoiam a pontuação online. Mas estes sistemas são adequados para dados densos em janelas estreitas de tempo, ou elementos escassos sobre janelas mais largas. Podem não escalar bem para os dados densos em janelas de tempo mais amplas, como visto em cenários PdM.
- Se a pontuação do lote não estiver disponível, a solução é adaptar a pontuação on-line para lidar com novos dados em pequenos lotes de cada vez.

## <a name="solution-templates-for-predictive-maintenance"></a>Modelos de solução para manutenção preditiva

A secção final deste guia fornece uma lista de modelos de solução PdM, tutoriais e experiências implementadas em Azure. Estas aplicações PdM podem ser implantadas numa subscrição do Azure em poucos minutos em alguns casos. Podem ser usados como demonstrações de prova de conceito, caixas de areia para experimentar alternativas, ou aceleradores para implementações reais de produção. Estes modelos estão localizados na [Galeria Azure AI](https://gallery.azure.ai) ou [no Azure GitHub.](https://github.com/Azure) Estas diferentes amostras serão enroladas neste modelo de solução ao longo do tempo.

| # | Título | Descrição |
|--:|:------|-------------|
| 2 | [Modelo de solução de manutenção preditiva Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Um modelo de solução de código aberto que demonstra modelação Azure ML e uma infraestrutura Azure completa capaz de suportar cenários de Manutenção Preditiva no contexto da monitorização remota IoT. |
| 3 | [Aprendizagem para Manutenção Preditiva](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Azure Notebook com uma solução de demonstração de utilização de redes LSTM (Long Short-Term Memory) (uma classe de Redes Neuronais Recorrentes) para Manutenção Preditiva, com um [post de blog nesta amostra.](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance)|
| 4 | [Manutenção Preditiva Azure para Aeroespacial](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Um dos primeiros modelos de solução PdM baseados no Azure ML v1.0 para manutenção de aeronaves. Este guia teve origem neste projeto. |
| 5 | [Kit de ferramentas Azure AI para IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | IA na Borda IoT utilizando o TensorFlow; toolkit embala modelos de aprendizagem profunda em recipientes Docker compatíveis com a Azure IoT Edge e expõe esses modelos como APIs REST.
| 6 | [Manutenção Preditiva Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS - Solução pré-configurada. Modelo PdM de manutenção de aeronaves com Suíte IoT. [Outro documento](/previous-versions/azure/iot-accelerators/about-iot-accelerators) e [passagem por cima](/previous-versions/azure/iot-accelerators/iot-accelerators-predictive-walkthrough) relacionado com o mesmo projeto. |
| 7 | [Modelo de manutenção preditiva usando serviços de R do servidor SQL](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstração do cenário de vida útil restante baseado em serviços R. |
| 8 | [Guia de Modelação de Manutenção Preditiva](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Recurso de conjunto de dados de manutenção de aeronaves projetado usando R com [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) e [conjuntos de dados](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) e caderno Azure e [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) em AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Recursos de formação para manutenção preditiva

O Microsoft Azure oferece caminhos de aprendizagem para os conceitos fundamentais por trás das técnicas PdM, além de conteúdo e formação em conceitos e práticas gerais de IA.

| Recurso de formação  | Disponibilidade |
|:-------------------|--------------|
| [Microsoft Docs: Função de cientista de dados](https://docs.microsoft.com/learn/roles/data-scientist) | Público |
| [Microsoft Docs: Papel de Engenheiro AI](https://docs.microsoft.com/learn/roles/ai-engineer) | Público |
| [Microsoft Docs: Função de Engenheiro de Dados](https://docs.microsoft.com/learn/roles/data-engineer) | Público |
| [Microsoft AI School](https://www.microsoft.com/ai/ai-school) | Público |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Público |
| [Microsoft: Playlists no YouTube para Inteligência Artificial e Analytics](https://www.youtube.com/c/MicrosoftAzure/playlists?view=50&sort=dd&shelf_id=7) | Público |
| [Microsoft AI Show](https://channel9.msdn.com/Shows/AI-Show) | Público |
| [Visão geral da plataforma AI](https://azure.microsoft.com/overview/ai-platform/) | Público |
| [Laboratório AI](https://www.microsoft.com/ai/ai-lab) | Público |
| [Microsoft AI](https://www.microsoft.com/AI) | Público |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Parceiros |

Além disso, moocs gratuitos (cursos online abertos massivos) em IA são oferecidos online por instituições académicas como Stanford e MIT, e outras empresas educativas.
