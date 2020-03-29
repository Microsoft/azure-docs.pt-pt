---
title: Suporte para a partilha de recursos de origem cruzada (CORS) [ Microsoft Docs
description: Saiba como ativar o suporte cors para os Serviços de Armazenamento Microsoft Azure.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bb296db0d97382deac984369704777de5d5cb362
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "65147696"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Suporte para a partilha de recursos de origem cruzada (CORS) para os serviços de armazenamento azure
Começando com a versão 2013-08-15, os serviços de armazenamento Azure suportam a Partilha de Recursos Cross-Origin (CORS) para os serviços de Blob, Table, Queue e File. CorS é uma funcionalidade HTTP que permite que uma aplicação web que funciona sob um domínio aceda a recursos em outro domínio. Os navegadores da Web implementam uma restrição de segurança conhecida como [política de origem mesma](https://www.w3.org/Security/wiki/Same_Origin_Policy) que impede uma página web de chamar APIs num domínio diferente; O CORS fornece uma forma segura de permitir que um domínio (o domínio de origem) chame APIs em outro domínio. Consulte a [especificação cors](https://www.w3.org/TR/cors/) para obter mais detalhes sobre o CORS.

Pode definir as regras CORS individualmente para cada um dos serviços de armazenamento, ligando para definir propriedades de [serviço blob,](https://msdn.microsoft.com/library/hh452235.aspx) [definir propriedades](https://msdn.microsoft.com/library/hh452232.aspx)de serviço de fila e definir propriedades de serviço de [mesa](https://msdn.microsoft.com/library/hh452240.aspx). Uma vez estabelecido as regras CORS para o serviço, então um pedido devidamente autorizado feito contra o serviço de um domínio diferente será avaliado para determinar se é permitido de acordo com as regras que especificou.

> [!NOTE]
> Note que o CORS não é um mecanismo de autenticação. Qualquer pedido feito contra um recurso de armazenamento quando o CORS está ativado deve ter uma assinatura de autenticação adequada, ou deve ser feito contra um recurso público.
> 
> 

## <a name="understanding-cors-requests"></a>Compreender os pedidos do CORS
Um pedido cors de um domínio de origem pode consistir em dois pedidos distintos:

* Um pedido de pré-voo, que questiona as restrições cors impostas pelo serviço. O pedido de pré-voo é exigido a menos que o método de pedido seja um [método simples](https://www.w3.org/TR/cors/), que significa GET, HEAD ou POST.
* O pedido real, feito contra o recurso desejado.

### <a name="preflight-request"></a>Pedido de pré-voo
O pedido de pré-voo consulta as restrições cors que foram estabelecidas para o serviço de armazenamento pelo proprietário da conta. O navegador web (ou outro agente de utilizador) envia um pedido OPÇÕES que inclui os cabeçalhos de pedido, método e domínio de origem. O serviço de armazenamento avalia a operação pretendida com base num conjunto pré-configurado de regras CORS que especificam quais os domínios de origem, métodos de pedido e cabeçalhos de pedido podem ser especificados num pedido real contra um recurso de armazenamento.

Se o CORS estiver ativado para o serviço e existir uma regra CORS que corresponda ao pedido de pré-voo, o serviço responde com o código de estado 200 (OK), e inclui os cabeçalhos de Controlo de Acesso necessários na resposta.

Se o CORS não estiver ativado para o serviço ou nenhuma regra CORS corresponder ao pedido de pré-voo, o serviço responderá com o código de estado 403 (Proibido).

Se o pedido OPÇões não contiver os cabeçalhos CORS necessários (os cabeçalhos de Origem e Acesso-Controlo-Método de Pedido), o serviço responderá com o código de estado 400 (Mau pedido).

Note que um pedido de pré-voo é avaliado contra o serviço (Blob, Fila e Tabela) e não contra o recurso solicitado. O proprietário da conta deve ter habilitado o CORS como parte dos imóveis do serviço de conta para que o pedido tivesse sucesso.

### <a name="actual-request"></a>Pedido real
Uma vez aceite o pedido de pré-voo e a resposta devolvida, o navegador enviará o pedido real contra o recurso de armazenamento. O navegador negará imediatamente o pedido real se o pedido de pré-voo for rejeitado.

O pedido real é tratado como um pedido normal contra o serviço de armazenamento. A presença do cabeçalho Origin indica que o pedido é um pedido CORS e o serviço verificará as regras cors correspondentes. Se for encontrada uma correspondência, os cabeçalhos do Access-Control são adicionados à resposta e enviados de volta para o cliente. Se não for encontrado um fósforo, os cabeçalhos cors control-control não são devolvidos.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Habilitar o CORS para os serviços de armazenamento azure
As regras cors são definidas ao nível do serviço, por isso você precisa ativar ou desativar cors para cada serviço (Blob, Fila e Mesa) separadamente. Por predefinição, o CORS está desativado para cada serviço. Para ativar o CORS, é necessário definir as propriedades de serviço apropriadas utilizando a versão 2013-08-15 ou posterior, e adicionar as regras cors às propriedades do serviço. Para mais detalhes sobre como ativar ou desativar o CORS para um serviço e como definir as regras CORS, consulte as propriedades do [serviço De set Blob,](https://msdn.microsoft.com/library/hh452235.aspx) [definir propriedades](https://msdn.microsoft.com/library/hh452232.aspx)de serviço de fila e definir propriedades de serviço de [mesa](https://msdn.microsoft.com/library/hh452240.aspx).

Aqui está uma amostra de uma única regra CORS, especificada através de uma operação set Service Properties:

```xml
<Cors>    
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
        <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
        <MaxAgeInSeconds>200</MaxAgeInSeconds>
    </CorsRule>
<Cors>
```

Cada elemento incluído na regra CORS é descrito abaixo:

* **AllowedOrigins**: Os domínios de origem que são autorizados a fazer um pedido contra o serviço de armazenamento via CORS. O domínio de origem é o domínio de onde o pedido é originário. Note que a origem deve ser uma correspondência sensível a casos com a origem que a idade do utilizador envia para o serviço. Também pode utilizar o personagem wildcard '*' para permitir que todos os domínios de origem façam pedidos via CORS. No exemplo acima, os domínios http:\/\//www.contoso.com e http: /www.fabrikam.com podem fazer pedidos contra o serviço utilizando cors.
* **Métodos Permitidos**: Os métodos (HTTP solicitar verbos) que o domínio de origem pode utilizar para um pedido CORS. No exemplo acima, só são permitidos pedidos PUT e GET.
* **Cabeçalhos permitidos**: Os cabeçalhos de pedido que o domínio de origem pode especificar no pedido cors. No exemplo acima, todos os cabeçalhos de metadados a começar pelo x-ms-meta-dados, x-ms-meta-alvo e x-ms-meta-abc são permitidos. Note que o caracteres wildcard '*' indica que qualquer cabeçalho a começar com o prefixo especificado é permitido.
* **Cabeçalhos Expostos**: Os cabeçalhos de resposta que podem ser enviados na resposta ao pedido cors e expostos pelo navegador ao emitente de pedido. No exemplo acima, o navegador é instruído a expor qualquer cabeçalho começando com x-ms-meta.
* **MaxAgeInSeconds**: O tempo máximo que um navegador deve cache o pedido de OPÇÕES pré-voo.

Os serviços de armazenamento Azure suportam especificar cabeçalhos pré-fixados tanto para os **elementos AllowedHeaders** como **ExposedHeaders.** Para permitir uma categoria de cabeçalhos, pode especificar um prefixo comum a essa categoria. Por exemplo, especificar *x-ms-meta** como um cabeçalho pré-fixado estabelece uma regra que combinará com todos os cabeçalhos que começam com x-ms-meta.

As seguintes limitações aplicam-se às regras cors:

* Pode especificar até cinco regras CORS por serviço de armazenamento (Blob, Table e Queue).
* O tamanho máximo de todas as definições de regras cors no pedido, excluindo as etiquetas XML, não deve exceder 2 KB.
* O comprimento de um cabeçalho permitido, cabeçalho exposto ou origem permitida não deve exceder 256 caracteres.
* Os cabeçalhos permitidos e os cabeçalhos expostos podem ser:
  * Cabeçalhos literais, onde é fornecido o nome exato do cabeçalho, como **x-ms-meta-processados**. Pode ser especificado no pedido um máximo de 64 cabeçalhos literais.
  * Cabeçalhos pré-fixados, onde é fornecido um prefixo do cabeçalho, como **x-ms-meta-data***. Especificar um prefixo desta forma permite ou expõe qualquer cabeçalho que comece com o prefixo dado. Pode ser especificado no pedido um máximo de dois cabeçalhos pré-fixados.
* Os métodos (ou verbos HTTP) especificados no elemento **AllowedMethods** devem estar em conformidade com os métodos suportados pelas APIs do serviço de armazenamento Azure. Os métodos suportados são DELETE, GET, HEAD, MERGE, POST, OPTIONS e PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Compreender a lógica de avaliação de regras cors
Quando um serviço de armazenamento recebe um pedido pré-voo ou real, avalia esse pedido com base nas regras cors que estabeleceu para o serviço através da operação de set Service Properties apropriada. As regras cors são avaliadas na ordem em que foram definidas no órgão de pedido da operação set Service Properties.

As regras cors são avaliadas da seguinte forma:

1. Em primeiro lugar, o domínio de origem do pedido é verificado contra os domínios listados para o elemento **AllowedOrigins.** Se o domínio de origem estiver incluído na lista, ou todos os domínios forem permitidos com o caráter wildcard '*', então as regras de avaliação prosseguem. Se o domínio de origem não estiver incluído, o pedido falha.
2. Em seguida, o método (ou verbo HTTP) do pedido é verificado contra os métodos listados no elemento **AllowedMethods.** Se o método estiver incluído na lista, então as regras de avaliação prosseguem; se não, então o pedido falha.
3. Se o pedido corresponder a uma regra no seu domínio de origem e no seu método, essa regra é selecionada para processar o pedido e não são avaliadas outras regras. Antes que o pedido possa ter sucesso, no entanto, quaisquer cabeçalhos especificados no pedido são verificados contra os cabeçalhos listados no elemento **AllowedHeaders.** Se os cabeceamentos enviados não corresponderem aos cabeçalhos permitidos, o pedido falha.

Uma vez que as regras são processadas no despacho em que estão presentes no órgão de pedido, as melhores práticas recomendam que especifique as regras mais restritivas em relação às origens em primeiro lugar na lista, de modo a que estas sejam avaliadas primeiro. Especifique regras menos restritivas – por exemplo, uma regra que permita todas as origens – no final da lista.

### <a name="example--cors-rules-evaluation"></a>Exemplo – Avaliação de regras do CORS
O exemplo seguinte mostra um organismo de pedido parcial para uma operação para definir as regras cors para os serviços de armazenamento. Consulte as propriedades do [serviço Blob,](https://msdn.microsoft.com/library/hh452235.aspx) [definir propriedades](https://msdn.microsoft.com/library/hh452232.aspx)do serviço de fila e definir propriedades de serviço de [mesa](https://msdn.microsoft.com/library/hh452240.aspx) para obter detalhes sobre a construção do pedido.

```xml
<Cors>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>PUT,HEAD</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>*</AllowedOrigins>
        <AllowedMethods>PUT,GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-blob-content-type, x-ms-blob-content-disposition</AllowedHeaders>
    </CorsRule>
    <CorsRule>
        <AllowedOrigins>http://www.contoso.com</AllowedOrigins>
        <AllowedMethods>GET</AllowedMethods>
        <MaxAgeInSeconds>5</MaxAgeInSeconds>
        <ExposedHeaders>x-ms-*</ExposedHeaders>
        <AllowedHeaders>x-ms-client-request-id</AllowedHeaders>
    </CorsRule>
</Cors>
```

Em seguida, considere os seguintes pedidos cors:

| Pedir |  |  | Resposta |  |
| --- | --- | --- | --- | --- |
| **Método** |**Origem** |**Cabeçalhos dos Pedidos** |**Jogo de Regras** |**Resultado** |
| **COLOQUE** |http:\//www.contoso.com |x-ms-blob-content-type |Primeira regra |Êxito |
| **Obter** |http:\//www.contoso.com |x-ms-blob-content-type |Segunda regra |Êxito |
| **Obter** |http:\//www.contoso.com |x-ms-cliente-request-id |Segunda regra |Falha |

O primeiro pedido corresponde à primeira regra – o domínio de origem corresponde às origens permitidas, o método corresponde aos métodos permitidos, e o cabeceamento corresponde aos cabeceamentos permitidos – e assim sucede.

O segundo pedido não corresponde à primeira regra porque o método não corresponde aos métodos permitidos. No entanto, corresponde à segunda regra, por isso tem sucesso.

O terceiro pedido corresponde à segunda regra no seu domínio e método de origem, pelo que não são avaliadas mais regras. No entanto, o *cabeçalho x-ms-cliente-request-id* não é permitido pela segunda regra, pelo que o pedido falha, apesar de a semântica da terceira regra lhe ter permitido ter sucesso.

> [!NOTE]
> Embora este exemplo demonstre uma regra menos restritiva perante uma regra mais restritiva, em geral, a melhor prática é enumerar em primeiro lugar as regras mais restritivas.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Compreender como o cabeçalho De Vary é definido
O cabeçalho *Vary* é um cabeçalho PADRÃO HTTP/1.1 composto por um conjunto de campos de cabeçalho de pedido que aconselham o navegador ou o agente utilizador sobre os critérios que foram selecionados pelo servidor para processar o pedido. O cabeçalho *Vary* é usado principalmente para cache por proxies, navegadores e CDNs, que o utilizam para determinar como a resposta deve ser em cache. Para mais detalhes, consulte a especificação do [cabeçalho Vary](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando o navegador ou outro agente de utilizador cachea a resposta de um pedido CORS, o domínio de origem é cached como a origem permitida. Quando um segundo domínio emite o mesmo pedido de um recurso de armazenamento enquanto a cache está ativa, o agente utilizador recupera o domínio de origem em cache. O segundo domínio não corresponde ao domínio em cache, pelo que o pedido falha quando de outra forma teria sucesso. Em certos casos, o Armazenamento Azure define o cabeçalho Vary para a **Origem** para instruir o agente utilizador a enviar o pedido cors subsequente ao serviço quando o domínio solicitado difere da origem em cache.

O Armazenamento Azure define o cabeçalho *Vary* para **a Origem** para pedidos reais GET/HEAD nos seguintes casos:

* Quando a origem do pedido corresponde exatamente à origem permitida definida por uma regra CORS. Para ser um fósforo exato, a regra CORS pode não incluir um personagem wildcard .
* Não existe nenhuma regra que corresponda à origem do pedido, mas o CORS está habilitado para o serviço de armazenamento.

No caso de um pedido GET/HEAD corresponder a uma regra CORS que permite todas as origens, a resposta indica que todas as origens são permitidas, e a cache do agente utilizador permitirá pedidos subsequentes de qualquer domínio de origem enquanto a cache estiver ativa.

Note que para pedidos que utilizem métodos que não o GET/HEAD, os serviços de armazenamento não definirão o cabeçalho Vary, uma vez que as respostas a estes métodos não são cached pelos agentes utilizadores.

O quadro seguinte indica como o armazenamento do Azure responderá aos pedidos GET/HEAD com base nos casos anteriormente mencionados:

| Pedir | Definição de conta e resultado da avaliação das regras |  |  | Resposta |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Cabeçalho de origem presente a pedido** |**Regra(s) cors especificada para este serviço** |**Regra de correspondência existe que permite todas as origens(*)** |**Regra de correspondência existe para correspondência de origem exata** |**Resposta inclui conjunto de cabeçalho Vary para origem** |**Resposta inclui Acesso-Controlo-Origem Permitida: "*"** |**Resposta inclui cabeçalhos expostos de acesso-controlo** |
| Não |Não |Não |Não |Não |Não |Não |
| Não |Sim |Não |Não |Sim |Não |Não |
| Não |Sim |Sim |Não |Não |Sim |Sim |
| Sim |Não |Não |Não |Não |Não |Não |
| Sim |Sim |Não |Sim |Sim |Não |Sim |
| Sim |Sim |Não |Não |Sim |Não |Não |
| Sim |Sim |Sim |Não |Não |Sim |Sim |

## <a name="billing-for-cors-requests"></a>Faturação para pedidos cors
Os pedidos de pré-voo bem-sucedidos são faturados se tiver ativado o CORS para qualquer um dos serviços de armazenamento para a sua conta (ligando para definir propriedades de [serviço blob,](https://msdn.microsoft.com/library/hh452235.aspx)definir propriedades de serviço de [fila,](https://msdn.microsoft.com/library/hh452232.aspx)ou definir propriedades de serviço de [mesa).](https://msdn.microsoft.com/library/hh452240.aspx) Para minimizar as cargas, considere definir o elemento **MaxAgeInSeconds** nas suas regras CORS para um grande valor para que o agente utilizador cache o pedido.

Os pedidos de pré-voo mal sucedidos não serão cobrados.

## <a name="next-steps"></a>Passos seguintes
[Definir propriedades de serviço blob](https://msdn.microsoft.com/library/hh452235.aspx)

[Definir propriedades do serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx)

[Definir propriedades do serviço de mesa](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificação de partilha de recursos de origem cruzada W3C](https://www.w3.org/TR/cors/)

