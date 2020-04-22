---
title: Guia da Azure AI para soluções de manutenção preditiva - Processo de Ciência de Dados da Equipa
description: Uma descrição abrangente da ciência dos dados que alimenta soluções de manutenção preditiva em várias indústrias verticais.
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
ms.openlocfilehash: 301e2be0c8b971a0236de6a8b5c8bd5f278c3aee
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686756"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Guia azure IA para soluções de manutenção preditiva

## <a name="summary"></a>Resumo

A manutenção preditiva **(PdM)** é uma aplicação popular de análises preditivas que podem ajudar as empresas de várias indústrias a alcançar uma elevada utilização de ativos e poupanças em custos operacionais. Este guia reúne as diretrizes empresariais e analíticas e as melhores práticas para desenvolver e implementar soluções PdM com sucesso utilizando a tecnologia da [plataforma Microsoft Azure AI.](https://azure.microsoft.com/overview/ai-platform)

Para começar, este guia introduz cenários de negócio específicos da indústria e o processo de qualificação destes cenários para a PdM. São também fornecidos os requisitos de dados e técnicas de modelação para a construção de soluções PdM. O conteúdo principal do guia está no processo de ciência de dados - incluindo os passos de preparação de dados, engenharia de recursos, criação de modelos e operacionalização de modelos. Para complementar estes conceitos-chave, este guia lista um conjunto de modelos de solução para ajudar a acelerar o desenvolvimento de aplicações PdM. O guia aponta ainda recursos de formação úteis para que o praticante saiba mais sobre a IA por detrás da ciência dos dados. 

### <a name="data-science-guide-overview-and-target-audience"></a>Visão geral do guia de ciência de dados e público-alvo
A primeira metade deste guia descreve problemas comerciais típicos, os benefícios da implementação do PdM para resolver estes problemas, e enumera alguns casos de uso comum. Os decisores empresariais (BDMs) beneficiarão deste conteúdo. A segunda metade explica a ciência dos dados por trás da PdM, e fornece uma lista de soluções PdM construídas usando os princípios descritos neste guia. Também fornece caminhos de aprendizagem e ponteiros para material de formação. Os decisores técnicos (TDMs) acharão este conteúdo útil.

| Comece com... | Se estiver... |
|:---------------|:---------------|
| [Caso de negócio para manutenção preditiva](#business-case-for-predictive-maintenance) |um decisor empresarial (BDM) que pretenda reduzir os custos de inatividade e operacionais e melhorar a utilização dos equipamentos |
| [Ciência dos Dados para manutenção preditiva](#data-science-for-predictive-maintenance) |um decisor técnico (TDM) que avalia as tecnologias PdM para compreender os requisitos únicos de processamento de dados e IA para manutenção preditiva |
| [Modelos de solução para manutenção preditiva](#solution-templates-for-predictive-maintenance)|um arquiteto de software ou Desenvolvedor de IA que procura levantar rapidamente uma demonstração ou uma prova de conceito |
| [Recursos de formação para manutenção preditiva](#training-resources-for-predictive-maintenance) | qualquer um ou todos os acima, e quer aprender os conceitos fundamentais por trás da ciência dos dados, ferramentas e técnicas.

### <a name="prerequisite-knowledge"></a>Conhecimento dos pré-requisitos
O conteúdo da BDM não espera que o leitor tenha qualquer conhecimento prévio em ciência de dados. Para o conteúdo da TDM, o conhecimento básico das estatísticas e da ciência dos dados é útil. Recomenda-se o conhecimento dos serviços de Dados Azure e IA, Python, R, XML e JSON. As técnicas de IA são implementadas em pacotes Python e R. Os modelos de solução são implementados utilizando serviços Azure, ferramentas de desenvolvimento e SDKs.

## <a name="business-case-for-predictive-maintenance"></a>Caso de negócio para manutenção preditiva

As empresas exigem que os equipamentos críticos estejam a funcionar no pico de eficiência e utilização para realizar o seu retorno sobre os investimentos de capital. Estes ativos podem ir desde motores de aeronaves, turbinas, elevadores ou refrigeradores industriais - que custam milhões - até aparelhos do dia-a-dia, como fotocopiadoras, máquinas de café ou refrigeradores de água.
- Por predefinição, a maioria das empresas depende da _manutenção corretiva,_ onde as peças são substituídas como e quando falham. A manutenção corretiva garante que as peças são completamente utilizadas (portanto, não desperdiçando a vida dos componentes), mas custa ao negócio em tempo de inatividade, mão de obra e requisitos de manutenção não programados (horas de folga ou locais inconvenientes).
- No nível seguinte, as empresas praticam _a manutenção preventiva,_ onde determinam o tempo útil para uma parte, e mantêm-na ou substituem-na antes de uma falha. A manutenção preventiva evita falhas não programadas e catastróficas. Mas os elevados custos do tempo de paragem programado, a subutilização do componente durante a sua vida útil, e o trabalho ainda permanecem.
- O objetivo da _manutenção preditiva_ é otimizar o equilíbrio entre a manutenção corretiva e preventiva, permitindo _apenas a tempo_ a substituição de componentes. Esta abordagem só substitui esses componentes quando estão perto de uma falha. Ao alargar o tempo de vida dos componentes (em comparação com a manutenção preventiva) e reduzir os custos não programados de manutenção e mão de obra (sobre a manutenção corretiva), as empresas podem obter poupanças de custos e vantagens competitivas.

## <a name="business-problems-in-pdm"></a>Problemas de negócio no PdM
As empresas enfrentam um elevado risco operacional devido a falhas inesperadas e têm uma visão limitada da causa principal dos problemas em sistemas complexos. Algumas das principais questões comerciais são:

- Detete anomalias no desempenho ou funcionalidade do equipamento ou do sistema.
- Preveja se um ativo pode falhar num futuro próximo.
- Estimar a vida útil restante de um ativo.
- Identifique as principais causas de falha de um ativo.
- Identifique quais as ações de manutenção que devem ser feitas, por quando, num ativo.

As declarações típicas de objetivos da PdM são:

- Reduzir o risco operacional de equipamento crítico da missão.
- Aumente a taxa de rentabilidade dos ativos prevendo falhas antes de ocorrerem.
- Controlar o custo da manutenção, permitindo operações de manutenção just-in-time.
- Menor atrito do cliente, melhorar a imagem da marca e perder vendas.
- Reduzir os custos de inventário reduzindo os níveis de inventário, prevendo o ponto de reencomenda.
- Descubra padrões ligados a vários problemas de manutenção.
- Fornecer KPL's (indicadores-chave de desempenho) tais como pontuações de saúde para as condições do ativo.
- Estimar o tempo de vida remanescente dos ativos.
- Recomende atividades de manutenção atempada.
- Ative o inventário a tempo, estimando as datas de encomenda para substituição de peças.

Estas declarações de golos são os pontos de partida para:

- _cientistas_ de dados para analisar e resolver problemas preditivos específicos.
- _arquitetos e desenvolvedores_ de nuvem para reunir um fim para a solução final.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Problemas de qualificação para manutenção preditiva
É importante salientar que nem todos os casos de utilização ou problemas de negócio podem ser efetivamente resolvidos pela PdM. Existem três critérios de qualificação importantes que devem ser considerados durante a seleção de problemas:

- O problema tem de ser preditivo na natureza; isto é, deve haver um alvo ou um resultado a prever. O problema deve também ter um caminho de ação claro para evitar falhas quando são detetadas.
- O problema deve ter um registo do histórico operacional do equipamento que contém _resultados bons e maus._ O conjunto de ações tomadas para mitigar os maus resultados deve também estar disponível como parte destes registos. Os relatórios de erro, os registos de manutenção da degradação do desempenho, reparação e substituição de registos também são importantes. Além disso, as reparações efetuadas para melhorá-las, e os registos de substituição também são úteis.
- O histórico registado deve refletir-se em dados _relevantes_ que sejam _suficientemente suficientes_ para suportar o caso de utilização. Para obter mais informações sobre relevância e suficiência de dados, consulte [os requisitos dos Dados para a manutenção preditiva](#data-requirements-for-predictive-maintenance).
- Finalmente, o negócio deve ter especialistas em domínios que tenham uma compreensão clara do problema. Devem estar cientes dos processos e práticas internas para poderem ajudar o analista a compreender e interpretar os dados. Devem também poder fazer as alterações necessárias aos processos comerciais existentes para ajudar a recolher os dados certos para os problemas, se necessário.

## <a name="sample-pdm-use-cases"></a>Casos de utilização de PdM da amostra
Esta secção centra-se numa coleção de casos de utilização de PdM de várias indústrias como Aeroespacial, Utilities e Transportes. Cada secção começa com um problema de negócio, e discute os benefícios da PdM, os dados relevantes em torno do problema de negócio, e finalmente os benefícios de uma solução PdM.

| Problema de negócios | Benefícios da PdM |
|:-----------------|-------------------|
|**Aviação**      |                   |
|Atraso de _voo e cancelamentos_ devido a problemas mecânicos. As falhas que não podem ser reparadas a tempo podem fazer com que os voos sejam cancelados e perturbar o agendamento e as operações. |As soluções PdM podem prever a probabilidade de uma aeronave ser atrasada ou cancelada devido a falhas mecânicas.|
|_Falha das peças_do motor dos aviões : As substituições da parte do motor dos aviões estão entre as tarefas de manutenção mais comuns no sector das companhias aéreas. As soluções de manutenção requerem uma gestão cuidadosa da disponibilidade, entrega e planeamento de stocks de componentes|Ser capaz de recolher informações sobre a fiabilidade dos componentes leva a uma redução substancial dos custos de investimento.|
|**Departamento Financeiro** |                         |
|_O insucesso do multibanco_ é um problema comum no sector bancário. O problema aqui é relatar a probabilidade de uma transação de levantamento de dinheiro multibanco ser interrompida devido a um engarrafamento de papel ou falha parcial no distribuidor de dinheiro. Com base nas previsões de falhas de transação, as multibanco podem ser serservidas proactivamente para evitar que ocorram falhas.| Em vez de permitir que a máquina falhe a meio de uma transação, a alternativa desejável é programar a máquina para negar o serviço com base na previsão.|
|**Energia** |                          |
|Falhas nas _turbinas eólicas_: As turbinas eólicas são a principal fonte de energia em países/regiões ambientalmente responsáveis e envolvem elevados custos de capital. Um componente chave nas turbinas eólicas é o motor gerador, cuja falha torna a turbina ineficaz. Também é muito caro de corrigir.|Prever KPIs como mTTF (tempo médio para falhar) pode ajudar as empresas de energia a prevenir falhas de turbinas e garantir o mínimo de tempo de inatividade. As probabilidades de falha informarão os técnicos para monitorizarem as turbinas que provavelmente falharão em breve e agendarão regimes de manutenção baseados no tempo. Os modelos preditivos fornecem insights sobre diferentes fatores que contribuem para a falha, o que ajuda os técnicos a entender melhor as causas fundamentais dos problemas.|
|Falhas de _disjuntores_: A distribuição de eletricidade para casas e empresas exige que as linhas de energia estejam sempre operacionais para garantir a entrega de energia. Os disjuntores ajudam a limitar ou evitar danos nas linhas elétricas durante a sobrecarga ou condições meteorológicas adversas. O problema do negócio aqui é prever falhas de disjuntores.| As soluções PdM ajudam a reduzir os custos de reparação e a aumentar o tempo de vida útil de equipamentos como disjuntores. Ajudam a melhorar a qualidade da rede de energia reduzindo falhas inesperadas e interrupções de serviço.|
|**Transporte e logística** |    |
|_Falhas_na porta do elevador : As grandes empresas de elevadores fornecem um serviço de pilha completa para milhões de elevadores funcionais em todo o mundo. A segurança dos elevadores, a fiabilidade e o tempo de funcionamento são as principais preocupações para os seus clientes. Estas empresas rastreiam estes e vários outros atributos através de sensores, para ajudá-las com a manutenção corretiva e preventiva. Num elevador, o problema mais proeminente do cliente é o mau funcionamento das portas do elevador. O problema do negócio neste caso é fornecer uma aplicação preditiva de base de conhecimento que preveja as potenciais causas de falhas nas portas.| Elevadores são investimentos de capital para uma vida útil potencial de 20 a 30 anos. Assim, cada venda potencial pode ser altamente competitiva; daí as expectativas de serviço e apoio são elevadas. A manutenção preditiva pode proporcionar a estas empresas uma vantagem sobre os seus concorrentes nas suas ofertas de produtos e serviços.|
|_Falhas_nas rodas : As falhas das rodas representam metade de todos os descarrilamentos dos comboios e custam milhares de milhões à indústria ferroviária global. As falhas nas rodas também fazem com que os carris se deteriorem, fazendo com que o carris se parta prematuramente. As quebras de caminhos de ferro levam a eventos catastróficos, como descarrilamentos. Para evitar tais casos, os caminhos de ferro monitorizam o desempenho das rodas e substituem-nas de forma preventiva. O problema do negócio aqui é a previsão de falhas nas rodas.| Manutenção preditiva das rodas ajudará na substituição just-in-time das rodas |
|_Falhas_nas portas do metro : Uma das principais razões para os atrasos nas operações do metro são as falhas nas portas dos vagões de comboio. O problema do negócio aqui é prever falhas nas portas dos comboios.|A consciência precoce de uma falha na porta, ou o número de dias até uma falha na porta, ajudará o negócio a otimizar os horários de manutenção da porta do comboio.|

A próxima secção entra nos detalhes de como perceber os benefícios do PdM acima discutidos.

## <a name="data-science-for-predictive-maintenance"></a>Ciência dos Dados para manutenção preditiva

Esta secção fornece orientações gerais sobre os princípios e práticas da ciência dos dados para a PdM. Destina-se a ajudar um TDM, um arquiteto de soluções ou um desenvolvedor a compreender os pré-requisitos e o processo de construção de aplicações de IA de ponta a ponta para pdM. Pode ler esta secção juntamente com uma revisão das demos e modelos de prova de conceito listados em Modelos de [Solução para manutenção preditiva](#solution-templates-for-predictive-maintenance). Em seguida, pode utilizar estes princípios e boas práticas para implementar a sua solução PdM em Azure.

> [!NOTE]
> Este guia NÃO se destina a ensinar ao leitor Data Science. São fornecidas várias fontes úteis para uma nova leitura na secção de recursos de [formação para manutenção preditiva.](#training-resources-for-predictive-maintenance) Os modelos de [solução](#solution-templates-for-predictive-maintenance) listados no guia demonstram algumas destas técnicas de IA para problemas específicos de PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Requisitos de dados para manutenção preditiva

O sucesso de qualquer aprendizagem depende de (a) a qualidade do que está a ser ensinado, e (b) a capacidade do aluno. Os modelos preditivos aprendem padrões a partir de dados históricos e preveem resultados futuros com determinada probabilidade com base nestes padrões observados. A precisão preditiva de um modelo depende da relevância, suficiência e qualidade dos dados de treino e teste. Os novos dados que são 'pontuados' utilizando este modelo devem ter as mesmas funcionalidades e esquema sinuoso que os dados de treino/teste. As características da característica (tipo, densidade, distribuição, e assim por diante) de novos dados devem corresponder às dos conjuntos de dados de treino e teste. O foco desta secção está nesses requisitos de dados.

### <a name="relevant-data"></a>Dados relevantes

Em primeiro lugar, os dados têm de ser _relevantes para o problema._ Considere o caso de utilização da _falha das rodas_ acima discutido - os dados de treino devem conter características relacionadas com as operações das rodas. Se o problema era prever a falha do sistema de _tração,_ os dados de treino têm de abranger todos os diferentes componentes para o sistema de tração. O primeiro caso visa um componente específico, enquanto que o segundo caso visa a falha de um subsistema maior. A recomendação geral é conceber sistemas de previsão sobre componentes específicos e não subsistemas maiores, uma vez que estes terão dados mais dispersos. O perito em domínios (ver [Problemas de qualificação para manutenção preditiva)](#qualifying-problems-for-predictive-maintenance)deve ajudar na seleção dos subconjuntos de dados mais relevantes para a análise. As fontes de dados relevantes são discutidas mais pormenorizadamente na preparação de [dados para manutenção preditiva.](#data-preparation-for-predictive-maintenance)

### <a name="sufficient-data"></a>Dados suficientes
São geralmente colocadas duas questões no que diz respeito aos dados do histórico de falhas: (1) "Quantos eventos de falha são necessários para treinar um modelo?" (2) "Quantos registos são considerados "suficientes"? Não há respostas definitivas, mas apenas regras de polegar. Para (1), mais o número de eventos de falha, melhor o modelo. Para (2), e o número exato de eventos de falha depende dos dados e do contexto do problema que está a ser resolvido. Mas, por outro lado, se uma máquina falhar demasiadas vezes, o negócio irá substituí-la, o que reduzirá os casos de falha. Aqui novamente, a orientação do especialista em domínios é importante. No entanto, existem métodos para lidar com a questão dos _acontecimentos raros._ São discutidos na secção [Manipulação de dados desequilibrados](#handling-imbalanced-data).

### <a name="quality-data"></a>Dados de qualidade
A qualidade dos dados é crítica - cada valor do atributo do preditor deve ser _exato_ em conjunto com o valor da variável-alvo. A qualidade dos dados é uma área bem estudada em estatística e gestão de dados, e, portanto, fora do âmbito deste guia.

> [!NOTE]
> Existem vários recursos e produtos empresariais para fornecer dados de qualidade. Uma amostra de referências é fornecida abaixo:
> - Dasu, T, Johnson, T., Exploratório data mining and Data Cleaning, Wiley, 2003.
> - [Análise de Dados Exploratórios, Wikipédia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Limpeza quantitativa de dados para grandes bases de dados](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Introdução à Limpeza de Dados com R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Preparação de dados para manutenção preditiva

### <a name="data-sources"></a>Origens de dados

As fontes de dados relevantes para a manutenção preditiva incluem, mas não se limitam a:
- Histórico de falhas
- Histórico de manutenção/reparação
- Condições de funcionamento da máquina
- Metadados de equipamentos

#### <a name="failure-history"></a>Histórico de falhas
Eventos de falha são raros nas aplicações pdM. No entanto, ao construir modelos de previsão, o algoritmo precisa de aprender sobre o padrão operacional normal de um componente, bem como os seus padrões de falha. Assim, os dados de formação devem conter um número suficiente de exemplos de ambas as categorias. Registos de manutenção e histórico de substituição de peças são boas fontes para encontrar eventos de falha. Com a ajuda de alguns conhecimentos de domínio, as anomalias nos dados de formação também podem ser definidas como falhas.

#### <a name="maintenancerepair-history"></a>Histórico de manutenção/reparação
O histórico de manutenção de um ativo contém detalhes sobre componentes substituídos, atividades de reparação realizadas, etc. Estes eventos registam padrões de degradação. A ausência desta informação crucial nos dados de formação pode levar a resultados de modelos enganosos. O histórico de falhas também pode ser encontrado no histórico de manutenção como códigos de erro especiais, ou datas de encomenda para peças. Fontes de dados adicionais que influenciam os padrões de falha devem ser investigadas e fornecidas por peritos em domínios.

#### <a name="machine-operating-conditions"></a>Condições de funcionamento da máquina
Os dados de streaming baseados no sensor (ou outros) do equipamento em funcionamento são uma importante fonte de dados. Uma suposição fundamental no PdM é que o estado de saúde de uma máquina se degrada ao longo do tempo durante o seu funcionamento de rotina. Espera-se que os dados contenham características que variam o tempo que capturam este padrão de envelhecimento, e quaisquer anomalias que conduzam à degradação. O aspeto temporal dos dados é necessário para que o algoritmo aprenda os padrões de falha e não falha ao longo do tempo. Com base nestes pontos de dados, o algoritmo aprende a prever quantas mais unidades de tempo uma máquina pode continuar a funcionar antes de falhar.

#### <a name="static-feature-data"></a>Dados de características estáticas
As características estáticas são metadados sobre o equipamento. Exemplos são a formação do equipamento, modelo, data manufaturada, data de início do serviço, localização do sistema e outras especificações técnicas.

Exemplos de dados relevantes para os casos de utilização do [PdM](#sample-pdm-use-cases) da amostra são tabulados abaixo:

| Caso de utilização | Exemplos de dados relevantes |
|:---------|---------------------------|
|_Atraso e cancelamentos de voo_ | Informações sobre a rota de voo sob a forma de pernas de voo e registos de página. Os dados da perna de voo incluem detalhes de encaminhamento, tais como data de partida/chegada, hora, aeroporto, paradções, etc. O registo de página inclui uma série de códigos de erro e manutenção registados pelo pessoal de manutenção do solo.|
|_Falha nas peças do motor dos aviões_ | Dados recolhidos a partir de sensores na aeronave que fornecem informações sobre o estado das várias peças. Os registos de manutenção ajudam a identificar quando ocorreram falhas nos componentes e quando foram substituídas.|
|_Falha no multibanco_ | Leituras de sensores para cada transação (depósito em dinheiro/cheque) e distribuição de dinheiro. Informação sobre a medição do intervalo entre notas, espessura de nota, distância de chegada de nota, verificar atributos etc. Registos de manutenção que fornecem códigos de erro, informações de reparação, da última vez que o distribuidor de dinheiro foi reabastecido.|
|_Falha na turbina eólica_ | Os sensores monitorizam as condições da turbina, tais como temperatura, direção do vento, energia gerada, velocidade do gerador, etc. Os dados são recolhidos a partir de várias turbinas eólicas de parques eólicos localizados em várias regiões. Normalmente, cada turbina terá múltiplas leituras de sensores que retransmitiam medições num intervalo de tempo fixo.|
|_Falhas no disjuntor_ | Registos de manutenção que incluem ações corretivas, preventivas e sistemáticas. Dados operacionais que incluem comandos automáticos e manuais enviados para disjuntores, tais como para ações abertas e próximas. Metadados do dispositivo, tais como data de fabrico, localização, modelo, etc. Especificações de disjuntores tais como níveis de tensão, geolocalização, condições ambientais.|
|_Falhas na porta do elevador_| Metadados de elevadores, tais como tipo de elevador, data fabricada, frequência de manutenção, tipo de construção, e assim por diante. Informação operacional, como número de ciclos de portas, tempo médio de fecho da porta. Histórico de falhanços com causas.|
|_Falhas nas rodas_ | Dados do sensor que medem a aceleração das rodas, as instâncias de travagem, a distância de condução, a velocidade, etc. Informações estáticas sobre rodas como fabricante, data fabricada. Dados de falha inferidos na base de dados de ordem parcial que rastreiam as datas e quantidades da encomenda.|
|_Falhas na porta do metro_ | Horários de abertura e fecho de portas, outros dados operacionais, como o estado atual das portas dos comboios. Os dados estáticos incluiriam as colunas de identificação de ativos, tempo e valor da condição.|

### <a name="data-types"></a>Tipos de dados
Dadas as fontes de dados acima referidas, os dois principais tipos de dados observados no domínio PdM são:

- _Dados temporais_: Telemetria operacional, condições da máquina, tipos de ordem de trabalho, códigos prioritários que terão carimbos de tempo no momento da gravação. Falha, manutenção/reparação e histórico de utilização também terão carimbos de tempo associados a cada evento.
- _Dados estáticos_: As características da máquina e as características do operador em geral são estáticas, uma vez que descrevem as especificações técnicas das máquinas ou dos atributos do operador. Se estas funcionalidades puderem mudar ao longo do tempo, também devem ter selos temporais associados a elas.

As variáveis do preditor e do alvo devem ser pré-processadas/transformadas em [tipos numéricos, categóricos e outros tipos](https://www.statsdirect.com/help/basics/measurement_scales.htm) de dados dependendo da utilizada no algoritmo.

### <a name="data-preprocessing"></a>Pré-processamento de dados
Como pré-requisito para _a engenharia de recursos,_ prepare os dados de vários fluxos para compor um esquema do qual é fácil construir funcionalidades. Visualizar os dados primeiro como uma tabela de registos. Cada linha na tabela representa uma instância de formação, e as colunas representam características _do preditor_ (também chamadas atributos independentes ou variáveis). Organize os dados de modo a que a última coluna(s) seja o _alvo_ (variável dependente). Para cada instância de treino, atribua um _rótulo_ como valor desta coluna.

Para dados temporais, divida a duração dos dados do sensor em unidades temporais. Cada registo deve pertencer a uma unidade de tempo para um ativo, _e deve oferecer informações distintas_. As unidades de tempo são definidas com base nas necessidades do negócio em múltiplos de segundos, minutos, horas, dias, meses, e assim por diante. A unidade de tempo não tem de ser a mesma que _a frequência de recolha de dados_. Se a frequência for elevada, os dados podem não mostrar qualquer diferença significativa de uma unidade para outra. Por exemplo, suponha que a temperatura ambiente foi recolhida a cada 10 segundos. A utilização desse mesmo intervalo para os dados de formação apenas infla o número de exemplos sem fornecer qualquer informação adicional. Para este caso, uma melhor estratégia seria utilizar a média dos dados em mais de 10 minutos, ou uma hora com base na justificação do negócio.

Para dados estáticos,
- _Registos de manutenção_: Os dados de manutenção bruta têm um identificador de ativos e um carimbo de tempo com informações sobre as atividades de manutenção que tenham sido realizadas num dado momento. Transforme as atividades de manutenção em colunas _categóricas,_ onde cada descritor de categoria supor exclusivamente mapas para uma ação de manutenção específica. O esquema para registos de manutenção incluiria o identificador de ativos, o tempo e a ação de manutenção.

- _Registos de falhas_: Falhas ou razões de falha podem ser registadas como códigos de erro específicos ou eventos de falha definidos por condições de negócio específicas. Nos casos em que o equipamento tem vários códigos de erro, o especialista em domínios deve ajudar a identificar os que são pertinentes à variável-alvo. Utilize os restantes códigos de erro ou condições para construir características _do preditor_ que se relacionam com estas falhas. O esquema para registos de falhas incluiria o identificador de ativos, o tempo, a falha ou a razão de falha - se disponível.

- _Metadados_da máquina e do operador: Fundir os dados da máquina e do operador num único esquema para associar um ativo ao seu operador, juntamente com os respetivos atributos. O esquema para as condições da máquina incluiria o identificador de ativos, as características do ativo, o identificador do operador e as características do operador.

Outras etapas de pré-processamento de dados incluem o manuseamento de _valores em falta_ e a _normalização_ dos valores dos atributos. Uma discussão detalhada está fora do âmbito deste guia - consulte a secção seguinte para obter algumas referências úteis.

Com as fontes de dados pré-processadas acima existentes, a transformação final antes da engenharia de recursos é juntar as tabelas acima com base no identificador de ativos e no carimbo temporal. A tabela resultante teria valores nulos para a coluna de avaria quando a máquina estiver em funcionamento normal. Estes valores nulos podem ser imputados por um indicador para o funcionamento normal. Utilize esta coluna de falha para criar _etiquetas para o modelo preditivo_. Para mais informações, consulte a secção sobre [técnicas de modelação para manutenção preditiva](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Com engenharia
A engenharia de funcionalidades é o primeiro passo antes da modelação dos dados. O seu papel no processo de ciência dos dados [é descrito aqui.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features) Uma _característica_ é um atributo preditivo para o modelo - como temperatura, pressão, vibração, e assim por diante. Para a PdM, a engenharia de recursos envolve abstrair a saúde de uma máquina sobre dados históricos recolhidos ao longo de uma duração considerável. Nesse sentido, é diferente dos seus pares, tais como monitorização remota, deteção de anomalias e deteção de falhas. 

### <a name="time-windows"></a>Janelas do tempo
A monitorização remota implica relatar os eventos que ocorrem a partir de _pontos no tempo_. Os modelos de deteção de anomalias avaliam (pontuação) fluxos de dados para sinalizar anomalias a partir de pontos no tempo. A deteção de falhas classifica falhas de tipos específicos à medida que ocorrem pontos no tempo. Em contraste, o PdM envolve prever falhas durante um _período de tempo futuro_, com base em características que representam o comportamento da máquina durante o período _histórico._ Para o PdM, os dados de recursos de momentos individuais são demasiado ruidosos para serem preditivos. Assim, os dados de cada funcionalidade precisam de ser _suavizados_ agregando pontos de dados ao longo das janelas de tempo.

### <a name="lag-features"></a>Funcionalidades de lag
Os requisitos de negócio definem até que ponto o modelo tem de prever para o futuro. Por sua vez, esta duração ajuda a definir "até onde o modelo tem de olhar" para fazer estas previsões. Este período de "olhar para trás" chama-se _lag_, e as características concebidas durante este período de atraso são chamadas _de funcionalidades_de lag . Esta secção discute funcionalidades de lag que podem ser construídas a partir de fontes de dados com selos temporais, e a criação de recursos a partir de fontes de dados estáticas. As características do lag são tipicamente _numéricas_ na natureza.

> [!IMPORTANT]
> O tamanho da janela é determinado através de experimentação, e deve ser finalizado com a ajuda de um perito em domínio. A mesma ressalva mantém-se para a seleção e definição de funcionalidades de lag, as suas agregações e o tipo de janelas.

#### <a name="rolling-aggregates"></a>Agregados rolantes
Para cada registo de um ativo, uma janela rolante de tamanho "W" é escolhida como o número de unidades de tempo para calcular os agregados. As características de lag são então calculadas utilizando os períodos W _antes da data_ desse registo. Na Figura 1, as linhas azuis mostram valores de sensor registados para um ativo para cada unidade de tempo. Eles denotam uma média de rolamento de valores de características sobre uma janela de tamanho W=3. A média de rolamento é calculada em todos os registos com carimbos de tempo na gama t<sub>1</sub> (em laranja) a t<sub>2</sub> (em verde). O valor para W é tipicamente em minutos ou horas dependendo da natureza dos dados. Mas para certos problemas, escolher um Grande W (digamos 12 meses) pode fornecer toda a história de um ativo até ao momento do registo.

![Figura 1: Características agregados rolantes](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Figura 1: Características agregados rolantes

Exemplos de agregados rolantes ao longo de uma janela de tempo são contados, medidas médias, medidas CUMESUM (soma cumulativa), valores min/max. Além disso, a variância, o desvio padrão e a contagem de outliers para além dos desvios padrão N são frequentemente usados. Exemplos de agregados que podem ser aplicados para os casos de [utilização](#sample-pdm-use-cases) neste guia são listados abaixo. 
- _Atraso_de voo : contagem de códigos de erro durante o último dia/semana.
- _Falha da parte do motor_do motor da aeronave : meios de rolamento, desvio padrão e soma ao longo do último dia, semana, etc. Esta métrica deve ser determinada juntamente com o especialista em domínio empresarial.
- _Falhas no multibanco_: meios de rolamento, mediano, alcance, desvios padrão, contagem de outliers para além de três desvios padrão, CUMESUM superior e inferior.
- _Falhas_na porta do metro : Contagem de eventos ao longo do passado dia, semana, duas semanas, etc.
- Falhas no _disjuntor:_ Falha conta na semana passada, ano, três anos, etc.

Outra técnica útil no PdM é capturar mudanças de tendência, picos e mudanças de nível usando algoritmos que detetam anomalias nos dados.

#### <a name="tumbling-aggregates"></a>Agregados caindo
Para cada registo rotulado de um ativo, define-se uma janela de tamanho _<sub>W-k,</sub> _ onde _k_ é o número de janelas do tamanho _W_. Os agregados são então criados sobre _k_ _tumbling windows_ _W-k, W-<sub>(k-1)</sub>, ..., W-2 ,<sub>2</sub>W-1<sub>1</sub> _ para os períodos antes do carimbo temporal de um recorde. _k_ pode ser um pequeno número para capturar efeitos a curto prazo, ou um grande número para capturar padrões de degradação a longo prazo. (ver Figura 2).

![Figura 2. Características agregadas caindo](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Figura 2. Características agregadas caindo

Por exemplo, as características de lag para a caixa de utilização de turbinas eólicas podem ser criadas com W=1 e k=3. Implicam o atraso de cada um dos últimos três meses usando os outliers superior escancarados.

### <a name="static-features"></a>Características estáticas

Especificações técnicas dos equipamentos, tais como data de fabrico, número de modelo, localização, são alguns exemplos de características estáticas. São tratados como variáveis _categóricas_ para modelação. Alguns exemplos para a caixa de utilização do disjuntor são a tensão, a corrente, a capacidade de potência, o tipo de transformador e a fonte de alimentação. Para falhas nas rodas, o tipo de rodas de pneus (liga vs aço) é um exemplo.

Os esforços de preparação de dados discutidos até agora devem levar à organização dos dados, tal como mostrado abaixo. Os dados de formação, teste e validação devem ter este esquema lógico (este exemplo mostra o tempo em unidades de dias).

| ID do ativo | Hora | \<Colunas de recurso> | Etiqueta |
| ---- | ---- | --- | --- |
| A123 |Dia 1 | . . . | . |
| A123 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dia 1 | . . . | . |
| B234 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |

O último passo na engenharia de recursos é a **rotulagem** da variável-alvo. Este processo depende da técnica de modelação. Por sua vez, a técnica de modelação depende do problema de negócio e da natureza dos dados disponíveis. A rotulagem é discutida na próxima secção.

> [!IMPORTANT]
> A preparação de dados e a engenharia de recursos são tão importantes como técnicas de modelação para chegar a soluções PdM bem sucedidas. O especialista em domínios e o praticante devem investir tempo significativo na chegada às características e dados certos para o modelo. Uma pequena amostra de muitos livros sobre engenharia de recursos estão listadas abaixo:
> - Pyle, D. Data Preparation for Data Mining (The Morgan Kaufmann Series in Data Management Systems), 1999
> - Zheng, A., Casari, A. Engenharia de Recursos para Aprendizagem automática: Princípios e Técnicas para Cientistas de Dados, O'Reilly, 2018.
> - Dong, G. Liu, H. (Editores), Engenharia de Recursos para Machine Learning e Análise de Dados (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Técnicas de modelação para manutenção preditiva

Esta secção discute as principais técnicas de modelação para problemas de PdM, juntamente com os seus métodos específicos de construção de rótulos. Note que uma única técnica de modelação pode ser usada em diferentes indústrias. A técnica de modelação é emparelhada com o problema da ciência dos dados, em vez do contexto dos dados em questão.

> [!IMPORTANT]
> A escolha dos rótulos para os casos de insucesso e a estratégia de rotulagem  
> deve ser determinado em consulta com o perito em domínios.

### <a name="binary-classification"></a>Classificação binária
A classificação binária é usada para _prever a probabilidade de um equipamento falhar num período de tempo futuro_ - chamado período horizonte futuro _X_. X é determinado pelo problema do negócio e pelos dados em questão, em consulta com o especialista em domínio. Os exemplos são:
- _tempo mínimo_ de acesso necessário para substituir componentes, implantar recursos de manutenção, realizar manutenção para evitar um problema que é provável que ocorra nesse período.
- _contagem mínima de eventos_ que podem acontecer antes que um problema ocorra.

Nesta técnica, são identificados dois tipos de exemplos de treino. Um exemplo positivo, _que indica uma falha,_ com etiqueta = 1. Um exemplo negativo, que indica operações normais, com etiqueta = 0. A variável-alvo, e daí os valores do rótulo, são _categóricas._ O modelo deve identificar cada novo exemplo como suscetível de falhar ou funcionar normalmente durante as próximas unidades de tempo X.

#### <a name="label-construction-for-binary-classification"></a>Construção de etiquetas para classificação binária
A questão aqui é: "Qual é a probabilidade de o ativo falhar nas próximas unidades X do tempo?" Para responder a esta pergunta, rotule os registos X antes da falha de um ativo como "prestes a falhar" (etiqueta = 1), e rotule todos os outros registos como sendo "normais" (etiqueta =0). (ver Figura 3).

![Figura 3. Rotulagem para classificação binária](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Figura 3. Rotulagem para classificação binária

Exemplos de estratégia de rotulagem para alguns dos casos de utilização são listados abaixo.
- _Atrasos_no voo : X pode ser escolhido como um dia, para prever atrasos nas próximas 24 horas. Em seguida, todos os voos que estão dentro de 24 horas antes de as falhas serem rotulados como 1.
- Falhas de dispensa de _caixa multibanco_: Um objetivo pode ser determinar a probabilidade de falha de uma transação na próxima hora. Nesse caso, todas as transações que ocorreram na última hora da falha são rotuladas como 1. Para prever a probabilidade de falha nas próximas notas de moeda N dispensadas, todas as notas dispensadas nas últimas notas n de uma falha são rotuladas como 1.
- _Falhas no disjuntor_: O objetivo pode ser prever a próxima falha do comando do disjuntor. Nesse caso, X é escolhido para ser um futuro comando.
- _Falhas na porta do comboio:_ X pode ser escolhido como dois dias.
- _Falhas_na turbina eólica : X pode ser escolhido como dois meses.

### <a name="regression-for-predictive-maintenance"></a>Regressão para manutenção preditiva
Os modelos de regressão são usados para _calcular a vida útil restante (RUL) de um ativo_. O RUL é definido como a quantidade de tempo em que um ativo está operacional antes da próxima falha ocorrer. Cada exemplo de treino é um registo que pertence a uma unidade de tempo _nY_ para um ativo, onde _n_ é o múltiplo. O modelo deve calcular o RUL de cada novo exemplo como um _número contínuo_. Este número denota o período de tempo remanescente antes da falha.

#### <a name="label-construction-for-regression"></a>Construção de etiquetas para regressão
A questão aqui é: "Qual é a vida útil restante (RUL) do equipamento?" Para cada registo anterior à falha, calcule a etiqueta como o número de unidades de tempo restantes antes da próxima falha. Neste método, os rótulos são variáveis contínuas. (Ver Figura 4)

![Figura 4. Rotulagem para regressão](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Figura 4. Rotulagem para regressão

Para a regressão, a rotulagem é feita com referência a um ponto de falha. O seu cálculo não é possível sem saber quanto tempo o ativo sobreviveu antes de uma falha. Assim, em contraste com a classificação binária, os ativos sem quaisquer falhas nos dados não podem ser utilizados para modelação. Esta questão é melhor abordada por outra técnica estatística chamada [Survival Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Mas podem surgir complicações potenciais ao aplicar esta técnica ao Uso de PdM casos que envolvem dados que variam o tempo com intervalos frequentes. Para obter mais informações sobre análise de sobrevivência, consulte [este pager de uma página](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificação multi-classe para manutenção preditiva
As técnicas de classificação de várias classes podem ser utilizadas em soluções PdM para dois cenários:
- Prever _dois resultados futuros_: O primeiro resultado é um intervalo de tempo para _falhar_ num ativo. O ativo é atribuído a um dos múltiplos períodos de tempo possíveis. O segundo resultado é a probabilidade de falhar num período futuro devido a _uma das múltiplas causas._ Esta previsão permite que a equipa de manutenção tenha cuidado com os sintomas e os horários de manutenção do plano.
- Preveja _a causa mais provável_ de uma dada falha. Este resultado recomenda o conjunto certo de ações de manutenção para corrigir uma falha. Uma lista classificada de causas de raiz e reparações recomendadas pode ajudar os técnicos a priorizar as suas ações de reparação após uma falha.

#### <a name="label-construction-for-multi-class-classification"></a>Construção de etiquetas para classificação multi-classe
A questão aqui é: "Qual é a probabilidade de um ativo falhar nas próximas unidades de tempo _nZ_ onde _n_ é o número de períodos?" Para responder a esta pergunta, rotule os registos nZ antes da falha de um ativo usando baldes de tempo (3Z, 2Z, Z). Rotular todos os outros registos como "normais" (etiqueta = 0). Neste método, a variável-alvo detém valores _categóricos._ (Ver Figura 5).

![Figura 5. Etiquetas de previsão do tempo de insucesso para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Figura 5. Rotulagem para classificação multi-classe para previsão do tempo de insucesso

A questão aqui é: "Qual é a probabilidade de o ativo falhar nas próximas unidades X de tempo devido à causa raiz/problema _P<sub>i?"</sub>_ onde _eu_ é o número de possíveis causas de raiz. Para responder a esta pergunta, rotule os registos X antes da falha de um ativo como "prestes a falhar devido à causa raiz _P<sub>i"</sub>_(etiqueta = _P<sub>i</sub>_). Rotular todos os outros registos como sendo "normais" (etiqueta = 0). Também neste método, os rótulos são categóricos (Ver Figura 6).

![Figura 6. Rótulos de previsão de causa seleção para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Figura 6. Rotulagem para classificação multi-classe para previsão de causas de raiz

O modelo atribui uma probabilidade de falha devido a cada _P<sub>i,</sub> _ bem como a probabilidade de não falha. Estas probabilidades podem ser ordenadas por magnitude para permitir a previsão dos problemas que são mais prováveis de ocorrer no futuro.

A questão aqui é: "Que ações de manutenção recomenda depois de uma falha?" Para responder a esta pergunta, a rotulagem _não requer que seja escolhido um horizonte futuro_, porque o modelo não prevê falhas no futuro. Está apenas a prever a causa mais provável _quando a falha já aconteceu._

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Métodos de formação, validação e teste para manutenção preditiva
O Processo de Ciência de Dados da [Equipa](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) proporciona uma cobertura completa do ciclo modelo de validação de testes de comboio. Esta secção discute aspetos exclusivos da PdM.

### <a name="cross-validation"></a>Validação cruzada
O objetivo da [validação cruzada](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) é definir um conjunto de dados para "testar" o modelo na fase de treino. Este conjunto de dados é chamado de conjunto de _validação_. Esta técnica ajuda a limitar problemas como o excesso de _encaixe_ e dá uma ideia de como o modelo se generalizará para um conjunto de dados independente. Ou seja, um conjunto de dados desconhecido, que pode ser de um problema real. A rotina de formação e teste para a PdM deve ter em conta os aspetos variáveis do tempo para melhor generalizar em dados futuros invisíveis.

Muitos algoritmos de aprendizagem automática dependem de uma série de hiperparâmetros que podem alterar significativamente o desempenho do modelo. Os valores ideais destes hiperparâmetros não são calculados automaticamente quando treinam o modelo. Devem ser especificados pelo cientista de dados. Existem várias formas de encontrar bons valores de hiperparâmetros.

A mais comum é a _validação cruzada k-fold_ que divide os exemplos aleatoriamente em _dobras k._ Para cada conjunto de valores de hiperparâmetros, executar o algoritmo de aprendizagem _k_ vezes. Em cada iteração, use os exemplos na dobra atual como um conjunto de validação, e o resto dos exemplos como um conjunto de formação. Treine o algoritmo sobre exemplos de treino e calcule as métricas de desempenho em exemplos de validação. No final deste ciclo, calcule a média das métricas de desempenho _k._ Para cada conjunto de valores hiperparâmetros, escolha os que têm o melhor desempenho médio. A tarefa de escolher hiperparâmetros é muitas vezes experimental na natureza.

Nos problemas de PdM, os dados são registados como uma série de eventos que vêm de várias fontes de dados. Estes registos podem ser encomendados de acordo com o momento da rotulagem. Assim, se o conjunto de dados for dividido _aleatoriamente_ em conjunto de formação e validação, _alguns dos exemplos de formação podem ser posteriores no tempo do que alguns exemplos de validação._ O desempenho futuro dos valores do hiperparâmetro será estimado com base em alguns dados que chegaram _antes_ do modelo ser treinado. Estas estimativas podem ser demasiado otimistas, especialmente se a série de tempo não for estacionária e evoluir ao longo do tempo. Como resultado, os valores do hiperparâmetro escolhido podem ser sub-ideais.

A forma recomendada é dividir os exemplos em formação e validação definidos de forma _dependente do tempo,_ onde todos os exemplos de validação são posteriores no tempo do que todos os exemplos de formação. Para cada conjunto de valores de hiperparâmetros, treine o algoritmo sobre o conjunto de dados de treino. Meça o desempenho do modelo no mesmo conjunto de validação. Escolha valores hiperparâmetros que mostrem o melhor desempenho. Os valores hiperparâmetros escolhidos por divisão de comboio/validação resultam num melhor desempenho futuro do modelo do que com os valores escolhidos aleatoriamente por validação cruzada.

O modelo final pode ser gerado treinando um algoritmo de aprendizagem através de dados de treino inteiros usando os melhores valores de hiperparâmetro.

### <a name="testing-for-model-performance"></a>Teste para desempenho do modelo
Uma vez construído um modelo, é necessária uma estimativa do seu desempenho futuro em novos dados. Uma boa estimativa é a métrica de desempenho dos valores hiperparâmetros calculados sobre o conjunto de validação, ou uma métrica de desempenho média calculada a partir da validação cruzada. Estas estimativas são muitas vezes excessivamente otimistas. O negócio pode muitas vezes ter algumas orientações adicionais sobre como gostariam de testar o modelo.

A forma recomendada para o PdM é dividir os exemplos em conjuntos de dados de formação, validação e teste de forma _dependente do tempo._ Todos os exemplos de teste devem ser posteriores no tempo do que todos os exemplos de formação e validação. Após a separação, gere o modelo e mede o seu desempenho como descrito anteriormente.

Quando as séries de tempo são estacionárias e fáceis de prever, tanto as abordagens aleatórias como dependentes do tempo geram estimativas semelhantes de desempenho futuro. Mas quando as séries de tempo não são estacionárias, e/ou difíceis de prever, a abordagem dependente do tempo gerará estimativas mais realistas de desempenho futuro.

### <a name="time-dependent-split"></a>Divisão dependente do tempo
Esta secção descreve as melhores práticas para implementar a divisão dependente do tempo. Uma divisão bidirecional dependente do tempo entre os conjuntos de treino e de teste é descrita abaixo.

Assuma um fluxo de eventos timestamped tais como medições de vários sensores. Defina características e etiquetas de treino e exemplos de teste ao longo de períodos de tempo que contenham múltiplos eventos. Por exemplo, para a classificação binária, crie funcionalidades baseadas em eventos passados e crie rótulos baseados em eventos futuros dentro de unidades de tempo "X" no futuro (ver as secções sobre técnicas de engenharia de [recursos](#feature-engineering) e técnicas de modelação). Assim, o calendário de rotulagem de um exemplo vem mais tarde do que o período de tempo das suas características.

Para uma divisão dependente do tempo, escolha um tempo de corte de _treino T<sub>c</sub> _ no qual treinar um modelo, com hiperparâmetros afinados usando dados históricos até T<sub>c</sub>. Para evitar fugas de futuras etiquetas que estão para além de T<sub>c</sub> nos dados de formação, escolha o mais recente tempo para rotular exemplos de formação como unidades X antes de T<sub>c</sub>. No exemplo indicado na Figura 7, cada quadrado representa um registo no conjunto de dados onde as funcionalidades e etiquetas são calculadas como descrito acima. O valor mostra os registos que devem ir para os conjuntos de treino e teste para X=2 e W=3:

![Figura 7. Divisão dependente do tempo para classificação binária](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Figura 7. Divisão dependente do tempo para classificação binária

As praças verdes representam registos pertencentes às unidades do tempo que podem ser usadas para o treino. Cada exemplo de formação é gerado tendo em conta os últimos três períodos de geração de recursos, e dois períodos futuros para a rotulagem antes de T<sub>c</sub>. Quando qualquer parte dos dois períodos futuros for além do T<sub>c,</sub>exclua esse exemplo do conjunto de dados de formação porque não se assume visibilidade para além de T<sub>c</sub>.

Os quadrados pretos representam os registos do conjunto de dados final marcado que não deve ser utilizado no conjunto de dados de formação, dada a restrição acima referida. Estes registos também não serão utilizados nos dados de teste, uma vez que são antes de T<sub>c</sub>. Além disso, os seus prazos de rotulagem dependem parcialmente do tempo de treino, o que não é o ideal. Os dados de treino e de teste devem ter quadros de tempo de rotulagem separados para evitar fugas de informação sobre etiquetas.

A técnica discutida até agora permite a sobreposição entre exemplos de treino e teste que têm selos temporais perto de T<sub>c</sub>. Uma solução para alcançar uma maior separação é excluir exemplos que estão dentro das unidades de tempo W de T<sub>c</sub> do conjunto de teste. Mas uma divisão tão agressiva depende da ampla disponibilidade de dados.

Os modelos de regressão utilizados para prever o RUL são mais severamente afetados pelo problema de fuga. A utilização do método de divisão aleatória leva a um excesso extremo. Para problemas de regressão, a divisão deve ser tal que os registos pertencentes a ativos com falhas antes de T<sub>c</sub> entrar no conjunto de treino. Registos de ativos que têm falhas após o corte entrarem no conjunto de testes.

Outra boa prática para dividir dados para treino e teste é usar uma divisão por ID de ativo. A divisão deve ser tal que nenhum dos ativos utilizados no conjunto de treinos seja utilizado no teste do desempenho do modelo. Utilizando esta abordagem, um modelo tem mais hipóteses de fornecer resultados mais realistas com novos ativos.

### <a name="handling-imbalanced-data"></a>Manuseamento de dados desequilibrados
Nos problemas de classificação, se houver mais exemplos de uma classe do que de outras, diz-se que o conjunto de dados está _desequilibrado._ Idealmente, são preferidos representantes suficientes de cada classe nos dados de formação para permitir a diferenciação entre diferentes classes. Se uma classe for inferior a 10% dos dados, os dados são considerados desequilibrados. A classe sub-representada é chamada de _classe minoritária._

Muitos problemas de PdM enfrentam conjuntos de dados tão desequilibrados, em que uma classe está severamente sub-representada em comparação com a outra classe, ou classes. Em algumas situações, a classe minoritária pode constituir apenas 0,001% do total dos pontos de dados. O desequilíbrio de classes não é exclusivo do PdM. Outros domínios em que falhas e anomalias são ocorrências raras enfrentam um problema semelhante, por exemplo, deteção de fraudes e intrusão de rede. Estas falhas constituem os exemplos da classe minoritária.

Com o desequilíbrio de classes nos dados, o desempenho da maioria dos algoritmos de aprendizagem padrão está comprometido, uma vez que visam minimizar a taxa de erro global. Para um conjunto de dados com 99% de exemplos negativos e 1% positivos, um modelo pode mostrar-se com 99% de precisão rotulando todas as instâncias como negativas. Mas o modelo irá classificar mal todos os exemplos positivos; por isso, mesmo que a sua precisão seja elevada, o algoritmo não é útil. Consequentemente, as métricas de avaliação convencionais, tais como a _precisão global da taxa de erro,_ são insuficientes para uma aprendizagem desequilibrada. Quando confrontadas com conjuntos de dados desequilibrados, outras métricas são utilizadas para a avaliação do modelo:
- Precisão
- Recall
- Pontuações de F1
- ROC ajustado ao custo (características de funcionamento do recetor)

Para obter mais informações sobre estas métricas, consulte a avaliação do [modelo.](#model-evaluation)

No entanto, existem alguns métodos que ajudam a resolver o problema do desequilíbrio das classes. As duas principais são _técnicas de amostragem_ e _aprendizagem sensível aos custos._

#### <a name="sampling-methods"></a>Métodos de amostragem
A aprendizagem desequilibrada envolve a utilização de métodos de amostragem para modificar o conjunto de dados de formação para um conjunto de dados equilibrado. Os métodos de amostragem não devem ser aplicados no conjunto de ensaios. Embora existam várias técnicas de amostragem, a maioria das técnicas de recolha de amostras são _aleatórias_ e _sob amostragem._

_A sobreamostração aleatória_ envolve selecionar uma amostra aleatória da classe minoritária, replicar estes exemplos e adicioná-los ao conjunto de dados de treino. Consequentemente, o número de exemplos na classe minoritária é aumentado e, eventualmente, equilibra o número de exemplos de classes diferentes. Uma desvantagem da sobreamostragem é que vários casos de certos exemplos podem fazer com que o classificador se torne demasiado específico, levando ao excesso de adaptação. O modelo pode mostrar uma elevada precisão de treino, mas o seu desempenho em dados de teste invisíveis pode ser sub-óptimo.

Inversamente, _aleatoriamente sob amostragem_ está selecionando uma amostra aleatória de uma classe maioritária e removendo esses exemplos de conjunto de dados de treino. No entanto, a remoção de exemplos da classe maioritária pode fazer com que o classificador perca conceitos importantes relativos à classe maioritária. _A amostragem híbrida_ em que a classe minoritária é sobre-amostrada e a classe maioritária é sub-amostrada ao mesmo tempo é outra abordagem viável.

Há muitas técnicas de amostragem sofisticadas. A técnica escolhida depende das propriedades dos dados e dos resultados das experiências iterativas por parte do cientista de dados.

#### <a name="cost-sensitive-learning"></a>Aprendizagem sensível aos custos
No PdM, as falhas que constituem a classe minoritária são mais interessantes do que os exemplos normais. Portanto, o foco está principalmente no desempenho do algoritmo em falhas. Prever incorretamente uma classe positiva como classe negativa pode custar mais do que vice-versa. Esta situação é geralmente referida como perda desigual ou custo assimétrico de elementos de classificação errada para diferentes classes. O classificador ideal deve fornecer uma elevada precisão de previsão sobre a classe minoritária, sem comprometer a precisão para a classe maioritária.

Há várias formas de alcançar este equilíbrio. Para mitigar o problema da perda desigual, atribuir um custo elevado à classificação errada da classe minoritária, e tentar minimizar o custo global. Algoritmos como _SVMs (Máquinas de Vetor_ de Suporte) adotam este método inerentemente, permitindo que o custo de exemplos positivos e negativos seja especificado durante o treino. Da mesma forma, o reforço de métodos como árvores de _decisão reforçadas_ geralmente mostram um bom desempenho com dados desequilibrados.

## <a name="model-evaluation"></a>Avaliação do modelo
A classificação errada é um problema significativo para os cenários de PdM em que o custo dos falsos alarmes para o negócio é elevado. Por exemplo, a decisão de aterrar uma aeronave com base numa previsão incorreta de falha do motor pode perturbar os horários e os planos de viagem. Tirar uma máquina de uma linha de montagem pode levar à perda de receitas. Assim, a avaliação do modelo com as métricas de desempenho certas contra novos dados de teste é fundamental.

As métricas típicas de desempenho utilizadas para avaliar os modelos PdM são discutidas abaixo:

- [A precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision) é a métrica mais popular usada para descrever o desempenho de um classificador. Mas a precisão é sensível às distribuições de dados, e é uma medida ineficaz para cenários com conjuntos de dados desequilibrados. Em vez disso, outras métricas são usadas. Ferramentas como [a matriz](https://en.wikipedia.org/wiki/Confusion_matrix) de confusão são usadas para calcular e raciocinar sobre a precisão do modelo.
- [A precisão](https://en.wikipedia.org/wiki/Precision_and_recall) dos modelos PdM diz respeito à taxa de alarmes falsos. A menor precisão do modelo corresponde geralmente a uma taxa mais elevada de alarmes falsos.
- [A](https://en.wikipedia.org/wiki/Precision_and_recall) taxa de recolha denotas quantas das falhas no conjunto de testes foram corretamente identificadas pelo modelo. Taxas de recordação mais elevadas significam que o modelo é bem sucedido na identificação das verdadeiras falhas.
- [A pontuação de F1](https://en.wikipedia.org/wiki/F1_score) é a média harmónica de precisão e recordação, com o seu valor a variar entre 0 (pior) e 1 (melhor).

Para classificação binária,
- [As curvas de funcionamento do recetor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) também são uma métrica popular. Nas curvas ROC, o desempenho do modelo é interpretado com base num ponto de funcionamento fixo no ROC.
- Mas para os problemas de PdM, _tabelas decile_ e gráficos de _elevação_ são mais informativos. Focam-se apenas na classe positiva (falhas) e fornecem uma imagem mais complexa do desempenho do algoritmo do que as curvas roc.
  - _As tabelas decile_ são criadas usando exemplos de teste numa ordem descendente de probabilidades de falha. As amostras encomendadas são então agrunadas em deciles (10% das amostras com maior probabilidade, depois 20%, 30%, e assim por diante). O rácio (taxa positiva verdadeira)/(linha de base aleatória) para cada decile ajuda a estimar o desempenho do algoritmo em cada decile. A linha de base aleatória assume valores 0.1, 0.2, e assim por diante.
  - [Os gráficos](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) de elevação traçam a taxa positiva decile contra a taxa positiva aleatória para todos os deciles. Os primeiros deciles são geralmente o foco dos resultados, uma vez que mostram os maiores ganhos. Os primeiros deciles também podem ser vistos como representativos para "em risco", quando utilizados para a PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalização do modelo para manutenção preditiva

O benefício do exercício de ciência dos dados só é realizado quando o modelo treinado é tornado operacional. Ou seja, o modelo deve ser implantado nos sistemas empresariais para fazer previsões com base em dados novos, anteriormente invisíveis.  Os novos dados devem estar exatamente em conformidade com a _assinatura_ modelo do modelo treinado de duas formas:
- todas as funcionalidades devem estar presentes em todos os casos lógicos (digamos uma linha numa tabela) dos novos dados.
- os novos dados devem ser pré-processados e cada uma das funcionalidades concebidas, exatamente da mesma forma que os dados de formação.

O processo acima referido é referido de muitas formas na literatura académica e industrial. Mas todas as seguintes declarações significam a mesma coisa:
- _Marque novos dados_ usando o modelo
- _Aplicar o modelo_ a novos dados
- _Operacionalizar_ o modelo
- _Implementar_ o modelo
- _Executar o modelo_ contra novos dados

Como já foi referido, a operacionalização do modelo para a PdM é diferente dos seus pares. Cenários que envolvem deteção de anomalias e deteção de falhas normalmente implementam _pontuação on-line_ (também chamado de pontuação em _tempo real)._ Aqui, o modelo _marca_ cada recorde de entrada, e devolve uma previsão. Para a deteção de anomalias, a previsão é uma indicação de que ocorreu uma anomalia (Exemplo: SVM de classe única). Para deteção de falhas, seria o tipo ou classe de falha.

Em contraste, o PdM envolve _pontuação_de lote . Para se conformar com a assinatura do modelo, as funcionalidades dos novos dados devem ser concebidas da mesma forma que os dados de formação. Para os grandes conjuntos de dados que são típicos para novos dados, as funcionalidades são agregadas ao longo do tempo janelas e pontuadas em lote. A pontuação do lote é normalmente feita em sistemas distribuídos como [Spark](https://spark.apache.org/) ou [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Existem algumas alternativas - ambas subóptimas:
- Os motores de dados de streaming suportam a agregação sobre janelas na memória. Assim, pode argumentar-se que apoiam a pontuação online. Mas estes sistemas são adequados para dados densos em janelas estreitas do tempo, ou elementos escassos sobre janelas mais largas. Podem não escalar bem para os dados densos em janelas de tempo mais largas, como visto em cenários de PdM.
- Se não estiver disponível a pontuação do lote, a solução é adaptar a pontuação on-line para lidar com novos dados em pequenos lotes de cada vez.

## <a name="solution-templates-for-predictive-maintenance"></a>Modelos de solução para manutenção preditiva

A secção final deste guia fornece uma lista de modelos de solução PdM, tutoriais e experiências implementadas em Azure. Estas aplicações PdM podem ser implantadas numa subscrição Azure dentro de minutos em alguns casos. Podem ser usados como demonstrações de prova de conceito, caixas de areia para experimentar alternativas, ou aceleradores para implementações reais de produção. Estes modelos estão localizados na [Galeria Azure AI](https://gallery.azure.ai) ou [no Azure GitHub.](https://github.com/Azure) Estas diferentes amostras serão enroladas neste modelo de solução ao longo do tempo.

| # | Título | Descrição |
|--:|:------|-------------|
| 2 | [Modelo de solução de manutenção preditiva azure](https://github.com/Azure/AI-PredictiveMaintenance) | Um modelo de solução de código aberto que demonstra a modelação Azure ML e uma infraestrutura Azure completa capaz de suportar cenários de manutenção preditiva no contexto da monitorização remota IoT. |
| 3 | [Aprendizagem para Manutenção Preditiva](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | O Azure Notebook com uma solução de demonstração de utilização de redes LSTM (Memória de Longo Prazo) (uma classe de Redes Neurais Recorrentes) para Manutenção Preditiva, com uma publicação de [blogue nesta amostra.](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance)|
| 4 | [Guia de Modelação de Manutenção Preditiva em R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guia de modelação PdM com scripts em R.|
| 5 | [Manutenção Preditiva Azure para Aeroespacial](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Um dos primeiros modelos de solução PdM baseados no Azure ML v1.0 para manutenção de aeronaves. Este guia teve origem neste projeto. |
| 6 | [Kit de ferramentas Azure AI para IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | IA na Borda IoT utilizando o TensorFlow; o toolkit embala modelos de aprendizagem profunda em recipientes Docker compatíveis com bordas Azure IoT e expõe esses modelos como APIs REST.
| 7 | [Manutenção Preditiva Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | Azure IoT Suite PCS - Solução Preconfigured. Modelo PdM de manutenção de aeronaves com IoT Suite. [Outro documento](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) e [passagem](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) relacionadocom o mesmo projeto. |
| 8 | [Modelo de manutenção preditiva usando serviços R do Servidor SQL](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demo de permanecer cenário útil de vida baseado em serviços R. |
| 9 | [Guia de Modelação de Manutenção Preditiva](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Recurso de conjunto de dados de manutenção de aeronaves projetado utilizando R com [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) e [conjuntos](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) de dados e [caderno Azure](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) e [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) em AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Recursos de formação para manutenção preditiva

O Microsoft Azure oferece caminhos de aprendizagem para os conceitos fundamentais por trás das técnicas de PdM, além de conteúdo e formação sobre conceitos e práticas gerais de IA.

| Recurso de formação  | Disponibilidade |
|:-------------------|--------------|
| [Caminho de aprendizagem para PdM usando árvores e floresta aleatória](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Público | 
| [Caminho de Aprendizagem para PdM usando deep learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Público |
| [Desenvolvedor de IA em Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Público |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Público |
| [Aprendizagem de IA Azure do GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Público |
| [LinkedIn Learning](https://www.linkedin.com/learning) | Público |
| [Microsoft AI YouTube Webinars](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Público |
| [Microsoft AI Show](https://channel9.msdn.com/Shows/AI-Show) | Público |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Parceiros |
| [Microsoft Partner Network](https://partner.microsoft.com/training/training-center) | Parceiros |

Além disso, moocs gratuitos (cursos online abertos massivos) em IA são oferecidos online por instituições académicas como Stanford e MIT, e outras empresas educativas.
