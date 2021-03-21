---
title: Melhores práticas de implementação
description: Este artigo fornece as melhores práticas para a implementação do Azure Purview. O Azure Purview permite que qualquer utilizador registe, descubra, compreenda e consuma fontes de dados.
author: shsandeep123
ms.author: sandeepshah
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 48966e481f9cf8796c866b5c15a4e2a8616eade7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "97695856"
---
# <a name="azure-purview-deployment-best-practices"></a>Azure Purview implementa as melhores práticas

Este artigo identifica tarefas comuns que podem ajudá-lo a implantar a Produção. Estas tarefas podem ser concluídas por fases, ao longo de um mês ou mais. Mesmo as organizações que já implementaram o Purview podem usar este guia para garantir que estão a tirar o máximo partido do seu investimento.

Uma implantação bem planeada de uma plataforma de governação de dados (como a Azure Purview), pode dar os seguintes benefícios:

- Melhor descoberta de dados
- Melhor colaboração analítica
- Retorno maximizado do investimento.

## <a name="prerequisites"></a>Pré-requisitos

* Acesso ao Microsoft Azure com uma subscrição de desenvolvimento ou produção
* Capacidade de criar recursos Azure, incluindo a Purview
* Acesso a fontes de dados como Azure Data Lake Storage ou Azure SQL em ambientes de teste, desenvolvimento ou produção
  * Para o armazenamento de data lake, o papel necessário para digitalizar é função de leitor
  * Para o SQL, a identidade deve ser capaz de consultar tabelas para amostragem de classificações
* Acesso ao Centro de Segurança Azure ou capacidade de colaborar com o Security Center Admin para rotulagem de dados

## <a name="identify-objectives-and-goals"></a>Identificar objetivos e objetivos

Muitas organizações iniciaram a sua jornada de governação de dados desenvolvendo soluções individuais que atendem a requisitos específicos de grupos isolados e domínios de dados em toda a organização. Embora as experiências possam variar dependendo da indústria, do produto e da cultura, a maioria das organizações tem dificuldade em manter controlos e políticas consistentes para este tipo de soluções.

Alguns dos objetivos comuns de governação de dados que pode querer identificar nas fases iniciais incluem:

* Maximizando o valor comercial dos seus dados
* Permitir uma cultura de dados onde os consumidores de dados podem facilmente encontrar, interpretar e confiar dados
* Crescente colaboração entre várias unidades de negócio para proporcionar uma experiência de dados consistente
* Fomentar a inovação acelerando a análise de dados para colher os benefícios da nuvem
* Tempo de diminuição para descobrir dados através de opções de self-service para vários grupos de habilidades
* Redução do tempo para o mercado para a entrega de soluções de análise que melhoram o serviço aos seus clientes
* Redução dos riscos operacionais que se devem à utilização de ferramentas específicas do domínio e tecnologia não apoiada

A abordagem geral consiste em dividir esses objetivos gerais em várias categorias e objetivos. Alguns exemplos incluem:

|Categoria|Objetivo|
|---------|---------|
|Deteção|Os utilizadores de administração devem ser capazes de digitalizar fontes de dados Azure e não-Azure (incluindo fontes no local) para recolher informações sobre os ativos de dados automaticamente.|
|Classificação|A plataforma deve classificar automaticamente os dados com base numa amostragem dos dados e permitir o sobreposição manual utilizando classificações personalizadas.|
|Consumo|Os utilizadores empresariais devem ser capazes de encontrar informações sobre cada ativo tanto para os metadados empresariais como técnicos.|
|Linhagem|Cada ativo deve mostrar uma visão gráfica dos conjuntos de dados subjacentes para que os utilizadores compreendam as fontes originais e que alterações foram feitas.|
|Colaboração|A plataforma deve permitir que os utilizadores colaborem fornecendo informações adicionais sobre cada ativo de dados.|
|Relatórios|Os utilizadores devem poder visualizar o reporte sobre a propriedade dos dados, incluindo dados sensíveis e dados que necessitem de enriquecimento adicional.|
|Governação de dados|A plataforma deve permitir ao administrador definir políticas de controlo de acesso e impor automaticamente o acesso aos dados com base em cada utilizador.|
|Fluxo de trabalho|A plataforma deve ter a capacidade de criar e modificar o fluxo de trabalho para que seja fácil escalar e automatizar várias tarefas dentro da plataforma.|
|Integração|Outras tecnologias de terceiros, como a bilhética ou a orquestração, devem poder integrar-se na plataforma através do script ou das APIs rest.|

