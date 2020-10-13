---
title: Restrições e detalhes do suporte de formatos API
titleSuffix: Azure API Management
description: Detalhes de questões e restrições conhecidas sobre suporte a formatos Open API, WSDL e WADL na Azure API Management.
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
ms.openlocfilehash: 86ed7f3941965bcac525a2ba71786d20a4753489
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91335505"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de APIs e problemas conhecidos

## <a name="about-this-list"></a>Sobre esta lista

Ao importar uma API, poderá encontrar algumas restrições ou identificar questões que precisam de ser corrigidas antes de poder realizar a importação com sucesso. Este artigo documenta estas limitações, organizadas pelo formato de importação da API. Também descreve como funciona a exportação da OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitações de importação OpenAPI/Swagger

Se estiver a receber erros na importação do seu documento OpenAPI, certifique-se de que o validou previamente. Pode fazê-lo utilizando o designer no portal Azure (Design - Front End - OpenAPI Specification Editor), ou com uma ferramenta de terceiros, como <a href="https://editor.swagger.io">o Swagger Editor.</a>

### <a name="general"></a><a name="open-api-general"> </a>Geral

-   Os parâmetros necessários em ambos os caminhos e consulta devem ter nomes únicos. (No OpenAPI, um nome de parâmetro só precisa de ser único dentro de um local, por exemplo, caminho, consulta, cabeçalho. No entanto, na Gestão da API permitimos que as operações sejam discriminadas tanto pelos parâmetros de percurso como por consulta (que o OpenAPI não suporta). É por isso que exigimos que os nomes dos parâmetros sejam únicos dentro de todo o modelo de URL.)
-   `\$ref` os ponteiros não podem fazer referência a ficheiros externos.
-   `x-ms-paths` e `x-servers` são as únicas extensões apoiadas.
-   As extensões personalizadas são ignoradas na importação e não são guardadas ou preservadas para exportação.
-   `Recursion` - A API Management não suporta definições definidas recursivamente (por exemplo, esquemas referentes a si mesmos).
-   O URL de ficheiro de origem (se disponível) é aplicado em URLs relativos do servidor.
-   As definições de segurança são ignoradas.
-   As definições de esquema inline para operações de API não são suportadas. As definições de esquema são definidas no âmbito da API e podem ser referenciadas em pedidos de operações de API ou âmbitos de resposta.
-   Um parâmetro de URL definido precisa fazer parte do modelo de URL.
-   `Produces` a palavra-chave, que descreve os tipos de MIME devolvidos por uma API, não é suportada. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>Versão OpenAPI 2

-   Apenas o formato JSON é suportado.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>Versão OpenAPI 3

-   Se muitos `servers` forem especificados, a API Management tentará selecionar o primeiro URL HTTPs. Se não houver URLs HTTPs - o primeiro URL HTTP. Se não houver URLs HTTP - o URL do servidor estará vazio.
-   `Examples` não é apoiado, mas `example` é.

## <a name="openapi-import-update-and-export-mechanisms"></a>Mecanismos de importação, atualização e exportação do OpenAPI

### <a name="general"></a><a name="open-import-export-general"> </a>Geral

-   As definições de API exportadas do serviço de Gestão API destinam-se principalmente a aplicações externas ao serviço de Gestão de API que precisam de chamar a API hospedada no serviço de Gestão da API. As definições de API exportadas não se destinam a ser novamente importadas para o mesmo ou diferente serviço de Gestão de API. Para a gestão de configuração de defiições de API em diferentes serivces/envionments, consulte a documentação relativa à utilização do Serviço de Gestão da API com o Git. 

### <a name="add-new-api-via-openapi-import"></a>Adicionar novo API através da importação do OpenAPI

Para cada operação encontrada no documento OpenAPI, será criada uma nova operação com o nome do recurso Azure e o nome de exibição definidos `operationId` para `summary` e, respectivamente. `operationId` o valor é normalizado seguindo as regras descritas abaixo. `summary` o valor é importado como é e o seu comprimento é limitado a 300 caracteres.

