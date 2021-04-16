---
title: Conversão de ontologies
titleSuffix: Azure Digital Twins
description: Compreender o processo de conversão de modelos padrão da indústria em DTDL para Azure Digital Twins
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 22b41fce59bf7dbe9db1186036c5ed44f07a4aad
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484489"
---
# <a name="convert-industry-standard-ontologies-to-dtdl-for-azure-digital-twins"></a>Converter as práticas padrão da indústria para DTDL para Azure Digital Twins

A maioria das [ontologias](concepts-ontologies.md) baseia-se em padrões semânticos web como [OWL,](https://www.w3.org/OWL/) [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS.](https://www.w3.org/2001/sw/wiki/RDFS) 

Para utilizar um modelo com Azure Digital Twins, deve estar em formato DTDL. Estes artigos descrevem a orientação geral do design sob a forma de um **padrão** de conversão para converter modelos baseados em RDF em DTDL para que possam ser usados com Azure Digital Twins. 

O artigo contém também o código de conversão de [**amostras**](#converter-samples) para conversores RDF e OWL, que pode ser estendido para outros esquemas na indústria da construção.

## <a name="conversion-pattern"></a>Padrão de conversão

Existem várias bibliotecas de terceiros que podem ser usadas na conversão de modelos baseados em RDF para DTDL. Algumas destas bibliotecas permitem-lhe colocar o seu ficheiro de modelo num gráfico. Você pode dar a volta através do gráfico à procura de construções específicas de RDFS e OWL, e convertê-las em DTDL.   

A tabela a seguir é um exemplo de como as construções de RDFS e OWL podem ser mapeadas para DTDL. 

| Conceito RDFS/OWL | Construção RDFS/OWL | Conceito DTDL | Construção DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Sufixo IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subclasses | `owl:Class`<br>Sufixo IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriedades do tipo de dados | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriedades da Interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriedades de objetos | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relação | `type:Relationship`<br>`name`<br>`target` (ou omitido se `rdfs:range` não)<br>`comment`<br>`displayName`<br>

O seguinte corte de código C# mostra como um ficheiro de modelo RDF é carregado num gráfico e convertido em DTDL, utilizando a biblioteca [**dotNetRDF.**](https://www.dotnetrdf.org/) 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/convertRDF.cs":::

## <a name="converter-samples"></a>Amostras de conversor

### <a name="rdf-converter-application"></a>Aplicação de conversor RDF 

Existe uma aplicação de amostra disponível que converte um ficheiro modelo baseado em RDF para [DTDL (versão 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para utilização pelo serviço Azure Digital Twins. Foi validado para o esquema [de tijolos,](https://brickschema.org/ontology/) e pode ser estendido para outros esquemas na indústria da construção (como [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Rede de Sensores Semânticos,](https://www.w3.org/TR/vocab-ssn/)ou [edifícioSSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

A amostra é uma aplicação de linha de comando .NET Core chamada **RdfToDtdlConverter**.

Pode obter a amostra aqui: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Para descarregar o código para a sua máquina, selecione o botão **de código Browse** por baixo do título na página da amostra, que o levará ao repo GitHub para a amostra. Selecione o botão **Código** e **Descarregue ZIP** para descarregar a amostra como um *. Ficheiro ZIP* chamado *RdfToDtdlConverter-main.zip*. Em seguida, pode desapertar o ficheiro e explorar o código.

:::image type="content" source="media/concepts-ontologies-convert/download-repo-zip.png" alt-text="Screenshot do rdfToDtdlConverter repo no GitHub. O botão Código é selecionado, produzindo uma pequena caixa de diálogo onde é realçado o botão Download ZIP." lightbox="media/concepts-ontologies-convert/download-repo-zip.png":::

Você pode usar esta amostra para ver os padrões de conversão em contexto, e ter como um bloco de construção para suas próprias aplicações executando conversões de modelos de acordo com suas necessidades específicas.

### <a name="owl2dtdl-converter"></a>Conversor OWL2DTDL 

O [**Conversor OWL2DTDL**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/OWL2DTDL) é uma amostra que traduz uma ontologia OWL num conjunto de declarações de interface DTDL, que podem ser usadas com o serviço Azure Digital Twins. Também trabalha para redes de ontologia, feitas de uma raiz ontologia reutilizando outras ontologias através `owl:imports` de declarações.

Este conversor foi usado para traduzir a [Ontologia do Núcleo Imobiliário](https://doc.realestatecore.io/3.1/full.html) para dTDL e pode ser usado para qualquer ontologia baseada em CORUJA.

## <a name="next-steps"></a>Passos seguintes 

* Saiba mais sobre o alargamento das práticas padrão da indústria para cumprir as suas especificações: [*Conceitos: Extensão das teologias da indústria*](concepts-ontologies-extend.md).

* Ou, continue no caminho para o desenvolvimento de modelos baseados em ontologias: [*Utilizando estratégias de ontologia num caminho de desenvolvimento de modelos.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)