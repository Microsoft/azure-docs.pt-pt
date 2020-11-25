---
title: Assegurar os ativos da Azure CDN com autenticação simbólica Microsoft Docs
description: Aprenda a usar a autenticação simbólica para garantir o acesso aos seus ativos Azure CDN.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95978047"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Assegurar os ativos da Azure CDN com autenticação simbólica

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

A autenticação token é um mecanismo que permite evitar que a Rede de Entrega de Conteúdos Azure (CDN) sirva ativos a clientes não autorizados. A autenticação token é normalmente feita para evitar *o hotlinking* de conteúdos, no qual um site diferente, como um quadro de mensagens, utiliza os seus ativos sem permissão. O hotlinking pode ter um impacto nos custos de entrega de conteúdos. Ao permitir a autenticação simbólica no CDN, os pedidos são autenticados pelo servidor de borda CDN antes que o CDN entregue o conteúdo. 

## <a name="how-it-works"></a>Como funciona

A autenticação token verifica que os pedidos são gerados por um site fidedigno, exigindo pedidos para conter um valor simbólico que detenha informações codificadas sobre o solicitador. O conteúdo só é servido a um solicitador se a informação codificada satisfaça os requisitos; caso contrário, os pedidos são negados. Pode configurar os requisitos utilizando um ou mais dos seguintes parâmetros:

- País/Região: Permitir ou negar pedidos originários dos países/regiões especificados pelo seu [código país/região](/previous-versions/azure/mt761717(v=azure.100)).
- URL: Permitir apenas pedidos que correspondam ao ativo ou caminho especificado.
- Anfitrião: Permitir ou negar pedidos que utilizem os anfitriões especificados no cabeçalho do pedido.
- Remetente: Permitir ou negar pedido do remetente especificado.
- Endereço IP: Permitir apenas pedidos originários de endereço IP específico ou sub-rede IP específico.
- Protocolo: Permitir ou negar pedidos com base no protocolo utilizado para solicitar o conteúdo.
- Prazo de validade: Atribua uma data e um período de tempo para garantir que um link permanece válido apenas por um período de tempo limitado.

