---
title: Introdução ao Catálogo de Dados do Azure
description: Este artigo fornece uma descrição geral do Catálogo de Dados do Microsoft Azure, incluindo as suas funcionalidades e os problemas que se destina a resolver. O Catálogo de Dados permite a qualquer utilizador registar-se, detetar, compreender e consumir origens de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: overview
ms.date: 08/01/2019
ms.openlocfilehash: e128e9f7515e572fc0be4b92ef03d8d98529ac66
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674908"
---
# <a name="what-is-azure-data-catalog"></a>O que é o Catálogo de Dados do Azure?
[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

O Azure Data Catalog é um serviço de nuvem totalmente gerido. Permite que os utilizadores descubram as fontes de dados de que necessitam e compreendam as fontes de dados que encontram. Ao mesmo tempo, o Catálogo de Dados ajuda as organizações a obter mais valor dos investimentos existentes.

Com o Catálogo de Dados, qualquer utilizador (analista, cientista de dados ou programador) pode detetar, compreender e consumir origens de dados. O Catálogo de Dados inclui um modelo de crowdsourcing dos metadados e anotações. É um local central único onde todos os utilizadores de uma organização podem contribuir com o seu conhecimento e criar uma comunidade e cultura de dados.

## <a name="discovery-challenges-for-data-consumers"></a>Desafios de deteção para os consumidores de dados

Tradicionalmente, detetar origens de dados empresariais era um processo orgânico com base em conhecimento tribal. Para as empresas que querem obter o máximo valor dos respetivos recursos de informações, esta abordagem coloca vários desafios:

* Os utilizadores podem não saber que existe uma fonte de dados a menos que entrem em contacto com ele como parte de outro processo. Não existe uma localização central onde as origens de dados estão registadas.
* A menos que os utilizadores saibam qual a localização de uma origem de dados, não poderão ligar-se aos dados através de uma aplicação cliente. As experiências de consumo de dados requerem que os utilizadores saibam qual é o caminho ou cadeia de ligação.
* A menos que os utilizadores saibam qual a localização da documentação de uma origem de dados, não poderão compreender as utilizações previstas dos dados. A documentação e as origens de dados podem residir numa variedade de locais e ser consumidas através de uma variedade de experiências.
* Se os utilizadores tiverem perguntas sobre um recurso de informações, têm de localizar o especialista ou equipa responsável pelos dados e contactá-lo offline. Não existe uma ligação explícita entre os dados e os especialistas que têm perspetivas sobre a sua utilização.
* A menos que os utilizadores compreendam o processo para pedir acesso à origem de dados, a deteção da origem de dados e a respetiva documentação continua a não ajudá-los a aceder aos dados.

## <a name="discovery-challenges-for-data-producers"></a>Desafios de deteção para os produtores de dados

Embora os consumidores de dados enfrentem os desafios que apresentámos, os utilizadores responsáveis pela produção e manutenção dos recursos de informações enfrentam os seus próprios desafios:

* Anotar origens de dados com metadados descritivos é, geralmente, um desperdício de esforço. As aplicações cliente tendem a ignorar descrições armazenadas na origem de dados.
* Criar documentação para origens de dados é, geralmente, um desperdício de esforço. Manter a documentação sincronizada com fontes de dados é uma responsabilidade permanente. Os utilizadores podem não ter confiança na documentação que é entendida como estando desatualizada.
* Criar e manter documentação para origens de dados é complexo e moroso. Assegurar a disponibilidade imediata dessa documentação para todos os utilizadores da origem de dados é ainda mais complexo.
* Restringir o acesso às origens de dados e garantir que os consumidores de dados sabem como pedir acesso é um desafio contínuo.

Quando estes desafios se sobrepõem, apresentam um barreira significativa para as empresas que querem encorajar e promover a utilização e a compreensão dos dados empresariais.

## <a name="azure-data-catalog-can-help"></a>O Catálogo de Dados do Azure pode ajudar

O Catálogo de Dados foi concebido para resolver estes problemas e para ajudar as empresas a obter o máximo valor dos recursos de informações existentes. O Catálogo de Dados torna os dados facilmente detetáveis e compreensíveis pelos utilizadores que gerem os dados.

O Catálogo de Dados fornece um serviço baseado na cloud, no qual uma origem de dados pode ser registada. Os dados permanecem na localização existente, mas uma cópia dos respetivos metadados é adicionada ao Catálogo de Dados, juntamente com uma referência à localização da origem de dados. Os metadados também são indexados para tornar cada origem de dados facilmente detetável através da pesquisa e compreensível para os utilizadores que a detetarem.

Depois de uma fonte de dados ter sido registada, os seus metadados podem então ser enriquecidos. Os metadados podem ser adicionados pelo utilizador que o registou ou por outros utilizadores da empresa. Qualquer utilizador pode anotar uma origem de dados, fornecendo as descrições, etiquetas ou outros metadados, como a documentação e os processos para pedir acesso à origem de dados. Estes metadados descritivos complementam os metadados estruturais (como nomes de colunas e tipos de dados) registados a partir da origem de dados.

Detetar e compreender origens de dados e a sua utilização são o objetivo principal de registar as origens. Os utilizadores empresariais poderão precisar de dados para business intelligence, programação de aplicações, ciência de dados ou para qualquer outra tarefa em que sejam necessários os dados certos. Podem utilizar a experiência de deteção do Catálogo de Dados para encontrar rapidamente os dados que satisfaçam as suas necessidades, compreender os dados para avaliar a adequação dos mesmos ao objetivo em questão e consumir os dados ao abrir a origem de dados na ferramenta que preferirem. 

Ao mesmo tempo, os utilizadores podem contribuir para o catálogo ao identificar, documentar e anotar origens de dados que já tenham sido registadas. Também podem registar novas origens de dados que a comunidade de utilizadores do catálogo pode, desta forma, detetar, compreender e consumir.

![Capacidades do Catálogo de Dados](./media/data-catalog-what-is-data-catalog/data-catalog-capabilities.png)

## <a name="learn-more-about-data-catalog"></a>Saiba mais sobre o Catálogo de Dados

Para saber mais sobre as funcionalidades do Catálogo de Dados, veja:

* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Como descobrir origens de dados](data-catalog-how-to-discover.md)
* [Como anotar origens de dados](data-catalog-how-to-annotate.md)
* [Como documentar origens de dados](data-catalog-how-to-documentation.md)
* [Como ligar a origens de dados](data-catalog-how-to-connect.md)
* [Como trabalhar com macrodados](data-catalog-how-to-big-data.md)
* [How to manage data assets (Como gerir recursos de dados)](data-catalog-how-to-manage.md)
* [Como configurar o Glossário Comercial](data-catalog-how-to-business-glossary.md)
* [Perguntas mais frequentes](data-catalog-frequently-asked-questions.md)

## <a name="next-steps"></a>Passos seguintes

Para começar com o Catálogo de Dados:

* [Quickstart: Criar um Catálogo de Dados Azure](data-catalog-get-started.md)
* [Abra o seu Catálogo de Dados Azure](https://www.azuredatacatalog.com)