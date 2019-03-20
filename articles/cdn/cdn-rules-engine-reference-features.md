---
title: Funcionalidades do motor de regras CDN do Azure | Documentos da Microsoft
description: Funcionalidades do motor de regras de documentação de referência para a CDN do Azure.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 205a8dae55394a82a60f54ed32bad95324a59517
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57996939"
---
# <a name="azure-cdn-rules-engine-features"></a>Funcionalidades do motor de regras CDN do Azure
Este artigo apresenta uma lista de descrições detalhadas dos recursos disponíveis para a rede de entrega de conteúdos (CDN) do Azure [motor de regras](cdn-rules-engine.md).

A terceira parte de uma regra é o recurso. Um recurso define o tipo de ação que é aplicada para o tipo de pedido que é identificado por um conjunto de condições de correspondência.

## <a name="access-features"></a>Funcionalidades de acesso

Esses recursos foram desenvolvidos para controlar o acesso ao conteúdo.

Name | Objetivo
-----|--------
[Negar o acesso (403)](#deny-access-403) | Determina se todos os pedidos são rejeitados com uma resposta em 403 Proibido.
[Token Auth](#token-auth) | Determina se a autenticação baseada em tokens é aplicada a um pedido.
[Código de negação de token de autenticação](#token-auth-denial-code) | Determina o tipo de resposta que é retornado para um utilizador quando um pedido é negado devido à autenticação baseada em tokens.
[Autenticação de token ignorar URL maiúsculas / minúsculas](#token-auth-ignore-url-case) | Determina se as comparações de URL feitas pela autenticação baseada em tokens são maiúsculas e minúsculas.
[Token de autenticação de parâmetro](#token-auth-parameter) | Determina se o parâmetro de cadeia de caracteres de consulta baseada em tokens de autenticação deve ser renomeado.


## <a name="caching-features"></a>Recursos de cache

Esses recursos foram desenvolvidos para personalizar a quando e como conteúdo é colocado em cache.

Name | Objetivo
-----|--------
[Parâmetros de largura de banda](#bandwidth-parameters) | Determina se os parâmetros de limitação de largura de banda (por exemplo, ec_rate e ec_prebuf) são Active Directory.
[Limitação de largura de banda](#bandwidth-throttling) | Limita a largura de banda para a resposta fornecida pela ponto de presença (POP).
[Ignorar Cache](#bypass-cache) | Determina se deve ignorar o pedido de colocação em cache.
[Tratamento de cabeçalho Cache-Control](#cache-control-header-treatment) | Controla a geração de `Cache-Control` cabeçalhos por POP quando a funcionalidade de Max-Age externo está ativa.
[Cadeia de consulta de chave de cache](#cache-key-query-string) | Determina se a chave de cache inclui ou exclui os parâmetros de cadeia de caracteres de consulta associados a um pedido.
[Reescrita de chave de cache](#cache-key-rewrite) | Reescreve a chave de cache associada a um pedido.
[Concluir o preenchimento de Cache](#complete-cache-fill) | Determina o que acontece quando um resultados do pedido numa falha de acerto na cache parcial num POP.
[Comprimir os tipos de ficheiro](#compress-file-types) | Define os formatos de arquivo para os ficheiros que são compactados no servidor.
[Predefinição interna Max-Age](#default-internal-max-age) | Determina o intervalo de idade máxima de predefinido para POP a revalidação de cache do servidor de origem.
[Expira o tratamento de cabeçalho](#expires-header-treatment) | Controla a geração de `Expires` cabeçalhos por um preenchimento de quando a funcionalidade de Max-Age externo está ativa.
[Duração máxima externa](#external-max-age) | Determina o intervalo de duração máxima para o navegador a revalidação de cache POP.
[Forçar a duração máxima interna](#force-internal-max-age) | Determina o intervalo de duração máxima para POP a revalidação de cache do servidor de origem.
[Suporte H.264 (transferência progressiva de HTTP)](#h264-support-http-progressive-download) | Determina os tipos de formatos de arquivo H.264, que podem ser utilizados para transmitir o conteúdo.
[Pedido de não-Cache honor](#honor-no-cache-request) | Determina se as solicitações de não-cache de um cliente HTTP são encaminhadas para o servidor de origem.
[Ignorar a origem não-Cache](#ignore-origin-no-cache) | Determina se a CDN ignora determinadas diretivas atendidas a partir de um servidor de origem.
[Ignorar Unsatisfiable intervalos](#ignore-unsatisfiable-ranges) | Determina a resposta que é devolvida para clientes quando uma solicitação gerar um código de estado de pedidos gama não Satisfiable 416.
[Max-obsoleta interno](#internal-max-stale) | Controles quanto posterior à hora de expiração normal um ativo em cache pode ser servido a partir de um preenchimento quando o POP não consegue revalide o elemento em cache com o servidor de origem.
[Cache parcial de partilha](#partial-cache-sharing) | Determina se um pedido pode gerar conteúdo parcialmente em cache.
[Prevalidate conteúdo em cache](#prevalidate-cached-content) | Determina se os conteúdos em cache é elegível para revalidação antecipada antes de seu valor de TTL expire.
[Atualizar ficheiros de Cache de Zero bytes](#refresh-zero-byte-cache-files) | Determina como o pedido de um cliente HTTP para um recurso de cache de 0 bytes é processado pelos POPs.
[Defina códigos de estado em cache](#set-cacheable-status-codes) | Define o conjunto de códigos de estado que pode resultar em conteúdo em cache.
[Entrega de conteúdos obsoleta com o erro](#stale-content-delivery-on-error) | Determina se a expirou em cache o conteúdo é entregue quando ocorre um erro durante a revalidação de cache ou ao obter os conteúdos solicitados a partir do servidor de origem do cliente.
[Obsoletos ao Revalide](#stale-while-revalidate) | Melhora o desempenho ao permitir que os POPs servir de cliente obsoleto ao solicitante enquanto revalidação está em curso.

## <a name="comment-feature"></a>Funcionalidade de comentário

Esta funcionalidade foi concebida para fornecer informações adicionais dentro de uma regra.

Name | Objetivo
-----|--------
[Comentário](#comment) | Permite que uma nota a ser adicionados dentro de uma regra.
 
## <a name="header-features"></a>Funcionalidades de cabeçalho

Esses recursos foram desenvolvidos para adicionar, modificar ou eliminar cabeçalhos da solicitação ou resposta.

Name | Objetivo
-----|--------
[Cabeçalho de resposta de idade](#age-response-header) | Determina se um cabeçalho de resposta de idade está incluído na resposta enviada para o autor do pedido.
[Depurar os cabeçalhos de resposta de Cache](#debug-cache-response-headers) | Determina se uma resposta pode incluir o cabeçalho de resposta de EC-X-Debug, que fornece informações sobre a política de cache para o recurso solicitado.
[Modificar o cabeçalho de pedido do cliente](#modify-client-request-header) | Substitui, acrescenta ou elimina um cabeçalho de uma solicitação.
[Modificar o cabeçalho de resposta do cliente](#modify-client-response-header) | Substitui, acrescenta ou elimina um cabeçalho de uma resposta.
[Conjunto IP de cliente cabeçalho personalizado](#set-client-ip-custom-header) | Permite que o endereço IP do cliente solicitante a ser adicionado ao pedido como um cabeçalho de pedido personalizado.


## <a name="logging-features"></a>Funcionalidades de registo

Esses recursos foram desenvolvidos para personalizar os dados armazenados nos ficheiros de registo não processados.

Name | Objetivo
-----|--------
[Campo de registo personalizado 1](#custom-log-field-1) | Determina o formato e o conteúdo que é atribuído para o campo de registo personalizado num ficheiro de registo não processados.
[Cadeia de consulta de registo](#log-query-string) | Determina se uma cadeia de consulta é armazenada, juntamente com o URL nos registos de acesso.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Recursos de origem

Esses recursos foram desenvolvidos para controlar a forma como a CDN se comunica com um servidor de origem.

Name | Objetivo
-----|--------
[Pedidos de ligação Keep-Alive máximos](#maximum-keep-alive-requests) | Define o número máximo de pedidos para uma ligação Keep-Alive antes de ser fechado.
[Cabeçalhos de especiais de proxy](#proxy-special-headers) | Define o conjunto de cabeçalhos de pedido de CDN específicas que são reencaminhados a partir de um preenchimento de para um servidor de origem.


## <a name="specialty-features"></a>Funcionalidades de especialidade

Esses recursos oferecem uma funcionalidade avançada para utilizadores avançados.

Name | Objetivo
-----|--------
[Métodos HTTP colocáveis em cache](#cacheable-http-methods) | Determina o conjunto de métodos HTTP adicionais que podem ser colocadas em cache na rede.
[Tamanho do corpo de pedido colocáveis em cache](#cacheable-request-body-size) | Define o limiar para determinar se uma resposta de POSTAGEM pode ser colocado em cache.
[Variável de utilizador](#user-variable) | Apenas para utilização interna.

 
## <a name="url-features"></a>Funcionalidades de URL

Esses recursos permitem que um pedido para ser redirecionado ou reescrito para um URL diferente.

Name | Objetivo
-----|--------
[Seguir redirecionamentos](#follow-redirects) | Determina se os pedidos podem ser redirecionados para o nome de anfitrião definida no cabeçalho de localização devolvido por um servidor de origem do cliente.
[Redirecionamento de URL](#url-redirect) | Redireciona os pedidos com o cabeçalho de localização.
[Reescrever URL](#url-rewrite)  | Reescreve o URL do pedido.



## <a name="azure-cdn-rules-engine-features-reference"></a>Referência de funcionalidades do motor de regras CDN do Azure

---
### <a name="age-response-header"></a>Cabeçalho de resposta de idade
**Objetivo**: Determina se um cabeçalho de resposta de idade está incluído na resposta enviada para o autor do pedido.

Value|Resultado
--|--
Ativado | O cabeçalho de resposta de idade está incluído na resposta enviada para o autor do pedido.
Desativado | O cabeçalho de resposta de idade está excluído da resposta enviada para o autor do pedido.

**Predefinição de comportamento**: Desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

<br>

---
### <a name="bandwidth-parameters"></a>Parâmetros de largura de banda
**Objetivo:** determina se os parâmetros de limitação de largura de banda (por exemplo, ec_rate e ec_prebuf) são Active Directory.

Parâmetros de limitação de largura de banda determinam se a taxa de transferência de dados para solicitação de um cliente estão limitadas a uma taxa personalizada.

Value|Resultado
--|--
Ativado|Permite que os POPs que respeite os pedidos de limitação de largura de banda.
Desativado|Faz com que os POPs ignorar os parâmetros de limitação de largura de banda. O conteúdo solicitado é atendido normalmente (ou seja, sem limitação de largura de banda).

**Comportamento predefinido:** ativada.
 
[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Limitação de largura de banda
**Objetivo:** regula a largura de banda para a resposta fornecida pelos POPs.

Ambas as opções seguintes devem ser definidas para configurar corretamente a limitação de largura de banda.

Opção|Descrição
--|--
KBytes por segundo|Defina esta opção para a largura de banda máxima (Kb por segundo) que pode ser utilizada para disponibilizar a resposta.
Segundos de Prebuf|Defina esta opção para o número de segundos para os POPs aguardar até que a largura de banda é limitada. O objetivo deste período de tempo de largura de banda sem restrições é impedir que um leitor de multimédia a ter problemas de armazenamento em buffer ou gagueira devido à limitação de largura de banda.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Ignorar Cache
**Objetivo:** determina se deve ignorar o pedido de colocação em cache.

Value|Resultado
--|--
Ativado|Faz com que todos os pedidos para enquadram-se por meio de para o servidor de origem, mesmo que o conteúdo foi colocado em cache anteriormente no POPs.
Desativado|Faz com que POPs para cache recursos de acordo com a política de cache definida no respetivos cabeçalhos de resposta.

**Comportamento predefinido:**

- **HTTP grandes:** desativado

<!---
- **ADN:** Enabled

--->

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Métodos HTTP colocáveis em cache
**Objetivo:** determina o conjunto de métodos HTTP adicionais que podem ser colocadas em cache na rede.

Informações da chave:

- Esta funcionalidade parte do princípio de que as respostas GET devem sempre ser colocado em cache. Como resultado, o método GET HTTP não deve ser incluído durante a configuração desta funcionalidade.
- Esta funcionalidade suporta apenas o método POST HTTP. Ativar a cache de resposta de POSTAGEM ao definir esta funcionalidade `POST`.
- Por predefinição, são colocadas em cache apenas os pedidos que tem o corpo é menor do que 14 Kb. Utilize a funcionalidade de tamanho de corpo de pedido colocáveis em cache para definir o tamanho máximo do pedido do corpo.

**Comportamento predefinido:** apenas GET respostas são colocadas em cache.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Tamanho do corpo de pedido colocáveis em cache
**Objetivo:** define o limiar para determinar se uma resposta de POSTAGEM pode ser colocado em cache.

Este limiar é determinado ao especificar um tamanho máximo do pedido do corpo. Pedidos que contêm um corpo de pedido maior não são colocadas em cache.

Informações da chave:

- Esta funcionalidade só é aplicável quando as respostas de POSTAGEM são elegíveis para a colocação em cache. Utilize a funcionalidade de métodos HTTP colocáveis em cache para ativar a colocação em cache de pedido POST.
- O corpo do pedido é levado em consideração para:
    - valores de x-www-form-urlencoded
    - Garantindo uma chave exclusiva de cache
- Definir um tamanho de corpo de pedido máximo grandes pode afetar o desempenho de entrega de dados.
    - **Valor recomendado:** 14 Kb
    - **Valor mínimo:** 1 Kb

**Comportamento predefinido:** 14 Kb

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Tratamento de cabeçalho Cache-Control
**Objetivo:** controla a geração de `Cache-Control` cabeçalhos por POP quando a funcionalidade de Max-Age externo está ativa.

A maneira mais fácil de obter este tipo de configuração é colocar o Max-Age externos e os recursos de tratamento de cabeçalho Cache-Control na mesma instrução.

Value|Resultado
--|--
Substituir|Garante que as ações seguintes ocorrem:<br/> -Substitui a `Cache-Control` cabeçalho gerado pelo servidor de origem. <br/>-Adiciona a `Cache-Control` cabeçalho produzidos pelo recurso externo Max-Age na resposta.
Pass Through|Garante que o `Cache-Control` cabeçalho produzido pelo recurso externo Max-Age nunca é adicionado à resposta. <br/> Se o servidor de origem produz uma `Cache-Control` cabeçalho, que passa para o utilizador final. <br/> Se o servidor de origem não produza um `Cache-Control` cabeçalho, então, esta opção pode fazer com que o cabeçalho de resposta a que não contenha um `Cache-Control` cabeçalho.
Adicionar se estiver em falta|Se um `Cache-Control` cabeçalho não foi recebido do servidor de origem, em seguida, esta opção acrescenta a `Cache-Control` cabeçalho produzido pela funcionalidade de Max-Age externo. Esta opção é útil para assegurar que todos os recursos são atribuídos um `Cache-Control` cabeçalho.
Remover| Esta opção garante que uma `Cache-Control` cabeçalho não está incluído com a resposta de cabeçalho. Se um `Cache-Control` cabeçalho já foi atribuído, em seguida, é removido da resposta de cabeçalho.

**Comportamento predefinido:** substituir.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Cadeia de consulta de chave de cache
**Objetivo:** determina se a chave de cache inclui ou exclui os parâmetros de cadeia de caracteres de consulta associados a um pedido.

Informações da chave:

- Especifique um ou mais nomes de parâmetro de cadeia de caracteres de consulta e separe cada nome de parâmetro com um único espaço.
- Esta funcionalidade determina se os parâmetros de cadeia de caracteres de consulta estão incluídos ou excluídos da chave de cache. São fornecidas informações adicionais para cada opção na tabela seguinte.

Type|Descrição
--|--
 Incluir|  Indica que cada parâmetro especificado deve ser incluído na chave do cache. Uma chave exclusiva de cache é gerada para cada pedido que contém um valor exclusivo para um parâmetro de cadeia de caracteres de consulta definido neste recurso. 
 Incluir todos  |Indica que é criada uma chave de cache exclusiva para cada solicitação para um elemento que inclui uma cadeia de consulta exclusivo. Este tipo de configuração não é normalmente recomendado porque ele pode levar a uma pequena porcentagem de acertos na cache. Um número baixo de acertos na cache aumenta a carga no servidor de origem, uma vez que ele deve atender a solicitações de mais. Esta configuração duplica o comportamento de colocação em cache, conhecido como "-cache exclusivo" na página de colocação em cache de cadeia de consulta. 
 Excluir | Indica que apenas o parâmetro ou parâmetros especificado está excluído da chave de cache. Todos os outros parâmetros de cadeia de caracteres de consulta estão incluídos na chave do cache. 
 Excluir todos os  |Indica que todos os parâmetros de cadeia de caracteres de consulta são excluídos da chave de cache. Esta configuração duplica o comportamento na página de colocação em cache de cadeia de consulta de cache do padrão de "standard – cache".  

O mecanismo de regras permite-lhe personalizar a forma em que o cache de cadeia de caracteres de consulta é implementado. Por exemplo, pode especificar que a colocação em cache de cadeia de caracteres de consulta é executada apenas em determinadas localizações ou tipos de ficheiro.

Para duplicar a cadeia de consulta de "no-cache" comportamento na página de colocação em cache de cadeia de consulta de colocação em cache, crie uma regra que contém uma condição de correspondência de caráter universal de consulta de URL e uma funcionalidade de ignorar a Cache. Defina a condição de correspondência de caráter universal de consulta de URL para um asterisco (*).

>[!IMPORTANT] 
> Se a autorização de token está ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que pode ser utilizado para colocar em cache de cadeia de caracteres de consulta. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

#### <a name="sample-scenarios"></a>Cenários de exemplo

A utilização de exemplo seguinte para esta funcionalidade fornece um pedido de exemplo e a chave de cache padrão:

- **Pedido de exemplo:** http://wpc.0001.&lt; domínio&gt;/800001/Origin/folder/asset.htm?sessionid=1234 & idioma = EN & userid = 01
- **Padrão cache-chave:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Incluir

Configuração de exemplo:

- **Tipo:** incluem
- **Parâmetro (s):** idioma

Este tipo de configuração irá gerar a seguinte cadeia de caracteres parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Incluir todos

Configuração de exemplo:

- **Tipo:** incluir todos

Este tipo de configuração irá gerar a seguinte cadeia de caracteres parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Excluir

Configuração de exemplo:

- **Tipo:** excluir
- **Parâmetro (s):** sessionid ID de utilizador

Este tipo de configuração irá gerar a seguinte cadeia de caracteres parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Excluir todos os

Configuração de exemplo:

- **Tipo:** excluir todos os

Este tipo de configuração irá gerar a seguinte cadeia de caracteres parâmetro cache-chave de consulta:

    /800001/Origin/folder/asset.htm

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Reescrita de chave de cache
**Objetivo:** reescreve a chave de cache associada a um pedido.

Uma chave de cache é o caminho relativo que identifica um recurso para fins de colocação em cache. Em outras palavras, os servidores de verificação para uma versão em cache de um ativo, de acordo com seu caminho conforme definido pela respetiva chave de cache.

Configure esse recurso com a definição ambas as opções seguintes:

Opção|Descrição
--|--
Caminho original| Defina o caminho relativo para os tipos de pedidos cuja chave de cache é reescrito. Pode ser definido um caminho relativo ao selecionar um caminho de base de origem e, em seguida, definir um padrão de expressão regular.
Novo caminho|Defina o caminho relativo para a nova chave de cache. Pode ser definido um caminho relativo ao selecionar um caminho de base de origem e, em seguida, definir um padrão de expressão regular. Este caminho relativo que pode ser construído dinamicamente através da utilização de [variáveis HTTP](cdn-http-variables.md).

**Comportamento predefinido:** chave um pedido de cache é determinado pelo URI do pedido.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Comentário
**Objetivo:** permite uma nota a ser adicionados dentro de uma regra.

Um uso para esta funcionalidade é fornecer informações adicionais sobre o objetivo geral de uma regra ou por que motivo uma determinada condição de correspondência ou recurso foi adicionado à regra.

Informações da chave:

- Pode ser especificado um máximo de 150 carateres.
- Utilize apenas caracteres alfanuméricos.
- Esta funcionalidade não afeta o comportamento da regra. Destina-se apenas para fornecer uma área em que pode fornecer informações para referência futura, ou que podem ajudar quando a regra de resolução de problemas.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Concluir o preenchimento de Cache
**Objetivo:** determina o que acontece quando um pedido resulta numa falha de acerto na cache parcial num POP.

Uma falha de acerto na cache parcial descreve o estado da cache para um recurso que não foi completamente baixado para um POP. Se um recurso é apenas parcialmente em cache num POP, em seguida, a próxima solicitação para esse recurso será reencaminhada novamente para o servidor de origem.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Uma falha de acerto na cache parcial ocorre normalmente depois de um utilizador for anulada um download ou para ativos que são exclusivamente pedidos utilizando HTTP solicitações na faixa. Esta funcionalidade é mais útil para grandes ativos que normalmente não são transferidos do início ao fim (por exemplo, vídeos). Como resultado, esta funcionalidade está ativada por predefinição na plataforma HTTP grandes. Ela é desabilitada em todas as outras plataformas.

Mantenha a configuração predefinida para a plataforma HTTP grandes, pois ele reduz a carga no seu servidor de origem do cliente e aumenta a velocidade a que os seus clientes transferem o seu conteúdo.

Value|Resultado
--|--
Ativado|Restaura o comportamento padrão. O comportamento padrão é forçar o POP para iniciar uma obtenção de plano de fundo do elemento do servidor de origem. Depois disso, o elemento será na cache local do POP.
Desativado|Impede que um preenchimento de realizar uma busca em segundo plano para o elemento. O resultado é que a próxima solicitação para esse elemento em que região faz com que um preenchimento de solicitá-la a partir do servidor de origem do cliente.

**Comportamento predefinido:** ativada.

#### <a name="compatibility"></a>Compatibilidade
Devido a da maneira na qual cache definições são controladas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
- COMO número
- Endereço IP do cliente
- Parâmetro de cookie
- Cookie parâmetro Regex
- País
- Dispositivo
- Edge Cname
- Domínio de referência
- Literal de cabeçalho do pedido
- Regex de cabeçalho do pedido
- Caráter universal de cabeçalho do pedido
- Método de pedido
- Esquema de pedido
- Literal de consulta de URL
- Regex de consulta de URL
- Caráter universal de consulta de URL
- Parâmetro de consulta de URL

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Comprimir os tipos de ficheiro
**Objetivo:** define os formatos de arquivo para os ficheiros que são compactados no servidor.

Um formato de arquivo pode ser especificado utilizando o respetivo tipo de suporte de Internet (por exemplo, tipo de conteúdo). Tipo de suporte de Internet é metadados independente de plataforma que permite que os servidores para identificar o formato de ficheiro de um determinado ativo. É fornecida uma lista dos tipos de suportes de dados do common Internet abaixo.

Tipo de suporte de Internet|Descrição
--|--
text/plain|Ficheiros de texto sem formatação
text/html| Ficheiros HTML
text/css|Folhas de estilo em cascata (CSS)
application/x-javascript|Javascript
application/javascript|Javascript

Informações da chave:

- Especifique vários tipos de suportes de dados de Internet por cada um com um único espaço de delimitação. 
- Esta funcionalidade só compacta ativos cujo tamanho é inferior a 1 MB. Ativos maiores não são compactados pelos servidores.
- Determinados tipos de conteúdo, como imagens, vídeo e ativos de mídia de áudio (por exemplo, JPG, MP3, MP4, etc.), já estão comprimidos. Porque compressão adicional sobre estes tipos de ativos não diminui significativamente o tamanho de ficheiro, é recomendável que não habilitar a compactação nos mesmos.
- Carateres universais, tais como asteriscos, não são suportadas.
- Antes de adicionar esta funcionalidade a uma regra, certifique-se de que defina a opção de compressão desativada na página de compactação para a plataforma à qual esta regra é aplicada.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Campo de registo personalizado 1
**Objetivo:** determina o formato e o conteúdo que será atribuído para o campo de registo personalizado num ficheiro de registo não processados.

Este campo personalizado permite-lhe determinar quais valores de cabeçalho do pedido e resposta são armazenados nos seus ficheiros de registo.

Por predefinição, o campo de registo personalizado é chamado de "x-ec_custom-1." O nome deste campo pode ser personalizado a partir da página de definições de registos não processados.

O formato para especificar cabeçalhos de solicitação e resposta é definido da seguinte forma:

Tipo de cabeçalho|Formato|Exemplos
-|-|-
Cabeçalho do pedido|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
Cabeçalho de Resposta|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informações da chave:

- Um campo de registo personalizado pode conter qualquer combinação de campos de cabeçalho e de texto sem formatação.
- Carateres válidos para este campo são os seguintes: alfanumérica (0 a 9, a-z e A-Z), travessões, vírgula, ponto e vírgula, apóstrofos, vírgulas, períodos, carateres de sublinhado, sinais de igual, parênteses, colchetes e espaços. O símbolo de percentagem e chaves e só são permitidas quando utilizado para especificar um campo de cabeçalho.
- A ortografia de cada campo de cabeçalho especificado tem de corresponder ao nome de cabeçalho de solicitação/resposta desejado.
- Se pretender especificar vários cabeçalhos, utilize um separador para indicar cada cabeçalho. Por exemplo, poderia usar uma abreviatura para cada cabeçalho:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Valor predefinido:** -

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Depurar os cabeçalhos de resposta de Cache
**Objetivo:** determina se uma resposta pode incluir [cabeçalhos de resposta de EC-X-Debug](cdn-http-debug-headers.md), que fornece informações sobre a política de cache para o recurso solicitado.

Depure a resposta de cache cabeçalhos serão incluídos na resposta quando os seguintes elementos forem verdadeiros:

- A funcionalidade de depurar cabeçalhos de resposta de Cache foi ativada no pedido especificado.
- O pedido especificado define o conjunto de cabeçalhos de resposta de cache de depuração que será incluído na resposta.

Depure cabeçalhos podem ser solicitados, incluindo o seguinte cabeçalho e as diretivas especificadas no pedido de resposta de cache:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Exemplo:**

EC-X-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Value|Resultado
-|-
Ativado|Pedidos para cabeçalhos de resposta de cache de depuração irão devolver uma resposta que inclui o cabeçalho X-EC-Debug.
Desativado|O cabeçalho de resposta de EC-X-Debug será excluído da resposta.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Predefinição interna Max-Age
**Objetivo:** determina o intervalo de idade máxima de predefinido para POP a revalidação de cache do servidor de origem. Em outras palavras, a quantidade de tempo passará até um POP irá verificar se um recurso em cache coincide com o elemento armazenado no servidor de origem.

Informações da chave:

- Esta ação só terá lugar para respostas de um servidor de origem que não atribuiu uma indicação de max-age na `Cache-Control` ou `Expires` cabeçalho.
- Esta ação não terá lugar para recursos que não são considerados colocáveis em cache.
- Esta ação não afeta o browser para revalidations de cache POP. Esses tipos de revalidations são determinados pela `Cache-Control` ou `Expires` cabeçalhos enviados para o browser, que pode ser personalizado com a funcionalidade de Max-Age externo.
- Os resultados desta ação não tem um efeito observable em cabeçalhos de resposta e o conteúdo retornado de POPs para o seu conteúdo, mas pode ter um efeito na quantidade de tráfego de revalidação enviado do POPs para o servidor de origem.
- Configure esse recurso por:
    - Selecionar o código de estado para os quais pode ser aplicada uma padrão interno duração máxima.
    - Especificar um valor inteiro e, em seguida, selecionar a unidade de tempo desejado (por exemplo, segundos, minutos, horas, etc.). Este valor define o intervalo de idade máxima interno do predefinido.

- Definir a unidade de tempo como "Desligado" atribuirá um intervalo de idade máxima interno do padrão de 7 dias para pedidos que não foram atribuídas uma indicação de idade máxima em seus `Cache-Control` ou `Expires` cabeçalho.

**Valor predefinido:** 7 dias

#### <a name="compatibility"></a>Compatibilidade
Devido a da maneira na qual cache definições são controladas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
- COMO número
- Endereço IP do cliente
- Parâmetro de cookie
- Cookie parâmetro Regex
- País
- Dispositivo
- Edge Cname
- Domínio de referência
- Literal de cabeçalho do pedido
- Regex de cabeçalho do pedido
- Caráter universal de cabeçalho do pedido
- Método de pedido
- Esquema de pedido
- Literal de consulta de URL
- Regex de consulta de URL
- Caráter universal de consulta de URL
- Parâmetro de consulta de URL

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Negar o acesso (403)
**Objetivo**: Determina se todos os pedidos são rejeitados com uma resposta em 403 Proibido.

Value | Resultado
------|-------
Ativado| Faz com que todos os pedidos que satisfazem os critérios de correspondência para ser rejeitada com uma resposta de proibido 403.
Desativado| Restaura o comportamento padrão. O comportamento padrão é permitir que o servidor de origem para determinar o tipo de resposta que vai ser devolvido.

**Predefinição de comportamento**: Desativado

> [!TIP]
   > É um uso possível para esta funcionalidade para associá-lo com uma condição de correspondência de cabeçalho do pedido para bloquear o acesso a Referenciadores HTTP que estão a utilizar ligações inline ao seu conteúdo.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Expira o tratamento de cabeçalho
**Objetivo:** controla a geração de `Expires` cabeçalhos por um preenchimento de quando a funcionalidade de Max-Age externo está ativa.

A maneira mais fácil de obter este tipo de configuração é colocar Max-Age externos e os recursos de tratamento de cabeçalho expira na mesma instrução.

Value|Resultado
--|--
Substituir|Garante que as seguintes ações, terá lugar:<br/>-Substitui a `Expires` cabeçalho gerado pelo servidor de origem.<br/>-Adiciona a `Expires` cabeçalho produzidos pelo recurso externo Max-Age na resposta.
Pass Through|Garante que o `Expires` cabeçalho produzido pelo recurso externo Max-Age nunca é adicionado à resposta. <br/> Se o servidor de origem produz uma `Expires` cabeçalho, ele passará para o utilizador final. <br/>Se o servidor de origem não produza um `Expires` cabeçalho, então, esta opção pode fazer com que o cabeçalho de resposta a que não contenha um `Expires` cabeçalho.
Adicionar se estiver em falta| Se um `Expires` cabeçalho não foi recebido do servidor de origem, em seguida, esta opção acrescenta a `Expires` cabeçalho produzido pela funcionalidade de Max-Age externo. Esta opção é útil para assegurar que todos os ativos seja atribuídos uma `Expires` cabeçalho.
Remover| Garante que uma `Expires` cabeçalho não está incluído com a resposta de cabeçalho. Se um `Expires` cabeçalho já foi atribuído, em seguida, é removido da resposta de cabeçalho.

**Comportamento predefinido:** substituir

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Duração máxima externa
**Objetivo:** determina o intervalo de duração máxima para o navegador a revalidação de cache POP. Em outras palavras, a quantidade de tempo passará até um navegador pode verificar a existência de uma nova versão de um ativo de um POP.

Ativar esta funcionalidade irá gerar `Cache-Control: max-age` e `Expires` cabeçalhos dos POPs e enviá-los para o cliente HTTP. Por predefinição, esses cabeçalhos irão substituir esses cabeçalhos criados pelo servidor de origem. No entanto, o tratamento de cabeçalho Cache-Control e os recursos de tratamento de cabeçalho expira podem ser utilizados para alterar este comportamento.

Informações da chave:

- Esta ação não afeta POP para revalidations de cache do servidor de origem. Esses tipos de revalidations são determinados pela `Cache-Control` e `Expires`  cabeçalhos recebido do servidor de origem e pode ser personalizados com o padrão interno Max-Age e as funcionalidades de força interno Max-Age.
- Configure esta funcionalidade ao especificar um valor inteiro e selecionar a unidade de tempo desejado (por exemplo, segundos, minutos, horas, etc.).
- Esse recurso na definição de um valor negativo faz com que os POPs enviar um `Cache-Control: no-cache` e uma `Expires` hora em que está definida no passado com cada resposta no browser. Embora um cliente HTTP não colocarão em cache a resposta, esta definição não irá afetar a capacidade dos POPs para colocar em cache a resposta do servidor de origem.
- Definir a unidade de tempo como "Desligado" desativa esta funcionalidade. O `Cache-Control` e `Expires`  cabeçalhos em cache com a resposta do servidor de origem passará para o navegador.

**Comportamento predefinido:** desativado

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Seguir redirecionamentos
**Objetivo:** determina se os pedidos podem ser redirecionados para o nome de anfitrião definida no cabeçalho de localização devolvido por um servidor de origem do cliente.

Informações da chave:

- Pedidos só podem ser redirecionados para o edge CNAMEs que correspondem a mesma plataforma.

Value|Resultado
-|-
Ativado|Pedidos podem ser redirecionados.
Desativado|Pedidos não serão redirecionados.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Forçar a duração máxima interna
**Objetivo:** determina o intervalo de duração máxima para POP a revalidação de cache do servidor de origem. Em outras palavras, a quantidade de tempo passará até um preenchimento pode verificar se um recurso em cache coincide com o elemento armazenado no servidor de origem.

Informações da chave:

- Esta funcionalidade irá substituir o intervalo de idade máxima definido na `Cache-Control` ou `Expires` cabeçalhos gerados a partir de um servidor de origem.
- Esta funcionalidade não afeta o browser para revalidations de cache POP. Esses tipos de revalidations são determinados pela `Cache-Control` ou `Expires` cabeçalhos enviados para o navegador.
- Esta funcionalidade não tem um efeito observable na resposta entregue por um preenchimento para o autor do pedido. No entanto, pode ter um efeito na quantidade de tráfego de revalidação enviado dos POPs para o servidor de origem.
- Configure esse recurso por:
    - Selecionar o código de estado para os quais será aplicada a uma duração máxima interna.
    - Especificar um valor inteiro e selecionar a unidade de tempo desejado (por exemplo, segundos, minutos, horas, etc.). Este valor define o intervalo de idade máxima da solicitação.

- Definir a unidade de tempo como "Desligado" desativa esta funcionalidade. Um intervalo de idade máxima interno não será atribuído a recursos de pedido. Se o cabeçalho original não contém instruções de colocação em cache, em seguida, o recurso ficará em cache, de acordo com a definição de Active Directory, a funcionalidade de predefinida interno Max-Age.

**Comportamento predefinido:** desativado

#### <a name="compatibility"></a>Compatibilidade
Devido a da maneira na qual cache definições são controladas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
- COMO número
- Endereço IP do cliente
- Parâmetro de cookie
- Cookie parâmetro Regex
- País
- Dispositivo
- Edge Cname
- Domínio de referência
- Literal de cabeçalho do pedido
- Regex de cabeçalho do pedido
- Caráter universal de cabeçalho do pedido
- Método de pedido
- Esquema de pedido
- Literal de consulta de URL
- Regex de consulta de URL
- Caráter universal de consulta de URL
- Parâmetro de consulta de URL

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>Suporte H.264 (transferência progressiva de HTTP)
**Objetivo:** determina os tipos de formatos de arquivo H.264, que podem ser utilizados para transmitir o conteúdo.

Informações da chave:

- Defina um conjunto de delimitada por espaço de mensagens em fila de extensões de nome de ficheiro permitidas H.264 na opção de extensões de ficheiro. A opção de extensões de ficheiro irá substituir o comportamento predefinido. Manter o suporte de MP4 e F4V, incluindo essas extensões de nome de ficheiro ao definir esta opção. 
- Incluir um período quando especificar cada extensão de nome de ficheiro (por exemplo, _mp4_, _.f4v_).

**Comportamento predefinido:** Download progressivo do HTTP suporta multimédia MP4 e F4V por predefinição.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Pedido de não-Cache honor
**Objetivo:** determina se de um cliente HTTP não-cache pedidos serão reencaminhados para o servidor de origem.

Um pedido de cache não ocorre quando o cliente HTTP envia um `Cache-Control: no-cache` e/ou `Pragma: no-cache` cabeçalho na solicitação HTTP.

Value|Resultado
--|--
Ativado|Permite que os pedidos de não-cache de um cliente HTTP para serem reencaminhados para o servidor de origem e o servidor de origem irá devolver os cabeçalhos de resposta e o corpo por meio do POP de volta para o cliente HTTP.
Desativado|Restaura o comportamento padrão. O comportamento padrão é impedir que os pedidos de cache não sejam encaminhados para o servidor de origem.

Para todo o tráfego de produção, recomendamos deixar esse recurso em seu estado padrão desativada. Caso contrário, não serão possível proteger servidores de origem dos utilizadores finais que inadvertidamente poderão acionar o número de pedidos não-cache durante a atualização de páginas da web ou a partir de muitos reprodutores de mídia populares, que estão codificadas para enviar um cabeçalho de cache não com cada solicitação de vídeo. No entanto, esta funcionalidade pode ser útil para aplicar a determinados não seja de produção de teste ou de teste de diretórios, para permitir que o conteúdo novo sob demanda obtidas a partir do servidor de origem.

O estado da cache que é comunicado para um pedido que pode ser reencaminhado para um servidor de origem devido a esta funcionalidade é `TCP_Client_Refresh_Miss`. O relatório de Estados de Cache, o que está disponível no Core reporting módulo, fornece informações estatísticas, estado da cache. Este relatório permite-lhe controlar o número e percentagem de pedidos que são reencaminhados para um servidor de origem devido a esta funcionalidade.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignorar a origem não-Cache
**Objetivo:** determina se o CDN irá ignorar as seguintes diretivas atendidas a partir de um servidor de origem:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informações da chave:

- Configure esta funcionalidade definindo uma lista delimitada por espaço de códigos de estado para o qual as diretivas acima serão ignoradas.
- O conjunto de códigos de estado válido para esta funcionalidade são: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Desative esta funcionalidade definindo-a como um valor em branco.

**Comportamento predefinido:** o comportamento padrão é que respeite as diretivas acima.

#### <a name="compatibility"></a>Compatibilidade
Devido a da maneira na qual cache definições são controladas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
- COMO número
- Endereço IP do cliente
- Parâmetro de cookie
- Cookie parâmetro Regex
- País
- Dispositivo
- Edge Cname
- Domínio de referência
- Literal de cabeçalho do pedido
- Regex de cabeçalho do pedido
- Caráter universal de cabeçalho do pedido
- Método de pedido
- Esquema de pedido
- Literal de consulta de URL
- Regex de consulta de URL
- Caráter universal de consulta de URL
- Parâmetro de consulta de URL

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignorar Unsatisfiable intervalos 
**Objetivo:** determina a resposta que será devolvida aos clientes quando uma solicitação gerar um código de estado de pedidos gama não Satisfiable 416.

Por predefinição, este código de estado é devolvido quando a solicitação de intervalo de bytes especificada não pode ser satisfeita por um POP e um campo de cabeçalho If-Range pedido não foi especificado.

Value|Resultado
-|-
Ativado|Impede que os POPs a responder a uma solicitação de intervalo de bytes inválido com um código de estado de pedidos gama não Satisfiable 416. Em vez disso, os servidores irão fornecer o recurso solicitado e retornar um 200 OK ao cliente.
Desativado|Restaura o comportamento padrão. O comportamento padrão é que respeite o código de estado de pedidos gama não Satisfiable 416.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Max-obsoleta interno
**Objetivo:** controles quanto posterior à hora de expiração normal um ativo em cache pode ser servido a partir de um POP quando o POP não consegue revalide o elemento em cache com o servidor de origem.

Normalmente, quando expira o tempo de duração máxima de um recurso, o POP irá enviar um pedido de revalidação para o servidor de origem. A vontade de servidor de origem, em seguida, responder com qualquer um de um 304 não modificado para dar o POP de um novo da concessão no ativo em cache or else com 200 OK para fornecer o POP com uma versão atualizada do recurso em cache.

Se o POP não consegue estabelecer uma ligação com o servidor de origem ao tentar uma revalidação, em seguida, o recurso interno Max-obsoleta controla se e como há muito tempo, o POP pode continuar a servir o ativo agora obsoleta.

Tenha em atenção que este intervalo de tempo é iniciado quando max-age o elemento expira, não quando a revalidação com falha ocorre. Portanto, o período máximo durante o qual pode ser servido a um recurso sem revalidação bem-sucedida é a quantidade de tempo especificado pela combinação de idade máxima mais max obsoleta. Por exemplo, se um recurso foi colocado em cache às 09:00 com uma duração máxima de 30 minutos e uma obsoleta máx. de 15 minutos, em seguida, uma tentativa de revalidação com falhas de 9:44 resultaria num usuário final receber o elemento em cache obsoleto, enquanto uma tentativa de revalidação com falhas de 9:46 resultaria no inglês d utilizador receber um 504 tempo limite do Gateway.

Qualquer valor configurado para esta funcionalidade será substituída por `Cache-Control: must-revalidate` ou `Cache-Control: proxy-revalidate` cabeçalhos recebidos do servidor de origem. Se qualquer um dos cabeçalhos é recebida do servidor de origem quando um recurso inicialmente é colocado em cache, o POP não irá servir um ativo em cache obsoleto. Nesse caso, se o POP não consegue revalide com a origem, quando o intervalo de idade máxima do recurso tiver expirado, o POP devolve um erro de tempo limite do Gateway 504.

Informações da chave:

- Configure esse recurso por:
    - Selecionar o código de estado para os quais será aplicada a uma obsoleta max.
    - Especificar um valor inteiro e, em seguida, selecionar a unidade de tempo desejado (por exemplo, segundos, minutos, horas, etc.). Este valor define a max-obsoleta interno que será aplicada.

- Definir a unidade de tempo como "Desligado" desativa esta funcionalidade. Um recurso em cache não será fornecido para além de seu tempo de expiração normal.

**Comportamento predefinido:** dois minutos

#### <a name="compatibility"></a>Compatibilidade
Devido a da maneira na qual cache definições são controladas, esta funcionalidade não pode ser associada com as seguintes condições de correspondência: 
- COMO número
- Endereço IP do cliente
- Parâmetro de cookie
- Cookie parâmetro Regex
- País
- Dispositivo
- Edge Cname
- Domínio de referência
- Literal de cabeçalho do pedido
- Regex de cabeçalho do pedido
- Caráter universal de cabeçalho do pedido
- Método de pedido
- Esquema de pedido
- Literal de consulta de URL
- Regex de consulta de URL
- Caráter universal de consulta de URL
- Parâmetro de consulta de URL

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Cadeia de consulta de registo
**Objetivo:** determina se uma cadeia de consulta será armazenada juntamente com o URL nos registos de acesso.

Value|Resultado
-|-
Ativado|Permite que o armazenamento de cadeias de caracteres de consulta durante o registro de URLs no registo de acesso. Se um URL não contém uma cadeia de consulta, em seguida, esta opção não terá um efeito.
Desativado|Restaura o comportamento padrão. O comportamento padrão é ignorar cadeias de consulta durante o registro de URLs no registo de acesso.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Pedidos de ligação Keep-Alive máximos
**Objetivo:** define o número máximo de pedidos para uma ligação Keep-Alive antes de ser fechado.

Definir o número máximo de pedidos para um valor baixo é desencorajado e poderá resultar em degradação do desempenho.

Informações da chave:

- Especifica este valor como um inteiro de todo.
- Não inclua vírgulas ou períodos no valor especificado.

**Valor predefinido:** 10 000 pedidos

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Modificar o cabeçalho de pedido do cliente
**Objetivo:** cada pedido contém um conjunto de cabeçalhos de pedido que descrevê-lo. Esta funcionalidade, pode optar por:

- Acrescentar ou substituir o valor atribuído a um cabeçalho de pedido. Se o cabeçalho de pedido especificado não existir, esta funcionalidade irá adicioná-lo para o pedido.
- Elimine um cabeçalho de pedido no pedido.

Pedidos que são reencaminhados para um servidor de origem irão refletir as alterações feitas por esta funcionalidade.

Uma das seguintes ações pode ser realizada num cabeçalho de pedido:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao final do valor de cabeçalho de pedido existente.|**Valor de cabeçalho do pedido (cliente):**<br/>Value1<br/>**Valor de cabeçalho do pedido (motor de regras):**<br/>Value2 <br/>**Novo valor de cabeçalho do pedido:** <br/>Value1Value2
Substituir|O valor de cabeçalho do pedido será definido com o valor especificado.|**Valor de cabeçalho do pedido (cliente):**<br/>Value1<br/>**Valor de cabeçalho do pedido (motor de regras):**<br/>Value2<br/>**Novo valor de cabeçalho do pedido:**<br/> Value2 <br/>
Eliminar|Elimina o cabeçalho de pedido especificado.|**Valor de cabeçalho do pedido (cliente):**<br/>Value1<br/>**Modificar a configuração de cabeçalho de pedido do cliente:**<br/>Elimine o cabeçalho do pedido em questão.<br/>**Resultado:**<br/>O cabeçalho de pedido especificado não será reencaminhado para o servidor de origem.

Informações da chave:

- Certifique-se de que o valor especificado na opção de nome é uma correspondência exata para o cabeçalho de pedido pretendido.
- Caso não é levado em conta para fins de identificação de um cabeçalho. Por exemplo, qualquer um das variações seguintes a `Cache-Control` o nome do cabeçalho pode ser usado para identificá-lo:
    - cache-control
    - CONTROLO DE CACHE
    - cachE-Control
- Ao especificar um nome de cabeçalho, utilize apenas carateres alfanuméricos, travessões e carateres de sublinhado.
- A eliminar um cabeçalho irá impedi-lo de que está a ser encaminhado para um servidor de origem pelos POPs.
- Os seguintes cabeçalhos são reservados e não podem ser modificados por esta funcionalidade:
    - reencaminhado
    - anfitrião
    - via
    - aviso
    - x-forwarded-for
    - Todos os nomes de cabeçalho que começam com "x-ec" estão reservados.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Modificar o cabeçalho de resposta do cliente
Cada resposta contém um conjunto de cabeçalhos de resposta que descrevê-lo. Esta funcionalidade, pode optar por:

- Acrescentar ou substituir o valor atribuído a um cabeçalho de resposta. Se o cabeçalho de resposta especificado não existir, esta funcionalidade irá adicioná-lo à resposta.
- Elimine um cabeçalho de resposta da resposta.

Por predefinição, os valores de cabeçalho de resposta são definidos por um servidor de origem e os POPs.

Uma das seguintes ações pode ser realizada num cabeçalho de resposta:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao final do valor de cabeçalho de resposta existente.|**Valor de cabeçalho de resposta (cliente):**<br />Value1<br/>**Valor de cabeçalho de resposta (motor de regras):**<br/>Value2<br/>**Novo valor de cabeçalho de resposta:**<br/>Value1Value2
Substituir|O valor de cabeçalho de resposta será definido com o valor especificado.|**Valor de cabeçalho de resposta (cliente):**<br/>Value1<br/>**Valor de cabeçalho de resposta (motor de regras):**<br/>Value2 <br/>**Novo valor de cabeçalho de resposta:**<br/>Value2 <br/>
Eliminar|Elimina o cabeçalho de resposta especificado.|**Valor de cabeçalho de resposta (cliente):**<br/>Value1<br/>**Modificar a configuração de cabeçalho de resposta do cliente:**<br/>Elimine o cabeçalho de resposta em questão.<br/>**Resultado:**<br/>O cabeçalho de resposta especificado não será reencaminhado para o autor do pedido.

Informações da chave:

- Certifique-se de que o valor especificado na opção de nome é uma correspondência exata para o cabeçalho de resposta desejado. 
- Caso não é levado em conta para fins de identificação de um cabeçalho. Por exemplo, qualquer um das variações seguintes a `Cache-Control` o nome do cabeçalho pode ser usado para identificá-lo:
    - cache-control
    - CONTROLO DE CACHE
    - cachE-Control
- A eliminar um cabeçalho impede que sejam encaminhados para o autor do pedido.
- Os seguintes cabeçalhos são reservados e não podem ser modificados por esta funcionalidade:
    - accept-encoding
    - Idade
    - ligação
    - content-encoding
    - content-length
    - content-range
    - date
    - servidor
    - informações finais
    - codificação de transferência
    - Atualizar
    - vary
    - via
    - aviso
    - Todos os nomes de cabeçalho que começam com "x-ec" estão reservados.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Cache parcial de partilha
**Objetivo:** determina se um pedido pode gerar conteúdo parcialmente em cache.

Esta cache parcial, em seguida, pode ser utilizado para satisfazer novas solicitações para esse conteúdo até que o conteúdo solicitado totalmente é colocado em cache.

Value|Resultado
-|-
Ativado|Pedidos podem gerar conteúdo parcialmente em cache.
Desativado|Pedidos só podem gerar uma versão totalmente armazenada em cache do conteúdo pedido.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Prevalidate conteúdo em cache
**Objetivo:** determina se conteúdo em cache serão elegível para a revalidação antecipada antes de seu valor de TTL expire.

Defina o período de tempo antes da expiração do TTL os conteúdos solicitados durante o qual é elegível para revalidação antecipada.

Informações da chave:

- Selecionar "Desligado", à medida que a unidade de tempo necessita revalidação deverá ocorrer após o conteúdo em cache TTL expirou. Tempo não deve ser especificado e é ignorado.

**Comportamento predefinido:** desativado. Revalidação só pode ocorrer depois que TTL o conteúdo em cache expirou.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Cabeçalhos de especiais de proxy
**Objetivo:** define o conjunto de [cabeçalhos de pedido HTTP específicos da Verizon](cdn-verizon-http-headers.md) que serão reencaminhados a partir de um preenchimento de para um servidor de origem.

Informações da chave:

- Cada cabeçalho de pedido de CDN específicos definido neste recurso é encaminhado para um servidor de origem. Cabeçalhos excluídos não são reencaminhados.
- Para impedir que um cabeçalho de pedido de CDN específicos sejam encaminhados, removê-lo da lista separadas por espaços no campo de cabeçalho da lista.

Os seguintes cabeçalhos HTTP estão incluídos na lista padrão:
- Através de
- X-reencaminhados-para
- Proto X reencaminhados
- X-Host
- X Midgress
- Lista de gateways de X
- X-EC-Name
- Anfitrião

**Comportamento predefinido:** todos os cabeçalhos de pedido específicos da CDN serão reencaminhados para o servidor de origem.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Atualizar ficheiros de Cache de Zero bytes
**Objetivo:** determina como o pedido de um cliente HTTP para um recurso de cache de 0 bytes é processado pelos POPs.

Valores válidos são:

Value|Resultado
--|--
Ativado|Faz com que o POP de refetch o elemento a partir do servidor de origem.
Desativado|Restaura o comportamento padrão. O comportamento padrão é servir a ativos de cache válido mediante pedido.

Esta funcionalidade não é necessária para a colocação em cache correta e de entrega de conteúdos, mas pode ser útil como uma solução alternativa. Por exemplo, geradores de conteúdo dinâmicos nos servidores de origem inadvertidamente podem resultar em respostas de 0 bytes enviadas para os POPs. Esses tipos de respostas são normalmente colocadas em cache pelos POPs. Se sabe que uma resposta de 0 bytes nunca é uma resposta válida 

para esse conteúdo, em seguida, esta funcionalidade pode impedir que esses tipos de ativos vinculado aos seus clientes.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Defina códigos de estado em cache
**Objetivo:** define o conjunto de códigos de estado que pode resultar em conteúdo em cache.

Por predefinição, a colocação em cache está apenas ativada para 200 respostas OK.

Defina um conjunto delimitada por espaços dos códigos de estado pretendido.

Informações da chave:

- Ative a funcionalidade de ignorar No-Cache de origem. Se esta funcionalidade não está ativada, em seguida, as respostas de não - 200 OK podem não ser armazenadas em cache.
- O conjunto de códigos de estado válido para esta funcionalidade são: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Esta funcionalidade não pode ser utilizada para desativar a colocação em cache de respostas que geram um código de 200 estado OK.

**Comportamento predefinido:** colocação em cache está ativada apenas para as respostas que geram um código de 200 estado OK.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Conjunto IP de cliente cabeçalho personalizado
**Objetivo:** adiciona um cabeçalho personalizado que identifica o cliente solicitador por endereço IP para o pedido.

A opção de nome de cabeçalho define o nome do cabeçalho de pedido personalizado onde está armazenado o endereço IP do cliente.

Esta funcionalidade permite que um cliente endereços de servidor de origem para obter informações sobre o IP de cliente por meio de um cabeçalho de pedido personalizado. Se o pedido é servido a partir da cache, o servidor de origem não irá ser informado de endereço IP do cliente. Por isso, recomenda-se que esta funcionalidade ser utilizada com os ativos que não são armazenados em cache.

Certifique-se de que o nome de cabeçalho especificado não corresponde a nenhum dos seguintes nomes:

- Nomes de cabeçalho de solicitação padrão. Uma lista de nomes de cabeçalho padrão pode ser encontrada na [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nomes de cabeçalho reservado:
    - reencaminhados para
    - anfitrião
    - vary
    - via
    - aviso
    - x-forwarded-for
    - Todos os nomes de cabeçalho que começam com "x-ec" estão reservados.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Entrega de conteúdos obsoleta com o erro
**Objetivo:** determina se expiradas conteúdo em cache serão entregues quando ocorre um erro durante a revalidação de cache ou ao obter os conteúdos solicitados a partir do servidor de origem do cliente.

Value|Resultado
-|-
Ativado|Conteúdo obsoleto é fornecido ao solicitante quando ocorre um erro durante uma ligação para um servidor de origem.
Desativado|Erro de servidor de origem é reencaminhado para o autor do pedido.

**Comportamento predefinido:** desativado

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Obsoletos ao Revalide
**Objetivo:** melhora o desempenho ao permitir que os POPs servir conteúdo obsoleto ao solicitante enquanto revalidação está em curso.

Informações da chave:

- O comportamento desta funcionalidade varia de acordo com a unidade de tempo selecionado.
    - **Unidade de tempo:** Especifique um intervalo de tempo e selecione uma unidade de tempo (por exemplo, segundos, minutos, horas, etc.) para permitir que a entrega de conteúdos obsoleta. Este tipo de configuração permite que a CDN para estender o período de tempo que pode entregar conteúdo antes de requerer validação, de acordo com a seguinte fórmula: **TTL** + **obsoletos ao tempo Revalide** 
    - **Desativar:** selecione "desligado" para exigir a revalidação antes de um pedido para o conteúdo obsoleto pode ser servido.
        - Não especifique um intervalo de tempo uma vez que é não aplicáveis e será ignorada.

**Comportamento predefinido:** desativado. Revalidação deve ocorrer antes do conteúdo solicitado pode ser servido.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Token Auth
**Objetivo:** determina se a autenticação baseada em Token será aplicada a um pedido.

Se estiver ativada a autenticação baseada em tokens, em seguida, apenas pedidos que fornecem um token criptografado e estejam em conformidade com os requisitos especificados por esse token serão cumpridos.

A chave de encriptação que é utilizada para encriptar e desencriptar valores do token é determinada pela chave primária e as opções de chave de cópia de segurança na página de autenticação de Token. Tenha em atenção que as chaves de encriptação são específicas da plataforma.

**Comportamento predefinido:** desativado.

Esta funcionalidade tem precedência sobre a maioria dos recursos com a exceção a funcionalidade de reescrita de URLs.

Value | Resultado
------|---------
Ativado | Protege os conteúdos solicitados com a autenticação baseada em tokens. Apenas as solicitações de clientes que fornecem um token válido e cumprem os requisitos serão cumpridas. Transações de FTP são excluídas da autenticação baseada em tokens.
Desativado| Restaura o comportamento padrão. O comportamento padrão é permitir que a configuração da autenticação baseada em tokens determinar se um pedido esteja protegido.

#### <a name="compatibility"></a>Compatibilidade
Não utilize a autenticação de Token com uma condição de correspondência de sempre. 

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Código de negação de token de autenticação
**Objetivo:** determina o tipo de resposta que vai ser devolvido a um utilizador quando um pedido é negado devido à autenticação baseada em tokens.

Os códigos de resposta disponíveis estão listados na tabela seguinte.

Código de Resposta|Nome de resposta|Descrição
-------------|-------------|--------
301|Movido permanentemente|Este código de estado redireciona os utilizadores não autorizados para o URL especificado no cabeçalho Location.
302|Localizado|Este código de estado redireciona os utilizadores não autorizados para o URL especificado no cabeçalho Location. Este código de estado é o método padrão do setor da execução de um redirecionamento.
307|Redirecionamento temporário|Este código de estado redireciona os utilizadores não autorizados para o URL especificado no cabeçalho Location.
401|Não autorizado|Combinar este código de estado com o cabeçalho de resposta WWW-Authenticate permite-lhe um prompt ao usuário para autenticação.
403|Proibido|Esta mensagem é a mensagem de estado de proibido de 403 padrão que um utilizador não autorizado vai ver quando tentar aceder aos conteúdos protegidos.
404|Ficheiro Não Encontrado|Este código de estado indica que o cliente HTTP foi capaz de comunicar com o servidor, mas o conteúdo solicitado não foi encontrado.

#### <a name="compatibility"></a>Compatibilidade
Não utilize o código de negação de autenticação do Token com uma condição de correspondência de sempre. Em alternativa, utilize o **a manipulação de recusa personalizado** secção a **Token de autenticação** página do **gerir** portal. Para obter mais informações, consulte [recursos de proteger a CDN do Azure com a autenticação de token](cdn-token-auth.md).

#### <a name="url-redirection"></a>Redirecionamento de URL

Esta funcionalidade suporta o redirecionamento de URL para um URL definido pelo utilizador quando está configurado para retornar um código de estado de 3xx. Este URL definido pelo utilizador pode ser especificado, efetuando os seguintes passos:

1. Selecione um código de resposta de 3xx para a funcionalidade de código de negação do Token Auth.
2. Selecione a opção de nome de cabeçalho opcional "Localização".
3. Defina a opção de valor de cabeçalho opcional para o URL pretendido.

Se um URL não está definido para um código de estado de 3xx, a página de resposta padrão para um código de estado de 3xx será devolvida ao usuário.

Redirecionamento de URL só é aplicável para códigos de resposta de 3xx.

A opção de valor de cabeçalho opcional suporta carateres alfanuméricos e espaços entre aspas duplas.

#### <a name="authentication"></a>Authentication

Esta funcionalidade suporta a capacidade de incluir o cabeçalho WWW-Authenticate em resposta a um pedido não autorizado para conteúdo protegido pela autenticação baseada em Token. Se o cabeçalho WWW-Authenticate foi definido como "básico" na sua configuração, será solicitado o utilizador não autorizado para as credenciais da conta.

A configuração acima pode ser alcançada ao executar os seguintes passos:

1. Selecione "401" como o código de resposta para a funcionalidade de código de negação do Token Auth.
2. Selecione "WWW-Authenticate" a opção de nome de cabeçalho opcional.
3. Definir a opção de valor de cabeçalho opcional para "básico".

O cabeçalho WWW-Authenticate só é aplicável para códigos de resposta 401.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Autenticação de token ignorar URL maiúsculas / minúsculas
**Objetivo:** determina se as comparações de URL feitas pela autenticação baseada em Token diferenciam maiúsculas de minúsculas.

Os parâmetros afetados por esta funcionalidade são:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Valores válidos são:

Value|Resultado
---|----
Ativado|Faz com que o POP Ignorar maiúsculas / minúsculas ao comparar os URLs para os parâmetros de autenticação baseada em tokens.
Desativado|Restaura o comportamento padrão. O comportamento padrão é para comparações de URL para a autenticação de Token seja maiúsculas e minúsculas.

**Comportamento predefinido:** desativado.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Token de autenticação de parâmetro
**Objetivo:** determina se o parâmetro de cadeia de caracteres de consulta baseada em tokens de autenticação deve ser renomeado.

Informações da chave:

- A opção de valor define o nome de parâmetro de cadeia de caracteres de consulta através do qual pode ser especificado um token.
- A opção de valor não pode ser definida como "ec_token."
- Certifique-se de que o nome definido na opção valor contém carateres de URL só é válidos.

Value|Resultado
----|----
Ativado|A opção de valor define o nome de parâmetro de cadeia de caracteres de consulta através dos quais os tokens devem ser definidos.
Desativado|Um token pode ser especificado como um parâmetro de cadeia de caracteres de consulta não definido no URL do pedido.

**Comportamento predefinido:** desativado. Um token pode ser especificado como um parâmetro de cadeia de caracteres de consulta não definido no URL do pedido.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Redirecionamento de URL
**Objetivo:** redireciona os pedidos com o cabeçalho de localização.

A configuração desta funcionalidade requer a definir as opções seguintes:

Opção|Descrição
-|-
Código|Selecione o código de resposta que vai ser devolvido para o autor do pedido.
Padrão de & origem| Essas configurações definem um padrão URI do pedido que identifica o tipo de pedidos que podem ser redirecionadas. Serão redirecionados apenas pedidos cujo URL cumprir ambos os seguintes critérios: <br/> <br/> **Origem (ou ponto de acesso ao conteúdo):** selecione um caminho relativo que identifica um servidor de origem. Este caminho é o _/XXXX/_ secção e ao nome de ponto final. <br/><br/> **Origem (padrão):** tem de ser definido um padrão que identifica os pedidos através do caminho relativo. Este padrão de expressão regular tem de definir um caminho que começa depois do acesso ao conteúdo anteriormente selecionado apontar diretamente (consulte acima). <br/> – Certifique-se de que o pedido URI critérios (ou seja, origem e padrão) definidos anteriormente não entram em conflito com quaisquer condições de correspondência definidas para esta funcionalidade. <br/> -Especifique um padrão; Se utilizar um valor em branco como o padrão, todas as cadeias de caracteres são correspondentes.
Destino| Defina o URL para o qual serão redirecionados os pedidos acima. <br/><br/> Construa dinamicamente este URL a utilizar: <br/> -Um padrão de expressão regular <br/>- [Variáveis HTTP](cdn-http-variables.md) <br/><br/> Substitua os valores capturados no padrão de origem para o padrão de destino usando $_n_ onde _n_ identifica um valor pela ordem em que foram capturado. Por exemplo, US $1 representa o primeiro valor capturado no padrão de origem, enquanto us $2 representa o segundo valor. <br/> 
É altamente recomendado para utilizar um URL absoluto. A utilização de um URL relativo pode redirecionar os URLs de CDN para um caminho inválido.

**Cenário de exemplo**

Neste exemplo, demonstra como redirecionar um URL de CNAME que resolve para este URL base do CDN do edge: http:\//marketing.azureedge.net/brochures

Qualificar pedidos será redirecionado para esta base edge URL de CNAME: http:\//cdn.mydomain.com/resources

Esse redirecionamento de URL pode ser alcançado através da seguinte configuração: ![Redirecionamento de URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Pontos principais:**

- A funcionalidade de redirecionamento de URLs define o pedido de URLs que serão redirecionados. Como resultado, as condições de correspondência adicionais não são necessárias. Embora a condição de correspondência foi definida como "Sempre", serão redirecionadas apenas as solicitações para a pasta "brochuras" na origem de cliente "marketing". 
- Todos os pedidos correspondentes serão redirecionados para o edge que CNAME URL definido na opção de destino. 
    - Cenário de exemplo #1: 
        - Pedido de exemplo (URL de CDN): http:\//marketing.azureedge.net/brochures/widgets.pdf 
        - URL do pedido (após o redirecionamento): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Cenário de exemplo #2: 
        - Pedido de exemplo (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf 
        - URL do pedido (após o redirecionamento): http:\//cdn.mydomain.com/resources/widgets.pdf
    - Cenário de exemplo #3: 
        - Pedido de exemplo (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL do pedido (após o redirecionamento): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- A variável de esquema de pedido (% {esquema}) é utilizada na opção de destino, que garante o esquema do pedido permanece inalterada após o redirecionamento.
- Os segmentos de URL que foram capturados no pedido são acrescentados ao novo URL por meio de "US $1."

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>Reescrita de URLs
**Objetivo:** reescreve o URL do pedido.

Informações da chave:

- A configuração desta funcionalidade requer a definir as opções seguintes:

Opção|Descrição
-|-
 Padrão de & origem | Essas configurações definem um padrão URI do pedido que identifica o tipo de pedidos que pode ser reescrito. Irão ser reescritos apenas pedidos cujo URL cumprir ambos os seguintes critérios: <br/><br/>  - **Origem (ou ponto de acesso ao conteúdo):** selecione um caminho relativo que identifica um servidor de origem. Este caminho é o _/XXXX/_ secção e ao nome de ponto final. <br/><br/> - **Origem (padrão):** tem de ser definido um padrão que identifica os pedidos através do caminho relativo. Este padrão de expressão regular tem de definir um caminho que começa depois do acesso ao conteúdo anteriormente selecionado apontar diretamente (consulte acima). <br/> Certifique-se de que o pedido URI critérios (ou seja, origem e padrão) definidos anteriormente não entram em conflito com qualquer uma das condições de correspondência definidas para esta funcionalidade. Especificar um padrão; Se utilizar um valor em branco como o padrão, todas as cadeias de caracteres são correspondentes. 
 Destino  |Defina a URL relativa para o qual os pedidos acima irão ser reescritos por: <br/>    1. Selecionar um ponto de acesso ao conteúdo que identifica um servidor de origem. <br/>    2. Definir um caminho relativo com: <br/>        -Um padrão de expressão regular <br/>        - [Variáveis HTTP](cdn-http-variables.md) <br/> <br/> Substitua os valores capturados no padrão de origem para o padrão de destino usando $_n_ onde _n_ identifica um valor pela ordem em que foram capturado. Por exemplo, US $1 representa o primeiro valor capturado no padrão de origem, enquanto us $2 representa o segundo valor. 

 Esta funcionalidade permite que os POPs regravar a URL sem redirecionamento tradicional de executar. Ou seja, o solicitante recebe o mesmo código de resposta, como se o URL reescrito tinha sido solicitado.

**Cenário de exemplo 1**

Este exemplo demonstra como redirecionar um URL de CNAME que resolve para este URL base do CDN do edge: http:\//marketing.azureedge.net/brochures/

Qualificar pedidos será redirecionado para esta base edge URL de CNAME: http:\//MyOrigin.azureedge.net/resources/

Esse redirecionamento de URL pode ser alcançado através da seguinte configuração: ![Redirecionamento de URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Cenário de exemplo 2**

Este exemplo demonstra como redirecionar um URL de CNAME do UPPERCASE em minúsculas com expressões regulares do edge.

Esse redirecionamento de URL pode ser alcançado através da seguinte configuração: ![Redirecionamento de URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Pontos principais:**

- O recurso de reescrita de URLs define o pedido de URLs que irão ser reescritos. Como resultado, as condições de correspondência adicionais não são necessárias. Embora a condição de correspondência foi definida como "Sempre", apenas as solicitações para a pasta "brochuras" na origem de cliente "marketing" irão ser reescritas.

- Os segmentos de URL que foram capturados no pedido são acrescentados ao novo URL por meio de "US $1."

#### <a name="compatibility"></a>Compatibilidade
Esta funcionalidade inclui correspondentes aos critérios que têm de ser cumpridos antes de ele pode ser aplicado a um pedido. Para impedir que a configuração de critérios de correspondência em conflito, esta funcionalidade é incompatível com as seguintes condições de correspondência:

- COMO número
- Origem de CDN
- Endereço IP do cliente
- Origem do cliente
- Esquema de pedido
- Diretório de caminho do URL
- Extensão do caminho de URL
- Nome de ficheiro de caminho de URL
- Literal de caminho do URL
- Regex do caminho de URL
- Caminho de URL com carateres universais
- Literal de consulta de URL
- Parâmetro de consulta de URL
- Regex de consulta de URL
- Caráter universal de consulta de URL

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Variável de utilizador
**Objetivo:** apenas para utilização interna.

[Voltar ao início](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Próximos Passos
* [Referência do motor de regras](cdn-rules-engine-reference.md)
* [Expressões condicionais do motor de regras](cdn-rules-engine-reference-conditional-expressions.md)
* [Condições de correspondência do motor de regras](cdn-rules-engine-reference-match-conditions.md)
* [Substituir comportamento HTTP utilizando o mecanismo de regras](cdn-rules-engine.md)
* [Descrição geral da CDN do Azure](cdn-overview.md)
