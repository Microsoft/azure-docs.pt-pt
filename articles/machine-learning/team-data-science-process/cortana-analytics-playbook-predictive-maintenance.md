---
title: Guia de IA do Azure para soluções de manutenção preditiva - Team Data Science Process
description: Uma descrição abrangente de ciência de dados que capacita a soluções de manutenção preditiva em várias indústrias verticais.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 05/11/2018
ms.author: tdsp
ms.custom: seodec18, previous-author=fboylu, previous-ms.author=fboylu
ms.openlocfilehash: 046d00eec9fd5a406a1e8bf78e48d4dd913b83fd
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57437573"
---
# <a name="azure-ai-guide-for-predictive-maintenance-solutions"></a>Guia de IA do Azure para soluções de manutenção preditiva

## <a name="summary"></a>Resumo

Manutenção preditiva (**PdM**) é um aplicativo popular de Análise Preditiva que pode ajudar as empresas em várias indústrias a atingir uma utilização elevada ativo e economia nos custos operacionais. Este guia reúne empresariais e analíticas diretrizes e melhores práticas para desenvolver e implementar soluções de PdM usando com êxito a [plataforma de IA do Azure de Microsoft](https://azure.microsoft.com/overview/ai-platform) tecnologia.

Para os iniciantes, este Guia apresenta cenários comerciais específicos da indústria e o processo de qualificação estes cenários para PdM. Os requisitos de dados e a técnicas de modelação para criar soluções de PdM também são fornecidas. O conteúdo principal do guia é o processo de ciência de dados - incluindo os passos de preparação de dados, engenharia de funcionalidades, criação de um modelo e operacionalização de modelo. Para complementar estes conceitos-chave, este Guia apresenta uma lista de um conjunto de modelos de soluções que ajudam a acelerar o desenvolvimento de aplicativos PdM. O guia também aponta para os recursos de treinamento útil para o profissional saber mais sobre a IA por trás de ciência de dados. 

### <a name="data-science-guide-overview-and-target-audience"></a>Dados ciência guia visão geral e de destino público-alvo
A primeira metade deste guia descreve os problemas de negócios típicos, os benefícios da implementação PdM para resolver estes problemas e apresenta uma lista de alguns casos de utilização comuns. Os tomadores de decisões comerciais (BDMs) irão beneficiar deste conteúdo. A segunda metade explica a ciência de dados por trás PdM e fornece uma lista de soluções de PdM criadas usando os princípios descritos neste guia. Ele também fornece os percursos de aprendizagem e ponteiros para o material de treinamento. Os tomadores de decisões técnicas (TDMs) irão encontrar este conteúdo útil.

| Começar com... | Se for... |
|:---------------|:---------------|
| [Caso comercial para manutenção preditiva](#Business-case-for-predictive-maintenance) |uma responsável pelas decisões comerciais (BDM) pretende reduzir os custos operacionais e tempo de inatividade e melhorar a utilização de equipamentos |
| [Ciência de dados para manutenção preditiva](#Data-Science-for-predictive-maintenance) |um tomador de decisões técnicas (TDM) avaliar tecnologias PdM para compreender os requisitos de ia para manutenção preditiva e de processamento de dados exclusivo |
| [Modelos de soluções para manutenção preditiva](#Solution-templates-for-predictive-maintenance)|um arquiteto de software ou um programador de IA à procura de criar rapidamente uma demonstração ou uma prova de conceito |
| [Recursos de treinamento para manutenção preditiva](#Training-resources-for-predictive-maintenance) | uma ou todas acima e quiser aprender os conceitos fundamentais da ciência de dados, ferramentas e técnicas.

### <a name="prerequisite-knowledge"></a>Conhecimento dos pré-requisitos
O conteúdo de BDM – não espera que o leitor tenha qualquer conhecimento de ciência de dados anterior. Para o conteúdo de TDM –, o conhecimento básico de estatísticas e ciência de dados é útil. É recomendável o conhecimento dos dados do Azure e serviços de IA, Python, R, XML e JSON. Técnicas de IA são implementadas nos pacotes de Python e R. Modelos de soluções são implementados por meio de serviços do Azure, ferramentas de desenvolvimento e SDKs.

## <a name="business-case-for-predictive-maintenance"></a>Caso comercial para manutenção preditiva

As empresas exigem equipamento crítico estar em execução na eficiência de pico e utilização para perceber seu retorno em investimentos de capitais. Esses ativos podem ir desde motores de aeronaves, turbines, elevadores ou chillers industriais - que custam milhões - a para baixo para aplicações de todos os dias como fotocopiadoras, máquinas de café ou coolers de água.
- Por predefinição, a maioria das empresas dependem _manutenção corretiva_, onde as partes são substituídas como e quando eles falharem. Manutenção corretiva garante partes são totalmente utilizadas (vida de componente, por conseguinte, não desperdiçando), custos, mas os negócios em tempo de inatividade, trabalho e os requisitos de manutenção agendada (desativado horas ou localizações inconvenientes).
- Na prática de empresas de nível, próximo _manutenção preventiva_, onde determinar o tempo de vida útil para uma peça e manter o ou substitua-o antes de uma falha. Manutenção preventiva evita falhas catastróficas e não agendadas. Mas os custos elevados de período de indisponibilidade planeado, permanecem de utilização insuficientemente do componente antes de seu ciclo de vida completo de uso e o trabalho ainda.
- O objetivo de _manutenção preditiva_ é otimizar o equilíbrio entre manutenção corretiva e preventiva, ao permitir _just-in-time_ substituição dos componentes. Essa abordagem substitui apenas esses componentes quando eles estiverem prestes uma falha. Ao expandir empreguem componente (comparada com a manutenção preventiva) e reduzir os custos de trabalho e de manutenção agendada (através de manutenção corretiva), as empresas podem obter reduções de custos e vantagens competitivas.

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

- _os cientistas de dados_ para analisar e resolver problemas específicos de previsão.
- _arquitetos e desenvolvedores de nuvem_ juntar-se de uma solução ponto a ponto.

## <a name="qualifying-problems-for-predictive-maintenance"></a>Qualificação problemas para manutenção preditiva
É importante enfatizar que nem todos os casos de utilização ou problemas de negócios podem ser resolvidos com eficiência por PdM. Existem três critérios de qualificação importantes que precisam ser consideradas durante a seleção de problema:

- O problema tem de ser preditiva por natureza; ou seja, deve haver um destino ou um resultado para prever. O problema também deve ter um caminho claro de ação para evitar falhas quando eles são detetados.
- O problema deve ter um registo de histórico de operacional do equipamento que contém _bons e maus resultados_. O conjunto de ações executadas para atenuar os resultados incorretos também deve estar disponível como parte destes registos. Relatórios de erros, registos de manutenção de degradação do desempenho, reparar e registos de substituição também são importantes. Além disso, os reparos realizados para melhorá-los e registos de substituição também são úteis.
- A história deve se refletir no _relevantes_ dados de _suficiente_ suficiente qualidade para suportar o caso de utilização. Para obter mais informações sobre a relevância de dados e sufficiency, consulte [requisitos de dados para manutenção preditiva](#Data-requirements-for-predictive-maintenance).
- Por fim, a empresa deve ter os especialistas de domínio que tenham um entendimento claro do problema. Eles devem estar cientes dos processos internos e práticas que poderão ajudar a analista de compreender e interpretar os dados. Eles também devem ser capazes de fazer as alterações necessárias para processos de negócio existentes para o ajudar a recolher os dados corretos para os problemas, se necessário.

## <a name="sample-pdm-use-cases"></a>Casos de utilização de PdM de exemplo
Esta seção se concentra numa coleção de PdM casos de utilização de vários setores, tais como indústria aeroespacial, utilitários e transportes. Cada seção começa com um problema de negócios e aborda os benefícios de PdM, os dados relevantes que o problema comercial e, finalmente, os benefícios de uma solução de PdM.

| Problema de negócios | Benefícios do PdM |
|:-----------------|-------------------|
|**Aviation**      |                   |
|_Andamento a atraso e cancelamentos_ devido a problemas de mecânicos. Falhas que não não possível reparar no tempo podem causar voos sejam canceladas e perturbar o agendamento e de operações. |Soluções de PdM podem prever a probabilidade de uma aeronave a ser atrasado ou foi cancelada devido a falhas mecânicas.|
|_Falha de partes do motor de aeronave_: Substituições de parte do motor de aeronave estão entre as tarefas de manutenção mais comuns no sector dos transportes aéreos. Soluções de manutenção requerem a gestão de cuidado de disponibilidade de estoque de componentes, entrega e planejamento|A possibilidade de obter a inteligência de fiabilidade de componente leva à redução substancial nos custos de investimento.|
|**Finanças** |                         |
|_Falha de ATM_ é um problema comum no setor bancário. O problema aqui é a probabilidade de que uma transação de retirada de dinheiro de ATM for interrompida devido a uma falha de confusão ou parte do documento no fornecedor de pagamento à vista de relatório. Com base nas predições de falhas de transação, caixas eletrônicos podem ser reparados proativamente para evitar a ocorrência de falhas.| Em vez de permitir que a máquina para falhar a meio de uma transação, é a alternativa desejável programa a máquina para negar serviços com base na predição.|
|**Energia** |                          |
|_Faça isso falhas turbina_: Turbines vento são a origem de energia principal em países ambiental responsáveis e envolvem custos de capitais altos. Um componente chave no turbines do vento é o motor de gerador. sua falha renderiza a turbina ineficaz. Também é altamente Caro corrigir.|Prever KPIs, tais como MTTF (tempo médio de falha) pode ajudar as empresas de energia evitar falhas de turbina e certifique-se de período de indisponibilidade mínimo. Probabilidades de falha irão informar técnicos para monitorizar turbines que são provável que falhem em breve e agendar regimes baseados no tempo de manutenção. Modelos de previsão de fornecem informações sobre os diferentes fatores que contribuem para a falha, o que ajuda a técnicos de compreender melhor que as principais causas de problemas.|
|_Falhas de disjuntor automático_: Distribuição de eletricidade para casas e empresas requer linhas de power fique operacional em todos os momentos para garantir a entrega de energia. Separadores de circuito ajudar a limitar ou evitar danos à potência linhas durante sobrecarga ou adversos sobreviver aos condições. O problema de negócios aqui é prever falhas de disjuntor automático.| As soluções de PdM ajudam a reduzir os custos de reparação e aumentar a vida útil de equipamentos, tais como separadores de circuito. Eles ajudam a melhorar a qualidade da rede energia ao reduzir falhas inesperadas e interrupções do serviço.|
|**Transportes e logística** |    |
|_Falhas de porta do elevador_: As empresas de grande elevador fornecem um serviço de pilha completa para milhões de elevadores funcionais em todo o mundo. Tempo de atividade, confiabilidade e segurança elevador são as principais preocupações para seus clientes. Estas empresas controlam esses e vários outros atributos através de sensores, para ajudá-los com a manutenção preventiva e medidas corretiva. Num elevador, o problema mais proeminente do cliente está a funcionar incorretamente portas elevador. Neste caso é o problema comercial proporcionar um aplicativo de previsão de base de dados de conhecimento que prevê que o potencial causa de falhas de porta de.| Elevadores são investimentos de capitais para potencialmente um tempo de vida do ano de 20 a 30. Para que cada venda potencial possa ser altamente competitiva; Por conseguinte, as expectativas de suporte são elevadas. Manutenção preditiva pode dar a essas empresas com uma vantagem sobre seus concorrentes em seus produtos e ofertas de serviço.|
|_Roda falhas_: Conta de falhas de roda para metade de todos os treinar caos e milhares de milhões para o setor do global rail de custos. Falhas de roda também causam rails para prejudicado, às vezes, fazendo com que o rail quebrar prematuramente. Quebras de Rail gerar eventos catastróficos, como o caos. Para evitar essas instâncias, railways monitorizar o desempenho de rodas e substituí-los de forma preventiva. O problema de negócios aqui é a predição de falhas de roda.| Manutenção preditiva de rodas ajudará com just-in-time substituição de rodas |
|_Falhas de porta de comboio de metrô_: A razão principal para atrasos em operações de metrô é falhas de porta de carros de comboio. O problema de negócios aqui é prever falhas de porta de comboio.|Reconhecimento de início de uma falha de porta ou o número de dias até uma falha de porta, ajudará a otimização de negócios treinar porta agendamentos de manutenção.|

A secção seguinte obtém os detalhes de como obter os benefícios de PdM descritos acima.

## <a name="data-science-for-predictive-maintenance"></a>Ciência de dados para manutenção preditiva

Esta seção fornece diretrizes gerais dos princípios de ciência de dados e prática para PdM. Destina-se para o ajudar a um TDM –, arquiteto de soluções, ou um desenvolvedor compreender os pré-requisitos e o processo para criar aplicações de ia de ponto-a-ponto para PdM. Pode ler esta secção, juntamente com uma revisão as demonstrações e modelos de prova de conceito estão listados na [modelos de soluções para manutenção preditiva](#Solution-templates-for-predictive-maintenance). Em seguida, pode utilizar esses princípios e práticas recomendadas para implementar a sua solução de PdM no Azure.

> [!NOTE]
> Este guia não se destina a ensinar o leitor de ciência de dados. Várias origens úteis são fornecidas para ler mais na seção para [recursos de treinamento para manutenção preditiva](#Training-resources-for-predictive-maintenance). O [modelos de soluções](#Solution-templates-for-predictive-maintenance) listados no manual de demonstrar algumas dessas técnicas de ia para problemas de PdM específicos.

## <a name="data-requirements-for-predictive-maintenance"></a>Requisitos de dados para manutenção preditiva

O sucesso de qualquer learning depende de (a) a qualidade de que está sendo ensinado e (b) a capacidade do aprendiz. Modelos de previsão de aprender os padrões de dados históricos e prever resultados futuros com determinados probabilidade com base nesses padrões observados. Precisão de previsão de um modelo depende a relevância, sufficiency e qualidade dos dados de preparação e teste. Os novos dados, que é "classificados" com este modelo devem ter os mesmos recursos e o esquema dos dados de treinamento e teste. As características de funcionalidade (tipo, densidade, distribuição e assim por diante) de novos dados devem corresponder ao que os conjuntos de dados de preparação e teste. O foco desta seção é tais requisitos de dados.

### <a name="relevant-data"></a>Dados relevantes

Em primeiro lugar, os dados tem de ser _relevantes para o problema_. Considere a _roda falha_ utilizar maiúsculas e minúsculas discutidas acima - os dados de treinamento devem conter as funcionalidades relacionadas com as operações de roda. Se o problema era prever a falha do _system força_, os dados de treinamento devem abranger todos os componentes diferentes para o sistema de força. O primeiro caso destina-se um componente específico, ao passo que o segundo caso destina-se a falha de um subsistema de maior. A recomendação geral é projetar os sistemas de predição sobre componentes específicos, em vez de subsistemas maiores, uma vez que o último será ter mais dispersos dados. O especialista de domínio (consulte [qualificar problemas para manutenção preditiva](#Qualifying-problems-for-predictive-maintenance)) deve ajudar a selecionar os subconjuntos mais relevantes de dados para a análise. As origens de dados relevantes são abordadas mais detalhadamente em [preparação de dados para manutenção preditiva](#Data-preparation-for-predictive-maintenance).

### <a name="sufficient-data"></a>Dados suficientes
Duas perguntas feitas normalmente em relação a dados do histórico de falha: (1) "o número de eventos de falha é necessários para preparar um modelo?" (2) "o número de registos é considerado como"bastante"?" Não há nenhum respostas definitiva, mas apenas regras básicas. Para (1), mais o número de eventos de falha, melhor o modelo. Para (2), e o número exato de eventos de falha depende dos dados e o contexto do problema que está sendo resolvido. Mas, por outro lado, se uma máquina falhar com muita frequência, em seguida, a empresa irá substituí-lo, que irá reduzir as instâncias de falha. Aqui, novamente, a documentação de orientação do domínio especialistas é importante. No entanto, existem métodos para lidar com a edição da _eventos raro_. Eles serão discutidos na seção [tratamento de dados desequilibrados](#Handling-imbalanced-data).

### <a name="quality-data"></a>Dados de qualidade
A qualidade dos dados é crítica - a cada valor de atributo de previsão tem de ser _precisos_ em conjunto com o valor da variável de destino. Qualidade de dados é uma área bem estudada na gestão de estatísticas e dados e, por conseguinte, fora do âmbito deste guia.

> [!NOTE]
> Existem vários recursos e produtos empresariais qualidade de dados. Um exemplo de referências é apresentado abaixo:
> - Dasu, T, Johnson, extração de dados de t., exploratórios e limpezas de dados, Wiley, 2003.
> - [Análise de dados exploratório, da Wikipedia](https://en.wikipedia.org/wiki/Exploratory_data_analysis)
> - [Dados quantitativos Hellerstein, J, limpeza para grandes bancos de dados](http://db.cs.berkeley.edu/jmh/papers/cleaning-unece.pdf)
> - [loo de Jonge, I, van der, M, introdução a limpeza de dados com R](https://cran.r-project.org/doc/contrib/de_Jonge+van_der_Loo-Introduction_to_data_cleaning_with_R.pdf)

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
Histórico de manutenção de um ativo contém detalhes sobre os componentes substituídos, atividades de reparação executadas etc. Padrões de degradação de registos desses eventos. Ausência dessas informações cruciais nos dados de treinamento pode levar a resultados de modelo enganoso. Histórico de falhas também pode ser encontrado no histórico de manutenção como códigos de erro especial ou datas de encomenda de partes. Origens de dados adicionais que influenciam a padrões de falhas devem ser investigadas e fornecidas por especialistas de domínio.

#### <a name="machine-operating-conditions"></a>Condições de funcionamento da máquina
Dados de transmissão em fluxo de sensores com base (ou outros) do equipamento na operação são uma origem de dados importantes. Uma suposição-chave no PdM é que o estado de funcionamento de uma máquina degrada ao longo do tempo durante a operação de rotina. Espera-se que os dados contêm recursos variados de tempo que capturam a esse padrão de classificação por vencimento e quaisquer anomalias que leva à degradação. O aspecto temporal dos dados é necessário para o algoritmo para aprender a falha e os padrões da não-falhas ao longo do tempo. O algoritmo com base nos seguintes pontos de dados, aprende prever a quantos mais unidades de tempo que uma máquina pode continuar a funcionar antes de falhar.

#### <a name="static-feature-data"></a>Dados de recursos estáticos
Recursos estáticos são metadados sobre o equipamento. Os exemplos são o equipamento marca, modelo, produzidos e data, comece a data do serviço, a localização do sistema e outras especificações técnicas.

Exemplos de dados relevantes para o [casos de utilização de exemplo PdM](#Sample-PdM-use-cases) são apresentadas abaixo:

| Caso de Utilização | Exemplos de dados relevantes |
|:---------|---------------------------|
|_Atraso de voo e cancelamentos_ | Informações de rota de voo na forma de registos de página e de legs de voo. Dados de leg de voo incluem detalhes de encaminhamento, como a data de entrada/saída, hora, aeroporto, layovers etc. Registo de página inclui uma série de códigos de erro e manutenção registado pelo pessoal de manutenção do zero.|
|_Falhas de partes de motores de aeronaves_ | Os dados recolhidos a partir de sensores na aeronave que fornece informações sobre a condição de várias partes. Registos de manutenção ajudam a identificar quando ocorreram de falhas de componentes e quando elas foram substituídas.|
|_Falha de ATM_ | As leituras dos sensores para cada transação (depositar o pagamento à vista/verificação) e diz respeito à administração de pagamento à vista. Informações sobre a medição de lacuna entre notas, tenha em atenção a espessura, tenha em atenção a distância de chegada, selecione atributos etc. Registos de manutenção que fornecem códigos de erro, informações de reparação, última vez o fornecedor de pagamento à vista foi preenchida novamente.|
|_Falha de turbina do vento_ | Sensores controlam turbina como temperatura, direção do vento, power gerado, velocidade de gerador etc. Recolha de dados do vários vento turbines de farms de vento localizados em várias regiões. Normalmente, cada turbina terá vários as leituras dos sensores retransmitir medidas num intervalo de tempo fixo.|
|_Falhas de disjuntor automático_ | Registos de manutenção que incluem ações corretivas, preventivas e sistemática. Dados operacionais, que inclui automáticos e manuais comandos enviados para os separadores de circuito, tais como para ações de abertura e fechamento. Metadados do dispositivo como a data de fabrico, localização, modelo, etc. Especificações do disjuntor automático como níveis de tensão, geolocalização, condições de ambiente.|
|_Falhas de porta de elevator_| Elevador metadados, tais como o tipo de elevador, produzidos e data, frequência de manutenção, tipo de construção e assim por diante. Informações operacionais, como o número de ciclos de porta, tempo médio de porta de fechar. Histórico de falhas com as causas.|
|_Falhas de roda_ | Dados de sensor que roda de medidas de aceleração, instâncias braking, motriz distância, velocidade, etc. Informações estáticas no rodas como fabricante, fabricados data. Dados de falha inferidos da base de dados de ordem de parte que controlam as datas de pedido e as quantidades.|
|_Falhas de porta de comboio de metrô_ | Abertura de porta e os tempos de fechamento, outros dados operacionais, como condição atual de portas de comboio. Dados estáticos incluiria o identificador de recurso, a hora e colunas de valor de condição.|

### <a name="data-types"></a>Tipos de dados
Os dois tipos de dados principal observados no domínio PdM tendo em conta as origens de dados acima, são:

- _Dados temporais_: Telemetria operacional, condições de máquina, tipos de ordem de trabalho, códigos de prioridade com carimbos de data / no momento da gravação. Falha de reparação/manutenção e histórico de utilização também terão os carimbos associados a cada evento.
- _Dados estáticos_: Funcionalidades de máquina e funcionalidades de operador em geral são estáticas, uma vez que eles descrevem as especificações técnicas de máquinas ou atributos de operador. Se esses recursos podem mudar ao longo do tempo, também terão os carimbos associados a eles.

As variáveis de previsão e de destino devem ser de pré-processados/transformado em [tipos de dados numéricos, categóricos e outros](https://www.statsdirect.com/help/basics/measurement_scales.htm) dependendo do algoritmo usado.

### <a name="data-preprocessing"></a>O pré-processamento de dados
Como um pré-requisito para _"feature Engineering"_, preparar os dados de vários fluxos para compor um esquema a partir do qual é fácil criar recursos. Visualize os dados pela primeira vez como uma tabela de registos. Cada linha na tabela representa uma instância de treinamento e as colunas representam _predictor_ recursos (também denominados atributos independentes ou variáveis). Organizar os dados de forma a que a última coluna (s) é o _destino_ (variável dependente). Para cada instância de treinamento, atribuir um _etiqueta_ como o valor desta coluna.

Para dados temporais, divida a duração de dados de sensor em unidades de tempo. Cada registo deve pertencer a uma unidade de tempo para um recurso _e deve oferecer informações distintas_. Unidades de tempo são definidas com base nas necessidades de negócio em múltiplos de segundos, minutos, horas, dias, meses, e assim por diante. A unidade de tempo _não tem de ser o mesmo que a frequência de recolha de dados_. Se a frequência é alta, os dados não podem mostrar qualquer diferença significativa de uma unidade para outro. Por exemplo, suponha que a temperatura ambiente foi recolhida cada 10 segundos. Apenas utilizar esse mesmo intervalo de tempo para os dados de treinamento aumenta o número de exemplos sem fornecer informações adicionais. Para esse caso, uma estratégia melhor seria usar a média de dados mais de 10 minutos, ou uma hora com base na justificativa comercial.

Para dados estáticos,
- _Registos de manutenção_: Dados não processados de manutenção tem um identificador de recurso e timestamp com informações sobre as atividades de manutenção que foram realizadas num determinado ponto no tempo. Transformar as atividades de manutenção em _categóricos_ colunas, onde um descritor de cada categoria exclusivamente mapeia para uma ação de manutenção específico. O esquema para os registos de manutenção incluiria o identificador de recurso, a hora e a ação de manutenção.

- _Registos de falha_: Falhas ou os motivos das falhas podem ser gravados como códigos de erro específico ou eventos de falha definidos por condições comerciais específicos. Em casos em que o equipamento tem vários códigos de erro, o especialista de domínio deve ajudar a identificar aqueles que são pertinentes para a variável de destino de mensagens em fila. Utilizar o restante de códigos de erro ou condições para construir _predictor_ funcionalidades que se correlacionam com essas falhas. O esquema para os registos de falha incluiria o identificador de recurso, hora, falha ou motivo da falha - se disponível.

- _Metadados de máquina e o operador_: Intercale os dados de máquina e o operador num esquema para associar um recurso com o operador, juntamente com seus respectivos atributos. O esquema para condições de máquina incluiria o identificador de recurso, funcionalidades de recurso, identificador de operador e funcionalidades de operador.

Outros passos de pré-processamento de dados incluem _lidar com valores em falta_ e _normalização_ de valores de atributos. Ver um debate detalhado está além do escopo deste guia – consulte a secção seguinte para algumas referências de útil.

Com o acima pré-processados origens de dados no local, a transformação final antes de é de engenharia de funcionalidades associar as tabelas acima, com base no identificador de recurso e timestamp. A tabela resultante teria valores nulos para a coluna de falha quando a máquina está em funcionamento normal. Estes valores nulas podem ser imputed por um indicador para um funcionamento normal. Utilize esta coluna de falha para criar _etiquetas para o modelo preditivo_. Para obter mais informações, consulte a secção sobre [modelagem técnicas para manutenção preditiva](#Modeling-techniques-for-predictive-maintenance).

## <a name="feature-engineering"></a>Com engenharia
Engenharia de funcionalidades é a primeira etapa antes da modelagem de dados. O processo de ciência de dados de sua função [descrita aqui](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/create-features). R _funcionalidade_ é um atributo preditivo para o modelo - como temperatura, pressão, vibração e assim por diante. Para PdM, a engenharia de funcionalidades envolve a abstração de estado de funcionamento de uma máquina através de dados históricos recolhidos ao longo de um período de tempo considerável. Nesse sentido, é diferente de seus colegas como monitorização remota, deteção de anomalias e deteção de falhas. 

### <a name="time-windows"></a>Janelas de tempo
Envolve os eventos que ocorrem como de relatórios de monitorização remota _pontos no tempo_. Modelos de deteção de anomalias avaliam fluxos de entrada (classificação) dos dados para anomalias de sinalizador a partir de pontos no tempo. Deteção de falhas classifica falhas sejam de tipos específicos à medida que ocorrem pontos no tempo. Por outro lado, PdM envolve a prever falhas através de um _período de tempo futuros_, com base nos recursos que representam o comportamento da máquina através de _período de tempo do histórico_. Para PdM, os dados da funcionalidade de pontos individuais de tempo são demasiado ruidosos ser preditiva. Por isso, os dados para cada funcionalidade tem de ser _smoothened_ ao agregar pontos de dados através de janelas de tempo.

### <a name="lag-features"></a>Funcionalidades de atraso
Os requisitos comerciais definem até que ponto o modelo tem que prever o futuro. Por sua vez, esta duração ajuda a definir 'até que ponto back o modelo tem de ter um aspeto' para fazer estas previsões. Este 'Procurar' período denomina-se a _atraso_, e são chamadas de funcionalidades desenvolvidas durante este período de atraso _lag funcionalidades_. Esta secção aborda os recursos de atraso que podem ser criados a partir de origens de dados de carimbos e criação de recursos de origens de dados estáticos. Normalmente, são funcionalidades de atraso _numérica_ por natureza.

> [!IMPORTANT]
> O tamanho da janela é determinado através de experimentação e deve ser finalizado com a ajuda de uma especialista de domínio. A limitação mesmo mantém-se para a seleção e a definição de recursos de atraso, suas agregações e o tipo do windows.

#### <a name="rolling-aggregates"></a>Agregados sem interrupção
Para cada registo de um ativo, uma janela sem interrupção de tamanho "W" for escolhida, para computar as agregações, como o número de unidades de tempo. Funcionalidades de atraso, em seguida, são calculadas usando os períodos de W _antes da data_ desse registo. Na figura 1, as linhas azuis mostram os valores de sensores registados para um recurso para cada unidade de tempo. Eles denotam uma média de valores de funcionalidade numa janela de tamanho W = 3. A média é calculada com todos os registos de carimbos de data no intervalo t<sub>1</sub> (cor de laranja) para t<sub>2</sub> (em verde). O valor para W normalmente está em minutos ou horas, consoante a natureza dos dados. Mas, para determinados problemas, escolher um grande W (Digamos, 12 meses) podem fornecer o histórico completo de um ativo até a hora do registo.

![Figura 1. Implementar funcionalidades agregadas](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png) figura 1. Implementar recursos de agregação

Exemplos de sem interrupção agregados numa janela de tempo são contagem, média, medidas CUMESUM (soma cumulativa), valores mínimos/máximos. Além disso, variação, desvio padrão e contagem de valores atípicos, além de desvios padrão de N, muitas vezes, são utilizados. Exemplos de agregados podem ser aplicados para o [casos de utilização](#Sample-PdM-use-cases) neste guia são listados abaixo. 
- _Atraso de vôo_: contagem de códigos de erro durante o dia/semana passada.
- _Falha da parte do motor de aeronave_: sem interrupção significa, desvio padrão e soma ao longo do último dia, semana, etc. Esta métrica deve ser determinada, juntamente com o domínio de negócios especialistas.
- _Falhas de ATM_: sem interrupção significa, mediana, intervalo, desvios padrão, contagem de valores atípicos, além de desvios padrão de três, CUMESUM superior e inferior.
- _Falhas de porta de comboio de metrô_: Contagem de eventos ao longo do dia anterior, semana, duas semanas etc.
- _Falhas de disjuntor automático_: Falha de conta ao longo da semana passada, ano, três anos etc.

Outra técnica útil no PdM é capturar alterações de tendências, picos e alterações no nível de usando algoritmos que detetem anomalias nos dados.

#### <a name="tumbling-aggregates"></a>Agregados em cascata
Para cada rotulado como registo de um ativo, uma janela de tamanho _W -<sub>k</sub>_  estiver definida, onde _k_ é o número de janelas de tamanho _W_. Agregações, em seguida, são criadas ao longo _k_ _janelas em cascata_ _W-k, W -<sub>(k-1)</sub>,..., W -<sub>2</sub>, W -<sub>1</sub>_  por períodos antes timestamp de um registo. _k_ pode ser um número pequeno para capturar os efeitos de curto prazo, ou um número grande para capturar os padrões de degradação de longo prazo. (consulte a figura 2).

![Figura 2. Funcionalidades de agregação em cascata](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png) figura 2. Em cascata funcionalidades agregadas

Por exemplo, lag funcionalidades para o caso de utilização do vento turbines pode ser criado com W = 1 e k = 3. Eles implicam o desfasamento para cada um dos últimos três meses usando valores atípicos superior e inferior.

### <a name="static-features"></a>Recursos estáticos

As especificações técnicas do equipamento como a data de fabrico, número de modelo, localização, são alguns exemplos de recursos estáticos. Eles são tratados como _categóricos_ variáveis para modelagem. Alguns exemplos para o caso de utilização do disjuntor automático são tensão atual, capacidade energética, tipo transformador e fonte de energia. Para falhas de roda, o tipo de rodas de tire (alloy vs acinzentado) é um exemplo.

Os esforços de preparação de dados discutidos até agora devem levar aos dados que está a ser organizados conforme mostrado abaixo. Dados de treinamento, teste e validação devem ter este esquema lógico (Este exemplo mostra o tempo em unidades de dias).

| ID do Recurso | Hora | <Feature Columns> | Etiqueta |
| ---- | ---- | --- | --- |
| A123 |Dia 1 | . . . | . |
| A123 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |
| B234 |Dia 1 | . . . | . |
| B234 |Dia 2 | . . . | . |
| ...  |...   | . . . | . |

É a última etapa na engenharia de funcionalidades do **etiquetagem** da variável de destino. Este processo é dependente da técnica de modelagem. Por sua vez, a técnica de modelagem depende do problema de negócios e a natureza dos dados disponíveis. Etiquetagem é abordada na secção seguinte.

> [!IMPORTANT]
> Preparação de dados e de engenharia de funcionalidades são tão importantes quanto técnicas para deparar-se com êxito PdM soluções de modelagem. O especialista de domínio e o profissional devem investir tempo significativo se chegar uma a certos recursos e os dados para o modelo. Uma pequena amostra de muitos livros em engenharia de funcionalidades estão listados abaixo:
> - Pyle, preparação de dados d. para os dados (a Morgan Kaufmann série em sistemas de gerenciamento de dados), de extração de 1999
> - Zheng, engenharia de funcionalidades de r. de r., Casari, para o Machine Learning: Princípios e as técnicas para cientistas de dados, o ' Reilly, de 2018.
> - Dong, G. Liu, H. (Editores), de funcionalidades de engenharia para Machine Learning e análise de dados (Chapman & Hall/CRC mineração de dados e série de deteção de dados de conhecimento), prima CRC, de 2018.

## <a name="modeling-techniques-for-predictive-maintenance"></a>Técnicas de modelação para manutenção preditiva

Esta secção descreve as técnicas de modelagem de principal para problemas de PdM, juntamente com seus métodos de construção de etiqueta específica. Tenha em atenção que uma técnica de modelagem único pode ser usada em vários setores diferentes. A técnica de modelagem está emparelhada o problema de ciência de dados, em vez do contexto dos dados em questão.

> [!IMPORTANT]
> A escolha de etiquetas para os casos de falha e a estratégia de etiquetagem  
> deve ser determinada após a consulta com a especialista de domínio.

### <a name="binary-classification"></a>Classificação binária
Classificação binária é utilizada para _prever a probabilidade de que uma parte do equipamento falha num período de tempo futuros_ - chamada a _período de horizonte futuras X_. X é determinado pelo problema de negócios e os dados em questão, após a consulta com a especialista de domínio. Os exemplos são:
- _tempo de antecedência mínima_ necessário substituir componentes, implementar recursos de manutenção, realizar a manutenção para evitar um problema que é provável que ocorram nesse período.
- _contagem mínima de eventos_ que pode acontecer antes de ocorrer um problema.

Nesta técnica, os dois tipos de exemplos de treinamento são identificados. Um exemplo de positivo _que indica uma falha_, com etiqueta = 1. Um exemplo negativo, o que indica que as operações normais, com etiqueta = 0. A variável de destino, e, por conseguinte, os valores de etiqueta, são _categóricos_. O modelo deve identificar cada novo exemplo probabilidade de falhar ou a funcionar normalmente nos próximos X unidades de tempo.

#### <a name="label-construction-for-binary-classification"></a>Construção de etiqueta de classificação binária
A questão aqui é: "O que é a probabilidade de que o elemento falhará no prazo de X unidades de tempo?" Para responder a essa pergunta, registos de etiqueta X antes da falha de um ativo como "prestes a falhar" (etiqueta = 1) e todos os outros registos como sendo "normal" da etiqueta (etiqueta = 0). (veja a figura 3).

![Figura 3. Etiquetas de classificação binária](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png) figura 3. Etiquetas de classificação binária

Seguem-se exemplos de etiquetagem estratégia para alguns dos casos de utilização.
- _Andamento a atrasos_: X pode ser escolhido como 1 dia, para prever a atrasos nas próximas 24 horas. Em seguida, todos os voos que estão dentro de 24 horas antes de falhas são identificados como 1.
- _Falhas de dispensar à vista de ATM_: Pode ser um objetivo de determinar a probabilidade de falha de uma transação na próxima hora de um. Nesse caso, todas as transações que ocorreram na última hora da falha são identificadas como 1. Para prever a probabilidade de falha sobre a moeda de N seguinte notas dispensed, todas as anotações dispensed dentro as últimas notas de N de uma falha são identificadas como 1.
- _Falhas de disjuntor automático_: O objetivo pode ser prever a seguinte falha do comando de disjuntor automático. Nesse caso, X é escolhido para ser um comando futuro.
- _Falhas de porta de treinar_: X pode ser escolhido como dois dias.
- _Faça isso falhas turbina_: X pode ser escolhido como dois meses.

### <a name="regression-for-predictive-maintenance"></a>Regressão para manutenção preditiva
Modelos de regressão estão habituados _a vida útil remanescente (RUL) de um recurso de computação_. RUL é definido como a quantidade de tempo que um recurso está operacional antes de ocorre a falha seguinte. Cada exemplo de treinamento é um registo que pertence a uma unidade de tempo _nY_ para um recurso, onde _n_ é o múltiplo. O modelo deve calcular a RUL de cada novo exemplo como um _contínuo número_. Este número indica o período de tempo restante antes da falha.

#### <a name="label-construction-for-regression"></a>Etiqueta de construção para regressão
A questão aqui é: "O que é a vida útil remanescente (RUL) do equipamento?" Para cada registo antes da falha, calcule a etiqueta a ser o número de unidades de tempo restante antes da falha seguinte. Nesse método, as etiquetas são variáveis contínuas. (Consulte a figura 4)

![Figura 4. Etiquetagem para regressão](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png) figura 4. Etiquetagem para regressão

Para regressão, a etiquetagem é feita com referência a um ponto de falha. Não é possível efetuar os seus cálculos sem saber quanto o elemento Sobreviveu antes de uma falha. Então, por outro lado de classificação binária, ativos sem quaisquer falhas nos dados não podem ser utilizados para a Modelagem. Este problema é melhor Tratado pela outra técnica de estatística chamada [análise de sobrevivência](https://en.wikipedia.org/wiki/Survival_analysis). Mas possíveis complicações podem surgir ao aplicar essa técnica para casos de utilização de PdM que envolvam dados de variáveis de tempo de mensagens em fila com intervalos freqüentes. Para obter mais informações sobre a análise de sobrevivência, consulte [de um pager](https://www.cscu.cornell.edu/news/statnews/stnews78.pdf).

### <a name="multi-class-classification-for-predictive-maintenance"></a>Classificação de Roc para manutenção preditiva
Técnicas de classificação de Roc podem ser utilizadas em soluções de PdM para dois cenários:
- Prever _dois resultados futuros_: É o primeiro resultado _um intervalo de tempo para falha_ para um recurso. O elemento é atribuído a um dos vários períodos possíveis de tempo. O segundo resultado é a probabilidade de falhas num período futuro devido a _uma das várias raiz faz com que_. Esta predição permite tripulação manutenção acompanhar os sintomas e agendamentos de manutenção do plano.
- Prever _causa mais provável que_ de uma determinada falha. Esse resultado recomenda o conjunto certo de ações de manutenção para corrigir uma falha. Uma lista ordenada de causas de raiz e reparos recomendados pode ajudar a priorizar suas ações de reparo após uma falha de técnicos.

#### <a name="label-construction-for-multi-class-classification"></a>Construção de etiqueta de classificação de Roc
A questão aqui é: "O que é a probabilidade de que um recurso irá falhar nos próximos _nZ_ unidades de tempo em que _n_ é o número de períodos?" Para responder a essa pergunta, da etiqueta nZ registos antes da falha de um recurso com os registos de tempo (3Z, 2Z, Z). Etiqueta, todos os outros registos como "normal" (etiqueta = 0). Nesse método, a variável de destino mantém _categóricos_ valores. (Consulte a figura 5).

![Figura 5. Etiquetas de predição de hora de falha para classificação multiclasses](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png) figura 5. Etiquetas para classificação de Roc para predição de falha de tempo

A questão aqui é: "O que é a probabilidade de que o elemento falhará no prazo de X unidades de tempo devido a causa de raiz/problema _P<sub>eu</sub>_?" em que _eu_ é o número de causas possíveis. Para responder a essa pergunta, registos de etiqueta X antes da falha de um ativo como "prestes a falhar devido a causa raiz _P<sub>eu</sub>_" (etiqueta = _P<sub>eu</sub>_). Todos os outros registos como sendo "normal" da etiqueta (etiqueta = 0). Nesse método além disso, as etiquetas são categóricos (consulte figura 6).

![Figura 6. Etiquetas de predição para classificação de várias classes de causa raiz](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png) figura 6. Etiquetas para classificação de Roc para predição de causa raiz

O modelo atribui uma probabilidade de falha devido a cada _P<sub>eu</sub>_  , bem como a probabilidade de não falha. Estas probabilidades podem ser ordenadas por magnitude para permitir que a previsão dos problemas que é mais provável que ocorram no futuro.

A questão aqui é: "As ações de manutenção recomenda após uma falha?" Para responder a essa pergunta, etiquetagem _não necessita de um futuro horizonte a ser escolhidas_, porque o modelo não é a previsão falha com no futuro. Ele é apenas a previsão mais provavelmente causa _assim que a falha já tiver ocorrido_.

## <a name="training-validation-and-testing-methods-for-predictive-maintenance"></a>Treinamento, validação e métodos de teste para manutenção preditiva
O [Team Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/overview) fornece uma cobertura completa do ciclo de validação de teste de formação do modelo. Esta secção aborda aspetos exclusivos para PdM.

### <a name="cross-validation"></a>Validação cruzada
O objetivo de [validação cruzada](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) é definir um conjunto de dados para "Testar" o modelo na fase de treinamento. Esse conjunto de dados é chamado de _conjunto de validação_. Problemas de limite de ajuda a esta técnica, como _overfitting_ e fornece informações sobre como o modelo generalizará a um conjunto de dados independente. Ou seja, um desconhecido conjunto de dados, que pode ser um problema real. A rotina de treinamento e teste para PdM precisa levar em conta os aspetos de variadas de tempo para generalizar melhor sobre dados futuros que não foram vistos.

Muitos algoritmos de machine learning dependem de um número de hiperparâmetros que podem ser alterado significativamente o desempenho do modelo. Os valores ideal destes hiperparâmetros não são calculados automaticamente quando o modelo de formação. Devem ser especificados por cientista de dados. Existem várias formas de localizar os valores válidos de hiperparâmetros.

A mais comum de um é _k subconjuntos de validação a validação cruzada_ que divide os exemplos aleatoriamente em _k_ subconjuntos. Para cada conjunto de valores de hiperparâmetros, execute o algoritmo de aprendizagem _k_ vezes. Em cada iteração, utilize os exemplos em subconjuntos de validação atual como um conjunto de validação e o resto dos exemplos como um conjunto de treinamento. Preparar o algoritmo através de exemplos de treinamento e as métricas de desempenho de computação através de exemplos de validação. No final desse loop, calcular a média de _k_ métricas de desempenho. Para cada conjunto de valores de hiper-parâmetros, escolha aqueles que têm o melhor desempenho médio. A tarefa de escolher hiperparâmetros, muitas vezes, é experimental por natureza.

Problemas de PdM, dados são registados como uma série de tempo de eventos provenientes de várias fontes de dados. Estes registos podem ser ordenados de acordo com o tempo de etiquetagem. Por conseguinte, se o conjunto de dados é dividido _aleatoriamente_ em conjunto de treinamento e validação, _alguns dos exemplos de treinamento podem ser posterior no tempo do que alguns dos exemplos de validação_. Desempenho futuro de valores de hiper-parâmetros irá ser estimado com base em alguns dados que chegaram _antes de_ foi preparado o modelo. Essas estimativas podem ser muito otimista, especialmente se a série de tempo não é estacionários e evolui ao longo do tempo. Como resultado, os valores de hiper-parâmetros escolhido podem ser inferior ao ideal.

A forma recomendada é dividir os exemplos em treinamento e validação definida _dependente de tempo_ maneira, onde todos os exemplos de validação são posteriores no tempo que todos os exemplos de treinamento. Para cada conjunto de valores de hiper-parâmetros, Treine o algoritmo ao longo do conjunto de dados de treinamento. Medir o desempenho do modelo sobre o mesmo conjunto de validação. Escolha os valores de hiper-parâmetros que mostram o melhor desempenho. Valores de hiper-parâmetros escolhidos pelo resultado da divisão de train/validação num desempenho melhor modelo futuro que com os valores escolhidos aleatoriamente pela validação cruzada.

O modelo final pode ser gerado por um algoritmo de aprendizagem de treinamento sobre os dados de treinamento inteiro com os melhores valores de hiper-parâmetros.

### <a name="testing-for-model-performance"></a>Testes de desempenho do modelo
Quando é criado um modelo, uma estimativa de seu desempenho futura em novos dados, é necessária. Uma boa estimativa é a métrica de desempenho de valores de hiper-parâmetros calculada com o conjunto de validação, ou uma métrica de desempenho médio calculada da validação cruzada. Estas estimativas costumam ser excessivamente otimista. A empresa, muitas vezes, pode ter algumas diretrizes adicionais sobre o deseja testar o modelo.

A forma recomendada de PdM é dividir os exemplos em treinamento, validação, e conjuntos de dados de teste _dependente de tempo_ forma. Todos os exemplos de teste devem ser posteriores no tempo que todos os exemplos de treinamento e validação. Após a divisão, gerar o modelo e medir o desempenho dele, conforme descrito anteriormente.

Quando a série de tempo é estacionários e fáceis de prever, abordagens aleatórias e dependente de tempo geram similares estimativas de desempenho futura. Mas quando séries de tempo não estacionários e/ou difícil de prever, a abordagem de dependente de tempo irá gerar mais realistas estimativas de desempenho futura.

### <a name="time-dependent-split"></a>Divisão de dependente de tempo
Esta secção descreve as melhores práticas para implementar a divisão de dependente de tempo. Uma divisão de bidirecional dependente de tempo entre conjuntos de testes e treinamento é descrita abaixo.

Suponha que um fluxo de eventos de timestamped como medidas de vários sensores. Defina os recursos e as etiquetas de treinamento e exemplos de teste ao longo de períodos de tempo que contêm vários eventos. Por exemplo, para classificação binária, criar recursos com base nos eventos passados e criar etiquetas com base em eventos futuros no "X" unidades de tempo no futuro (consulte as secções sobre ["feature Engineering"](#Feature-engineering) e técnicas de modelação). Portanto, o etiquetagem período de tempo de um exemplo vem depois que o intervalo de tempo de seus recursos.

Para divisão dependente de tempo, escolher uma _T o tempo limite de treinamento<sub>c</sub>_  no qual pretende preparar um modelo, com hiperparâmetros ajustados a utilização dos dados históricos até T<sub>c</sub>. Para evitar a fuga de etiquetas futuras que ultrapassam T<sub>c</sub> os dados de treinamento, escolha a hora mais recente para exemplos de treinamento de etiqueta ser X unidades antes de T<sub>c</sub>. No exemplo mostrado na figura 7, cada quadrado representa um registro no conjunto de dados em que as funcionalidades e as etiquetas são computadas, tal como descrito acima. A figura mostra os registos que devem passar à preparação e teste conjuntos para X = 2 e W = 3:

![Figura 7. Dividir a classificação binário dependente de tempo](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png) figura 7. Dividir a classificação binário dependente de tempo

Os quadrados em verde representam registos que pertencem a unidades de tempo que podem ser usadas para treinamento. Cada exemplo de treinamento é gerado pelo passado a considerar três períodos para geração de funcionalidade e dois períodos futuros para etiquetagem antes de T<sub>c</sub>. Quando qualquer parte dos dois períodos futuros está além T<sub>c</sub>, excluir esse exemplo do conjunto de dados de treinamento, porque não existem visibilidade pressupõe-se para além de T<sub>c</sub>.

Os quadrados pretas representam os registos do conjunto de dados etiquetado final que não deve ser utilizada no conjunto de dados de treinamento, tendo em conta a restrição acima. Estes registos também não serão utilizados nos dados, de teste, pois são antes de T<sub>c</sub>. Além disso, os períodos de tempo de etiquetas parcialmente dependem do período de tempo de treinamento, que não é ideal. Dados de preparação e teste devem ter períodos de tempo de etiquetas separados para impedir o vazamento de informações da etiqueta.

A técnica discutida até agora permite a sobreposição entre a preparação e teste exemplos que tenham carimbos de data / perto T<sub>c</sub>. Uma solução para alcançar maior separação é a exclusão de exemplos que são em unidades de tempo de W de T<sub>c</sub> do teste definido. Mas esse uma divisão agressiva depende da disponibilidade de dados de uma ampla.

Utilizado para prever a RUL de modelos de regressão são mais gravemente afetados pelo problema de vazamento. Usar o método split aleatório leva a extreme ajuste excessivo. Para problemas de regressão, a divisão deve ser, de modo que os registos que pertencem a recursos com falhas antes de T<sub>c</sub> vá para o conjunto de treinamento. Registos de recursos que têm falhas após o limite de ir para o conjunto de teste.

Outra prática recomendada para a divisão de dados para preparação e teste é usar uma divisão por ID de recurso. A divisão deve ser, de modo a que nenhum dos ativos utilizados no conjunto de treinamento são usados em testes de desempenho do modelo. Com essa abordagem, um modelo tem uma melhor oportunidade de fornecer resultados mais realistas com novos recursos.

### <a name="handling-imbalanced-data"></a>Manipular dados desequilibrados
Em problemas de classificação, se existirem mais exemplos de uma classe que dos outros, o conjunto de dados é conhecido como _desequilibrado_. O ideal é que suficiente representantes de cada classe nos dados de treinamento são preferenciais para ativar a diferenciação entre diferentes classes. Se uma classe for inferior a 10% dos dados, os dados for considerados desequilibrado. A classe sub-representadas é chamada uma _classe minoritários_.

Muitos problemas de PdM enfrentam esses desequilibrado conjuntos de dados, onde uma classe é gravemente sub-representadas em comparação comparados outros classe ou classes. Em algumas situações, a classe minoritários poderão constituir apenas 0,001% dos total de pontos de dados. Não é exclusivo para PdM desequilíbrio de classe. Outros domínios onde falhas e anomalias são raras ocorrências enfrentam um problema semelhante, para obter exemplos, deteção de fraudes e intrusão de rede. Estas falhas constituem exemplos de classe minoritários.

Com desequilíbrio de classe nos dados, desempenho de algoritmos de aprendizagem mais padrão for comprometido, uma vez que eles têm como objetivo minimizar a taxa de erro geral. Para um conjunto de dados com 99% negativo e exemplos positivos de 1%, um modelo pode ser exibido para que a precisão de 99% por etiquetagem todas as instâncias como negativa. Mas o modelo enganássemos classifica todos os exemplos positivos; por isso, mesmo que a precisão é elevada, o algoritmo não é útil. Conseqüentemente, métricas de avaliação convencionais como _precisão geral na taxa de erros_ não são suficientes para aprendizagem desequilibrada. Quando enfrentar desequilibrados conjuntos de dados, outras métricas são utilizadas para a avaliação do modelo:
- Precisão
- Recuperar
- F1 pontuações
- Custo ajustado ROC (características operacionais de destinatário)

Para obter mais informações sobre estas métricas, veja [modelar avaliação](#Model-evaluation).

No entanto, existem alguns métodos que o ajudam a classe de resolver o problema de desequilíbrio. Duas as principais são _técnicas de amostragem_ e _custo learning confidencial_.

#### <a name="sampling-methods"></a>Métodos de amostragem
Learning desequilibrado envolve o uso de métodos para modificar o conjunto de dados de treinamento para um conjunto de dados com balanceamento de amostragem. Métodos de amostragem são para não ser aplicada ao conjunto de teste. Apesar de existirem várias técnicas de amostragem, a maioria dos direta são _oversampling aleatório_ e _em amostragem_.

_Oversampling aleatório_ envolve a seleção de uma amostra aleatória da classe minoritários, replicar estes exemplos e adicioná-las ao conjunto de dados de treinamento. Conseqüentemente, o número de exemplos na classe minoritários aumenta e, eventualmente, equilibrar o número de exemplos de diferentes classes. Uma desvantagem de oversampling é que múltiplas instâncias de determinados exemplos podem fazer com que o classificador para se tornar muito específicos, que leva a ajuste excessivo. O modelo pode mostrar a precisão de treinamento de alta, mas seu desempenho em dados de teste que não foram vistos pode ser inferior ao ideal.

Por outro lado, _aleatórios em amostragem_ é selecionando uma amostra aleatória de uma classe de maioria e remover esses exemplos de conjunto de dados de treinamento. No entanto, a remoção de exemplos da classe de maioria pode fazer com que o classificador de perder a conceitos importantes relativas à classe maioria. _Amostragem de híbrida_ em que classe minoritários é excessivamente amostragem e classe maioria está sob-objeto de amostragem ao mesmo tempo é outra abordagem viável.

Há muitas técnicas de amostragem sofisticadas. A técnica escolhida depende as propriedades de dados e os resultados de experimentações iterativos, cientista de dados.

#### <a name="cost-sensitive-learning"></a>Learning confidencial de custos
No PdM, falhas que constituem a classe minoritários são mais interessantes que exemplos normais. Portanto, o foco está principalmente no desempenho do algoritmo sobre falhas. Prever incorretamente uma classe positiva como uma classe negativa pode custar mais do que vice versa. Esta situação é normalmente chamada desiguais perda ou assimétrico custo dos elementos enganássemos classificar para diferentes classes. O classificador ideal deve disponibilizam exatidão da previsão elevada através da classe minoritários, sem comprometer a precisão para a classe da maioria.

Existem várias formas de alcançar este equilíbrio. Para mitigar o problema de perda desiguais, atribuir um alto custo a classificação incorrecta da classe minoritários e tentar minimizar o custo geral. Gostam de algoritmos _SVMs (máquinas de Vetores de suporte)_ adotar esse método inerentemente, ao permitir que o custo de exemplos positivos e negativos seja especificado durante o treinamento. Da mesma forma, como a adaptativo métodos _aumentado árvores de decisão_ normalmente Mostrar bom desempenho com dados desequilibrados.

## <a name="model-evaluation"></a>Avaliação do modelo
Classificação incorrecta é um problema significativo para cenários de PdM onde o custo de falsos alarmes para a empresa é elevado. Por exemplo, uma decisão a partir do zero com base numa previsão incorreta de falha do motor de uma aeronave pode interromper agendas e planos de viagens. Colocar offline uma máquina de uma linha de montagem pode levar à perda de receita. Portanto, a avaliação do modelo com as métricas de desempenho certo contra novos dados de teste é fundamental.

Métricas de desempenho típica utilizadas para avaliar os modelos de PdM são abordadas abaixo:

- [Precisão](https://en.wikipedia.org/wiki/Accuracy_and_precision) é a métrica mais popular utilizada para descrever o desempenho de um classificador. Mas precisão é sensível a distribuições de dados e é uma medida ineficaz para cenários com desequilibrado conjuntos de dados. Outras métricas são utilizadas em vez disso. Ferramentas como o [matriz de confusão](https://en.wikipedia.org/wiki/Confusion_matrix) são utilizados para computação e ponderar a precisão do modelo.
- [Precisão](https://en.wikipedia.org/wiki/Precision_and_recall) de PdM modelos relacionados com a taxa de falsos alarmes. Menor precisão do modelo geralmente corresponde a uma taxa elevada de alarmes falsos.
- [Lembre-se de](https://en.wikipedia.org/wiki/Precision_and_recall) taxa indica o número de falhas no conjunto de teste, foram identificado corretamente pelo modelo. Taxas de recolhimento mais elevadas significa que o modelo é efetuada com êxito na identificação de falhas de verdade.
- [F1 pontuação](https://en.wikipedia.org/wiki/F1_score) é a média harmônica de precisão e lembre-se com o respetivo valor entre 0 (piores) a 1 (melhor).

Para classificação binária,
- [Recetor operacional curvas (multiclasse mediante)](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) também é uma métrica popular. Em curvas de cor MULTICLASSE, o desempenho do modelo é interpretado com base em um fixo ponto operacional no ROC.
- Mas para problemas de PdM _tabelas decile_ e _gráficos de comparação de precisão_ são mais informativo. Concentre-se apenas na classe positiva (de falhas) e fornecer uma visão mais complexa do desempenho do algoritmo de curvas ROC.
  - _Tabelas de decile_ são criados com exemplos de teste numa ordem descendente de probabilidades de falha. Os exemplos ordenados, em seguida, são agrupados em deciles (10% das amostras com maior probabilidade, em seguida, 20%, 30% e assim por diante). O /(random baseline) rácio (taxa positivo verdadeiro) para cada decile ajuda a estimar o desempenho do algoritmo em cada decile. A linha de base aleatória assume valores 0,1 0,2 e assim por diante.
  - [Gráficos de comparação de precisão](http://www2.cs.uregina.ca/~dbd/cs831/notes/lift_chart/lift_chart.html) a decile Verdadeiro positivo taxa versus aleatória taxa de positiva verdadeira para todos os deciles de desenho. As primeira deciles geralmente são o foco dos resultados, uma vez que mostrem os ganhos de maior. Primeira deciles também podem ser vistos como representativo para "em risco," quando utilizada para PdM.

## <a name="model-operationalization-for-predictive-maintenance"></a>Operacionalização de modelo para manutenção preditiva

O benefício que o exercício de ciência de dados é percebido apenas quando o modelo preparado é feito operacional. Ou seja, o modelo tem de ser implementado nos sistemas de negócio para fazer predições com base nos dados de novo, anteriormente que não foram vistos.  Os novos dados tem exatamente está em conformidade com o _assinatura modelo_ do modelo preparado de duas formas:
- todos os recursos tem de estar presentes em cada instância lógica (por exemplo, uma linha numa tabela) de novos dados.
- os novos dados tem de ser previamente processados, e cada uma das funcionalidades desenvolvido, exatamente da mesma forma como os dados de treinamento.

O processo acima é indicado de muitas formas na literatura para instituições académicas e do setor. No entanto, as seguintes instruções significam a mesma coisa:
- _Classificar dados novos_ usando o modelo
- _Aplicar o modelo_ para novos dados
- _Operacionalizar_ o modelo
- _Implementar_ o modelo
- _Executar o modelo_ contra dados novos

Como já declarado, operacionalização de modelo para PdM é diferente de seus colegas. Implementam cenários que envolvem a deteção de anomalias e deteção de falhas normalmente _online de classificação_ (também denominado _classificação em tempo real_). Aqui, o modelo _pontuações_ cada registo de entrada e retorna uma predição. Para deteção de anomalias, a predição é uma indicação de que ocorreu uma anomalia (exemplo: One-class SVM). Para deteção de falhas, seria o tipo ou de uma classe de falha.

Por outro lado, PdM envolve _classificação de lote_. Para estar em conformidade com a assinatura de modelo, os recursos novos dados tem de ser desenvolvidos da mesma forma como os dados de treinamento. Para grandes conjuntos de dados que é normais para novos dados, os recursos são agregados ao longo de janelas de tempo e classificados no batch. Classificação de lote é geralmente Feito em sistemas distribuídos, como [Spark](http://spark.apache.org/) ou [do Azure Batch](https://docs.microsoft.com/azure/batch/batch-api-basics). Existem duas alternativas - ambos inferior ao ideal:
- Mecanismos de transmissão em fluxo de dados suportam a agregação ao longo do windows na memória. Então, poderíamos argumentar que suportam a classificação online. Mas estes sistemas são adequados para dados densos em estreitas janelas de tempo ou elementos dispersos ao longo do windows mais amplas. Eles podem dimensiona bem para os dados densos sobre janelas de tempo maiores, como visto em cenários de PdM.
- Se a classificação de lote não estiver disponível, a solução é adaptar online de classificação para lidar com novos dados em pequenos lotes de cada vez.

## <a name="solution-templates-for-predictive-maintenance"></a>Modelos de soluções para manutenção preditiva

A seção final deste guia fornece uma lista de modelos de soluções PdM, tutoriais e experiências implementadas no Azure. Esses aplicativos PdM podem ser implementados numa subscrição do Azure numa questão de minutos em alguns casos. Eles podem ser usados como demonstrações de prova de conceito, áreas de segurança para experimentar alternativas ou accelerators para implementações de produção real. Estes modelos estão localizados no [Galeria de IA do Azure](http://gallery.azure.ai) ou [GitHub do Azure](https://github.com/Azure). Estes exemplos diferentes serão implementados ao longo do tempo para este modelo de solução.

| # | Cargo | Descrição |
|--:|:------|-------------|
| 2 | [Modelo de solução de manutenção preditiva do Azure](https://github.com/Azure/AI-PredictiveMaintenance) | Um modelo de solução de código-fonte aberto que demonstra a Modelagem de ML e uma infraestrutura do Azure completa capaz de suportar cenários de manutenção preditiva no contexto de monitorização remota do IoT. |
| 3 | [Aprendizagem para manutenção preditiva](https://github.com/Azure/MachineLearningSamples-DeepLearningforPredictiveMaintenance) | Bloco de notas do Azure com uma solução de demonstração de como utilizar as redes LSTM (memória de curto prazo longo) (uma classe de redes Neurais recorrente) para manutenção preditiva, com um [mensagem de blogue esta amostra](https://azure.microsoft.com/blog/deep-learning-for-predictive-maintenance).|
| 4 | [Guia de modelagem de manutenção preditiva no R](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) | Guia de modelagem de PdM com scripts em R.|
| 5 | [Manutenção preditiva do Azure para aeronáutica](https://gallery.azure.ai/Solution/Predictive-Maintenance-for-Aerospace-1) | Um dos modelos de soluções de PdM primeiro com base na versão 1.0 do Azure ML para manutenção de aeronave. Este guia originado este projeto. |
| 6 | [Ferramentas de IA do Azure para IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge) | IA no IoT edge com o TensorFlow; Kit de ferramentas pacotes aprofundadas modelos em contentores do Docker compatível com o Azure IoT Edge de aprendizagem e expor esses modelos como REST APIs.
| 7 | [Manutenção preditiva do IoT do Azure](https://github.com/Azure/azure-iot-predictive-maintenance) | O Azure IoT Suite PCS – solução pré-configurada. Modelo de PdM de manutenção das aeronaves com o IoT Suite. [Outro documento](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-overview) e [passo a passo](https://docs.microsoft.com/azure/iot-suite/iot-suite-predictive-walkthrough) relacionados com o mesmo projeto. |
| 8 | [Modelo de manutenção Preditiva com o SQL Server R Services](https://gallery.azure.ai/Tutorial/Predictive-Maintenance-Template-with-SQL-Server-R-Services-1) | Demonstração de cenário de vida útil restante com base nos serviços de R. |
| 9 | [Guia de modelagem de manutenção preditiva](https://gallery.azure.ai/Collection/Predictive-Maintenance-Modelling-Guide-1) | Funcionalidade de conjunto de dados de manutenção das aeronaves desenvolvida através da linguagem R com [experimentações](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Experiment-1) e [conjuntos de dados](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Modelling-Guide-Data-Sets-1) e [bloco de notas do Azure](https://gallery.azure.ai/Notebook/Predictive-Maintenance-Modelling-Guide-R-Notebook-1) e [experimentações](https://gallery.azure.ai/Experiment/Predictive-Maintenance-Step-1-of-3-data-preparation-and-feature-engineering-2)na versão 1.0 do AzureML|

## <a name="training-resources-for-predictive-maintenance"></a>Recursos de treinamento para manutenção preditiva

O Microsoft Azure oferece os percursos de aprendizagem para os conceitos básicos por trás de técnicas de PdM, além de conteúdo e treinamento em geral, os conceitos de IA e prática.

| Recursos de treinamento  | Disponibilidade |
|:-------------------|--------------|
| [Percurso de aprendizagem do PdM usando árvores e florestas de Random](https://aischool.microsoft.com/learning-paths/1H5vH5wAYcAy88CoQWQcA8) | Público | 
| [Percurso de aprendizagem do PdM com aprendizagem profunda](https://aischool.microsoft.com/learning-paths/FSIXxYkOGcauo0eUO8qAS) | Público |
| [Programador AI no Azure](https://azure.microsoft.com/training/learning-paths/azure-ai-developer) | Público |
| [Instituição de ensino do Microsoft AI](https://aischool.microsoft.com/learning-paths) | Público |
| [Aprendizagem de IA do Azure do GitHub](https://github.com/Azure/connectthedots/blob/master/readme.md) | Público |
| [Aprendizagem do LinkedIn](http://www.linkedin.com/learning) | Público |
| [Webinars do YouTube de IA da Microsoft](https://www.youtube.com/watch?v=NvrH7_KKzoM&t=4s) | Público |
| [Show de IA da Microsoft](http://channel9.msdn.com/Shows/AI-Show) | Público |
| [LearnAI@MS](https://learnanalytics.microsoft.com) | Parceiros |
| [Rede de parceiros da Microsoft](https://learningportal.microsoft.com) | Parceiros |

Além disso, MOOCS gratuitas (cursos abertos massivos online) em IA estão online oferecidas por instituições académicas como Stanford e MIT e outras empresas de instituições de ensino.