## <a name="top-questions-to-ask"></a>Principais perguntas a fazer

Uma vez que a sua organização concorda com os objetivos e objetivos de alto nível, haverá muitas perguntas de vários grupos. É crucial reunir estas questões para elaborar um plano para resolver todas as preocupações. Algumas questões de exemplo que poderá encontrar durante a fase inicial:

1. Quais são as principais fontes de dados da organização e sistemas de dados?
2. Para fontes de dados que ainda não são suportadas pela Purview, quais são as minhas opções?
3. De quantas instâncias de Purview precisamos?
4. Quem são os utilizadores?
5. Quem pode digitalizar novas fontes de dados?
6. Quem pode modificar o conteúdo dentro da Purview?
7. Que processo posso utilizar para melhorar a qualidade dos dados em Purview?
8. Como arrancar a plataforma com ativos críticos existentes, termos glossários e contactos?
9. Como integrar-se com os sistemas existentes?
10. Como recolher feedback e construir um processo sustentável?

Embora possa não ter a resposta para a maioria destas perguntas imediatamente, pode ajudar a sua organização a enquadrar este projeto e garantir que todos os requisitos "must-have" podem ser cumpridos.

## <a name="include-the-right-stakeholders"></a>Incluir as partes interessadas certas

Para garantir o sucesso da implementação do Purview para toda a empresa, é importante envolver as partes interessadas certas. Apenas algumas pessoas estão envolvidas na fase inicial. No entanto, à medida que o âmbito se expande, você exigirá personalidades adicionais para contribuir para o projeto e fornecer feedback.

Algumas partes interessadas chave que você pode querer incluir:

|Persona|Funções|
|---------|---------|
|**Oficial chefe de dados**|O CDO supervisiona um conjunto de funções que podem incluir gestão de dados, qualidade de dados, gestão de dados mestre, ciência de dados, inteligência empresarial e criação de estratégia de dados. Podem ser o patrocinador do projeto de implementação de Purview.|
|**Proprietário de domínio/negócio**|Um empresário que influencia o uso de ferramentas e tem controlo orçamental|
|**Analista de Dados**|Capaz de enquadrar um problema de negócio e analisar dados para ajudar os líderes a tomar decisões empresariais|
|**Arquiteto de Dados**|Bases de dados de design para aplicações de linha de negócios críticas da missão, juntamente com a conceção e implementação da segurança dos dados|
|**Engenheiro de Dados**|Operar e manter a pilha de dados, extrair dados de diferentes fontes, integrar e preparar dados, configurar pipelines de dados|
|**Cientista de Dados**|Construir modelos analíticos e configurar produtos de dados a serem acedidos por APIs|
|**DB Administrador**|Possuir, acompanhar e resolver incidentes e pedidos relacionados com a base de dados no âmbito de acordos de nível de serviço (SLAs); Pode configurar oleodutos de dados|
|**DevOps**|Desenvolvimento e implementação de aplicações em linha de negócio; pode incluir escrever scripts e capacidades de orquestração|
|**Especialista em Segurança de Dados**|Avaliar a segurança global da rede e dos dados, que envolve dados que entram e saem de Purview|

## <a name="identify-key-scenarios"></a>Identificar cenários-chave

