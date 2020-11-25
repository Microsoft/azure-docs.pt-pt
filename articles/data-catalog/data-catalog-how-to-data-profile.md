---
title: Como utilizar fontes de dados de perfis de dados no Catálogo de Dados Azure
description: Como escrever um artigo que realça como incluir perfis de dados de nível de tabela e coluna ao registar fontes de dados no Catálogo de Dados Azure e como usar perfis de dados para entender fontes de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: b27ff631ce13d70d15a6fd2b6dd5ad5bccaf5450
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021916"
---
# <a name="how-to-data-profile-data-sources-in-azure-data-catalog"></a>Como obter fontes de dados de perfil de dados no Catálogo de Dados Azure

## <a name="introduction"></a>Introdução

**O Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados empresariais. Por outras palavras, **o Azure Data Catalog** tem tudo a ver com ajudar as pessoas a descobrir, compreender e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes. Quando uma fonte de dados é registada no **Azure Data Catalog,** os seus metadados são copiados e indexados pelo serviço, mas a história não termina aí.

A funcionalidade de Perfis de **Dados** do Catálogo de Dados do **Azure** examina os dados de fontes de dados suportadas no seu catálogo e recolhe estatísticas e informações sobre esses dados. É fácil incluir um perfil dos seus ativos de dados. Quando registar um ativo de dados, escolha **Incluir perfil de dados** na ferramenta de registo de fonte de dados.

## <a name="what-is-data-profiling"></a>O que é perfis de dados

O perfil de dados examina os dados na fonte de dados que está a ser registada e recolhe estatísticas e informações sobre esses dados. Durante a descoberta da fonte de dados, estas estatísticas podem ajudá-lo a determinar a adequação dos dados para resolver o seu problema de negócio.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes fontes de dados suportam perfis de dados:

* SQL Server (incluindo Azure SQL DB e Azure Synapse Analytics) mesas e vistas
* Mesas e vistas do oráculo
* Teradata mesas e vistas
* Tabelas do Hive

A inclusão de perfis de dados ao registar ativos de dados ajuda os utilizadores a responder a questões sobre fontes de dados, incluindo:

* Pode ser usado para resolver o meu problema comercial?
* Os dados estão em conformidade com padrões ou padrões específicos?
* Quais são algumas das anomalias da fonte de dados?
* Quais são os possíveis desafios de integrar estes dados na minha aplicação?

> [!NOTE]
> Também pode adicionar documentação a um ativo para descrever como os dados podem ser integrados numa aplicação. Ver [Como documentar fontes de dados](data-catalog-how-to-documentation.md).
>

<a name="howto"></a>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registar uma fonte de dados

É fácil incluir um perfil da sua fonte de dados. Quando registar uma fonte de dados, no painel **de objetos a registar** da ferramenta de registo de fonte de dados, escolha Incluir Perfil de **Dados**.

![Incluir caixa de verificação de perfis de dados](media/data-catalog-data-profile/data-catalog-register-profile.png)

Para saber mais sobre como registar fontes de dados, consulte [Como registar fontes de dados](data-catalog-how-to-register.md) e [começar com o Azure Data Catalog](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtragem de ativos de dados que incluem perfis de dados

Para descobrir ativos de dados que incluam um perfil de dados, pode incluir `has:tableDataProfiles` ou como um dos seus termos de `has:columnsDataProfiles` pesquisa.

> [!NOTE]
> A seleção incluir o **perfil de dados** na ferramenta de registo de fonte de dados inclui informações de perfil de nível de tabela e coluna. No entanto, a API do Catálogo de Dados permite que os ativos de dados sejam registados com apenas um conjunto de informações de perfil incluídas.
>

## <a name="viewing-data-profile-information"></a>Visualização de informações sobre o perfil de dados

Assim que encontrar uma fonte de dados adequada com um perfil, pode ver os detalhes do perfil de dados. Para visualizar o perfil de dados, selecione um ativo de dados e escolha o **Perfil de Dados** na janela do portal do Catálogo de Dados.

![Separador de perfil de dados](media/data-catalog-data-profile/data-catalog-view.png)

Um perfil de dados no **Catálogo de Dados do Azure** mostra informações sobre o perfil da tabela e da coluna, incluindo:

### <a name="object-data-profile"></a>Perfil de dados do objeto

* Número de linhas
* Tamanho da tabela
* Quando o objeto foi atualizado pela última vez

### <a name="column-data-profile"></a>Perfil de dados da coluna

* Tipo de dados de coluna
* Número de valores distintos
* Número de linhas com valores NUOS
* Desvio mínimo, máximo, médio e padrão para valores de coluna

## <a name="summary"></a>Resumo

O perfil de dados fornece estatísticas e informações sobre ativos de dados registados para ajudá-lo a determinar a adequação dos dados para resolver problemas de negócio. Além de anotar e documentar fontes de dados, os perfis de dados podem dar aos utilizadores uma compreensão mais profunda dos seus dados.

## <a name="see-also"></a>Consulte também

* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
