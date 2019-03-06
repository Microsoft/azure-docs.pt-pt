---
title: Playbook do modelo de solução do Cortana Intelligence para previsão de procura de energia
description: Um modelo de solução no Microsoft Cortana Intelligence permite que o ajuda a prever a procura para uma empresa de utilitário de energia.
services: machine-learning
author: ilanr9
manager: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/24/2016
ms.author: garye
ms.openlocfilehash: bb5ef610e55495c372a47ff78e3252c9d8ec7055
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57435921"
---
# <a name="cortana-intelligence-solution-template-playbook-for-demand-forecasting-of-energy"></a>Playbook do modelo de solução do Cortana Intelligence para previsão de procura de energia
## <a name="executive-summary"></a>Resumo Executivo
Nos últimos anos, a Internet das coisas (IoT), fontes de energia alternativa e grandes quantidades de dados foram mescladas para criar grandes oportunidades no domínio utilitário e de energia. Ao mesmo tempo, o utilitário e o setor energético todo visto consumo mesclar com consumidores exigentes melhores formas de controlar o uso de energia. Por conseguinte, o utilitário e empresas de rede inteligente são na necessidade excelente para inovar e renovar propriamente ditas. Além disso, muitos grades de consumo de energia e utilitários estão se tornando desatualizada e muito dispendioso manter e gerir. Durante o ano passado, a equipe tem trabalhado num número de compromissos dentro do domínio de energia. Durante estes compromissos, mas encontrámos muitos casos em que os ISVs (fornecedores independentes de Software) ou utilitários tem procurado para previsão de procura energética futuras. Estes previsões desempenham um papel importante em seus negócios atuais e futuros e tornaram-se a base para vários casos de utilização. Estes incluem a previsão da carga de energia de curto e longo prazo, comércio, balanceamento de carga, otimização de grade etc. Grandes volumes de dados e métodos de análise avançadas (AA), como o Machine Learning (ML) são os habilitadores chave para a produção de previsões precisas e fiáveis.

Este manual de comunicação social, reunimos os negócios e analíticas diretrizes necessárias para um desenvolvimento com êxito e solução de previsão de implementação de procura de energia. Essas diretrizes propostas podem ajudar a utilitários, cientistas de dados e engenheiros de dados no estabelecimento de soluções totalmente operacionalizadas, com base na cloud, previsão de procura. Para as empresas que estão apenas começando seus macrodados e a jornada de análises avançadas, essa solução pode representar a semente inicial em sua estratégia de rede inteligente de longo prazo.

> [!TIP]
> Para transferir um diagrama que fornece uma visão geral da arquitetura deste modelo, consulte [arquitetura de modelo de solução do Cortana Intelligence para previsão de procura de energia](cortana-analytics-architecture-demand-forecasting-energy.md).
>
>

## <a name="overview"></a>Descrição geral
Este documento aborda os aspectos técnicos de com o Cortana Intelligence e, em particular do Azure Machine Learning (AML) para a implementação e a implantação de soluções de previsão de energia, comerciais e dados. O documento é composta por três partes principais:

1. Noções sobre empresas
2. Compreensão de dados
3. Implementação técnica

O **Noções sobre empresas** parte descreve o aspecto de negócio é necessário compreender e considerar antes de tomar uma decisão de investimento. Explica como qualificar o problema comercial à mão para garantir a Análise Preditiva e machine learning, de fato, efetiva e aplicáveis. O documento ainda mais explica as noções básicas do machine learning e como são utilizadas para resolver problemas de previsão de energia. Ele descreve os pré-requisitos e os critérios de qualificação de um caso de utilização. Exemplo de alguns casos de utilização e caso comercial cenários também são fornecidos.

Os dados são o principal ingrediente para qualquer solução de machine learning. O **compreensão de dados** parte deste documento aborda alguns aspetos importantes dos dados. Ele descreve o tipo de dados que é necessária para a previsão de energia, os requisitos de qualidade de dados e que origens de dados, normalmente, existem. Também vamos explicar como os dados brutos são usados para preparar os recursos de dados que, na verdade, a parte de modelagem de unidade.

A terceira parte do documento abrange a **implementação técnica** aspecto de uma solução. Engenharia de funcionalidades e modelação estão no núcleo do processo de ciência de dados e, portanto, estão sendo discutidos detalhadamente. Ele cobre o conceito de serviços web, que são um importante veículo de implementação de nuvem de soluções de Análise Preditiva. Podemos também descrevem uma arquitetura típica de uma solução de operacionalizado ponto-a-ponto.

Além disso, o documento inclui o material de referência que pode utilizar para obter ainda mais a compreensão do domínio e tecnologia.

