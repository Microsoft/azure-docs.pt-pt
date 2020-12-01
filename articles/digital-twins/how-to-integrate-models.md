---
title: Integrar modelos padrão da indústria
titleSuffix: Azure Digital Twins
description: Compreender como integrar modelos padrão da indústria em DTDL para Azure Digital Twins, quer utilizando ontologias DTDL especiais quer convertendo ontotologias existentes
author: baanders
ms.author: baanders
ms.date: 11/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: f5bfe128ddc04e8048bb89a8e39035434dfd2b92
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96352886"
---
# <a name="integrate-industry-standard-models-with-dtdl-for-azure-digital-twins"></a>Integrar modelos padrão da indústria com DTDL para Azure Digital Twins

A utilização de modelos baseados nos padrões da indústria ou na utilização de uma representação ontrologia padrão, como RDF ou OWL, fornece um ponto de partida rico ao desenhar os seus modelos Azure Digital Twins. A utilização de modelos industriais também ajuda na normalização e partilha de informação.

Para ser utilizado com a Azure Digital Twins, um modelo deve ser representado na Linguagem de Definição de [**Gémeos Digitais (DTDL)**](concepts-models.md)baseada em JSON-LD . Por isso, este artigo descreve como representar os seus modelos padrão da indústria em DTDL, integrando os conceitos existentes da indústria com semântica DTDL para que a Azure Digital Twins possa usá-los. O modelo DTDL serve então como fonte de verdade para o modelo dentro da Azure Digital Twins.

Existem três caminhos possíveis para integrar modelos padrão da indústria com DTDL:
* **Adote**: Pode iniciar a sua solução com uma ontologia DTDL de código aberto que foi construída com base em padrões da indústria amplamente adotados. 
* **Converter**: Se já tiver modelos existentes, terá de os converter em DTDL.
* **Autor**: Pode sempre desenvolver os seus próprios modelos DTDL personalizados de raiz, conforme descrito em [*Como-a-: Gerir modelos personalizados.*](how-to-manage-model.md)

## <a name="adopt-an-open-source-dtdl-ontology"></a>Adotar uma ontologia DTDL de código aberto

É frequentemente mais fácil começar com uma ontologia DTDL de código aberto do que começar a partir de uma página em branco. 