A competência pode ser usada para gerir centralmente a governação de dados através do espólio de dados de uma organização abrangendo ambientes de nuvem e no local. Para ter uma implementação bem sucedida, você deve identificar cenários-chave que são cruciais para o negócio. Estes cenários podem cruzar os limites da unidade de negócio ou impactar várias personalidades do utilizador, quer a montante quer a jusante.

Estes cenários podem ser escritos de várias maneiras, mas deve incluir pelo menos estas cinco dimensões:

1. Persona – Quem são os utilizadores?
2. Sistema de origem – Quais são as fontes de dados como a Azure Data Lake Storage Gen2 ou a Azure SQL Database?
3. Área de Impacto – Qual é a categoria deste cenário?
4. Cenários de detalhe – Como os utilizadores usam a Alças para resolver problemas?
5. Resultado esperado – Quais são os critérios de sucesso?

Os cenários devem ser específicos, exequíveis e executáveis com resultados mensuráveis. Alguns cenários de exemplo que pode utilizar:

|Scenario|Detalhes|Persona|
|---------|---------|---------|
|Catalogar ativos críticos de negócio|Preciso de informação sobre cada conjunto de dados para ter uma boa compreensão do que é. Este cenário inclui dados de metadados empresariais e técnicos sobre os dados definidos no catálogo. As fontes de dados incluem Azure Data Lake Storage Gen2, Azure Synapse DW e/ou Power BI. Este cenário também inclui recursos no local, como o SQL Server.|Analista de Negócios, Cientista de Dados, Engenheiro de Dados|
|Descubra ativos críticos de negócio|Preciso de um motor de busca que possa pesquisar todos os metadados do catálogo. Eu deveria ser capaz de pesquisar usando termo técnico, termo de negócio com pesquisa simples ou complexa usando wildcard.|Analista de Negócios, Cientista de Dados, Engenheiro de Dados, Administrador de Dados|
|Rastrear dados para compreender a sua origem e resolver problemas de dados|Preciso de ter linhagem de dados para rastrear dados em relatórios, previsões ou modelos de volta à sua origem original e compreender as mudanças e onde os dados residiram durante o ciclo de vida dos dados. Este cenário precisa de suportar os gasodutos de dados prioritários Azure Data Factory e Databricks.|Engenheiro de Dados, Cientista de Dados|
|Enriqueça metadados em ativos de dados críticos|Preciso enriquecer o conjunto de dados no catálogo com metadados técnicos que são gerados automaticamente. A classificação e a rotulagem são alguns exemplos.|Engenheiro de Dados, Domain/Proprietário de Empresas|
|Reger os ativos de dados com experiência amigável do utilizador|Preciso de um glossário de negócios para metadados específicos do negócio. Os utilizadores empresariais podem usar o Purview para cenários de self-service para anotar os seus dados e permitir que os dados sejam descobertos facilmente através da pesquisa.|Domain/Business Owner, Analista de Negócios, Cientista de Dados, Engenheiro de Dados|

## <a name="deployment-models"></a>Modelos de implementação

Se tiver apenas um pequeno grupo usando o Purview com casos básicos de uso do consumo, a abordagem poderia ser tão simples como ter uma instância de Purview para servir todo o grupo. No entanto, também pode perguntar-se se a sua organização precisa de mais do que uma instância de Purview. E se usando vários casos de Purview, como os empregados podem promover os ativos de uma fase para outra.

### <a name="determine-the-number-of-purview-instances"></a>Determinar o número de instâncias de Competência

Na maioria dos casos, deve haver apenas uma conta de Purview para toda a organização. Esta abordagem tira o máximo partido dos "efeitos de rede" onde o valor da plataforma aumenta exponencialmente em função dos dados que residem dentro da plataforma.

No entanto, existem exceções a este padrão:

