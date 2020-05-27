---
title: Azure CDN da Verizon Premium governa características do motor / Microsoft Docs
description: Documentação de referência para O CDN Azure a partir das características do motor de regras Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: d2d4090934a940809fe75ad70e0650eb1c9353f1
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872721"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN da Verizon Premium governa características do motor

Este artigo lista descrições detalhadas das funcionalidades disponíveis para o Motor de [Regras](cdn-verizon-premium-rules-engine.md)da Rede de Entrega de Conteúdos Azure (CDN).

A terceira parte de uma regra é a característica. Uma funcionalidade define o tipo de ação que é aplicada ao tipo de pedido que é identificado por um conjunto de condições de correspondência.


Para as funcionalidades mais recentes, consulte a documentação do Motor verizon [Rules.](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions)


## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN do motor de regras Verizon Premium apresenta referência

---

### <a name="age-response-header"></a>Cabeçalho de resposta à idade

**Objetivo**: Determina se um cabeçalho de resposta da Idade está incluído na resposta enviada ao solicitador.

Valor|Resultado
--|--
Ativado | O cabeçalho de resposta age está incluído na resposta enviada ao solicitador.
Desativado | O cabeçalho de resposta age é excluído da resposta enviada ao solicitador.

**Comportamento predefinido**: Desativado.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Parâmetros de largura de banda

**Finalidade:** Determina se os parâmetros de estrangulamento da largura de banda (por exemplo, ec_rate e ec_prebuf) estão ativos.

Os parâmetros de estrangulamento da largura de banda determinam se a taxa de transferência de dados para o pedido de um cliente está limitada a uma taxa personalizada.

Valor|Resultado
--|--
Ativado|Permite que os POPs honrem os pedidos de estrangulamento da largura de banda.
Desativado|Faz com que os POPs ignorem os parâmetros de estrangulamento da largura de banda. O conteúdo solicitado é servido normalmente (isto é, sem estrangulamento da largura de banda).

**Comportamento predefinido:** Habilitado.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Estrangulamento da largura de banda

**Finalidade:** Acelera a largura de banda para a resposta fornecida pelos POPs.

Ambas as opções devem ser definidas para configurar corretamente a largura de banda.

Opção|Descrição
--|--
Kbytes por segundo|Defina esta opção para a largura de banda máxima (Kb por segundo) que pode ser usada para fornecer a resposta.
Prebuf segundos|Defina esta opção para o número de segundos para os POPs esperarem até que a largura de banda seja acelerada. O objetivo deste período de tempo de largura de banda sem restrições é evitar que um leitor de meios de comunicação experimente problemas de gaguez ou tampão devido ao estrangulamento da largura de banda.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Bypass Cache

**Finalidade:** Determina se o pedido deve contornar o cache.

Valor|Resultado
--|--
Ativado|Faz com que todos os pedidos caiam no servidor de origem, mesmo que o conteúdo tenha sido previamente emcache em POPs.
Desativado|Faz com que os POPs cache ativos de acordo com a política de cache definida nos seus cabeçalhos de resposta.

**Comportamento predefinido:**

- **HTTP Grande:** Deficientes

<!---
- **ADN:** Enabled

--->

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Métodos HTTP cacheable

**Finalidade:** Determina o conjunto de métodos HTTP adicionais que podem ser emcache na rede.

Informação-chave:

- Esta funcionalidade pressupõe que as respostas GET devem ser sempre em cache. Como resultado, o método GET HTTP não deve ser incluído ao definir esta função.
- Esta funcionalidade suporta apenas o método POST HTTP. Ativar o cache de resposta POST definindo esta função para `POST` .
- Por padrão, apenas os pedidos cujo corpo é inferior a 14 Kb são cached. Utilize a função de tamanho corporal de pedido cacheable para definir o tamanho máximo do corpo de pedido.

**Comportamento predefinido:** Apenas as respostas GET são em cache.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Tamanho do corpo do pedido cacheable

**Finalidade:** Define o limiar para determinar se uma resposta POST pode ser colocada em cache.

Este limiar é determinado especificando o tamanho máximo do corpo de pedido. Os pedidos que contenham um corpo de pedido maior não são cached.

Informação-chave:

- Esta Funcionalidade só é aplicável quando as respostas post são elegíveis para cache. Utilize a funcionalidade Métodos HTTP Cacheable para ativar o cacheching de pedido post.
- O organismo de pedido é tomado em consideração para:
    - x-www-form-urlencoded valores
    - Garantindo uma chave de cache única
- A definição de um grande tamanho máximo do corpo de pedidos pode ter impacto no desempenho da entrega de dados.
    - **Valor Recomendado:** 14 Kb
    - **Valor Mínimo:** 1 Kb

**Comportamento predefinido:** 14 Kb

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Tratamento do cabeçalho de controlo de cache

**Finalidade:** Controla a geração de `Cache-Control` cabeçalhos pelo POP quando a Funcionalidade Externa max-age está ativa.

A maneira mais fácil de alcançar este tipo de configuração é colocar as funcionalidades externas de tratamento de cabeçalho sinuoso e de controlo de cache na mesma declaração.

Valor|Resultado
--|--
Overwrite|Garante que ocorrem as seguintes ações:<br/> - Substitui o `Cache-Control` cabeçalho gerado pelo servidor de origem. <br/>- Adiciona o `Cache-Control` cabeçalho produzido pela função Externa Max-Age à resposta.
Passar|Garante que o `Cache-Control` cabeçalho produzido pela função Externa Max-Age nunca é adicionado à resposta. <br/> Se o servidor de origem produzir um `Cache-Control` cabeçalho, passa para o utilizador final. <br/> Se o servidor de origem não produzir um `Cache-Control` cabeçalho, então esta opção pode fazer com que o cabeçalho de resposta não contenha um `Cache-Control` cabeçalho.
Adicione se faltar|Se um `Cache-Control` cabeçalho não foi recebido do servidor de origem, então esta opção adiciona o `Cache-Control` cabeçalho produzido pela função Externa Max-Age. Esta opção é útil para garantir que todos os ativos são atribuídos um `Cache-Control` cabeçalho.
Remover| Esta opção garante que um `Cache-Control` cabeçalho não esteja incluído com a resposta do cabeçalho. Se um `Cache-Control` cabeceamento já tiver sido atribuído, então é removido da resposta do cabeceamento.

**Comportamento predefinido:** Sobrepor.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cadeia de consulta de chave de cache

**Finalidade:** Determina se a chave de cache inclui ou exclui parâmetros de corda de consulta associados a um pedido.

Informação-chave:

- Especifique um ou mais nomes de parâmetros de corda de consulta e separe cada nome de parâmetro com um único espaço.
- Esta função determina se os parâmetros de corda de consulta estão incluídos ou excluídos da chave de cache. São fornecidas informações adicionais para cada opção na tabela seguinte.

Tipo|Descrição
--|--
 Incluir|  Indica que cada parâmetro especificado deve ser incluído na tecla cache. Uma chave de cache única é gerada para cada pedido que contém um valor único para um parâmetro de corda de consulta definido nesta funcionalidade.
 Incluir todos  |Indica que uma chave de cache única é criada para cada pedido a um ativo que inclui uma cadeia de consulta única. Este tipo de configuração não é tipicamente recomendado porque pode levar a uma pequena percentagem de acessos de cache. Um número reduzido de acertos de cache aumenta a carga no servidor de origem, porque deve servir mais pedidos. Esta configuração duplica o comportamento de cache conhecido como "cache único" na página Dery-String Caching.
 Excluir | Indica que apenas os parâmetros especificados estão excluídos da chave de cache. Todos os outros parâmetros de corda de consulta estão incluídos na chave de cache.
 Excluir tudo  |Indica que todos os parâmetros de corda de consulta estão excluídos da chave de cache. Esta configuração duplica o comportamento padrão de cache na página DeRy-String Caching.  

