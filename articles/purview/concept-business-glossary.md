---
title: Compreender as funcionalidades glossários de negócios em Azure Purview (pré-visualização)
description: Este artigo explica o que é o glossário de negócios em Azure Purview.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 8b391438d8d6605e7ef493a6552af634db840ad5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555158"
---
# <a name="understand-business-glossary-features-in-azure-purview"></a>Compreenda as características glossários de negócios em Azure Purview

Este artigo fornece uma visão geral da característica glossária de negócios em Azure Purview. 

## <a name="business-glossary"></a>Glossário comercial

Um glossário fornece vocabulário para utilizadores de negócios.  Consiste em termos de negócio que podem estar relacionados uns com os outros e permite que sejam categorizados para que possam ser entendidos em diferentes contextos. Estes termos podem então ser mapeados para ativos como uma base de dados, tabelas, colunas, etc. Isto ajuda na abstração do jargão técnico associado aos repositórios de dados e permite que o utilizador do negócio descubra e trabalhe com dados no vocabulário que lhes é mais familiar.


Um glossário de negócios é uma coleção de termos. Cada termo representa um objeto numa organização e é altamente provável que existam múltiplos termos que representam o mesmo objeto. Um cliente também pode ser referido como cliente, comprador ou comprador. Estes múltiplos termos têm uma relação entre si. A relação entre estes termos pode ser uma das seguintes:

- sinónimos - termos diferentes com a mesma definição
- relacionado - nome diferente com definição semelhante

O mesmo termo também pode implicar múltiplos objetos de negócio. É importante que cada termo seja bem definido e claramente compreendido dentro da organização.

## <a name="custom-attributes"></a>Atributos personalizados

A Azure Purview suporta oito atributos fora da caixa para qualquer termo glossário de negócios:
- Name
- Definição
- Administradores de dados
- Especialistas em dados
- Acrónimo
- Sinónimos
- Termos relacionados
- Recursos

Estes atributos não podem ser editados ou eliminados. No entanto, estes atributos não são suficientes para definir completamente um termo numa organização. Para resolver este problema, o Purview fornece uma funcionalidade onde pode definir atributos personalizados para o seu glossário.

## <a name="term-templates"></a>Modelos de prazo

Os Modelos de Prazo fornecem atributos habituais glossários para serem agrupados logicamente no catálogo. A funcionalidade permite-lhe agrupar todos os atributos personalizados relevantes num modelo e, em seguida, aplicar o modelo enquanto cria o termo glossário. Por exemplo, todos os atributos personalizados relacionados com finanças como centro de custos, centro de lucros, código contabilístico podem ser agrupados num modelo de modelo de prazo Modelo de Financiamento e o modelo de Finanças pode ser usado para criar termos glossários financeiros.

Todos os atributos padrão estão agrupados num modelo padrão do sistema. Qualquer modelo de termo que criar conterá estes atributos juntamente com quaisquer atributos personalizados adicionais criados como parte do processo de criação do modelo.

## <a name="glossary-vs-classification-vs-sensitivity-labels"></a>Etiquetas de classificação glossária vs

Embora termos glossários, classificações e etiquetas sejam anotações para um ativo de dados, cada um deles tem um significado diferente no contexto do catálogo. 

### <a name="glossary"></a>Glossário

Como acima referido, o termo glossário de negócios define o vocabulário de negócio para uma organização e ajuda a colmatar a lacuna entre vários departamentos da sua empresa.

### <a name="classifications"></a>Classificações

As classificações são anotações que podem ser atribuídas a entidades. A flexibilidade das classificações permite-lhe usá-las para vários cenários, tais como:

- compreender a natureza dos dados armazenados nos ativos de dados
- definindo políticas de controlo de acesso

A Purview tem mais de 100 classificadores de sistema hoje em dia e pode definir os seus próprios classificadores no catálogo. Como parte do processo de digitalização, detetamos automaticamente estas classificações e aplicamo-las a ativos e esquemas de dados. No entanto, pode sobrepô-los a qualquer momento. As sobreposições humanas nunca são substituídas por exames automatizados.

### <a name="sensitivity-labels"></a>Etiquetas de confidencialidade

Os rótulos de sensibilidade são um tipo de anotação que lhe permite classificar e proteger os dados da sua organização, sem impedir a produtividade e a colaboração. Os rótulos de sensibilidade são usados para identificar as categorias de tipos de classificação dentro dos seus dados organizacionais, e agrupar as políticas que deseja aplicar a cada categoria. A Purview faz uso dos mesmos tipos de informação sensíveis que o Microsoft 365, que lhe permite esticar as suas políticas de segurança existentes e proteger todo o seu conteúdo e propriedade de dados. As mesmas etiquetas podem ser partilhadas em todos os produtos do Microsoft Office e ativos de dados em Purview.

## <a name="next-steps"></a>Passos seguintes

- [Gerir modelos de prazo](how-to-manage-term-templates.md)
- [Navegue no catálogo de dados em Azure Purview](how-to-browse-catalog.md)
