---
title: Como utilizar os dados de criação de perfis de origens de dados no catálogo de dados do Azure
description: Artigo que mostra como destaque como incluem perfis de dados de nível de tabela e coluna ao registar as origens de dados no catálogo de dados do Azure e como utilizar perfis de dados para compreender as origens de dados.
services: data-catalog
author: JasonWHowell
ms.author: jasonh
ms.assetid: 94a8274b-5c9c-4962-a4b1-2fed38a3d919
ms.service: data-catalog
ms.topic: conceptual
ms.date: 01/18/2018
ms.openlocfilehash: 64185a951b25b4e04ea5fc65aeede9b0e617d0c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001724"
---
# <a name="data-profile-data-sources"></a>Origens de dados de perfis de dados
## <a name="introduction"></a>Introdução
**Catálogo de dados do Microsoft Azure** é um serviço cloud totalmente gerido que funciona como um sistema de registo e sistema de deteção de origens de dados empresariais. Em outras palavras, **catálogo de dados do Azure** é tudo para ajudar as pessoas a detetar, compreender e utilizar origens de dados e ajuda as organizações a obter mais valor dos seus dados existentes. Quando uma origem de dados estiver registrada no **catálogo de dados do Azure**, seus metadados é copiado e indexados pelo serviço, mas a história não acaba nisso.

O **dados de criação de perfis** recurso do **catálogo de dados do Azure** examina os dados de origens de dados suportados no seu catálogo e recolhe estatísticas e informações sobre esses dados. É fácil de incluir um perfil de seus ativos de dados. Quando registra um recurso de dados, escolha **incluir o perfil de dados** na ferramenta de registo de origem de dados.

## <a name="what-is-data-profiling"></a>O que é a criação de perfis de dados
A criação de perfis de dados examina os dados na origem de dados a ser registado e recolhe estatísticas e informações sobre esses dados. Durante a deteção de origem de dados, essas estatísticas podem ajudar a determinar a adequação dos dados para resolver seu problema empresarial.

<!-- In [How to discover data sources](data-catalog-how-to-discover.md), you learn about **Azure Data Catalog's** extensive search capabilities including searching for data assets that have a profile. See [How to include a data profile when registering a data source](#howto). -->

As seguintes origens de dados suportam a criação de perfis de dados:

* Vistas e tabelas do SQL Server (incluindo a BD SQL do Azure e Azure SQL Data Warehouse)
* Oracle tabelas e vistas
* Teradata tabelas e vistas
* Tabelas do Hive

Incluindo perfis de dados quando registar recursos de dados ajuda os usuários a responder a perguntas sobre origens de dados, incluindo:

* Pode servir para resolver o meu problema de negócios?
* Os dados em conformidade com a particular por padrões ou padrões?
* Quais são as anomalias da origem de dados?
* Quais são os possíveis desafios de integrar esses dados em meu aplicativo?

> [!NOTE]
> Também pode adicionar documentação para um recurso para descrever como os dados podem ser integrados num aplicativo. Ver [como documentar origens de dados](data-catalog-how-to-documentation.md).
>
>

<a name="howto"/>

## <a name="how-to-include-a-data-profile-when-registering-a-data-source"></a>Como incluir um perfil de dados ao registar uma origem de dados
É fácil de incluir um perfil da sua origem de dados. Quando registar uma origem de dados, no **objetos a registar** painel da ferramenta de registo de origem de dados, escolha **perfil de dados incluem**.

![](media/data-catalog-data-profile/data-catalog-register-profile.png)

Para saber mais sobre como registar origens de dados, veja [como registar origens de dados](data-catalog-how-to-register.md) e [introdução ao catálogo de dados do Azure](data-catalog-get-started.md).

## <a name="filtering-on-data-assets-that-include-data-profiles"></a>Filtrar em recursos de dados que incluem perfis de dados
Para detetar recursos de dados que incluem um perfil de dados, pode incluir `has:tableDataProfiles` ou `has:columnsDataProfiles` como um dos seus termos de pesquisa.

> [!NOTE]
> Selecionando **incluir o perfil de dados** na origem de dados a ferramenta de registo inclui tabela e informações de perfil de nível de coluna. No entanto, a API do catálogo de dados permite que os recursos de dados a registar com apenas um conjunto de informações de perfil incluídas.
>
>

## <a name="viewing-data-profile-information"></a>Ver informações de perfil de dados
Depois de localizar uma origem de dados adequado com um perfil, pode ver os detalhes do perfil de dados. Para visualizar o perfil de dados, selecione um recurso de dados e escolha **perfil de dados** na janela do portal do catálogo de dados.

![](media/data-catalog-data-profile/data-catalog-view.png)

Um perfil de dados no **catálogo de dados do Azure** mostra a tabela e coluna perfil informações, incluindo:

### <a name="object-data-profile"></a>Perfil de dados de objeto
* Número de linhas
* Tamanho da tabela
* Quando o objeto última atualização

### <a name="column-data-profile"></a>Perfil de dados de coluna
* Tipo de dados de coluna
* Número de valores distintos
* Número de linhas com valores nulos
* Mínimo, máximo, média e desvio padrão para valores de coluna

## <a name="summary"></a>Resumo
Criação de perfis de dados fornecem estatísticas e informações sobre os recursos de dados registados para o ajudar a determinar a adequação dos dados para resolver problemas empresariais. Além de anotar e documentar origens de dados, perfis de dados podem dar aos utilizadores uma compreensão mais aprofundada dos seus dados.

## <a name="see-also"></a>Consultar Também
* [Como registar origens de dados](data-catalog-how-to-register.md)
* [Introdução ao Catálogo de Dados do Azure](data-catalog-get-started.md)