O motor de regras permite-lhe personalizar a forma como o cache de cordas de consulta é implementado. Por exemplo, pode especificar que o cache de cordas de consulta é realizado apenas em certos locais ou tipos de ficheiros.

Para duplicar o comportamento de cache de corda "sem cache" na página DeRy-String Caching, crie uma regra que contenha uma condição de correspondência url Query Wildcard e uma função de Cache bypass. Delineie a condição de correspondência do URL Query Wildcard para um asterisco (*).

>[!IMPORTANT]
> Se a autorização de ficha estiver ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que pode ser usado para consulta de cache de cordas. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

#### <a name="sample-scenarios"></a>Cenários de amostra

A utilização da amostra seguinte para esta funcionalidade fornece um pedido de amostra e a chave de cache padrão:

- **Pedido de amostra:** http://wpc.0001.&lt ;D omain &gt; /800001/Origem/pasta/asset.htm?sessionid=1234&idioma=EN&userid=01
- **Chave de cache padrão:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Incluir

Configuração da amostra:

- **Tipo:** Incluir
- **Parâmetros):** linguagem

Este tipo de configuração geraria a seguinte chave cache-chave do parâmetro de corda de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Incluir todos

Configuração da amostra:

- **Tipo:** Incluir todos

Este tipo de configuração geraria a seguinte chave cache-chave do parâmetro de corda de consulta:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Excluir

Configuração da amostra:

- **Tipo:** Excluir
- **Parâmetros):** sessão útil

Este tipo de configuração geraria a seguinte chave cache-chave do parâmetro de corda de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Excluir tudo

Configuração da amostra:

- **Tipo:** Excluir tudo

Este tipo de configuração geraria a seguinte chave cache-chave do parâmetro de corda de consulta:

    /800001/Origin/folder/asset.htm

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Reescrever a chave cache

**Finalidade:** Reescreve a chave de cache associada a um pedido.

Uma chave de cache é o caminho relativo que identifica um ativo para fins de cache. Por outras palavras, os servidores verificam uma versão em cache de um ativo de acordo com o seu caminho definido pela sua chave de cache.

Configure esta funcionalidade definindo ambas as seguintes opções:

Opção|Descrição
--|--
Caminho Original| Defina o caminho relativo para os tipos de pedidos cuja chave de cache é reescrita. Um caminho relativo pode ser definido selecionando um caminho de origem base e, em seguida, definindo um padrão de expressão regular.
Novo Caminho|Defina o caminho relativo para a nova tecla cache. Um caminho relativo pode ser definido selecionando um caminho de origem base e, em seguida, definindo um padrão de expressão regular. Este caminho relativo pode ser construído dinamicamente através da utilização de [variáveis HTTP](cdn-http-variables.md).

**Comportamento predefinido:** A chave de cache de um pedido é determinada pelo pedido URI.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Comentário

**Finalidade:** Permite que uma nota seja adicionada dentro de uma regra.

Uma das utilizações para esta funcionalidade é fornecer informações adicionais sobre o propósito geral de uma regra ou por que uma determinada condição ou característica de correspondência foi adicionada à regra.

Informação-chave:

- Pode especificar-se um máximo de 150 caracteres.
- Utilize apenas caracteres alfanuméricos.
- Esta funcionalidade não afeta o comportamento da regra. Destina-se apenas a fornecer uma área onde possa fornecer informações para referência futura ou que possa ajudar na resolução de problemas da regra.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Enchimento completo de cache

**Finalidade:** Determina o que acontece quando um pedido resulta numa falha parcial de cache num POP.

Uma falha parcial de cache descreve o estado de cache de um ativo que não foi completamente descarregado para um POP. Se um ativo for apenas parcialmente cached em um POP, então o próximo pedido para esse ativo será reencaminhado para o servidor de origem.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Uma falha parcial de cache ocorre normalmente após um utilizador abortar um download ou para ativos que são exclusivamente solicitados usando pedidos de gama HTTP. Esta funcionalidade é mais útil para grandes ativos que normalmente não são descarregados do início ao fim (por exemplo, vídeos). Como resultado, esta funcionalidade é ativada por padrão na plataforma HTTP Large. É desativado em todas as outras plataformas.

Mantenha a configuração predefinida para a plataforma HTTP Large, pois reduz a carga no servidor de origem do cliente e aumenta a velocidade a que os seus clientes descarregam o seu conteúdo.

Valor|Resultado
--|--
Ativado|Restaura o comportamento padrão. O comportamento padrão é forçar o POP a iniciar uma busca de fundo do ativo a partir do servidor de origem. Depois disso, o ativo estará na cache local do POP.
Desativado|Impede que um POP realize uma busca de fundo para o ativo. O resultado é que o próximo pedido desse ativo daquela região faz com que um POP o solicite ao servidor de origem do cliente.

**Comportamento predefinido:** Habilitado.

#### <a name="compatibility"></a>Compatibilidade

Devido à forma como as definições de cache são rastreadas, esta funcionalidade não pode ser associada às seguintes condições de jogo:

- Número as
- Endereço IP do Cliente
- Parâmetro de cookies
- Parameter de biscoitos Regex
- País
- Dispositivo
- Microsoft Edge Cname
- Domínio de referimento
- Pedido cabeçalho literal
- Pedido de cabeçalho Regex
- Pedir Header Wildcard
- Método de Pedido
- Regime de Pedidos
- Url Consulta Literal
- URL Consulta Regex
- URL Consulta Wildcard
- Parâmetro de consulta de URL

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Tipos de ficheiros compressivos

**Finalidade:** Define os formatos de ficheiro para os ficheiros que são comprimidos no servidor.

Um formato de ficheiro pode ser especificado utilizando o seu tipo de mídia de Internet (por exemplo, Tipo de Conteúdo). O tipo de mídia de Internet é metadados independentes da plataforma que permite aos servidores identificar o formato de ficheiro de um determinado ativo. Uma lista de tipos comuns de mídia internet é fornecida abaixo.

Tipo de mídia de internet|Descrição
--|--
texto/planície|Ficheiros de texto simples
text/html| Ficheiros HTML
text/css|Folhas de estilo em cascata (CSS)
aplicação/x-javascript|JavaScript
aplicação/javascript|JavaScript

Informação-chave:

- Especifique vários tipos de meios de Internet delimitando cada um com um único espaço.
- Esta funcionalidade apenas comprime ativos cujo tamanho seja inferior a 1 MB. Os ativos maiores não são comprimidos pelos servidores.
- Certos tipos de conteúdos, tais como imagens, vídeo e meios de comunicação áudio (por exemplo, JPG, MP3, MP4, etc.), já estão comprimidos. Uma vez que a compressão adicional neste tipo de ativos não diminui significativamente o tamanho dos ficheiros, recomenda-se que não permita a compressão sobre os mesmos.
- Os caracteres wildcard, como os asteriscos, não são suportados.
- Antes de adicionar esta funcionalidade a uma regra, certifique-se de que define a opção Desativação de Compressão na página de Compressão para a plataforma à qual esta regra é aplicada.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Campo de registo personalizado 1