1. **Testar novas configurações** – As organizações podem querer criar múltiplas instâncias para testar configurações ou classificações de digitalização em ambientes isolados. Apesar de existir uma funcionalidade de "versão" em algumas áreas da plataforma, como é o caso do glossário, seria mais fácil ter uma instância "descartável" para testar livremente.
2. **Separar teste, pré-produção e produção** – As organizações querem criar diferentes plataformas para diferentes tipos de dados armazenados em diferentes ambientes. Não é recomendado, uma vez que estes tipos de dados são diferentes tipos de conteúdo. Pode utilizar o termo glossário ao nível ou categoria de hierarquia superior para segregar tipos de conteúdo.
3. **Conglomerados e modelo federado** – Os conglomerados têm muitas vezes muitas unidades de negócio (BUs) que operam separadamente, e, em alguns casos, nem sequer partilham faturação uns com os outros. Nesses casos, a organização acabará por criar uma instância de visão para cada BU. Este modelo não é o ideal, mas pode ser necessário, especialmente porque muitas vezes as BUs não estão dispostas a partilhar faturação.
4. **Conformidade** – Existem alguns regimes de conformidade rigorosos, que tratam até os metadados como sensíveis e exigem que esteja numa geografia específica. Se uma empresa tem múltiplas geografias, a única solução é ter vários casos de Purview, um para cada geografia.

### <a name="create-a-process-to-move-to-production"></a>Criar um processo para passar à produção

Algumas organizações podem decidir manter as coisas simples trabalhando com uma única versão de produção de Purview. Provavelmente não precisam de ir além dos cenários de descoberta, pesquisa e navegação. Se alguns ativos têm termos glossários incorretos, é perdulário deixar as pessoas auto-corrigirem-se. No entanto, a maioria das organizações que pretendem implantar a Purview em várias unidades de negócio vai querer ter alguma forma de processo e controlo.

Outro aspeto importante a incluir no seu processo de produção é a forma como as classificações e os rótulos podem ser migrados. A Alçada tem mais de 90 classificadores de sistema. Pode aplicar classificações de sistema ou personalizados em ativos de ficheiros, tabelas ou colunas. As classificações são como etiquetas de assunto e são usadas para marcar e identificar conteúdo de um tipo específico encontrado dentro do seu espólio de dados durante a digitalização. Os rótulos de sensibilidade são usados para identificar as categorias de tipos de classificação dentro dos seus dados organizacionais e, em seguida, agrupar as políticas que deseja aplicar a cada categoria. Faz uso dos mesmos tipos de informação sensíveis que o Microsoft 365, permitindo-lhe esticar as suas políticas de segurança existentes e proteção em todo o seu conteúdo e propriedade de dados. Pode digitalizar e classificar automaticamente os documentos. Por exemplo, se tiver um ficheiro nomeado multiple.docx e tiver um número de Identificação Nacional no seu conteúdo, o Purview adicionará classificação como Número de Identificação Nacional da UE na página Detalhes do Ativo.

Em Purview, existem várias áreas onde os Administradores de Catálogo precisam de garantir a consistência e as melhores práticas de manutenção ao longo do seu ciclo de vida:

* **Ativos de** dados – As fontes de dados terão de ser reescanadas em ambientes. Não é recomendado digitalizar apenas no desenvolvimento e depois regenera-los usando APIs em Produção. A razão principal é que os scanners de Purview fazem muito mais "cablagem" nos bastidores dos ativos de dados, o que poderia ser complexo para movê-los para uma instância de visão diferente. É muito mais fácil adicionar a mesma fonte de dados na produção e digitalizar as fontes novamente. A melhor prática geral é ter documentação de todos os exames, ligações e mecanismos de autenticação que estão sendo usados.
* **Conjuntos de regras** de digitalização – Esta é a sua coleção de regras atribuídas a digitalizações específicas, tais como tipo de ficheiro e classificações para detetar. Se não tiver tantas regras de digitalização, é possível reutilizá-las manualmente novamente através da Produção. Isto requer um processo interno e uma boa documentação. No entanto, se a regra mudar diariamente ou semanalmente, isso poderá ser abordado explorando a rota REST API.
* **Classificações personalizadas** – As suas classificações também podem não ser alteradas regularmente. Durante a fase inicial de implantação, pode levar algum tempo a compreender vários requisitos para chegar a classificações personalizadas. No entanto, uma vez resolvido, isto exigirá pouca mudança. Assim, a recomendação aqui é migrar manualmente quaisquer classificações personalizadas sobre ou usar a API REST.
* **Glossário** – É possível exportar e importar termos glossários através do UX. Para cenários de automação, também pode utilizar a API REST.
* **Políticas de padrão definidas** por recursos – Esta funcionalidade é muito avançada para que qualquer organização típica se aplique. Em alguns casos, o seu Azure Data Lake Storage tem convenções de nomeação de pastas e estrutura específica que pode causar problemas para a Purview gerar o conjunto de recursos. A sua unidade de negócio poderá também querer alterar a construção do conjunto de recursos com personalizações adicionais que se adaptem às necessidades do negócio. Para este cenário, o melhor é acompanhar todas as alterações através da REST API e documentar as alterações através da plataforma de versão externa.
* **Atribuição de funções** – É aqui que controla quem tem acesso a Purview e quais as permissões que têm. A Purview também tem API REST para apoiar a exportação e importação de utilizadores e funções, mas isso não é compatível com a API atlas. A recomendação é atribuir um Grupo de Segurança Azure e gerir a adesão ao grupo.

### <a name="plan-and-implement-different-integration-points-with-purview"></a>Planear e implementar diferentes pontos de integração com a Purview

É provável que uma organização madura já tenha um catálogo de dados existente. A questão-chave é se continuar a utilizar a tecnologia existente e a sincronizar com a Purview. A visão permite a publicação de informações através das APIs do Atlas, mas não se destinam a apoiar este tipo de cenário. Algumas organizações podem decidir inicialmente como arrancar o uso da Purview migrando sobre os ativos de dados existentes a partir de outras soluções de catálogo de dados. Isto pode ser feito através das APIs do Atlas como uma abordagem unidireccionnal. A sincronização entre diferentes tecnologias de catálogo não deve ser considerada no desenho a longo prazo. O que normalmente aconteceu é que cada unidade de negócio pode continuar a usar as soluções existentes para ativos de dados mais antigos, enquanto a Purview seria usada para analisar fontes de dados mais recentes.

Para outros cenários de integração, como bilhética, interface personalizada de utilizador e orquestração, pode utilizar apis atlas e pontos finais de Kafka. Em geral, existem quatro pontos de integração com a Purview:

* **Data Asset** – Isto permite ao Purview digitalizar os ativos de uma loja de forma a enumerar o que são esses ativos e recolher quaisquer metadados facilmente disponíveis sobre eles. Assim, para o SQL esta poderia ser uma lista de DBs, tabelas, procedimentos armazenados, vistas e dados config sobre eles mantidos em lugares como `sys.tables` . Para algo como Azure Data Factory (ADF) isto poderia estar enumerando todos os oleodutos e obtendo dados sobre quando foram criados, última execução, estado atual.
* **Linhagem** – Isto permite ao Purview recolher informações de um sistema de análise/mutação de dados sobre a forma como os dados se movem. Para algo como Spark isto poderia estar a recolher informações da execução de um caderno para ver que dados o caderno ingeriu, como o transformou e onde o produção. Para algo como SQL, pode estar a analisar registos de consultas para reverter o engenheiro que operações de mutação foram executadas e o que fizeram. Apoiamos a linhagem baseada em empurrar e puxar dependendo das necessidades.
* **Classificação** – Isto permite ao Purview recolher amostras físicas de fontes de dados e executá-las através do nosso sistema de classificação. O sistema de classificação calcula a semântica de um pedaço de dados. Por exemplo, podemos saber que um ficheiro é um ficheiro Parquet e tem três colunas e a terceira é uma corda. Mas os classificadores que fazemos nas amostras dir-nos-ão que a corda é um nome, endereço ou número de telefone. Iluminar este ponto de integração significa que definimos como o Purview pode abrir objetos como cadernos, oleodutos, arquivos de parquet, tabelas e contentores.
* **A Embedded Experience** – Produtos que têm um "estúdio" como a experiência (como ADF, Synapse, SQL Studio, PBI e Dynamics) normalmente querem permitir que os utilizadores descubram dados com os quais queiram interagir e também encontrar locais para os dados de saída. O catálogo da Purview pode ajudar a acelerar estas experiências proporcionando uma experiência de incorporação. Esta experiência pode ocorrer ao nível da API ou do UX, por opção do parceiro. Ao incorporar uma chamada para o Purview, a organização pode aproveitar o mapa da Propriedade de Dados da Purview para encontrar ativos de dados, ver linhagem, verificar esquemas, olhar para classificações, contactos etc.

