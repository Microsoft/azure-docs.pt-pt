---
title: Conversão de modelos padrão da indústria
titleSuffix: Azure Digital Twins
description: Compreender o padrão de conversão dos modelos padrão da indústria (RDF/OWL) para DTDL
author: baanders
ms.author: baanders
ms.date: 9/28/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 76d1fd91053216103ef6ace0e56979c57eca569f
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2020
ms.locfileid: "92002651"
---
# <a name="convert-industry-standard-models-to-dtdl-for-azure-digital-twins"></a>Converter modelos padrão da indústria para DTDL para Azure Digital Twins

Os modelos em Azure Digital Twins estão representados na Linguagem de Definição de [**Gémeos Digitais (DTDL)**](concepts-models.md)baseada em JSON-LD. Se tiver modelos existentes fora da Azure Digital Twins que se baseiem num padrão da indústria, como RDF ou OWL, terá de **os converter em DTDL** para os utilizar com a Azure Digital Twins. A versão DTDL tornar-se-á então a fonte de verdade para o modelo dentro da Azure Digital Twins.

Este artigo descreve um padrão para converter a indústria baseada em RDF ou modelos personalizados para DTDL. Inclui:
* **Orientação a nível estratégico** que pode ser aplicada a uma variedade de padrões e tipos de modelos
* [**Código de amostra** para um conversor específico de RDF](#sample-converter-application)

## <a name="industry-models"></a>Modelos da indústria  

A utilização de modelos industriais proporciona um ponto de partida rico ao desenhar o seu modelo Azure Digital Twins. A utilização de modelos industriais também ajuda na normalização e partilha de informação. 

Alguns modelos comuns da indústria incluem:  

| Indústria vertical | Modelo |
| --- | --- | 
| Gestão de edifícios /instalações | [RealEstateCore](https://www.realestatecore.io/)<br>[BRICK Schema](https://brickschema.org/ontology/1.1/)<br>[Building Topology Ontology (BOT)](https://w3c-lbd-cg.github.io/bot/)<br>[Rede de Sensores Semânticos](https://www.w3.org/TR/vocab-ssn/)<br>[BuildingSmart Industry Foundation Classes (IFC)](https://technical.buildingsmart.org/standards/ifc/ifc-schema-specifications/) |
| Cidades Inteligentes | [ETSI NGSI-LD](https://www.etsi.org/deliver/etsi_gr/CIM/001_099/008/01.01.01_60/gr_CIM008v010101p.pdf)<br>[Referência de Aplicações Inteligentes (SAREF)](https://saref.etsi.org/) |
| Rede energética | [CIM](https://cimug.ucaiug.org/) / [IEC 61968](https://en.wikipedia.org/wiki/IEC_61968) | 

Dependendo das suas necessidades, também pode usar o DTDL para personalizar ou ampliar modelos da indústria, ou desenvolver o seu próprio modelo personalizado de raiz. 

## <a name="create-and-edit-models"></a>Criar e editar modelos

O primeiro passo na modelação é criar os seus modelos. Pode criar e completar a edição dos seus modelos padrão da indústria antes de os converter em DTDL, ou pode convertê-los em DTDL mais cedo e continuar a editá-los como documentos DTDL.

### <a name="with-industry-standards"></a>Com os padrões da indústria

A maioria dos modelos da indústria (também referidos como **ontologias)** baseiam-se em padrões web semânticos como [OWL,](https://www.w3.org/OWL/[) [RDF](https://www.w3.org/2001/sw/wiki/RDF)e [RDFS](https://www.w3.org/2001/sw/wiki/RDFS). 

Em alguns casos, pode querer criar ou editar um modelo utilizando ferramentas de modelo baseadas em OWL. Pode utilizar qualquer número de ferramentas de autoria de modelos para desenhar um modelo baseado em OWL, incluindo o seguinte.
* [WebProtégé](https://protege.stanford.edu/products.php#web-protege) e [Protégé Desktop](https://protege.stanford.edu/products.php#desktop-protege) são exemplos populares. Pode importar modelos da indústria em vários formatos, editar ou estender um modelo e exportar o modelo. 
* [WebVOWL](http://www.visualdataweb.de/webvowl/) é outra ferramenta popular para visualizar modelos. 

Se criar um modelo usando algum padrão da indústria que não seja DTDL, terá de o converter em DTDL e enviá-lo para a Azure Digital Twins. 

#### <a name="common-model-file-formats"></a>Formatos de ficheiros de modelos comuns 

RDF, OWL e RDFS são os blocos básicos de construção da teia semântica. 

**A RDF** fornece uma estrutura conceptual para descrever as coisas, sob a forma de **triplos.** Os triplos consistem em três partes: **sujeito,** **predicado,** e **objeto.** Os objetos podem ser feitos de URIs. 

Aqui estão alguns exemplos de triplos RDF:

```
<LogicalDevice> <hasCapabiity> <Temperature>
<Chiller> <locatedIn> <Level1>
<Asset> <isPartOf> <Asset>  
```

Estes exemplos são todos RDF válidos, mas a última declaração é semanticamente inválida. Esta situação é onde a especificação OWL entra na imagem. **A OWL** define o que pode escrever com RDF para ter ontologia válida.

Aqui está um exemplo fazendo uso de OWL: 

```
<Asset> <isPartOf> <Building>
<TemperatureSensor> <isType> <Sensor>
```

**O RDFS** fornece semântica vocabulário adicional que o ajuda a definir e descrever as aulas. Por exemplo, uma dessas classes `rdfs:subClassOf` é:

```
<Equipment> <subClassOf> <Asset>
```

Os modelos podem ser guardados, importados e exportados em muitos formatos de ficheiros, incluindo:  
* RDF/XML 
* Tartaruga (TTL) 
* CORUJA/XML 

### <a name="with-dtdl"></a>Com DTDL

A Azure Digital Twins utiliza a **Linguagem de Definição Digital DeFins (DTDL)** baseada em JSON-LD para modelação. Qualquer modelo que seja utilizado com a Azure Digital Twins terá de ser originalmente escrito ou convertido em DTDL.

Ao trabalhar com modelos em DTDL, pode utilizar a [**extensão DTDL**](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)   disponível para [o Código do Estúdio Visual,](https://code.visualstudio.com/)que fornece validação de sintaxe e outras funcionalidades para facilitar a escrita de modelos DTDL.

Pode ler mais sobre os modelos Azure Digital Twins e os seus componentes em [*Conceitos: Modelos personalizados*](concepts-models.md) e [*Como fazer: Gerir modelos personalizados.*](how-to-manage-model.md)

## <a name="convert-models-to-dtdl"></a>Converter modelos em DTDL

Para utilizar um modelo com Azure Digital Twins, deve estar em formato DTDL. Esta secção abrange como converter modelos baseados em RDF em DTDL para que possam ser usados com Azure Digital Twins.

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

## <a name="validate-and-upload-dtdl-models"></a>Validar e carregar modelos DTDL

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

Uma vez que um modelo é convertido e validado, você pode **carregá-lo para o seu exemplo Azure Digital Twins**. Para obter mais informações sobre este processo, consulte a secção de [*modelos upload*](how-to-manage-model.md#upload-models) de *Como-a-: Gerir modelos personalizados.*

## <a name="sample-converter-application"></a>Aplicação do conversor de amostras 

Existe uma aplicação de amostra disponível que converte um ficheiro modelo baseado em RDF para [DTDL (versão 2)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) para utilização pelo serviço Azure Digital Twins. A amostra é uma aplicação de linha de comando .NET Core chamada **RdfToDtdlConverter**.

Pode obter a amostra aqui: [**RdfToDtdlConverter**](https://docs.microsoft.com/samples/azure-samples/rdftodtdlconverter/digital-twins-model-conversion-samples/). 

Para descarregar o código para a sua máquina, é *premida* o botão Download ZIP por baixo do título na página de aterragem da amostra. Isto irá descarregar um ficheiro *ZIP* com o nome *RdfToDtdlConverter_sample_application_to_convert_RDF_to_DTDL.zip*, que depois pode desapertar e explorar.

Você pode usar esta amostra para ver os padrões de conversão em contexto, e ter como um bloco de construção para suas próprias aplicações executando conversões de modelos de acordo com suas necessidades específicas.

## <a name="next-steps"></a>Passos seguintes 

Leia mais sobre a conceção e gestão de modelos digitais twin:
 
* [*Conceitos: Modelos personalizados*](concepts-models.md)
* [*Como fazer: Gerir modelos personalizados*](how-to-manage-model.md)
* [*Como fazer: Parse e validar modelos*](how-to-parse-models.md)