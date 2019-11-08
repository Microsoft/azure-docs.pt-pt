---
title: Restrições e problemas conhecidos na importação da API de gerenciamento de API do Azure | Microsoft Docs
description: Detalhes de problemas conhecidos e restrições sobre como importar para o gerenciamento de API do Azure usando os formatos Open API, WSDL ou WADL.
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
ms.date: 11/06/2019
ms.author: apimpm
ms.openlocfilehash: 88ef235d47a548ce426eaa2e8a8a56fb9dcb01d2
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796040"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos

## <a name="about-this-list"></a>Sobre esta lista

Ao importar uma API, você pode entrar em algumas restrições ou identificar problemas que precisam ser corrigidos antes de poder importar com êxito. Este artigo documenta esses documentos, organizados pelo formato de importação da API.

## <a name="open-api"> </a>Openapi/Swagger

Se você estiver recebendo erros ao importar o documento do OpenAPI, verifique se você o validou antecipadamente. Você pode fazer isso usando o designer no portal do Azure (design-front-end-editor de especificação OpenAPI) ou com uma ferramenta de terceiros, como o <a href="https://editor.swagger.io">Editor do Swagger</a>.

### <a name="open-api-general"> </a>Geral

-   Os parâmetros necessários no caminho e na consulta devem ter nomes exclusivos. (No OpenAPI, um nome de parâmetro só precisa ser exclusivo dentro de um local, por exemplo, caminho, consulta, cabeçalho. No entanto, no gerenciamento de API, permitimos que as operações sejam discriminadas por parâmetros de caminho e de consulta (que OpenAPI não dá suporte). É por isso que exigimos que os nomes de parâmetro sejam exclusivos no modelo de URL inteiro.)
-   **\$** ponteiros de referência não podem fazer referência a arquivos externos.
-   **x-MS-Paths** e **x-Servers** são as únicas extensões com suporte.
-   As extensões personalizadas são ignoradas na importação e não são salvas ou preservadas para exportação.
-   **Recursão** -o gerenciamento de API não dá suporte a definições definidas recursivamente (por exemplo, esquemas que se referim a si mesmos).
-   A URL do arquivo de origem (se disponível) é aplicada a URLs de servidor relativas.
-   As definições de segurança são ignoradas.
-   Não há suporte para definições de esquema embutidas para operações de API. As definições de esquema são definidas no escopo da API e podem ser referenciadas em escopos de resposta ou solicitação de operações de API.
-   Um parâmetro de URL definido precisa fazer parte do modelo de URL.
-   **Produz** palavra-chave, que descreve os tipos MIME retornados por uma API, não tem suporte. 

### <a name="open-api-v2"> </a>Openapi versão 2

-   Há suporte apenas para o formato JSON.

### <a name="open-api-v3"> </a>Openapi versão 3

-   Se vários **servidores** forem especificados, o gerenciamento de API tentará selecionar a primeira URL https. Se não houver nenhuma URL HTTPs-a primeira HTTP. Se não houver nenhuma URL de HTTP, ela estará vazia.
-   Não há suporte para **exemplos** , mas o **exemplo** é.
-   Não há suporte para **dados de várias partes/formulário** .

## <a name="wsdl"> </a>WSDL

Os arquivos WSDL são usados para criar APIs de passagem SOAP e SOAP para REST.

-   **Associações SOAP** -somente as associações SOAP de estilo "Document" e "literal" têm suporte. Não há suporte para o estilo "RPC" ou codificação SOAP.
-   **WSDL: Import** -esse atributo não tem suporte. Os clientes devem mesclar as importações em um documento.
-   **Mensagens com várias partes** – não há suporte para esses tipos de mensagens.
-   **WCF WSHttpBinding** -os serviços SOAP criados com Windows Communication Foundation devem usar BasicHttpBinding-WSHttpBinding sem suporte.
-   **MTOM** -os serviços usando MTOM <em>podem</em> funcionar. O suporte oficial não é oferecido no momento.
-   **Recursão** -os tipos definidos recursivamente (por exemplo, consulte uma matriz de si mesmos) não são suportados pelo APIM.
-   **Vários namespaces** -vários namespaces podem ser usados em um esquema, mas somente o namespace de destino pode ser usado para definir partes de mensagem. Namespaces diferentes do destino que são usados para definir outros elementos de entrada ou saída não são preservados. Embora tal documento WSDL possa ser importado, em exportar todas as partes de mensagem terão o namespace de destino do WSDL.
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