## <a name="phase-1-pilot"></a>Fase 1: Piloto

Nesta fase, o Purview deve ser criado e configurado para um conjunto muito pequeno de utilizadores. Normalmente, é apenas um grupo de 2-3 pessoas trabalhando juntas para correr através de cenários de ponta a ponta. São considerados os defensores da Purview na sua organização. O principal objetivo desta fase é garantir que as principais funcionalidades possam ser satisfeitas e os intervenientes certos estejam cientes do projeto.

### <a name="tasks-to-complete"></a>Tarefas a completar

|Tarefa|Detalhes|Duração|
|---------|---------|---------|
|Reúna & chegar a acordo sobre os requisitos|Discussão com todas as partes interessadas para reunir um conjunto completo de requisitos. Diferentes personalidades devem participar para acordar um subconjunto de requisitos para completar para cada fase do projeto.|1 Semana|
|Configurar o kit de arranque|Passe pelo [Purview Quick Start](create-catalog-portal.md) e crie o [Kit de Arranque de Purview](tutorial-scan-data.md) para despromugar o Purview a todas as partes interessadas.|1 Dia|
|Navegar em Purga|Entenda como usar o Purview a partir da página inicial.|1 Dia|
|Configurar a ADF para a linhagem|Identifique os principais oleodutos e os ativos de dados. Recolha todas as informações necessárias para ligar a uma conta ADF interna.|1 Dia|
|Digitalize uma fonte de dados como Azure Data Lake Storage|Adicione a fonte de dados e crie uma varredura. Certifique-se de que a varredura deteta com sucesso todos os ativos.|2 Dias|
|Pesquisar e navegar|Permitir que os utilizadores finais acedam ao Purview e realizem cenários de pesquisa e navegação de ponta a ponta.|1 Dia|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* A conta de visão é criada com sucesso na subscrição da organização sob o inquilino da organização.
* Um pequeno grupo de utilizadores com múltiplas funções pode aceder ao Purview.
* A visão está configurada para digitalizar pelo menos uma fonte de dados.
* Os utilizadores devem poder extrair valores-chave da Competência, tais como:
  * Pesquisar e navegar
  * Linhagem
* Os utilizadores devem ser capazes de atribuir a propriedade do ativo na página do ativo.
* Apresentação e demonstração para sensibilizar as principais partes interessadas.
* Buy-in da administração para aprovar recursos adicionais para a fase MVP.

## <a name="phase-2-minimum-viable-product"></a>Fase 2: Produto mínimo viável

Uma vez que você tenha os requisitos acordados e unidades de negócio participantes para embarcar Em Purga, o próximo passo é trabalhar numa versão de Produto Viável Mínimo (MVP). Nesta fase, irá expandir o uso do Purview para mais utilizadores que terão necessidades adicionais horizontal e verticalmente. Haverá cenários-chave que devem ser cumpridos horizontalmente para todos os utilizadores, tais como termos glossários, pesquisa e navegação. Haverá também requisitos de profundidade verticalmente para cada unidade de negócio ou grupo para cobrir cenários específicos de ponta a ponta, tais como a linhagem de Azure Data Lake Storage a Azure Synapse DW a Power BI.