Para obter mais informações, consulte os exemplos de configuração detalhada para cada parâmetro na [configuração da autenticação simbólica](#setting-up-token-authentication).

>[!IMPORTANT] 
> Se a autorização de token estiver ativada para qualquer caminho nesta conta, o modo cache padrão é o único modo que pode ser usado para a caching de cordas de consulta. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

## <a name="reference-architecture"></a>Arquitetura de referência

O seguinte diagrama de fluxo de trabalho descreve como o CDN utiliza a autenticação simbólica para trabalhar com a sua aplicação web.

![Fluxo de trabalho de autenticação simbólica CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação de token no ponto final da CDN
    
O seguinte fluxograma descreve como a Azure CDN valida um pedido do cliente quando a autenticação simbólica é configurada no ponto final do CDN.

![Lógica de validação simbólica da CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Criação de autenticação simbólica

1. A partir do [portal Azure,](https://portal.azure.com)navegue até ao seu perfil CDN e, em seguida, selecione **Gerir** para lançar o portal suplementar.

    ![Botão de gestão de perfil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Passe por **HTTP Large**, em seguida, selecione **Token Auth** no flyout. Em seguida, pode configurar a chave de encriptação e os parâmetros de encriptação da seguinte forma:

   1. Crie uma ou mais chaves de encriptação. Uma chave de encriptação é sensível a maiússãos e pode conter qualquer combinação de caracteres alfanuméricos. Não são permitidos outros tipos de caracteres, incluindo espaços. O comprimento máximo é de 250 caracteres. Para garantir que as suas chaves de encriptação são aleatórias, recomenda-se que as crie utilizando a [ferramenta OpenSSL](https://www.openssl.org/). 

      A ferramenta OpenSSL tem a seguinte sintaxe:

      ```rand -hex <key length>```

      Por exemplo:

      ```OpenSSL> rand -hex 32``` 

      Para evitar tempo de inatividade, crie uma chave primária e uma chave de reserva. Uma chave de backup fornece acesso ininterrupto ao seu conteúdo quando a sua chave primária está a ser atualizada.
    
   2. Introduza uma chave de encriptação única na caixa **chave primária** e introduza opcionalmente uma chave de backup na caixa de chave de cópia **de segurança.**

   3. Selecione a versão de encriptação mínima para cada chave a partir da sua lista **de versão de encriptação mínima** e, em seguida, selecione **Update**:
      - **V2**: Indica que a chave pode ser utilizada para gerar tokens das versões 2.0 e 3.0. Utilize esta opção apenas se estiver a transitar de uma chave de encriptação da versão 2.0 para uma tecla versão 3.0.
      - **V3**: (Recomendado) Indica que a chave só pode ser utilizada para gerar tokens da versão 3.0.

      ![Chave de configuração de auth token CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Utilize a ferramenta encriptada para configurar parâmetros de encriptação e gerar um token. Com a ferramenta encriptada, pode permitir ou negar pedidos com base no tempo de validade, país/região, remetente, protocolo e IP do cliente (em qualquer combinação). Embora não haja limite para o número e combinação de parâmetros que podem ser combinados para formar um símbolo, o comprimento total de um símbolo é limitado a 512 caracteres. 

      ![Ferramenta de encriptação CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Introduza valores para um ou mais dos seguintes parâmetros de encriptação na secção **Ferramenta Encriptada:** 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nome do parâmetro</th> 
      >   <th>Descrição</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Atribui um prazo de validade a um token, após o qual o token expira. Os pedidos apresentados após o prazo de validade são negados. Este parâmetro utiliza uma contrao tempotadizada Unix, que se baseia no número de segundos desde a época padrão unix de `1/1/1970 00:00:00 GMT` . (Pode utilizar ferramentas online para converter entre o tempo padrão e o tempo Unix.)> 
      >    Por exemplo, se pretender que o token expire em `12/31/2016 12:00:00 GMT` , insira o valor do semósta de tempo Unix, `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Permite-lhe adaptar tokens a um determinado ativo ou caminho. Restringe o acesso a pedidos cujo URL comece com um caminho relativo específico. Os URLs são sensíveis a casos. Insira vários caminhos separando cada caminho com uma vírgula; não adicione espaços. Dependendo dos seus requisitos, pode configurar diferentes valores para fornecer diferentes níveis de acesso.> 
      >    Por exemplo, para o `http://www.mydomain.com/pictures/city/strasbourg.png` URL, estes pedidos são permitidos para os seguintes valores de entrada: 
      >    <ul>
      >       <li>Valor de entrada `/` : Todos os pedidos são permitidos.</li>
      >       <li>Valor de `/pictures` entrada, são permitidos os seguintes pedidos: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Valor de entrada `/pictures/` : São permitidos apenas pedidos que contenham o `/pictures/` percurso. Por exemplo, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Valor de entrada `/pictures/city/strasbourg.png` : Apenas são permitidos pedidos para este caminho e ativo específicos.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Só permite pedidos originários de um ou mais países/regiões especificados. São negados pedidos originários de todos os outros países/regiões. Utilizar um [código iso 3166 de dois](/previous-versions/azure/mt761717(v=azure.100)) letras para cada país/região e separar cada um deles com uma vírgula; não adicione um espaço. Por exemplo, se quiser permitir o acesso apenas dos Estados Unidos e da França, `US,FR` entre.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nega pedidos originários de um ou mais países/regiões especificados. São permitidos pedidos originários de todos os outros países/regiões. A implementação é a mesma que o <b>parâmetro ec_country_allow.</b> Se um código país/região estiver presente nos parâmetros <b>ec_country_allow</b> e <b>ec_country_deny,</b> o parâmetro <b>ec_country_allow</b> tem precedência.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Só permite pedidos do remetente especificado. Um remetente identifica o URL da página web que está ligado ao recurso solicitado. Não inclua o protocolo no valor do parâmetro.> 
      >    São permitidos os seguintes tipos de entrada:
      >    <ul>
      >       <li>Um nome de anfitrião ou um nome de anfitrião e um caminho.</li>
      >       <li>Vários remetentes. Para adicionar vários remetentes, separe cada remetente com uma vírgula; não adicione um espaço. Se especificar um valor de reenso, mas as informações do remetente não forem enviadas no pedido devido à configuração do navegador, o pedido é negado por padrão.</li> 
      >       <li>Pedidos com informação de ressoante em falta ou em branco. Por predefinição, o <b>parâmetro ec_ref_allow</b> bloqueia este tipo de pedidos. Para permitir estes pedidos, introduza o texto, "em falta", ou introduza um valor em branco (utilizando uma vírgula).</li> 
      >       <li>Subdomínios. Para permitir subdomínios, introduza um asterisco ( \* ). Por exemplo, para permitir que todos os subdomínios `contoso.com` de, insira `*.contoso.com` .</li>
      >    </ul> 
      >    Por exemplo, para permitir o acesso a pedidos `www.contoso.com` de, todos os subdomínios em `contoso2.com` , e pedidos com referrórios em branco ou em falta, insira `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Nega pedidos do remetente especificado. A implementação é a mesma que o <b>parâmetro ec_ref_allow.</b> Se um remetente estiver presente nos parâmetros <b>ec_ref_allow</b> e <b>ec_ref_deny,</b> o parâmetro <b>ec_ref_allow</b> tem precedência.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Só permite pedidos a partir do protocolo especificado. Valores válidos `http` `https` são, ou `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Nega pedidos do protocolo especificado. A implementação é a mesma que o <b>parâmetro ec_proto_allow.</b> Se um protocolo estiver presente nos parâmetros <b>ec_proto_allow</b> e <b>ec_proto_deny,</b> o parâmetro <b>ec_proto_allow</b> tem precedência.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Restringe o acesso ao endereço IP do solicitador especificado. Tanto o IPV4 como o IPV6 são suportados. Pode especificar um único endereço IP de pedido ou endereços IP associados a uma sub-rede específica. Por exemplo, `11.22.33.0/22` permite pedidos de endereços IP 11.22.32.1 a 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Depois de ter terminado de introduzir os valores dos parâmetros de encriptação, selecione uma chave para encriptar (se criou uma chave primária e uma chave de backup) a partir da lista **De Teclas Para Encriptar.**
    
   6. Selecione uma versão de encriptação da lista **de versão encriptação:** **V2** para a versão 2 ou **V3** para a versão 3 (recomendado). 

   7. **Selecione Encrypt** para gerar o token.

      Depois de gerado o token, é exibido na caixa **Token Gerada.** Para utilizar o token, apencha-o como uma cadeia de consulta até ao fim do ficheiro no seu caminho URL. Por exemplo, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcionalmente, teste o seu token com a ferramenta desencriptação para que possa ver os parâmetros do seu token. Cole o valor simbólico na caixa **token para desencriptar.** Selecione a chave de encriptação para usar na lista **de chave para desencriptar** e, em seguida, selecione **Desencript**.

      Após a desencriptar o token, os seus parâmetros são apresentados na caixa **de Parâmetros Originais.**

   9. Opcionalmente, personalize o tipo de código de resposta que é devolvido quando um pedido é negado. Selecione **Ativado** e, em seguida, selecione o código de resposta da lista **de Código de Resposta.** **O nome do cabeçalho** é automaticamente definido para **localização**. **Selecione Guardar** para implementar o novo código de resposta. Para certos códigos de resposta, também deve introduzir o URL da sua página de erro na caixa **Valore do Cabeçalho.** O código de resposta **403** (Proibido) é selecionado por predefinição. 

3. Em **HTTP Large**, selecione Rules **Engine**. Utiliza o motor de regras para definir caminhos para aplicar a funcionalidade, ativar a função de autenticação simbólica e ativar capacidades adicionais relacionadas com a autenticação token. Para obter mais informações, consulte [a referência do motor regras.](./cdn-verizon-premium-rules-engine-reference.md)

   1. Selecione uma regra existente ou crie uma nova regra para definir o ativo ou caminho para o qual pretende aplicar a autenticação simbólica. 
   2. Para ativar a autenticação simbólica numa regra, selecione **[Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** da lista **de funcionalidades** e, em seguida, selecione **Ativado**. Selecione **Update** se estiver a atualizar uma regra ou **adicionar** se estiver a criar uma regra.
        
      ![CdN regras de autenticação simbólica do motor permitem exemplo](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. No motor de regras, também pode ativar funcionalidades adicionais relacionadas com a autenticação token. Para ativar qualquer uma das seguintes funcionalidades, selecione-a na lista **de Funcionalidades** e, em seguida, selecione **Ativado**.
    
   - **[Token Auth Denial Code](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)**: Determina o tipo de resposta que é devolvida a um utilizador quando um pedido é negado. As regras aqui estabelecidas substituem o código de resposta definido na secção **de Tratamento de Negação Personalizada** na página de autenticação baseada em fichas.

   - **[Token Auth Ignore CASO URL](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)**: Determina se o URL utilizado para validar o token é sensível a casos.

   - **[Parâmetro Token Auth](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)**: Rebatiza o parâmetro de cadeia de consulta de auth simbólico que aparece no URL solicitado. 
        
     ![CDN regras de autenticação de símbolos de símbolos de design](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Pode personalizar o seu token acedendo ao código fonte no [GitHub.](https://github.com/VerizonDigital/ectoken)
   As línguas disponíveis incluem:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>Funcionalidades da Azure CDN e preços do fornecedor

Para obter informações sobre funcionalidades, consulte [as funcionalidades do produto Azure CDN](cdn-features.md). Para obter informações sobre preços, consulte [os preços da Rede de Entrega de Conteúdos.](https://azure.microsoft.com/pricing/details/cdn/)