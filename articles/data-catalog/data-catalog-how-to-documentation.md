---
title: Como documentar fontes de dados no catálogo de dados do Azure
description: Artigo de instruções destacando como documentar ativos de dados no catálogo de dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 5c0bc97e5a5b362ea78f82ea99bb967a9607093a
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68882494"
---
# <a name="document-data-sources"></a>Documentar origens de dados
## <a name="introduction"></a>Introdução
**Microsoft Azure catálogo de dados** é um serviço de nuvem totalmente gerenciado que serve como um sistema de registro e sistema de descoberta para fontes de dados empresariais. Em outras palavras, o **Catálogo de dados do Azure** está prestes a ajudar as pessoas a descobrir, *compreender*e usar fontes de dados e ajudar as organizações a obter mais valor de seus dados existentes.

Quando uma fonte de dados é registrada no **Catálogo de dados do Azure**, seus metadados são copiados e indexados pelo serviço, mas a história não termina. O **Catálogo de dados do Azure** também permite que os usuários forneçam sua própria documentação completa que pode descrever o uso e cenários comuns para a fonte de dados.

Em [como anotar fontes de dados](data-catalog-how-to-annotate.md), você aprende que os especialistas que conhecem a fonte de dados podem anotá-los com marcas e uma descrição. O portal do **Catálogo de dados do Azure** inclui um editor de Rich Text para que os usuários possam documentar totalmente os ativos e contêineres de dados. O editor inclui formatação de parágrafo, como títulos, formatação de texto, listas com marcadores, listas numeradas e tabelas.

Marcas e descrições são ótimas para anotações simples. No entanto, para ajudar os consumidores de dados a entender melhor o uso de uma fonte de dados e cenários de negócios para uma fonte de dados, um especialista pode fornecer documentação completa e detalhada. É fácil documentar uma fonte de dados. Selecione um ativo de dados ou contêiner e escolha **documentação**.

![Guia de documentação em um catálogo de dados](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentando ativos de dados
O benefício da documentação do **Catálogo de dados do Azure** permite que você use seu catálogo de dados como um repositório de conteúdo para criar uma narração completa de seus ativos de dados. Você pode explorar o conteúdo detalhado que descreve contêineres e tabelas. Se você já tiver conteúdo em outro repositório de conteúdo, como o SharePoint ou um compartilhamento de arquivos, poderá adicionar aos links de documentação do ativo para fazer referência a esse conteúdo existente. Esse recurso torna seus documentos existentes mais detectáveis.

> [!NOTE]
> A documentação não está incluída no índice de pesquisa.
>

![Guia de documentação e hiperlink para link da Web](media/data-catalog-documentation/data-catalog-documentation2.png)

O nível de documentação pode variar de descrever as características e o valor de um contêiner de ativos de dados para uma descrição detalhada do esquema de tabela dentro de um contêiner. O nível de documentação fornecido deve ser orientado pelas suas necessidades de negócios. Mas, em geral, aqui estão alguns prós e contras de documentação de ativos de dados:

* Documente apenas um contêiner: Todo o conteúdo está em um único lugar, mas talvez não precise de detalhes necessários para que os usuários tomem uma decisão informada.
* Documente apenas as tabelas: O conteúdo é específico desse objeto, mas os usuários têm vários lugares para documentos.
* Tabelas e contêineres de documento: A abordagem mais abrangente, mas pode introduzir mais manutenção dos documentos.

## <a name="summary"></a>Resumo
A documentação de fontes de dados com o **Catálogo de dados do Azure** pode criar uma narração sobre seus ativos de dados com o máximo de detalhes necessário.  Usando links, você pode vincular ao conteúdo armazenado em um repositório de conteúdo existente, que reúne seus recursos e seus ativos de dados existentes juntos. Depois que os usuários descobrirem ativos de dados apropriados, eles poderão ter um conjunto completo de documentação.
