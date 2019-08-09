---
title: Como usar fontes de dados de criação de perfil de dados no catálogo de dados do Azure
description: Artigo de instruções que destaca como incluir perfis de dados em nível de tabela e coluna ao registrar fontes de dados no catálogo de dados do Azure e como usar perfis de dados para entender fontes de dados.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 4223d310bc747a518abaeadbbb467aa44871578f
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882517"
---
# <a name="data-profile-data-sources"></a>Origens de dados de perfis de dados

## <a name="introduction"></a>Introdução

**Microsoft Azure catálogo de dados** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados empresariais. Em outras palavras, o **Catálogo de dados do Azure** está prestes a ajudar as pessoas a descobrir, compreender e usar fontes de dados e ajudar as organizações a obter mais valor de seus dados existentes. Quando uma fonte de dados é registrada no **Catálogo de dados do Azure**, seus metadados são copiados e indexados pelo serviço, mas a história não termina.

O recurso **criação de perfil de dados** do catálogo de dados do **Azure** examina os dados de fontes de dados com suporte em seu catálogo e coleta estatísticas e informações sobre esses dados. É fácil incluir um perfil de seus ativos de dados. Ao registrar um ativo de dados, escolha **incluir perfil de dados** na ferramenta de registro da fonte de dados.

## <a name="what-is-data-profiling"></a>O que é criação de perfil de dados

A criação de perfil de dados examina os dados na fonte de dados que está sendo registrada e coleta estatísticas e informações sobre esses dados. Durante a descoberta da fonte de dados, essas estatísticas podem ajudá-lo a determinar a adequação dos dados para resolver o problema de negócios.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes fontes de dados dão suporte à criação de perfil de dados:

* Tabelas e exibições do SQL Server (incluindo o BD SQL do Azure e o Azure SQL Data Warehouse)
* Tabelas e exibições do Oracle
* Exibições e tabelas do Teradata
* Tabelas do hive

A inclusão de perfis de dados ao registrar ativos de dados ajuda os usuários a responder perguntas sobre fontes de dados, incluindo:

* Ele pode ser usado para resolver meu problema de negócios?
* Os dados estão em conformidade com padrões específicos ou padrões?
* Quais são algumas das anomalias da fonte de dados?
* Quais são os possíveis desafios de integrar esses dados ao meu aplicativo?

> [!NOTE]
> Você também pode adicionar documentação a um ativo para descrever como os dados podem ser integrados a um aplicativo. Consulte [como documentar fontes de dados](data-catalog-how-to-documentation.md).
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registrar uma fonte de dados

É fácil incluir um perfil de sua fonte de dados. Ao registrar uma fonte de dados, no painel **objetos a serem registrados** da ferramenta de registro de fonte de dados, escolha **incluir perfil de dados**.

![Caixa de seleção incluir perfil de dados](media/data-catalog-data-profile/data-catalog-register-profile.png)

Para saber mais sobre como registrar fontes de dados, consulte [como registrar fontes de dados](data-catalog-how-to-register.md) e [introdução ao catálogo de dados do Azure](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtragem de ativos de dados que incluem perfis de dados

Para descobrir ativos de dados que incluem um perfil de dados, você `has:tableDataProfiles` pode `has:columnsDataProfiles` incluir ou como um dos seus termos de pesquisa.

> [!NOTE]
> A seleção de **incluir perfil de dados** na ferramenta de registro de fonte de dados inclui informações de perfil em nível de tabela e coluna. No entanto, a API do catálogo de dados permite que os ativos de dados sejam registrados com apenas um conjunto de informações de perfil incluído.
>

## <a name="viewing-data-profile-information"></a>Exibindo informações do perfil de dados

Quando encontrar uma fonte de dados adequada com um perfil, você poderá exibir os detalhes do perfil de dados. Para exibir o perfil de dados, selecione um ativo de dados e escolha **perfil de dados** na janela do portal do catálogo de dados.

![Guia perfil de dados](media/data-catalog-data-profile/data-catalog-view.png)

Um perfil de dados no **Catálogo de dados do Azure** mostra informações de perfil de tabela e coluna, incluindo:

### <a name="object-data-profile"></a>Perfil de dados de objeto

* Número de linhas
* Tamanho da tabela
* Quando o objeto foi atualizado pela última vez

### <a name="column-data-profile"></a>Perfil de dados de coluna

* Tipo de dados da coluna
* Número de valores distintos
* Número de linhas com valores nulos
* Desvio mínimo, máximo, médio e padrão para valores de coluna

## <a name="summary"></a>Resumo

A criação de perfil de dados fornece estatísticas e informações sobre ativos de dados registrados para ajudá-lo a determinar a adequação dos dados para resolver problemas de negócios. Além de anotar e documentar fontes de dados, os perfis de dados podem dar aos usuários uma compreensão mais profunda dos seus dados.

## <a name="see-also"></a>Consultar Também

* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