### <a name="tasks-to-complete"></a>Tarefas a completar

|Tarefa|Detalhes|Duração|
|---------|---------|---------|
|[Scan Azure Synapse Analytics](register-scan-azure-synapse-analytics.md)|Comece a embarcar as suas fontes de base de dados e digitalize-as para preencher ativos-chave|2 Dias|
|[Criar classificações e regras personalizadas](create-a-custom-classification-and-classification-rule.md)|Uma vez digitalizados os seus ativos, os seus utilizadores poderão perceber que existem casos de utilização adicional para mais classificação ao lado das classificações padrão de Purview.|2-4 Semanas|
|[Scan Power BI](register-scan-power-bi-tenant.md)|Se a sua organização utilizar o Power BI, pode digitalizar o Power BI de forma a recolher todos os ativos de dados utilizados por Data Scientists ou Analistas de Dados que têm requisitos para incluir a linhagem a partir da camada de armazenamento.|1-2 Semanas|
|[Termos glossários de importação](how-to-create-import-export-glossary.md)|Na maioria dos casos, a sua organização pode já desenvolver uma coleção de termos glossários e atribuição de prazos a ativos. Isto exigirá um processo de importação para a Purview através de .csv ficheiro.|1 Semana|
|Adicionar contactos a ativos|Para os ativos de topo, pode querer estabelecer um processo para permitir que outras personalidades atribuam contactos ou importem através de APIs REST.|1 Semana|
|Adicione etiquetas sensíveis e digitalize|Isto pode ser opcional para algumas organizações, dependendo do uso da Rotulagem da M365.|1-2 Semanas|
|Obtenha classificação e insights sensíveis|Para reportagem e perspicácia em Purview, pode aceder a esta funcionalidade para obter vários relatórios e fornecer apresentação à gestão.|1 Dia|
|Utilizadores de adição a bordo que utilizam utilizadores geridos pela Purview|Este passo exigirá que o Administrador de Purview trabalhe com o Azure Ative Directory Admin para criar novos Grupos de Segurança que concedam acesso à Purview.|1 Semana|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* Com sucesso a bordo de um grupo maior de utilizadores para a Purview (50+)
* Digitalizar fontes de dados críticos do negócio
* Importar e atribuir todos os termos glossários críticos
* Teste com sucesso uma rotulagem importante nos activos-chave
* Cumpriu com sucesso cenários mínimos para os utilizadores de unidades de negócio participantes

## <a name="phase-3-pre-production"></a>Fase 3: Pré-produção

Uma vez ultrapassada a fase MVP, é hora de planear o marco da pré-produção. A sua organização pode decidir ter uma instância separada de Purview para pré-produção e produção, ou manter o mesmo caso, mas restringir o acesso. Também nesta fase, poderá querer incluir a digitalização em fontes de dados no local, como o SQL Server. Se houver alguma lacuna nas fontes de dados não suportadas pela Purview, é hora de explorar a API do Atlas para entender opções adicionais.

### <a name="tasks-to-complete"></a>Tarefas a completar

