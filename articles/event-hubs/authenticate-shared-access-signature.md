---
title: Autenticar acesso aos Hubs de Eventos Azure com assinaturas de acesso partilhado
description: Este artigo mostra-lhe como autenticar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: spelluru
ms.openlocfilehash: d17026dba26b3c1cb846d60967180c29563c425d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74545578"
---
# <a name="authenticate-access-to-event-hubs-resources-using-shared-access-signatures-sas"></a>Autenticar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado (SAS)
A assinatura de acesso partilhado (SAS) dá-lhe controlo granular sobre o tipo de acesso que concede aos clientes que têm a assinatura de acesso partilhado. Aqui estão alguns dos controlos que pode definir num SAS: 

- O intervalo sobre o qual o SAS é válido, incluindo o tempo de início e o tempo de validade.
- As permissões concedidas pela SAS. Por exemplo, um Espaço de nome SAS para um Espaço de Nome saque de Eventos pode conceder a permissão de escuta, mas não a permissão de envio.
- Apenas os clientes que apresentam credenciais válidas podem enviar dados para um centro de eventos.
- Um cliente não pode fazer-se passar por outro cliente.
- Um cliente rouge pode ser impedido de enviar dados para um centro de eventos.

Este artigo abrange a autenticação dos recursos do Event Hubs utilizando a SAS. Para aprender sobre **a autorização** de acesso aos recursos do Event Hubs utilizando a SAS, consulte [este artigo](authorize-access-shared-access-signature.md). 

> [!NOTE]
> A Microsoft recomenda que utilize credenciais de AD Azure sempre que possível como uma boa prática de segurança, em vez de utilizar as assinaturas de acesso partilhado, que podem ser mais facilmente comprometidas. Enquanto pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder acesso fino aos seus recursos do Event Hubs, a Azure AD oferece capacidades semelhantes sem a necessidade de gerir tokens SAS ou preocupar-se em revogar um SAS comprometido.
> 
> Para obter mais informações sobre a integração da AD Azure nos Hubs de Eventos Azure, consulte Autorizar o acesso aos Centros de [Eventos utilizando o Azure AD](authorize-access-azure-active-directory.md). 


## <a name="configuring-for-sas-authentication"></a>Configuração para autenticação SAS
Você pode configurar a regra de autorização de acesso partilhada EventHubs em um espaço de nome de Event Hubs, ou uma entidade (caso de hub de evento ou Tópico kafka em um espaço de nome baseado em EventoS). Configurar uma regra de autorização de acesso partilhado a um grupo de consumidores não é atualmente suportado, mas pode utilizar regras configuradas num espaço de nome ou entidade para garantir o acesso ao grupo de consumidores. 

A imagem que se segue mostra como as regras de autorização se aplicam às entidades da amostra. 

![Regra de autorização configurar](./media/authenticate-shared-access-signature/configure-sas-authorization-rule.png)

Neste exemplo, o espaço de nome sinuoso do Evento Hubs (ExampleNamespace) tem duas entidades: eh1 e tópico1. As regras de autorização são definidas tanto a nível da entidade como também ao nível do espaço de nome.  

As regras de autorização manageRuleNS, sendRuleNS e listenRuleNS aplicam-se tanto ao hub de eventos como ao tópico t1. As regras de autorização de "Rule-eh" de escuta aplicam-se apenas ao centro de eventos eh1 e a regra de autorização do rulet aplica-se apenas ao tópico1. 

Ao utilizar a regra de autorização sendRuleNS, os pedidos de cliente podem enviar para eh1 e tópico1. Quando é utilizada a regra de autorização do SendRuleT, aplica o acesso granular apenas ao tópico1 e, portanto, as aplicações dos clientes que utilizam esta regra para acesso não podem agora enviar para o eh1, mas apenas para o tópico1.

