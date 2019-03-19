---
title: Proteger recursos da CDN do Azure com a autenticação de token | Documentos da Microsoft
description: Saiba como utilizar a autenticação de token para proteger o acesso aos seus recursos do CDN do Azure.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mezha
ms.openlocfilehash: 6ff7b018cea8bedd93d3c9aef3b3250ecf5251b4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58123730"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Proteger recursos da CDN do Azure com a autenticação de token

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Descrição geral

Autenticação de token é um mecanismo que permite-lhe impedir que a rede Azure entrega de conteúdos (CDN) que serve ativos a clientes não autorizados. Autenticação de token é geralmente feita para impedir *hotlinking* de conteúdo, em que um Web site diferente, por exemplo, um quadro de mensagens, utiliza os recursos sem permissão. Hotlinking pode ter um impacto sobre os custos de entrega de conteúdos. Ao ativar a autenticação de token na CDN, os pedidos são autenticados pelo servidor de borda da CDN antes da CDN oferece o conteúdo. 

## <a name="how-it-works"></a>Como funciona

Autenticação de token verifica que os pedidos são gerados por um site confiável, exigindo que os pedidos para conter um valor de token que retenções codificada informações sobre o autor do pedido. Conteúdo é fornecido para um autor de pedido apenas se as informações de codificada cumprem os requisitos; caso contrário, as solicitações serão negadas. Pode configurar os requisitos, utilizando um ou mais dos seguintes parâmetros:

