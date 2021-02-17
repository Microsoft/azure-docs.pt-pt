---
title: Adoção de práticas padrão da indústria
titleSuffix: Azure Digital Twins
description: Conheça as intírias existentes da indústria que podem ser adotadas para a Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 71795e9dc439d5f634fc4d777aa6b5cdd66e8f5c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561664"
---
# <a name="adopting-an-industry-ontology"></a>Adotar uma indústria de ontologia

Como pode ser mais fácil começar com uma ontologia DTDL de código aberto do que a partir de uma página em branco, a Microsoft está em parceria com especialistas em domínio para publicar ontologias, que representam convenções da indústria amplamente aceites e suportam uma variedade de casos de uso do cliente. 

O resultado é um conjunto de ontologias baseadas em DTDL de código aberto, que aprendem com, baseiam-se, aprendem ou utilizam diretamente os padrões da indústria. As torções destinam-se a satisfazer as necessidades dos desenvolvedores a jusante, com o potencial de serem amplamente adotados e/ou alargados pela indústria.

Neste momento, a Microsoft tem trabalhado com parceiros imobiliários para desenvolver uma ontologia para edifícios inteligentes, que fornece terreno comum para modelar edifícios inteligentes baseados nos padrões da indústria para evitar a reinvenção. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologia de edifício inteligente RealEstateCore

A Microsoft estabeleceu uma parceria com a [RealEstateCore](https://www.realestatecore.io/), um consórcio sueco de proprietários imobiliários, fornecedores de software e instituições de investigação, para fornecer uma ontologia DTDL de código aberto para a indústria imobiliária: a [**onologia RealEstateCore baseada em DTDL para edifícios inteligentes.**](https://github.com/Azure/opendigitaltwins-building)

Este edifício inteligente ontologia fornece terreno comum para modelar edifícios inteligentes, usando padrões da indústria (como [BRICK Schema](https://brickschema.org/ontology/) ou [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html)) para evitar a reinvenção. A ontologia também vem com as melhores práticas para como consumir e alargá-la corretamente. 

Para saber mais sobre a estrutura da ontologia e convenções de modelação, como usá-la, como alargá-la, e como contribuir, visite o [repositório](https://github.com/Azure/opendigitaltwins-building)da ontologia no GitHub. 

Pode ainda ler mais sobre a parceria com a RealEstateCore e os objetivos para esta iniciativa neste post de blog e vídeo que acompanha: [*o RealEstateCore, um edifício inteligente de ontologia para gémeos digitais, já está disponível.*](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o alargamento das práticas padrão da indústria para cumprir as suas especificações: [*Conceitos: Extensão das teologias da indústria*](concepts-ontologies-extend.md).

* Ou, continue no caminho para o desenvolvimento de modelos baseados em ontologias: [*Utilizando estratégias de ontologia num caminho de desenvolvimento de modelos.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)