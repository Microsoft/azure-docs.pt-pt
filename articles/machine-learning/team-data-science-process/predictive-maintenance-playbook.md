---
title: Guia de IA do Azure para soluções de manutenção preditiva - Team Data Science Process
description: Uma descrição abrangente de ciência de dados que capacita a soluções de manutenção preditiva em várias indústrias verticais.
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
ms.openlocfilehash: 5cf3f02284777a54a9d26cad8a7f3b5b4fa6b335
ms.sourcegitcommit: 20429bc76342f9d365b1ad9fb8acc390a671d61e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79087805"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Guia de IA do Azure para soluções de manutenção preditiva

## <a name="summary"></a>Resumo

A manutenção preditiva **(PdM)** é uma aplicação popular de análises preditivas que podem ajudar as empresas de várias indústrias a alcançar uma elevada utilização de ativos e poupanças em custos operacionais. Este guia reúne as diretrizes empresariais e analíticas e as melhores práticas para desenvolver e implementar soluções PdM com sucesso utilizando a tecnologia da [plataforma Microsoft Azure AI.](https://azure.microsoft.com/overview/ai-platform)

Para os iniciantes, este Guia apresenta cenários comerciais específicos da indústria e o processo de qualificação estes cenários para PdM. Os requisitos de dados e a técnicas de modelação para criar soluções de PdM também são fornecidas. O conteúdo principal do guia é o processo de ciência de dados - incluindo os passos de preparação de dados, engenharia de funcionalidades, criação de um modelo e operacionalização de modelo. Para complementar estes conceitos-chave, este Guia apresenta uma lista de um conjunto de modelos de soluções que ajudam a acelerar o desenvolvimento de aplicativos PdM. O guia também aponta para os recursos de treinamento útil para o profissional saber mais sobre a IA por trás de ciência de dados. 

### <a name="data-science-guide-overview-and-target-audience"></a>Dados ciência guia visão geral e de destino público-alvo
A primeira metade deste guia descreve os problemas de negócios típicos, os benefícios da implementação PdM para resolver estes problemas e apresenta uma lista de alguns casos de utilização comuns. Os tomadores de decisões comerciais (BDMs) irão beneficiar deste conteúdo. A segunda metade explica a ciência de dados por trás PdM e fornece uma lista de soluções de PdM criadas usando os princípios descritos neste guia. Ele também fornece os percursos de aprendizagem e ponteiros para o material de treinamento. Os tomadores de decisões técnicas (TDMs) irão encontrar este conteúdo útil.

| Começar com... | Se for... |
|:---------------|:---------------|
| [Caso de negócio para manutenção preditiva](#business-case-for-predictive-maintenance) |uma responsável pelas decisões comerciais (BDM) pretende reduzir os custos operacionais e tempo de inatividade e melhorar a utilização de equipamentos |
| [Ciência dos Dados para manutenção preditiva](#data-science-for-predictive-maintenance) |um tomador de decisões técnicas (TDM) avaliar tecnologias PdM para compreender os requisitos de ia para manutenção preditiva e de processamento de dados exclusivo |
| [Modelos de solução para manutenção preditiva](#solution-templates-for-predictive-maintenance)|um arquiteto de software ou um programador de IA à procura de criar rapidamente uma demonstração ou uma prova de conceito |
| [Recursos de formação para manutenção preditiva](#training-resources-for-predictive-maintenance) | uma ou todas acima e quiser aprender os conceitos fundamentais da ciência de dados, ferramentas e técnicas.

### <a name="prerequisite-knowledge"></a>Conhecimento dos pré-requisitos
O conteúdo de BDM – não espera que o leitor tenha qualquer conhecimento de ciência de dados anterior. Para o conteúdo de TDM –, o conhecimento básico de estatísticas e ciência de dados é útil. É recomendável o conhecimento dos dados do Azure e serviços de IA, Python, R, XML e JSON. Técnicas de IA são implementadas nos pacotes de Python e R. Modelos de soluções são implementados por meio de serviços do Azure, ferramentas de desenvolvimento e SDKs.

## <a name="business-case-for-predictive-maintenance"></a>Caso comercial para manutenção preditiva

As empresas exigem equipamento crítico estar em execução na eficiência de pico e utilização para perceber seu retorno em investimentos de capitais. Esses ativos podem ir desde motores de aeronaves, turbines, elevadores ou chillers industriais - que custam milhões - a para baixo para aplicações de todos os dias como fotocopiadoras, máquinas de café ou coolers de água.
- Por predefinição, a maioria das empresas depende da _manutenção corretiva,_ onde as peças são substituídas como e quando falham. Manutenção corretiva garante partes são totalmente utilizadas (vida de componente, por conseguinte, não desperdiçando), custos, mas os negócios em tempo de inatividade, trabalho e os requisitos de manutenção agendada (desativado horas ou localizações inconvenientes).
- No nível seguinte, as empresas praticam _a manutenção preventiva,_ onde determinam o tempo útil para uma parte, e mantêm-na ou substituem-na antes de uma falha. Manutenção preventiva evita falhas catastróficas e não agendadas. Mas os elevados custos do tempo de paragem programado, a subutilização do componente durante a sua vida útil, e o trabalho ainda permanecem.
- O objetivo da _manutenção preditiva_ é otimizar o equilíbrio entre a manutenção corretiva e preventiva, permitindo _apenas a tempo_ a substituição de componentes. Essa abordagem substitui apenas esses componentes quando eles estiverem prestes uma falha. Ao expandir empreguem componente (comparada com a manutenção preventiva) e reduzir os custos de trabalho e de manutenção agendada (através de manutenção corretiva), as empresas podem obter reduções de custos e vantagens competitivas.

## <a name="business-problems-in-pdm"></a>Problemas de negócio num PdM
As empresas enfrentam riscos operacionais elevado devido a falhas inesperadas e de limitaram informações sobre a causa raiz dos problemas em sistemas complexos. Algumas das principais perguntas empresariais que são:

- Detete anomalias no desempenho do sistema ou de equipamento ou a funcionalidade.
- Prever se um recurso pode falhar num futuro próximo.
- Calcule a vida útil remanescente de um ativo.
- Identifica as principais causas de falha de um ativo.
- Identifica o que as ações de manutenção precisam ser feito por quando, num elemento.

Instruções de objetivo típico de PdM são:

- Reduza o risco operacional dos equipamentos de missão crítica.
- Aumente a taxa de retorno na ativos ao prever falhas antes de ocorrerem.
- Controlar o custo de manutenção, permitindo que as operações de manutenção de just-in-time.
- Reduzir por motivo de desgaste do cliente, melhorar a imagem de marca e a perda de vendas.
- Redução dos custos de inventário, reduzindo os níveis de estoque ao prever o momento de reordenação.
- Descubra padrões ligados a diversos problemas de manutenção.
- Forneça KPIs (indicadores chave de desempenho), como as pontuações de estado de funcionamento para condições de recurso.
- Calcule a vida útil restante de ativos.
- Recomendamos as atividades de manutenção oportuna.
- Ative-se apenas no inventário de tempo ao estimar as datas de pedido para substituição de partes.

Estas instruções de objetivo são os pontos de partida para:

- _cientistas_ de dados para analisar e resolver problemas preditivos específicos.
- _arquitetos e desenvolvedores_ de nuvem para reunir um fim para a solução final.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Qualificação problemas para manutenção preditiva
É importante enfatizar que nem todos os casos de utilização ou problemas de negócios podem ser resolvidos com eficiência por PdM. Existem três critérios de qualificação importantes que precisam ser consideradas durante a seleção de problema:

- O problema tem de ser preditiva por natureza; ou seja, deve haver um destino ou um resultado para prever. O problema também deve ter um caminho claro de ação para evitar falhas quando eles são detetados.
- O problema deve ter um registo do histórico operacional do equipamento que contém _resultados bons e maus._ O conjunto de ações executadas para atenuar os resultados incorretos também deve estar disponível como parte destes registos. Relatórios de erros, registos de manutenção de degradação do desempenho, reparar e registos de substituição também são importantes. Além disso, os reparos realizados para melhorá-los e registos de substituição também são úteis.
- O histórico registado deve refletir-se em dados _relevantes_ que sejam _suficientemente suficientes_ para suportar o caso de utilização. Para obter mais informações sobre relevância e suficiência de dados, consulte [os requisitos dos Dados para a manutenção preditiva](#data-requirements-for-predictive-maintenance).
- Por fim, a empresa deve ter os especialistas de domínio que tenham um entendimento claro do problema. Eles devem estar cientes dos processos internos e práticas que poderão ajudar a analista de compreender e interpretar os dados. Eles também devem ser capazes de fazer as alterações necessárias para processos de negócio existentes para o ajudar a recolher os dados corretos para os problemas, se necessário.

## <a name="sample-pdm-use-cases"></a>Casos de utilização de PdM de exemplo
Esta seção se concentra numa coleção de PdM casos de utilização de vários setores, tais como indústria aeroespacial, utilitários e transportes. Cada seção começa com um problema de negócios e aborda os benefícios de PdM, os dados relevantes que o problema comercial e, finalmente, os benefícios de uma solução de PdM.

| Problema de negócios | Benefícios do PdM |
|:-----------------|-------------------|
|**Aviação**      |                   |
|Atraso de _voo e cancelamentos_ devido a problemas mecânicos. Falhas que não não possível reparar no tempo podem causar voos sejam canceladas e perturbar o agendamento e de operações. |Soluções de PdM podem prever a probabilidade de uma aeronave a ser atrasado ou foi cancelada devido a falhas mecânicas.|
|_Falha das peças_do motor dos aviões : As substituições da parte do motor dos aviões estão entre as tarefas de manutenção mais comuns no sector das companhias aéreas. Soluções de manutenção requerem a gestão de cuidado de disponibilidade de estoque de componentes, entrega e planejamento|A possibilidade de obter a inteligência de fiabilidade de componente leva à redução substancial nos custos de investimento.|
|**Finanças** |                         |
|_O insucesso do multibanco_ é um problema comum no sector bancário. O problema aqui é a probabilidade de que uma transação de retirada de dinheiro de ATM for interrompida devido a uma falha de confusão ou parte do documento no fornecedor de pagamento à vista de relatório. Com base nas predições de falhas de transação, caixas eletrônicos podem ser reparados proativamente para evitar a ocorrência de falhas.| Em vez de permitir que a máquina para falhar a meio de uma transação, é a alternativa desejável programa a máquina para negar serviços com base na predição.|
|**Energia** |                          |
|Falhas nas _turbinas eólicas_: As turbinas eólicas são a principal fonte de energia em países/regiões ambientalmente responsáveis e envolvem elevados custos de capital. Um componente chave nas turbinas eólicas é o motor gerador, cuja falha torna a turbina ineficaz. Também é altamente Caro corrigir.|Prever KPIs, tais como MTTF (tempo médio de falha) pode ajudar as empresas de energia evitar falhas de turbina e certifique-se de período de indisponibilidade mínimo. Probabilidades de falha irão informar técnicos para monitorizar turbines que são provável que falhem em breve e agendar regimes baseados no tempo de manutenção. Modelos de previsão de fornecem informações sobre os diferentes fatores que contribuem para a falha, o que ajuda a técnicos de compreender melhor que as principais causas de problemas.|
|Falhas de _disjuntores_: A distribuição de eletricidade para casas e empresas exige que as linhas de energia estejam sempre operacionais para garantir a entrega de energia. Separadores de circuito ajudar a limitar ou evitar danos à potência linhas durante sobrecarga ou adversos sobreviver aos condições. O problema de negócios aqui é prever falhas de disjuntor automático.| As soluções de PdM ajudam a reduzir os custos de reparação e aumentar a vida útil de equipamentos, tais como separadores de circuito. Eles ajudam a melhorar a qualidade da rede energia ao reduzir falhas inesperadas e interrupções do serviço.|
|**Transporte e logística** |    |
|_Falhas_na porta do elevador : As grandes empresas de elevadores fornecem um serviço de pilha completa para milhões de elevadores funcionais em todo o mundo. Tempo de atividade, confiabilidade e segurança elevador são as principais preocupações para seus clientes. Estas empresas controlam esses e vários outros atributos através de sensores, para ajudá-los com a manutenção preventiva e medidas corretiva. Num elevador, o problema mais proeminente do cliente está a funcionar incorretamente portas elevador. Neste caso é o problema comercial proporcionar um aplicativo de previsão de base de dados de conhecimento que prevê que o potencial causa de falhas de porta de.| Elevadores são investimentos de capitais para potencialmente um tempo de vida do ano de 20 a 30. Para que cada venda potencial possa ser altamente competitiva; Por conseguinte, as expectativas de suporte são elevadas. Manutenção preditiva pode dar a essas empresas com uma vantagem sobre seus concorrentes em seus produtos e ofertas de serviço.|
|_Falhas_nas rodas : As falhas das rodas representam metade de todos os descarrilamentos dos comboios e custam milhares de milhões à indústria ferroviária global. Falhas de roda também causam rails para prejudicado, às vezes, fazendo com que o rail quebrar prematuramente. Quebras de Rail gerar eventos catastróficos, como o caos. Para evitar essas instâncias, railways monitorizar o desempenho de rodas e substituí-los de forma preventiva. O problema de negócios aqui é a predição de falhas de roda.| Manutenção preditiva de rodas ajudará com just-in-time substituição de rodas |
|_Falhas_nas portas do metro : Uma das principais razões para os atrasos nas operações do metro são as falhas nas portas dos vagões de comboio. O problema de negócios aqui é prever falhas de porta de comboio.|Reconhecimento de início de uma falha de porta ou o número de dias até uma falha de porta, ajudará a otimização de negócios treinar porta agendamentos de manutenção.|

A secção seguinte obtém os detalhes de como obter os benefícios de PdM descritos acima.

## <a name="data-science-for-predictive-maintenance"></a>Ciência de dados para manutenção preditiva

Esta seção fornece diretrizes gerais dos princípios de ciência de dados e prática para PdM. Destina-se para o ajudar a um TDM –, arquiteto de soluções, ou um desenvolvedor compreender os pré-requisitos e o processo para criar aplicações de ia de ponto-a-ponto para PdM. Pode ler esta secção juntamente com uma revisão das demos e modelos de prova de conceito listados em Modelos de [Solução para manutenção preditiva](#solution-templates-for-predictive-maintenance). Em seguida, pode utilizar esses princípios e práticas recomendadas para implementar a sua solução de PdM no Azure.

> [!NOTE]
> Este guia não se destina a ensinar o leitor de ciência de dados. São fornecidas várias fontes úteis para uma nova leitura na secção de recursos de [formação para manutenção preditiva.](#training-resources-for-predictive-maintenance) Os modelos de [solução](#solution-templates-for-predictive-maintenance) listados no guia demonstram algumas destas técnicas de IA para problemas específicos de PdM.

## <a name="data-requirements-for-predictive-maintenance"></a>Requisitos de dados para manutenção preditiva

O sucesso de qualquer learning depende de (a) a qualidade de que está sendo ensinado e (b) a capacidade do aprendiz. Modelos de previsão de aprender os padrões de dados históricos e prever resultados futuros com determinados probabilidade com base nesses padrões observados. Precisão de previsão de um modelo depende a relevância, sufficiency e qualidade dos dados de preparação e teste. Os novos dados, que é "classificados" com este modelo devem ter os mesmos recursos e o esquema dos dados de treinamento e teste. As características de funcionalidade (tipo, densidade, distribuição e assim por diante) de novos dados devem corresponder ao que os conjuntos de dados de preparação e teste. O foco desta seção é tais requisitos de dados.

### <a name="relevant-data"></a>Dados relevantes

Em primeiro lugar, os dados têm de ser _relevantes para o problema._ Considere o caso de utilização da _falha das rodas_ acima discutido - os dados de treino devem conter características relacionadas com as operações das rodas. Se o problema era prever a falha do sistema de _tração,_ os dados de treino têm de abranger todos os diferentes componentes para o sistema de tração. O primeiro caso destina-se um componente específico, ao passo que o segundo caso destina-se a falha de um subsistema de maior. A recomendação geral é projetar os sistemas de predição sobre componentes específicos, em vez de subsistemas maiores, uma vez que o último será ter mais dispersos dados. O perito em domínios (ver [Problemas de qualificação para manutenção preditiva)](#qualifying-problems-for-predictive-maintenance)deve ajudar na seleção dos subconjuntos de dados mais relevantes para a análise. As fontes de dados relevantes são discutidas mais pormenorizadamente na preparação de [dados para manutenção preditiva.](#data-preparation-for-predictive-maintenance)

### <a name="sufficient-data"></a>Dados suficientes
Duas perguntas feitas normalmente em relação a dados do histórico de falha: (1) "o número de eventos de falha é necessários para preparar um modelo?" (2) "Quantos registos são considerados "suficientes"? Não há respostas definitivas, mas apenas regras de polegar. Para (1), mais o número de eventos de falha, melhor o modelo. Para (2), e o número exato de eventos de falha depende dos dados e o contexto do problema que está sendo resolvido. Mas, por outro lado, se uma máquina falhar com muita frequência, em seguida, a empresa irá substituí-lo, que irá reduzir as instâncias de falha. Aqui, novamente, a documentação de orientação do domínio especialistas é importante. No entanto, existem métodos para lidar com a questão dos _acontecimentos raros._ São discutidos na secção [Manipulação de dados desequilibrados](#handling-imbalanced-data).

### <a name="quality-data"></a>Dados de qualidade
A qualidade dos dados é crítica - cada valor do atributo do preditor deve ser _exato_ em conjunto com o valor da variável-alvo. Qualidade de dados é uma área bem estudada na gestão de estatísticas e dados e, por conseguinte, fora do âmbito deste guia.

> [!NOTE]
> Existem vários recursos e produtos empresariais qualidade de dados. Um exemplo de referências é apresentado abaixo:
> - Dasu, T, Johnson, extração de dados de t., exploratórios e limpezas de dados, Wiley, 2003.
> - [Análise de Dados Exploratórios, Wikipédia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Hellerstein, J, Limpeza quantitativa de dados para grandes bases de dados](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [de Jonge, E, van der loo, M, Introdução à Limpeza de Dados com R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

## <a name="data-preparation-for-predictive-maintenance"></a>Preparação de dados para manutenção preditiva

### <a name="data-sources"></a>Origens de dados

As origens de dados relevantes para manutenção preditiva incluem, mas não sejam limitam a:
- Histórico de falhas
- Histórico de reparação/manutenção
- Condições de funcionamento da máquina
- Metadados de equipamentos

#### <a name="failure-history"></a>Histórico de falhas
Eventos de falha são raros em aplicativos de PdM. No entanto, ao criar modelos de predição, o algoritmo tem de saber mais sobre o padrão de operacional normal de um componente, bem como seus padrões de falha. Portanto, os dados de treinamento devem conter um número suficiente de exemplos de ambas as categorias. Histórico de substituição de registros e partes de manutenção são boas fontes para encontrar eventos de falha. Com a ajuda de alguns dados de conhecimento do domínio, anomalias nos dados de treinamento também podem ser definidas como falhas.

#### <a name="maintenancerepair-history"></a>Histórico de reparação/manutenção
O histórico de manutenção de um ativo contém detalhes sobre componentes substituídos, atividades de reparação realizadas, etc. Estes eventos registam padrões de degradação. Ausência dessas informações cruciais nos dados de treinamento pode levar a resultados de modelo enganoso. Histórico de falhas também pode ser encontrado no histórico de manutenção como códigos de erro especial ou datas de encomenda de partes. Origens de dados adicionais que influenciam a padrões de falhas devem ser investigadas e fornecidas por especialistas de domínio.

#### <a name="machine-operating-conditions"></a>Condições de funcionamento da máquina
Dados de transmissão em fluxo de sensores com base (ou outros) do equipamento na operação são uma origem de dados importantes. Uma suposição fundamental no PdM é que o estado de saúde de uma máquina se degrada ao longo do tempo durante o seu funcionamento de rotina. Espera-se que os dados contêm recursos variados de tempo que capturam a esse padrão de classificação por vencimento e quaisquer anomalias que leva à degradação. O aspecto temporal dos dados é necessário para o algoritmo para aprender a falha e os padrões da não-falhas ao longo do tempo. O algoritmo com base nos seguintes pontos de dados, aprende prever a quantos mais unidades de tempo que uma máquina pode continuar a funcionar antes de falhar.

#### <a name="static-feature-data"></a>Dados de recursos estáticos
Recursos estáticos são metadados sobre o equipamento. Os exemplos são o equipamento marca, modelo, produzidos e data, comece a data do serviço, a localização do sistema e outras especificações técnicas.

Exemplos de dados relevantes para os casos de utilização do [PdM](#sample-pdm-use-cases) da amostra são tabulados abaixo:

| Caso de Utilização | Exemplos de dados relevantes |
|:---------|---------------------------|
|_Atraso e cancelamentos de voo_ | Informações de rota de voo na forma de registos de página e de legs de voo. Os dados da perna de voo incluem detalhes de encaminhamento, tais como data de partida/chegada, hora, aeroporto, paradções, etc. O registo de página inclui uma série de códigos de erro e manutenção registados pelo pessoal de manutenção do solo.|
|_Falha nas peças do motor dos aviões_ | Os dados recolhidos a partir de sensores na aeronave que fornece informações sobre a condição de várias partes. Registos de manutenção ajudam a identificar quando ocorreram de falhas de componentes e quando elas foram substituídas.|
|_Falha no multibanco_ | As leituras dos sensores para cada transação (depositar o pagamento à vista/verificação) e diz respeito à administração de pagamento à vista. Informação sobre a medição do intervalo entre notas, espessura de nota, distância de chegada de nota, verificar atributos etc. Registos de manutenção que fornecem códigos de erro, informações de reparação, da última vez que o distribuidor de dinheiro foi reabastecido.|
|_Falha na turbina eólica_ | Os sensores monitorizam as condições da turbina, tais como temperatura, direção do vento, energia gerada, velocidade do gerador, etc. Os dados são recolhidos a partir de várias turbinas eólicas de parques eólicos localizados em várias regiões. Normalmente, cada turbina terá vários as leituras dos sensores retransmitir medidas num intervalo de tempo fixo.|
|_Falhas no disjuntor_ | Registos de manutenção que incluem ações corretivas, preventivas e sistemática. Dados operacionais, que inclui automáticos e manuais comandos enviados para os separadores de circuito, tais como para ações de abertura e fechamento. Metadados do dispositivo, tais como data de fabrico, localização, modelo, etc. Especificações de disjuntores tais como níveis de tensão, geolocalização, condições ambientais.|
|_Falhas na porta do elevador_| Elevador metadados, tais como o tipo de elevador, produzidos e data, frequência de manutenção, tipo de construção e assim por diante. Informações operacionais, como o número de ciclos de porta, tempo médio de porta de fechar. Histórico de falhas com as causas.|
|_Falhas nas rodas_ | Dados do sensor que medem a aceleração das rodas, as instâncias de travagem, a distância de condução, a velocidade, etc. Informações estáticas sobre rodas como fabricante, data fabricada. Dados de falha inferidos da base de dados de ordem de parte que controlam as datas de pedido e as quantidades.|
|_Falhas na porta do metro_ | Abertura de porta e os tempos de fechamento, outros dados operacionais, como condição atual de portas de comboio. Dados estáticos incluiria o identificador de recurso, a hora e colunas de valor de condição.|

### <a name="data-types"></a>Tipos de dados
Os dois tipos de dados principal observados no domínio PdM tendo em conta as origens de dados acima, são:

- _Dados temporais_: Telemetria operacional, condições da máquina, tipos de ordem de trabalho, códigos prioritários que terão carimbos de tempo no momento da gravação. Falha de reparação/manutenção e histórico de utilização também terão os carimbos associados a cada evento.
- _Dados estáticos_: As características da máquina e as características do operador em geral são estáticas, uma vez que descrevem as especificações técnicas das máquinas ou dos atributos do operador. Se esses recursos podem mudar ao longo do tempo, também terão os carimbos associados a eles.

As variáveis do preditor e do alvo devem ser pré-processadas/transformadas em [tipos numéricos, categóricos e outros tipos](https://www.statsdirect.com/help/basics/measurement_scales.htm) de dados dependendo da utilizada no algoritmo.

### <a name="data-preprocessing"></a>O pré-processamento de dados
Como pré-requisito para _a engenharia de recursos,_ prepare os dados de vários fluxos para compor um esquema do qual é fácil construir funcionalidades. Visualize os dados pela primeira vez como uma tabela de registos. Cada linha na tabela representa uma instância de formação, e as colunas representam características _do preditor_ (também chamadas atributos independentes ou variáveis). Organize os dados de modo a que a última coluna(s) seja o _alvo_ (variável dependente). Para cada instância de treino, atribua um _rótulo_ como valor desta coluna.

Para dados temporais, divida a duração de dados de sensor em unidades de tempo. Cada registo deve pertencer a uma unidade de tempo para um ativo, _e deve oferecer informações distintas_. Unidades de tempo são definidas com base nas necessidades de negócio em múltiplos de segundos, minutos, horas, dias, meses, e assim por diante. A unidade de tempo não tem de ser a mesma que _a frequência de recolha de dados_. Se a frequência é alta, os dados não podem mostrar qualquer diferença significativa de uma unidade para outro. Por exemplo, suponha que a temperatura ambiente foi recolhida cada 10 segundos. Apenas utilizar esse mesmo intervalo de tempo para os dados de treinamento aumenta o número de exemplos sem fornecer informações adicionais. Para esse caso, uma estratégia melhor seria usar a média de dados mais de 10 minutos, ou uma hora com base na justificativa comercial.

Para dados estáticos,
- _Registos de manutenção_: Os dados de manutenção bruta têm um identificador de ativos e um carimbo de tempo com informações sobre as atividades de manutenção que tenham sido realizadas num dado momento. Transforme as atividades de manutenção em colunas _categóricas,_ onde cada descritor de categoria supor exclusivamente mapas para uma ação de manutenção específica. O esquema para os registos de manutenção incluiria o identificador de recurso, a hora e a ação de manutenção.

- _Registos de falhas_: Falhas ou razões de falha podem ser registadas como códigos de erro específicos ou eventos de falha definidos por condições de negócio específicas. Em casos em que o equipamento tem vários códigos de erro, o especialista de domínio deve ajudar a identificar aqueles que são pertinentes para a variável de destino de mensagens em fila. Utilize os restantes códigos de erro ou condições para construir características _do preditor_ que se relacionam com estas falhas. O esquema para os registos de falha incluiria o identificador de recurso, hora, falha ou motivo da falha - se disponível.

- _Metadados_da máquina e do operador: Fundir os dados da máquina e do operador num único esquema para associar um ativo ao seu operador, juntamente com os respetivos atributos. O esquema para condições de máquina incluiria o identificador de recurso, funcionalidades de recurso, identificador de operador e funcionalidades de operador.

Outras etapas de pré-processamento de dados incluem o manuseamento de _valores em falta_ e a _normalização_ dos valores dos atributos. Ver um debate detalhado está além do escopo deste guia – consulte a secção seguinte para algumas referências de útil.

Com o acima pré-processados origens de dados no local, a transformação final antes de é de engenharia de funcionalidades associar as tabelas acima, com base no identificador de recurso e timestamp. A tabela resultante teria valores nulos para a coluna de falha quando a máquina está em funcionamento normal. Estes valores nulas podem ser imputed por um indicador para um funcionamento normal. Utilize esta coluna de falha para criar _etiquetas para o modelo preditivo_. Para mais informações, consulte a secção sobre [técnicas de modelação para manutenção preditiva](#modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Com engenharia
Engenharia de funcionalidades é a primeira etapa antes da modelagem de dados. O seu papel no processo de ciência dos dados [é descrito aqui.](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features) Uma _característica_ é um atributo preditivo para o modelo - como temperatura, pressão, vibração, e assim por diante. Para a PdM, a engenharia de recursos envolve abstrair a saúde de uma máquina sobre dados históricos recolhidos ao longo de uma duração considerável. Nesse sentido, é diferente de seus colegas como monitorização remota, deteção de anomalias e deteção de falhas. 

### <a name="time-windows"></a>Janelas de tempo
A monitorização remota implica relatar os eventos que ocorrem a partir de _pontos no tempo_. Modelos de deteção de anomalias avaliam fluxos de entrada (classificação) dos dados para anomalias de sinalizador a partir de pontos no tempo. Deteção de falhas classifica falhas sejam de tipos específicos à medida que ocorrem pontos no tempo. Em contraste, o PdM envolve prever falhas durante um _período de tempo futuro_, com base em características que representam o comportamento da máquina durante o período _histórico._ Para PdM, os dados da funcionalidade de pontos individuais de tempo são demasiado ruidosos ser preditiva. Assim, os dados de cada funcionalidade precisam de ser _suavizados_ agregando pontos de dados ao longo das janelas de tempo.

### <a name="lag-features"></a>Funcionalidades de atraso
Os requisitos comerciais definem até que ponto o modelo tem que prever o futuro. Por sua vez, esta duração ajuda a definir 'até que ponto back o modelo tem de ter um aspeto' para fazer estas previsões. Este período de "olhar para trás" chama-se _lag_, e as características concebidas durante este período de atraso são chamadas _de funcionalidades_de lag . Esta secção aborda os recursos de atraso que podem ser criados a partir de origens de dados de carimbos e criação de recursos de origens de dados estáticos. As características do lag são tipicamente _numéricas_ na natureza.

> [!IMPORTANT]
> O tamanho da janela é determinado através de experimentação e deve ser finalizado com a ajuda de uma especialista de domínio. A limitação mesmo mantém-se para a seleção e a definição de recursos de atraso, suas agregações e o tipo do windows.

#### <a name="rolling-aggregates"></a>Agregados sem interrupção
Para cada registo de um ativo, uma janela sem interrupção de tamanho "W" for escolhida, para computar as agregações, como o número de unidades de tempo. As características de lag são então calculadas utilizando os períodos W _antes da data_ desse registo. Na figura 1, as linhas azuis mostram os valores de sensores registados para um recurso para cada unidade de tempo. Eles denotam uma média de valores de funcionalidade numa janela de tamanho W = 3. A média de rolamento é calculada em todos os registos com carimbos de tempo na gama t<sub>1</sub> (em laranja) a t<sub>2</sub> (em verde). O valor para W normalmente está em minutos ou horas, consoante a natureza dos dados. Mas, para determinados problemas, escolher um grande W (Digamos, 12 meses) podem fornecer o histórico completo de um ativo até a hora do registo.

![Figura 1. Implementar recursos de agregação](./media/predictive-maintenance-playbook/rolling-aggregate-features.png)

Figura 1. Implementar recursos de agregação

Exemplos de sem interrupção agregados numa janela de tempo são contagem, média, medidas CUMESUM (soma cumulativa), valores mínimos/máximos. Além disso, variação, desvio padrão e contagem de valores atípicos, além de desvios padrão de N, muitas vezes, são utilizados. Exemplos de agregados que podem ser aplicados para os casos de [utilização](#sample-pdm-use-cases) neste guia são listados abaixo. 
- _Atraso_de voo : contagem de códigos de erro durante o último dia/semana.
- _Falha da parte do motor_do motor da aeronave : meios de rolamento, desvio padrão e soma ao longo do último dia, semana, etc. Esta métrica deve ser determinada juntamente com o especialista em domínio empresarial.
- _Falhas no multibanco_: meios de rolamento, mediano, alcance, desvios padrão, contagem de outliers para além de três desvios padrão, CUMESUM superior e inferior.
- _Falhas_na porta do metro : Contagem de eventos ao longo do passado dia, semana, duas semanas, etc.
- Falhas no _disjuntor:_ Falha conta na semana passada, ano, três anos, etc.

Outra técnica útil no PdM é capturar alterações de tendências, picos e alterações no nível de usando algoritmos que detetem anomalias nos dados.

#### <a name="tumbling-aggregates"></a>Agregados em cascata
Para cada registo rotulado de um ativo, define-se uma janela de tamanho _<sub>W-k,</sub>_  onde _k_ é o número de janelas do tamanho _W_. Os agregados são então criados sobre _k_ _tumbling windows_ _W-k, W-<sub>(k-1)</sub>, ..., W-2 ,<sub>2</sub>W-1<sub>1</sub>_  para os períodos antes do carimbo temporal de um recorde. _k_ pode ser um pequeno número para capturar efeitos a curto prazo, ou um grande número para capturar padrões de degradação a longo prazo. (consulte a figura 2).

![Figura 2. Em cascata funcionalidades agregadas](./media/predictive-maintenance-playbook/tumbling-aggregate-features.png)

Figura 2. Em cascata funcionalidades agregadas

Por exemplo, lag funcionalidades para o caso de utilização do vento turbines pode ser criado com W = 1 e k = 3. Eles implicam o desfasamento para cada um dos últimos três meses usando valores atípicos superior e inferior.

### <a name="static-features"></a>Recursos estáticos

As especificações técnicas do equipamento como a data de fabrico, número de modelo, localização, são alguns exemplos de recursos estáticos. São tratados como variáveis _categóricas_ para modelação. Alguns exemplos para o caso de utilização do disjuntor automático são tensão atual, capacidade energética, tipo transformador e fonte de energia. Para falhas de roda, o tipo de rodas de tire (alloy vs acinzentado) é um exemplo.

Os esforços de preparação de dados discutidos até agora devem levar aos dados que está a ser organizados conforme mostrado abaixo. Dados de treinamento, teste e validação devem ter este esquema lógico (Este exemplo mostra o tempo em unidades de dias).

| ID do Recurso | Hora | \<Feature Columns> | Etiqueta |
| ---- | ---- | --- | --- |
| A123 |Dia 1 | . . . | . |
| A123 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dia 1 | . . . | . |
| B234 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |

O último passo na engenharia de recursos é a **rotulagem** da variável-alvo. Este processo é dependente da técnica de modelagem. Por sua vez, a técnica de modelagem depende do problema de negócios e a natureza dos dados disponíveis. Etiquetagem é abordada na secção seguinte.

> [!IMPORTANT]
> Preparação de dados e de engenharia de funcionalidades são tão importantes quanto técnicas para deparar-se com êxito PdM soluções de modelagem. O especialista de domínio e o profissional devem investir tempo significativo se chegar uma a certos recursos e os dados para o modelo. Uma pequena amostra de muitos livros em engenharia de funcionalidades estão listados abaixo:
> - Pyle, preparação de dados d. para os dados (a Morgan Kaufmann série em sistemas de gerenciamento de dados), de extração de 1999
> - Zheng, r., Casari, engenharia de funcionalidades de r. para o Machine Learning: princípios e as técnicas para cientistas de dados, o ' Reilly, de 2018.
> - Dong, G. Liu, H. (Editores), Engenharia de Recursos para Machine Learning e Análise de Dados (Chapman & Hall/CRC Data Mining and Knowledge Discovery Series), CRC Press, 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Técnicas de modelação para manutenção preditiva

Esta secção descreve as técnicas de modelagem de principal para problemas de PdM, juntamente com seus métodos de construção de etiqueta específica. Tenha em atenção que uma técnica de modelagem único pode ser usada em vários setores diferentes. A técnica de modelagem está emparelhada o problema de ciência de dados, em vez do contexto dos dados em questão.

> [!IMPORTANT]
> A escolha de etiquetas para os casos de falha e a estratégia de etiquetagem  
> deve ser determinada após a consulta com a especialista de domínio.

### <a name="binary-classification"></a>Classificação binária
A classificação binária é usada para _prever a probabilidade de um equipamento falhar num período de tempo futuro_ - chamado período horizonte futuro _X_. X é determinado pelo problema do negócio e pelos dados em questão, em consulta com o especialista em domínio. Os exemplos são:
- _tempo mínimo_ de acesso necessário para substituir componentes, implantar recursos de manutenção, realizar manutenção para evitar um problema que é provável que ocorra nesse período.
- _contagem mínima de eventos_ que podem acontecer antes que um problema ocorra.

Nesta técnica, os dois tipos de exemplos de treinamento são identificados. Um exemplo positivo, _que indica uma falha,_ com etiqueta = 1. Um exemplo negativo, o que indica que as operações normais, com etiqueta = 0. A variável-alvo, e daí os valores do rótulo, são _categóricas._ O modelo deve identificar cada novo exemplo probabilidade de falhar ou a funcionar normalmente nos próximos X unidades de tempo.

#### <a name="label-construction-for-binary-classification"></a>Construção de etiqueta de classificação binária
A questão aqui é: "o que é a probabilidade de que o elemento falhará no prazo de X unidades de tempo?" Para responder a essa pergunta, registos de etiqueta X antes da falha de um ativo como "prestes a falhar" (etiqueta = 1) e todos os outros registos como sendo "normal" da etiqueta (etiqueta = 0). (veja a figura 3).

![Figura 3. Etiquetas de classificação binária](./media/predictive-maintenance-playbook/labelling-for-binary-classification.png)

Figura 3. Etiquetas de classificação binária

Seguem-se exemplos de etiquetagem estratégia para alguns dos casos de utilização.
- _Atrasos_no voo : X pode ser escolhido como um dia, para prever atrasos nas próximas 24 horas. Em seguida, todos os voos que estão dentro de 24 horas antes de falhas são identificados como 1.
- Falhas de dispensa de _caixa multibanco_: Um objetivo pode ser determinar a probabilidade de falha de uma transação na próxima hora. Nesse caso, todas as transações que ocorreram na última hora da falha são identificadas como 1. Para prever a probabilidade de falha sobre a moeda de N seguinte notas dispensed, todas as anotações dispensed dentro as últimas notas de N de uma falha são identificadas como 1.
- _Falhas no disjuntor_: O objetivo pode ser prever a próxima falha do comando do disjuntor. Nesse caso, X é escolhido para ser um comando futuro.
- _Falhas na porta do comboio:_ X pode ser escolhido como dois dias.
- _Falhas_na turbina eólica : X pode ser escolhido como dois meses.

### <a name="regression-for-predictive-maintenance"></a>Regressão para manutenção preditiva
Os modelos de regressão são usados para _calcular a vida útil restante (RUL) de um ativo_. RUL é definido como a quantidade de tempo que um recurso está operacional antes de ocorre a falha seguinte. Cada exemplo de treino é um registo que pertence a uma unidade de tempo _nY_ para um ativo, onde _n_ é o múltiplo. O modelo deve calcular o RUL de cada novo exemplo como um _número contínuo_. Este número indica o período de tempo restante antes da falha.

#### <a name="label-construction-for-regression"></a>Etiqueta de construção para regressão
A questão aqui é: "Qual é a vida útil remanescente (RUL) do equipamento?" Para cada registo antes da falha, calcule a etiqueta a ser o número de unidades de tempo restante antes da falha seguinte. Nesse método, as etiquetas são variáveis contínuas. (Consulte a figura 4)

![Figura 4. Etiquetagem para regressão](./media/predictive-maintenance-playbook/labelling-for-regression.png)

Figura 4. Etiquetagem para regressão

Para regressão, a etiquetagem é feita com referência a um ponto de falha. Não é possível efetuar os seus cálculos sem saber quanto o elemento Sobreviveu antes de uma falha. Então, por outro lado de classificação binária, ativos sem quaisquer falhas nos dados não podem ser utilizados para a Modelagem. Esta questão é melhor abordada por outra técnica estatística chamada [Survival Analysis](https://en.wikipedia.org/wiki/Survival_analysis). Mas possíveis complicações podem surgir ao aplicar essa técnica para casos de utilização de PdM que envolvam dados de variáveis de tempo de mensagens em fila com intervalos freqüentes. Para obter mais informações sobre análise de sobrevivência, consulte [este pager de uma página](https://www.cscu.cornell.edu/news/news.php/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificação de Roc para manutenção preditiva
Técnicas de classificação de Roc podem ser utilizadas em soluções de PdM para dois cenários:
- Prever _dois resultados futuros_: O primeiro resultado é um intervalo de tempo para _falhar_ num ativo. O elemento é atribuído a um dos vários períodos possíveis de tempo. O segundo resultado é a probabilidade de falhar num período futuro devido a _uma das múltiplas causas._ Esta predição permite tripulação manutenção acompanhar os sintomas e agendamentos de manutenção do plano.
- Preveja _a causa mais provável_ de uma dada falha. Esse resultado recomenda o conjunto certo de ações de manutenção para corrigir uma falha. Uma lista ordenada de causas de raiz e reparos recomendados pode ajudar a priorizar suas ações de reparo após uma falha de técnicos.

#### <a name="label-construction-for-multi-class-classification"></a>Construção de etiqueta de classificação de Roc
A questão aqui é: "Qual é a probabilidade de um ativo falhar nas próximas unidades de tempo _nZ_ onde _n_ é o número de períodos?" Para responder a essa pergunta, da etiqueta nZ registos antes da falha de um recurso com os registos de tempo (3Z, 2Z, Z). Etiqueta, todos os outros registos como "normal" (etiqueta = 0). Neste método, a variável-alvo detém valores _categóricos._ (Consulte a figura 5).

![Figura 5. Etiquetas de previsão do tempo de insucesso para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Figura 5. Etiquetas para classificação de Roc para predição de falha de tempo

A questão aqui é: "Qual é a probabilidade de o ativo falhar nas próximas unidades X de tempo devido à causa raiz/problema _P<sub>i?"</sub>_ onde _eu_ é o número de possíveis causas de raiz. Para responder a esta pergunta, rotule os registos X antes da falha de um ativo como "prestes a falhar devido à causa raiz _P<sub>i"</sub>_ (etiqueta = _P<sub>i</sub>_ ). Todos os outros registos como sendo "normal" da etiqueta (etiqueta = 0). Nesse método além disso, as etiquetas são categóricos (consulte figura 6).

![Figura 6. Rótulos de previsão de causa seleção para classificação multiclasse](./media/predictive-maintenance-playbook/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Figura 6. Etiquetas para classificação de Roc para predição de causa raiz

O modelo atribui uma probabilidade de falha devido a cada _P<sub>i,</sub>_  bem como a probabilidade de não falha. Estas probabilidades podem ser ordenadas por magnitude para permitir que a previsão dos problemas que é mais provável que ocorram no futuro.

A questão aqui é: "que ações de manutenção recomenda após uma falha?" Para responder a esta pergunta, a rotulagem _não requer que seja escolhido um horizonte futuro_, porque o modelo não prevê falhas no futuro. Está apenas a prever a causa mais provável _quando a falha já aconteceu._

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Treinamento, validação e métodos de teste para manutenção preditiva
O Processo de Ciência de Dados da [Equipa](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) proporciona uma cobertura completa do ciclo modelo de validação de testes de comboio. Esta secção aborda aspetos exclusivos para PdM.

### <a name="cross-validation"></a>Validação cruzada
O objetivo da [validação cruzada](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) é definir um conjunto de dados para "testar" o modelo na fase de treino. Este conjunto de dados é chamado de conjunto de _validação_. Esta técnica ajuda a limitar problemas como o excesso de _encaixe_ e dá uma ideia de como o modelo se generalizará para um conjunto de dados independente. Ou seja, um desconhecido conjunto de dados, que pode ser um problema real. A rotina de treinamento e teste para PdM precisa levar em conta os aspetos de variadas de tempo para generalizar melhor sobre dados futuros que não foram vistos.

Muitos algoritmos de machine learning dependem de um número de hiperparâmetros que podem ser alterado significativamente o desempenho do modelo. Os valores ideal destes hiperparâmetros não são calculados automaticamente quando o modelo de formação. Devem ser especificados por cientista de dados. Existem várias formas de localizar os valores válidos de hiperparâmetros.

A mais comum é a _validação cruzada k-fold_ que divide os exemplos aleatoriamente em _dobras k._ Para cada conjunto de valores de hiperparâmetros, executar o algoritmo de aprendizagem _k_ vezes. Em cada iteração, utilize os exemplos em subconjuntos de validação atual como um conjunto de validação e o resto dos exemplos como um conjunto de treinamento. Preparar o algoritmo através de exemplos de treinamento e as métricas de desempenho de computação através de exemplos de validação. No final deste ciclo, calcule a média das métricas de desempenho _k._ Para cada conjunto de valores de hiper-parâmetros, escolha aqueles que têm o melhor desempenho médio. A tarefa de escolher hiperparâmetros, muitas vezes, é experimental por natureza.

Problemas de PdM, dados são registados como uma série de tempo de eventos provenientes de várias fontes de dados. Estes registos podem ser ordenados de acordo com o tempo de etiquetagem. Assim, se o conjunto de dados for dividido _aleatoriamente_ em conjunto de formação e validação, _alguns dos exemplos de formação podem ser posteriores no tempo do que alguns exemplos de validação._ O desempenho futuro dos valores do hiperparâmetro será estimado com base em alguns dados que chegaram _antes_ do modelo ser treinado. Essas estimativas podem ser muito otimista, especialmente se a série de tempo não é estacionários e evolui ao longo do tempo. Como resultado, os valores de hiper-parâmetros escolhido podem ser inferior ao ideal.

A forma recomendada é dividir os exemplos em formação e validação definidos de forma _dependente do tempo,_ onde todos os exemplos de validação são posteriores no tempo do que todos os exemplos de formação. Para cada conjunto de valores de hiper-parâmetros, Treine o algoritmo ao longo do conjunto de dados de treinamento. Meça o desempenho do modelo no mesmo conjunto de validação. Escolha os valores de hiper-parâmetros que mostram o melhor desempenho. Valores de hiper-parâmetros escolhidos pelo resultado da divisão de train/validação num desempenho melhor modelo futuro que com os valores escolhidos aleatoriamente pela validação cruzada.

O modelo final pode ser gerado por um algoritmo de aprendizagem de treinamento sobre os dados de treinamento inteiro com os melhores valores de hiper-parâmetros.

### <a name="testing-for-model-performance"></a>Testes de desempenho do modelo
Quando é criado um modelo, uma estimativa de seu desempenho futura em novos dados, é necessária. Uma boa estimativa é a métrica de desempenho de valores de hiper-parâmetros calculada com o conjunto de validação, ou uma métrica de desempenho médio calculada da validação cruzada. Estas estimativas costumam ser excessivamente otimista. A empresa, muitas vezes, pode ter algumas diretrizes adicionais sobre o deseja testar o modelo.

A forma recomendada para o PdM é dividir os exemplos em conjuntos de dados de formação, validação e teste de forma _dependente do tempo._ Todos os exemplos de teste devem ser posteriores no tempo que todos os exemplos de treinamento e validação. Após a divisão, gerar o modelo e medir o desempenho dele, conforme descrito anteriormente.

Quando a série de tempo é estacionários e fáceis de prever, abordagens aleatórias e dependente de tempo geram similares estimativas de desempenho futura. Mas quando séries de tempo não estacionários e/ou difícil de prever, a abordagem de dependente de tempo irá gerar mais realistas estimativas de desempenho futura.

### <a name="time-dependent-split"></a>Divisão de dependente de tempo
Esta secção descreve as melhores práticas para implementar a divisão de dependente de tempo. Uma divisão de bidirecional dependente de tempo entre conjuntos de testes e treinamento é descrita abaixo.

Suponha que um fluxo de eventos de timestamped como medidas de vários sensores. Defina os recursos e as etiquetas de treinamento e exemplos de teste ao longo de períodos de tempo que contêm vários eventos. Por exemplo, para a classificação binária, crie funcionalidades baseadas em eventos passados e crie rótulos baseados em eventos futuros dentro de unidades de tempo "X" no futuro (ver as secções sobre técnicas de engenharia de [recursos](#feature-engineering) e técnicas de modelação). Portanto, o etiquetagem período de tempo de um exemplo vem depois que o intervalo de tempo de seus recursos.

Para uma divisão dependente do tempo, escolha um tempo de corte de _treino T<sub>c</sub>_  no qual treinar um modelo, com hiperparâmetros afinados usando dados históricos até T<sub>c</sub>. Para evitar fugas de futuras etiquetas que estão para além de T<sub>c</sub> nos dados de formação, escolha o mais recente tempo para rotular exemplos de formação como unidades X antes de T<sub>c</sub>. No exemplo mostrado na figura 7, cada quadrado representa um registro no conjunto de dados em que as funcionalidades e as etiquetas são computadas, tal como descrito acima. A figura mostra os registos que devem passar à preparação e teste conjuntos para X = 2 e W = 3:

![Figura 7. Dividir a classificação binário dependente de tempo](./media/predictive-maintenance-playbook/time-dependent-split-for-binary-classification.png)

Figura 7. Dividir a classificação binário dependente de tempo

Os quadrados em verde representam registos que pertencem a unidades de tempo que podem ser usadas para treinamento. Cada exemplo de formação é gerado tendo em conta os últimos três períodos de geração de recursos, e dois períodos futuros para a rotulagem antes de T<sub>c</sub>. Quando qualquer parte dos dois períodos futuros for além do T<sub>c,</sub>exclua esse exemplo do conjunto de dados de formação porque não se assume visibilidade para além de T<sub>c</sub>.

Os quadrados pretas representam os registos do conjunto de dados etiquetado final que não deve ser utilizada no conjunto de dados de treinamento, tendo em conta a restrição acima. Estes registos também não serão utilizados nos dados de teste, uma vez que são antes de T<sub>c</sub>. Além disso, os períodos de tempo de etiquetas parcialmente dependem do período de tempo de treinamento, que não é ideal. Dados de preparação e teste devem ter períodos de tempo de etiquetas separados para impedir o vazamento de informações da etiqueta.

A técnica discutida até agora permite a sobreposição entre exemplos de treino e teste que têm selos temporais perto de T<sub>c</sub>. Uma solução para alcançar uma maior separação é excluir exemplos que estão dentro das unidades de tempo W de T<sub>c</sub> do conjunto de teste. Mas esse uma divisão agressiva depende da disponibilidade de dados de uma ampla.

Utilizado para prever a RUL de modelos de regressão são mais gravemente afetados pelo problema de vazamento. Usar o método split aleatório leva a extreme ajuste excessivo. Para problemas de regressão, a divisão deve ser tal que os registos pertencentes a ativos com falhas antes de T<sub>c</sub> entrar no conjunto de treino. Registos de recursos que têm falhas após o limite de ir para o conjunto de teste.

Outra prática recomendada para a divisão de dados para preparação e teste é usar uma divisão por ID de recurso. A divisão deve ser, de modo a que nenhum dos ativos utilizados no conjunto de treinamento são usados em testes de desempenho do modelo. Com essa abordagem, um modelo tem uma melhor oportunidade de fornecer resultados mais realistas com novos recursos.

### <a name="handling-imbalanced-data"></a>Manipular dados desequilibrados
Nos problemas de classificação, se houver mais exemplos de uma classe do que de outras, diz-se que o conjunto de dados está _desequilibrado._ O ideal é que suficiente representantes de cada classe nos dados de treinamento são preferenciais para ativar a diferenciação entre diferentes classes. Se uma classe for inferior a 10% dos dados, os dados for considerados desequilibrado. A classe sub-representada é chamada de _classe minoritária._

Muitos problemas de PdM enfrentam esses desequilibrado conjuntos de dados, onde uma classe é gravemente sub-representadas em comparação comparados outros classe ou classes. Em algumas situações, a classe minoritários poderão constituir apenas 0,001% dos total de pontos de dados. Não é exclusivo para PdM desequilíbrio de classe. Outros domínios onde falhas e anomalias são raras ocorrências enfrentam um problema semelhante, para obter exemplos, deteção de fraudes e intrusão de rede. Estas falhas constituem exemplos de classe minoritários.

Com desequilíbrio de classe nos dados, desempenho de algoritmos de aprendizagem mais padrão for comprometido, uma vez que eles têm como objetivo minimizar a taxa de erro geral. Para um conjunto de dados com 99% negativo e exemplos positivos de 1%, um modelo pode ser exibido para que a precisão de 99% por etiquetagem todas as instâncias como negativa. Mas o modelo enganássemos classifica todos os exemplos positivos; por isso, mesmo que a precisão é elevada, o algoritmo não é útil. Consequentemente, as métricas de avaliação convencionais, tais como a _precisão global da taxa de erro,_ são insuficientes para uma aprendizagem desequilibrada. Quando enfrentar desequilibrados conjuntos de dados, outras métricas são utilizadas para a avaliação do modelo:
- Precisão
- Recuperar
- F1 pontuações
- Custo ajustado ROC (características operacionais de destinatário)

Para obter mais informações sobre estas métricas, consulte a avaliação do [modelo.](#model-evaluation)

No entanto, existem alguns métodos que o ajudam a classe de resolver o problema de desequilíbrio. As duas principais são _técnicas de amostragem_ e _aprendizagem sensível aos custos._

#### <a name="sampling-methods"></a>Métodos de amostragem
Learning desequilibrado envolve o uso de métodos para modificar o conjunto de dados de treinamento para um conjunto de dados com balanceamento de amostragem. Métodos de amostragem são para não ser aplicada ao conjunto de teste. Embora existam várias técnicas de amostragem, a maioria das técnicas de recolha de amostras são _aleatórias_ e _sob amostragem._

_A sobreamostração aleatória_ envolve selecionar uma amostra aleatória da classe minoritária, replicar estes exemplos e adicioná-los ao conjunto de dados de treino. Conseqüentemente, o número de exemplos na classe minoritários aumenta e, eventualmente, equilibrar o número de exemplos de diferentes classes. Uma desvantagem de oversampling é que múltiplas instâncias de determinados exemplos podem fazer com que o classificador para se tornar muito específicos, que leva a ajuste excessivo. O modelo pode mostrar a precisão de treinamento de alta, mas seu desempenho em dados de teste que não foram vistos pode ser inferior ao ideal.

Inversamente, _aleatoriamente sob amostragem_ está selecionando uma amostra aleatória de uma classe maioritária e removendo esses exemplos de conjunto de dados de treino. No entanto, a remoção de exemplos da classe de maioria pode fazer com que o classificador de perder a conceitos importantes relativas à classe maioria. _A amostragem híbrida_ em que a classe minoritária é sobre-amostrada e a classe maioritária é sub-amostrada ao mesmo tempo é outra abordagem viável.

Há muitas técnicas de amostragem sofisticadas. A técnica escolhida depende as propriedades de dados e os resultados de experimentações iterativos, cientista de dados.

#### <a name="cost-sensitive-learning"></a>Learning confidencial de custos
No PdM, falhas que constituem a classe minoritários são mais interessantes que exemplos normais. Portanto, o foco está principalmente no desempenho do algoritmo sobre falhas. Prever incorretamente uma classe positiva como uma classe negativa pode custar mais do que vice versa. Esta situação é normalmente chamada desiguais perda ou assimétrico custo dos elementos enganássemos classificar para diferentes classes. O classificador ideal deve disponibilizam exatidão da previsão elevada através da classe minoritários, sem comprometer a precisão para a classe da maioria.

Existem várias formas de alcançar este equilíbrio. Para mitigar o problema de perda desiguais, atribuir um alto custo a classificação incorrecta da classe minoritários e tentar minimizar o custo geral. Algoritmos como _SVMs (Máquinas de Vetor_ de Suporte) adotam este método inerentemente, permitindo que o custo de exemplos positivos e negativos seja especificado durante o treino. Da mesma forma, o reforço de métodos como árvores de _decisão reforçadas_ geralmente mostram um bom desempenho com dados desequilibrados.

## <a name="model-evaluation"></a>Avaliação do modelo
Classificação incorrecta é um problema significativo para cenários de PdM onde o custo de falsos alarmes para a empresa é elevado. Por exemplo, uma decisão a partir do zero com base numa previsão incorreta de falha do motor de uma aeronave pode interromper agendas e planos de viagens. Colocar offline uma máquina de uma linha de montagem pode levar à perda de receita. Portanto, a avaliação do modelo com as métricas de desempenho certo contra novos dados de teste é fundamental.

Métricas de desempenho típica utilizadas para avaliar os modelos de PdM são abordadas abaixo:

- [A precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision) é a métrica mais popular usada para descrever o desempenho de um classificador. Mas precisão é sensível a distribuições de dados e é uma medida ineficaz para cenários com desequilibrado conjuntos de dados. Outras métricas são utilizadas em vez disso. Ferramentas como [a matriz](https://en.wikipedia.org/wiki/Confusion_matrix) de confusão são usadas para calcular e raciocinar sobre a precisão do modelo.
- [A precisão](https://en.wikipedia.org/wiki/Precision_and_recall) dos modelos PdM diz respeito à taxa de alarmes falsos. Menor precisão do modelo geralmente corresponde a uma taxa elevada de alarmes falsos.
- [A](https://en.wikipedia.org/wiki/Precision_and_recall) taxa de recolha denotas quantas das falhas no conjunto de testes foram corretamente identificadas pelo modelo. Taxas de recolhimento mais elevadas significa que o modelo é efetuada com êxito na identificação de falhas de verdade.
- [A pontuação de F1](https://en.wikipedia.org/wiki/F1_score) é a média harmónica de precisão e recordação, com o seu valor a variar entre 0 (pior) e 1 (melhor).

Para classificação binária,
- [As curvas de funcionamento do recetor (ROC)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) também são uma métrica popular. Em curvas de cor MULTICLASSE, o desempenho do modelo é interpretado com base em um fixo ponto operacional no ROC.
- Mas para os problemas de PdM, _tabelas decile_ e gráficos de _elevação_ são mais informativos. Concentre-se apenas na classe positiva (de falhas) e fornecer uma visão mais complexa do desempenho do algoritmo de curvas ROC.
  - _As tabelas decile_ são criadas usando exemplos de teste numa ordem descendente de probabilidades de falha. Os exemplos ordenados, em seguida, são agrupados em deciles (10% das amostras com maior probabilidade, em seguida, 20%, 30% e assim por diante). O /(random baseline) rácio (taxa positivo verdadeiro) para cada decile ajuda a estimar o desempenho do algoritmo em cada decile. A linha de base aleatória assume valores 0,1 0,2 e assim por diante.
  - [Os gráficos](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) de elevação traçam a taxa positiva decile contra a taxa positiva aleatória para todos os deciles. As primeira deciles geralmente são o foco dos resultados, uma vez que mostrem os ganhos de maior. Primeira deciles também podem ser vistos como representativo para "em risco," quando utilizada para PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalização de modelo para manutenção preditiva

O benefício que o exercício de ciência de dados é percebido apenas quando o modelo preparado é feito operacional. Ou seja, o modelo tem de ser implementado nos sistemas de negócio para fazer predições com base nos dados de novo, anteriormente que não foram vistos.  Os novos dados devem estar exatamente em conformidade com a _assinatura_ modelo do modelo treinado de duas formas:
- todos os recursos tem de estar presentes em cada instância lógica (por exemplo, uma linha numa tabela) de novos dados.
- os novos dados tem de ser previamente processados, e cada uma das funcionalidades desenvolvido, exatamente da mesma forma como os dados de treinamento.

O processo acima é indicado de muitas formas na literatura para instituições académicas e do setor. No entanto, as seguintes instruções significam a mesma coisa:
- _Marque novos dados_ usando o modelo
- _Aplicar o modelo_ a novos dados
- _Operacionalizar_ o modelo
- _Implementar_ o modelo
- _Executar o modelo_ contra novos dados

Como já declarado, operacionalização de modelo para PdM é diferente de seus colegas. Cenários que envolvem deteção de anomalias e deteção de falhas normalmente implementam _pontuação on-line_ (também chamado de pontuação em _tempo real)._ Aqui, o modelo _marca_ cada recorde de entrada, e devolve uma previsão. Para deteção de anomalias, a predição é uma indicação de que ocorreu uma anomalia (exemplo: SVM de uma classe). Para deteção de falhas, seria o tipo ou de uma classe de falha.

Em contraste, o PdM envolve _pontuação_de lote . Para estar em conformidade com a assinatura de modelo, os recursos novos dados tem de ser desenvolvidos da mesma forma como os dados de treinamento. Para grandes conjuntos de dados que é normais para novos dados, os recursos são agregados ao longo de janelas de tempo e classificados no batch. A pontuação do lote é normalmente feita em sistemas distribuídos como [Spark](https://spark.apache.org/) ou [Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Existem duas alternativas - ambos inferior ao ideal:
- Mecanismos de transmissão em fluxo de dados suportam a agregação ao longo do windows na memória. Então, poderíamos argumentar que suportam a classificação online. Mas estes sistemas são adequados para dados densos em estreitas janelas de tempo ou elementos dispersos ao longo do windows mais amplas. Eles podem dimensiona bem para os dados densos sobre janelas de tempo maiores, como visto em cenários de PdM.
- Se a classificação de lote não estiver disponível, a solução é adaptar online de classificação para lidar com novos dados em pequenos lotes de cada vez.

## <a name="solution-templates-for-predictive-maintenance"></a>Modelos de soluções para manutenção preditiva

A seção final deste guia fornece uma lista de modelos de soluções PdM, tutoriais e experiências implementadas no Azure. Esses aplicativos PdM podem ser implementados numa subscrição do Azure numa questão de minutos em alguns casos. Eles podem ser usados como demonstrações de prova de conceito, áreas de segurança para experimentar alternativas ou accelerators para implementações de produção real. Estes modelos estão localizados na [Galeria Azure AI](https://gallery.azure.ai) ou [no Azure GitHub.](https://github.com/Azure) Estes exemplos diferentes serão implementados ao longo do tempo para este modelo de solução.

| # | Título | Descrição |
|--:|:------|-------------|
| 2 | [Modelo de solução de manutenção preditiva azure](https://github.com/Azure/AI-PredictiveMaintenance) | Um modelo de solução de código aberto que demonstra a modelação Azure ML e uma infraestrutura Azure completa capaz de suportar cenários de manutenção preditiva no contexto da monitorização remota IoT. |
| 3 | [Aprendizagem Profunda para Manutenção Preditiva](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | O Azure Notebook com uma solução de demonstração de utilização de redes LSTM (Memória de Longo Prazo) (uma classe de Redes Neurais Recorrentes) para Manutenção Preditiva, com uma publicação de [blogue nesta amostra.](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance)|
| 4 | [Guia de Modelação de Manutenção Preditiva em R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guia de modelagem de PdM com scripts em R.|
| 5 | [Manutenção Preditiva Azure para Aeroespacial](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Um dos modelos de soluções de PdM primeiro com base na versão 1.0 do Azure ML para manutenção de aeronave. Este guia originado este projeto. |
| 6 | [Kit de ferramentas Azure AI para IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | IA na Borda IoT utilizando o TensorFlow; o toolkit embala modelos de aprendizagem profunda em recipientes Docker compatíveis com bordas Azure IoT e expõe esses modelos como APIs REST.
| 7 | [Manutenção Preditiva Azure IoT](https://github.com/Azure/azure-iot-predictive-maintenance) | O Azure IoT Suite PCS – solução pré-configurada. Modelo de PdM de manutenção das aeronaves com o IoT Suite. [Outro documento](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) e [passagem](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) relacionadocom o mesmo projeto. |
| 8 | [Modelo de manutenção preditiva usando serviços R do Servidor SQL](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstração de cenário de vida útil restante com base nos serviços de R. |
| 9 | [Guia de Modelação de Manutenção Preditiva](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Recurso de conjunto de dados de manutenção de aeronaves projetado utilizando R com [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) e [conjuntos](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) de dados e [caderno Azure](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) e [experiências](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2) em AzureML v1.0|

## <a name="training-resources-for-predictive-maintenance"></a>Recursos de treinamento para manutenção preditiva

O Microsoft Azure oferece os percursos de aprendizagem para os conceitos básicos por trás de técnicas de PdM, além de conteúdo e treinamento em geral, os conceitos de IA e prática.

| Recursos de treinamento  | Disponibilidade |
|:-------------------|--------------|
| [Caminho de aprendizagem para PdM usando árvores e floresta aleatória](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Público | 
| [Caminho de Aprendizagem para PdM usando deep learning](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Público |
| [Desenvolvedor de IA em Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Público |
| [Microsoft AI School](https://aischool.microsoft.com/learning-paths) | Público |
| [Aprendizagem de IA Azure do GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Público |
| [Aprendizagem linkedIn](https://www.linkedin.com/learning) | Público |
| [Microsoft AI YouTube Webinars](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Público |
| [Microsoft AI Show](https://channel9.msdn.com/Shows/AI-Show) | Público |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Parceiros |
| [Microsoft Partner Network](https://learningportal.microsoft.com) | Parceiros |

Além disso, MOOCS gratuitas (cursos abertos massivos online) em IA estão online oferecidas por instituições académicas como Stanford e MIT e outras empresas de instituições de ensino.
