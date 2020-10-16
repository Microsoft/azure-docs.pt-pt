---
title: Operações core IO / Microsoft Azure Maps
description: Aprenda a ler e escrever de forma eficiente XML e dados delimitados utilizando bibliotecas principais a partir do módulo IO espacial.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: 1689ff4d24b3bf82298041fbb84d759b451d8eab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91321769"
---
# <a name="core-io-operations"></a>Operações core IO

Além de fornecer ferramentas para ler ficheiros de dados espaciais, o módulo de IO espacial expõe as bibliotecas subjacentes fundamentais para ler e escrever XML e delimitar dados de forma rápida e eficiente.

O `atlas.io.core` espaço de nome contém duas classes de baixo nível que podem ler e escrever rapidamente dados de CSV e XML. Estas classes base alimentam os leitores de dados espaciais e os escritores no módulo Spatial IO. Sinta-se livre para usá-los para adicionar suporte adicional de leitura e escrita para ficheiros CSV ou XML.
 
## <a name="read-delimited-files"></a>Ler ficheiros delimitados

A `atlas.io.core.CsvReader` classe lê cordas que contêm conjuntos de dados delimitados. Esta classe fornece dois métodos para ler dados:

- A `read` função irá ler o conjunto de dados completo e devolver uma matriz bidimensional de cordas representando todas as células do conjunto de dados delimitado.
- A `getNextRow` função lê cada linha de texto num conjunto de dados delimitado e devolve uma matriz de cordas que representa todas as células nessa linha de conjunto de dados. O utilizador pode processar a linha e eliminar qualquer memória não precisa dessa linha antes de processar a linha seguinte. Então, a função é mais eficiente em termos de memória.

Por predefinição, o leitor usará o carácter de vírgula como o delimiter. No entanto, o delimiter pode ser alterado para qualquer personagem ou definido para `'auto'` . Quando `'auto'` programado, o leitor analisará a primeira linha de texto na cadeia. Em seguida, irá selecionar o personagem mais comum da tabela abaixo para usar como o delimiter.

| Delimitador | Caráter |
| :-- | :-- |
| Ponto | `,` |
| Tecla de Tabulação | `\t` |
| Tubo | `|` |

Este leitor também suporta classificações de texto que são usadas para manusear células que contêm o caráter delimiter. O carácter da citação `'"'` é o qualificador de texto predefinido, mas pode ser alterado para qualquer personagem.

## <a name="write-delimited-files"></a>Escrever ficheiros delimitados

Escreve `atlas.io.core.CsvWriter` uma variedade de objetos como uma corda delimitada. Qualquer personagem pode ser usado como um delimiter ou um qualificador de texto. O delimiter predefinido é vírgula ( `','` ) e o qualificador de texto predefinido é o caráter de citação `'"'` ()

Para utilizar esta classe, siga os passos abaixo:

- Crie uma instância da classe e, opcionalmente, desafase um delimiter personalizado ou de um qualificador de texto.
- Escreva dados para a classe utilizando a `write` função ou a `writeRow` função. Para a `write` função, passe uma matriz bidimensional de objetos representando várias linhas e células. Para utilizar a `writeRow` função, passe uma série de objetos representando uma linha de dados com várias colunas.
- Ligue `toString` para a função para recuperar a corda delimitada. 
- Opcionalmente, ligue para o `clear` método para tornar o escritor reutilizável e reduzir a sua alocação de recursos, ou chamar o método para eliminar a instância do `delete` escritor.

> [!Note]
> O número de colunas escritas será limitado ao número de células na primeira linha dos dados passados ao escritor.

## <a name="read-xml-files"></a>Ler ficheiros XML

A `atlas.io.core.SimpleXmlReader` classe é mais rápida a analisar ficheiros XML do que `DOMParser` . No entanto, a `atlas.io.core.SimpleXmlReader` classe requer que os ficheiros XML sejam bem formatados. Os ficheiros XML que não estão bem formatados, por exemplo, faltando tags de fecho, provavelmente resultarão num erro.

O código que se segue demonstra como utilizar a `SimpleXmlReader` classe para analisar uma cadeia XML num objeto JSON e serializá-la num formato desejado.

```javascript
//Create an instance of the SimpleXmlReader and parse an XML string into a JSON object.
var xmlDoc = new atlas.io.core.SimpleXmlReader().parse(xmlStringToParse);

//Verify that the root XML tag name of the document is the file type your code is designed to parse.
if (xmlDoc && xmlDoc.root && xmlDoc.root.tagName && xmlDoc.root.tagName === '<Your desired root XML tag name>') {

    var node = xmlDoc.root;

    //Loop through the child node tree to navigate through the parsed XML object.
    for (var i = 0, len = node.childNodes.length; i < len; i++) {
        childNode = node.childNodes[i];

        switch (childNode.tagName) {
            //Look for tag names, parse and serialized as desired.
        }
    }
}
```

## <a name="write-xml-files"></a>Escreva ficheiros XML

A `atlas.io.core.SimpleXmlWriter` classe escreve XML bem formatado de forma eficiente na memória.

O código a seguir demonstra como utilizar a `SimpleXmlWriter` classe para gerar uma cadeia XML bem formatada.

```javascript
//Create an instance of the SimpleXmlWriter class.
var writer = new atlas.io.core.SimpleXmlWriter();

//Start writing the document. All write functions return a reference to the writer, making it easy to chain the function calls to reduce the code size.
writer.writeStartDocument(true)
    //Specify the root XML tag name, in this case 'root'
    .writeStartElement('root', {
        //Attributes to add to the root XML tag.
        'version': '1.0',
        'xmlns': 'http://www.example.com',
         //Example of a namespace.
        'xmlns:abc': 'http://www.example.com/abc'
    });

//Start writing an element that has the namespace abc and add other XML elements as children.
writer.writeStartElement('abc:parent');

//Write a simple XML element like <title>Azure Maps is awesome!</title>
writer.writeElement('title', 'Azure Maps is awesome!');

//Close the element that we have been writing children to.
writer.writeEndElement();

//Finish writing the document by closing the root tag and the document.
writer.writeEndElement().writeEndDocument();

//Get the generated XML string from the writer.
var xmlString = writer.toString();
```

O XML gerado a partir do código acima seria como o seguinte.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<root version="1.0" xmlns="http://www.example.com" xmlns:abc="http://www.example.com/abc">
    <abc:parent>
        <title>Azure Maps is awesome!</title>
    </abc:parent>
</root>
```

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as aulas e métodos utilizados neste artigo:

[CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

[CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

[SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

[SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

[Detalhes do formato de dados suportado](spatial-io-supported-data-format-details.md)