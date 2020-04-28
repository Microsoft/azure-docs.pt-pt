---
title: Como utilizar fontes de dados de perfis de dados no Catálogo de Dados do Azure
description: Como fazer o artigo destacando como incluir perfis de dados de nível de tabela e coluna ao registar fontes de dados no Catálogo de Dados do Azure e como usar perfis de dados para entender fontes de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 04ac6c2bf0137289221a4ae6ed58d5a71ad21739
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "68950226"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Como dados de fontes de dados no Catálogo de Dados do Azure

## <a name="introduction"></a>Introdução

O **Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados da empresa. Por outras palavras, o **Azure Data Catalog** tem tudo a ver com ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes. Quando uma fonte de dados é registada no **Azure Data Catalog,** os seus metadados são copiados e indexados pelo serviço, mas a história não termina aí.

A funcionalidade de Perfis de **Dados** do Catálogo de **Dados do Azure** examina os dados de fontes de dados suportadas no seu catálogo e recolhe estatísticas e informações sobre esses dados. É fácil incluir um perfil dos seus ativos de dados. Quando registar um ativo de dados, escolha **Incluir o Perfil de Dados** na ferramenta de registo de fonte de dados.

## <a name="what-is-data-profiling"></a>O que é Perfis de Dados

O perfil de dados examina os dados na fonte de dados que estão a ser registados e recolhe estatísticas e informações sobre esses dados. Durante a descoberta de fontes de dados, estas estatísticas podem ajudá-lo a determinar a adequação dos dados para resolver o seu problema de negócio.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes fontes de dados suportam o perfil de dados:

* SQL Server (incluindo mesas e vistas do Servidor SQL (incluindo Azure SQL DB e Azure SQL Data Warehouse)
* Mesas e vistas do oráculo
* Tabelas e vistas teradata
* Tabelas do Hive

Incluir perfis de dados ao registar os ativos de dados ajuda os utilizadores a responder a perguntas sobre fontes de dados, incluindo:

* Pode ser usado para resolver o meu problema de negócio?
* Os dados estão em conformidade com padrões ou padrões específicos?
* Quais são algumas das anomalias da fonte de dados?
* Quais são os possíveis desafios de integrar estes dados na minha aplicação?

> [!NOTE]
> Também pode adicionar documentação a um ativo para descrever como os dados podem ser integrados numa aplicação. Ver [Como documentar fontes de dados](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registar uma fonte de dados

É fácil incluir um perfil da sua fonte de dados. Quando registar uma fonte de dados, nos **Objetos a registar** o painel da ferramenta de registo de fonte de dados, escolha **Incluir o Perfil**de Dados .

![Incluir caixa de verificação de perfil de dados](media/data-catalog-data-profile/data-catalog-register-profile.png)

Para saber mais sobre como registar fontes de dados, consulte [como registar fontes](data-catalog-how-to-register.md) de dados e começar com o Catálogo de [Dados Do Azure](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtragem de ativos de dados que incluem perfis de dados

Para descobrir os ativos de dados que `has:tableDataProfiles` `has:columnsDataProfiles` incluem um perfil de dados, pode incluir ou como um dos seus termos de pesquisa.

> [!NOTE]
> A seleção **Incluir o Perfil de Dados** na ferramenta de registo de fontes de dados inclui informações de tabela e perfil de nível de coluna. No entanto, a API do Catálogo de Dados permite que os ativos de dados sejam registados com apenas um conjunto de informações de perfil incluídas.
>

## <a name="viewing-data-profile-information"></a>Visualização de informações sobre o perfil de dados

Assim que encontrar uma fonte de dados adequada com um perfil, pode visualizar os dados do perfil de dados. Para visualizar o perfil de dados, selecione um ativo de dados e escolha o **Perfil de Dados** na janela do portal Data Catalog.

![Separador de perfil de dados](media/data-catalog-data-profile/data-catalog-view.png)

Um perfil de dados no Catálogo de **Dados do Azure** mostra informações sobre tabela e perfil de coluna, incluindo:

### <a name="object-data-profile"></a>Perfil de dados de objetos

* Número de linhas
* Tamanho da mesa
* Quando o objeto foi atualizado pela última vez

### <a name="column-data-profile"></a>Perfil de dados da coluna

* Tipo de dados da coluna
* Número de valores distintos
* Número de linhas com valores NULOs
* Desvio mínimo, máximo, médio e padrão para valores de coluna

## <a name="summary"></a>Resumo

O perfil de dados fornece estatísticas e informações sobre os ativos de dados registados para ajudá-lo a determinar a adequação dos dados para resolver problemas de negócio. Juntamente com a anotação e documentação de fontes de dados, os perfis de dados podem dar aos utilizadores uma compreensão mais profunda dos seus dados.

## <a name="see-also"></a>Veja também

* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