**Finalidade:** Determina o formato e o conteúdo que será atribuído ao campo de registo personalizado num ficheiro de registo bruto.

Este campo personalizado permite-lhe determinar quais os valores de cabeçalho de pedido e resposta armazenados nos seus ficheiros de registo.

Por padrão, o campo de registo personalizado chama-se "x-ec_custom-1". O nome deste campo pode ser personalizado a partir da página Definições de Registo Bruto.

O formato de especificação dos cabeçalhos de pedido e resposta é definido da seguinte forma:

Tipo de cabeçalho|Formato|Exemplos
-|-|-
Cabeçalho do Pedido|`%{[RequestHeader]()}[i]()` | %{Aceitar-Codificar}i <br/> {Referrer}i <br/> %{Autorização}i
Cabeçalho de Resposta|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informação-chave:

- Um campo de registo personalizado pode conter qualquer combinação de campos de cabeçalho e texto simples.
- Os caracteres válidos para este campo são os seguintes: alfanumérico (0-9, a-z e A-Z), traços, cólons, semi-cólons, apóstrofos, vírgulas, períodos, sublinhados, sinais iguais, parênteses, parênteses e espaços. O símbolo percentual e os aparelhos encaracolados só são permitidos quando utilizados para especificar um campo de cabeçalho.
- A ortografia de cada campo de cabeçalho especificado deve coincidir com o nome do cabeçalho de pedido/resposta pretendido.
- Se pretender especificar vários cabeçalhos, utilize um separador para indicar cada cabeçalho. Por exemplo, pode utilizar uma abreviatura para cada cabeçalho:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Valor predefinido:** -

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Cabeçalhos de resposta de cache dedepura

**Finalidade:** Determina se uma resposta pode incluir [cabeçalhos de resposta X-EC-Debug,](cdn-http-debug-headers.md)que fornece informações sobre a política de cache para o ativo solicitado.

Os cabeçalhos de resposta de cache de depuração serão incluídos na resposta quando ambos os seguintes forem verdadeiros:

- A função Debug Cache Response Headers foi ativada no pedido especificado.
- O pedido especificado define o conjunto de cabeçalhos de resposta de depuração que serão incluídos na resposta.

Os cabeçalhos de resposta de cache de depuração podem ser solicitados, incluindo o seguinte cabeçalho e as diretivas especificadas no pedido:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Exemplo:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valor|Resultado
-|-
Ativado|Os pedidos de cabeçalhos de resposta de cache dedepura retornarão uma resposta que inclui o cabeçalho X-EC-Debug.
Desativado|O cabeçalho de resposta X-EC-Debug será excluído da resposta.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Idade máxima interna padrão

**Finalidade:** Determina o intervalo de idade máxima padrão para pop para a revalidação do cache do servidor de origem. Por outras palavras, a quantidade de tempo que passará antes de um POP verificará se um ativo em cache corresponde ao ativo armazenado no servidor de origem.

Informação-chave:

- Esta ação só terá lugar para respostas de um servidor de origem que não atribuiu uma indicação de idade máxima no `Cache-Control` cabeçalho ou `Expires` cabeçalho.
- Esta ação não se realizará para ativos que não sejam considerados cacheables.
- Esta ação não afeta as revalidações de cache pop do navegador. Este tipo de revalidações são determinados pelos `Cache-Control` `Expires` cabeçalhos ou cabeçalhos enviados para o navegador, que podem ser personalizados com a funcionalidade Externa Max-Age.
- Os resultados desta ação não têm um efeito observável nos cabeçalhos de resposta e o conteúdo devolvido dos POPs para o seu conteúdo, mas pode ter um efeito na quantidade de tráfego de revalidação enviado de POPs para o seu servidor de origem.
- Configure esta funcionalidade por:
    - Selecionando o código de estado para o qual pode ser aplicada uma idade máxima interna predefinida.
    - Especificando um valor inteiro e, em seguida, selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas, etc.). Este valor define o intervalo interno de idade máxima por defeito.

- A definição da unidade de tempo para "Off" atribuirá um intervalo de idade máxima interna de 7 dias para pedidos que não tenham sido atribuídos uma indicação de idade máxima no seu `Cache-Control` ou `Expires` cabeçalho.

**Valor Predefinido:** 7 dias

#### <a name="compatibility"></a>Compatibilidade

Devido à forma como as definições de cache são rastreadas, esta funcionalidade não pode ser associada às seguintes condições de jogo:
- Número as
- Endereço IP do Cliente
- Parâmetro de cookies
- Parameter de biscoitos Regex
- País
- Dispositivo
- Nome de borda
- Domínio de referimento
- Pedido cabeçalho literal
- Pedido de cabeçalho Regex
- Pedir Header Wildcard
- Método de Pedido
- Regime de Pedidos
- Url Consulta Literal
- URL Consulta Regex
- URL Consulta Wildcard
- Parâmetro de consulta de URL

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Acesso a Negação (403)

**Finalidade**: Determina se todos os pedidos são rejeitados com uma resposta proibida 403.

Valor | Resultado
------|-------
Ativado| Faz com que todos os pedidos que satisfaçam os critérios correspondentes sejam rejeitados com uma resposta proibida 403.
Desativado| Restaura o comportamento padrão. O comportamento predefinido é permitir que o servidor de origem determine o tipo de resposta que será devolvida.

**Comportamento predefinido**: Desativado

> [!TIP]
   > Uma utilização possível para esta funcionalidade é associá-la a uma condição de jogo do 'Cabeçalho de Pedido' para bloquear o acesso aos refereredores HTTP que estão a utilizar links inline para o seu conteúdo.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Expira o tratamento do cabeçalho

**Finalidade:** Controla a geração de `Expires` cabeçalhos por um POP quando a funcionalidade Externa Max-Age está ativa.

A maneira mais fácil de alcançar este tipo de configuração é colocar as funcionalidades externas de Tratamento de Cabeçalho e Expira na mesma declaração.

Valor|Resultado
--|--
Overwrite|Garante que serão tomadas as seguintes ações:<br/>- Substitui o `Expires` cabeçalho gerado pelo servidor de origem.<br/>- Adiciona o `Expires` cabeçalho produzido pela função Externa Max-Age à resposta.
Passar|Garante que o `Expires` cabeçalho produzido pela função Externa Max-Age nunca é adicionado à resposta. <br/> Se o servidor de origem produzir um `Expires` cabeçalho, passará para o utilizador final. <br/>Se o servidor de origem não produzir um `Expires` cabeçalho, então esta opção pode fazer com que o cabeçalho de resposta não contenha um `Expires` cabeçalho.
Adicione se faltar| Se um `Expires` cabeçalho não foi recebido do servidor de origem, então esta opção adiciona o `Expires` cabeçalho produzido pela função Externa Max-Age. Esta opção é útil para garantir que todos os ativos serão atribuídos um `Expires` cabeçalho.
Remover| Garante que um `Expires` cabeçalho não está incluído com a resposta do cabeçalho. Se um `Expires` cabeceamento já tiver sido atribuído, então é removido da resposta do cabeceamento.

**Comportamento predefinido:** Sobreescrever

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Idade Máxima Externa

**Finalidade:** Determina o intervalo de idade máxima para o navegador para a revalidação de cache POP. Por outras palavras, o tempo que passará antes de um navegador poder verificar uma nova versão de um ativo a partir de um POP.

