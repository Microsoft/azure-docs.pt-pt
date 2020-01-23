---
title: Restrições e detalhes de suporte a formatos de API
titleSuffix: Azure API Management
description: Detalhes de problemas conhecidos e restrições sobre os formatos Open API, WSDL e WADL dão suporte ao gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513376"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos

## <a name="about-this-list"></a>Sobre esta lista

Ao importar uma API, você pode ter algumas restrições ou identificar problemas que precisam ser corrigidos antes de poder executar a importação com êxito. Este artigo documenta essas limitações, organizadas pelo formato de importação da API. Ele também descreve como o OpenAPI Export funciona.

## <a name="open-api"> </a>Limitações de importação do openapi/Swagger

Se você estiver recebendo erros ao importar o documento do OpenAPI, verifique se você o validou antecipadamente. Você pode fazer isso usando o designer no portal do Azure (design-front-end-editor de especificação OpenAPI) ou com uma ferramenta de terceiros, como o <a href="https://editor.swagger.io">Editor do Swagger</a>.

### <a name="open-api-general"> </a>Geral

-   Os parâmetros necessários no caminho e na consulta devem ter nomes exclusivos. (No OpenAPI, um nome de parâmetro só precisa ser exclusivo dentro de um local, por exemplo, caminho, consulta, cabeçalho. No entanto, no gerenciamento de API, permitimos que as operações sejam discriminadas por parâmetros de caminho e de consulta (que OpenAPI não dá suporte). É por isso que exigimos que os nomes de parâmetro sejam exclusivos no modelo de URL inteiro.)
-   `\$ref` ponteiros não podem referenciar arquivos externos.
-   `x-ms-paths` e `x-servers` são as únicas extensões com suporte.
-   As extensões personalizadas são ignoradas na importação e não são salvas ou preservadas para exportação.
-   `Recursion`-o gerenciamento de API não dá suporte a definições definidas recursivamente (por exemplo, esquemas que se referim a si mesmos).
-   A URL do arquivo de origem (se disponível) é aplicada a URLs de servidor relativas.
-   As definições de segurança são ignoradas.
-   Não há suporte para definições de esquema embutidas para operações de API. As definições de esquema são definidas no escopo da API e podem ser referenciadas em escopos de resposta ou solicitação de operações de API.
-   Um parâmetro de URL definido precisa fazer parte do modelo de URL.
-   `Produces` palavra-chave, que descreve os tipos MIME retornados por uma API, não tem suporte. 

### <a name="open-api-v2"> </a>Openapi versão 2

-   Há suporte apenas para o formato JSON.

### <a name="open-api-v3"> </a>Openapi versão 3

-   Se muitos `servers` forem especificados, o gerenciamento de API tentará selecionar a primeira URL HTTPs. Se não houver nenhuma URL HTTPs-a primeira HTTP. Se não houver nenhuma URL de HTTP, ela estará vazia.
-   Não há suporte para `Examples`, mas `example` é.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mecanismos de importação, atualização e exportação do OpenAPI

### <a name="add-new-api-via-openapi-import"></a>Adicionar nova API por meio da importação de OpenAPI

Para cada operação encontrada no documento OpenAPI, uma nova operação será criada com o nome do recurso do Azure e o nome de exibição definido como `operationId` e `summary` respectivamente. `operationId` valor é normalizado seguindo as regras descritas abaixo. `summary` valor é importado como está e seu comprimento está limitado a 300 caracteres.

Se `operationId` não for especificado (ou seja, não estiver presente, `null`ou vazio), o valor do nome do recurso do Azure será gerado pela combinação do método HTTP e do modelo de caminho, por exemplo, `get-foo`.

Se `summary` não for especificado (ou seja, não estiver presente, `null`ou vazio), `display name` valor será definido como `operationId`. Se `operationId` não for especificado, o valor do nome de exibição será gerado pela combinação do método HTTP e do modelo de caminho, por exemplo, `Get - /foo`.

### <a name="update-an-existing-api-via-openapi-import"></a>Atualizar uma API existente por meio da importação do OpenAPI

Durante a importação, a API existente é alterada para corresponder à API descrita no documento OpenAPI. Cada operação no documento OpenAPI corresponde à operação existente, comparando seu valor de `operationId` com o nome de recurso do Azure da operação existente.

Se uma correspondência for encontrada, as propriedades da operação existente serão atualizadas "in-loco".

Se uma correspondência não for encontrada, uma nova operação será criada usando as regras descritas na seção acima. Para cada nova operação, a importação tentará copiar políticas de uma operação existente com o mesmo método e modelo de caminho HTTP.

Todas as operações incompatíveis existentes serão excluídas.

Para tornar a importação mais previsível, siga estas diretrizes:

- Certifique-se de especificar `operationId` propriedade para cada operação.
- Evite alterar `operationId` após a importação inicial.
- Nunca altere `operationId` e o modelo de método ou caminho HTTP ao mesmo tempo.

### <a name="export-api-as-openapi"></a>Exportar API como OpenAPI

Para cada operação, o nome do recurso do Azure será exportado como um `operationId`e o nome de exibição será exportado como um `summary`.
Regras de normalização para operationId

- Converter em minúsculas.
- Substitua cada sequência de caracteres não alfanuméricos por um único traço, por exemplo, `GET-/foo/{bar}?buzz={quix}` será transformada em `get-foo-bar-buzz-quix-`.
- Os traços de corte em ambos os lados, por exemplo, `get-foo-bar-buzz-quix-` ficarão `get-foo-bar-buzz-quix`
- Truncar para se ajustar a 76 caracteres, com quatro caracteres menores que o limite máximo para um nome de recurso.
- Use os quatro caracteres restantes para um sufixo de eliminação de duplicação, se necessário, na forma de `-1, -2, ..., -999`.


## <a name="wsdl"> </a>WSDL

Os arquivos WSDL são usados para criar APIs de passagem SOAP e SOAP para REST.

-   **Associações SOAP** -somente as associações SOAP de estilo "Document" e "literal" têm suporte. Não há suporte para o estilo "RPC" ou codificação SOAP.
-   **WSDL: Import** -esse atributo não tem suporte. Os clientes devem mesclar as importações em um documento.
-   **Mensagens com várias partes** – não há suporte para esses tipos de mensagens.
-   **WCF WSHttpBinding** -os serviços SOAP criados com Windows Communication Foundation devem usar BasicHttpBinding-WSHttpBinding sem suporte.
-   **MTOM** -os serviços usando MTOM <em>podem</em> funcionar. O suporte oficial não é oferecido no momento.
-   **Recursão** -os tipos definidos recursivamente (por exemplo, consulte uma matriz de si mesmos) não são suportados pelo APIM.
-   **Vários namespaces** -vários namespaces podem ser usados em um esquema, mas somente o namespace de destino pode ser usado para definir partes de mensagem. Os namespaces diferentes do destino, que são usados para definir outros elementos de entrada ou saída, não são preservados. Embora tal documento WSDL possa ser importado, em exportar todas as partes de mensagem terão o namespace de destino do WSDL.
-   **Matrizes** – a transformação SOAP-to-REST dá suporte apenas a matrizes encapsuladas mostradas no exemplo abaixo:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"> </a>WADL

No momento, não há nenhum problema de importação de WADL conhecido.
