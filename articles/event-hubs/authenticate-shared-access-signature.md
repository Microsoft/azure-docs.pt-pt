---
title: Autenticar acesso aos Hubs de Eventos Azure com assinaturas de acesso partilhado
description: Este artigo mostra-lhe como autenticar o acesso aos recursos do Event Hubs usando assinaturas de acesso partilhado.
ms.topic: conceptual
ms.date: 06/23/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: ff141fc1bb681e2356a4471dfdc808d622fd76b5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98986500"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autenticar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado (SAS)
A assinatura de acesso partilhado (SAS) dá-lhe controlo granular sobre o tipo de acesso que concede aos clientes que têm a assinatura de acesso partilhado. Aqui estão alguns dos controlos que pode definir num SAS: 

- O intervalo sobre o qual o SAS é válido, incluindo o tempo de início e o prazo de validade.
- As permissões concedidas pelo SAS. Por exemplo, um SAS para um espaço de nomes de Event Hubs pode conceder a permissão de escuta, mas não a permissão de envio.
- Apenas os clientes que apresentem credenciais válidas podem enviar dados para um centro de eventos.
- Um cliente não pode fazer-se passar por outro cliente.
- Um cliente fraudulento pode ser impedido de enviar dados para um centro de eventos.

Este artigo abrange autenticar o acesso aos recursos do Event Hubs utilizando o SAS. Para saber mais sobre **a autorização de** acesso aos recursos do Event Hubs utilizando o SAS, consulte este [artigo.](authorize-access-shared-access-signature.md) 