## <a name="generate-a-shared-access-signature-token"></a>Gerar um símbolo de assinatura de acesso partilhado 
Qualquer cliente que tenha acesso ao nome de uma regra de autorização e uma das suas chaves de assinatura pode gerar um token SAS. O símbolo é gerado através da criação de uma corda no seguinte formato:

- `se`– Token expira instantaneamente. Inteiro refletindo segundos desde época 00:00:00 UTC em 1 de janeiro de 1970 (época UNIX) quando o símbolo expirar
- `skn`– Nome da regra de autorização, que é o nome-chave SAS.
- `sr`– URI do recurso a ser acedido.
- `sig`– Assinatura.

A linha de assinatura é o hash SHA-256 computado sobre o recurso URI (âmbito descrito na secção anterior) e a representação de cordas do instante de validade do símbolo, separado por CRLF.

A computação de hash parece semelhante ao seguinte código pseudo e devolve um valor hash de 256 bits/32 byte. 

```
SHA-256('https://<yournamespace>.servicebus.windows.net/'+'\n'+ 1438205742)
```

O símbolo contém os valores não hashed para que o destinatário possa recomputar o hash com os mesmos parâmetros, verificando que o emitente está na posse de uma chave de assinatura válida.

O recurso URI é o URI completo do recurso Service Bus a que o acesso é reclamado. Por exemplo,<namespace>http://<entityPath> `sb://<namespace>.servicebus.windows.net/<entityPath>;` .servicebus.windows.net/ `http://contoso.servicebus.windows.net/eventhubs/eh1`ou isto é, .

O URI deve ser codificado por cento.

A regra de autorização de acesso partilhado utilizada para a assinatura deve ser configurada na entidade especificada por este URI, ou por um dos seus pais hierárquicos. Por `http://contoso.servicebus.windows.net/eventhubs/eh1` exemplo, `http://contoso.servicebus.windows.net` ou no exemplo anterior.

Um token SAS é válido para <resourceURI> todos os recursos pré-fixados com o utilizado na cadeia de assinaturas.

