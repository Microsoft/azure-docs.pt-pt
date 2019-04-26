---
title: Entidades de dados | Documentos da Microsoft
description: Uma visão geral de entidades de dados.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 579856ab87aaf8d051f2e3c161bb2d0e2f693ed5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60397299"
---
# <a name="data-entities"></a>Entidades de dados

Este artigo define e fornece uma descrição geral de entidades de dados. Ele inclui informações sobre as capacidades de entidades de dados, os cenários que eles oferecem suporte, as categorias que são utilizadas para e como os métodos para criá-las.

## <a name="overview"></a>Descrição geral

Uma entidade de dados é uma abstração da implementação física das tabelas de base de dados. Por exemplo, em tabelas normalizadas, muitos dos dados para cada cliente podem ser armazenado numa tabela de cliente e, em seguida, o resto pode estar distribuído por um pequeno conjunto de tabelas relacionadas. Neste caso, a entidade de dados para o conceito de cliente é apresentado como uma exibição desnormalizada, em que cada linha contém todos os dados da tabela customer e suas tabelas relacionadas. Uma entidade de dados encapsula um conceito de negócios num formato que facilita o desenvolvimento e integração. A natureza abstrata de uma entidade de dados pode simplificar o desenvolvimento de aplicativos e personalização. Mais tarde, a abstração também separa o código da aplicação de volume de alterações inevitável as tabelas físicas entre versões.

Para resumir: Entidade de dados fornece a abstração conceitual e encapsulamento (exibição desnormalizada) de esquemas de tabela subjacente para representar os conceitos de dados de chave e funcionalidades.

## <a name="capabilities"></a>Capacidades

Uma entidade de dados tem as seguintes capacidades:

- Ele substitui divergentes e fragmentados conceitos de AXD, entidades de estrutura de importação/exportação de dados (DIXF), e consulta de agregação com o conceito único.
- Ele fornece uma pilha única para capturar a lógica de negócio e para permitir cenários como a importação/exportação, integração e programação.
- Torna-se o principal mecanismo para exportar e importar pacotes de dados para cenários de dados de gerenciamento de ciclo de vida de aplicativos (ALM) e demonstração.
- Ele pode ser exposto como serviços OData e, em seguida, utilizado em cenários de integração de síncrona de estilo de tabela e integrações do Microsoft Office.

Ver [entidades de dados](https://docs.microsoft.com/dynamics365/operations/dev-itpro/data-entities/data-entities) para obter mais informações.