> [!NOTE]
> A Microsoft recomenda que utilize credenciais AZure AD quando possível como uma melhor prática de segurança, em vez de usar as assinaturas de acesso partilhada, que podem ser mais facilmente comprometidas. Embora possa continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder acesso fino aos recursos do Event Hubs, a Azure AD oferece capacidades semelhantes sem a necessidade de gerir tokens SAS ou se preocupar em revogar um SAS comprometido.
> 
> Para obter mais informações sobre a integração da AD Azure nos Hubs de Eventos Azure, consulte [acesso autorizado aos Centros de Eventos utilizando a Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Configuração para a autenticação SAS
Pode configurar a regra de autorização de acesso partilhado do EventHubs num espaço de nomes de Event Hubs ou numa entidade (instância do centro de eventos ou tópico kafka num centro de eventos). Atualmente, a configuração de uma regra de autorização de acesso partilhado a um grupo de consumidores não é suportada, mas pode utilizar regras configuradas num espaço ou entidade de nome para garantir o acesso ao grupo de consumidores. 

A imagem a seguir mostra como as regras de autorização se aplicam às entidades de amostragem. 

![Regra de autorização de configuração](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Neste exemplo, a amostra Event Hubs namespace (ExemploNamespace) tem duas entidades: eh1 e topic1. As regras de autorização são definidas tanto a nível da entidade como ao nível do espaço de nome.  

As regras de autorização gerenciaisRuleNS, sendRuleNS e listenRuleNS aplicam-se tanto ao centro de eventos e a 1.000 e ao tópico t1. As regras de autorização listenRule-eh e sendRule-eh aplicam-se apenas ao caso do centro de eventos e à regra de autorização sendRuleT aplica-se apenas ao tópico 1. 

Ao utilizar a regra de autorização sendRuleNS, os pedidos do cliente podem enviar para o eh1 e para o tópico1. Quando a regra de autorização sendRuleT é utilizada, aplica o acesso granular apenas ao tópico1 e, portanto, as aplicações do cliente que utilizam esta regra de acesso não podem agora enviar para o eh1, mas apenas para o tópico1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um token de assinatura de acesso partilhado 
Qualquer cliente que tenha acesso ao nome de uma regra de autorização e uma das suas chaves de assinatura pode gerar um token SAS. O token é gerado através da criação de uma cadeia no seguinte formato:

- `se`  – Token expiração instantânea. Inteiro refletindo segundos desde época 00:00:00 UTC em 1 de janeiro de 1970 (época UNIX) quando o token expira
- `skn` – Nome da regra de autorização, que é o nome-chave SAS.
- `sr` – URI do recurso a ser acedido.
- `sig` – Assinatura.

O fio de assinatura é o hash SHA-256 calculado sobre o recurso URI (âmbito descrito na secção anterior) e a representação de cadeia do instantâneo de expiração do símbolo, separado por CRLF.

O cálculo de haxixe é semelhante ao seguinte código pseudo e devolve um valor de haxixe de 256 bits/32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O token contém os valores não hashed para que o destinatário possa recompensar o haxixe com os mesmos parâmetros, verificando se o emitente está na posse de uma chave de assinatura válida.

O recurso URI é o URI completo do recurso Service Bus a que o acesso é reclamado. Por exemplo, http:// <namespace> .servicebus.windows.net/ ou <entityPath> `sb://<namespace>.servicebus.windows.net/<entityPath>;` seja, `http://contoso.servicebus.windows.net/eventhubs/eh1` .

O URI deve estar codificado por cento.

A regra de autorização de acesso partilhado utilizada para a assinatura deve ser configurada na entidade especificada por este URI, ou por um dos seus pais hierárquicos. Por exemplo, `http://contoso.servicebus.windows.net/eventhubs/eh1` ou `http://contoso.servicebus.windows.net` no exemplo anterior.

Um token SAS é válido para todos os recursos pré-fixados com o <resourceURI> usado na corda de assinatura.

> [!NOTE]
> Você gera um token de acesso para Os Centros de Eventos usando a política de acesso compartilhado. Para mais informações, consulte [a política de autorização de acesso partilhado.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)

### <a name="generating-a-signaturetoken-from-a-policy"></a>Gerar uma assinatura (token) de uma política 
A secção seguinte mostra a geração de um token SAS utilizando políticas de assinatura de acesso partilhado,

#### <a name="nodejs"></a>NodeJS

```javascript
function createSharedAccessToken(uri, saName, saKey) { 
    if (!uri || !saName || !saKey) { 
            throw "Missing required parameter"; 
        } 
    var encoded = encodeURIComponent(uri); 
    var now = new Date(); 
    var week = 60*60*24*7;
    var ttl = Math.round(now.getTime() / 1000) + week;
    var signature = encoded + '\n' + ttl; 
    var signatureUTF8 = utf8.encode(signature); 
    var hash = crypto.createHmac('sha256', saKey).update(signatureUTF8).digest('base64'); 
    return 'SharedAccessSignature sr=' + encoded + '&sig=' +  
        encodeURIComponent(hash) + '&se=' + ttl + '&skn=' + saName; 
```

#### <a name="java"></a>JAVA

```java
private static String GetSASToken(String resourceUri, String keyName, String key)
  {
      long epoch = System.currentTimeMillis()/1000L;
      int week = 60*60*24*7;
      String expiry = Long.toString(epoch + week);

      String sasToken = null;
      try {
          String stringToSign = URLEncoder.encode(resourceUri, "UTF-8") + "\n" + expiry;
          String signature = getHMAC256(key, stringToSign);
          sasToken = "SharedAccessSignature sr=" + URLEncoder.encode(resourceUri, "UTF-8") +"&sig=" +
                  URLEncoder.encode(signature, "UTF-8") + "&se=" + expiry + "&skn=" + keyName;
      } catch (UnsupportedEncodingException e) {

          e.printStackTrace();
      }

      return sasToken;
  }


public static String getHMAC256(String key, String input) {
    Mac sha256_HMAC = null;
    String hash = null;
    try {
        sha256_HMAC = Mac.getInstance("HmacSHA256");
        SecretKeySpec secret_key = new SecretKeySpec(key.getBytes(), "HmacSHA256");
        sha256_HMAC.init(secret_key);
        Encoder encoder = Base64.getEncoder();

        hash = new String(encoder.encode(sha256_HMAC.doFinal(input.getBytes("UTF-8"))));

    } catch (InvalidKeyException e) {
        e.printStackTrace();
    } catch (NoSuchAlgorithmException e) {
        e.printStackTrace();
   } catch (IllegalStateException e) {
        e.printStackTrace();
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }

    return hash;
}
```
#### <a name="php"></a>PHP

```php
function generateSasToken($uri, $sasKeyName, $sasKeyValue) 
{ 
    $targetUri = strtolower(rawurlencode(strtolower($uri))); 
    $expires = time();  
    $expiresInMins = 60; 
    $week = 60*60*24*7;
    $expires = $expires + $week; 
    $toSign = $targetUri . "\n" . $expires; 
    $signature = rawurlencode(base64_encode(hash_hmac('sha256',             
     $toSign, $sasKeyValue, TRUE))); 
    
    $token = "SharedAccessSignature sr=" . $targetUri . "&sig=" . $signature . "&se=" . $expires .      "&skn=" . $sasKeyName; 
    return $token; 
}
```

#### <a name="c"></a>C#

```csharp
private static string createToken(string resourceUri, string keyName, string key)
{
    TimeSpan sinceEpoch = DateTime.UtcNow - new DateTime(1970, 1, 1);
    var week = 60 * 60 * 24 * 7;
    var expiry = Convert.ToString((int)sinceEpoch.TotalSeconds + week);
    string stringToSign = HttpUtility.UrlEncode(resourceUri) + "\n" + expiry;
    HMACSHA256 hmac = new HMACSHA256(Encoding.UTF8.GetBytes(key));
    var signature = Convert.ToBase64String(hmac.ComputeHash(Encoding.UTF8.GetBytes(stringToSign)));
    var sasToken = String.Format(CultureInfo.InvariantCulture, "SharedAccessSignature sr={0}&sig={1}&se={2}&skn={3}", HttpUtility.UrlEncode(resourceUri), HttpUtility.UrlEncode(signature), expiry, keyName);
    return sasToken;
}
```

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autenticação de editores de Centros de Eventos com SAS 
Uma editora de eventos define um ponto final virtual para um centro de eventos. O editor só pode ser usado para enviar mensagens para um centro de eventos e não receber mensagens.

Normalmente, um centro de eventos emprega um editor por cliente. Todas as mensagens enviadas a qualquer uma das editoras de um centro de eventos são encosadas dentro desse centro de eventos. Os editores permitem um controlo de acesso fino.

Cada cliente do Event Hubs é atribuído um token único, que é enviado para o cliente. Os tokens são produzidos de tal forma que cada símbolo único dá acesso a diferentes editores únicos. Um cliente que detém um símbolo só pode enviar a uma editora, e a nenhum outro editor. Se vários clientes partilham o mesmo símbolo, cada um deles partilha a editora.

Todos os tokens são atribuídos com chaves SAS. Normalmente, todos os tokens são assinados com a mesma chave. Os clientes não estão cientes da chave, o que impede os clientes de fabricarem fichas. Os clientes operam com as mesmas fichas até expirarem.

Por exemplo, para definir as regras de autorização que se resumem apenas ao envio/publicação para Os Centros de Eventos, é necessário definir uma regra de autorização de envio. Isto pode ser feito a um nível de espaço de nome ou dar mais âmbito granular a uma determinada entidade (instância de centros de eventos ou um tópico). Um cliente ou uma aplicação que seja alargada a esse acesso granular é chamado, editor do Event Hubs. Para tal, siga estes passos:

1. Crie uma chave SAS na entidade que pretende publicar para atribuir o âmbito **de envio.** Para mais informações, consulte [as políticas de autorização de acesso partilhado.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)
2. Gere um token SAS com um prazo de validade para um editor específico utilizando a chave gerada no passo 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Forneça o token ao cliente editor, que só pode enviar à entidade e à editora a que o token concede acesso.

    Uma vez expirado o sinal, o cliente perde o acesso ao envio/publicação para a entidade. 


> [!NOTE]
> Embora não seja recomendado, é possível equipar dispositivos com fichas que dêem acesso a um centro de eventos ou a um espaço de nome. Qualquer dispositivo que detenha este token pode enviar mensagens diretamente para o centro de eventos. Além disso, o dispositivo não pode ser bloqueado de envio para o centro de eventos.
> 
> É sempre recomendado dar miras específicas e granulares.

> [!IMPORTANT]
> Uma vez criadas as fichas, cada cliente é a provisionado com o seu próprio símbolo único.
>
> Quando o cliente envia dados para um centro de eventos, ele marca o seu pedido com o token. Para evitar que um intruso ouça e roube o token, a comunicação entre o cliente e o centro de eventos deve ocorrer por um canal encriptado.
> 
> Se um símbolo for roubado por um agressor, o agressor pode fazer-se passar pelo cliente cujo símbolo foi roubado. Bloquear uma editora torna esse cliente inutilizável até receber um novo símbolo que usa uma editora diferente.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autenticação de centros de eventos com SAS 
Para autenticar aplicações de back-end que consomem a partir dos dados gerados pelos produtores de Event Hubs, a autenticação simbólica do Event Hubs exige que os seus clientes tenham os direitos de **gestão** ou os privilégios de **escuta** atribuídos ao seu espaço de nomes de Eventos ou ao centro de eventos ou ao tópico. Os dados são consumidos a partir de Centros de Eventos usando grupos de consumidores. Enquanto a política SAS lhe dá âmbito granular, este âmbito é definido apenas ao nível da entidade e não ao nível do consumidor. Significa que os privilégios definidos ao nível do espaço de nome ou do nível do centro de eventos ou do nível tópico serão aplicados aos grupos de consumidores dessa entidade.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Autorizar a utilização de SAS](authenticate-shared-access-signature.md)
- [Autorizar a utilização do controlo de acesso baseado em funções Azure (Azure RBAC)](authenticate-shared-access-signature.md)
- [Saiba mais sobre os Centros de Eventos](event-hubs-about.md)

Consulte os seguintes artigos relacionados:

- [Autenticar pedidos aos Azure Event Hubs a partir de uma aplicação utilizando o Azure Ative Directory](authenticate-application.md)
- [Autenticar uma identidade gerida com o Azure Ative Directory para aceder aos Recursos dos Hubs de Eventos](authenticate-managed-identity.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando o Azure Ative Directory](authorize-access-azure-active-directory.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)