É importante observar que, não pretendemos abordar neste documento, o processo de ciência de dados mais aprofundado, seus aspectos técnicos e matemáticos. Esses detalhes podem ser encontrados no [documentação de serviço do Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) e [blogs](https://blogs.microsoft.com/blog/tag/azure-machine-learning/).

### <a name="target-audience"></a>Público-alvo
O público-alvo para este documento é tanto a empresas, como a equipe de suporte técnico que gostaria de obter dados de conhecimento e compreensão do Machine Learning com base em soluções e como elas estão sendo usadas especificamente dentro do domínio de previsão de energia.

Os cientistas de dados também podem se beneficiar de ler este documento para obter uma melhor compreensão do processo de alto nível que orienta a implantação de uma solução de previsão de energia. Nesse contexto também pode ser utilizado para estabelecer uma linha de base boa e o ponto de partida para obter mais informações detalhadas e avançadas do material.

### <a name="industry-trends"></a>Tendências da indústria
Nos últimos anos, IoT, fontes de energia alternativa e grandes quantidades de dados foram mescladas para criar grandes oportunidades no espaço de utilitário e de energia. Ao mesmo tempo, o utilitário e os setores de energia todo visto consumo mesclar com consumidores exigentes melhores formas de controlar o uso de energia.

Muitos utilitário e empresas de energia inteligente tem sido pioneiras a [rede inteligente](https://en.wikipedia.org/wiki/Smart_grid) mediante a implementação de um número de utilização casos que tornam a usar os dados gerados pela grade. Casos de utilização de muitos giram em torno das características inerentes de produção de eletricidade: não pode ser acumulado nem armazenado à parte como inventário. Então, o que é produzido tem de ser utilizado. Utilitários que querem tornar-se mais eficientes precisam de prever o consumo de energia simplesmente porque isso dá a eles maior capacidade de **equilibrar suprimento e demandas**, evitando assim o desperdício de energia, **reduzir gás greenhouse emissões**e controlar o custo.

Quando se fala dos custos, há um outro aspecto importante, o que é o preço. Trouxeram numa grande necessidade de novas capacidades de negociação de energia entre utilitários **prever a procura futura e preços futuro de eletricidade**. Isso pode ajudar as empresas a determinar os volumes de produção.

Quando usamos a palavra "inteligência", nos Referimos, na verdade, a uma grade que pode saber mais e, em seguida, fazer predições. Pode prever as alterações sazonais no consumo, bem como **previr situações de sobrecarga temporária e ajustar automaticamente para o mesmo**. Por remotamente regular consumo (com a ajuda destes medidores inteligentes), podem ser processadas situações de sobrecarga localizada. **Pelo primeiro prever e, em seguida, agir**, a grade torna-se mais inteligente ao longo do tempo.

Para o restante deste documento, nos concentraremos numa família específica de casos de utilização que abrangem a previsão de futuro, curto e longo prazo procura de energia. Temos trabalhado nessas áreas por alguns meses e passaram a alguns dados de conhecimento e habilidades que permitem-nos produzir resultados de nível de setor. Outros casos de utilização serão abordados também o documento num futuro próximo.

## <a name="business-understanding"></a>Compreensão Empresarial
### <a name="business-goals"></a>Objetivos de negócio
O **demonstração de energia** objetivo é demonstrar uma análise preditiva típico e a solução que pode ser implementada num intervalo de tempo muito curto de machine learning. Especificamente, o nosso foco atual é sobre como ativar soluções de previsão de procura de energia, para que seu valor comercial pode ser percebido e aproveitado após a rapidamente. As informações neste manual de comunicação podem ajudá-o cliente ao realizar os seguintes objetivos:

* Pouco tempo para o valor do machine learning com base em solução
* Caso para outros casos de utilização ou a um âmbito mais amplo, com base na respetiva necessidade comercial de utilização de capacidade para expandir um piloto
* Obter rapidamente conhecimentos do produto do Cortana Intelligence Suite

Com estes objetivos em mente, este playbook tem como objetivo a fornecer o conhecimento comercial e técnico que irá ajudar a atingir essas metas.

### <a name="power-load-and-demand-forecasting"></a>Carga de energia e a previsão de procura
No setor energético, pode haver muitas formas no qual a pedido previsão pode ajudar a resolver problemas comerciais críticos. Na verdade, previsão da procura pode ser considerado a base para muitos casos de utilização de núcleos do setor. Em geral, consideramos que os dois tipos de previsões de procura de energia: curto e longo prazo. Cada um deles pode cumprir uma finalidade diferente e utilizar uma abordagem diferente. A principal diferença entre os dois é o horizonte previsão, o que significa que o intervalo de tempo no futuro para o qual estamos seria a previsão.

#### <a name="short-term-load-forecasting"></a>Carga do termo de curta de previsão
Dentro do contexto de procura energética, a curto prazo carregar previsão (STLF) é definido como a carga agregada, que é prevista num futuro próximo em várias partes da grade (ou a grade como um todo). Neste contexto, a curto prazo é definido para ser o prazo dentro do intervalo de 1 hora para 24 horas. Em alguns casos, um horizonte de 48 horas também é possível. Portanto, STLF é muito comum num caso de utilização operacional da grade. Aqui estão alguns exemplos de STLF controlado por casos de utilização:

* Procura e balanceamento de
* Suporte de negociação de energia
* Tomada de mercado (preço de energia de definição)
* Otimização de operacional de grelha
* [Resposta da procura](https://en.wikipedia.org/wiki/Demand_response)
* Picos de previsão de procura
* Gestão de lado a pedido
* Balanceamento de carga e prevenção de sobrecarga
* Longo prazo a previsão de carga
* Índice de falhas e deteção de anomalias
* Redistribuição/curtailment de pico

Modelo STLF baseiam-se principalmente o passado quase (último dia ou semana) os dados de consumo e a utilização previstosm temperatura como um importante fator. Temperatura precisa previsão para a hora seguinte de obtenção e cópia de segurança para 24 horas está se tornando menos de um desafio dias agora. Esses modelos são menos sensíveis à sazonais padrões ou tendências de consumo a longo prazo.

Soluções SLTF também têm probabilidades gerar o grande volume de chamadas de predição (pedidos de serviço), uma vez que estão sendo invocadas numa base horária e, em alguns casos, mesmo com freqüência mais alta. Também é muito comum ver implantation onde cada subestação individual ou transformador é representada como um modelo autónomo e, portanto, o volume de pedidos de predição são ainda maior.

#### <a name="long-term-load-forecasting"></a>Longo prazo a previsão de carga
O objetivo de longo prazo carga previsão (LTLF) é a previsão de procura de energia com um prazo que vão de 1 semana a vários meses (e, em alguns casos, para um número de anos). Esse intervalo de horizonte principalmente é aplicável para o planejamento e casos de utilização do investimento.

Para cenários de longo prazo, é importante ter dados de alta qualidade que abrange um intervalo de vários anos (mínimo 3 anos). Esses modelos normalmente extrair os padrões de sazonalidade dos dados históricos e fazer uso do predicators externas, tais como os padrões de meteorologia e clima.

É importante esclarecer o que é de previsão horizonte mais tempo, menos precisos pode ser a previsão. Portanto, é importante produzir alguns intervalos de confiança, juntamente com a previsão real que permitiria que seres humanos fatorar a variação possíveis em seu processo de planejamento.

Uma vez que o cenário de consumo para LTLF principalmente está planejando, podemos esperar muito mais baixos volumes de previsão (em comparação com a STLF). Nós provavelmente verá estas predições incorporadas a ferramentas de visualização de como o Excel ou Power BI e ser invocadas manualmente pelo utilizador.

### <a name="short-term-vs-long-term-prediction"></a>Curtos prazo vs. Predição de longo prazo
A tabela seguinte compara STLF e LTLF sentido para os atributos mais importantes:

| Atributo | Previsão da carga de curto prazo | Longo prazo previsão de carga |
| --- | --- | --- |
| Previsão de horizonte |A partir de 1 hora para 48 horas |De 1 a 6 meses ou mais |
| Granularidade de dados |Por Hora |Hora ou diária |
| Casos de utilização típica |<ul><li>A pedido/fornecimento balanceamento</li><li>Escolha a hora de previsão</li><li>Resposta da procura</li></ul> |<ul><li>Longo prazo planeamento</li><li>Planeamento de recursos de grelha</li><li>Planejamento de recursos</li></ul> |
| Indicadores típicos |<ul><li>Dia ou da semana</li><li>Hora do dia</li><li>Temperatura por hora</li></ul> |<ul><li>Mês do ano</li><li>Dia do mês</li><li>Temperatura de longo prazo e climáticas</li></ul> |
| Intervalo de dados históricos |Valor de duas ou três anos de dados |Valor de 5 a 10 anos de dados |
| Precisão típico |MAPE * de 5% ou inferior |MAPE * de 25% ou inferior |
| Frequência de previsão |Produzidos de hora a hora ou a cada 24 horas |Produzido depois mensal, trimestral ou anualmente |

\*[MAPE](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error) – significa que os erros de percentagem média

Como pode ser visto desta tabela, é realmente importante distinguir entre o curto e a longo prazo em cenários de previsão, à medida que eles representam diferentes necessidades empresariais e poderão ter de implementação diferentes e padrões de consumo.

### <a name="example-use-case-1-esmart-systems--overload-optimization"></a>Caso de utilização de exemplo 1: eSmart sistemas – otimização de sobrecarga
Uma função importante de um [rede inteligente](https://en.wikipedia.org/wiki/Smart_grid) é dinamicamente e constantemente otimizar e ajustar os padrões de consumo de alteração. O consumo de energia pode ser afetado por alterações a curto prazo que são principalmente causadas por flutuações de temperatura (*por exemplo,*, mais energia é utilizada para a condição de ligação sem fios ou de aquecimento...). Ao mesmo tempo, o consumo de energia também é influenciado por tendências a longo prazo. Estas podem incluir os efeitos de sazonalidade, feriados nacionais, crescimento de consumo de longo prazo e até mesmo econômicos fatores como o índice de consumidor, o preço de petróleo e o PIB.

Neste caso, [eSmart](https://www.esmartsystems.com/) desejava implantar uma solução baseada na cloud que permite prever a propensão de uma situação de sobrecarga em qualquer determinada subestação da grade. Em particular, eSmart queria identificar subestações que provavelmente sobrecarregar dentro de uma hora, para que uma ação imediata pôde ser realizada de evitar ou resolver essa situação.

Uma lista exata e a rápida execução predição requer a implementação dos três modelos de previsão:

* Há muito tempo o modelo de termo que permite a previsão de consumo de energia em cada subestação durante o próximo algumas semanas ou meses
* Modelo de curto prazo que permite a predição de situação de sobrecarga numa determinado subestação durante a hora seguinte
* Modelo de temperatura que fornece a previsão de temperatura futura ao longo de vários cenários

O objetivo do modelo de longo prazo é classificar as subestações por seus propensão para sobrecarregar (devido a respetiva capacidade de transmissão de energia) durante a semana ou mês seguinte. Isso permite que a criação de uma lista curta de subestações serviria como entrada para a predição a curto prazo. Como temperatura é um importante fator para o modelo de longo prazo, existe a necessidade de constantemente produzem previsões de temperatura de cenário multi e fornecê-los como entrada para o modelo de longo prazo. A previsão de curto prazo, em seguida, é invocada para prever a qual subestação é provável que na próxima hora de sobrecarga.

Os modelos de curto e longo prazo são implementados individualmente por cada subestação. Por conseguinte, a execução prática desses modelos requer orquestração extensa. Para obter uma maior exatidão de predição a curto prazo, um modelo mais granular é dedicado para cada hora do dia. Todos esses modelos são executados a cada hora e concluir a execução dentro de alguns minutos para permitir tempo suficiente responder e tomar ações preventivas, se necessário. Esta coleção de modelos é mantida atualizada através da utilização de reparametrização periódica os dados mais recentes.

Podem encontrar mais informações sobre este caso de utilização [aqui](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18945).

#### <a name="use-case-qualification-criteria--prerequisites"></a>Utilize critérios de qualificação maiúsculas – pré-requisitos
A principal vantagem do Cortana Intelligence é em sua capacidade poderosas para implementar e dimensionar soluções centralizadas em do machine learning. Ele foi projetado para suporte a milhares de previsões que são executadas em simultâneo. Pode dimensionar automaticamente para satisfazer um padrão de consumo de alteração. Portanto, é o foco de uma solução em precisão e desempenho computacional sem paralelo. Por exemplo, uma empresa de utilitário é interessada na produção de procura de energia precisos para a hora seguinte e para cada hora do dia de previsão. Por outro lado, estamos menos interessados em responder à pergunta de por que a procura está prevista para ser como ele é (o próprio modelo encarrega-se de que).

Portanto, é importante perceber que nem todos os casos de utilização e problemas de negócios podem ser resolvidos de forma eficaz a com o machine learning.

Cortana Intelligence e machine learning podem ser muito eficazes na resolver um problema de negócios determinado quando são cumpridos os seguintes critérios:

* É o problema de negócios em mãos **preditiva** por natureza. Um exemplo de casos de utilização preditiva é uma empresa de utilitário que gostaria de prever a carga de energia numa determinado subestação durante a hora seguinte. Por outro lado, analisar e classificação de drivers de históricos de procura seria **descritivo** por natureza e, portanto, menos aplicável.
* Existe um claro **caminho da ação de** a tomar depois da predição está disponível. Por exemplo, se prever uma sobrecarga numa subestação durante a hora seguinte, pode acionar uma ação de proativa de reduzir a carga que está associada essa subestação e impedindo potencialmente uma sobrecarga.
* Representa o caso de utilização uma **típico tipo de problema** , de modo que quando resolvido, pode preparar o caminho para resolver outras semelhante de casos de utilização.
* O cliente pode definir **objetivos quantitativos e qualitativos** para demonstrar uma implementação de solução com êxito. Por exemplo, um objetivo quantitativo boa para previsão de procura de energia deve ser o limiar de precisão necessária (*por exemplo,*, é permitido até 5% erro) ou quando prever subestação sobrecarregar, em seguida, a precisão (taxa de positivos verdadeiros) e lembre-se (extensão da positivos verdadeiros) deve ser superior um determinado limiar. Estes objetivos devem ser derivados de objetivos de negócio do cliente.
* Existe um claro **cenário de integração** com fluxo de trabalho de negócios da empresa. Por exemplo, a previsão da carga de subestação pode ser integrada no Centro de controlo de grelha para permitir que atividades de prevenção de sobrecarga.
* O cliente tem pronto a utilizar **dados com qualidade suficiente** para suportar o caso de utilização (consulte outras informações na secção seguinte, **qualidade dos dados**, neste manual de comunicação social).
* A arquitetura de centrados em dados de cloud engloba cliente ou **aprendizagem baseado na nuvem**, incluindo o Azure ML e outros componentes do Cortana Intelligence Suite.
* O cliente está disposto a estabelecer **um fluxo de dados ponto a ponto** esse instalações a entrega de dados para a cloud de forma contínua e está disposto a **operacionalizar** a solução.
* O cliente está pronto para **dedicar recursos** que irá ser ativamente envolvidos durante a implementação piloto inicial para que os dados de conhecimento e a propriedade da solução podem ser transferidos para o cliente após a conclusão bem-sucedida.
* O recurso de cliente deve ser um **professional dados qualificados**, preferencialmente, um cientista de dados.

Qualificação de um caso de utilização com base nos critérios acima pode significativamente melhorar as taxas de êxito de um caso de utilização e estabelecer uma boa base para a implementação de casos de utilização futura.

### <a name="cloud-based-solutions"></a>Soluções baseadas na cloud
Cortana Intelligence Suite no Azure é um ambiente integrado que residem na cloud. A implementação de uma solução de análises avançadas num ambiente de cloud possui vantagens significativas para as empresas e ao mesmo tempo podem significar grande mudança para as empresas que ainda utilize no local soluções de TI. No setor energético, existe uma tendência clara da migração gradual de operações para a cloud. Essa tendência anda de mãos dadas, juntamente com o desenvolvimento da grade inteligente tal como explicado acima, na **as tendências da indústria**. Como este playbook se concentra numa solução baseada na cloud no domínio de energia, é importante explicar as vantagens e outras considerações de implantação de uma solução com base na cloud.

Talvez a maior vantagem de uma solução com base na cloud é o custo. Como uma solução faz uso dos componentes implantadas na nuvem, não há custos iniciais ou os custos de componente de COGS (custo de bens vendidos) associados a ele. Isso significa que não há necessidade de investir em hardware, software e manutenção IT e, portanto, há uma redução substancial no risco de negócios.

Outra vantagem importante é a estrutura de custos de pay as you go de soluções baseadas na cloud. Servidores com base na cloud para armazenamento ou de computação podem ser implementados e dimensionados de forma just-conforme necessário. Isso representa a vantagem da eficiência de custo de uma solução baseada na cloud.

Por fim, não há necessidade de investir na manutenção IT ou desenvolvimento de infra-estrutura futuras, como tudo isso é parte da oferta com base na cloud. Até esse limite, Cortana Intelligence Suite inclui o melhor nos serviços de classe e seu mapa da estrada mantém em evolução. Novos recursos, componentes e capacidades estão constantemente sendo apresentadas e evoluem.

Para uma empresa que está a iniciar sua transição para a cloud, que altamente Recomendamos para adotar uma abordagem de gradual implementando um Roteiro de migração da cloud. Acreditamos que, para utilitários e empresas no domínio de energia, os casos de utilização que são abordados neste manual de comunicação representam uma excelente oportunidade para criar um piloto de soluções de Análise Preditiva na cloud.

#### <a name="business-case-justification-considerations"></a>Considerações de justificação caso comercial
Em muitos casos, o cliente pode estar interessado em tirar uma justificativa comercial para um caso de uso determinado, em que uma solução baseada na cloud e Machine Learning são componentes importantes. Ao contrário de uma solução no local, no caso de uma solução baseada na cloud, o componente de custos à cabeça é mínimo e a maioria dos elementos de custo está associada a utilização real. Quando se trata de implantar um solução no Cortana Intelligence Suite de previsão de energia, vários serviços podem ser integrados com uma única estrutura comum do custo. Por exemplo, as bases de dados (*por exemplo,*, SQL Azure) pode ser utilizado para armazenar os dados não processados e, em seguida, para o Azure ML de prevê a real é utilizada para alojar os serviços de previsão. Neste exemplo, a estrutura de custo pode incluir componentes transacionais e de armazenamento.

Por outro lado, um deve ter uma boa compreensão do valor comercial de operar uma demanda de energia, previsão do (curto ou longo prazo). Na verdade, é importante perceber o valor comercial de cada operação de previsão. Por exemplo, com precisão de previsão de carga de energia para as próximas 24 horas pode impedir overproduction ou pode ajudar a evitar sobrecargas na grade e isso pode ser quantificado em termos de economia financeira numa base diária.

Uma fórmula básica para calcular o benefício financeiro de procura de previsão solução seria: ![Solução de previsão de fórmula básica para calcular o benefício financeiro de procura](media/cortana-analytics-playbook-demand-forecasting-energy/financial-benefit-formula.png)

Uma vez que o Cortana Intelligence Suite fornece um modelo de preços pay as you go, não há necessidade de incorrer num componente de custo fixo para esta fórmula. Esta fórmula pode ser calculada numa base diária, mensal ou anual.

Atual do Cortana Intelligence Suite e planos de preços do Azure ML podem ser encontradas [aqui](https://azure.microsoft.com/pricing/details/machine-learning/).

### <a name="solution-development-process"></a>Processo de desenvolvimento de solução
O ciclo de desenvolvimento de uma solução, normalmente, envolve 4 fases, em todos os quais podemos tornar de previsão de procura de energia a utilização de tecnologias baseadas na nuvem e os serviços do Cortana Intelligence Suite.

Isso é ilustrado no diagrama seguinte:

![Ciclo de rede inteligente](media/cortana-analytics-playbook-demand-forecasting-energy/smart-grid-cycle.png)

O parágrafo a seguir descreve esse processo 4 passo:

1. **Recolha de dados** – avançada de qualquer solução de análise com base baseia-se nos dados (consulte **compreensão de dados**). Especificamente, quando se trata de Análise Preditiva e de previsão, Contamos com fluxo contínuo e dinâmico de dados. No caso de previsão de procura de energia, estes dados podem ter origem diretamente a partir de medidores inteligentes, ou já ser agregados numa base de dados no local. Precisamos também de outras fontes externas de dados como meteorologia e temperatura. Este fluxo contínuo de dados deve ser orquestrado, agendado e armazenado. [O Azure Data Factory](https://azure.microsoft.com/services/data-factory/) (ADF) é o nosso trabalho pesado de principal para realizar essa tarefa.
2. **Modelagem** – para previsões de energia precisos e fiáveis, uma deve desenvolver (train) e manter um excelente modelo que faz com que utilize dos dados históricos e extrai os padrões significativos e preditivos dos dados. A área de Machine Learning (ML) tem crescido rapidamente com algoritmos mais avançados que estão sendo desenvolvidos regularmente. O Azure ML Studio oferece uma experiência de utilizador fantástica que ajuda a utilizar os mais avançados algoritmos de ML dentro de um fluxo de trabalho completo. Esse fluxo de trabalho é ilustrado no diagrama de fluxo de intuitivo e inclui a preparação de dados, extração de funcionalidade, modelação e avaliação do modelo. O utilizador pode extrair em centenas de vários modelos que estão incluídos neste ambiente. No final desta fase, um cientista de dados terão um modelo funcional totalmente avaliada e pronto para implantação.

   O diagrama seguinte é uma ilustração de um fluxo de trabalho típico:

   ![Fluxo de trabalho de modelagem](media/cortana-analytics-playbook-demand-forecasting-energy/modeling-workflow.png)
3. **Implementação** – com um modelo funcional em mãos, a próxima etapa é a implementação. Aqui o modelo é convertido num serviço web que expõe uma API RESTful que podem ser invocados simultaneamente através da Internet de vários clientes de consumo. O Azure ML oferece uma forma simples de implementar um modelo diretamente a partir do Azure ML Studio, com um único clique de um botão. O processo de toda a implementação acontece nos bastidores. Esta solução pode dimensionar automaticamente para satisfazer o consumo necessário.
4. **Consumo** – nesta fase, fazemos, na verdade, utilizar o modelo de previsão de produzir previsões. O consumo pode ser condicionado a partir de uma aplicação de utilizador (*por exemplo,*, dashboard) ou diretamente de um sistema operacional, como a pedido/fornecimento balanceamento de sistema ou uma solução de otimização de grade. Vários casos de utilização podem ser condicionados a partir de um único modelo.

## <a name="data-understanding"></a>Compreensão de dados
Depois de falarmos sobre as considerações de negócios (consulte **Noções sobre empresas**) de uma demanda de energia, solução de previsão, agora estamos prontos para discuti a parte de dados. Qualquer solução de Análise Preditiva baseia-se nos dados fiáveis. Para previsão de procura de energia, Contamos com dados históricos de consumo com vários níveis de granularidade. Que dados históricos são utilizados como o material de raw. Ele vai sofrer uma análise cuidada na qual a cientista de dados irá identificar indicadores (também denominados como recursos) que podem ser colocados num modelo que eventualmente irá gerar as previsões necessárias.

No restante desta seção, descreveremos os vários passos e considerações para compreender os dados e como colocá-lo a um formulário utilizável.

### <a name="the-model-development-cycle"></a>O ciclo de desenvolvimento do modelo
Produção de bons modelos requer uma preparação cuidadosa de previsão e planejamento. A divisão de processo de modelagem em várias etapas e com foco numa etapa por vez podem melhorar significativamente o o resultado de todo o processo.

O diagrama seguinte ilustra como o processo de modelagem pode ser dividido em vários passos:

![Ciclo de desenvolvimento do modelo](media/cortana-analytics-playbook-demand-forecasting-energy/model-development-cycle.png)

Como pode ser visto que o ciclo de consiste em seis etapas:

* Formulação de problema
* Ingestão de dados e exploração de dados
* Preparação de dados e de engenharia de funcionalidades
* Modelação
* Avaliação do modelo
* Desenvolvimento

O restante desta seção descreveremos as etapas individuais e os itens a serem considerados em cada passo.

### <a name="problem-formulation"></a>Formulação de problema
Pode considerá-a formulação de problema como a etapa mais importante, é necessário efetuar antes da implementação de qualquer solução de Análise Preditiva. Aqui estamos seria transformar o problema comercial e decompor-lo para elementos específicos que podem ser resolvidos através de dados e técnicas de modelagem. É uma boa prática para formular o problema como um conjunto de perguntas que gostaria de responder. Eis algumas questões possíveis que podem ser aplicáveis no âmbito da previsão de procura de energia:

* O que é a carga esperada numa subestação individual na próxima hora ou dia?
* A que hora do dia será minha grade experiência picos de procura?
* Qual a probabilidade é minha grade para Suster a carga máxima esperados?
* Quantidade de energia deve gerar a estação de energia durante cada hora do dia?

Formular essas perguntas nos permite se concentrar em obter os dados corretos e implementar uma solução totalmente alinhado com o problema de negócios em questão. Além disso, pode então definir algumas métricas-chave que permitem-nos avaliar o desempenho do modelo. Por exemplo, grau de precisão a previsão será e qual é o intervalo de erro que ainda seria aceitável, a empresa?

### <a name="data-sources"></a>Origens de Dados
A grade inteligente modernos recolhe dados de várias partes e componentes da grade. Estes dados representam vários aspectos da operação e a utilização da grade de energia. Dentro do âmbito da procura energética, previsão, podemos são limitando a discussão sobre as origens de dados que refletem o consumo de procura real. Uma origem importante do consumo de energia são medidores inteligentes. Utilitários em todo o mundo são implantar rapidamente medidores inteligentes para seus consumidores. Medidores inteligentes registram o consumo de energia real e constantemente estes dados para a empresa prestadora de reencaminhamento. Dados são recolhidos e enviados, o intervalo fixo, desde a cada 5 minutos para 1 hora. Mais avançados medidores inteligentes podem ser programados remotamente para controlar e equilibrar o consumo real dentro de um núcleo. Os dados de medidor inteligente é relativamente fiáveis e incluem um carimbo de data / hora. Assim que um importante ingrediente para previsão de demanda. Medidor de dados podem ser agregados (summed) em vários níveis na topologia grid: transformador, subestação, região, *etc*. Em seguida, podemos escolher o nível da agregação necessário para criar um modelo de previsão para o mesmo. Por exemplo, se a empresa prestadora gostaria de prever futuros carga em cada um dos seus subestações de grade, em seguida, dados todos os medidores podem ser agregados para cada subestação individual e utilizados como uma entrada para o modelo de previsão. Nos Referimos medidores inteligentes como uma origem de dados interno.

Uma previsão de procura de energia confiáveis serão também contam com outras origens de dados externas. Um fator importante que afeta o consumo de energia é a meteorologia ou mais precisamente a temperatura. Dados históricos mostram forte correlação entre a temperatura externa e consumo de energia. Durante os dias de acesso frequente de Verão, os consumidores que utilizam de seus aparelhos de ar-condicionado e durante o poder de Inverno no aquecer sistemas. Uma origem fiável das temperaturas históricas disponíveis na localização de grade, portanto, é fundamental. Além disso, podemos também contam com uma previsão exata da temperatura como um fator de consumo de energia.

Outras origens de dados externo também podem ajudar na criação de modelos de previsão de procura de energia. Estas podem incluir as alterações climáticas de longo prazo, índices económicas (*por exemplo,*, PIB) e outros. Neste documento, não incluirá estas outras origens de dados.

### <a name="data-structure"></a>Estrutura de dados
Depois de identificar as origens de dados necessários, gostaríamos de Certifique-se de que os dados não processados que tenham sido recolhidos incluem as funcionalidades de dados correto. Para criar um modelo de previsão de procura fiável, precisamos de garantir que os dados recolhidos incluem elementos de dados que podem ajudar a prever a procura futura. Seguem-se alguns requisitos básicos sobre a estrutura de dados (esquema) dos dados não processados.

Os dados brutos é composta por linhas e colunas. Cada medição é representada como uma única linha de dados. Cada linha de dados inclui várias colunas (também denominadas como recursos ou campos).

1. **Carimbo de data / hora** – o campo timestamp representa o tempo real quando a medida foi registada. Ele deve estar em conformidade com um dos formatos de data/hora comuns. Partes de data e hora devem ser incluídos. Na maioria dos casos, não é necessário para o tempo para ser gravado até o segundo nível de granularidade. É importante especificar o fuso horário em que os dados são registrados.
2. **ID do medidor** -este campo identifica o medidor ou o dispositivo de medição. Ele é uma variável categórica e pode ser uma combinação de dígitos e carateres.
3. **Valor de consumo** – este é o consumo real numa determinada data/hora. O consumo pode ser medido em kWh (kilowatt-hour) ou qualquer outro preferencial unidades. É importante observar que a unidade de medida deve permanecer consistente em todas as medições de dados. Em alguns casos, o consumo pode ser fornecido mais 3 fases de energia. Nesse caso, seria necessário recolher todas as fases de consumo independentes.
4. **Temperatura** – a temperatura, normalmente, é recolhida a partir de uma origem de independente. No entanto, deve ser compatível com os dados de consumo. Deve incluir um carimbo conforme descrito acima, que permitirá a ser sincronizados com os dados de consumo real. O valor de temperatura pode ser especificado em graus Celsius ou Fahrenheit, mas deve permanecer consistente em todas as medidas.
5. **Localização –** o campo de localização é normalmente associado ao local onde os dados de temperatura tenham sido recolhidos. Ele pode ser representado como um número de código postal ou no formato do latitude/longitude (lat e longos).

As tabelas a seguir mostra exemplos de um bom formato de dados de consumo e a temperatura:

| **data** | **tempo** | **ID do medidor** | **Fase 1** | **Fase 2** | **Fase 3** |
| --- | --- | --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |ABC1234 |7.0 |2.1 |5.3 |
| 7/1/2015 |10:00:01 |ABC1234 |7.1 |2.2 |4.3 |
| 7/1/2015 |10:00:02 |ABC1234 |6.0 |2.1 |4.0 |

| **data** | **tempo** | **Localização** | **temperatura** |
| --- | --- | --- | --- |
| 7/1/2015 |10:00:00 |11242 |24.4 |
| 7/1/2015 |10:00:01 |11242 |24.4 |
| 7/1/2015 |10:00:02 |11242 |24.5 |

Como pode ser visto acima, este exemplo inclui 3 valores diferentes para consumo associadas com 3 fases de energia. Além disso, tenha em atenção que os campos de data e hora são separados, no entanto também podem ser combinados numa única coluna. Neste caso, a coluna de localização é representada num formato de código postal de 5 dígitos e a temperatura num formato de graus Celsius.

### <a name="data-format"></a>Formato de dados
Cortana Intelligence Suite pode oferecer suporte os formatos de dados mais comuns, como CSV, TSV, JSON, *etc*. É importante que o formato de dados permanece consistente para todo o ciclo de vida do projeto.

### <a name="data-ingestion"></a>Ingestion de Dados
Uma vez que a previsão de procura de energia é prevista de constantemente e com frequência, nós tem de garantir que os dados não processados estão a ser encaminhados por meio de um processo de ingestão de dados sólida e fiável. O processo de ingestão tem de garantir que os dados não processados estão disponíveis para o processo de previsão no tempo necessário. Isso significa que a frequência de ingestão de dados deve ser superior à frequência de previsão.

Por exemplo: Se nossa solução de previsão da procura geraria uma previsão novo às 8:00, diariamente, em seguida, é necessário garantir que todos os dados que tenham sido recolhidos durante as últimas 24 horas tiverem sido totalmente ingeridos até esse ponto e tem até mesmo incluir na última hora de dados.

Para tal, o Cortana Intelligence Suite oferece várias formas de suporte a um processo de ingestão de dados fiável. Isso será ainda mais abordado a **implementação** seção deste documento.

### <a name="data-quality"></a>Qualidade de dados
A origem de dados não processados que é necessária para realizar a previsão de procura fiável e exata têm de cumprir alguns critérios de qualidade de dados básicos. Embora métodos estatísticos avançados podem ser utilizados para compensar algum problema de qualidade de dados, ainda precisamos Certifique-se de está ultrapassando algum limite de qualidade de base de dados quando a ingestão de novos dados. Seguem-se algumas considerações sobre a qualidade de dados não processados:

* **Valor em falta** – isso refere-se para a situação quando medição específica não foi recolhida. O requisito de básico aqui é que a taxa de valor em falta não deve ser superior a 10% para qualquer determinado período de tempo. No caso deve ser indicado que um único valor está em falta-lo utilizando um valor predefinido (por exemplo: '9999') e não '0', que pode ser uma medida válida.
* **Precisão da medição** – o valor real do consumo ou temperatura deve ser registrado com precisão. Medidas incorretas produzirá previsões incorretas. Normalmente, o erro de medida deve ser inferior a 1 de % relativo ao valor true.
* **Tempo de medida** – é necessário que o período de tempo real dos dados recolhidos serão não desviar por mais de 10 segundos em relação ao tempo true da medição real.
* **Sincronização** — quando estão a ser utilizadas várias origens de dados (*por exemplo,*, de consumo e de temperatura), tem de garantir que não há nenhuma sincronização de hora problemas entre eles. Isso significa que a diferença de tempo entre o carimbo de hora recolhido quaisquer dois independente das origens de dados não deve exceder os mais de 10 segundos.
* **Latência** – tal como explicado acima, na **ingestão de dados**, são dependentes de um processo de fluxo e ingestão de dados fiável. Para controlar o que podemos tem de garantir que a Microsoft controlar a latência de dados. Isto é especificado como a diferença de tempo entre o momento em que foi efetuada a medida real e a hora a que foi carregada para o armazenamento do Cortana Intelligence Suite e está pronta a utilizar. Para a carga de curto prazo a previsão a latência total não deve ser superior a 30 minutos. Para a carga de longo prazo a previsão a latência total não deve ser maior que 1 dia.

### <a name="data-preparation-and-feature-engineering"></a>Preparação de dados e de engenharia de funcionalidades
Depois dos dados não processados foram ingeridos (consulte **ingestão de dados**) e trabalha com segurança armazenada, está pronto para ser processado. A fase de preparação de dados é, basicamente, levando os dados não processados e a conversão (transformar, reformulação de) num formulário para a fase de modelagem. Que pode incluir operações simples, tais como utilizar a coluna de dados não processados, como está com seu valor de medida real, valores padronizados, operações mais complexas, como [tempo lagging](https://en.wikipedia.org/wiki/Lag_operator)entre outros. As colunas de dados recém-criado são referidas como recursos de dados e o processo de gerar estes é chamado para engenharia de funcionalidades. No final deste processo seria temos um novo conjunto de dados que tem sido derivado dos dados brutos e podem ser usado para modelagem. Além disso, a fase de preparação de dados tem de lidar com valores em falta (consulte **qualidade dos dados**) e compensá-los. Em alguns casos, precisaremos também normalizar os dados para se certificar de que todos os valores são representados na mesma escala.

Nesta secção, que listamos alguns dos recursos de dados comuns, que estão incluídos na energia da previsão de procura modelos.

**Hora de base em recursos:** Estas funcionalidades são derivadas dos dados de data/timestamp. Estas são extraídas e convertidas em categóricos recursos como:

* Hora do dia – esta é a hora do dia que aceita valores entre 0 e 23
* Dia da semana – isso representa o dia da semana e aceita valores entre 1 (Domingo) e 7 (Sábado)
* Dia do mês – isso representa a data real e pode assumir valores entre 1 e 31
* Mês do ano – isso representa o mês e aceita valores entre 1 (Janeiro) e 12 (Dezembro)
* Fim de semana – esta é uma funcionalidade de valor binário que usa os valores de 0 para dias da semana ou 1 para o fim de semana
* Feriado - esse é um recurso de valor binário que usa os valores de 0 para um dia regular ou 1 para um feriado
* Termos de Fourier-os termos de Fourier são pesos que são derivados do período de tempo e são usados para capturar a sazonalidade (cycles) nos dados. Uma vez que podemos ter várias fases da vida nos nossos dados precisamos vários termos de Fourier. Por exemplo, valores de pedido podem ter anuais, semanais e diários temporadas/ciclos, o que resulta em termos de Fourier 3.

**Recursos de medição independente:** Os recursos independentes incluem todos os elementos de dados que gostaríamos de o utilizar como indicadores no nosso modelo. Aqui, podemos excluir o recurso dependente que precisamos para prever.

* Funcionalidade de atraso – são tempo desviado valores da procura real. Por exemplo, as funcionalidades de atraso 1 irão conter o valor a pedido na hora anterior (partindo do princípio de dados por hora) em relação ao timestamp atual. Da mesma forma, vamos adicionar o desfasamento de 2, lag 3, *etc*. A combinação real dos recursos de atraso que são utilizados são determinados durante a fase de modelagem pela avaliação dos resultados de modelo.
* Longo prazo mais populares – esta funcionalidade representa o crescimento linear de procura entre os anos.

**Funcionalidade dependente:** O recurso dependente é a coluna de dados que gostaríamos de nosso modelo para prever. Com o [supervisionado aprendizagem](https://en.wikipedia.org/wiki/Supervised_learning), precisamos primeiro preparar o modelo usando os recursos dependentes (que é também conhecido como rótulos). Isso permite que o modelo aprender os padrões nos dados associados ao recurso dependente. Previsão de procura de energia, normalmente, queremos prever a procura real e, portanto, usaríamos-lo como o recurso dependente.

**Processamento de valores em falta:** Durante a fase de preparação de dados, precisamos de determinar a melhor estratégia para lidar com valores em falta. É principalmente para isso, usando as várias estatísticas [métodos de imputation dados](https://en.wikipedia.org/wiki/Imputation_\(statistics\)). No caso de previsão de procura de energia, podemos normalmente impute valores em falta utilizando a média móvel de pontos de dados disponíveis anteriores.

**Normalização de dados:** Normalização de dados é outro tipo de transformação que é usado para trazer todos os dados numéricos, como a previsão de procura numa escala similar. Isso normalmente ajuda a melhorar a precisão do modelo e a precisão. Normalmente, fazer isso, dividindo o valor real por intervalo de dados.
Isto irá reduzir o valor original num intervalo menor, normalmente entre -1 e 1.

## <a name="modeling"></a>Modelação
A fase de modelagem é onde ocorre a conversão dos dados num modelo. No núcleo desse processo lá são avançados algoritmos de analisar os dados históricos (dados de treinamento), extrair os padrões e criar um modelo. Esse modelo pode ser utilizado mais tarde para prever sobre novos dados que não foi utilizados para criar o modelo.

Assim que tivermos um modelo de fiável de trabalho que, em seguida, pode utilizá-lo para pontuar novos dados estruturados para incluir as funcionalidades necessárias (X). O processo de classificação fará com que utilize do modelo persistente (objeto da fase de treinamento) e prever a variável de destino que está em falta por Ŷ.

### <a name="demand-forecasting-modeling-techniques"></a>Técnicas de modelagem de previsão de procura
No caso de fazermos de previsão de procura utiliza dados históricos que estão ordenados por tempo. Referimos geralmente dados que incluem a dimensão de hora como [série de tempo](https://en.wikipedia.org/wiki/Time_series). O objetivo de modelagem de série de tempo é encontrar tempo relacionadas tendências, sazonalidade, auto-correlação (correlação ao longo do tempo) e Formulá-las num modelo.

Nos últimos anos algoritmos avançados foram desenvolvidos para acomodar a previsão de séries de tempo e aumentar a exatidão da previsão. Resumidamente, vamos abordar algumas delas aqui.

> [!NOTE]
> Esta secção não se destina a ser utilizado como uma máquina de aprendizagem e a descrição geral de previsão, mas em vez disso, como um pequeno inquérito de modelar técnicas que são frequentemente utilizadas para previsão de procura. Para obter mais informações e instituições de ensino material sobre previsão de séries de tempo, recomendamos vivamente o livro online [previsão: princípios e práticas recomendadas](https://www.otexts.org/).
>
>

#### <a name="ma-moving-average"></a>**MA (média)**
Média móvel é uma das técnicas analíticas primeiro que foi utilizado para previsão de séries de tempo e é ainda uma das mais frequentemente utilizadas técnicas até hoje. Também é a base para mais avançadas técnicas de previsão. Com a média móvel, previsão o próximo ponto de dados por uma média ao longo dos pontos mais recentes de K, onde K denota a ordem da média móvel.

A técnica de média móvel tem o efeito de suavização a previsão e, portanto, não pode processar bem grande volatilidade dos dados.

#### <a name="ets-exponential-smoothing"></a>**ETS (nivelamento exponencial)**
Nivelamento exponencial (ETS) é uma família de vários métodos que usam uma média ponderada de pontos de dados recentes para prever o próximo ponto de dados. A idéia é atribuir pesos superior para valores mais recentes e gradualmente diminuir este peso para valores de medida mais antigos. Há uma série de métodos diferentes com esta família, alguns deles incluem manipulação de sazonalidade nos dados, como o método de Holt-Winters sazonais.

Alguns desses métodos também avaliar a sazonalidade dos dados.

#### <a name="arima-auto-regression-integrated-moving-average"></a>**ARIMA (média móvel integrada de regressão automática)**
Automática regressão integrada mover média (ARIMA) é outra família de métodos que é normalmente utilizada para previsão de série temporal. Praticamente combina métodos de regressão automática com a média móvel. Os métodos de regressão automática usar modelos de regressão, efetuando os valores de séries de tempo anterior para calcular o próximo ponto de data. Métodos ARIMA também se aplicam os métodos de diferenciação que incluem a calcular a diferença entre os pontos de dados e a utilização dos mesmos em vez do valor original medido. Por fim, ARIMA também usa as técnicas de média móveis discutidos acima. A combinação de todos esses métodos de várias formas é o que constrói a família de métodos ARIMA.

ETS e ARIMA são amplamente utilizada atualmente para previsão de procura de energia e muitos outros problemas de previsão. Em muitos casos combinados em conjunto para fornecer resultados muito precisos.

**Regressão múltipla geral** modelos de regressão poderiam ser a abordagem de modelagem mais importante dentro do domínio do machine learning e estatísticas. No contexto de séries de tempo usamos de regressão para prever os valores de futuros (*por exemplo,*, de procura). Regressão Vamos dar uma combinação de linear dos indicadores e saiba os pesos (também referidos como coeficientes) desses indicadores durante o processo de treinamento. O objetivo é produzir uma linha de regressão que irão prever o nosso valor previsto. Métodos de regressão adequam-se quando a variável de destino é numérica e, portanto, também se adequa a previsão de séries de tempo. Há uma grande variedade de métodos de regressão, incluindo modelos de regressão muito simples, tal como [regressão Linear](https://en.wikipedia.org/wiki/Linear_regression) e muito mais avançadas, como árvores de decisões [Random florestas](https://en.wikipedia.org/wiki/Random_forest), [Neural Redes](https://en.wikipedia.org/wiki/Artificial_neural_network)e aumentou a árvores de decisões.

Construir como um problema de regressão de previsão de procura de energia nos dá muita flexibilidade para selecionar os nossos recursos de dados que podem ser combinados dos dados de séries de tempo de procura real e os fatores externos como temperatura. Obter mais informações sobre as funcionalidades selecionadas são discutidas em engenharia da funcionalidade (consulte **preparação de dados e a funcionalidade de engenharia**) secção este manual de comunicação social.

A nossa experiência com a implementação e implementação do piloto de previsões de procura de energia, Achamos que os modelos de regressão avançadas que estão disponíveis no Azure ML tendem a produzir os melhores resultados e ganhar utilizá-los.

## <a name="model-evaluation"></a>Avaliação do modelo
Avaliação do modelo tem um papel fundamental dentro de **ciclo de desenvolvimento do modelo**. Neste passo, vamos ver em validar o modelo e seu desempenho com os dados da vida real. Durante o passo de modelagem usamos uma parte dos dados disponíveis para o modelo de formação. Durante a fase de avaliação, vamos dar o resto dos dados para testar o modelo. Praticamente, significa que podemos são manutenção os dados do novo modelo que tenham sido reestruturados e contém os mesmos recursos que o conjunto de dados de treinamento. No entanto, durante o processo de validação, vamos utilizar o modelo para prever a variável de destino, em vez de fornecer a variável de destino disponíveis. Normalmente, nos Referimos a esse processo como modelo de classificação. Podemos seria, em seguida, utilize os valores de destino VERDADEIRO e compará-los por aqueles de que o previsto. O objetivo aqui é medir e minimizar o erro de previsão, que significa que a diferença entre as previsões e o valor true. Avaliando a medição de erro é a chave, uma vez que gostaríamos de ajustar o modelo e validar se o erro, na verdade, está a diminuir. Ajuste fino do modelo pode ser feito modificando os parâmetros de modelo que controlam o processo de aprendizado ou ao adicionar ou remover recursos de dados (denominados [parâmetros paramétrico](https://channel9.msdn.com/Blogs/Azure/Data-Science-Series-Building-an-Optimal-Model-With-Parameter-Sweep)). Praticamente que significa que podemos poderá ter de iterar entre a engenharia de funcionalidades, de modelagem e modelar fases de avaliação várias vezes até que somos capazes de reduzir o erro para o nível necessário.

É importante ênfase que o erro de previsão nunca seja zero como nunca se dá um modelo que perfeitamente pode prever a cada resultado. No entanto, há uma determinada magnitude do erro que é aceitável pela empresa. Durante o processo de validação, gostaríamos de Certifique-se de que o nosso erro de previsão do modelo está no nível ou melhor do que o nível de tolerância de negócios. Portanto, é importante definir o nível do erro tolerável no início do ciclo de durante a **problema formulação** fase.

### <a name="typical-evaluation-techniques"></a>Técnicas de avaliação típicas
Existem várias formas na qual predição erro pode ser medido e quantificado. Nesta secção, nos concentraremos a discussão sobre técnicas de avaliação relevantes à série de tempo e, em particular para previsão de procura de energia.

#### <a name="mapehttpsenwikipediaorgwikimeanabsolutepercentageerror"></a>[**MAPE**](https://en.wikipedia.org/wiki/Mean_absolute_percentage_error)
MAPE significa significa absoluto erro de percentagem. Com MAPE são computando a diferença entre cada previstos ponto e o valor real desse ponto. Em seguida, podemos quantificar o erro por ponto, calculando a proporção da diferença em relação ao valor real. No último passo, vamos média estes valores. A fórmula matemática usada para MAPE é o seguinte:

![Fórmula MAPE](media/cortana-analytics-playbook-demand-forecasting-energy/mape-formula.png)
*onde A<sub>t</sub> é o valor real, F<sub>t</sub> é o valor previsto e n é o horizonte de previsão.*

## <a name="deployment"></a>Implementação
Assim que temos chegar a fase de modelagem e validar o desempenho de modelo, que estamos prontos para ir para a fase de implantação. Neste contexto, a implementação significa a habilitar o cliente consumir o modelo através da execução de previsões de indisponibilidade reais no mesmo em grande escala. O conceito de implantação é a chave do Azure ML, uma vez que o nosso objetivo principal é constantemente invocar previsões em vez de apenas obter a informação dos dados. A fase de implantação é a parte em que ativamos o modelo ser consumida em grande escala.

Dentro do contexto de previsão de procura de energia, nosso objetivo é invocar previsões periódicas e contínuas, garantindo que os dados atualizados estão disponíveis para o modelo e que os dados previstos são enviados de volta para o cliente de consumo.

### <a name="web-services-deployment"></a>Implementação de serviços da Web
O bloco de construção implementável principal no Azure ML é o serviço web. Esta é a maneira mais eficiente para permitir o consumo de um modelo preditivo na cloud. O serviço Web encapsula o modelo e termina com um [RESTful](https://www.restapitutorial.com/) API (Interface de programação de aplicativo). A API pode ser utilizada como parte de qualquer código de cliente, conforme ilustrado no diagrama abaixo.

![Estamos a implementação do serviço e o consumo](media/cortana-analytics-playbook-demand-forecasting-energy/web-service-deployment-and-consumption.png)

Como pode ser visto, o serviço web é implementado na nuvem do Cortana Intelligence Suite e pode ser invocado ao longo do seu ponto de extremidade exposto REST API. Um tipo diferente de clientes em vários domínios pode invocar o serviço através da API Web em simultâneo. O serviço web também pode dimensionar para oferecer suporte a milhares de chamadas simultâneas.

### <a name="a-typical-solution-architecture"></a>Uma arquitetura de solução típica
Ao implementar uma solução de previsão de procura de energia, estamos interessados na implantação de uma solução ponto a ponto que faz mais do que o serviço da web de predição e facilita o fluxo de dados inteiro. No momento que podemos invocar uma previsão de novo, precisamos de certificar-se de que o modelo é fornecido com os recursos de dados atualizados. Isso significa que os dados brutos coletados recentemente é constantemente ingeridos, processados e transformados na funcionalidade necessária definida no qual o modelo foi criado. Ao mesmo tempo, gostaríamos de disponibilizar os dados previstos para o end os clientes consumidores. Um exemplo dados de ciclo de fluxo (ou pipeline de dados) está ilustrado no diagrama abaixo:

![Fluxo de dados ponto a ponto da previsão de procura de energia](media/cortana-analytics-playbook-demand-forecasting-energy/energy-demand-forecase-end-data-flow.png)

Estes são os passos que ocorrem como parte do ciclo de previsão de procura de energia:

1. Milhões de medidores de dados implementados estão constantemente a gerar dados de consumo de energia em tempo real.
2. Estes dados está a ser recolhidos e carregada para o repositório na cloud (*por exemplo,*, BLOBs do Azure).
3. Antes de ser processado, os dados não processados são agregados a um subestação ou a nível regional, conforme definido pela empresa.
4. O processamento do recurso (consulte **preparação de dados e a funcionalidade de processamento**), em seguida, ocorra e produz os que é necessários para um modelo de dados de preparação ou de classificação – os dados do conjunto de recursos são armazenados numa base de dados (*por exemplo,*, SQL Azure).
5. O serviço novamente treinamento é invocado para voltar a preparar o modelo de previsão – essa versão atualizada do modelo é mantido para que possa ser utilizado pelo serviço web de pontuação.
6. O serviço web de pontuação é invocado com base numa agenda que se adequa a frequência de previsão necessária.
7. Os dados previstos são armazenados numa base de dados que pode ser acedida pelo cliente de consumo do fim.
8. O cliente de consumo obtém as previsões, aplica-se-lo novamente para a grade e consome-lo de acordo com o caso de uso necessárias.

É importante observar que esta todo o ciclo é totalmente automatizado e é executada com base numa agenda. A orquestração completa deste ciclo de dados pode ser feita usando as ferramentas, tal como [do Azure Data Factory](https://azure.microsoft.com/services/data-factory/).

### <a name="end-to-end-deployment-architecture"></a>Arquitetura de implantação de ponta a ponta
Para implementar praticamente uma solução de previsão de procura de energia no Cortana Intelligence, é necessário garantir que os componentes necessários são estabelecidos e configurados corretamente.

O diagrama seguinte ilustra uma arquitetura típica do Cortana Intelligence com base em que implementa e orquestra o ciclo de fluxo de dados que é descrito acima:

![Arquitetura de implantação de ponta a ponta](media/cortana-analytics-playbook-demand-forecasting-energy/architecture.png)

Para obter mais informações sobre cada um dos componentes e toda a arquitetura consulte o modelo de solução de energia.