Por exemplo, as soluções Smart Buildings podem alavancar a [**ontologia RealEstateCore baseada em DTDL, que**](https://github.com/Azure/opendigitaltwins-building)fornece terreno comum para modelar edifícios inteligentes, aproveitando os padrões da indústria para evitar a reinvenção. 

Estas ontotologias DTDL de código aberto também fornecem as melhores práticas para como consumir e alargar adequadamente os modelos. 

## <a name="convert-existing-models-to-dtdl"></a>Converter os modelos existentes em DTDL

A maioria dos modelos da indústria (também referidos como **ontologias)** baseiam-se em padrões web semânticos como [OWL,](https://www.w3.org/OWL/) [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Para utilizar um modelo com Azure Digital Twins, deve estar em formato DTDL. Esta secção descreve a orientação geral do design sob a forma de um **padrão** de conversão para converter modelos baseados em RDF em DTDL para que possam ser usados com Azure Digital Twins. 

Contém também [ **um código de conversão** de amostra para um conversor RDF,](#sample-converter-application)que foi validado para o esquema de [tijolos](https://brickschema.org/ontology/) e pode ser estendido para outros esquemas na indústria da construção.

### <a name="conversion-pattern"></a>Padrão de conversão

Existem várias bibliotecas de terceiros que podem ser usadas na conversão de modelos baseados em RDF para DTDL. Algumas destas bibliotecas permitem-lhe colocar o seu ficheiro de modelo num gráfico. Você pode dar a volta através do gráfico à procura de construções específicas de RDFS e OWL, e convertê-las em DTDL.   

A tabela a seguir é um exemplo de como as construções de RDFS e OWL podem ser mapeadas para DTDL. 

| Conceito RDFS/OWL | Construção RDFS/OWL | Conceito DTDL | Construção DTDL |
| --- | --- | --- | --- |
| Classes | `owl:Class`<br>Sufixo IRI<br>``rdfs:label``<br>``rdfs:comment`` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment` 
| Subclasses | `owl:Class`<br>Sufixo IRI<br>`rdfs:label`<br>`rdfs:comment`<br>`rdfs:subClassOf` | Interface | `@type:Interface`<br>`@id`<br>`displayName`<br>`comment`<br>`extends` 
| Propriedades do tipo de dados | `owl:DatatypeProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:label`<br>`rdfs:range` | Propriedades da Interface | `@type:Property`<br>`name`<br>`displayName`<br>`schema` 
| Propriedades de objetos | `owl:ObjectProperty`<br>`rdfs:label` ou `INode`<br>`rdfs:range`<br>`rdfs:comment`<br>`rdfs:label` | Relação | `type:Relationship`<br>`name`<br>`target` (ou omitido se `rdfs:range` não)<br>`comment`<br>`displayName`<br>

O seguinte corte de código C# mostra como um ficheiro de modelo RDF é carregado num gráfico e convertido em DTDL, utilizando a biblioteca [**dotNetRDF.**](https://www.dotnetrdf.org/) 

```csharp
using VDS.RDF.Ontology; 
using VDS.RDF.Parsing; 
using Microsoft.Azure.DigitalTwins.Parser; 

//...

Console.WriteLine("Reading file..."); 

FileLoader.Load(_ontologyGraph, rdfFile.FullName); 

// Start looping through for each owl:Class 
foreach (OntologyClass owlClass in _ontologyGraph.OwlClasses) 
{ 

    // Generate a DTMI for the owl:Class 
    string Id = GenerateDTMI(owlClass); 

    if (!String.IsNullOrEmpty(Id)) 
    { 

        Console.WriteLine($"{owlClass.ToString()} -> {Id}"); 

        // Create Interface
        DtdlInterface dtdlInterface = new DtdlInterface 
        { 
            Id = Id, 
            Type = "Interface", 
            DisplayName = GetInterfaceDisplayName(owlClass), 
            Comment = GetInterfaceComment(owlClass), 
            Contents = new List<DtdlContents>() 
        }; 

        // Use DTDL 'extends' for super classes 
        IEnumerable<OntologyClass> foundSuperClasses = owlClass.DirectSuperClasses; 

        //... 
     }

     // Add interface to the list of interfaces 
     _interfaceList.Add(dtdlInterface); 
} 

// Serialize to JSON 
var json = JsonConvert.SerializeObject(_interfaceList); 

//...
``` 

### <a name="sample-converter-application"></a>Aplicação do conversor de amostras 

Existe uma aplicação de amostra disponível que converte um ficheiro modelo baseado em RDF para [DTDL (versão 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para utilização pelo serviço Azure Digital Twins. Foi validado para o esquema [de tijolos,](https://brickschema.org/ontology/) e pode ser estendido para outros esquemas na indústria da construção (como [Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/), [Rede de Sensores Semânticos,](https://www.w3.org/TR/vocab-ssn/)ou [edifícioSSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/)).

A amostra é uma aplicação de linha de comando .NET Core chamada **RdfToDtdlConverter**.

Pode obter a amostra aqui: [**RdfToDtdlConverter**](/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Para descarregar o código para a sua máquina, é *premida* o botão Download ZIP por baixo do título na página de aterragem da amostra. Isto irá descarregar um ficheiro *ZIP* com o nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, que depois pode desapertar e explorar.

Você pode usar esta amostra para ver os padrões de conversão em contexto, e ter como um bloco de construção para suas próprias aplicações executando conversões de modelos de acordo com suas necessidades específicas.

## <a name="validate-and-upload-dtdl-models"></a>Validar e carregar modelos DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Uma vez que um modelo é convertido e validado, você pode **carregá-lo para o seu exemplo Azure Digital Twins**. Para obter mais informações sobre este processo, consulte a secção de [*modelos upload*](how-to-manage-model.md#upload-models) de *Como-a-: Gerir modelos personalizados.*

## <a name="next-steps"></a>Passos seguintes 

Leia mais sobre a conceção e gestão de modelos digitais twin:
 
* [*Conceitos: Modelos personalizados*](concepts-models.md)
* [*Como fazer: Gerir modelos personalizados*](how-to-manage-model.md)
* [*Como fazer: Parse e validar modelos*](how-to-parse-models.md)