Ativar esta funcionalidade gerará `Cache-Control: max-age` e `Expires` cabeçalhos dos POPs e enviá-los-á para o cliente HTTP. Por padrão, estes cabeçalhos irão substituir os cabeçalhos criados pelo servidor de origem. No entanto, o Tratamento do Cabeçalho de Controlo de Cache e as funcionalidades de Tratamento do Cabeçalho Expirapodem ser usados para alterar este comportamento.

Informação-chave:

- Esta ação não afeta as revalidações de cache do servidor de origem. Estes tipos de revalidações são determinados pelos `Cache-Control` `Expires` cabeçalhos e cabeçalhos recebidos do servidor de origem, e podem ser personalizados com as funcionalidades De Sem Carga Interna max-Age e as funcionalidades da Força Interna Max-Age.
- Configure esta função especificando um valor inteiro e selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas, etc.).
- A definição desta funcionalidade para um valor negativo faz com que os POPs enviem um `Cache-Control: no-cache` e um tempo que é definido no passado com cada resposta ao `Expires` navegador. Embora um cliente HTTP não cache a resposta, esta definição não afetará a capacidade dos POPs de cache a resposta do servidor de origem.
- A definição da unidade de tempo para "Desligar" desativará esta função. Os `Cache-Control` `Expires` cabeçalhos e cabeçalhos emcache com a resposta do servidor de origem passarão para o navegador.

**Comportamento predefinido:** Fora

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Siga redirecionamentos

**Finalidade:** Determina se os pedidos podem ser redirecionados para o nome de anfitrião definido no cabeçalho de Localização devolvido por um servidor de origem do cliente.

Informação-chave:

- Os pedidos só podem ser redirecionados para CNAMEs de borda que correspondam à mesma plataforma.

Valor|Resultado
-|-
Ativado|Os pedidos podem ser redirecionados.
Desativado|Os pedidos não serão redirecionados.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Força Interna Max-Age

**Finalidade:** Determina o intervalo de idade máxima para pop para a revalidação do cache do servidor de origem. Por outras palavras, a quantidade de tempo que passará antes de um POP pode verificar se um ativo em cache corresponde ao ativo armazenado no servidor de origem.

Informação-chave:

- Esta funcionalidade irá sobrepor-se ao intervalo de idade máxima definido ou `Cache-Control` `Expires` cabeçalhos gerados a partir de um servidor de origem.
- Esta funcionalidade não afeta as revalidações de cache pop do navegador. Este tipo de revalidações são determinados pelos `Cache-Control` `Expires` cabeçalhos ou cabeçalhos enviados para o navegador.
- Esta funcionalidade não tem um efeito observável na resposta transmitida por um POP ao solicitador. No entanto, pode ter um efeito na quantidade de tráfego de revalidação enviado dos POPs para o servidor de origem.
- Configure esta funcionalidade por:
    - Selecionando o código de estado para o qual será aplicada uma idade máxima interna.
    - Especificando um valor inteiro e selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas, etc.). Este valor define o intervalo de idade máxima do pedido.

- A definição da unidade de tempo para "Desligar" desativa esta função. Um intervalo interno de idade máxima não será atribuído aos ativos solicitados. Se o cabeçalho original não contiver instruções de cache, então o ativo será cached de acordo com a configuração ativa na função Predefinida Internal Max-Age.

**Comportamento predefinido:** Fora

#### <a name="compatibility"></a>Compatibilidade

Devido à forma como as definições de cache são rastreadas, esta funcionalidade não pode ser associada às seguintes condições de jogo:
- Número as
- Endereço IP do Cliente
- Parâmetro de cookies
- Parameter de biscoitos Regex
- País
- Dispositivo
- Nome de borda
- Domínio de referimento
- Pedido cabeçalho literal
- Pedido de cabeçalho Regex
- Pedir Header Wildcard
- Método de Pedido
- Regime de Pedidos
- Url Consulta Literal
- URL Consulta Regex
- URL Consulta Wildcard
- Parâmetro de consulta de URL

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>Suporte H.264 (HTTP Progressive Download)

**Finalidade:** Determina os tipos de formatos de ficheiroH 264 que podem ser utilizados para transmitir conteúdo.

Informação-chave:

- Defina um conjunto de extensões de nome de ficheiro H.264 permitidas na opção Extensões de Ficheiros. A opção de extensões de ficheiros irá anular o comportamento predefinido. Mantenha o suporte MP4 e F4V, incluindo as extensões de nome de ficheiro ao definir esta opção.
- Inclua um período em que especifique cada extensão de nome de ficheiro (por exemplo, _.mp4_, _.f4v_).

**Comportamento predefinido:** HTTP Progressive Download suporta os meios MP4 e F4V por padrão.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Honor No-Cache Request

**Finalidade:** Determina se os pedidos de não cache de um cliente HTTP serão encaminhados para o servidor de origem.

Um pedido de não cache ocorre quando o cliente HTTP envia um `Cache-Control: no-cache` e/ou `Pragma: no-cache` cabeçalho no pedido HTTP.

Valor|Resultado
--|--
Ativado|Permite que os pedidos de não cache de um cliente HTTP sejam encaminhados para o servidor de origem, e o servidor de origem devolverá os cabeçalhos de resposta e o corpo através do POP de volta ao cliente HTTP.
Desativado|Restaura o comportamento padrão. O comportamento padrão é evitar que os pedidos de não cache sejam encaminhados para o servidor de origem.

Para todo o tráfego de produção, é altamente recomendado deixar esta funcionalidade no seu estado de desativação padrão. Caso contrário, os servidores de origem não serão protegidos dos utilizadores finais que podem inadvertidamente desencadear muitos pedidos de não cache quando refrescar páginas web, ou dos muitos jogadores populares de media que são codificados para enviar um cabeçalho sem cache com cada pedido de vídeo. No entanto, esta funcionalidade pode ser útil para se aplicar a certos diretórios de encenação ou teste de não produção, de modo a permitir que novos conteúdos sejam puxados a pedido do servidor de origem.

O estado de cache que é reportado para um pedido que pode ser encaminhado para um servidor de origem devido a esta funcionalidade é `TCP_Client_Refresh_Miss` . O relatório Cache Statuses, que está disponível no módulo de reporte Core, fornece informações estatísticas pelo estado da cache. Este relatório permite-lhe rastrear o número e a percentagem de pedidos que estão a ser encaminhados para um servidor de origem devido a esta funcionalidade.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Ignore a Origem Sem Cache

**Finalidade:** Determina se o CDN ignorará as seguintes diretivas servidas a partir de um servidor de origem:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Informação-chave:

- Configure esta funcionalidade definindo uma lista de códigos de estado delimitadas pelo espaço para os quais as diretivas acima referidas serão ignoradas.
- O conjunto de códigos de estado válidos para esta funcionalidade são: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, e 505.
- Desative esta função colocando-a num valor em branco.

**Comportamento predefinido:** O comportamento padrão é honrar as diretivas acima referidas.

#### <a name="compatibility"></a>Compatibilidade

Devido à forma como as definições de cache são rastreadas, esta funcionalidade não pode ser associada às seguintes condições de jogo:
- Número as
- Endereço IP do Cliente
- Parâmetro de cookies
- Parameter de biscoitos Regex
- País
- Dispositivo
- Nome de borda
- Domínio de referimento
- Pedido cabeçalho literal
- Pedido de cabeçalho Regex
- Pedir Header Wildcard
- Método de Pedido
- Regime de Pedidos
- Url Consulta Literal
- URL Consulta Regex
- URL Consulta Wildcard
- Parâmetro de consulta de URL

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Ignore ranges não atatisfiáveis

