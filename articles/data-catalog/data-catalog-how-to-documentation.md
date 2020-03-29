---
title: Como documentar fontes de dados no Catálogo de Dados do Azure
description: Como fazer o artigo destacando como documentar os ativos de dados no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: e9e9013d354585d04f205feb93a84d94c0f05905
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68950194"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Como documentar fontes de dados no Catálogo de Dados do Azure

## <a name="introduction"></a>Introdução
O **Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados da empresa. Por outras palavras, o **Azure Data Catalog** tem tudo a ver com ajudar as pessoas a descobrir, *compreender*e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes.

Quando uma fonte de dados é registada no **Azure Data Catalog,** os seus metadados são copiados e indexados pelo serviço, mas a história não termina aí. **O Azure Data Catalog** também permite que os utilizadores forneçam a sua própria documentação completa que possa descrever o uso e cenários comuns para a fonte de dados.

Em [Como anotar fontes](data-catalog-how-to-annotate.md)de dados, aprende-se que os especialistas que conhecem a fonte de dados podem anotá-lo com etiquetas e uma descrição. O portal **Azure Data Catalog** inclui um rico editor de texto para que os utilizadores possam documentar totalmente os ativos e contentores de dados. O editor inclui formatação de parágrafo, tais como títulos, formatação de texto, listas com balas, listas numeradas e tabelas.

Etiquetas e descrições são ótimas para anotações simples. No entanto, para ajudar os consumidores de dados a compreender melhor a utilização de uma fonte de dados, e cenários de negócio para uma fonte de dados, um especialista pode fornecer documentação completa e detalhada. É fácil documentar uma fonte de dados. Selecione um ativo ou recipiente de dados e escolha **documentação.**

![Separador de documentação num Catálogo de Dados](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentar ativos de dados
O benefício da documentação do Catálogo de **Dados do Azure** permite-lhe utilizar o seu Catálogo de Dados como repositório de conteúdos para criar uma narrativa completa dos seus ativos de dados. Pode explorar conteúdos detalhados que descrevem contentores e mesas. Se já tiver conteúdo noutro repositório de conteúdos, como o SharePoint ou uma partilha de ficheiros, pode adicionar aos links de documentação do ativo para fazer referência a este conteúdo existente. Esta funcionalidade torna os documentos existentes mais descobriveis.

> [!NOTE]
> A documentação não está incluída no índice de pesquisa.
>

![Separador de documentação e hiperligação para ligação web](media/data-catalog-documentation/data-catalog-documentation2.png)

O nível de documentação pode ir desde a descrição das características e valor de um contentor de ativos de dados até uma descrição detalhada do esquema de mesa dentro de um recipiente. O nível de documentação fornecido deve ser impulsionado pelas necessidades do seu negócio. Mas, em geral, aqui estão alguns prós e contras de documentar os ativos dos dados:

* Documente apenas um recipiente: Todo o conteúdo está num só local, mas pode não ter detalhes necessários para que os utilizadores tomem uma decisão informada.
* Documente apenas as tabelas: O conteúdo é específico desse objeto, mas os seus utilizadores têm vários locais para documentos.
* Documentos e tabelas: Abordagem mais abrangente, mas pode introduzir uma maior manutenção dos documentos.

## <a name="summary"></a>Resumo
Documentar fontes de dados com **o Azure Data Catalog** pode criar uma narrativa sobre os seus ativos de dados com o máximo de detalhes que precisar.  Ao utilizar links, pode ligar-se aos conteúdos armazenados num repositório de conteúdos existente, que reúne os seus docs e os seus ativos de dados existentes. Assim que os seus utilizadores descobrirem os ativos de dados apropriados, podem ter um conjunto completo de documentação.
