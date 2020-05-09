---
title: Assegurar ativos da CDN azure com autenticação simbólica. Microsoft Docs
description: Aprenda a usar a autenticação simbólica para garantir o acesso aos seus ativos de CDN Azure.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 491f413f9bf189b1a46d04042fd7223a47af1f24
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2020
ms.locfileid: "82929133"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Assegurar ativos da Azure CDN com autenticação simbólica

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

A autenticação simbólica é um mecanismo que permite evitar que a Rede de Entrega de Conteúdos Azure (CDN) sirva ativos a clientes não autorizados. A autenticação token é normalmente feita para evitar *o hotlinking* de conteúdos, em que um site diferente, como um quadro de mensagens, utiliza os seus ativos sem permissão. Hotlinking pode ter um impacto nos custos de entrega do seu conteúdo. Ao permitir a autenticação simbólica no CDN, os pedidos são autenticados pelo servidor de borda CDN antes de o CDN entregar o conteúdo. 

## <a name="how-it-works"></a>Como funciona

A autenticação simbólica verifica que os pedidos são gerados por um site fidedigno, exigindo pedidos para conter um valor simbólico que detém informações codificadas sobre o solicitador. O conteúdo só é servido a um solicitador se as informações codificadas satisfizerem os requisitos; caso contrário, os pedidos são negados. Pode configurar os requisitos utilizando um ou mais dos seguintes parâmetros:

- País: Permitir ou negar pedidos originários dos países/regiões especificados pelo seu [código de país](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Permitir apenas pedidos que correspondam ao ativo ou caminho especificado.
- Anfitrião: Permitir ou negar pedidos que utilizem os anfitriões especificados no cabeçalho do pedido.
- Remetente: Permitir ou negar o pedido do referido remetente.
- Endereço IP: Permitir apenas pedidos originários de endereço IP específico ou sub-rede IP.
- Protocolo: Permitir ou negar pedidos com base no protocolo utilizado para solicitar o conteúdo.
- Tempo de validade: Atribuir uma data e um período de tempo para garantir que um link permanece válido apenas por um período de tempo limitado.

Para mais informações, consulte os exemplos de configuração detalhados de cada parâmetro na configuração da [autenticação simbólica](#setting-up-token-authentication).

>[!IMPORTANT] 
> Se a autorização de ficha estiver ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que pode ser usado para consulta de cache de cordas. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama de fluxo de trabalho seguinte descreve como o CDN usa a autenticação simbólica para trabalhar com a sua aplicação web.

![Fluxo de trabalho de autenticação simbólica CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação simbólica no ponto final da CDN
    
O fluxograma seguinte descreve como o Azure CDN valida um pedido de cliente quando a autenticação simbólica é configurada no ponto final da CDN.

![Lógica de validação de fichas CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configuração da autenticação simbólica

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao seu perfil CDN e, em seguida, selecione **Gerir** para lançar o portal suplementar.

    ![Perfil CDN Gerir botão](./media/cdn-token-auth/cdn-manage-btn.png)

2. Paire sobre **HTTP Large**e, em seguida, selecione **Token Auth** no flyout. Em seguida, pode configurar a chave de encriptação e os parâmetros de encriptação da seguinte forma:

   1. Crie uma ou mais chaves de encriptação. Uma chave de encriptação é sensível a casos e pode conter qualquer combinação de caracteres alfanuméricos. Quaisquer outros tipos de caracteres, incluindo espaços, não são permitidos. O comprimento máximo é de 250 caracteres. Para garantir que as suas chaves de encriptação são aleatórias, recomenda-se que as crie utilizando a [ferramenta OpenSSL](https://www.openssl.org/). 

      A ferramenta OpenSSL tem a seguinte sintaxe:

      ```rand -hex <key length>```

      Por exemplo:

      ```OpenSSL> rand -hex 32``` 

      Para evitar o tempo de inatividade, crie uma chave primária e de reserva. Uma chave de reserva proporciona acesso ininterrupto ao seu conteúdo quando a sua chave principal está a ser atualizada.
    
   2. Introduza uma chave de encriptação única na caixa chave primária e **introduza** opcionalmente uma chave de reserva na caixa **'Chave de cópia** de segurança'.

   3. Selecione a versão de encriptação mínima para cada tecla a partir da sua lista de versão de **encriptação mínima** e, em seguida, selecione **Update**:
      - **V2**: Indica que a chave pode ser utilizada para gerar fichas de 2.0 e 3.0. Utilize esta opção apenas se estiver a transitar de uma chave de encriptação da versão 2.0 para uma chave versão 3.0.
      - **V3**: (Recomendado) Indica que a tecla só pode ser utilizada para gerar fichas da versão 3.0.

      ![Chave de configuração de auth token CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Utilize a ferramenta de encriptação para configurar parâmetros de encriptação e gerar um símbolo. Com a ferramenta de encriptação, pode permitir ou negar pedidos com base no tempo de validade, país/região, remetente, protocolo e IP do cliente (em qualquer combinação). Embora não exista um limite para o número e combinação de parâmetros que podem ser combinados para formar um símbolo, o comprimento total de um símbolo é limitado a 512 caracteres. 

      ![Ferramenta de encriptação CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Introduza valores para um ou mais dos seguintes parâmetros de encriptação na secção **Encrypt Tool:** 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nome do parâmetro</th> 
      >   <th>Descrição</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Atribui um tempo de validade a um símbolo, após o qual o símbolo expira. Os pedidos apresentados após o prazo de validade são negados. Este parâmetro utiliza uma marca de tempo Unix, que se baseia no número `1/1/1970 00:00:00 GMT`de segundos desde a época unix padrão de . (Pode utilizar ferramentas online para converter entre o tempo padrão e o tempo Unix.)> 
      >    Por exemplo, se quiser que o `12/31/2016 12:00:00 GMT`símbolo expire a , `1483185600`insira o valor da marca Unix, . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Permite-lhe adaptar fichas a um determinado ativo ou caminho. Restringe o acesso a pedidos cujo URL começa com um caminho relativo específico. Os URLs são sensíveis aos casos. Insere vários caminhos separando cada caminho com uma vírem; não adicionar espaços. Dependendo dos seus requisitos, pode configurar diferentes valores para fornecer diferentes níveis de acesso.> 
      >    Por exemplo, para `http://www.mydomain.com/pictures/city/strasbourg.png`o URL, estes pedidos são permitidos para os seguintes valores de entrada: 
      >    <ul>
      >       <li>Valor `/`de entrada : Todos os pedidos são permitidos.</li>
      >       <li>Valor `/pictures`de entrada, são permitidos os seguintes pedidos: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Valor `/pictures/`de entrada : Só `/pictures/` são permitidos pedidos que contenham o caminho. Por exemplo, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Valor `/pictures/city/strasbourg.png`de entrada : Apenas são permitidos pedidos para este caminho específico e ativo.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Só permite pedidos originários de um ou mais países/regiões especificados. Os pedidos originários de todos os outros países/regiões são negados. Utilize um [código de país ISO 3166](/previous-versions/azure/mt761717(v=azure.100)) de duas letras para cada país e separe cada um com uma vírposta; não adicionar um espaço. Por exemplo, se quiser permitir o acesso a partir `US,FR`dos Estados Unidos e da França, entre .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nega pedidos originários de um ou mais países/regiões especificados. São permitidos pedidos originários de todos os outros países/regiões. A implementação é a mesma que o parâmetro <b>ec_country_allow.</b> Se um código de país estiver presente nos parâmetros <b>ec_country_allow</b> e <b>ec_country_deny,</b> o parâmetro <b>ec_country_allow</b> tem precedência.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Só permite pedidos do referido remetente. Um remetente identifica o URL da página web que está ligado ao recurso que está a ser solicitado. Não inclua o protocolo no valor do parâmetro.> 
      >    São permitidos os seguintes tipos de entrada:
      >    <ul>
      >       <li>Um nome de anfitrião ou um nome de anfitrião e um caminho.</li>
      >       <li>Vários referantes. Para adicionar vários referores, separe cada remetente com uma vírem; não adicionar um espaço. Se especificar um valor de remetente, mas a informação do remetente não é enviada no pedido devido à configuração do navegador, o pedido é negado por predefinição.</li> 
      >       <li>Pedidos com informação de remetente em falta ou em branco. Por padrão, o parâmetro <b>ec_ref_allow</b> bloqueia este tipo de pedidos. Para permitir estes pedidos, introduza o texto, "em falta", ou introduza um valor em branco (utilizando uma vírgula de rasto).</li> 
      >       <li>Subdomínios. Para permitir subdomínios, introduza\*um asterisco ( ). Por exemplo, para permitir que `contoso.com`todos `*.contoso.com`os subdomínios de , insira .</li>
      >    </ul> 
      >    Por exemplo, para permitir o `www.contoso.com`acesso a pedidos `contoso2.com`de todos os subdomínios sob `www.contoso.com,*.contoso.com,missing`, e pedidos com remetedores em branco ou em falta, insira .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Nega os pedidos do referido remetente. A implementação é a mesma que o parâmetro <b>ec_ref_allow.</b> Se um remetente estiver presente nos parâmetros <b>ec_ref_allow</b> e <b>ec_ref_deny,</b> o parâmetro <b>ec_ref_allow</b> tem precedência.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Apenas permite pedidos do protocolo especificado. Valores `http`válidos são, `https`ou `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Nega os pedidos do protocolo especificado. A implementação é a mesma que o parâmetro <b>ec_proto_allow.</b> Se houver um protocolo presente nos parâmetros <b>ec_proto_allow</b> e <b>ec_proto_deny,</b> o parâmetro <b>ec_proto_allow</b> tem precedência.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Restringe o acesso ao endereço IP do solicitador especificado. Tanto o IPV4 como o IPV6 são apoiados. Pode especificar um único endereço IP de pedido ou endereços IP associados a uma sub-rede específica. Por exemplo, `11.22.33.0/22` permite pedidos de endereços IP 11.22.32.1 a 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Depois de ter terminado de introduzir valores de parâmetros de encriptação, selecione uma chave para encriptar (se tiver criado uma chave primária e de cópia de segurança) a partir da lista **De Encriptação.**
    
   6. Selecione uma versão de encriptação da lista de **versão encriptada:** **V2** para a versão 2 ou **V3** para a versão 3 (recomendado). 

   7. Selecione **Encriptar** para gerar o símbolo.

      Após a geração do token, é apresentado na caixa **Token Gerada.** Para utilizar o símbolo, acomode-o como uma corda de consulta até ao fim do ficheiro no seu percurso url. Por exemplo, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcionalmente, teste o seu símbolo com a ferramenta de desencriptação para que possa ver os parâmetros do seu token. Colhe o valor simbólico na caixa **Token tocrypt.** Selecione a chave de encriptação para utilizar na lista **Key To Decrypt** e, em seguida, selecione **Decrypt**.

      Após a desencriptação do símbolo, os seus parâmetros são apresentados na caixa de **parâmetros originais.**

   9. Opcionalmente, personalize o tipo de código de resposta que é devolvido quando um pedido é negado. Selecione **Ativado**e, em seguida, selecione o código de resposta da lista do Código de **Resposta.** **O nome do cabeçalho** é automaticamente definido para **Localização**. Selecione **Guardar** para implementar o novo código de resposta. Para determinados códigos de resposta, também deve introduzir o URL da sua página de erro na caixa **'Valor Cabeçalho'.** O código de resposta **403** (Proibido) é selecionado por defeito. 

3. Em **HTTP Large,** selecione **Rules Engine**. Utiliza o motor de regras para definir caminhos para aplicar a funcionalidade, ativar a funcionalidade de autenticação simbólica e permitir capacidades adicionais relacionadas com a autenticação simbólica. Para mais informações, consulte [Regras de referência do motor](cdn-rules-engine-reference.md).

   1. Selecione uma regra existente ou crie uma nova regra para definir o ativo ou caminho para o qual pretende aplicar autenticação simbólica. 
   2. Para ativar a autenticação simbólica numa regra, selecione **[Token Auth](cdn-verizon-premium-rules-engine-reference-features.md#token-auth)** da lista **De Características** e, em seguida, selecione **Ativado**. Selecione **Atualizar** se estiver a atualizar uma regra ou **adicionar** se estiver a criar uma regra.
        
      ![A autenticação simbólica do motor de regras cdN permite o exemplo](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. No motor de regras, também pode ativar funcionalidades adicionais relacionadas com a autenticação de token. Para ativar qualquer uma das seguintes funcionalidades, selecione-as na lista **de Funcionalidades** e, em seguida, selecione **Ativado**.
    
   - **[Token Auth Denial Code](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-denial-code)**: Determina o tipo de resposta que é devolvida a um utilizador quando um pedido é negado. As regras estabelecidas aqui sobrepõem-se ao código de resposta definido na secção de manipulação de **negação personalizada** na página de autenticação baseada em token.

   - **[Token Auth Ignore URL Case](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Determina se o URL usado para validar o token é sensível a casos.

   - **[Token Auth Parameter](cdn-verizon-premium-rules-engine-reference-features.md#token-auth-parameter)**: Rebatiza o parâmetro de corda de auth auth token que aparece no URL solicitado. 
        
     ![CDN regras motor de autenticação token exemplo](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Pode personalizar o seu token acedendo ao código fonte no [GitHub](https://github.com/VerizonDigital/ectoken).
   As línguas disponíveis incluem:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Funcionalidades do CDN azure e preços do fornecedor

Para obter informações sobre funcionalidades, consulte [as funcionalidades do produto Azure CDN](cdn-features.md). Para obter informações sobre preços, consulte [os preços da Rede](https://azure.microsoft.com/pricing/details/cdn/)de Entrega de Conteúdos .