**Finalidade:** Determina a resposta que será devolvida aos clientes quando um pedido gera um código de estado 416 Solicitado não satisfiável.

Por predefinição, este código de estado é devolvido quando o pedido de alcance de byte especificado não pode ser satisfeito por um POP e não foi especificado um campo de cabeçalho de pedido se-Range.

Valor|Resultado
-|-
Ativado|Impede que os POPs respondam a um pedido de byte-range inválido com um código de estado de intervalo não satisfiável 416 solicitado. Em vez disso, os servidores entregarão o ativo solicitado e devolverão um OK de 200 OK ao cliente.
Desativado|Restaura o comportamento padrão. O comportamento padrão é honrar o código de estado 416 solicitado não satisfiável.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Max-Stale interno

**Finalidade:** Controla o tempo normal de validade que um ativo em cache pode ser servido a partir de um POP quando o POP não é capaz de revalidar o ativo em cache com o servidor de origem.

Normalmente, quando o tempo máximo de um ativo expira, o POP enviará um pedido de revalidação para o servidor de origem. O servidor de origem responderá então com um 304 Não Modificado para dar ao POP um novo contrato de arrendamento sobre o ativo em cache, ou então com 200 OK para fornecer ao POP uma versão atualizada do ativo em cache.

Se o POP não conseguir estabelecer uma ligação com o servidor de origem enquanto tenta tal revalidação, então esta funcionalidade Internal Max-Stale controla se, e por quanto tempo, o POP pode continuar a servir o ativo agora velho.

Note que este intervalo de tempo começa quando a idade máxima do ativo expirar, e não quando ocorre a revalidação falhada. Por conseguinte, o período máximo durante o qual um ativo pode ser servido sem revalidação bem sucedida é a quantidade de tempo especificada pela combinação de idade máxima mais o máximo. Por exemplo, se um ativo fosse cacheado às 9:00 com uma idade máxima de 30 minutos e um máximo de 15 minutos, então uma tentativa falhada de revalidação às 9:44 resultaria em um utilizador final recebendo o ativo em cache velho, enquanto uma tentativa falhada de revalidação às 9:46 resultaria no utilizador final recebendo um Timeout Gateway 504.

Qualquer valor configurado para esta funcionalidade é substituído por `Cache-Control: must-revalidate` ou `Cache-Control: proxy-revalidate` cabeçalhos recebidos do servidor de origem. Se algum desses cabeçalhos for recebido do servidor de origem quando um ativo é inicialmente cache, então o POP não servirá um ativo em cache velho. Neste caso, se o POP não conseguir revalidar com a origem quando o intervalo máximo de idade do ativo tiver expirado, o POP devolve um erro de tempo de gateway 504.

Informação-chave:

- Configure esta funcionalidade por:
    - Selecionando o código de estado para o qual será aplicado um maxilar máximo.
    - Especificando um valor inteiro e, em seguida, selecionando a unidade de tempo desejada (por exemplo, segundos, minutos, horas, etc.). Este valor define o maxilar interno que será aplicado.

- A definição da unidade de tempo para "Desligar" desativará esta função. Um ativo em cache não será servido para além do seu tempo normal de validade.

**Comportamento predefinido:** Dois minutos

#### <a name="compatibility"></a>Compatibilidade

Devido à forma como as definições de cache são rastreadas, esta funcionalidade não pode ser associada às seguintes condições de jogo:
- Número as
- Endereço IP do Cliente
- Parâmetro de cookies
- Parameter de biscoitos Regex
- País
- Dispositivo
- Nome de borda
- Domínio de referimento
- Pedido cabeçalho literal
- Pedido de cabeçalho Regex
- Pedir Header Wildcard
- Método de Pedido
- Regime de Pedidos
- Url Consulta Literal
- URL Consulta Regex
- URL Consulta Wildcard
- Parâmetro de consulta de URL

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Corda de consulta de log

**Finalidade:** Determina se uma cadeia de consulta será armazenada juntamente com o URL nos registos de acesso.

Valor|Resultado
-|-
Ativado|Permite o armazenamento de cordas de consulta ao gravar URLs num registo de acesso. Se um URL não contiver uma cadeia de consulta, então esta opção não terá um efeito.
Desativado|Restaura o comportamento padrão. O comportamento padrão é ignorar as cordas de consulta ao gravar URLs num registo de acesso.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Pedidos máximos de manutenção viva

**Finalidade:** Define o número máximo de pedidos de uma ligação Keep-Alive antes de ser fechado.

A fixação do número máximo de pedidos para um valor baixo é desencorajada e pode resultar numa degradação do desempenho.

Informação-chave:

- Especifique este valor como um inteiro inteiro inteiro.
- Não inclua vírgulas ou períodos no valor especificado.

**Valor Predefinido:** 10.000 pedidos

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Modificar cabeçalho de pedido de cliente

**Finalidade:** Cada pedido contém um conjunto de cabeçalhos de pedido que o descrevem. Esta funcionalidade pode:

- Anexar ou substituir o valor atribuído a um cabeçalho de pedido. Se o cabeçalho de pedido especificado não existir, então esta funcionalidade irá adicioná-lo ao pedido.
- Apague um cabeçalho de pedido do pedido.

Os pedidos que são encaminhados para um servidor de origem refletirão as alterações efetuadas por esta funcionalidade.

Uma das seguintes ações pode ser realizada num cabeçalho de pedido:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao fim do valor do cabeçalho do pedido existente.|**Valor do cabeçalho de pedido (cliente):**<br/>Valor1<br/>**Valor do cabeçalho de pedido (motor de regras):**<br/>Valor2 <br/>**Novo valor do cabeçalho do pedido:** <br/>Valor1Valor2
Overwrite|O valor do cabeçalho do pedido será definido para o valor especificado.|**Valor do cabeçalho de pedido (cliente):**<br/>Valor1<br/>**Valor do cabeçalho de pedido (motor de regras):**<br/>Valor2<br/>**Novo valor do cabeçalho do pedido:**<br/> Valor2 <br/>
Eliminar|Elimina o cabeçalho de pedido especificado.|**Valor do cabeçalho de pedido (cliente):**<br/>Valor1<br/>**Modificar a configuração do cabeçalho do pedido do cliente:**<br/>Apague o cabeçalho do pedido em questão.<br/>**Resultado:**<br/>O cabeçalho de pedido especificado não será encaminhado para o servidor de origem.

Informação-chave:

- Certifique-se de que o valor especificado na opção Nome é uma correspondência exata para o cabeçalho de pedido pretendido.
- O caso não é tomado em consideração com o propósito de identificar um cabeçalho. Por exemplo, qualquer uma das seguintes variações do `Cache-Control` nome cabeçalho pode ser utilizada para identificá-lo:
    - cache-control
    - CACHE-CONTROL
    - cache-Control
- Ao especificar um nome cabeçalho, utilize apenas caracteres alfanuméricos, traços ou sublinhados.
- A abater um cabeçalho impedirá que seja encaminhado para um servidor de origem pelos POPs.
- Os seguintes cabeçalhos são reservados e não podem ser modificados por esta característica:
    - encaminhado
    - anfitrião
    - através
    - aviso
    - x-forward-for
    - Todos os nomes do cabeçalho que começam com "x-ec" estão reservados.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Modificar cabeçalho de resposta ao cliente

Cada resposta contém um conjunto de cabeçalhos de resposta que a descrevem. Esta funcionalidade pode:

- Anexar ou substituir o valor atribuído a um cabeçalho de resposta. Se o cabeçalho de resposta especificado não existir, então esta função irá adicioná-lo à resposta.
- Apague um cabeçalho de resposta da resposta.

Por predefinição, os valores do cabeçalho de resposta são definidos por um servidor de origem e pelos POPs.

Uma das seguintes ações pode ser realizada num cabeçalho de resposta:

Opção|Descrição|Exemplo
-|-|-
Acrescentar|O valor especificado será adicionado ao fim do valor do cabeçalho de resposta existente.|**Valor do cabeçalho de resposta (cliente):**<br />Valor1<br/>**Valor do cabeçalho de resposta (motor de regras):**<br/>Valor2<br/>**Novo valor do cabeçalho de resposta:**<br/>Valor1Valor2
Overwrite|O valor do cabeçalho de resposta será definido para o valor especificado.|**Valor do cabeçalho de resposta (cliente):**<br/>Valor1<br/>**Valor do cabeçalho de resposta (motor de regras):**<br/>Valor2 <br/>**Novo valor do cabeçalho de resposta:**<br/>Valor2 <br/>
Eliminar|Elimina o cabeçalho de resposta especificado.|**Valor do cabeçalho de resposta (cliente):**<br/>Valor1<br/>**Modificar a configuração do cabeçalho de resposta ao cliente:**<br/>Apague o cabeçalho de resposta em questão.<br/>**Resultado:**<br/>O cabeçalho de resposta especificado não será reencaminhado para o solicitador.

Informação-chave:

- Certifique-se de que o valor especificado na opção Nome é uma correspondência exata para o cabeçalho de resposta desejado.
- O caso não é tomado em consideração com o propósito de identificar um cabeçalho. Por exemplo, qualquer uma das seguintes variações do `Cache-Control` nome cabeçalho pode ser utilizada para identificá-lo:
    - cache-control
    - CACHE-CONTROL
    - cache-Control
- A apagar um cabeceamento impede que seja encaminhado para o solicitador.
- Os seguintes cabeçalhos são reservados e não podem ser modificados por esta característica:
    - aceitar-codificar
    - idade
    - ligação
    - codificação de conteúdo
    - comprimento do conteúdo
    - gama de conteúdos
    - data
    - servidor
    - trailer
    - transferência-codificação
    - atualizar
    - variar
    - através
    - aviso
    - Todos os nomes do cabeçalho que começam com "x-ec" estão reservados.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Partilha parcial de cache

**Finalidade:** Determina se um pedido pode gerar conteúdo parcialmente cached.

Esta cache parcial pode então ser utilizada para satisfazer novos pedidos para esse conteúdo até que o conteúdo solicitado esteja totalmente em cache.

Valor|Resultado
-|-
Ativado|Os pedidos podem gerar conteúdo parcialmente emcache.
Desativado|Os pedidos só podem gerar uma versão totalmente emcachedo do conteúdo solicitado.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Conteúdo Pré-validado em Cached

**Finalidade:** Determina se o conteúdo em cache será elegível para revalidação antecipada antes do seu TTL expirar.

Defina o período de tempo anterior à expiração do TTL do conteúdo solicitado durante o qual será elegível para revalidação antecipada.

Informação-chave:

- A seleção "Off" como a unidade de tempo requer a revalidação após a expiração do TTL do conteúdo em cache. O tempo não deve ser especificado e ignorado.

**Comportamento predefinido:** Fora, fora. A revalidação só pode ocorrer depois de expirada a TTL do conteúdo em cache.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Cabeçalhos Especiais proxy

**Finalidade:** Define o conjunto de [cabeçalhos de pedido http específicos](cdn-verizon-http-headers.md) de Verizon que serão encaminhados de um POP para um servidor de origem.

Informação-chave:

- Cada cabeçalho de pedido específico da CDN definido nesta funcionalidade é encaminhado para um servidor de origem. Os cabeçalhos excluídos não são reencaminhados.
- Para evitar que um cabeçalho de pedido específico da CDN seja reencaminhado, remova-o da lista separada pelo espaço no campo da lista de cabeçalhos.

Os seguintes cabeçalhos HTTP estão incluídos na lista predefinida:
- Via
- X-Forward-For
- X-Forwarded-Proto
- X-Host
- X-Midgress
- Lista X-Gateway
- X-EC-Name
- Anfitrião

**Comportamento predefinido:** Todos os cabeçalhos de pedido específicos da CDN serão encaminhados para o servidor de origem.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Atualizar ficheiros de cache de zero byte

**Finalidade:** Determina como o pedido de um cliente HTTP para um ativo de cache de 0 byte é tratado pelos POPs.

Os valores válidos são:

Valor|Resultado
--|--
Ativado|Faz com que o POP realhe o ativo do servidor de origem.
Desativado|Restaura o comportamento padrão. O comportamento padrão é servir ativos de cache válidos mediante pedido.

Esta funcionalidade não é necessária para a correta entrega de cache e conteúdo, mas pode ser útil como uma sucução. Por exemplo, geradores de conteúdo dinâmicos em servidores de origem podem inadvertidamente resultar em respostas de 0 byte saem para os POPs. Estes tipos de respostas são tipicamente cached pelos POPs. Se você sabe que uma resposta de 0 byte nunca é uma resposta válida para tal conteúdo, esta funcionalidade pode impedir que este tipo de ativos sejam servidos aos seus clientes.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Definir códigos de estado cacheable

**Finalidade:** Define o conjunto de códigos de estado que podem resultar em conteúdo em cache.

Por predefinição, o cache só está ativado para 200 respostas OK.

Defina um conjunto delimitado rés-do-espaço dos códigos de estado pretendidos.

Informação-chave:

- Ative a função Ignore Origin No-Cache. Se esta funcionalidade não estiver ativada, as respostas não-200 OK podem não ser colocadas em cache.
- O conjunto de códigos de estado válidos para esta funcionalidade são: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504, e 505.
- Esta função não pode ser utilizada para desativar o cache para respostas que gerem um código de estado de 200 OK.

**Comportamento predefinido:** O caching só está habilitado para respostas que gerem um código de estado de 200 OK.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Definir cabeçalho personalizado IP do cliente

**Finalidade:** Adiciona um cabeçalho personalizado que identifica o cliente solicitado por endereço IP ao pedido.

A opção de nome Header define o nome do cabeçalho de pedido personalizado onde o endereço IP do cliente está armazenado.

Esta funcionalidade permite que um servidor de origem do cliente descubra os endereços IP do cliente através de um cabeçalho de pedido personalizado. Se o pedido for servido a partir de cache, então o servidor de origem não será informado do endereço IP do cliente. Por isso, recomenda-se que esta funcionalidade seja utilizada com ativos que não estejam em cache.

Certifique-se de que o nome do cabeçalho especificado não corresponde a nenhum dos seguintes nomes:

