---
title: Suporte a recursos de várias origens (CORS) de partilha | Documentos da Microsoft
description: Saiba como ativar o suporte de CORS para serviços de armazenamento do Microsoft Azure.
services: storage
author: cbrooksmsft
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 2/22/2017
ms.author: cbrooks
ms.subservice: common
ms.openlocfilehash: bb1f4861f3867c592ecab86e85d3a4dfbab6738e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002951"
---
# <a name="cross-origin-resource-sharing-cors-support-for-the-azure-storage-services"></a>Recursos de várias origens (CORS) suporte para os serviços de armazenamento do Azure de partilha
A partir da versão 2013-08-15, os serviços de armazenamento do Azure suportam Cross-Origin Resource Sharing (CORS) para os serviços de Blob, tabela, fila e ficheiro. CORS é uma funcionalidade HTTP que permite que uma aplicação web em execução num domínio aceder a recursos em outro domínio. Navegadores da Web implementam uma restrição de segurança, conhecida como [política de mesma origem](https://www.w3.org/Security/wiki/Same_Origin_Policy) que impede que uma página da web de chamar APIs num domínio diferente; CORS fornece uma forma segura de permitir que um domínio (o domínio de origem) chame APIs de outro domínio. Consulte a [especificação CORS](https://www.w3.org/TR/cors/) para obter detalhes sobre o CORS.

Pode definir as regras CORS individualmente para cada um dos serviços de armazenamento, ao chamar [definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades do serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx), e [definir propriedades do serviço tabela](https://msdn.microsoft.com/library/hh452240.aspx). Depois de definir as regras CORS para o serviço, em seguida, um pedido devidamente autorizado efetuado para o serviço a partir de um domínio diferente será avaliado para determinar se é permitido de acordo com as regras que especificou.

> [!NOTE]
> Tenha em atenção que o CORS não é um mecanismo de autenticação. Qualquer solicitação feita em relação a um recurso de armazenamento quando o CORS está ativado deve ter uma assinatura de uma autenticação adequada, ou que têm de ser feita em relação a um recurso de público.
> 
> 

## <a name="understanding-cors-requests"></a>Noções básicas sobre solicitações CORS
Uma solicitação CORS de um domínio de origem pode consistir em dois pedidos separados:

* Uma solicitação de simulação, que consulta as restrições de CORS impostas pelo serviço. Solicitação de simulação é necessária, a menos que o método de pedido é uma [método simples](https://www.w3.org/TR/cors/), ou seja, GET, HEAD ou POST.
* O pedido real, feito contra o recurso pretendido.

### <a name="preflight-request"></a>Solicitação de simulação
As consultas de solicitação de simulação, as restrições de CORS que foram estabelecidas para o serviço de armazenamento, o proprietário da conta. O navegador da web (ou outros agentes de utilizador) envia um pedido de opções que inclui os cabeçalhos de solicitação, o método e a origem do domínio. O serviço de armazenamento avalia a operação pretendida, com base num conjunto de regras CORS que especificar os domínios de origem, os métodos de pedido e cabeçalhos de pedido podem ser especificados um pedido real em relação a um recurso de armazenamento pré-configurado.

Se o CORS está ativado para o serviço e existe uma regra CORS que corresponde à solicitação de simulação, o serviço responde com o código de estado 200 (OK) e inclui os cabeçalhos necessários do controlo de acesso na resposta.

Se o CORS não está ativada para o serviço ou nenhuma regra CORS corresponde à solicitação de simulação, o serviço irá responder com o código de estado 403 (proibido).

Se o pedido de opções não contém os cabeçalhos CORS necessários (dos cabeçalhos de origem e o Access-Control-Request-Method), o serviço irá responder com o código de estado 400 (pedido incorreto).

Tenha em atenção que uma solicitação de simulação é avaliada contra o serviço (BLOBs, filas e tabelas) e não contra o recurso pedido. O proprietário da conta tem de ter ativado CORS como parte das propriedades do serviço de conta para que o pedido com êxito.

### <a name="actual-request"></a>Pedido real
Depois da solicitação de simulação é aceite e a resposta é retornada, o navegador irá enviar a solicitação real com o recurso de armazenamento. O navegador irá negar o real pedir imediatamente caso a solicitação de simulação é rejeitada.

O pedido real é tratado como normal pedido com o serviço de armazenamento. A presença do cabeçalho Origin indica que o pedido é um pedido CORS e o serviço irá verificar as regras CORS correspondentes. Se for encontrada uma correspondência, os cabeçalhos de controlo de acesso são adicionados à resposta e enviados de volta ao cliente. Se não for encontrada uma correspondência, os cabeçalhos de CORS Access-Control não são devolvidos.

## <a name="enabling-cors-for-the-azure-storage-services"></a>Ativar o CORS para os serviços de armazenamento do Azure
As regras CORS estão definidas no nível de serviço, por isso terá de ativar ou desativar o CORS para cada serviço (BLOBs, filas e tabelas) em separado. Por predefinição, o CORS está desativada para cada serviço. Para ativar o CORS, tem de definir as propriedades de serviço apropriado a utilizar a versão 2013-08-15 ou posterior e adicionar as regras CORS para as propriedades do serviço. Para obter detalhes sobre como ativar ou desativar a CORS para um serviço e como definir as regras CORS, consulte [definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades do serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx), e [definir o serviço de tabela Propriedades](https://msdn.microsoft.com/library/hh452240.aspx).

Eis um exemplo de uma única regra CORS, especificado por meio de uma operação definir propriedades do serviço:

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

* **AllowedOrigins**: Os domínios de origem que têm permissão para fazer um pedido para o serviço de armazenamento através do CORS. O domínio de origem é o domínio a partir do qual o pedido tem origem. Tenha em atenção que a origem tem de ser uma correspondência exata de maiúsculas e minúsculas com a origem que a idade do usuário envia para o serviço. Também pode utilizar o caráter universal "*" para permitir que todos os domínios de origem fazer pedidos através do CORS. No exemplo acima, os domínios [ http://www.contoso.com ](http://www.contoso.com) e [ http://www.fabrikam.com ](http://www.fabrikam.com) possa fazer pedidos para o serviço utilizando a CORS.
* **AllowedMethods**: Os métodos (verbos de pedido HTTP) que o domínio de origem pode utilizar para um pedido CORS. No exemplo acima, apenas pedidos PUT e GET são permitidos.
* **AllowedHeaders**: Os cabeçalhos de pedido que o domínio de origem pode especificar no pedido CORS. No exemplo acima, todos os cabeçalhos de metadados a partir do x-ms-metadados, x-ms-meta-destino e x-ms-meta-abc são permitidos. Tenha em atenção que o caráter universal "*" indica que qualquer início de cabeçalho com o prefixo especificado é permitido.
* **ExposedHeaders**: Os cabeçalhos de resposta que podem ser enviados na resposta ao pedido de CORS e expostos pelo browser para o emissor do pedido. No exemplo acima, o navegador é instruído para expor qualquer início de cabeçalho com x-ms-meta.
* **MaxAgeInSeconds**: O tempo máximo que um navegador deve OPTIONS prévio em cache o pedido.

Os serviços de armazenamento do Azure suportam a especificação de cabeçalhos com prefixo para ambos os **AllowedHeaders** e **ExposedHeaders** elementos. Para permitir uma categoria de cabeçalhos, pode especificar um prefixo comum para essa categoria. Por exemplo, pode especificar *x-ms-meta** como um cabeçalho com prefixo estabelece uma regra que irá corresponder a todos os cabeçalhos que começam com x-ms-meta.

As seguintes limitações aplicam-se para as regras CORS:

* Pode especificar até cinco regras CORS por serviço de armazenamento (Blob, tabela e fila).
* O tamanho máximo de todas as definições das regras CORS na solicitação, excluindo as marcas XML, não deve exceder os 2 KB.
* O comprimento de um cabeçalho permitido, cabeçalho exposto ou origem permitida não deve exceder os 256 carateres.
* Cabeçalhos permitidos e cabeçalhos expostos podem-se a:
  * Cabeçalhos de literais, onde o nome de cabeçalho exata é fornecido, tal como **x-ms-meta-processados**. Um máximo de 64 cabeçalhos literais pode ser especificado no pedido.
  * O prefixo cabeçalhos, onde um prefixo do cabeçalho é fornecido, tal como * * x-ms-meta-dados ***. Especificar um prefixo dessa maneira permite ou expõe qualquer cabeçalho que começa com o prefixo especificado. Um máximo de dois cabeçalhos com prefixo pode ser especificado no pedido.
* Os métodos (ou verbos HTTP) especificado na **AllowedMethods** elemento tem de estar em conformidade com os métodos suportados pelo APIs de serviço de armazenamento do Azure. Métodos suportados são DELETE, GET, HEAD, MERGE, POST, opções e PUT.

## <a name="understanding-cors-rule-evaluation-logic"></a>Compreender a lógica de avaliação da regra CORS
Quando um serviço de armazenamento recebe uma solicitação de simulação ou real, ela é avaliada esse pedido com base nas regras CORS que estabeleceu para o serviço através da operação definir propriedades do serviço apropriado. As regras CORS são avaliadas na ordem em que tenham sido definidos no corpo do pedido da operação definir propriedades do serviço.

As regras CORS são avaliadas da seguinte forma:

1. Em primeiro lugar, o domínio de origem do pedido é comparado com os domínios listados para o **AllowedOrigins** elemento. Se o domínio de origem está incluído na lista, ou todos os domínios têm permissão com o caráter universal "*", em seguida, as regras de avaliação continua. Se o domínio de origem não está incluído, em seguida, o pedido falha.
2. Em seguida, o método (ou o verbo HTTP) do pedido é comparado com os métodos listados na **AllowedMethods** elemento. Se o método está incluído na lista, avaliação de regras continua; caso contrário, em seguida, o pedido falhar.
3. Se o pedido de corresponder a uma regra no seu domínio de origem e seu método, essa regra é selecionada para processar que o pedido e não mais regras são avaliadas. Antes do pedido pode ter êxito, as qualquer cabeçalho especificado na solicitação, no entanto, é verificado relativamente os cabeçalhos listados na **AllowedHeaders** elemento. Se os cabeçalhos enviados não corresponderem os cabeçalhos permitidos, o pedido falha.

Uma vez que as regras são processadas na ordem que estão presentes no corpo do pedido, recomendadas as melhores práticas que especifica as regras mais restritivas em relação à origens em primeiro lugar na lista, para que estes são avaliadas primeiro. Especifique as regras que sejam menos restritivas – por exemplo, uma regra para permitir todas as origens – no final da lista.

### <a name="example--cors-rules-evaluation"></a>Exemplo – avaliação de regras CORS
O exemplo seguinte mostra um corpo de pedido parcial de uma operação definir as regras CORS para os serviços de armazenamento. Ver [definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades do serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx), e [definir propriedades do serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx) para obter detalhes sobre como construir o pedido.

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

Em seguida, considere os seguintes pedidos CORS:

| Pedir |  |  | Resposta |  |
| --- | --- | --- | --- | --- |
| **Método** |**Origem** |**Cabeçalhos dos Pedidos** |**Correspondência de regra** |**Resultado** |
| **PUT** |http://www.contoso.com |x-ms-blob-content-type |Primeira regra |Êxito |
| **GET** |http://www.contoso.com |x-ms-blob-content-type |Segunda regra |Êxito |
| **GET** |http://www.contoso.com |x-ms-client-request-id |Segunda regra |Falha |

A primeira solicitação correspondente à regra primeiro – o domínio de origem corresponde as origens permitidas, o método corresponde os métodos permitidos e o cabeçalho corresponde a cabeçalhos permitidos – e por isso, é bem-sucedida.

A segunda solicitação não corresponde a primeira regra porque o método não coincide com os métodos permitidos. -Lo, no entanto, coincide com a segunda regra, para ter êxito.

O pedido de terceiro corresponde a segunda regra no seu domínio de origem e o método, para que não existem regras adicionais são avaliadas. No entanto, o *cabeçalho x-ms-client-request-id* não é permitida pela regra segundo, para que o pedido falhar, apesar do fato de que a semântica da regra terceiro teria permitido ela tenha êxito.

> [!NOTE]
> Embora este exemplo mostra uma regra de menos restritiva, antes de um mais restritivo, em geral a melhor prática é listar as regras mais restritivas em primeiro lugar.
> 
> 

## <a name="understanding-how-the-vary-header-is-set"></a>Compreender a forma como o cabeçalho de podem variar está definido
O *podem variar* cabeçalho é um padrão cabeçalho HTTP/1.1 consiste num conjunto de campos de cabeçalho de pedido aconselhe o agente de utilizador ou de navegador sobre os critérios que foram selecionados pelo servidor para processar o pedido. O *podem variar* cabeçalho é usado principalmente para colocação em cache por proxies, navegadores e CDNs, o que utilizam para saber como a resposta deve ser colocado em cache. Para obter detalhes, consulte as especificações para o [cabeçalho podem variar](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).

Quando o navegador ou outro agente de utilizador coloca em cache a resposta de um pedido CORS, o domínio de origem é colocado em cache, como a origem permitida. Quando um segundo domínio emite o mesmo pedido para um recurso de armazenamento, enquanto a cache estiver ativa, o agente do usuário obtém o domínio de origem em cache. Segundo domínio não corresponde ao domínio em cache, para que o pedido falha quando-caso contrário, serão bem-sucedidas. Em certos casos, o armazenamento do Azure define o cabeçalho de podem variar **origem** para instruir o agente de utilizador para enviar o pedido CORS subsequente ao serviço quando o domínio solicitante é diferente da origem em cache.

Conjuntos de armazenamento do Azure a *podem variar* cabeçalho para **origem** para pedidos GET/HEAD reais nos seguintes casos:

* Quando a origem do pedido corresponde exatamente a origem permitida definida por uma regra CORS. Para ser uma correspondência exata, a regra CORS não pode incluir um caráter universal "*" caráter.
* Não existe nenhuma regra de correspondência da origem do pedido, mas o CORS está ativado para o serviço de armazenamento.

No caso em que um pedido GET/HEAD corresponde a uma regra CORS que permite que todas as origens, a resposta indica que todas as origens são permitidas e a cache do agente de utilizador irá permitir que os pedidos subsequentes de qualquer domínio de origem enquanto a cache está ativa.

Tenha em atenção que para pedidos através de métodos que não seja GET/HEAD, os serviços de armazenamento não irão definir o cabeçalho de podem variar, uma vez que as respostas para esses métodos não estão em cache pelos agentes do usuário.

A tabela seguinte indica como o Azure storage responderão aos pedidos GET/HEAD com base nos casos de mencionadas anteriormente:

| Pedir | Definição da conta e o resultado da avaliação da regra |  |  | Resposta |  |  |
| --- | --- | --- | --- | --- | --- | --- |
| **Cabeçalho de origem presente no pedido** |**CORS regra (s) especificado para este serviço** |**Existe de regra correspondente que permite que todos os origins(*)** |**Regra correspondente não existe para a correspondência de origem exato** |**A resposta inclui o cabeçalho de podem variar definido para a origem** |**A resposta inclui Access-Control-permitido-Origin: "*"** |**A resposta inclui Access-Control-expostos-Headers** |
| Não |Não |Não |Não |Não |Não |Não |
| Não |Sim |Não |Não |Sim |Não |Não |
| Não |Sim |Sim |Não |Não |Sim |Sim |
| Sim |Não |Não |Não |Não |Não |Não |
| Sim |Sim |Não |Sim |Sim |Não |Sim |
| Sim |Sim |Não |Não |Sim |Não |Não |
| Sim |Sim |Sim |Não |Não |Sim |Sim |

## <a name="billing-for-cors-requests"></a>Faturação de solicitações CORS
Simulação com êxito pedidos são faturadas se tiver ativado o CORS para qualquer um dos serviços de armazenamento para a sua conta (chamando [definir propriedades do serviço Blob](https://msdn.microsoft.com/library/hh452235.aspx), [definir propriedades do serviço de fila](https://msdn.microsoft.com/library/hh452232.aspx), ou [Definir as propriedades do serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx)). Para minimizar os custos, considere definir o **MaxAgeInSeconds** elemento no seu CORS de regras para um grande valor, de modo a que o agente de utilizador armazena em cache o pedido.

Pedidos sem êxito de simulação não serão cobrados.

## <a name="next-steps"></a>Passos Seguintes
[Definir as propriedades do serviço de BLOBs](https://msdn.microsoft.com/library/hh452235.aspx)

[Definir as propriedades do serviço fila](https://msdn.microsoft.com/library/hh452232.aspx)

[Definir as propriedades do serviço de tabela](https://msdn.microsoft.com/library/hh452240.aspx)

[Especificação de partilha de recursos de várias origens de W3C](https://www.w3.org/TR/cors/)