> [!NOTE]
> Você gera um sinal de acesso para Centros de Eventos usando a política de acesso partilhado. Para mais informações, consulte a política de autorização de [acesso partilhado.](authorize-access-shared-access-signature.md#shared-access-authorization-policies)

### <a name="generating-a-signaturetoken-from-a-policy"></a>Gerar uma assinatura (símbolo) de uma política 
A secção seguinte mostra a geração de um símbolo SAS utilizando políticas de assinatura de acesso partilhado,

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

## <a name="authenticating-event-hubs-publishers-with-sas"></a>Autenticação de editores de Hubs de Eventos com SAS 
Uma editora de eventos define um ponto final virtual para um hub de eventos. A editora só pode ser usada para enviar mensagens para um centro de eventos e não receber mensagens.

Normalmente, um centro de eventos emprega uma editora por cliente. Todas as mensagens que são enviadas a qualquer uma das editoras de um centro de eventos são enquecidas dentro desse centro de eventos. As editoras permitem um controlo de acesso fino.

A cada cliente do Event Hubs é atribuído um símbolo único, que é enviado para o cliente. Os tokens são produzidos de tal forma que cada símbolo único dá acesso a diferentes editores únicos. Um cliente que detém um símbolo só pode enviar a uma editora, e a nenhum outro editor. Se vários clientes partilham o mesmo símbolo, então cada um deles partilha o editor.

Todas as fichas são atribuídas com chaves SAS. Normalmente, todas as fichas são assinadas com a mesma chave. Os clientes não estão cientes da chave, o que impede os clientes de fabricarem fichas. Os clientes operam com as mesmas fichas até expirarem.

Por exemplo, para definir as regras de autorização aplicáveis ao envio/publicação apenas para Os Centros de Eventos, é necessário definir uma regra de autorização de envio. Isto pode ser feito a um nível de espaço de nome ou dar mais espaço granular a uma determinada entidade (instância de centros de eventos ou um tópico). Um cliente ou uma aplicação que é acessível com tal acesso granular é chamado, editora event hubs. Para o fazer, siga estes passos:

1. Crie uma chave SAS na entidade que pretende publicar para atribuir o âmbito de **envio** na seleção. Para mais informações, consulte as políticas de autorização de [acesso partilhado](authorize-access-shared-access-signature.md#shared-access-authorization-policies).
2. Gere um token SAS com um tempo de validade para um editor específico utilizando a chave gerada no passo 1.

    ```csharp
    var sasToken = SharedAccessSignatureTokenProvider.GetPublisherSharedAccessSignature(
                new Uri("Service-Bus-URI"),
                "eventub-name",
                "publisher-name",
                "sas-key-name",
                "sas-key",
                TimeSpan.FromMinutes(30));
    ```
3. Forneça o símbolo ao cliente editor, que só pode enviar à entidade e ao editor que token concede acesso.

    Uma vez expirado o token, o cliente perde o acesso ao envio/publicação para a entidade. 


> [!NOTE]
> Embora não seja recomendado, é possível equipar dispositivos com fichas que concedem acesso a um hub de eventos ou a um espaço de nome. Qualquer dispositivo que detenha esta ficha pode enviar mensagens diretamente para o centro do evento. Além disso, o dispositivo não pode ser colocado na lista negra do envio para o centro do evento.
> 
> É sempre recomendado dar âmbitos específicos e granulares.

> [!IMPORTANT]
> Uma vez criados os tokens, cada cliente é aprovisionado com o seu próprio símbolo único.
>
> Quando o cliente envia dados para um centro de eventos, marca o seu pedido com o símbolo. Para evitar que um intruso espionagem e roube o símbolo, a comunicação entre o cliente e o centro do evento deve ocorrer sobre um canal encriptado.
> 
> Se um símbolo for roubado por um agressor, o agressor pode fazer-se passar pelo cliente cujo símbolo foi roubado. Na lista negra de uma editora, torna esse cliente inutilizável até receber um novo símbolo que usa uma editora diferente.


## <a name="authenticating-event-hubs-consumers-with-sas"></a>Autenticação de eventos Hubs consumidores com SAS 
Para autenticar aplicações back-end que consomem a partir dos dados gerados pelos produtores de Event Hubs, a autenticação simbólica do Event Hubs exige que os seus clientes tenham os direitos de **gestão** ou os privilégios de **escuta** atribuídos ao seu espaço de nome do Event Hubs ou à instância ou tópico do hub de eventos. Os dados são consumidos a partir de Centros de Eventos utilizando grupos de consumidores. Embora a política SAS lhe dê âmbito granular, este âmbito é definido apenas ao nível da entidade e não ao nível do consumidor. Significa que os privilégios definidos a nível do espaço de nome ou a instância ou nível de tópico do evento serão aplicados aos grupos de consumidores dessa entidade.

## <a name="next-steps"></a>Passos seguintes
Consulte os seguintes artigos:

- [Autorizar a utilização do SAS](authenticate-shared-access-signature.md)
- [Autorizar a utilização do controlo de acesso de base de funções (RBAC)](authenticate-shared-access-signature.md)
- [Saiba mais sobre centros de eventos](event-hubs-about.md)

Consulte os seguintes artigos relacionados:

- [Autenticação solicita aos Hubs de Eventos Azure de uma aplicação que usa o Diretório Ativo Azure](authenticate-application.md)
- [Autenticar uma identidade gerida com o Diretório Ativo Azure para aceder aos Recursos hubs do Evento](authenticate-managed-identity.md)
- [Autorizar acesso aos recursos do Event Hubs utilizando o Diretório Ativo Azure](authorize-access-azure-active-directory.md)
- [Autorizar o acesso aos recursos do Event Hubs utilizando assinaturas de acesso partilhado](authorize-access-shared-access-signature.md)