- País: Permitir ou negar pedidos que têm origem nos países/regiões especificados pelo respetivo [indicativo de país](https://msdn.microsoft.com/library/mt761717.aspx).
- URL: Permitir apenas pedidos que correspondam a ativo especificado ou o caminho.
- Host: Permitir ou negar pedidos que utilizam os anfitriões especificados no cabeçalho do pedido.
- Referenciador: Permitir ou negar o pedido a partir do referenciador especificado.
- Endereço IP: Permitir apenas pedidos provenientes do endereço IP específico ou de sub-rede IP.
- Protocolo: Permitir ou negar pedidos com base no protocolo utilizado para pedir o conteúdo.
- Hora de expiração: Atribua um período de data e hora para se certificar de que uma ligação permanece válida apenas por um período de tempo limitado.

Para obter mais informações, veja os exemplos de configuração detalhadas para cada parâmetro na [configurar a autenticação de token](#setting-up-token-authentication).

>[!IMPORTANT] 
> Se a autorização de token está ativada para qualquer caminho nesta conta, o modo de cache padrão é o único modo que pode ser utilizado para colocar em cache de cadeia de caracteres de consulta. Para obter mais informações, consulte [Control Azure CDN caching behavior with query strings](cdn-query-string-premium.md)(Controlar o comportamento de colocação em cache do Azure CDN com cadeias de consulta).

## <a name="reference-architecture"></a>Arquitetura de referência

O diagrama de fluxo de trabalho seguinte descreve como a CDN utiliza a autenticação de token para trabalhar com a sua aplicação web.

![Fluxo de trabalho de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Lógica de validação do token no ponto final da CDN
    
O fluxograma a seguir descreve como a CDN do Azure valida um pedido de cliente quando a autenticação de token está configurada no ponto final da CDN.

![Lógica de validação de token de CDN](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Configurar a autenticação de token

1. Partir do [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN, em seguida, selecione **gerir** para iniciar o portal suplementar.

    ![Botão de gerir do perfil CDN](./media/cdn-token-auth/cdn-manage-btn.png)

2. Coloque o cursor sobre **HTTP grandes**, em seguida, selecione **Token Auth** no submenu da. Pode, em seguida, configurar os parâmetros de encriptação e a chave de encriptação da seguinte forma:

   1. Crie uma ou mais chaves de encriptação. Uma chave de encriptação diferencia maiúsculas de minúsculas e pode conter qualquer combinação de carateres alfanuméricos. Todos os outros tipos de carateres, incluindo espaços, não são permitidos. O comprimento máximo é 250 carateres. Para garantir que suas chaves de encriptação aleatórias, recomenda-se que criá-los utilizando o [ferramenta de OpenSSL](https://www.openssl.org/). 

      A ferramenta de OpenSSL tem a seguinte sintaxe:

      ```rand -hex <key length>```

      Por exemplo:

      ```OpenSSL> rand -hex 32``` 

      Para evitar o tempo de inatividade, crie um site primário e uma chave de cópia de segurança. Uma chave de cópia de segurança fornece um acesso ininterrupto ao seu conteúdo, quando a chave primária está a ser atualizada.
    
   2. Introduza uma chave de encriptação exclusivo no **chave primária** caixa e, opcionalmente, introduza uma chave de cópia de segurança no **chave de cópia de segurança** caixa.

   3. Selecione a versão mínima de encriptação para cada chave da sua **versão mínima de encriptação** lista, em seguida, selecione **atualização**:
      - **V2**: Indica que a chave pode ser utilizada para gerar tokens com a versão 2.0 e 3.0. Utilize esta opção apenas se estão a transitar a partir de uma chave de encriptação de versão herdada 2.0 para uma chave de versão 3.0.
      - **V3**: (Recomendado) Indica que a chave apenas pode ser utilizada para gerar tokens de versão 3.0.

      ![Chave de configuração de autenticação de token de CDN](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. Utilize a ferramenta de criptografar para definir os parâmetros de encriptação e gerar um token. Com a ferramenta de encriptar, pode permitir ou negar pedidos com base na hora de expiração, o país, o referenciador, o protocolo e o IP de cliente (em qualquer combinação). Embora não exista nenhum limite para o número e a combinação de parâmetros que podem ser combinados para formar um token, o comprimento total de um token está limitado a 512 carateres. 

      ![Ferramenta de encriptar de CDN](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Introduza valores para um ou mais dos seguintes parâmetros de encriptação no **encriptar ferramenta** secção: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Nome do parâmetro</th> 
      >   <th>Descrição</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Atribui um prazo de expiração para um token, após o qual o token expira. Pedidos submetidos após a hora de expiração serão negadas. Este parâmetro utiliza um carimbo de Unix, o que se baseia no número de segundos desde a época de Unix padrão de `1/1/1970 00:00:00 GMT`. (Pode utilizar ferramentas online para converter entre horário padrão e a hora Unix.)> 
      >    Por exemplo, se pretender que o token para expirarem `12/31/2016 12:00:00 GMT`, introduza o valor do Unix timestamp, `1483185600`. 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Permite que adapte tokens para um determinado ativo ou o caminho. Restringe o acesso aos pedidos cujo URL começar com um caminho relativo específico. URLs diferenciam maiúsculas de minúsculas. Vários caminhos de entrada, separando cada caminho com uma vírgula; Não adicione espaços. Dependendo dos requisitos, pode configurar valores diferentes para fornecer um nível diferente de acesso.> 
      >    Por exemplo, para o URL `http://www.mydomain.com/pictures/city/strasbourg.png`, estes pedidos são permitidos para os seguintes valores de entrada: 
      >    <ul>
      >       <li>Valor de entrada `/`: Todos os pedidos são permitidos.</li>
      >       <li>Valor de entrada `/pictures`, são permitidos os seguintes pedidos de: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Valor de entrada `/pictures/`: Pede apenas o que contém o `/pictures/` caminho são permitidos. Por exemplo, `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Valor de entrada `/pictures/city/strasbourg.png`: São permitidos apenas pedidos para este caminho específico e o recurso.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Permite apenas pedidos provenientes de um ou mais países especificados. Pedidos provenientes de todos os outros países serão negados. Utilizar duas letras [indicativo de país ISO 3166](https://msdn.microsoft.com/library/mt761717.aspx) para cada país e separar cada um deles com uma vírgula; não adicione um espaço. Por exemplo, se quiser autorizar o acesso apenas a Estados Unidos e em França, introduza `US,FR`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Nega pedidos provenientes de um ou mais países especificados. São permitidos pedidos provenientes de todos os outros países. A implementação é igual a <b>ec_country_allow</b> parâmetro. Se um código de país está presente em ambos os <b>ec_country_allow</b> e <b>ec_country_deny</b> parâmetros, o <b>ec_country_allow</b> parâmetro tem precedência.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>Apenas permite pedidos do referenciador especificado. Um Referenciador identifica o URL da página da web que está ligada ao recurso que está a ser solicitado. Não inclua o protocolo no valor do parâmetro.> 
      >    São permitidos os seguintes tipos de entrada:
      >    <ul>
      >       <li>Um nome de anfitrião ou um nome de anfitrião e um caminho.</li>
      >       <li>Vários Referenciadores. Para adicionar vários Referenciadores, separe cada Referenciador com uma vírgula; Não adicione um espaço. Se especificar um valor de referência, mas as informações de referência não são enviadas no pedido devido a configuração do browser, o pedido é negado por predefinição.</li> 
      >       <li>Pedidos com informações de Referenciador em falta ou em branco. Por predefinição, o <b>ec_ref_allow</b> parâmetro bloqueia esses tipos de pedidos. Para permitir que essas solicitações, introduza o texto, "em falta", ou introduza um valor em branco (utilizando uma vírgula à direita).</li> 
      >       <li>Subdomínios. Para permitir que os subdomínios, introduza um asterisco (\*). Por exemplo, para permitir que todos os subdomínios do `contoso.com`, introduza `*.contoso.com`.</li>
      >    </ul> 
      >    Por exemplo, para permitir o acesso para pedidos de `www.contoso.com`, todos os subdomínios em `contoso2.com`, e pedidos com Referenciadores em branco ou em falta, introduza `www.contoso.com,*.contoso.com,missing`.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Nega pedidos do referenciador especificado. A implementação é igual a <b>ec_ref_allow</b> parâmetro. Se houver um Referenciador em ambos os <b>ec_ref_allow</b> e <b>ec_ref_deny</b> parâmetros, o <b>ec_ref_allow</b> parâmetro tem precedência.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>Permite apenas pedidos do protocolo especificado. Os valores válidos são `http`, `https`, ou `http,https`.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Nega pedidos do protocolo especificado. A implementação é igual a <b>ec_proto_allow</b> parâmetro. Se um protocolo está presente em ambos os <b>ec_proto_allow</b> e <b>ec_proto_deny</b> parâmetros, o <b>ec_proto_allow</b> parâmetro tem precedência.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Restringe o acesso ao endereço IP do autor do pedido especificado. São suportados IPV4 e IPV6. Pode especificar um endereço IP único pedido ou endereços IP associados uma sub-rede específica. Por exemplo, `11.22.33.0/22` permite pedidos de endereços IP 11.22.32.1 para 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Depois de terminar a inserir valores de parâmetros de encriptação, selecione uma chave para criptografar (se tiver criado um site primário e uma chave de cópia de segurança) a partir da **chave deve ser criptografada** lista.
    
   6. Selecione uma versão de encriptação do **Encryption versão** lista: **V2** para a versão 2 ou **V3** para a versão 3 (recomendado). 

   7. Selecione **Encrypt** para gerar o token.

      Depois do token é gerado, será apresentado no **gerado Token** caixa. Para utilizar o token, anexe-a como uma cadeia de consulta para o fim do ficheiro no seu caminho de URL. Por exemplo, `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Opcionalmente, pode teste o seu token com a ferramenta de desencriptação para que possa visualizar os parâmetros de seu token. Cole o valor do token no **Token para desencriptar** caixa. Selecionar a chave de encriptação a utilizar a partir da **chave para descriptografar** lista, em seguida, selecione **desencriptar**.

      Depois do token é descriptografado, seus parâmetros são apresentados no **parâmetros Original** caixa.

   9. Opcionalmente, personalize o tipo de código de resposta que é retornado quando um pedido é negado. Selecione **Enabled**, em seguida, selecione o código de resposta da **código de resposta** lista. **Nome de cabeçalho** é automaticamente definido como **localização**. Selecione **guardar** para implementar o novo código de resposta. Para alguns códigos de resposta, também tem de introduzir o URL da sua página de erro no **valor de cabeçalho** caixa. O **403** código de resposta (proibido) está selecionado por predefinição. 

3. Sob **HTTP grandes**, selecione **motor de regras**. Utilize o mecanismo de regras para definir caminhos para aplicar a funcionalidade, ativar a funcionalidade de autenticação de token e habilitar recursos relacionados à autenticação de token adicionais. Para obter mais informações, consulte [referência do motor de regras](cdn-rules-engine-reference.md).

   1. Selecione uma regra existente ou crie uma nova regra para definir o recurso ou o caminho para o qual pretende aplicar a autenticação de token. 
   2. Para ativar a autenticação de token numa regra, selecione **[autenticação de Token](cdn-rules-engine-reference-features.md#token-auth)** partir o **funcionalidades** lista, em seguida, selecione **ativado**. Selecione **atualização** se estiver a atualizar uma regra ou **Add** se estiver a criar uma regra.
        
      ![Exemplo de ativar autenticação de token de motor de regras CDN](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. O mecanismo de regras, também pode ativar funcionalidades relacionadas com a autenticação de token adicionais. Para ativar qualquer uma das seguintes funcionalidades, selecione-o a partir da **funcionalidades** lista, em seguida, selecione **ativado**.
    
   - **[Código de negação de autenticação de token](cdn-rules-engine-reference-features.md#token-auth-denial-code)**: Determina o tipo de resposta que é retornado para um utilizador quando um pedido é negado. O código de resposta definido de substituição de regras definidas a **a manipulação de recusa personalizado** secção na página de autenticação baseada em tokens.

   - **[Autenticação de token ignorar URL maiúsculas / minúsculas](cdn-rules-engine-reference-features.md#token-auth-ignore-url-case)**: Determina se o URL utilizado para validar o token diferencia maiúsculas de minúsculas.

   - **[Parâmetro de autenticação de token](cdn-rules-engine-reference-features.md#token-auth-parameter)**: Muda o nome o parâmetro de cadeia de caracteres de consulta de token de autenticação que é apresentado no URL solicitado. 
        
     ![Exemplo de definições de autenticação de token de motor de regras CDN](./media/cdn-token-auth/cdn-rules-engine2.png)

5. Pode personalizar o token ao aceder ao código-fonte na [GitHub](https://github.com/VerizonDigital/ectoken).
   Idiomas disponíveis incluem:
    
   - C
   - C#
   - PHP
   - Perl
   - Java
   - Python 

## <a name="azure-cdn-features-and-provider-pricing"></a>Fornecedor de preços e funcionalidades da CDN do Azure

Para obter informações sobre as funcionalidades, consulte [funcionalidades do produto da CDN do Azure](cdn-features.md). Para obter informações sobre preços, consulte [preços de rede de entrega de conteúdos](https://azure.microsoft.com/pricing/details/cdn/).
