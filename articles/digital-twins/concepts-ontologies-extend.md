---
title: Extensão de ontologies
titleSuffix: Azure Digital Twins
description: Conheça as razões e estratégias por trás do alargamento de uma ontologia
author: baanders
ms.author: baanders
ms.date: 2/12/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: e5973f58887b212919ad739232faafddcf9e735c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "100561545"
---
# <a name="extending-ontologies"></a>Extensão de ontologies 

Uma [ontologia](concepts-ontologies.md)padrão da indústria , como a [ontologia RealEstateCore baseada em DTDL para edifícios inteligentes,](https://github.com/Azure/opendigitaltwins-building)é uma ótima maneira de começar a construir a sua solução IoT. As pologias da indústria fornecem um rico conjunto de interfaces base que são projetadas para o seu domínio e projetadas para trabalhar fora da caixa em serviços Azure IoT, como Azure Digital Twins. 

No entanto, é possível que a sua solução possa ter necessidades específicas que não estejam cobertas pela ontologia da indústria. Por exemplo, é melhor ligar os seus gémeos digitais a modelos 3D armazenados num sistema separado. Neste caso, você pode estender uma destas ontologias para adicionar as suas próprias capacidades, mantendo todos os benefícios da ontologia original.

Este artigo utiliza a ontologia [RealEstateCore](https://www.realestatecore.io/) baseada em DTDL como base para exemplos de extensão de ontologias com novas propriedades DTDL. As técnicas descritas aqui são gerais, no entanto, e podem ser aplicadas a qualquer parte de uma ontologia baseada em DTDL com qualquer capacidade DTDL (Telemetria, Propriedade, Relacionamento, Componente ou Comando). 

## <a name="realestatecore-space-hierarchy"></a>Hierarquia espacial RealEstateCore 

Na ontologia RealEstateCore baseada em DTDL, a hierarquia espacial é usada para definir vários tipos de espaços: Quartos, Edifícios, Zona, etc. A hierarquia estende-se de cada um destes modelos para definir vários tipos de Salas, Edifícios e Zonas. 

Uma parte da hierarquia parece o diagrama abaixo. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-original.png" alt-text="Diagrama de fluxo ilustrando parte da hierarquia espacial RealEstateCore. No nível superior, há um elemento chamado Espaço; é ligado por uma seta 'estende', de um nível para o quarto; A sala está ligada por duas setas &quot;estende-se&quot; até um nível para ConferenceRoom e Office."::: 

Para obter mais informações sobre a ontologia RealEstateCore, consulte [*Conceitos: Adotar ontologias padrão da indústria.*](concepts-ontologies-adopt.md#realestatecore-smart-building-ontology)

## <a name="extending-the-realestatecore-space-hierarchy"></a>Ampliação da hierarquia espacial RealEstateCore 

Por vezes, a sua solução tem necessidades específicas que não são abrangidas pela ontologia da indústria. Neste caso, o alargamento da hierarquia permite-lhe continuar a utilizar a ontologia da indústria, personalizando-a para as suas necessidades. 

Neste artigo, discutimos dois casos diferentes em que o alargamento da hierarquia da ontologia é útil: 

* Adicionar novas interfaces para conceitos que não estão na ontologia da indústria. 
* Adicionar propriedades adicionais (ou relacionamentos, componentes, telemetria ou comandos) às interfaces existentes.

### <a name="add-new-interfaces-for-new-concepts"></a>Adicionar novas interfaces para novos conceitos 

Neste caso, pretende adicionar interfaces para conceitos necessários para a sua solução, mas que não estão presentes na ontologia da indústria. Por exemplo, se a sua solução tiver outros tipos de quartos que não estão representados na ontologia RealEstateCore baseada em DTDL, então pode adicioná-los estendendo-se diretamente das interfaces RealEstateCore. 

O exemplo abaixo apresenta uma solução que precisa de representar "salas de foco", que não estão presentes na ontologia RealEstateCore. Uma sala de foco é um pequeno espaço projetado para as pessoas se concentrarem numa tarefa por algumas horas de cada vez. 

Para alargar a ontologia da indústria com este novo conceito, crie uma nova interface que [se estenda](concepts-models.md#model-inheritance) a partir das interfaces na ontologia da indústria. 

Depois de adicionar a interface da sala de foco, a hierarquia estendida mostra o novo tipo de quarto. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-1.png" alt-text="Diagrama de fluxo que ilustra a hierarquia espacial RealEstateCore de cima, com uma nova adição. No nível inferior com ConferenceRoom e Office, há um novo elemento chamado FocusRoom (também conectado através de uma seta 'estende' a partir da sala)"::: 

### <a name="add-additional-capabilities-to-existing-interfaces"></a>Adicionar capacidades adicionais às interfaces existentes 

Neste caso, pretende adicionar mais propriedades (ou relacionamentos, componentes, telemetria ou comandos) a interfaces que se encontrem na ontologia da indústria.

Nesta secção, você verá dois exemplos: 
* Se estiver a construir uma solução que apresente desenhos 3D de espaços que já tem num sistema existente, talvez queira associar cada gémeo digital ao seu desenho 3D (por ID) para que quando a solução exibe informações sobre o espaço, também possa recuperar o desenho 3D do sistema existente. 
* Se a sua solução precisar de acompanhar o estado on-line/offline das salas de conferências, então é melhor acompanhar o estado da sala de conferências para ser usada em visualização ou consultas. 

Ambos os exemplos podem ser implementados com novas propriedades: um `drawingId` imóvel que associa o desenho 3D ao twin digital e uma propriedade "online" que indica se a sala de conferências está online ou não. 

Normalmente, não quer modificar a ontologia da indústria diretamente porque gostaria de ser capaz de incorporar atualizações na sua solução no futuro (o que substituiria as suas adições). Em vez disso, este tipo de adições pode ser feito na sua própria hierarquia de interface que se estende a partir da ontologia RealEstateCore baseada em DTDL. Cada interface que cria utiliza uma herança de interface múltipla para estender a interface RealEstateCore e a interface principal da sua hierarquia de interface estendida. Esta abordagem permite-lhe utilizar a ontologia da indústria e os seus acréscimos em conjunto. 

Para alargar a ontologia da indústria, cria as suas próprias interfaces que se estendem a partir das interfaces da indústria ontologia e adicionam as novas capacidades às suas interfaces estendidas. Para cada interface que pretende estender, cria uma nova interface. As interfaces estendidas são escritas em DTDL (ver DTDL para interfaces estendidas mais tarde neste documento). 

Depois de estender a parte da hierarquia acima mostrada, a hierarquia estendida parece o diagrama abaixo. Aqui a interface space estendida adiciona a `drawingId` propriedade que conterá um ID que associa o gémeo digital ao desenho 3D. Além disso, a interface ConferenceRoom adiciona uma propriedade "online" que conterá o estado on-line da sala de conferências. Através da herança, a interface ConferenceRoom contém todas as capacidades da interface RealEstateCore ConferenceRoom, bem como todas as capacidades da interface space estendida. 

:::image type="content" source="media/concepts-extending-ontologies/RealEstateCore-extended-2.png" alt-text="Diagrama de fluxo que ilustra a hierarquia espacial RealEstateCore estendida de cima, com mais novas adições. A sala agora partilha o seu nível com um elemento espacial, que se conecta com uma seta 'estende' um nível para um novo elemento da Sala ao lado do ConferenceRoom e Office.  Os novos elementos estão ligados à ontologia existente com relações mais &quot;alargadas&quot;."::: 

## <a name="using-the-extended-space-hierarchy"></a>Usando a hierarquia espacial estendida 

Quando criar gémeos digitais usando a hierarquia espacial estendida, o modelo de cada gémeo digital será um da hierarquia espacial estendida (não a ontologia original da indústria) e incluirá todas as capacidades da ontologia da indústria e das interfaces estendidas, embora a herança da interface.

O modelo de cada gémeo digital será uma interface da hierarquia estendida, mostrada no diagrama abaixo. 
 
:::image type="content" source="media/concepts-extending-ontologies/ontology-with-models.png" alt-text="Um excerto da hierarquia espacial RealEstateCore estendida, incluindo Espaço (nível superior), uma Sala (nível médio) e ConferenceRoom, Office e FocusRoom (nível inferior). Os nomes dos modelos estão ligados a cada elemento (por exemplo, o Quarto está ligado a um modelo chamado Room101)."::: 

Ao consultar gémeos digitais utilizando o ID do modelo (o `IS_OF_MODEL` operador), devem ser utilizados os IDs do modelo da hierarquia estendida. Por exemplo, `SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:com:example:Office;1')`. 

## <a name="contributing-back-to-the-original-ontology"></a>Contribuindo de volta para a ontologia original 

Em alguns casos, irá alargar a ontologia da indústria de uma forma que é amplamente útil para a maioria dos utilizadores da ontologia. Neste caso, deve considerar contribuir com as suas extensões de volta para a ontologia original. Cada ontologia tem um processo diferente para contribuir, por isso verifique o repositório gitHub da ontologia para obter detalhes da contribuição. 

## <a name="dtdl-for-new-interfaces"></a>DTDL para novas interfaces 

O DTDL para novas interfaces que se estendem diretamente da indústria ontologia seria assim. 

```json
{
  "@id": "dtmi:com:example:FocusRoom;1", 
  "@type": "interface", 
  "extends": "dtmi:digitaltwins:rec_3_3:building:Office;1", 
  "@context": "dtmi:dtdl:context;2" 
} 
```

## <a name="dtdl-for-extended-interfaces"></a>DTDL para interfaces estendidas 

O DTDL para as interfaces estendidas, limitado à parte acima discutida, seria assim. 

```json
[
  {
    "@id": "dtmi:com:example:Space;1",
    "@type": "Interface",
    "extends": "dtmi:digitaltwins:rec_3_3:core:Space;1",
    "contents": [
      {
        "@type": "Property",
        "name": "drawingid",
        "schema": "string"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Room;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:core:Room;1",
      "dtmi:com:example:Space;1"
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:ConferenceRoom;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:ConferenceRoom;1",
      "dtmi:com:example:Room;1"
    ],
    "contents": [
      {
        "@type": "Property",
        "name": "online",
        "schema": "boolean"
      }
    ],
    "@context": "dtmi:dtdl:context;2"
  },
  {
    "@id": "dtmi:com:example:Office;1",
    "@type": "Interface",
    "extends": [
      "dtmi:digitaltwins:rec_3_3:building:Office;1", 
      "dtmi:com:example:Room;1" 
    ],
    "@context": "dtmi:dtdl:context;2" 
  }, 
  {
    "@id": "dtmi:com:example:FocusRoom;1", 
    "@type": "Interface", 
    "extends": "dtmi:com:example:Office;1", 
    "@context": "dtmi:dtdl:context;2" 
  }
]
``` 

## <a name="next-steps"></a>Passos seguintes

Continue no caminho para o desenvolvimento de modelos baseados em ontologias: [*Utilizando estratégias de ontologia num caminho de desenvolvimento de modelos.*](concepts-ontologies.md#using-ontology-strategies-in-a-model-development-path)