Se `operationId` não for especificado (isto é, não `null` presente, ou vazio), o valor do nome do recurso Azure será gerado combinando método HTTP e modelo de caminho, por exemplo, `get-foo` .

Se `summary` não for especificado (isto é, não presente, ou `null` vazio), o valor será definido para `display name` `operationId` . Se `operationId` não for especificado, o valor do nome do visor será gerado combinando método HTTP e modelo de caminho, por exemplo, `Get - /foo` .

### <a name="update-an-existing-api-via-openapi-import"></a>Atualizar uma API existente através da importação do OpenAPI

Durante a importação a API existente é alterada para corresponder à API descrita no documento OpenAPI. Cada operação no documento OpenAPI é comparada com a operação existente, comparando o seu `operationId` valor com o nome de recurso Azure da operação existente.

Se for encontrada uma correspondência, as propriedades da operação existentes serão atualizadas "no local".

Se não for encontrado um fósforo, será criada uma nova operação utilizando as regras descritas na secção acima. Para cada nova operação, a importação tentará copiar as políticas de uma operação existente com o mesmo método HTTP e modelo de caminho.

Todas as operações incomparáveis existentes serão eliminadas.

Para tornar a importação mais previsível, siga estas orientações:

- Certifique-se de especificar `operationId` propriedade para cada operação.
- Abstenha-se de mudar `operationId` após a importação inicial.
- Nunca altere `operationId` e o método HTTP ou o modelo de caminho ao mesmo tempo.

### <a name="export-api-as-openapi"></a>Exportação de API como OpenAPI

Para cada operação, o seu nome de recurso Azure será exportado como `operationId` um , e o nome de exibição será exportado como um `summary` .
Regras de normalização para o operationId

- Converta-se em minúsculas.
- Substitua cada sequência de caracteres não alfanuméricos por um único traço, por exemplo, `GET-/foo/{bar}?buzz={quix}` será transformado em `get-foo-bar-buzz-quix-` .
- Traços de corte em ambos os lados, por exemplo, `get-foo-bar-buzz-quix-` tornar-se-ão `get-foo-bar-buzz-quix`
- Truncado para caber 76 caracteres, quatro caracteres menos do que o limite máximo para um nome de recurso.
- Utilize os restantes quatro caracteres para um sufixo deduplica, se necessário, sob a forma de `-1, -2, ..., -999` .


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Os ficheiros WSDL são utilizados para criar APIs de passagem de SABÃO e SOAP-to-REST.

-   **As ligações SOAP** -Apenas são suportadas as ligações SOAP de codificação de estilo "documento" e "literal". Não existe suporte para o estilo "rpc" ou soap-encoding.
-   **WSDL:Import** - Este atributo não é suportado. Os clientes devem fundir as importações num único documento.
-   **Mensagens com várias partes** - Este tipo de mensagens não são suportadas.
-   **WCF wsHttpBinding** - Os serviços soap criados com a Windows Communication Foundation devem utilizar o basicHttpBinding - wsHttpBinding não é suportado.
-   **MTOM** - Os serviços que utilizam o MTOM <em>podem</em> funcionar. O apoio oficial não é oferecido neste momento.
-   **Recursion** - Os tipos que são definidos de forma recursiva (por exemplo, consulte um conjunto de si mesmos) não são suportados pela APIM.
-   **Múltiplos espaços de nome -** Vários espaços de nome podem ser usados num esquema, mas apenas o espaço de nome alvo pode ser usado para definir partes de mensagens. Os espaços de identificação que não o alvo, que são utilizados para definir outros elementos de entrada ou saída, não são preservados. Embora tal documento WSDL possa ser importado, na exportação todas as partes de mensagens terão o espaço de nome-alvo da WSDL.
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

## <a name="wadl"></a><a name="wadl"> </a>RIO WADL

Atualmente, não existem questões de importação conhecidas da WADL.
