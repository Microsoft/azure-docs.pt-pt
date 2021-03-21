---
title: Como documentar fontes de dados no Catálogo de Dados do Azure
description: Como-a-dia que realça como documentar os ativos de dados no Catálogo de Dados do Azure.
author: JasonWHowell
ms.author: jasonh
ms.service: data-catalog
ms.topic: how-to
ms.date: 08/01/2019
ms.openlocfilehash: 21168e403ffa8fb1472a5bfcac1fcd2079a52e2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104674925"
---
# <a name="how-to-document-data-sources-in-azure-data-catalog"></a>Como documentar fontes de dados no Catálogo de Dados do Azure

[!INCLUDE [Azure Purview redirect](../../includes/data-catalog-use-purview.md)]

## <a name="introduction"></a>Introdução
**O Microsoft Azure Data Catalog** é um serviço de cloud totalmente gerido que serve como um sistema de registo e sistema de descoberta para fontes de dados empresariais. Por outras palavras, **o Azure Data Catalog** tem tudo a ver com ajudar as pessoas a descobrir, *compreender* e usar fontes de dados, e ajudar as organizações a obter mais valor a partir dos seus dados existentes.

Quando uma fonte de dados é registada no **Azure Data Catalog,** os seus metadados são copiados e indexados pelo serviço, mas a história não termina aí. **O Azure Data Catalog** também permite que os utilizadores forneçam a sua documentação completa que possa descrever o uso e cenários comuns para a fonte de dados.

Em [Como anotar fontes de dados,](data-catalog-how-to-annotate.md)você aprende que os especialistas que conhecem a fonte de dados podem anotar com etiquetas e uma descrição. O portal **Azure Data Catalog** inclui um rico editor de texto para que os utilizadores possam documentar totalmente os ativos e contentores de dados. O editor inclui formatação de parágrafos, tais como títulos, formatação de texto, listas de balas, listas numeradas e tabelas.

Etiquetas e descrições são ótimas para anotações simples. No entanto, para ajudar os consumidores de dados a entender melhor a utilização de uma fonte de dados, e cenários de negócio para uma fonte de dados, um especialista pode fornecer documentação completa e detalhada. É fácil documentar uma fonte de dados. Selecione um ativo de dados ou recipiente e escolha **Documentação.**

![Separador de documentação em um Catálogo de Dados](media/data-catalog-documentation/data-catalog-documentation.png)

## <a name="documenting-data-assets"></a>Documentar ativos de dados
O benefício da documentação do Catálogo de **Dados Azure** permite-lhe utilizar o seu Catálogo de Dados como um repositório de conteúdos para criar uma narrativa completa dos seus ativos de dados. Pode explorar conteúdo detalhado que descreve recipientes e tabelas. Se já tiver conteúdo em outro repositório de conteúdo, como o SharePoint ou uma partilha de ficheiros, pode adicionar aos links de documentação do ativo para referência a este conteúdo existente. Esta funcionalidade torna os seus documentos existentes mais detetáveis.

> [!NOTE]
> A documentação não está incluída no índice de pesquisa.
>

![Separador de documentação e hiperligação para ligação web](media/data-catalog-documentation/data-catalog-documentation2.png)

O nível de documentação pode ir desde a descrição das características e do valor de um contentor de dados até uma descrição detalhada do esquema de tabela dentro de um contentor. O nível de documentação fornecido deve ser impulsionado pelas necessidades do seu negócio. Mas em geral, aqui estão alguns prós e contras de documentar os ativos de dados:

* Documentar apenas um recipiente: Todo o conteúdo está num só local, mas pode não ter detalhes necessários para que os utilizadores possam tomar uma decisão informada.
* Documente apenas as tabelas: O conteúdo é específico desse objeto, mas os seus utilizadores têm vários locais para documentos.
* Documentos e tabelas: Abordagem mais abrangente, mas pode introduzir mais manutenção dos documentos.

## <a name="summary"></a>Resumo
Documentar fontes de dados com **o Azure Data Catalog** pode criar uma narrativa sobre os seus ativos de dados com o maior detalhe que precisar.  Ao utilizar links, pode ligar-se aos conteúdos armazenados num repositório de conteúdo existente, que reúne os seus documentos e ativos de dados existentes. Assim que os seus utilizadores descobrirem os ativos de dados adequados, podem ter um conjunto completo de documentação.
