---
title: Abordagem e processo de adoção do Catálogo de Dados do Azure
description: Este artigo apresenta uma abordagem e o processo para que as organizações considerem a adoção do Catálogo de Dados do Azure, incluindo como definir uma visão, identificar casos de utilização empresarial fundamentais e escolher um projeto piloto.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: ebfaa59f1800da084d611f6e050a59da63a7f195
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674755"
---
# <a name="approach-and-process-for-adopting-azure-data-catalog"></a>Abordagem e processo de adoção do Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

Este artigo vai ajudá-lo a começar a adotar o **Catálogo de Dados do Azure** na sua organização. Para adotar com êxito o **Catálogo de Dados do Azure**, tem de se concentrar em três itens principais: definir a visão, identificar os casos de utilização empresarial fundamentais dentro da organização e escolher um projeto piloto.

## <a name="introducing-the-azure-data-catalog"></a>Introdução ao Catálogo de Dados do Azure

No mundo de trabalho, as expectativas das pessoas em relação à capacidade de encontrarem informações especializadas sobre recursos de dados foram alteradas. Atualmente, com a ampla utilização de ferramentas de redes sociais na área de trabalho, como o Yammer, as pessoas esperam conseguir obter rapidamente assistência e conselhos sobre uma vasta gama de tópicos. O **Catálogo de Dados do Azure** ajuda as empresas e as equipas a consolidar as informações sobre os recursos de dados da empresa num repositório central. Os consumidores de dados podem descobrir estes recursos de dados e obter conhecimento com contribuição de especialistas na matéria.

Este artigo apresenta uma abordagem para a introdução ao **Catálogo de Dados do Azure**. O artigo descreve um plano de adoção típico do Catálogo de Dados para a empresa fictícia com o nome Adventure Works.

**O que é o Catálogo de Dados do Azure?**

