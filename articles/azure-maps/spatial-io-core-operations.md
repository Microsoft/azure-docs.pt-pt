---
title: Operações core IO  Microsoft Azure Maps
description: Aprenda a ler e escrever de forma eficiente XML e dados delimitados usando bibliotecas centrais do módulo IO espacial.
author: farah-alyasari
ms.author: v-faalya
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: d2a82fd5d9ba958fd6490a83ecbbe0a4bdf820a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78370937"
---
# <a name="core-io-operations"></a>Operações core IO

Além de fornecer ferramentas para ler ficheiros de dados espaciais, o módulo IO espacial expõe bibliotecas subjacentes fundamentais para ler e escrever XML e dados delimitados de forma rápida e eficiente.

O espaço de `atlas.io.core` contém duas classes de baixo nível que podem ler e escrever dados CSV e XML rapidamente. Estas classes base alimentam os leitores de dados espaciais e escritores no módulo Espacial IO. Sinta-se livre para usá-los para adicionar suporte adicional de leitura e escrita para ficheiros CSV ou XML.
 
## <a name="read-delimited-files"></a>Ler ficheiros delimitados

A classe `atlas.io.core.CsvReader` lê cordas que contêm conjuntos de dados delimitados. Esta classe fornece dois métodos para ler dados:

- A função `read` irá ler o conjunto completo de dados e devolver uma gama bidimensional de cordas que representam todas as células do conjunto de dados delimitado.
- A função `getNextRow` lê cada linha de texto num conjunto de dados delimitado e devolve um conjunto de cordas que representam todas as células dessa linha de conjunto de dados. O utilizador pode processar a linha e eliminar qualquer memória desnecessária dessa linha antes de processar a próxima linha. Então, a função é mais eficiente em memória.

Por predefinição, o leitor utilizará o personagem da vírina como delimitador. No entanto, o delimitador pode ser alterado para qualquer personagem ou definido para `'auto'`. Quando definido para `'auto'`, o leitor analisará a primeira linha de texto na cadeia. Em seguida, selecionará o personagem mais comum da tabela abaixo para usar como delimitador.

| | |
| :-- | :-- |
| Víris | `,` |
| Guia | `\t` |
| Tubo | `|` |

Este leitor também suporta qualificações de texto que são usadas para lidar com células que contêm o caráter delimitador. O carácter da citação (`'"'`) é o qualificador de texto padrão, mas pode ser alterado para qualquer personagem.

## <a name="write-delimited-files"></a>Escrever ficheiros delimitados

O `atlas.io.core.CsvWriter` escreve uma série de objetos como uma corda delimitada. Qualquer personagem pode ser usado como delimitador ou como um qualificador de texto. O delimitador predefinido é vírex (`','`) e o modo de qualificação de texto padrão é o carácter de citação (`'"'`).

Para utilizar esta aula, siga os passos abaixo:

- Crie uma instância da classe e, opcionalmente, detetete um delimitador personalizado ou um qualificador de texto.
- Escreva dados para a classe utilizando a função `write` ou a função `writeRow`. Para a função `write`, passe uma matriz bidimensional de objetos que representam múltiplas linhas e células. Para utilizar a função `writeRow`, passe uma série de objetos que representam uma linha de dados com várias colunas.
- Ligue para a função `toString` para recuperar a corda delimitada. 
- Opcionalmente, ligue para o método `clear` para tornar o escritor reutilizável e reduzir a sua alocação de recursos, ou chamar o método `delete` para eliminar a instância do escritor.

> [!Note]
> O número de colunas escritas será limitado ao número de células na primeira linha dos dados transmitidos ao escritor.

## <a name="read-xml-files"></a>Ler ficheiros XML

A aula de `atlas.io.core.SimpleXmlReader` é mais rápida a analisar ficheiros XML do que `DOMParser`. No entanto, a classe `atlas.io.core.SimpleXmlReader` requer que os ficheiros XML sejam bem formatados. Os ficheiros XML que não estão bem formatados, por exemplo, faltando etiquetas de fecho, provavelmente resultarão num erro.

O código que se segue demonstra como usar a classe `SimpleXmlReader` para analisar uma corda XML num objeto JSON e serializá-la num formato desejado.

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

A classe `atlas.io.core.SimpleXmlWriter` escreve XML bem formatado de uma forma eficiente em memória.

O código que se segue demonstra como usar a classe `SimpleXmlWriter` para gerar uma cadeia XML bem formatada.

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

O XML gerado a partir do código acima seria o seguinte.

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

> [!div class="nextstepaction"]
> [CsvReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvreader)

> [!div class="nextstepaction"]
> [CsvWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.csvwriter)

> [!div class="nextstepaction"]
> [SimpleXmlReader](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlreader)

> [!div class="nextstepaction"]
> [SimpleXmlWriter](https://docs.microsoft.com/javascript/api/azure-maps-spatial-io/atlas.io.core.simplexmlwriter)

Consulte os seguintes artigos para obter mais amostras de código para adicionar aos seus mapas:

> [!div class="nextstepaction"]
> [Detalhes do formato de dados suportados](spatial-io-supported-data-format-details.md)