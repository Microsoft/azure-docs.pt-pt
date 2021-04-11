---
title: Políticas de validação de gestão da Azure API | Microsoft Docs
description: Saiba mais sobre as políticas que pode utilizar na Azure API Management para validar pedidos e respostas.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801898"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>Políticas de Gestão da API para validar pedidos e respostas

Este artigo fornece uma referência para as seguintes políticas de Gestão da API. Para obter informações sobre políticas de adição e configuração, consulte [Políticas em Gestão de API.](./api-management-policies.md)

Utilize políticas de validação para validar pedidos e respostas da API contra um esquema OpenAPI e proteger contra vulnerabilidades como injeção de cabeçalhos ou carga útil. Embora não seja um substituto para um Firewall de aplicação web, as políticas de validação proporcionam flexibilidade para responder a uma classe adicional de ameaças que não estão cobertas por produtos de segurança que dependem de regras estáticas e predefinidas.

## <a name="validation-policies"></a>Políticas de validação

- [Validar o conteúdo](#validate-content) - Valida o tamanho ou esquema JSON de um órgão de pedido ou resposta contra o esquema da API. 
- [Validar parâmetros](#validate-parameters) - Valida o cabeçalho, consulta ou parâmetros de percurso do pedido contra o esquema da API.
- [Validar cabeçalhos](#validate-headers) - Valida os cabeçalhos de resposta contra o esquema da API.
- [Validar código de estado](#validate-status-code) - Valida os códigos de estado HTTP em respostas ao esquema API.

> [!NOTE]
> O tamanho máximo do esquema da API que pode ser usado por uma política de validação é de 4 MB. Se o esquema exceder este limite, as políticas de validação retornarão os erros no tempo de funcionaamento. Para o aumentar, contacte [o suporte.](https://azure.microsoft.com/support/options/) 

## <a name="actions"></a>Ações

Cada política de validação inclui um atributo que especifica uma ação, que a API Management toma ao validar uma entidade num pedido de API ou resposta contra o esquema da API. Pode ser especificada uma ação para elementos que estejam representados no esquema da API e, dependendo da política, para elementos que não estejam representados no esquema da API. Uma ação especificada no elemento infantil de uma apólice substitui uma ação especificada para o seu progenitor.

Ações disponíveis:

| Ação         | Descrição          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignorar | Ignore a validação. |
| prevenir | Bloquear o processamento de pedido ou resposta, registar o erro de validação verbose e devolver um erro. O processamento é interrompido quando o primeiro conjunto de erros é detetado. |
| detect | Registar erros de validação, sem interromper o pedido ou o processamento de resposta. |

## <a name="logs"></a>Registos

Os detalhes sobre os erros de validação durante a execução da política são registados na variável `context.Variables` especificada no `errors-variable-name` atributo no elemento raiz da apólice. Quando configurado numa `prevent` ação, um erro de validação bloqueia o pedido ou o processamento de resposta e também é propagado à `context.LastError` propriedade. 

Para investigar erros, utilize uma política [de rastreio](api-management-advanced-policies.md#Trace) para registar os erros de variáveis de contexto para [Insights de Aplicação](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Implicações de desempenho

A adição de políticas de validação pode afetar o rendimento da API. Aplicam-se os seguintes princípios gerais:
* Quanto maior for o tamanho do esquema da API, menor será a produção. 
* Quanto maior for a carga útil num pedido ou resposta, menor será a produção. 
* O tamanho do esquema da API tem um impacto maior no desempenho do que o tamanho da carga útil. 
* A validação contra um esquema de API que é de vários megabytes de tamanho pode causar tempo de pedido ou resposta em algumas condições. O efeito é mais pronunciado nos níveis  **de Consumo** e **Desenvolvimento** do serviço. 

Recomendamos a realização de testes de carga com as suas cargas de trabalho de produção esperadas para avaliar o impacto das políticas de validação no rendimento da API.

## <a name="validate-content"></a>Validar conteúdo

A `validate-content` apólice valida o tamanho ou esquema JSON de um pedido ou órgão de resposta contra o esquema da API. Os formatos que não o JSON não são suportados.

### <a name="policy-statement"></a>Declaração política

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Exemplo

No exemplo seguinte, a carga útil JSON em pedidos e respostas é validada no modo de deteção. As mensagens com cargas superiores a 100 KB estão bloqueadas. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar conteúdo | Elemento de raiz.                                                                                                                               | Yes      |
| conteúdo | Adicione um ou mais destes elementos para validar o tipo de conteúdo no pedido ou resposta, e execute a ação especificada.  | No |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ação não especificada do tipo de conteúdo | [Ação](#actions) para executar pedidos ou respostas com um tipo de conteúdo que não está especificado no esquema da API. |  Yes     | N/D   |
| tamanho máximo | Comprimento máximo do corpo do pedido ou resposta em bytes, verificado contra o `Content-Length` cabeçalho. Se o corpo de pedido ou o corpo de resposta forem comprimidos, este valor é o comprimento descomprimido. Valor máximo permitido: 102.400 bytes (100 KB).  | Yes       | N/D   |
| tamanho-excedido-ação | [Ação](#actions) a realizar para pedidos ou respostas cujo corpo exceda o tamanho especificado em `max-size` . |  Yes     | N/D   |
| nome variável de erros | Nome da variável `context.Variables` para registar erros de validação para.  |   Yes    | N/D   |
| tipo | Tipo de conteúdo para executar a validação do corpo, verificado contra o `Content-Type` cabeçalho. Este valor é insensível. Se estiver vazio, aplica-se a todos os tipos de conteúdo especificados no esquema da API. |   No    |  N/D  |
| validar como | Motor de validação a utilizar para validação do corpo de um pedido ou resposta com um tipo de conteúdo correspondente. Atualmente, o único valor suportado é "json".   |  Yes     |  N/D  |
| ação | [Ação](#actions) para executar pedidos ou respostas cujo corpo não corresponda ao tipo de conteúdo especificado.  |  Yes      | N/D   |

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada, saída, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="validate-parameters"></a>Validar parâmetros

A `validate-parameters` política valida os parâmetros do cabeçalho, consulta ou caminho em pedidos contra o esquema da API.

> [!IMPORTANT]
> Se importou uma API utilizando uma versão API de gestão antes `2021-01-01-preview` de, a `validate-parameters` política pode não funcionar. Poderá ser necessário [reimportar](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) a sua API utilizando a versão API de gestão `2021-01-01-preview` ou posterior.


### <a name="policy-statement"></a>Declaração política

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Exemplo

Neste exemplo, todos os parâmetros de consulta e caminho são validados no modo de prevenção e cabeçalhos no modo de deteção. A validação é ultrapassada para vários parâmetros do cabeçalho:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar parâmetros | Elemento de raiz. Especifica ações de validação predefinidos para todos os parâmetros nos pedidos.                                                                                                                              | Yes      |
| cabeçalhos | Adicione este elemento para substituir as ações de validação predefinidos para parâmetros do cabeçalho em pedidos.   | No |
| consulta | Adicione este elemento para substituir as ações de validação predefinidos para parâmetros de consulta em pedidos.  | No |
| caminho | Adicione este elemento para substituir as ações de validação predefinidos para parâmetros de trajetória de URL em pedidos.  | No |
| parameter | Adicione um ou mais elementos para parâmetros nomeados para substituir a configuração de nível mais alto das ações de validação. | No |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ação de parâmetro especificado | [Ações](#actions) a executar para os parâmetros de pedido especificados no esquema da API. <br/><br/> Quando fornecido num `headers` `query` , ou `path` elemento, o valor sobrepõe-se ao valor do `specified-parameter-action` `validate-parameters` elemento.  |  Yes     | N/D   |
| ação de parâmetros não especificados | [Ações](#actions) a executar para parâmetros de pedido que não estão especificados no esquema da API. <br/><br/>Quando fornecido num `headers` ou `query` elemento, o valor sobrepõe-se ao valor do `unspecified-parameter-action` `validate-parameters` elemento. |  Yes     | N/D   |
| nome variável de erros | Nome da variável `context.Variables` para registar erros de validação para.  |   Yes    | N/D   |
| name | Nome do parâmetro para anular a ação de validação para. Este valor é insensível.  | Yes | N/D |
| ação | [Ação](#actions) a executar para o parâmetro com o nome correspondente. Se o parâmetro for especificado no esquema API, este valor substitui a configuração de nível `specified-parameter-action` superior. Se o parâmetro não for especificado no esquema API, este valor substitui a configuração de nível `unspecified-parameter-action` superior.| Yes | N/D | 

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** entrada

-   **Âmbitos de política:** todos os âmbitos

## <a name="validate-headers"></a>Validar cabeçalhos

A `validate-headers` política valida os cabeçalhos de resposta contra o esquema da API.

> [!IMPORTANT]
> Se importou uma API utilizando uma versão API de gestão antes `2021-01-01-preview` de, a `validate-headers` política pode não funcionar. Poderá ser necessário reimportar a sua API utilizando a versão API de gestão `2021-01-01-preview` ou posterior.

### <a name="policy-statement"></a>Declaração política

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Exemplo

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar cabeçalhos | Elemento de raiz. Especifica as ações de validação padrão para todos os cabeçalhos em respostas.                                                                                                                              | Yes      |
| cabeçalho | Adicione um ou mais elementos para cabeçalhos nomeados para anular as ações de validação padrão para cabeçalhos em respostas. | No |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ação de cabeçalho especificado | [Ação](#actions) a executar para cabeçalhos de resposta especificados no esquema da API.  |  Yes     | N/D   |
| ação não especificada do cabeçalho | [Ação](#actions) a executar para cabeçalhos de resposta que não estejam especificados no esquema da API.  |  Yes     | N/D   |
| nome variável de erros | Nome da variável `context.Variables` para registar erros de validação para.  |   Yes    | N/D   |
| name | Nome do cabeçalho para anular a ação de validação para. Este valor é insensível. | Yes | N/D |
| ação | [Ação](#actions) a executar para cabeçalho com o nome correspondente. Se o cabeçalho for especificado no esquema API, este valor sobrepõe-se ao valor do `specified-header-action` `validate-headers` elemento. Caso contrário, sobrepõe-se ao valor do `unspecified-header-action` elemento de cabeçalhos validados. | Yes | N/D | 

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** saída, erro

-   **Âmbitos de política:** todos os âmbitos

## <a name="validate-status-code"></a>Validar código de estado

A `validate-status-code` política valida os códigos de estado HTTP em respostas ao esquema da API. Esta política pode ser utilizada para evitar fugas de erros de backend, que podem conter vestígios de pilha. 

### <a name="policy-statement"></a>Declaração política

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Exemplo

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elementos

| Nome         | Descrição                                                                                                                                   | Obrigatório |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validar código de estado | Elemento de raiz.                                                                                                | Yes      |
| código de estado | Adicione um ou mais elementos para códigos de estado HTTP para anular a ação de validação padrão para códigos de estado em respostas. | No |

### <a name="attributes"></a>Atributos

| Nome                       | Descrição                                                                                                                                                            | Obrigatório | Predefinição |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| ação de código de estado não especificado | [Ação](#actions) a executar para códigos de estado HTTP em respostas que não estejam especificadas no esquema API.  |  Yes     | N/D   |
| nome variável de erros | Nome da variável `context.Variables` para registar erros de validação para.  |   Yes    | N/D   |
| code | Código de estado HTTP para anular ação de validação para. | Yes | N/D |
| ação | [Ação](#actions) a executar para o código de estado de correspondência, que não está especificado no esquema da API. Se o código de estado for especificado no esquema da API, esta sobreposição não faz efeito. | Yes | N/D | 

### <a name="usage"></a>Utilização

Esta política pode ser utilizada nas [seguintes secções](./api-management-howto-policies.md#sections) e [âmbitos políticos.](./api-management-howto-policies.md#scopes)

-   **Secções políticas:** saída, erro

-   **Âmbitos de política:** todos os âmbitos


## <a name="validation-errors"></a>Erros de validação
A tabela que se segue enumera todos os possíveis erros das políticas de validação. 

* **Detalhes** - Pode ser usado para investigar erros. Não era para ser partilhado publicamente.
* **Resposta pública** - Erro devolvido ao cliente. Não desmente detalhes de implementação.

| **Nome**                             | **Tipo**                                                        | **Regra de validação** | **Detalhes**                                                                                                                                       | **Resposta pública**                                                                                                                       | **Ação**           |
|----|----|---|---|---|----|
| **validar conteúdo** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |PedidoCorpo                                                     | TamanhoLimit           | O corpo do pedido é {size} bytes longo e excede o limite configurado de bytes {maxSize} .                                                       | O corpo do pedido é {size} bytes longo e excede o limite de bytes {maxSize}                                                          | detetar / prevenir |
||Corpo de Resposta                                                    | TamanhoLimit           | O corpo da resposta é {size} bytes longo e excede o limite configurado de bytes {maxSize} .                                                      | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {mensagemContentType}                 | PedidoCorpo                                                     | Não especificado         | Não é permitido o tipo de conteúdo não especificado {messageContentType} .                                                                                     | Não é permitido o tipo de conteúdo não especificado {messageContentType} .                                                                             | detetar / prevenir |
| {mensagemContentType}                 | Corpo de Resposta                                                    | Não especificado         | Não é permitido o tipo de conteúdo não especificado {messageContentType} .                                                                                     | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| | Apischema                                                       |                     | O esquema da API não existe ou não pode ser resolvido.                                                                                          | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
|                                      | Apischema                                                       |                     | O esquema da API não especifica definições.                                                                                                        | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {mensagemContentType}                 | Pessoa-pedido / Corpo de Resposta                                      | Definição desaparecida   | O esquema da API não contém definição {definitionName}, que está associado ao tipo de conteúdo {messageContentType}.                        | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {mensagemContentType}                 | PedidoCorpo                                                     | Incorreto Demissão    | O corpo do pedido não está em conformidade com a definição {definitionName}, que está associada ao tipo de conteúdo {messageContentType}.<br/><br/>{valError.Message} Linha} {valError.LineNumber}, Posição: {valError.LinePosition}                  | O corpo do pedido não está em conformidade com a definição {definitionName}, que está associada ao tipo de conteúdo {messageContentType}.<br/><br/>{valError.Message} Linha} {valError.LineNumber}, Posição: {valError.LinePosition}            | detetar / prevenir |
| {mensagemContentType}                 | Corpo de Resposta                                                    | Incorreto Demissão    | O corpo da resposta não está em conformidade com a definição {definitionName}, que está associada ao tipo de conteúdo {messageContentType}.<br/><br/>{valError.Message} Linha} {valError.LineNumber}, Posição: {valError.LinePosition}                                       | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
|                                      | PedidoCorpo                                                     | ValidaçãoExcepção | O corpo do pedido não pode ser validado para o tipo de conteúdo {messageContentType}.<br/><br/>{detalhes de exceção}                                                                | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
|                                      | Corpo de Resposta                                                    | ValidaçãoExcepção | O corpo da resposta não pode ser validado para o tipo de conteúdo {messageContentType}.<br/><br/>{detalhes de exceção}                                                                | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| **validar parâmetros / validar cabeçalhos** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {paramName} / {headerName}           | ConsultaParameter / PathParameter / RequestHeader                  | Não especificado         | Não é permitido o parâmetro de {path / parâmetro de consulta / cabeçalho} {paramName}                                                               | Não é permitido o parâmetro de {path / parâmetro de consulta / cabeçalho} {paramName}                                                       | detetar / prevenir |
| {cabeçalhoName}                         | ResponseHeader                                                  | Não especificado         | Não é permitido cabeçalho não especificado {headerName} não é permitido.                                                                                                   | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
|                                      |Apischema                                                       |                     | O esquema da API não existe ou não podia ser resolvido.                                                                                            | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
|                                       | Apischema                                                       |                     | O esquema da API não especifica definições.                                                                                                          | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {paramName}                          | QueriaParameter / PathParameter / RequestHeader / ResponseHeader | Definição desaparecida   | O esquema da API não contém definição {definitionName}, que está associado ao parâmetro {parâmetro de consulta / parâmetro de caminho / cabeçalho} {paramName}.  | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {paramName}                          | ConsultaParameter / PathParameter / RequestHeader                  | Incorreto Demissão    | O pedido não pode conter vários valores para o parâmetro {parâmetro de consulta / parâmetro do caminho / cabeçalho} {paramName}.                                           | O pedido não pode conter vários valores para o parâmetro {parâmetro de consulta / parâmetro do caminho / cabeçalho} {paramName}.                                   | detetar / prevenir |
| {cabeçalhoName}                         | ResponseHeader                                                  | Incorreto Demissão    | A resposta não pode conter vários valores para o cabeçalho {headerName}.                                                                              | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {paramName}                          | ConsultaParameter / PathParameter / RequestHeader                  | Incorreto Demissão    | O valor do parâmetro {parâmetro de consulta / parâmetro do caminho / cabeçalho} {paramName} não está em conformidade com a definição.<br/><br/>{valError.Message} Linha} {valError.LineNumber}, Posição: {valError.LinePosition}                                          | O valor do parâmetro {parâmetro de consulta / parâmetro de caminho / cabeçalho} {paramName} não está em conformidade com a definição.<br/><br/>{valError.Message} Linha} {valError.LineNumber}, Posição: {valError.LinePosition}                              | detetar / prevenir |
| {cabeçalhoName}                         | ResponseHeader                                                  | Incorreto Demissão    | O valor do cabeçalho {headerName} não está em conformidade com a definição.<br/><br/>{valError.Message} Linha} {valError.LineNumber}, Posição: {valError.LinePosition}                                                                              | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {paramName}                          | ConsultaParameter / PathParameter / RequestHeader                  | Incorreto Demissão    | O valor do parâmetro {parâmetro de consulta / parâmetro do caminho / cabeçalho} {paramName} não pode ser analisado de acordo com a definição. <br/><br/>{ex. Mensagem}                                | O valor do parâmetro {parâmetro de consulta / parâmetro do caminho / cabeçalho} {paramName} não poderia ser analisado de acordo com a definição. <br/><br/>{ex. Mensagem}                      | detetar / prevenir |
| {cabeçalhoName}                         | ResponseHeader                                                  | Incorreto Demissão    | O valor do cabeçalho {headerName} não pôde ser analisado de acordo com a definição.                                                                  | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {paramName}                          | ConsultaParameter / PathParameter / RequestHeader                  | ValidaçãoError     | {Parâmetro de consulta / Parâmetro do caminho / Cabeçalho} {paramName} não pode ser validado.<br/><br/>{detalhes de exceção}                                                                      | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| {cabeçalhoName}                         | ResponseHeader                                                  | ValidaçãoError     | Cabeçalho {cabeçalhoName} não pode ser validado.<br/><br/>{detalhes de exceção}                                                                                                          | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |
| **validar código de estado**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {código de estado}                        | Código de Estado                                                      | Não especificado         | O código de estado de resposta {status-code} não é permitido.                                                                                                | O pedido não pôde ser processado devido a um erro interno. Contacte o proprietário da API.                                                       | detetar / prevenir |


A tabela a seguir enumera todos os valores possíveis de razão de um erro de validação juntamente com possíveis valores de mensagem:

|  **Razão**         |    **Mensagem** |
|---|---|  
| Mau pedido       |     {Detalhes} para variável de contexto, {Resposta pública} para cliente|
| Resposta não permitida  | {Detalhes} para variável de contexto, {Resposta pública} para cliente |






## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre o trabalho com as políticas, consulte:

-   [Políticas em Gestão de API](api-management-howto-policies.md)
-   [Transformar APIs](transform-api.md)
-   [Referência política](./api-management-policies.md) para uma lista completa de declarações políticas e suas definições
-   [Exemplos de Políticas](./policy-reference.md)
-   [Processamento de erros](./api-management-error-handling-policies.md)
