---
title: Proteger o acesso e os dados
description: Acesso seguro a entradas, saídas, gatilhos baseados em pedidos, histórico de execução, tarefas de gestão e acesso a outros recursos em Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla, rarayudu
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 7b082c226b38633d6c34ee2fe4d5227252b2bfcb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556388"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Acesso seguro e dados em Azure Logic Apps

A Azure Logic Apps conta com [o Azure Storage](../storage/index.yml) para armazenar e encriptar automaticamente [os dados em repouso](../security/fundamentals/encryption-atrest.md). Esta encriptação protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade. Por predefinição, o Azure Storage utiliza chaves geridas pela Microsoft para encriptar os seus dados. Para obter mais informações, consulte [a encriptação do Azure Storage para obter dados em repouso](../storage/common/storage-service-encryption.md).

Para controlar mais o acesso e proteger dados sensíveis em Azure Logic Apps, pode configurar uma segurança adicional nestas áreas:

* [Acesso a chamadas de entrada para gatilhos baseados em pedidos](#secure-inbound-requests)
* [Acesso a operações de aplicações lógicas](#secure-operations)
* [Acesso a executar entradas e saídas de histórico](#secure-run-history)
* [Acesso às entradas de parâmetros](#secure-action-parameters)
* [Acesso a chamadas de saída para outros serviços e sistemas](#secure-outbound-requests)
* [Bloquear a criação de ligações para conectores específicos](#block-connections)
* [Orientação de isolamento para apps lógicas](#isolation-logic-apps)
* [Linha de base de segurança Azure para apps Azure Logic](../logic-apps/security-baseline.md)

Para mais informações sobre segurança em Azure, consulte estes tópicos:

* [Descrição geral da encriptação do Azure](../security/fundamentals/encryption-overview.md)
* [Encriptação de dados Azure-at-Rest](../security/fundamentals/encryption-atrest.md)
* [Referência de Segurança do Azure](../security/benchmarks/overview.md)

<a name="secure-inbound-requests"></a>

## <a name="access-for-inbound-calls-to-request-based-triggers"></a>Acesso a chamadas de entrada para gatilhos baseados em pedidos

As chamadas de entrada que uma aplicação lógica recebe através de um gatilho baseado em pedidos, tais como o gatilho [pedido](../connectors/connectors-native-reqres.md) ou o gatilho [HTTP Webhook,](../connectors/connectors-native-webhook.md) encriptação de suporte e são protegidos com [Segurança da Camada de Transporte (TLS) 1.2 no mínimo](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecido como Camada de Tomadas Seguras (SSL). A Logic Apps aplica esta versão ao receber uma chamada de entrada para o gatilho do Pedido ou uma chamada de volta para o gatilho ou ação http Webhook. Se tiver erros de aperto de mão TLS, certifique-se de que utiliza O TLS 1.2. Para obter mais informações, consulte [a resolução do problema TLS 1.0](/security/solving-tls1-problem).

As chamadas de entrada suportam estas suítes de cifra:

* TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
* TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

Aqui estão as formas adicionais de limitar o acesso aos gatilhos que recebem chamadas de entrada para a sua aplicação lógica para que apenas os clientes autorizados possam ligar para a sua aplicação lógica:

* [Gerar assinaturas de acesso partilhado (SAS)](#sas)
* [Ativar a autenticação aberta do Diretório Ativo Azure (Azure AD OAuth)](#enable-oauth)
* [Exponha a sua app lógica com a Azure API Management](#azure-api-management)
* [Restringir endereços IP de entrada](#restrict-inbound-ip-addresses)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar assinaturas de acesso partilhado (SAS)

Cada ponto final de pedido de uma aplicação lógica tem uma [Assinatura de Acesso Partilhado (SAS)](/rest/api/storageservices/constructing-a-service-sas) no URL do ponto final, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL contém o `sp` `sv` parâmetro , e `sig` consulta, conforme descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica permissões para os métodos HTTP permitidos a utilizar. |
| `sv` | Especifica a versão SAS para utilizar para gerar a assinatura. |
| `sig` | Especifica a assinatura a utilizar para autenticar o acesso ao gatilho. Esta assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades url. Nunca exposta ou publicada, esta chave é mantida encriptada e armazenada com a aplicação lógica. A sua aplicação lógica autoriza apenas os gatilhos que contêm uma assinatura válida criada com a chave secreta. |
|||

As chamadas de entrada para um ponto final de pedido podem utilizar apenas um sistema de autorização, seja SAS ou [Azure Ative Directory Open Authentication](#enable-oauth). Apesar de utilizar um esquema não desativa o outro esquema, usar ambos os esquemas ao mesmo tempo causa um erro porque o serviço não sabe qual o esquema a escolher.

Para obter mais informações sobre a garantia de acesso com SAS, consulte estas secções neste tópico:

* [Regenerar chaves de acesso](#access-keys)
* [Criar URLs de retorno de validade](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para gerar uma nova chave de acesso à segurança a qualquer momento, utilize o portal Azure REST API ou Azure. Todos os URLs anteriormente gerados que utilizam a tecla antiga são invalidados e já não têm autorização para desencadear a aplicação lógica. Os URLs que recupera após a regeneração são assinados com a nova chave de acesso.

1. No [portal Azure,](https://portal.azure.com)abra a app lógica que tem a chave que pretende regenerar.

1. No menu da aplicação lógica, em **Definições,** selecione **Teclas de acesso**.

1. Selecione a chave que pretende regenerar e termine o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de retorno de validade

Se partilhar o URL do ponto final para um gatilho baseado em pedidos com outras partes, pode gerar URLs de retorno que usam chaves específicas e têm datas de validade. Desta forma, pode enrolar perfeitamente as teclas ou restringir o acesso a desencadear a sua aplicação lógica com base num período de tempo específico. Para especificar uma data de validade para um URL, utilize a [API de Apps Lógicas REST,](/rest/api/logic/workflowtriggers)por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `NotAfter` propriedade usando uma cadeia de data JSON. Esta propriedade devolve um URL de retorno que é válido apenas até a `NotAfter` data e hora.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando gera ou lista URLs de retorno para um gatilho baseado em pedidos, pode especificar a chave a utilizar para a assinatura do URL. Para gerar um URL assinado por uma chave específica, utilize a [API logic Apps REST](/rest/api/logic/workflowtriggers), por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `KeyType` propriedade como qualquer um ou `Primary` `Secondary` . Esta propriedade devolve um URL assinado pela chave de segurança especificada.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-open-authentication-azure-ad-oauth"></a>Ativar a autenticação aberta do Diretório Ativo Azure (Azure AD OAuth)

Para chamadas de entrada para um ponto final criado por um gatilho baseado em pedidos, pode ativar [o Azure AD OAuth](../active-directory/develop/index.yml) definindo ou adicionando uma política de autorização para a sua aplicação lógica. Desta forma, as chamadas de entrada usam [fichas de acesso OAuth](../active-directory/develop/access-tokens.md) para autorização.

Quando a sua aplicação lógica recebe um pedido de entrada que inclui um token de acesso OAuth, o serviço Azure Logic Apps compara as reclamações do token com as reclamações especificadas por cada política de autorização. Se existir uma correspondência entre as reclamações do token e todas as reclamações em pelo menos uma apólice, a autorização é bem sucedida para o pedido de entrada. O token pode ter mais reclamações do que o número especificado pela política de autorização.

#### <a name="considerations-before-you-enable-azure-ad-oauth"></a>Considerações antes de ativar Azure AD OAuth

* Uma chamada de entrada para o ponto final do pedido pode utilizar apenas um esquema de autorização, quer a Azure AD OAuth, quer [a Assinatura de Acesso Partilhado (SAS).](#sas) Apesar de utilizar um esquema não desativa o outro esquema, usar ambos os esquemas ao mesmo tempo causa um erro porque o serviço De aplicações lógicas não sabe qual o esquema a escolher.

* Apenas os sistemas de autorização [do tipo Portador](../active-directory/develop/active-directory-v2-protocols.md#tokens) são suportados para os tokens de acesso Azure AD OAuth, o que significa que o `Authorization` cabeçalho para o token de acesso deve especificar o `Bearer` tipo.

* A sua aplicação lógica está limitada a um número máximo de políticas de autorização. Cada política de autorização também tem um número máximo de [reclamações.](../active-directory/develop/developer-glossary.md#claim) Para obter mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Uma política de autorização deve incluir pelo menos a **reclamação do Emitente,** que tem um valor que começa com um `https://sts.windows.net/` ou `https://login.microsoftonline.com/` (OAuth V2) como o ID do emitente Azure AD.

  Por exemplo, suponha que a sua aplicação lógica tem uma política de autorização que requer dois tipos de reclamação, **Audience** e **Emitente.** Esta [secção de carga útil](../active-directory/develop/access-tokens.md#payload-claims) da amostra para um token de acesso descodificado inclui ambos os tipos de reclamação onde `aud` é o valor do **Público** e é o valor `iss` do **Emitente:**

  ```json
  {
      "aud": "https://management.core.windows.net/",
      "iss": "https://sts.windows.net/<Azure-AD-issuer-ID>/",
      "iat": 1582056988,
      "nbf": 1582056988,
      "exp": 1582060888,
      "_claim_names": {
         "groups": "src1"
      },
      "_claim_sources": {
         "src1": {
            "endpoint": "https://graph.windows.net/7200000-86f1-41af-91ab-2d7cd011db47/users/00000-f433-403e-b3aa-7d8406464625d7/getMemberObjects"
         }
      },
      "acr": "1",
      "aio": "AVQAq/8OAAAA7k1O1C2fRfeG604U9e6EzYcy52wb65Cx2OkaHIqDOkuyyr0IBa/YuaImaydaf/twVaeW/etbzzlKFNI4Q=",
      "amr": [
         "rsa",
         "mfa"
      ],
      "appid": "c44b4083-3bb0-00001-b47d-97400853cbdf3c",
      "appidacr": "2",
      "deviceid": "bfk817a1-3d981-4dddf82-8ade-2bddd2f5f8172ab",
      "family_name": "Sophia Owen",
      "given_name": "Sophia Owen (Fabrikam)",
      "ipaddr": "167.220.2.46",
      "name": "sophiaowen",
      "oid": "3d5053d9-f433-00000e-b3aa-7d84041625d7",
      "onprem_sid": "S-1-5-21-2497521184-1604012920-1887927527-21913475",
      "puid": "1003000000098FE48CE",
      "scp": "user_impersonation",
      "sub": "KGlhIodTx3XCVIWjJarRfJbsLX9JcdYYWDPkufGVij7_7k",
      "tid": "72f988bf-86f1-41af-91ab-2d7cd011db47",
      "unique_name": "SophiaOwen@fabrikam.com",
      "upn": "SophiaOwen@fabrikam.com",
      "uti": "TPJ7nNNMMZkOSx6_uVczUAA",
      "ver": "1.0"
   }
   ```

#### <a name="enable-azure-ad-oauth-for-your-logic-app"></a>Ativar o Azure AD OAuth para a sua aplicação lógica

Siga estes passos para o portal Azure ou para o modelo do Gestor de Recursos Azure:

<a name="define-authorization-policy-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure,](https://portal.azure.com)adicione uma ou mais políticas de autorização à sua aplicação lógica:

1. No [portal Azure,](https://portal.microsoft.com)encontre e abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicativos lógico, em **Definições,** selecione **Autorização**. Depois de abrir o painel de autorização, **selecione Adicionar a política**.

   ![Selecione "Autorização" > "Adicionar política"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Forneça informações sobre a política de autorização especificando os tipos e valores de [reclamação](../active-directory/develop/developer-glossary.md#claim) que a sua aplicação lógica espera no token de acesso apresentado por cada chamada de entrada para o gatilho do Pedido:

   ![Fornecer informações para a política de autorização](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da política** | Yes | O nome que quer usar para a política de autorização |
   | **Pedidos** | Yes | Os tipos e valores de reclamação que a sua aplicação lógica aceita a partir de chamadas de entrada. O valor de reclamação é limitado a um [número máximo de caracteres](logic-apps-limits-and-config.md#authentication-limits). Aqui estão os tipos de reclamação disponíveis: <p><p>- **Emitente** <br>- **Público** <br>- **Assunto** <br>- **JWT ID** (identificador JSON Web Token) <p><p>No mínimo, a lista **de Reclamações** deve incluir a **reclamação do Emitente,** que tem um valor que começa com `https://sts.windows.net/` ou como `https://login.microsoftonline.com/` iD do emitente Azure AD. Para obter mais informações sobre estes tipos de reclamações, consulte [as fichas de segurança Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Também pode especificar o seu próprio tipo de reclamação e valor. |
   |||

1. Para adicionar outra reclamação, selecione a partir destas opções:

   * Para adicionar outro tipo de reclamação, **selecione Adicionar a reclamação padrão,** selecione o tipo de reclamação e especifique o valor de reclamação.

   * Para adicionar a sua própria reclamação, **selecione Adicionar reclamação personalizada**. Para mais informações, consulte [como fornecer reclamações opcionais à sua aplicação.](../active-directory/develop/active-directory-optional-claims.md) A sua reclamação personalizada é então armazenada como parte do seu ID JWT; por exemplo, `"tid": "72f988bf-86f1-41af-91ab-2d7cd011db47"` . . 

1. Para adicionar outra política de autorização, **selecione Adicionar a política**. Repita os passos anteriores para definir a política.

1. Quando tiver terminado, selecione **Guardar**.

1. Para incluir o `Authorization` cabeçalho a partir do token de acesso nas saídas do gatilho baseadas no pedido, consulte incluir o [cabeçalho "Autorização" no pedido de saídas de gatilho](#include-auth-header).


Propriedades de fluxo de trabalho, tais como políticas, não aparecem na visão de código da sua aplicação lógica no portal Azure. Para aceder programaticamente às suas políticas, ligue para a seguinte API através do Azure Resource Manager (ARM): `https://management.azure.com/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group-name}/providers/Microsoft.Logic/workflows/{your-workflow-name}?api-version=2016-10-01&_=1612212851820` . Certifique-se de que substitui os valores de espaço reservado para o seu ID de subscrição Azure, nome do grupo de recursos e nome do fluxo de trabalho.


<a name="define-authorization-policy-template"></a>

#### <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

No seu modelo ARM, defina uma política de autorização seguindo estes passos e sintaxe abaixo:

1. Na secção para a `properties` definição de recursos da sua [aplicação lógica,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#logic-app-resource-definition)adicione um `accessControl` objeto, se não existe, que contenha um `triggers` objeto.

   Para obter mais informações sobre o `accessControl` objeto, consulte [as gamas IP de entrada de restrição no modelo Azure Resource Manager](#restrict-inbound-ip-template) e na referência do modelo de [fluxos de trabalho microsoft.logic](/azure/templates/microsoft.logic/2019-05-01/workflows).

1. No `triggers` objeto, adicione um `openAuthenticationPolicies` objeto que contenha o `policies` objeto onde define uma ou mais políticas de autorização.

1. Fornecer um nome para a política de autorização, definir o tipo de apólice para `AAD` , e incluir um conjunto onde você especifica um ou mais tipos de `claims` reclamação.

   No mínimo, o `claims` conjunto deve incluir o tipo de reclamação emitente onde define a propriedade da reclamação e definir o para começar com ou `name` como o `iss` `value` `https://sts.windows.net/` `https://login.microsoftonline.com/` ID do emitente Azure. Para obter mais informações sobre estes tipos de reclamações, consulte [as fichas de segurança Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Também pode especificar o seu próprio tipo de reclamação e valor.

1. Para incluir o `Authorization` cabeçalho a partir do token de acesso nas saídas do gatilho baseadas no pedido, consulte incluir o [cabeçalho "Autorização" no pedido de saídas de gatilho](#include-auth-header).

Aqui está a sintaxe a seguir:

```json
"resources": [
   {
      // Start logic app resource definition
      "properties": {
         "state": "<Enabled-or-Disabled>",
         "definition": {<workflow-definition>},
         "parameters": {<workflow-definition-parameter-values>},
         "accessControl": {
            "triggers": {
               "openAuthenticationPolicies": {
                  "policies": {
                     "<policy-name>": {
                        "type": "AAD",
                        "claims": [
                           {
                              "name": "<claim-name>",
                              "value": "<claim-value>"
                           }
                        ]
                     }
                  }
               }
            },
         },
      },
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
      "apiVersion": "2016-06-01",
      "dependsOn": [
      ]
   }
   // End logic app resource definition
],
```

---

<a name="include-auth-header"></a>

#### <a name="include-authorization-header-in-request-trigger-outputs"></a>Incluir o cabeçalho 'Autorização' no pedido de saídas de gatilho

Para aplicações lógicas que [permitem a autenticação aberta do Azure Ative Directory (Azure AD OAuth)](#enable-oauth) para autorizar chamadas de entrada para aceder a gatilhos baseados em pedidos, pode ativar as saídas do gatilho do Pedido ou do webhook HTTP para incluir o `Authorization` cabeçalho a partir do token de acesso OAuth. Na definição de JSON subjacente do gatilho, adicione e desaccione `operationOptions` a propriedade para `IncludeAuthorizationHeadersInOutputs` . Aqui está um exemplo para o gatilho do pedido:

```json
"triggers": {
   "manual": {
      "inputs": {
         "schema": {}
      },
      "kind": "Http",
      "type": "Request",
      "operationOptions": "IncludeAuthorizationHeadersInOutputs"
   }
}
```

Para obter mais informações, veja estes tópicos:

* [Referência de esquema para tipos de gatilho e ação - Pedido de gatilho](../logic-apps/logic-apps-workflow-actions-triggers.md#request-trigger)
* [Referência de esquema para tipos de gatilho e ação - http Webhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#http-webhook-trigger)
* [Referência de esquema para tipos de gatilho e ação - Opções de operação](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

<a name="azure-api-management"></a>

### <a name="expose-your-logic-app-with-azure-api-management"></a>Exponha a sua app lógica com a Azure API Management

Para adicionar mais [protocolos de autenticação](../active-directory/develop/authentication-vs-authorization.md) à sua aplicação lógica, considere utilizar o serviço [de Gestão API da Azure.](../api-management/api-management-key-concepts.md) Este serviço ajuda-o a expor a sua aplicação lógica como uma API e oferece uma monitorização, segurança, política e documentação rica para qualquer ponto final. A API Management pode expor um ponto final público ou privado para a sua aplicação lógica. Para autorizar o acesso a este ponto final, pode utilizar o Azure AD OAuth, [certificado de cliente](#client-certificate-authentication)ou outras normas de segurança para autorizar o acesso a esse ponto final. Quando a API Management recebe um pedido, o serviço envia o pedido para a sua aplicação lógica, fazendo também as transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas a API Management ligue para a sua aplicação lógica, pode [restringir os endereços IP de entrada da sua aplicação lógica.](#restrict-inbound-ip)

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Restringir endereços IP de entrada

Juntamente com a Assinatura de Acesso Partilhado (SAS), é possível que queira limitar especificamente os clientes que podem ligar para a sua aplicação lógica. Por exemplo, se gerir o seu ponto final de pedido utilizando [a Azure API Management,](../api-management/api-management-key-concepts.md)pode restringir a sua aplicação lógica para aceitar pedidos apenas a partir do endereço IP para a instância do [serviço de Gestão API que cria.](../api-management/get-started-create-service-instance.md)

Independentemente de quaisquer endereços IP que especifique, ainda pode executar uma aplicação lógica que tenha um gatilho baseado em pedidos utilizando a [API de Aplicações Lógicas REST: Workflow Triggers - Executar](/rest/api/logic/workflowtriggers/run) pedido ou utilizando a API Management. No entanto, este cenário ainda requer [autenticação](../active-directory/develop/authentication-vs-authorization.md) contra a API Azure REST. Todos os eventos aparecem no Registo de Auditoria do Azure. Certifique-se de que define as políticas de controlo de acesso em conformidade.

Para restringir os endereços IP de entrada para a sua aplicação lógica, siga estes passos para o portal Azure ou para o modelo do Gestor de Recursos Azure:

<a name="restrict-inbound-ip-portal"></a>

#### <a name="portal"></a>[Portal](#tab/azure-portal)

No [portal Azure,](https://portal.azure.com)este filtro afeta tanto os *gatilhos* como as ações, contrariando a descrição no portal nos **endereços IP de entrada permitidos**. Para configurar este filtro separadamente para gatilhos e para ações, utilize o `accessControl` objeto num modelo de Gestor de Recursos Azure para a sua aplicação lógica ou para a [API de Aplicações Lógicas REST: Workflow - Criar ou Atualizar a operação](/rest/api/logic/workflows/createorupdate).

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **as definições do Fluxo de Trabalho**.

1. Na secção de configuração do **controlo de acesso,** nos **endereços IP de entrada permitidos,** escolha o caminho para o seu cenário:

   * Para tornar a sua aplicação lógica callable apenas como uma app lógica aninhada, utilizando a ação de [Azure Logic Apps](../logic-apps/logic-apps-http-endpoint.md)incorporadas , selecione Only other Logic Apps , que *funciona apenas* quando utiliza a ação **Azure Logic Apps** para chamar a app lógica aninhada. 

     Esta opção escreve um conjunto vazio para o seu recurso de aplicação lógica e requer que apenas chamadas de aplicações lógicas dos pais que usam a ação **de Azure Logic Apps** incorporadas possam desencadear a app lógica aninhada.

   * Para tornar a sua aplicação lógica callable apenas como uma app aninhada utilizando a ação HTTP, selecione **gamas IP específicas**, e *não* **apenas outras aplicações lógicas**. Quando o IP varia para a caixa **de gatilhos** aparecer, insira os [endereços IP de saída](../logic-apps/logic-apps-limits-and-config.md#outbound)da aplicação lógica principal . Uma gama IP válida utiliza estes formatos: *x.x.x.x/x* ou *x.x.x-x.x.x.x.x.x*.

     > [!NOTE]
     > Se utilizar a única opção **de Aplicações Lógicas** e a ação HTTP para ligar para a sua aplicação lógica aninhada, a chamada está bloqueada e obtém um erro "401 Não Autorizado".

   * Para cenários em que pretende restringir as chamadas de entrada de outros IPs, quando os **intervalos IP para a** caixa de gatilhos aparecem, especifique os intervalos de endereço IP que o gatilho aceita. Uma gama IP válida utiliza estes formatos: *x.x.x.x/x* ou *x.x.x-x.x.x.x.x.x*.

1. Opcionalmente, em **Chamadas Restrict para obter mensagens de entrada e saída do histórico de execução para os endereços IP fornecidos,** pode especificar os intervalos de endereço IP para chamadas de entrada que podem aceder a mensagens de entrada e saída no histórico de execução.

<a name="restrict-inbound-ip-template"></a>

#### <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

No seu modelo ARM, especifique os intervalos de endereço IP de entrada permitidos na definição de recursos da sua aplicação lógica utilizando a `accessControl` secção. Nesta secção, utilize as `triggers` `actions` secções , e as `contents` secções opcionais, conforme apropriado, incluindo a `allowedCallerIpAddresses` secção com a propriedade e `addressRange` desavere o valor da propriedade para a gama IP permitida em formato *x.x.x.x/x.x.x.x* 

* Se a sua aplicação lógica aninhada utilizar a opção **Only other Logic Apps,** que permite chamadas de entrada apenas de outras aplicações lógicas que usam a ação de Azure Logic Apps incorporadas, coloque a `allowedCallerIpAddresses` propriedade numa matriz vazia **([]**- e *omita* a `addressRange` propriedade.

* Se a sua aplicação lógica aninhada utilizar a opção **de gamas IP específica** para outras chamadas de entrada, como outras aplicações lógicas que utilizam a ação HTTP, inclua a secção e define a propriedade para a gama IP `allowedCallerIpAddresses` `addressRange` permitida.

Este exemplo mostra uma definição de recurso para uma aplicação lógica aninhada que permite chamadas de entrada apenas a partir de aplicações lógicas que usam a ação de Azure Logic Apps incorporadas:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": []
               },
               "actions": {
                  "allowedCallerIpAddresses": []
               },
               // Optional
               "contents": {
                  "allowedCallerIpAddresses": []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

Este exemplo mostra uma definição de recurso para uma aplicação lógica aninhada que permite chamadas de entrada de aplicações lógicas que usam a ação HTTP:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               <workflow-definition>
            },
            "parameters": {
            },
            "accessControl": {
               "triggers": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               },
               "actions": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     }
                  ]
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicações lógicas

Só é possível permitir que utilizadores ou grupos específicos executem tarefas específicas, tais como gerir, editar e visualizar aplicações lógicas. Para controlar as suas permissões, utilize [o controlo de acesso baseado em funções Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) para que possa atribuir funções personalizadas ou incorporadas aos membros da sua subscrição Azure:

* [Logic App Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite-lhe gerir aplicações lógicas, mas não pode alterar o acesso às mesmos.

* [Operador de aplicativos logicos](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite-lhe ler, ativar e desativar aplicações lógicas, mas não pode editá-las ou atualizá-las.

Para evitar que outros mudem ou apaguem a sua aplicação lógica, pode utilizar [o Azure Resource Lock](../azure-resource-manager/management/lock-resources.md). Esta capacidade impede que outros mudem ou apaguem os recursos produtivos.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acesso à execução de dados de histórico

Durante uma aplicação lógica executada, todos os dados são [encriptados durante](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) o trânsito utilizando a Segurança da Camada de Transporte (TLS) e [em repouso](../security/fundamentals/encryption-atrest.md). Quando a sua aplicação lógica terminar de funcionar, pode ver o histórico para essa execução, incluindo os passos que correram juntamente com o estado, duração, entradas e saídas para cada ação. Este detalhe rico fornece uma visão de como a sua aplicação lógica funcionava e onde você pode começar a resolver problemas que surjam.

Quando vê o histórico de execução da sua aplicação lógica, as Aplicações Lógicas autenticam o seu acesso e, em seguida, fornecem links para as entradas e saídas para os pedidos e respostas para cada execução. No entanto, para ações que lidam com quaisquer palavras-passe, segredos, chaves ou outras informações sensíveis, pretende impedir que outros o visualizam e acedam a esses dados. Por exemplo, se a sua aplicação lógica obtiver um segredo do [Azure Key Vault](../key-vault/general/overview.md) para usar ao autenticar uma ação HTTP, pretende esconder esse segredo da vista.

Para controlar o acesso às entradas e saídas no histórico de execução da sua aplicação lógica, tem estas opções:

* [Restringir o acesso por intervalo de endereços IP](#restrict-ip).

  Esta opção ajuda-o a garantir o acesso ao histórico de execução com base nos pedidos de um intervalo específico de endereços IP.

* [Proteja os dados do histórico através da obfuscação](#obfuscate).

  Em muitos gatilhos e ações, você pode garantir as entradas, saídas ou ambos no histórico de execução de uma aplicação lógica.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por intervalo de endereços IP

Pode limitar o acesso às entradas e saídas no histórico de execução da sua aplicação lógica para que apenas os pedidos de intervalos específicos de endereços IP possam visualizar esses dados.

Por exemplo, para bloquear qualquer pessoa de aceder a entradas e saídas, especifique um intervalo de endereço IP, como `0.0.0.0-0.0.0.0` . Apenas uma pessoa com permissões de administrador pode remover esta restrição, o que proporciona a possibilidade de acesso "just-in-time" aos dados da sua aplicação lógica.

Para especificar as gamas IP permitidas, siga estes passos para o portal Azure ou para o modelo do Gestor de Recursos Azure:

#### <a name="portal"></a>[Portal](#tab/azure-portal)

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **as definições do Fluxo de Trabalho**.

1. Na **configuração do controlo de acesso** Permitido  >  **endereços IP de entrada**, selecione **gamas IP específicas**.

1. Nos **intervalos IP para conteúdos, especifique** as gamas de endereços IP que podem aceder ao conteúdo a partir de entradas e saídas.

   Uma gama IP válida utiliza estes formatos: *x.x.x.x/x* ou *x.x.x-x.x.x.x*

#### <a name="resource-manager-template"></a>[Modelo de gestor de recursos](#tab/azure-resource-manager)

No seu modelo ARM, especifique as gamas IP utilizando a `accessControl` secção com a secção na `contents` definição de recursos da sua aplicação lógica, por exemplo:

``` json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "contents": {
                  "allowedCallerIpAddresses": [
                     {
                        "addressRange": "192.168.12.0/23"
                     },
                     {
                        "addressRange": "2001:0db8::/64"
                     }
                  ]
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

---

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Dados seguros na história da execução utilizando a obfuscação

Muitos gatilhos e ações têm configurações para garantir entradas, saídas ou ambos do histórico de execução de uma aplicação lógica. Antes de utilizar estas definições para ajudá-lo a proteger estes dados, reveja estas considerações:

* Quando obscurece as entradas ou saídas num gatilho ou ação, as Aplicações Lógicas não enviam os dados protegidos para o Azure Log Analytics. Além disso, não é possível adicionar [propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) ao gatilho ou ação para monitorização.

* A [API de Aplicações Lógicas para lidar com o histórico de fluxos de trabalho](/rest/api/logic/) não devolve saídas seguras.

* Para garantir saídas de uma ação que obscureça entradas ou obscureça explicitamente as saídas, ligue manualmente **as Saídas Seguras** nessa ação.

* Certifique-se de que liga **entradas seguras** ou **saídas seguras** em abaixo, onde espera que o histórico de execução obscureça esses dados.

  **Definição de saídas seguras**

  Quando liga manualmente **as Saídas Seguras** num gatilho ou ação, a Logic Apps esconde estas saídas na história da execução. Se uma ação a jusante utilizar explicitamente estas saídas seguras como entradas, a Logic Apps esconde as entradas desta ação no histórico de execução, mas *não permite* a definição de **Entradas Seguras** da ação.

  ![Saídas seguras como entradas e impacto a jusante na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações Compose, Parse JSON e Response têm apenas a definição **de Entradas Seguras.** Quando ligado, a definição também esconde as saídas destas ações. Se estas ações utilizarem explicitamente as saídas seguras a montante como entradas, a Logic Apps esconde as entradas e saídas destas ações, mas *não permite a* definição de Entradas Seguras **destas** ações. Se uma ação a jusante utilizar explicitamente as saídas ocultas das ações Compose, Parse JSON ou Response como entradas, as Aplicações *Lógicas não escondem as entradas ou saídas desta ação a jusante*.

  ![Saídas garantidas como entradas com impacto a jusante em ações específicas](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Definição de entradas seguras**

  Quando liga manualmente **as entradas seguras** num gatilho ou ação, a Logic Apps esconde estas entradas no histórico de execução. Se uma ação a jusante utilizar explicitamente as saídas visíveis desse gatilho ou ação como entradas, a Logic Apps esconde as entradas desta ação a jusante no histórico de execução, mas *não permite entradas* **seguras** nesta ação e não esconde as saídas desta ação.

  ![Entradas seguras e impacto a jusante na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se as ações Compose, Parse JSON e Response utilizarem explicitamente as saídas visíveis do gatilho ou ação que tem as entradas seguras, a Logic Apps esconde as entradas e saídas destas ações, mas *não permite a* definição de Entradas Seguras **destas** ações. Se uma ação a jusante utilizar explicitamente as saídas ocultas das ações Compose, Parse JSON ou Response como entradas, as Aplicações *Lógicas não escondem as entradas ou saídas desta ação a jusante*.

  ![Entradas seguras e impacto a jusante em ações específicas](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Entradas e saídas seguras no designer

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

   ![Aplicativo de lógica aberta no Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou ação onde pretende proteger dados sensíveis, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

   ![Abra as definições de gatilho ou ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ligue as **entradas seguras,** **as saídas seguras** ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ligue "Entradas seguras" ou "Saídas Seguras"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou o gatilho mostram agora um ícone de bloqueio na barra de título.

   ![Barra de título de ação ou gatilho mostra ícone de bloqueio](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Os tokens que representam saídas seguras de ações anteriores também mostram ícones de bloqueio. Por exemplo, quando seleciona tal saída da lista de conteúdos dinâmicos para utilizar numa ação, esse token mostra um ícone de bloqueio.

   ![Selecione token para saída segura](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois de a aplicação lógica ser executada, pode ver a história para essa execução.

   1. No painel de **visão geral** da aplicação lógica, selecione a execução que pretende ver.

   1. No painel de **aplicações Logic run,** expanda as ações que pretende rever.

      Se optar por ocultar tanto as entradas como as saídas, esses valores parecem agora ocultos.

      ![Entradas e saídas escondidas na história da execução](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Inserires e saídas seguras na vista de código

Na definição subjacente de gatilho ou ação, adicione ou atualize a `runtimeConfiguration.secureData.properties` matriz com qualquer um dos dois valores:

* `"inputs"`: Assegura entradas na história da execução.
* `"outputs"`: Assegura saídas na história da execução.

```json
"<trigger-or-action-name>": {
   "type": "<trigger-or-action-type>",
   "inputs": {
      <trigger-or-action-inputs>
   },
   "runtimeConfiguration": {
      "secureData": {
         "properties": [
            "inputs",
            "outputs"
         ]
      }
   },
   <other-attributes>
}
```

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso às entradas de parâmetros

Se implementar em diferentes ambientes, considere parametrizar os valores na definição de fluxo de trabalho que variam em função desses ambientes. Desta forma, pode evitar dados codificados através de um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para implementar a sua aplicação lógica, proteger dados sensíveis definindo parâmetros seguros e passar esses dados como entradas separadas através dos parâmetros do modelo utilizando um ficheiro de [parâmetros.](../azure-resource-manager/templates/template-parameters.md) [](../azure-resource-manager/templates/parameter-files.md)

Por exemplo, se autenticar ações HTTP com [Azure Ative Directory Open Authentication](#azure-active-directory-oauth-authentication) (Azure AD OAuth), pode definir e ocultar os parâmetros que aceitam o ID do cliente e o segredo do cliente que são utilizados para a autenticação. Para definir estes parâmetros na sua aplicação lógica, utilize a `parameters` secção na definição de fluxo de trabalho da sua aplicação lógica e no modelo de Gestor de Recursos para implementação. Para ajudar a garantir valores de parâmetros que não deseja mostrados ao editar a sua aplicação lógica ou visualizar o histórico de execução, defina os parâmetros utilizando o `securestring` ou tipo e use a `secureobject` codificação conforme necessário. Os parâmetros que têm este tipo não são devolvidos com a definição de recurso e não estão acessíveis ao visualizar o recurso após a implementação. Para aceder a estes valores de parâmetro durante o tempo de funcionamento, utilize a expressão dentro da `@parameters('<parameter-name>')` definição de fluxo de trabalho. Esta expressão é avaliada apenas em tempo de execução e é descrita pela Linguagem de Definição do [Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md)

> [!NOTE]
> Se utilizar um parâmetro num cabeçalho ou corpo de pedido, esse parâmetro poderá ser visível quando visualizar o histórico de execução da sua aplicação lógica e o pedido HTTP de saída. Certifique-se de que também define as suas políticas de acesso ao conteúdo em conformidade.
> Também pode usar [a obfuscação](#obfuscate) para ocultar entradas e saídas no seu histórico de execução. Os cabeçalhos de autorização nunca são visíveis através de entradas ou saídas. Então, se um segredo é usado lá, esse segredo não é recuperável.

Para mais informações, consulte estas secções neste tópico:

* [Parâmetros seguros nas definições de fluxo de trabalho](#secure-parameters-workflow)
* [Dados seguros na história da execução utilizando a obfuscação](#obfuscate)

Se [automatizar a implementação de aplicações lógicas utilizando modelos de Gestor de Recursos,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode definir [parâmetros](../azure-resource-manager/templates/template-parameters.md)de modelo seguros , que são avaliados na implementação, utilizando os `securestring` e `secureobject` tipos. Para definir os parâmetros do modelo, use a secção de nível superior do seu `parameters` modelo, que é separada e diferente da secção de definição de fluxo de `parameters` trabalho. Para fornecer os valores para os parâmetros do modelo, utilize um [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md)separado .

Por exemplo, se utilizar segredos, pode definir e usar parâmetros de modelo seguros que recuperam esses segredos do [Azure Key Vault](../key-vault/general/overview.md) durante a sua implantação. Em seguida, pode fazer referência ao cofre de chaves e ao segredo no seu ficheiro de parâmetros. Para obter mais informações, veja estes tópicos:

* [Passe valores sensíveis na implementação utilizando o Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)
* [Parâmetros seguros nos modelos do Gestor de Recursos Azure](#secure-parameters-deployment-template) mais tarde neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Parâmetros seguros nas definições de fluxo de trabalho

Para proteger informações sensíveis na definição de fluxo de trabalho da sua aplicação lógica, utilize parâmetros seguros para que esta informação não seja visível depois de salvar a sua aplicação lógica. Por exemplo, suponha que tenha uma ação HTTP requer autenticação básica, que utiliza um nome de utilizador e senha. Na definição de fluxo de trabalho, a `parameters` secção define os `basicAuthPasswordParam` `basicAuthUsernameParam` parâmetros utilizando o `securestring` tipo. A definição de ação refere então estes parâmetros na `authentication` secção.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('basicAuthUsernameParam')",
               "password": "@parameters('basicAuthPasswordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "basicAuthPasswordParam": {
         "type": "securestring"
      },
      "basicAuthUsernameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Parâmetros seguros nos modelos do Gestor de Recursos Azure

Um [modelo de Gestor de Recursos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) para uma aplicação lógica tem várias `parameters` secções. Para proteger palavras-passe, chaves, segredos e outras informações sensíveis, defina parâmetros seguros ao nível do modelo e ao nível de definição de fluxo de trabalho utilizando o `securestring` ou `secureobject` o tipo. Em seguida, pode armazenar estes valores no [Cofre da Chave Azure](../key-vault/general/overview.md) e utilizar o [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md) para fazer referência ao cofre de chaves e ao segredo. O seu modelo recupera essa informação na implementação. Para obter mais informações, consulte [valores sensíveis de passe na implementação utilizando o Cofre da Chave Azure](../azure-resource-manager/templates/key-vault-parameter.md).

Aqui está mais informações sobre estas `parameters` secções:

* No nível superior do modelo, uma `parameters` secção define os parâmetros para os valores que o modelo utiliza na *implementação*. Por exemplo, estes valores podem incluir cadeias de ligação para um ambiente de implantação específico. Em seguida, pode armazenar estes valores num [ficheiro de parâmetros](../azure-resource-manager/templates/parameter-files.md)separado, o que facilita a alteração destes valores.

* Dentro da definição de recursos da sua aplicação lógica, mas fora da definição de fluxo de trabalho, uma `parameters` secção especifica os valores para os parâmetros da definição de fluxo de trabalho. Nesta secção, pode atribuir estes valores utilizando expressões de modelo que referenciam os parâmetros do seu modelo. Estas expressões são avaliadas na implantação.

* Dentro da definição de fluxo de trabalho, uma `parameters` secção define os parâmetros que a sua aplicação lógica utiliza no tempo de execução. Em seguida, pode referenciar estes parâmetros dentro do fluxo de trabalho da sua aplicação lógica utilizando expressões de definição de fluxo de trabalho, que são avaliadas em tempo de execução.

Este modelo de exemplo que tem múltiplas definições de parâmetros seguros que usam o `securestring` tipo:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma palavra-passe que é depois passada para o parâmetro da definição de fluxo de trabalho `basicAuthPasswordParam` |
| `TemplateUsernameParam` | Um parâmetro de modelo que aceita um nome de utilizador que é depois passado para o parâmetro da definição de fluxo de trabalho `basicAuthUserNameParam` |
| `basicAuthPasswordParam` | Um parâmetro de definição de fluxo de trabalho que aceita a palavra-passe para autenticação básica numa ação HTTP |
| `basicAuthUserNameParam` | Um parâmetro de definição de fluxo de trabalho que aceita o nome de utilizador para autenticação básica numa ação HTTP |
|||

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "TemplatePasswordParam": {
         "type": "securestring"
      },
      "TemplateUsernameParam": {
         "type": "securestring"
      },
      "LogicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('basicAuthUsernameParam')",
                           "password": "@parameters('basicAuthPasswordParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "basicAuthPasswordParam": {
                     "type": "securestring"
                  },
                  "basicAuthUsernameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "basicAuthPasswordParam": {
                  "value": "[parameters('TemplatePasswordParam')]"
               },
               "basicAuthUsernameParam": {
                  "value": "[parameters('TemplateUsernameParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

<a name="secure-outbound-requests"></a>

## <a name="access-for-outbound-calls-to-other-services-and-systems"></a>Acesso a chamadas de saída para outros serviços e sistemas

Com base na capacidade do ponto final do destino, as chamadas de saída enviadas pelo [gatilho HTTP ou a ação HTTP](../connectors/connectors-native-http.md), suportam a encriptação e são protegidas com segurança da camada de transporte [(TLS) 1.0, 1.1 ou 1.2](https://en.wikipedia.org/wiki/Transport_Layer_Security), anteriormente conhecida como Camada de Tomadas Seguras (SSL). A Logic Apps negoceia com o ponto final alvo sobre a utilização da versão mais alta possível que é suportada. Por exemplo, se o ponto final do alvo suportar 1.2, o gatilho http ou ação utiliza primeiro o 1.2. Caso contrário, o conector utiliza a próxima versão suportada mais alta.

Aqui estão informações sobre certificados auto-assinados TLS/SSL:

* Para aplicações lógicas no ambiente global e multi-inquilino Azure, o conector HTTP não permite certificados TLS/SSL auto-assinados. Se a sua aplicação lógica fizer uma chamada HTTP para um servidor e apresentar um certificado auto-assinado TLS/SSL, a chamada HTTP falha com um `TrustFailure` erro.

* Para aplicações lógicas num [ambiente de serviço de integração (ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)o conector HTTP permite certificados auto-assinados para apertos de mão TLS/SSL. No entanto, primeiro deve [ativar o suporte de certificado auto-assinado](../logic-apps/create-integration-service-environment-rest-api.md#request-body) para um ISE existente ou novo ISE, utilizando a API de Aplicações Lógicas e instalar o certificado público no `TrustedRoot` local.

Aqui estão mais formas de ajudar a proteger pontos finais que lidam com chamadas enviadas a partir da sua aplicação lógica:

* [Adicionar autenticação a pedidos de saída](#add-authentication-outbound).

  Quando utiliza o gatilho HTTP ou ação para enviar chamadas de saída, pode adicionar autenticação ao pedido enviado pela sua aplicação lógica. Por exemplo, pode selecionar estes tipos de autenticação:

  * [Autenticação básica](#basic-authentication)

  * [Autenticação de certificado de cliente](#client-certificate-authentication)

  * [Autenticação ativa do Diretório OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerida](#managed-identity-authentication)

* Restringir o acesso a partir de endereços IP de aplicações lógicas.

  Todas as chamadas para pontos finais de aplicações lógicas têm origem em endereços IP designados específicos que são baseados nas regiões das suas aplicações lógicas. Pode adicionar filtragem que aceita pedidos apenas a partir desses endereços IP. Para obter estes endereços IP, consulte [Limites e configuração para Azure Logic Apps](logic-apps-limits-and-config.md#firewall-ip-configuration).

* Melhorar a segurança das ligações aos sistemas no local.

  A Azure Logic Apps proporciona integração com estes serviços para ajudar a fornecer uma comunicação mais segura e fiável no local.

  * Gateway de dados no local

    Muitos conectores geridos em Azure Logic Apps facilitam ligações seguras a sistemas no local, tais como Sistema de Ficheiros, SQL, SharePoint e DB2. O gateway envia dados de fontes no local em canais encriptados através do Azure Service Bus. Todo o tráfego tem origem como tráfego de saída seguro do agente de gateway. Saiba [como funciona o portal de dados no local.](logic-apps-gateway-install.md#gateway-cloud-service)

  * Conecte-se através da Azure API Management

    [A Azure API Management](../api-management/api-management-key-concepts.md) fornece opções de conexão no local, tais como rede privada virtual local-a-local e integração [ExpressRoute](../expressroute/expressroute-introduction.md) para sistemas de procuração e comunicação seguros para sistemas no local. Se tiver uma API que dá acesso ao seu sistema de acesso ao seu sistema no local, e expôs essa API criando uma instância de [serviço de Gestão API,](../api-management/get-started-create-service-instance.md)pode chamar a API no fluxo de trabalho da sua aplicação lógica selecionando o gatilho ou ação da API Management incorporada no Logic App Designer.

    > [!NOTE]
    > O conector mostra apenas os serviços de Gestão da API onde tem permissões para visualizar e conectar, mas não mostra serviços de Gestão de API baseados no consumo.

    1. No Logic App Designer, `api management` insira na caixa de pesquisa. Escolha o passo com base na adição de um gatilho ou de uma ação:<p>

       * Se estiver a adicionar um gatilho, que é sempre o primeiro passo no seu fluxo de trabalho, **selecione Escolha um gatilho API Management Azure**.

       * Se estiver a adicionar uma ação, **selecione Escolha uma ação de Gestão API Azure**.

       Este exemplo adiciona um gatilho:

       ![Adicione o gatilho de gestão API Azure](./media/logic-apps-securing-a-logic-app/select-api-management.png)

    1. Selecione a sua instância de serviço de Gestão API previamente criada.

       ![Selecione a instância de serviço de gestão da API](./media/logic-apps-securing-a-logic-app/select-api-management-service-instance.png)

    1. Selecione a chamada API para usar.

       ![Selecione a API existente](./media/logic-apps-securing-a-logic-app/select-api.png)

<a name="add-authentication-outbound"></a>

### <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a chamadas de saída

Os pontos finais HTTP e HTTPS suportam vários tipos de autenticação. Em alguns gatilhos e ações que utiliza para o envio de chamadas ou pedidos de saída para estes pontos finais, pode especificar um tipo de autenticação. No Logic App Designer, os gatilhos e ações que suportam a escolha de um tipo de autenticação têm uma propriedade **autenticação.** No entanto, esta propriedade pode nem sempre aparecer por defeito. Nestes casos, no gatilho ou ação, abra a nova lista **de parâmetros** e selecione **Autenticação**.

> [!IMPORTANT]
> Para proteger informações sensíveis que a sua aplicação lógica trata, utilize parâmetros seguros e codifique os dados conforme necessário.
> Para obter mais informações sobre a utilização e fixação de parâmetros, consulte [acesso às entradas de parâmetros](#secure-action-parameters).

<a name="authentication-types-supported-triggers-actions"></a>

#### <a name="authentication-types-for-triggers-and-actions-that-support-authentication"></a>Tipos de autenticação para gatilhos e ações que suportam a autenticação

Esta tabela identifica os tipos de autenticação que estão disponíveis nos gatilhos e ações onde pode selecionar um tipo de autenticação:

| Tipo de autenticação | Gatilhos e ações apoiados |
|---------------------|--------------------------------|
| [Básica](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Certificado de Cliente](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Diretório Ativo OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Não processado](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Identidade gerida](#managed-identity-authentication) | **Gatilhos e ações incorporados** <p><p>Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP Webhook <p><p>**Conectores geridos** <p><p>Azure AD Identity Protection, Azure Automation, Azure Container Instance, Azure Data Explorer, Azure Data Factory, Azure Data Lake, Azure Event Grid, Azure IoT Central V3, Azure Key Vault, Azure Resource Manager, Azure Sentinel, HTTP com Azure AD <p><p>**Nota:** O suporte para conectores geridos encontra-se atualmente em pré-visualização. |
|||

<a name="basic-authentication"></a>

#### <a name="basic-authentication"></a>Autenticação básica

Se a opção [Basic](../active-directory-b2c/secure-rest-api.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | Básico | O tipo de autenticação a utilizar |
| **Nome de Utilizador** | `username` | Yes | <*nome do utilizador*>| O nome de utilizador para autenticar o acesso ao ponto final do serviço alvo |
| **Palavra-passe** | `password` | Yes | <*senha*> | A palavra-passe para autenticar o acesso ao ponto final do serviço alvo |
||||||

Quando utilizar [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num [modelo do Azure Resource Manager para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros em tempo de execução. Este exemplo de definição de ação HTTP especifica a autenticação `type` como e utiliza a `Basic` [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Basic",
         "username": "@parameters('userNameParam')",
         "password": "@parameters('passwordParam')"
      }
  },
  "runAfter": {}
}
```

<a name="client-certificate-authentication"></a>

#### <a name="client-certificate-authentication"></a>Autenticação do Certificado de Cliente

Se estiver disponível a opção ['Cliente's](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) Certificate' especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | **Certificado de Cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a utilizar. Pode gerir certificados com [a Azure API Management.](../api-management/api-management-howto-mutual-certificates.md) <p></p>**Nota:** Os conectores personalizados não suportam a autenticação baseada em certificados tanto para chamadas de entrada como para saída. |
| **Pfx** | `pfx` | Yes | <*codificado-pfx-file-conteúdo*> | O conteúdo codificado de base64 a partir de um ficheiro de Troca de Informações Pessoais (PFX) <p><p>Para converter o ficheiro PFX em formato codificado base64, pode utilizar o PowerShell seguindo estes passos: <p>1. Guarde o conteúdo do certificado numa variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converter o conteúdo do certificado utilizando a `ToBase64String()` função e guardar esse conteúdo num ficheiro de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` <p><p>**Resolução de problemas**: Se utilizar o `cert mmc/PowerShell` comando, poderá obter este erro: <p><p>`Could not load the certificate private key. Please check the authentication certificate password is correct and try again.` <p><p>Para resolver este erro, tente converter o ficheiro PFX num ficheiro PEM e de volta utilizando o `openssl` comando: <p><p>`openssl pkcs12 -in certificate.pfx -out certificate.pem` <br>`openssl pkcs12 -in certificate.pem -export -out certificate2.pfx` <p><p>Depois, quando obtém a cadeia codificada base64 para o novo ficheiro PFX convertido do certificado, a cadeia funciona agora em Azure Logic Apps. |
| **Palavra-passe** | `password`| No | <*password-para-pfx-file*> | A senha de acesso ao ficheiro PFX |
|||||

Quando utilizar [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num [modelo do Azure Resource Manager para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros em tempo de execução. Este exemplo de definição de ação HTTP especifica a autenticação `type` como e utiliza a `ClientCertificate` [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ClientCertificate",
         "pfx": "@parameters('pfxParam')",
         "password": "@parameters('passwordParam')"
      }
   },
   "runAfter": {}
}
```

Para obter mais informações sobre a segurança de serviços utilizando a autenticação do certificado do cliente, consulte estes tópicos:

* [Melhorar a segurança das APIs utilizando a autenticação de certificados de cliente na Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Melhorar a segurança dos serviços back-end utilizando a autenticação de certificados de cliente na Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Melhore a segurança para o seu serviço RESTfuL utilizando certificados de cliente](../active-directory-b2c/secure-rest-api.md)
* [Credenciais de certificado para autenticação de pedidos](../active-directory/develop/active-directory-certificate-credentials.md)
* [Utilizar um certificado TLS/SSL no código no Serviço de Aplicações do Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

#### <a name="azure-active-directory-open-authentication"></a>Autenticação aberta do Diretório Ativo Azure

Nos detonadores de pedidos, pode utilizar [a Azure Ative Directory Open Authentication (Azure AD OAuth)](../active-directory/develop/index.yml)para autenticar chamadas recebidas depois de [configurar as políticas de autorização AZure AD](#enable-oauth) para a sua aplicação lógica. Para todos os outros gatilhos e ações que forneçam o tipo de autenticação **OAuth do Diretório Ativo** para que possa selecionar, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | **Diretório Ativo OAuth** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a utilizar. As Aplicações Lógicas seguem atualmente o [protocolo OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Autoridade** | `authority` | No | <*URL-para-autoridade-emitente-simbólico*> | A URL para a autoridade que fornece o token de acesso. Por predefinição, este valor é `https://login.windows.net` . |
| **Inquilino** | `tenant` | Yes | <*inquilino-ID*> | A iD do inquilino para o inquilino da Ad Azure |
| **Audiência** | `audience` | Yes | <*recursos para autorizar*> | O recurso que pretende utilizar para autorização, por exemplo, `https://management.core.windows.net/` |
| **ID de Cliente** | `clientId` | Yes | <*iD cliente*> | O ID do cliente para a app solicitando autorização |
| **Tipo credencial** | `credentialType` | Yes | Certificado <br>ou <br>Segredo | O tipo de credencial que o cliente usa para solicitar autorização. Esta propriedade e valor não aparecem na definição subjacente da sua aplicação lógica, mas determina as propriedades que aparecem para o tipo de credencial selecionado. |
| **Segredo** | `secret` | Sim, mas só para o tipo de credencial "Secreto". | <*cliente-segredo*> | O segredo do cliente para solicitar autorização |
| **Pfx** | `pfx` | Sim, mas apenas para o tipo credencial "Certificado". | <*codificado-pfx-file-conteúdo*> | O conteúdo codificado de base64 a partir de um ficheiro de Troca de Informações Pessoais (PFX) |
| **Palavra-passe** | `password` | Sim, mas apenas para o tipo credencial "Certificado". | <*password-para-pfx-file*> | A senha de acesso ao ficheiro PFX |
|||||

Quando utilizar [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num [modelo do Azure Resource Manager para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros em tempo de execução. Este exemplo de definição de ação HTTP especifica a autenticação `type` como , o tipo de `ActiveDirectoryOAuth` credencial como , e utiliza a `Secret` [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "ActiveDirectoryOAuth",
         "tenant": "@parameters('tenantIdParam')",
         "audience": "https://management.core.windows.net/",
         "clientId": "@parameters('clientIdParam')",
         "credentialType": "Secret",
         "secret": "@parameters('secretParam')"
     }
   },
   "runAfter": {}
}
```

<a name="raw-authentication"></a>

#### <a name="raw-authentication"></a>Autenticação bruta

Se a opção **Raw** estiver disponível, pode utilizar este tipo de autenticação quando tiver de utilizar [esquemas de autenticação](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) que não sigam o [protocolo OAuth 2.0](https://oauth.net/2/). Com este tipo, cria manualmente o valor do cabeçalho de autorização que envia com o pedido de saída e especifica o valor do cabeçalho no seu gatilho ou ação.

Por exemplo, aqui está um cabeçalho de amostra para um pedido HTTPS que segue o [protocolo OAuth 1.0](https://tools.ietf.org/html/rfc5849):

```text
Authorization: OAuth realm="Photos",
   oauth_consumer_key="dpf43f3p2l4k3l03",
   oauth_signature_method="HMAC-SHA1",
   oauth_timestamp="137131200",
   oauth_nonce="wIjqoS",
   oauth_callback="http%3A%2F%2Fprinter.example.com%2Fready",
   oauth_signature="74KNZJeDHnMBp0EMJ9ZHt%2FXKycU%3D"
```

No gatilho ou ação que suporta a autenticação bruta, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | Não processado | O tipo de autenticação a utilizar |
| **Valor** | `value` | Yes | <*valor de autorização-cabeçalho*> | O valor do cabeçalho de autorização a utilizar para a autenticação |
||||||

Quando utilizar [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num [modelo do Azure Resource Manager para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros em tempo de execução. Este exemplo de definição de ação HTTP especifica a autenticação `type` como , e utiliza a `Raw` [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET",
      "uri": "@parameters('endpointUrlParam')",
      "authentication": {
         "type": "Raw",
         "value": "@parameters('authHeaderParam')"
      }
   },
   "runAfter": {}
}
```

<a name="managed-identity-authentication"></a>

#### <a name="managed-identity-authentication"></a>Autenticação de identidade gerida

Quando a opção [de identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) estiver disponível no gatilho ou [ação que suporta a autenticação de identidade gerida,](#add-authentication-outbound)a sua aplicação lógica pode utilizar a identidade atribuída ao sistema ou uma *única* identidade atribuída manualmente ao utilizador para autenticar o acesso aos recursos Azure que estão protegidos pelo Azure Ative Directory (Azure AD), em vez de credenciais, segredos ou fichas AD Azure. O Azure gere esta identidade para si e ajuda-o a proteger as suas credenciais porque não tem segredos de gestão ou usa diretamente fichas AD AZure. Saiba mais sobre [os serviços Azure que suportam identidades geridas para a autenticação AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Antes da sua aplicação lógica poder utilizar uma identidade gerida, siga os passos no [acesso autenticado aos recursos do Azure utilizando identidades geridas em Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Estes passos permitem a identidade gerida na sua aplicação lógica e configuram o acesso dessa identidade ao recurso Azure alvo.

1. Antes de uma função Azure poder utilizar uma identidade gerida, [primeiro permitir a autenticação para funções Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

1. No gatilho ou ação que suporta a utilização de uma identidade gerida, forneça estas informações:

   **Gatilhos e ações incorporados**

   | Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Yes | **Identidade Gerida** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a utilizar |
   | **Identidade Gerida** | `identity` | Yes | * **Identidade gerida atribuída ao sistema** <br>ou <br>`SystemAssigned` <p><p>* <*nome de identidade atribuído pelo utilizador*> | A identidade gerida para usar |
   | **Audiência** | `audience` | Yes | <*destino-recursos-ID*> | O ID de recursos para o recurso-alvo a que pretende aceder. <p>Por exemplo, `https://storage.azure.com/` torna os [tokens de acesso para](../active-directory/develop/access-tokens.md) autenticação válidos para todas as contas de armazenamento. No entanto, também pode especificar um URL de serviço de raiz, como por exemplo `https://fabrikamstorageaccount.blob.core.windows.net` para uma conta de armazenamento específica. <p>**Nota:** A propriedade **do Público** pode estar escondida em alguns gatilhos ou ações. Para tornar esta propriedade visível, no gatilho ou ação, abra a nova lista **de parâmetros** e selecione **Audience**. <p><p>**Importante**: Certifique-se de que este ID de recurso-alvo *corresponde exatamente* ao valor que a Azure AD espera, incluindo quaisquer cortes de rasto necessários. Assim, a identificação de `https://storage.azure.com/` recursos para todas as contas de Armazenamento Azure Blob requer um corte de fuga. No entanto, a identificação de recursos para uma conta de armazenamento específica não requer um corte de fuga. Para encontrar estes IDs de recursos, consulte [os serviços Azure que suportam a Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   |||||

   Quando utilizar [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num [modelo do Azure Resource Manager para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros em tempo de execução. Por exemplo, esta definição de ação HTTP especifica a autenticação `type` como e utiliza a `ManagedServiceIdentity` [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

   ```json
   "HTTP": {
      "type": "Http",
      "inputs": {
         "method": "GET",
         "uri": "@parameters('endpointUrlParam')",
         "authentication": {
            "type": "ManagedServiceIdentity",
            "identity": "SystemAssigned",
            "audience": "https://management.azure.com/"
         },
      },
      "runAfter": {}
   }
   ```

   **Triggers e ações do conector gerido**

   | Propriedade (designer) | Necessário | Valor | Descrição |
   |---------------------|----------|-------|-------------|
   | **Nome da ligação** | Yes | <*nome de conexão*> ||
   | **Identidade gerida** | Yes | **Identidade gerida atribuída pelo sistema** <br>ou <br> <*nome de identidade gerido pelo utilizador*> | O tipo de autenticação a utilizar |
   |||||


<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Bloquear a criação de ligações

Se a sua organização não permitir a ligação a recursos específicos utilizando os seus conectores em Azure Logic Apps, pode [bloquear a capacidade de criar essas ligações](../logic-apps/block-connections-connectors.md) para conectores específicos em fluxos de trabalho de aplicações lógicas utilizando a [Azure Policy.](../governance/policy/overview.md) Para obter mais informações, consulte [as ligações do Bloco criadas por conectores específicos em Azure Logic Apps](../logic-apps/block-connections-connectors.md).

<a name="isolation-logic-apps"></a>

## <a name="isolation-guidance-for-logic-apps"></a>Orientação de isolamento para apps lógicas

Você pode usar aplicações lógicas Azure no [Governo Azure](../azure-government/documentation-government-welcome.md) apoiando todos os níveis de impacto nas regiões descritas pelo [Azure Government Impact Level 5 Isolation Guidance](../azure-government/documentation-government-impact-level-5.md#azure-logic-apps) e o Us Department of Defense Cloud [Computing Security Requirements Guide (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/SRG/index.html). Para satisfazer estes requisitos, a Logic Apps suporta a capacidade de criar e executar fluxos de trabalho num ambiente com recursos dedicados para que possa reduzir o impacto de desempenho de outros inquilinos da Azure nas suas apps lógicas e evitar partilhar recursos informáticos com outros inquilinos.

* Para executar o seu próprio código ou executar a transformação de XML, [crie e chame uma função Azure](../logic-apps/logic-apps-azure-functions.md), em vez de usar a capacidade de código [inline](../logic-apps/logic-apps-add-run-inline-code.md) ou fornecer [conjuntos para usar como mapas,](../logic-apps/logic-apps-enterprise-integration-maps.md)respectivamente. Além disso, crie o ambiente de hospedagem para a sua aplicação de função para cumprir os seus requisitos de isolamento.

  Por exemplo, para satisfazer os requisitos do Nível de Impacto 5, crie a sua aplicação de função com o [plano de Serviço de Aplicações](../azure-functions/dedicated-plan.md) utilizando o [nível de preços **isolado,**](../app-service/overview-hosting-plans.md) juntamente com um [Ambiente de Serviço de Aplicações (ASE)](../app-service/environment/intro.md) que também utiliza o nível de preços **isolado.** Neste ambiente, as aplicações de função funcionam em máquinas virtuais dedicadas Azure e redes virtuais dedicadas Azure, que proporcionam isolamento de rede em cima do isolamento computacional para as suas apps e capacidades de escala máxima. Para mais informações, consulte [Azure Government Impact Level 5 Isolation Guidance - Azure Functions](../azure-government/documentation-government-impact-level-5.md#azure-functions).

  Para obter mais informações, veja estes tópicos:<p>

  * [Planos de Serviço de Aplicações Azure](../app-service/overview-hosting-plans.md)
  * [Opções de rede das Funções do Azure](../azure-functions/functions-networking-options.md)
  * [Azure Anfitriões Dedicados para máquinas virtuais](../virtual-machines/dedicated-hosts.md)
  * [Isolamento de máquina virtual em Azure](../virtual-machines/isolation.md)
  * [Implementar serviços dedicados da Azure em redes virtuais](../virtual-network/virtual-network-for-azure-services.md)

* Para criar aplicações lógicas que funcionam com recursos dedicados e que podem aceder a recursos protegidos por uma rede virtual Azure, pode criar um [ambiente de serviço de integração (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * Algumas redes virtuais Azure utilizam pontos finais privados[(Azure Private Link](../private-link/private-link-overview.md)) para fornecer acesso a serviços Azure PaaS, tais como Azure Storage, Azure Cosmos DB, ou Azure SQL Database, serviços de parceiros ou serviços de clientes que estão hospedados no Azure. Se as suas aplicações lógicas precisam de acesso a redes virtuais que usam pontos finais privados, tem de criar, implementar e executar essas aplicações lógicas dentro de um ISE.

  * Para obter mais controlo sobre as chaves de encriptação utilizadas pelo Azure Storage, pode configurar, utilizar e gerir a sua própria chave utilizando [o Cofre da Chave Azure](../key-vault/general/overview.md). Esta capacidade também é conhecida como "Bring Your Own Key" (BYOK), e a sua chave é chamada de "chave gerida pelo cliente". Para obter mais informações, consulte [Configurar as chaves geridas pelo cliente para encriptar os dados em repouso para ambientes de serviço de integração (ISEs) em Azure Logic Apps](../logic-apps/customer-managed-keys-integration-service-environment.md).

Para obter mais informações, veja estes tópicos:

* [Isolamento na Nuvem Pública de Azure](../security/fundamentals/isolation-choices.md)
* [Segurança para aplicações iaaS altamente sensíveis em Azure](/azure/architecture/reference-architectures/n-tier/high-security-iaas)

## <a name="next-steps"></a>Passos seguintes

* [Linha de base de segurança Azure para Azure Logic Apps](../logic-apps/security-baseline.md)
* [Automatizar a implementação para apps Azure Logic](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Monitorizar aplicações lógicas](../logic-apps/monitor-logic-apps-log-analytics.md)