- Nomes padrão de cabeçalho de pedido. Uma lista de nomes padrão do cabeçalho pode ser encontrada no [RFC 2616](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nomes reservados do cabeçalho:
    - encaminhado-para
    - anfitrião
    - variar
    - através
    - aviso
    - x-forward-for
    - Todos os nomes do cabeçalho que começam com "x-ec" estão reservados.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Entrega de conteúdo estomado no erro

**Finalidade:** Determina se o conteúdo em cache expirado será entregue quando ocorrer um erro durante a revalidação da cache ou quando recuperar o conteúdo solicitado do servidor de origem do cliente.

Valor|Resultado
-|-
Ativado|O conteúdo do stale é servido ao solicitado quando ocorre um erro durante uma ligação a um servidor de origem.
Desativado|O erro do servidor de origem é encaminhado para o solicitador.

**Comportamento predefinido:** Deficientes

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Stale Enquanto Revalida

**Finalidade:** Melhora o desempenho, permitindo que os POPs sirvam conteúdo estonteado ao solicitador enquanto ocorre a revalidação.

Informação-chave:

- O comportamento desta funcionalidade varia de acordo com a unidade de tempo selecionada.
    - **Unidade do Tempo:** Especifique um comprimento de tempo e selecione uma unidade de tempo (por exemplo, Segundos, Minutos, Horas, etc.) para permitir a entrega de conteúdo estorado. Este tipo de configuração permite ao CDN prolongar o tempo que pode entregar conteúdo antes de necessitar de validação de acordo com a seguinte fórmula: **TTL**  +  **Stale While Revalidate Time**
    - **Fora:** Selecione "Off" para exigir a revalidação antes de ser servido um pedido de conteúdo estragado.
        - Não especifique um período de tempo, uma vez que é inaplicável e será ignorado.

**Comportamento predefinido:** Fora, fora. A revalidação deve ter lugar antes de o conteúdo solicitado poder ser servido.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token Auth

**Finalidade:** Determina se a autenticação baseada em Token será aplicada a um pedido.

Se a autenticação baseada em Token estiver ativada, então apenas os pedidos que forneçam um token encriptado e cumpram os requisitos especificados por esse token serão honrados.

A chave de encriptação que é usada para encriptar e desencriptar valores de token é determinada pela Chave Primária e pelas opções de Chave de Reserva na página Token Auth. Tenha em mente que as chaves de encriptação são específicas da plataforma.

**Comportamento predefinido:** Deficiente.

Esta função tem precedência sobre a maioria das funcionalidades, com exceção da função URL Rewrite.

Valor | Resultado
------|---------
Ativado | Protege o conteúdo solicitado com autenticação baseada em token. Apenas os pedidos de clientes que forneçam um símbolo válido e cumpram os seus requisitos serão honrados. As transações FTP estão excluídas da autenticação baseada em token.
Desativado| Restaura o comportamento padrão. O comportamento predefinido é permitir que a configuração de autenticação baseada em Token determine se um pedido será garantido.

#### <a name="compatibility"></a>Compatibilidade

Não utilize Token Auth com uma condição de correspondência always.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Código de Negação token Auth

**Finalidade:** Determina o tipo de resposta que será devolvida a um utilizador quando um pedido é negado devido à autenticação baseada em token.

Os códigos de resposta disponíveis estão listados na tabela seguinte.

Código de Resposta|Nome da resposta|Descrição
-------------|-------------|--------
301|Movido permanentemente|Este código de estado redireciona utilizadores não autorizados para o URL especificado no cabeçalho de Localização.
302|Localizado|Este código de estado redireciona utilizadores não autorizados para o URL especificado no cabeçalho de Localização. Este código de estado é o método padrão da indústria para realizar um redirecionamento.
307|Redirecionamento Temporário|Este código de estado redireciona utilizadores não autorizados para o URL especificado no cabeçalho de Localização.
401|Não autorizado|Combinar este código de estado com o cabeçalho de resposta WWW-Authenticate permite-lhe solicitar a autenticação a um utilizador.
403|Proibido|Esta mensagem é a mensagem padrão 403 Proibida que um utilizador não autorizado verá ao tentar aceder a conteúdos protegidos.
404|Arquivo não encontrado|Este código de estado indica que o cliente HTTP foi capaz de comunicar com o servidor, mas o conteúdo solicitado não foi encontrado.

#### <a name="compatibility"></a>Compatibilidade

Não utilize o Código de Negação Token Auth com uma condição de correspondência sempre. Em vez disso, utilize a secção de manipulação de **negação personalizada** na página **Token Auth** do portal **Manage.** Para mais informações, consulte a Segurança dos [ativos da CDN azure com autenticação simbólica](cdn-token-auth.md).

#### <a name="url-redirection"></a>Redirecionamento url

Esta funcionalidade suporta a reorientação do URL para um URL definido pelo utilizador quando está configurado para devolver um código de estado 3xx. Este URL definido pelo utilizador pode ser especificado executando os seguintes passos:

1. Selecione um código de resposta 3xx para a funcionalidade Token Auth Denial Code.
2. Selecione "Localização" da opção Nome opcional do cabeçalho.
3. Delineie a opção Valor do Cabeçalho Opcional para o URL pretendido.

Se um URL não for definido para um código de estado 3xx, então a página de resposta padrão para um código de estado 3xx será devolvida ao utilizador.

A reorientação do URL só é aplicável para códigos de resposta 3xx.

A opção Optional Header Value suporta caracteres alfanuméricos, aspas e espaços.

#### <a name="authentication"></a>Autenticação

Esta funcionalidade suporta a capacidade de incluir o cabeçalho WWW-Authenticate ao responder a um pedido não autorizado de conteúdo protegido pela Autenticação Baseada em Token. Se o cabeçalho WWW-Authenticate tiver sido definido como "básico" na sua configuração, então o utilizador não autorizado será solicitado para credenciais de conta.

A configuração acima pode ser alcançada executando os seguintes passos:

1. Selecione "401" como código de resposta para a função Token Auth Denial Code.
2. Selecione "WWW-Authenticate" na opção Nome opcional do cabeçalho.
3. Detete a opção Opcional de Valor do Cabeçalho para "básico".

O cabeçalho WWW-Authenticate só é aplicável para 401 códigos de resposta.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Token Auth Ignore URL Case

**Finalidade:** Determina se as comparações de URL feitas pela Autenticação Baseada em Token são sensíveis aos casos.

Os parâmetros afetados por esta funcionalidade são:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Os valores válidos são:

Valor|Resultado
---|----
Ativado|Faz com que o POP ignore o caso ao comparar URLs para parâmetros de autenticação baseados em token.
Desativado|Restaura o comportamento padrão. O comportamento padrão é para as comparações de URL para a Autenticação Token ser sensível a casos.

**Comportamento predefinido:** Deficiente.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Parâmetro Token Auth

**Finalidade:** Determina se o parâmetro de corda de autenticação baseado em token deve ser renomeado.

Informação-chave:

- A opção Valor define o nome do parâmetro de corda de consulta através do qual um símbolo pode ser especificado.
- A opção Valor não pode ser definida como "ec_token".
- Certifique-se de que o nome definido na opção Valor contém apenas caracteres URL válidos.

Valor|Resultado
----|----
Ativado|A opção Valor define o nome do parâmetro de corda de consulta através do qual as fichas devem ser definidas.
Desativado|Um símbolo pode ser especificado como um parâmetro de corda de consulta indefinida no URL de pedido.

**Comportamento predefinido:** Deficiente. Um símbolo pode ser especificado como um parâmetro de corda de consulta indefinida no URL de pedido.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>Redirecionamento url

**Finalidade:** Redireciona os pedidos através do cabeçalho de localização.

A configuração desta funcionalidade requer definir as seguintes opções:

Opção|Descrição
-|-
Código|Selecione o código de resposta que será devolvido ao solicitador.
Padrão de & de origem| Estas definições definem um padrão URI de pedido que identifica o tipo de pedidos que podem ser redirecionados. Apenas os pedidos cujo URL satisfaz ambos os seguintes critérios serão redirecionados: <br/> <br/> **Fonte (ou ponto de acesso** ao conteúdo): Selecione um caminho relativo que identifique um servidor de origem. Este caminho é a secção _/XXXX/_ e o seu nome final. <br/><br/> **Fonte (padrão):** Deve ser definido um padrão que identifique pedidos por caminho relativo. Este padrão de expressão regular deve definir um caminho que começa diretamente após o ponto de acesso ao conteúdo previamente selecionado (ver acima). <br/> - Certifique-se de que os critérios de pedido URI (isto é, Padrão Fonte &) previamente definidos não entram em conflito com quaisquer condições de correspondência definidas para esta funcionalidade. <br/> - Especificar um padrão; se usar um valor em branco como padrão, todas as cordas são correspondidas.
Destino| Defina o URL para o qual os pedidos acima referidos serão redirecionados. <br/><br/> Construa dinamicamente este URL usando: <br/> - Um padrão de expressão regular <br/>- [Variáveis HTTP](cdn-http-variables.md) <br/><br/> Substitua os valores capturados no padrão de origem no padrão de destino usando $_n_ onde _n_ identifica um valor pela ordem em que foi capturado. Por exemplo, $1 representa o primeiro valor capturado no padrão de origem, enquanto $2 representa o segundo valor. <br/>

É altamente recomendado usar um URL absoluto. A utilização de um URL relativo pode redirecionar os URLs de CDN para um caminho inválido.

**Cenário de amostragem**

Este exemplo, demonstra como redirecionar um URL CNAME de borda que resolve para este URL CDN base: http: \/ /marketing.azureedge.net/brochures

Os pedidos de qualificação serão redirecionados para este URL CNAME de borda base: http: \/ /cdn.mydomain.com/resources

Esta redirecção de URL pode ser alcançada através da seguinte configuração: ![ Redirecionamento de URL](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Pontos principais:**

- A função DE Redirecionamento URL define os URLs de pedido que serão redirecionados. Como resultado, não são necessárias condições adicionais de jogo. Embora a condição de jogo tenha sido definida como "Sempre", apenas os pedidos que apontam para a pasta "brochuras" sobre a origem do cliente "marketing" serão redirecionados.
- Todos os pedidos correspondentes serão redirecionados para o URL CNAME bordado definido na opção Destino.
    - Cenário de amostra #1:
        - Pedido de amostra (URL CDN): http: \/ /marketing.azureedge.net/brochures/widgets.pdf
        - Solicitar URL (após redirecionamento): http: \/ /cdn.mydomain.com/resources/widgets.pdf  
    - Cenário de amostra #2:
        - Pedido de amostra (URL Edge CNAME): http: \/ /marketing.mydomain.com/brochures/widgets.pdf
        - Solicitar URL (após redirecionamento): http: \/ /cdn.mydomain.com/resources/widgets.pdf Cenário de amostra
    - Cenário de amostra #3:
        - Pedido de amostra (URL Edge CNAME): http: \/ /brochures.mydomain.com/campaignA/final/productC.ppt
        - Solicitar URL (após redirecionamento): http: \/ /cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- A variável do Regime de Pedido (%{scheme}) é alavancada na opção Destino, que garante que o regime do pedido permanece inalterado após a reorientação.
- Os segmentos de URL que foram capturados a partir do pedido são anexados ao novo URL via "$1".

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>Reescrita de URLs

**Finalidade:** Reescreve o URL de pedido.

Informação-chave:

- A configuração desta funcionalidade requer definir as seguintes opções:

Opção|Descrição
-|-
 Padrão de & de origem | Estas definições definem um padrão URI de pedido que identifica o tipo de pedidos que podem ser reescritos. Apenas os pedidos cujo URL satisfaz ambos os seguintes critérios serão reescritos: <br/><br/>  - **Fonte (ou ponto de acesso** ao conteúdo): Selecione um caminho relativo que identifique um servidor de origem. Este caminho é a secção _/XXXX/_ e o seu nome final. <br/><br/> - **Fonte (padrão):** Deve ser definido um padrão que identifique pedidos por caminho relativo. Este padrão de expressão regular deve definir um caminho que começa diretamente após o ponto de acesso ao conteúdo previamente selecionado (ver acima). <br/> Verifique se os critérios de pedido URI (isto é, Padrão Fonte &) previamente definidos não entram em conflito com nenhuma das condições de correspondência definidas para esta funcionalidade. Especificar um padrão; se usar um valor em branco como padrão, todas as cordas são correspondidas.
 Destino  |Defina o URL relativo ao qual os pedidos acima referidos serão reescritos por: <br/>    1. Selecionar um ponto de acesso ao conteúdo que identifique um servidor de origem. <br/>    2. Definindo um caminho relativo utilizando: <br/>        - Um padrão de expressão regular <br/>        - [Variáveis HTTP](cdn-http-variables.md) <br/> <br/> Substitua os valores capturados no padrão de origem no padrão de destino usando $_n_ onde _n_ identifica um valor pela ordem em que foi capturado. Por exemplo, $1 representa o primeiro valor capturado no padrão de origem, enquanto $2 representa o segundo valor.

 Esta funcionalidade permite que os POPs reescrevam o URL sem realizar um redirecionamento tradicional. Ou seja, o solicitor recebe o mesmo código de resposta que se o URL reescrito tivesse sido solicitado.

**Cenário da amostra 1**

Este exemplo demonstra como redirecionar um URL CNAME de borda que resolve para este URL CDN base: http: \/ /marketing.azureedge.net/brochures/

Os pedidos de qualificação serão redirecionados para este URL CNAME de borda base: http: \/ /MyOrigin.azureedge.net/resources/

Esta redirecção de URL pode ser alcançada através da seguinte configuração: ![ Redirecionamento de URL](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Cenário da amostra 2**

Este exemplo demonstra como redirecionar um URL CNAME de borda da MAIÚSta para minúscula utilizando expressões regulares.

Esta redirecção de URL pode ser alcançada através da seguinte configuração: ![ Redirecionamento de URL](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Pontos principais:**

- A função URL Rewrite define os URLs de pedido que serão reescritos. Como resultado, não são necessárias condições adicionais de jogo. Embora a condição de jogo tenha sido definida como "Sempre", apenas os pedidos que apontam para a pasta "brochuras" na origem do cliente "marketing" serão reescritos.

- Os segmentos de URL que foram capturados a partir do pedido são anexados ao novo URL via "$1".

#### <a name="compatibility"></a>Compatibilidade

Esta funcionalidade inclui critérios de correspondência que devem ser cumpridos antes de poder ser aplicado a um pedido. A fim de evitar a criação de critérios de jogo contraditórios, esta função é incompatível com as seguintes condições de jogo:

- Número as
- Origem CDN
- Endereço IP do Cliente
- Origem do Cliente
- Regime de Pedidos
- Diretório de caminhos URL
- Extensão do caminho do URL
- Nome de ficheiro de caminho URL
- URL Caminho Literal
- URL Path Regex
- URL Path Wildcard
- Url Consulta Literal
- Parâmetro de consulta de URL
- URL Consulta Regex
- URL Consulta Wildcard

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Variável do utilizador

**Finalidade:** Apenas para uso interno.

[De volta ao topo](#azure-cdn-from-verizon-premium-rules-engine-features)
## <a name="next-steps"></a>Passos seguintes

- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Condições de correspondência do motor de regras](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Sobrepor o comportamento do HTTP usando o motor de regras](cdn-verizon-premium-rules-engine.md)
- [Visão geral do CDN azure](cdn-overview.md)
