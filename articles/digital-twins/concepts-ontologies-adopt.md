---
title: Adoção de práticas padrão da indústria
titleSuffix: Azure Digital Twins
description: Conheça as intírias existentes da indústria que podem ser adotadas para a Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: c4f003fc9e418501af76281c10277fce3606e24c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102124232"
---
# <a name="adopting-an-industry-ontology"></a>Adotar uma indústria de ontologia

Como pode ser mais fácil começar com uma ontologia DTDL de código aberto do que a partir de uma página em branco, a Microsoft está em parceria com especialistas em domínio para publicar ontologias, que representam convenções da indústria amplamente aceites e suportam uma variedade de casos de uso do cliente. 

O resultado é um conjunto de ontologias baseadas em DTDL de código aberto, que aprendem com, baseiam-se, aprendem ou utilizam diretamente os padrões da indústria. As torções destinam-se a satisfazer as necessidades dos desenvolvedores a jusante, com o potencial de serem amplamente adotados e/ou alargados pela indústria.

Neste momento, a Microsoft tem trabalhado com parceiros para desenvolver uma ontologia para [edifícios inteligentes](#realestatecore-smart-building-ontology) e uma ontologia para [cidades inteligentes](#smart-cities-ontology), que fornecem terreno comum para modelação baseada em padrões nestas indústrias para evitar a reinvenção. 

## <a name="realestatecore-smart-building-ontology"></a>Ontologia de edifício inteligente RealEstateCore

*Encontre a ontologia aqui: [**Digital Twins Definition Language-based RealEstateCore onlogy for smart buildings**](https://github.com/Azure/opendigitaltwins-building)*.

A Microsoft estabeleceu uma parceria com [a RealEstateCore](https://www.realestatecore.io/), um consórcio sueco de proprietários imobiliários, fornecedores de software e instituições de investigação, para entregar esta ontologia DTDL de código aberto para a indústria imobiliária.

Este edifício inteligente ontologia fornece terreno comum para modelar edifícios inteligentes, usando padrões da indústria (como [BRICK Schema](https://brickschema.org/ontology/) ou [W3C Building Topology Ontology](https://w3c-lbd-cg.github.io/bot/index.html)) para evitar a reinvenção. A ontologia também vem com as melhores práticas para como consumir e alargá-la corretamente. 

Para saber mais sobre a estrutura da ontologia e convenções de modelação, como usá-la, como alargá-la, e como contribuir, visite o repositório da ontologia no GitHub: [Azure/opendigitaltwins-building.](https://github.com/Azure/opendigitaltwins-building) 

Pode ainda ler mais sobre a parceria com a RealEstateCore e os objetivos para esta iniciativa neste post de blog e vídeo que acompanha: [o RealEstateCore, um edifício inteligente de ontologia para gémeos digitais, já está disponível.](https://techcommunity.microsoft.com/t5/internet-of-things/realestatecore-a-smart-building-ontology-for-digital-twins-is/ba-p/1914794)

## <a name="smart-cities-ontology"></a>Ontologia de cidades inteligentes

*Encontre a ontologia aqui: [**Linguagem de Definição de Gémeos Digitais (DTDL) para Smart Cities**](https://github.com/Azure/opendigitaltwins-smartcities)*.

A Microsoft colaborou com [a Open Agile Smart Cities (OASC)](https://oascities.org/) e a [Sirus](https://sirus.be/) para fornecer uma ontologia baseada em DTDL para cidades inteligentes, começando com [a ETSI CIM NGSI-LD.](https://www.etsi.org/committee/cim) Além do ETSI NGSI-LD, também avaliámos Saref4City, CityGML, ISO e outros.

A versão atual da ontologia está focada num conjunto inicial de modelos. Os autores da ontologia saúdam-no para contribuir para alargar o conjunto inicial de casos de uso, bem como melhorar os modelos existentes. 

Para saber mais sobre a ontologia, como usá-la, e como contribuir, visite o repositório da ontologia no GitHub: [Azure/opendigitaltwins-smartcities](https://github.com/Azure/opendigitaltwins-smartcities). 

Você também pode ler mais sobre as parcerias e abordagem para cidades inteligentes neste post de blog e vídeo de acompanhamento: [Smart Cities Ontology for Digital Twins](https://techcommunity.microsoft.com/t5/internet-of-things/smart-cities-ontology-for-digital-twins/ba-p/2166585).

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre o alargamento das práticas padrão da indústria para cumprir as suas especificações: [*Conceitos: Extensão das teologias da indústria*](concepts-ontologies-extend.md).

* Ou, continue no caminho para o desenvolvimento de modelos baseados em ontologias: [*Utilizando estratégias de ontologia num caminho de desenvolvimento de modelos.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)