|Tarefa|Detalhes|Duração|
|---------|---------|---------|
|Refine a sua digitalização usando o conjunto de regras de digitalização|A sua organização terá muitas fontes de dados para pré-produção. É importante pré-definir critérios-chave para digitalização para que as classificações e extensão de ficheiros possam ser aplicadas de forma consistente em todo o tabuleiro.|1-2 Dias|
|Avaliar a disponibilidade da região para digitalização|Dependendo da região das fontes de dados e dos requisitos organizacionais em matéria de conformidade e segurança, poderá considerar quais as regiões que devem estar disponíveis para digitalização.|1 Dia|
|Compreenda o conceito de firewall ao digitalizar|Este passo requer alguma exploração de como a organização configura a sua firewall e como o Purview pode autenticar-se para aceder às fontes de dados para digitalização.|1 Dia|
|Compreenda o conceito de Ligação Privada ao digitalizar|Se a sua organização utilizar o Private Link, deve estabelecer as bases da segurança da rede para incluir o Private Link como parte dos requisitos.|1 Dia|
|[Digitalizar no local o SQL Server](register-scan-on-premises-sql-server.md)|Isto é opcional se tiver no local o SQL Server. A verificação requer a configuração [do tempo de execução de integração auto-hospedado](manage-integration-runtimes.md) e a adição do SQL Server como fonte de dados.|1-2 Semanas|
|Use a API rest de purview para cenários de integração|Se tiver requisitos para integrar o Purview com outras tecnologias do terceiro partido, como orquestração ou sistema de bilhética, poderá querer explorar a área da API REST.|1-4 Semanas|
|Compreender os preços da Visão|Este passo fornecerá à organização informações financeiras importantes para tomar uma decisão.|1-5 Dias|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* Com sucesso a bordo de pelo menos uma unidade de negócio com todos os utilizadores
* Digitalize a fonte de dados no local, como o SQL Server
* POC pelo menos um cenário de integração usando REST API
* Concluir um plano para ir para a produção que deve incluir áreas-chave em infraestruturas e segurança

## <a name="phase-4-production"></a>Fase 4: Produção

As fases acima referidas devem ser seguidas para criar uma governação da informação eficaz, que é a base para melhores programas de governação. A governação de dados ajudará a sua organização a preparar-se para as tendências crescentes como IA, Hadoop, IoT e blockchain. É apenas o começo para muitas coisas dados e análises, e há muito mais que pode ser discutido. O resultado desta solução proporcionaria:

* **Business Focused** - Uma solução alinhada com os requisitos e cenários do negócio em relação aos requisitos técnicos.
* **Future Ready** - Uma solução irá maximizar as funcionalidades padrão da plataforma e utilizar práticas padronizadas da indústria para atividades de configuração ou script para suportar os avanços/evolução da plataforma.

### <a name="tasks-to-complete"></a>Tarefas a completar

|Tarefa|Detalhes|Duração|
|---------|---------|---------|
|Digitalizar fontes de dados de produção com firewall habilitados|Se isto é opcional quando a firewall está no lugar, mas é importante explorar opções para endurecer a sua infraestrutura.|1-5 Dias|
|Ativar Ligação Privada|Se isto for opcional quando o Link Privado for utilizado. Caso contrário, pode ignorar isto, já que é um critério obrigatório quando o Privado está ativado.|1-5 Dias|
|Criar fluxo de trabalho automatizado|O fluxo de trabalho é importante para automatizar processos como aprovação, escalada, revisão e gestão de problemas.|2-3 Semanas|
|Documentação de operação|A governação de dados não é um projeto único. É um programa em curso para alimentar a tomada de decisões baseada em dados e criar oportunidades para o negócio. É fundamental documentar procedimentos-chave e normas comerciais.|1 Semana|

### <a name="acceptance-criteria"></a>Critérios de aceitação

* Com sucesso a bordo de todas as unidades de negócio e seus utilizadores
* Cumprir com sucesso os requisitos de infraestrutura e segurança para a produção
* Atendem com sucesso todos os casos de utilização exigidos pelos utilizadores

## <a name="platform-hardening"></a>Endurecimento da plataforma

Podem ser tomadas medidas adicionais de endurecimento:

* Aumente a postura de segurança, permitindo a verificação dos recursos de firewall ou utilize o Private Link
* Digitalização de âmbito de afinação para melhorar o desempenho da varredura
* Utilize APIs de REST para exportar metadados e propriedades críticas para backup e recuperação
* Utilize fluxo de trabalho para automatizar bilhetes e eventos para evitar erros humanos

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Executar o kit de arranque e digitalizar dados](tutorial-scan-data.md)
- [Tutorial: Navegue na página inicial e procure um ativo](tutorial-asset-search.md)