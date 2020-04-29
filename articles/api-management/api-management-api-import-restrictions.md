---
title: Restrições e detalhes do suporte aos formatos API
titleSuffix: Azure API Management
description: Detalhes de questões conhecidas e restrições sobre os formatos Open API, WSDL e WADL suportam na Gestão API Azure.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76513376"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de APIs e problemas conhecidos

## <a name="about-this-list"></a>Sobre esta lista

Ao importar uma API, pode encontrar algumas restrições ou identificar questões que precisam de ser retificadas antes de poder realizar com sucesso a importação. Este artigo documenta estas limitações, organizadas pelo formato de importação da API. Também descreve como funciona a exportação da OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitações de importação OpenAPI/Swagger

Se estiver a receber erros que importem o seu documento OpenAPI, certifique-se de que o validou previamente. Pode fazê-lo utilizando o designer no portal Azure (Design - Front End - OpenAPI Specification Editor), ou com uma ferramenta de terceiros como <a href="https://editor.swagger.io">swagger Editor.</a>

### <a name="general"></a><a name="open-api-general"> </a>Geral

-   Os parâmetros necessários em ambos os caminhos e consulta devem ter nomes únicos. (No OpenAPI, um nome de parâmetro só precisa ser único dentro de um local, por exemplo caminho, consulta, cabeçalho. No entanto, na API Management permitimos que as operações sejam discriminadas tanto por parâmetros de percurso como de consulta (que a OpenAPI não suporta). É por isso que exigimos que os nomes dos parâmetros sejam únicos dentro de todo o modelo de URL.)
-   `\$ref`ponteiros não podem referenciar ficheiros externos.
-   `x-ms-paths`e `x-servers` são as únicas extensões apoiadas.
-   As extensões personalizadas são ignoradas na importação e não são guardadas ou preservadas para exportação.
-   `Recursion`- A API Management não suporta definições definidas de forma recursiva (por exemplo, schemas referindo-se a si mesmos).
-   O URL de ficheiro de origem (se disponível) é aplicado a URLs relativos do servidor.
-   As definições de segurança são ignoradas.
-   As definições inline de esquemas para operações de API não são suportadas. As definições de esquema sema são definidas no âmbito da API e podem ser referenciadas em âmbitos de pedido ou resposta de operações da API.
-   Um parâmetro DE URL definido precisa fazer parte do modelo de URL.
-   `Produces`palavra-chave, que descreve tipos MIME devolvidos por uma API, não é suportado. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>Versão OpenAPI 2

-   Apenas o formato JSON é suportado.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>Versão 3 da OpenAPI

-   Se `servers` muitos forem especificados, a API Management tentará selecionar o primeiro URL HTTPs. Se não houver URLs HTTPs - o primeiro URL HTTP. Se não houver URLs HTTP - o URL do servidor estará vazio.
-   `Examples`não é apoiado, `example` mas é.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mecanismos de importação, atualização e exportação da OpenAPI

### <a name="add-new-api-via-openapi-import"></a>Adicione novo API via importação openAPI

Para cada operação encontrada no documento OpenAPI, será criada uma nova operação `operationId` com `summary` o nome de recurso Azure e o nome de exibição definidos e respectivamente. `operationId`o valor é normalizado seguindo as regras descritas abaixo. `summary`o valor é importado como é e o seu comprimento é limitado a 300 caracteres.

Se `operationId` não for especificado (isto é, não presente, `null`ou vazio), o valor do nome do recurso Azure será gerado combinando o método HTTP e o modelo de caminho, por exemplo, `get-foo`.

Se `summary` não for especificado (isto é, não `null` `display name` presente, ou `operationId`vazio), o valor será definido para . Se `operationId` não for especificado, o valor do nome do ecrã será gerado `Get - /foo`combinando o método HTTP e o modelo de caminho, por exemplo, .

### <a name="update-an-existing-api-via-openapi-import"></a>Atualizar um API existente através da importação openAPI

Durante a importação, a API existente é alterada para corresponder à API descrita no documento OpenAPI. Cada operação no documento OpenAPI é igualada à `operationId` operação existente, comparando o seu valor com o nome de recurso Azure da operação existente.

Se for encontrada uma correspondência, as propriedades da operação existente serão atualizadas "no local".

Se não for encontrada uma correspondência, será criada uma nova operação utilizando as regras descritas na secção acima. Para cada nova operação, a importação tentará copiar as políticas de uma operação existente com o mesmo método HTTP e modelo de percurso.

Todas as operações incomparáveis existentes serão eliminadas.

Para tornar a importação mais previsível, siga estas orientações:

- Certifique-se `operationId` de especificar a propriedade para cada operação.
- Abster-se `operationId` de mudar após a importação inicial.
- Nunca `operationId` mude e http método ou modelo de caminho ao mesmo tempo.

### <a name="export-api-as-openapi"></a>API de exportação como OpenAPI

Para cada operação, o seu nome de `operationId`recurso Azure será exportado `summary`como um , e o nome de exibição será exportado como a .
Regras de normalização para operaçãoId

- Converta-se em minúsculas.
- Substitua cada sequência de caracteres não alfanuméricos `GET-/foo/{bar}?buzz={quix}` por um único `get-foo-bar-buzz-quix-`traço, por exemplo, será transformado em .
- Traços de corte em ambos `get-foo-bar-buzz-quix-` os lados, por exemplo, tornar-se-ão`get-foo-bar-buzz-quix`
- Truncate para caber 76 caracteres, quatro caracteres menos do que o limite máximo para um nome de recurso.
- Utilize os restantes quatro caracteres para um sufixo de `-1, -2, ..., -999`duplicação, se necessário, sob a forma de .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Os ficheiros WSDL são utilizados para criar APIs de passagem de SABÃO e SOAP-to-REST.

-   **Encadernações soap** -Apenas são suportadas encadernações de sabão de estilo "documento" e codificação "literal". Não existe suporte para o estilo "rpc" ou para a codificação de sabão.
-   **WSDL:Import** - Este atributo não é suportado. Os clientes devem fundir as importações num único documento.
-   **Mensagens com várias partes** - Este tipo de mensagens não são suportadas.
-   **WCF wsHttpBinding** - Os serviços soap criados com a Windows Communication Foundation devem utilizar o básicoHttpBinding - wsHttpBinding não é suportado.
-   **MTOM** - Os serviços que utilizam mtom <em>podem</em> funcionar. O apoio oficial não é oferecido neste momento.
-   **Recursion** - Os tipos que são definidos recursivamente (por exemplo, refiram-se a uma variedade de si mesmos) não são suportados pela APIM.
-   **Vários espaços de nomes** - Vários espaços de nome podem ser usados num esquema, mas apenas o espaço de nome-alvo pode ser usado para definir partes de mensagem. Não são preservados espaços de nome sem o alvo, utilizados para definir outros elementos de entrada ou saída. Embora tal documento WSDL possa ser importado, na exportação todas as partes de mensagem terá o espaço-nome-alvo da WSDL.
-   **Arrays** - A transformação SOAP-to-REST suporta apenas matrizes embrulhadas mostradas no exemplo abaixo:

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

## <a name="wadl"></a><a name="wadl"> </a>WADL

Atualmente, não existem questões de importação conhecidas da WADL.
