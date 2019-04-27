---
title: Restrições e problemas conhecidos na importação de API de gestão de API do Azure | Documentos da Microsoft
description: Detalhes de problemas conhecidos e restrições à importação no usando os formatos de API aberta, WSDL ou WADL de API Management do Azure.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 251caa840446e75ff13d9b4dcebcbae3a36473c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60657514"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restrições de importação de API e problemas conhecidos
## <a name="about-this-list"></a>Sobre esta lista
Ao importar uma API, poderá se deparou com algumas restrições ou identificar problemas que precisam de ser retificados antes de poder importá com êxito. Documentos neste artigo, organizadas pelo formato de importação da API.

## <a name="open-api"> </a>OpenAPI/Swagger

Se estiver a receber erros de importar o seu documento OpenAPI, certifique-se de que validar com antecedência. Pode fazer isso usando o designer no portal do Azure (Design - Front-End - Editor de especificação de OpenAPI) ou com uma ferramenta de terceiros, como <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="open-api-general"> </a>Geral

* Parâmetros obrigatórios no caminho e consulta tem de ter nomes exclusivos. (No OpenAPI um nome de parâmetro apenas tem de ser exclusivo dentro de um local, por exemplo, caminho, consulta e cabeçalho. No entanto, na gestão de API, podemos permitir operações a ser discriminados por parâmetros de caminho e consulta (que não suporta a OpenAPI). That's por que é necessário que os nomes de parâmetros de ser exclusivo dentro do modelo de URL completo.)
* **$ref** ponteiros não podem referenciar arquivos externos.
* **x-ms-caminhos** e **x-servers** são as extensões suportadas apenas.
* Extensões personalizadas são ignoradas ao importar e não são guardadas ou preservadas para exportação.
* **Recursão** -gestão de API não suporta definições definidas recursivamente (por exemplo, esquemas que faça referência a próprios).
* URL do ficheiro de origem (se disponível) é aplicada a URLs do servidor relativo.

### <a name="open-api-v2"> </a>Versão 2 do OpenAPI

* Formato JSON só é suportado.

### <a name="open-api-v3"> </a>OpenAPI versão 3

* Se muitos **servidores** forem especificados, gestão de API irá tentar selecionar o primeiro URL HTTPs. Caso não haja nenhum URL HTTPs - o primeiro URL de HTTP. Caso não haja nenhum URL de HTTP - o URL do servidor estará vazio.
* **Exemplos** não é suportada, mas **exemplo** é.
* **Multipart/form-data** não é suportada.

> [!IMPORTANT]
> Veja este [documento](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) para obter informações importantes e sugestões relacionadas com a importação de OpenAPI.

## <a name="wsdl"> </a>WSDL
Ficheiros WSDL são utilizados para gerar as APIs de pass-through SOAP ou servir como o back-end de uma API de SOAP para REST.
* **Enlaces de SOAP** -enlaces de apenas SOAP do estilo "documento" e a codificação de "literal" são suportados. Não há suporte para o estilo de "rpc" ou a codificação de SOAP.
* **WSDL:import** -este atributo não é suportado. Os clientes devem mesclar as importações num documento.
* **Mensagens com várias partes** -esses tipos de mensagens não são suportados.
* **WCF wsHttpBinding** -serviços SOAP criados com o Windows Communication Foundation devem utilizar a basicHttpBinding, wsHttpBinding não é suportada.
* **MTOM** - serviços usando o MTOM <em>poderá</em> trabalhar. Não é disponibilizado suporte oficial neste momento.
* **Recursão** -tipos que são definidos recursivamente (por exemplo, consulte a uma matriz por si mesmos) não são suportadas pelo APIM.
* **Vários espaços de nomes** - vários espaços de nomes podem ser usados num esquema, mas apenas o espaço de nomes de destino pode ser usado para definir partes das mensagens. Espaços de nomes diferente do destino que são utilizados para definir a outros elementos de entrada ou de saída não são mantidos. Embora possa ser importado um documento WSDL, todas as partes da mensagem serão necessário o espaço de nomes de destino do WSDL na exportação.

## <a name="wadl"> </a>WADL
Atualmente, não existem não existem problemas de importação WADL conhecidos.