O **Catálogo de Dados do Azure** é um serviço completamente gerido no Azure e um catálogo de informações ao nível de toda a empresa (metadados) que permite a deteção personalizada de origens de dados. Com o Catálogo de Dados pode registar, detetar, anotar e ligar a recursos de dados. O Catálogo de Dados foi concebido para gerir recursos de informações diferentes para facilitar a localização de recursos de dados, compreendê-los e ligar aos mesmos. Reduz o tempo de obter informações a partir dos dados disponíveis e aumenta o valor para as organizações. Para mais informações, veja [Catálogo de Dados do Microsoft Azure](https://azure.microsoft.com/services/data-catalog/).

## <a name="azure-data-catalog-adoption-plan"></a>Plano de adoção do Catálogo de Dados do Azure

Um plano de adoção do **Catálogo de Dados do Azure** descreve como as vantagens da utilização do serviço são comunicadas aos intervenientes e utilizadores, bem como o tipo de formação que vai fornecer aos utilizadores. Um fator fundamental para o êxito da adoção do Catálogo de Dados é a forma como comunica eficazmente o valor do serviço aos utilizadores e intervenientes. As audiências primárias num plano de adoção inicial são os utilizadores do serviço. Independentemente da quantidade de compra dos intervenientes, se os utilizadores ou os clientes da sua oferta do Catálogo de Dados não o incorporarem na deles, a adoção não vai ser bem-sucedida. Portanto, este artigo assume que tem uma compra do interveniente e foca-se em criar um plano para a adoção do utilizador do Catálogo de Dados.
Um plano de adoção eficaz envolve os utilizadores nas capacidades do Catálogo de Dados e fornece-lhes as informações e a orientação para as alcançarem. Os utilizadores têm de compreender o valor que o Catálogo de Dados lhes fornece para ajudá-los a terem êxito nas tarefas deles. Quando as pessoas vêem como o Catálogo de Dados pode ajudá-las a alcançar mais resultados com os dados, o valor da adoção do Catálogo de Dados torna-se claro. A mudança é difícil. Por conseguinte, é necessário um plano eficaz para considerar os desafios da mudança.

Um plano de adoção ajuda-o a comunicar o que é fundamental para as pessoas terem êxito e alcançarem os seus objetivos. Um plano típico explica como o Catálogo de Dados vai facilitar a vida do utilizador e inclui as seguintes partes:

* **Declaração de Visão** - Ajuda-o a discutir de forma concisa o plano de adoção com os utilizadores e intervenientes. É a sua abordagem rápida.
* **Equipa piloto e Influenciadores** - Aprender com uma equipa piloto e influenciadores vai ajudá-lo a refinar a apresentação do Catálogo de Dados a equipas e utilizadores. Os influenciadores podem ministrar formação entre pares aos colegas utilizadores. Também ajuda a identificar bloqueadores e fatores da adoção.
* **Planear as Comunicações e o Buzz** - Ajuda os utilizadores a compreender de que forma o Catálogo de Dados pode ajudá-los e como pode promover a adoção orgânica nas equipas e, em última análise, em toda a organização.
* **Plano de Formação** - A formação abrangente resulta normalmente no êxito da adoção e em resultados favoráveis.

Eis algumas sugestões para definir um plano de adoção do **Catálogo de Dados do Azure**.

## <a name="define-your-data-catalog-project-vision"></a>Definir a sua visão de projeto do Catálogo de Dados

O primeiro passo para definir um plano de adoção do **Catálogo de Dados do Azure** é escrever uma descrição aspiracional dos seus objetivos. É melhor manter a declaração de visão razoavelmente abrangente, mas suficientemente concisa para definir os objetivos a curto e a longo prazo.

Eis algumas sugestões para ajudá-lo a definir a visão:

* **Identificar o fator de implementação chave** - Considere as necessidades específicas de gestão de origens de dados da empresa que podem ser resolvidas com o Catálogo de Dados. Ajuda a indicar as principais vantagens da utilização do Catálogo de Dados. Por exemplo, podem existir origens de dados comuns que todos os novos funcionários devem conhecer e saber como utilizar ou origens de dados importantes e complexas que apenas são compreendidas profundamente por algumas pessoas essenciais. O **Catálogo de Dados do Azure** pode facilitar a deteção e a compreensão destas origens de dados, para que estes pontos de tensão bem conhecidos possam ser resolvidos direta e antecipadamente na adoção do serviço.
* **Seja explícito e claro** - Uma compreensão clara da visão vai colocar todos os utilizadores ao mesmo nível em relação ao valor que o Catálogo de Dados traz à organização e como a visão suporta os objetivos organizacionais.
* **Seja uma inspiração para os utilizadores que pretendem utilizar o Catálogo de Dados** - A sua visão e o plano de comunicação devem inspirar os utilizadores a reconhecerem que o Catálogo de Dados pode ser-lhes útil para localizar e ligar às origens de dados para alcançarem mais com os dados.
* **Especificar os objetivos e a linha cronológica** - Garante que o seu plano de adoção tem resultados específicos e alcançáveis. Uma linha cronológica mantém todas as pessoas focadas e permite que os pontos de verificação meçam o sucesso.

Eis um exemplo de declaração de visão para um plano de adoção típico do Catálogo de Dados para a empresa fictícia com o nome Adventure Works:

**O Azure Data Catalog** capacita a equipa da Adventure Works Finance para colaborar em fontes de dados chave, para que cada membro da equipa possa facilmente encontrar e utilizar os dados de que necessita e partilhar os seus conhecimentos com a equipa como um todo.

Assim que tiver uma declaração de visão nítida, deve identificar um projeto piloto adequado ao Catálogo de Dados. Geralmente, existem vários cenários para o Catálogo de Dados, pelo que a secção seguinte fornece algumas sugestões para identificar casos de utilização relevantes.

## <a name="identify-data-catalog-business-use-cases"></a>Identificar casos de utilização empresarial do Catálogo de Dados

Para identificar os casos de utilização que são relevantes para o Catálogo de Dados, interaja com especialistas de várias unidades de negócio para identificar casos de utilização relevantes e problemas de negócio a resolver. Reveja os desafios que as pessoas têm na identificação e compreensão dos recursos de dados. Por exemplo, as equipas sabem mais sobre os recursos de dados apenas após perguntarem a várias pessoas na organização que possuem origens de dados relevantes?

É melhor escolher casos de utilização acessíveis: cenários que são importantes mas que têm uma elevada probabilidade de sucesso se forem resolvidos com o Catálogo de Dados.

Eis algumas sugestões para identificar casos de utilização:

* **Defina os objetivos da equipa** -De que modo a equipa atinge os objetivos? Não se foque ainda no Catálogo de Dados, pois pretende ser objetivo nesta fase. Lembre-se de que se trata dos resultados de negócios, não da tecnologia.
* **Defina o problema empresarial** - Quais são os problemas que a equipa enfrenta relativamente a localizar e saber mais sobre os recursos de dados? Por exemplo, poderão encontrar informações sobre origens de dados importantes nos livros do Excel numa pasta de rede, e a equipa pode passar muito tempo a localizar os livros.
* **Compreenda a cultura de equipa relacionada com a mudança** - Muitos desafios de adoção estão relacionados com a resistência a mudanças, e não com a implementação de uma nova ferramenta. A forma como uma equipa responde à mudança é importante ao identificar casos de utilização, uma vez que o processo existente pode estar implementado porque "Fizemos sempre assim" ou "se funciona, porquê mudar?". A adoção de qualquer nova ferramenta ou processo é sempre mais fácil quando as pessoas afetadas compreendem o valor que irá ser alcançado com a mudança e apreciam a importância de resolver os problemas.
* **Mantenha o foco relacionado com os ativos de dados** - Ao discutir os problemas de negócio que uma equipa enfrenta, é necessário "cortar as weeds", e focar-se no que é relevante para alavancar os ativos de dados da empresa de forma mais eficaz.

Eis alguns casos de utilização de exemplo relacionados com o Catálogo de Dados:

### <a name="example-use-cases"></a>Casos de utilização de exemplo

* **Registar origens de dados de alto valor central** - As TI gerem as origens de dados utilizadas em toda a organização. As TI podem introduzir o Catálogo de Dados ao registar e anotar origens de dados empresariais comuns.
* **Registar origens de dados com base em equipas** - Equipas diferentes têm origens de dados úteis e de linha de negócio. Introduza o **Catálogo de Dados do Azure** ao identificar e registar origens de dados fundamentais utilizadas por várias equipas diferentes, e capture o conhecimento tribal da equipa em anotações do **Catálogo de Dados do Azure**.
* **Business intelligence personalizada** - As equipas passam muito tempo a combinar dados de várias origens. Registe e anote as origens de dados numa localização central para eliminar o processo manual de deteção de origens de dados.

Para ler mais sobre os cenários do Catálogo de Dados, veja [Cenários comuns do Catálogo de Dados do Azure](data-catalog-common-scenarios.md).

Depois de identificar alguns casos de utilização do Catálogo de Dados, devem surgir cenários comuns. A secção seguinte explica como identificar o primeiro projeto piloto com base num caso de utilização.

## <a name="choose-a-data-catalog-pilot-project"></a>Escolha um projeto piloto do Catálogo de Dados

Um fator de êxito essencial é simplificar e começar por algo pequeno. Um projeto piloto bem definido e com um âmbito restrito ajuda a manter a continuidade do projeto sem ficar atolado num projeto demasiado complexo ou com demasiados participantes. Mas também é importante incluir uma mistura de utilizadores, de adotantes iniciais aos céticos. Os utilizadores que aderirem à solução ajudam a refinar a sua comunicação futura e o plano de buzz. Os céticos ajudam a identificar e a abordar problemas de bloqueio. À medida que os céticos se transformam em campeões, pode utilizar os comentários deles para identificar fatores de êxito.

O plano piloto deve fasear-se nos objetivos empresariais que pretende alcançar com o Catálogo de Dados. À medida que aprende a partir do projeto piloto inicial, pode expandir a base de utilizadores. É aconselhável um projeto piloto inicial fechado para estabelecer êxito mensurável, mas é o objetivo principal é o crescimento orgânico ou viral. Com o crescimento orgânico do Catálogo de Dados, os utilizadores controlam a própria utilização dos dados e podem influenciar e encorajar outras pessoas a adotarem e a contribuírem para o catálogo.

### <a name="target-the-right-team"></a>Escolha a equipa certa

Ao escolher o projeto piloto, selecione uma equipa com os cenários mais apelativos para resolver um problema empresarial existente. Por exemplo, um analista de negócios cria relatórios a partir de uma base de dados SQL Server. O problema é que só tomaram conhecimento da fonte de dados depois de falarem com vários colegas. Finalmente, depois de perder tempo a tentar descobrir quais as fontes de dados a utilizar, descobriram sobre um livro do Excel, que contém uma descrição de cada fonte de dados. Embora o livro excel descreva adequadamente as tabelas de que necessitam, teriam rapidamente encontrado estas fontes de dados se fossem registadas e anotadas no **Catálogo de Dados do Azure.**

### <a name="identify-data-heroes"></a>Identificar heróis de dados

O primeiro projeto piloto deve incluir algumas pessoas que produzem e consomem dados, para que a equipa tenha uma representação balanceada.

Os **Produtores de Dados** são os utilizadores com conhecimentos sobre origens de dados. Por exemplo, noutra equipa, o David trabalhou extensivamente com origens de dados fundamentais do Adventure Works. Antes da adoção do **Catálogo de Dados do Azure**, o David criou um livro do Excel para capturar informações sobre origens de dados do Adventure Works.

Os **Consumidores de Dados** são as pessoas com conhecimentos sobre a utilização dos dados para resolver problemas empresariais. Por exemplo, a Teresa é uma analista de negócios que utiliza origens de dados SQL Server do Adventure Works para analisar os dados.

Um dos problemas empresariais que o **Catálogo de Dados do Azure** aborda é ligar **Produtores de Dados** a **Consumidores de Dados**. Consegue-o servindo como um repositório central para informações sobre origens de dados empresariais. Com o Catálogo de Dados, o David regista origens de dados do Adventure Works e do SQL Server. A utilização de crowdsourcing qualquer utilizador que descubra esta fonte de dados pode partilhar as suas opiniões sobre os dados, além de utilizar os dados que descobriram. Por exemplo, a Teresa deteta as origens de dados ao pesquisar o catálogo e partilha os conhecimentos especializados sobre os dados.  Agora, as outras pessoas na organização beneficiam do conhecimento partilhado ao pesquisarem o catálogo de dados.

* Para saber mais sobre como registar origens de dados, veja [Registar origens de dados](data-catalog-get-started.md).
* Para saber mais sobre como detetar origens de dados, veja [Pesquisar origens de dados](data-catalog-get-started.md).

### <a name="start-small-and-focused"></a>Comece por algo pequeno e mantenha-se focado

Para a maioria dos projetos piloto empresariais, deve fazer o seeding do catálogo com origens de dados de elevado valor, para que os utilizadores empresariais possam ver rapidamente o valor do Catálogo de Dados. As TI são um bom local para começar a identificar as origens de dados comuns que seriam de interesse para a sua equipa piloto. Para origens de dados suportadas, como o SQL Server, recomendamos que utilize a ferramenta de registo de origem de dados do **Catálogo de Dados do Azure**. Com a ferramenta de registo de origem de dados, pode registar uma vasta gama de origens de dados, incluindo bases de dados SQL Server e Oracle, e relatórios do SQL Server Reporting Services. Para obter uma lista completa das origens de dados atuais, veja [Origens de dados suportadas pelo Catálogo de Dados do Azure](data-catalog-dsr.md).

Depois de ter identificado e registado as origens de dados fundamentais, é possível importar também descrições de origens de dados armazenadas noutras localizações. A API do Catálogo de Dados permite aos programadores carregar descrições e anotações de outra localização, como o livro do Excel que o David criou e mantém.

A secção seguinte descreve um projeto de exemplo da empresa Adventure Works.

### <a name="an-example-project"></a>Um projeto de exemplo

Para este exemplo, Teresa, a analista de negócio, cria relatórios para a equipa dela, utilizando os dados de uma base de dados SQL Server. O problema é que só tomaram conhecimento da fonte de dados depois de falarem com vários colegas. Teriam rapidamente encontrado estas fontes de dados se fossem registadas e anotadas numa localização central, como **o Azure Data Catalog.**

Para ilustrar como a Teresa e a equipa dela podem facilmente encontrar dados de elevado valor, utilize a ferramenta de registo de origens de dados para povoar o catálogo com informações (metadados) sobre as origens de dados. Desta forma, as informações sobre a base de dados ficam disponível para a equipa e a empresa, não apenas para alguns indivíduos. Depois de as origens de dados serem registadas no Catálogo de Dados, a Nancy e a sua equipa podem utilizá-las facilmente. O resultado é um catálogo de dados mais abrangente e relevante para a equipa e a empresa. À medida que mais equipas adotam o Catálogo de Dados, as origens de dados empresariais ficam mais fáceis de encontrar e utilizar, permitindo desta forma uma cultura mais centrada em dados, para conseguir mais com os seus dados.

Para mais informações sobre a ferramenta de registo de origens de dados, veja [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md).

Como parte do projeto piloto, a equipa da Teresa também utiliza origens de dados que são descritas num livro do Excel que o David e os colegas dele mantêm. Uma vez que outras equipas na empresa também utilizam livros do Excel para descrever origens de dados, a equipa de TI decide criar uma ferramenta para migrar o livro do Excel para o Catálogo de Dados. Ao utilizar a API REST do Catálogo de Dados para importar anotações existentes, a equipa do projeto piloto pode ter um catálogo de dados completo com metadados extraídos das origens de dados, utilizando a ferramenta de registo de origens de dados, completa com as informações anteriormente documentadas pelos produtores e consumidores de dados, sem haver necessidade de reintrodução manual. À medida que o catálogo de dados da empresa cresce, a organização pode utilizar a ferramenta de registo de origens de dados para origens de dados comuns, e a API do Catálogo de Dados para origens personalizadas e cenários pouco comuns.

> [!NOTE]
> Escrevemos uma ferramenta de exemplo que utiliza a API do **Catálogo de Dados do Azure** para migrar um livro do Excel para o Catálogo de Dados. Para obter informações sobre a API do Catálogo de Dados e a ferramenta de exemplo, pode [transferir o exemplo de código do livro Ad Hoc](https://github.com/Azure-Samples/data-catalog-dotnet-excel-register-data-assets) e consultar a documentação [API REST do Catálogo de Dados do Azure](/rest/api/datacatalog/).

Depois de o projeto piloto estar implementado, está na altura de executar o seu plano de adoção do Catálogo de Dados.

### <a name="execute"></a>Executar

Nesta altura, identificou casos de utilização do Catálogo de Dados e identificou o primeiro projeto. Além disso, registou as origens de dados fundamentais do Adventure Works e adicionou informações do livro do Excel, utilizando a ferramenta que as TI incorporaram. Agora está na altura de trabalhar com a equipa piloto para iniciar o processo de adoção do Catálogo de Dados.

Eis algumas sugestões para começar:

* **Provoque entusiasmo** - Os utilizadores empresariais vão ficar entusiasmados se pensarem que o **Catálogo de Dados do Azure** vai facilitar as suas vidas. Experimente basear a conversa na solução e nos respetivos benefícios, não na própria tecnologia.
* **Facilite a mudança** - Comece por algo pequeno e comunique o plano aos utilizadores empresariais. Para ter êxito, é fundamental envolver os utilizadores desde o início, para que possam influenciar o resultado e desenvolver um sentido de propriedade sobre a solução.
* **Estime os adotantes iniciais** - Os adotantes iniciais são utilizadores empresariais apaixonados pelo que fazem e entusiasmados por dar a conhecer as vantagens do **Catálogo de Dados do Azure** aos respetivos parceiros.
* **Direcione a formação** - Os utilizadores empresariais não precisam de saber tudo sobre o Catálogo de Dados; direcione a formação para abordar objetivos específicos da equipa. Concentre-se no que os utilizadores vão fazer e como algumas das respetivas tarefas podem ser alteradas, para incorporar o **Catálogo de Dados do Azure** nas rotinas diárias deles.
* **Esteja disposto a falhar** - Se o projeto piloto não estiver a alcançar os resultados pretendidos, reavalie e identifique as áreas a alterar/onde corrigir problemas no projeto piloto antes de passar para um âmbito maior.

Antes de a equipa piloto passar para a utilização do Catálogo de Dados, convoque uma reunião inicial para discutir expetativas para o projeto piloto e forneça formação inicial.

### <a name="set-expectations"></a>Definir expetativas

A definição de expetativas e objetivos ajuda os utilizadores empresariais a concentrarem-se em resultados específicos. Para manter o projeto controlado, atribua trabalhos de casa regulares (por exemplo, diária ou semanalmente com base no âmbito e na duração do projeto piloto). Uma das capacidades mais importantes do Catálogo de Dados é o crowdsourcing de recursos de dados, para que os utilizadores empresariais possam beneficiar do conhecimento dos dados empresariais. Um excelente trabalho de casa a atribuir é que cada membro da equipa piloto registe ou anote pelo menos uma origem de dados que utilizou. Veja [Registar uma origem de dados](data-catalog-get-started.md) e [Como anotar origens de dados](data-catalog-get-started.md).

Reúna regularmente com a equipa para rever algumas das anotações. As boas anotações sobre origens de dados estão no centro de uma adoção do Catálogo de Dados com êxito, pois fornecem informações significativas sobre origens de dados, numa localização central. Sem boa anotações, o conhecimento sobre origens de dados permanece disperso por toda a empresa. Veja [Como anotar origens de dados](data-catalog-get-started.md).

Por fim, o derradeiro teste do projeto é se os utilizadores conseguem detetar e compreender as origens de dados que têm de utilizar. Os utilizadores piloto devem testar o catálogo regularmente para assegurar que as origens de dados que utilizam para o trabalho diário são relevantes. Quando uma origem de dados necessária está em falta ou não foi corretamente anotada, tal deverá servir como um lembrete para registar origens de dados adicionais ou para fornecer anotações adicionais. Esta prática não só adiciona valor ao esforço do piloto, como também cria hábitos eficazes que vão ser transferidos para outras equipas depois de o projeto piloto estar concluído.

### <a name="provide-training"></a>Fornecer formação

A formação deve ser suficiente para que os utilizadores comecem e adaptada aos objetivos específicos e ao nível de experiência dos membros da equipa piloto. Para começar a formação, pode seguir os passos no artigo [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md). Além disso, pode transferir a [Apresentação da Formação do Projeto Piloto do Catálogo de Dados do Azure](https://github.com/Azure-Samples/data-catalog-dotnet-get-started/blob/master/Azure%20Data%20Catalog%20Training.pptx?raw=true). Esta apresentação do PowerPoint deve ajudá-lo a começar a apresentação do Catálogo de Dados aos membros da equipa piloto.

## <a name="conclusion"></a>Conclusão

Assim que a sua equipa piloto estiver a funcionar bem e tiver atingido os seus objetivos iniciais, deve expandir a adoção do Catálogo de Dados para mais equipas. Aplique e refine o que aprendeu no projeto piloto para expandir o Catálogo de Dados em toda a organização.

Os adotantes iniciais que participaram no projeto piloto podem ser úteis para espalhar a palavra sobre as vantagens da adotar o Catálogo de Dados. Eles podem partilhar com outras equipas a forma como o Catálogo de Dados ajudou a equipa a resolver problemas empresariais, detetar origens de dados mais facilmente e partilhar informações sobre as origens de dados que utilizam. Por exemplo, os adotantes iniciais na equipa piloto do Adventure Works poderiam mostrar a outras pessoas como é fácil localizar informações sobre recursos de dados do Adventure Works que antes eram difíceis de encontrar e compreender.

Este artigo informou-o sobre como introduzir o **Catálogo de Dados do Azure** na sua organização. Esperamos que tenha conseguido iniciar um projeto piloto do Catálogo de Dados e expandir o Catálogo de Dados na organização.

## <a name="next-steps"></a>Passos seguintes

[Criar um Catálogo de Dados do Azure](data-catalog-get-started.md)
