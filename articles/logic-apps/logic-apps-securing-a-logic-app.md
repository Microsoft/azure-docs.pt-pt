---
title: Proteger o acesso e os dados
description: Acesso seguro a inputs, saídas, gatilhos baseados em pedidos, histórico de execução, tarefas de gestão e acesso a outros recursos em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: b00dae5c807cb8bec3b9e345c9b2af2c227139b7
ms.sourcegitcommit: 0fda81f271f1a668ed28c55dcc2d0ba2bb417edd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2020
ms.locfileid: "82901115"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Acesso seguro e dados em Aplicações Lógicas Azure

Para controlar o acesso e proteger dados sensíveis em Aplicações Lógicas Azure, pode configurar segurança para estas áreas:

* [Acesso a gatilhos baseados em pedidos](#secure-triggers)
* [Acesso a operações de aplicações lógicas](#secure-operations)
* [Acesso a inputs e saídas de histórico](#secure-run-history)
* [Acesso a entradas de parâmetros](#secure-action-parameters)
* [Acesso a serviços e sistemas chamados de aplicações lógicas](#secure-outbound-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Acesso a gatilhos baseados em pedidos

Se a sua aplicação lógica utilizar um gatilho baseado em pedidos, que recebe chamadas ou pedidos recebidos, como o [gatilho De pedido](../connectors/connectors-native-reqres.md) ou [webhook,](../connectors/connectors-native-webhook.md) pode limitar o acesso para que apenas os clientes autorizados possam ligar para a sua aplicação lógica. Todos os pedidos recebidos por uma aplicação lógica são encriptados e protegidos com transport layer security (TLS), anteriormente conhecido como Secure Sockets Layer (SSL), protocolo.

Aqui estão as opções que podem ajudá-lo a garantir o acesso a este tipo de gatilho:

* [Gerar assinaturas de acesso partilhado](#sas)
* [Ativar a autenticação aberta do diretório ativo azure (Azure AD OAuth)](#enable-oauth)
* [Restringir os endereços IP de entrada](#restrict-inbound-ip-addresses)
* [Adicionar autenticação aberta do diretório ativo Azure (Azure AD OAuth) ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar assinaturas de acesso partilhado (SAS)

Cada ponto final de pedido numa aplicação lógica tem uma [Assinatura de Acesso Partilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) no URL do ponto final, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL `sp`contém `sv`o `sig` parâmetro , e consulta descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica permissões para os métodos HTTP permitidos de utilização. |
| `sv` | Especifica a versão SAS a utilizar para gerar a assinatura. |
| `sig` | Especifica a assinatura a utilizar para autenticar o acesso ao gatilho. Esta assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades de URL. Nunca exposta ou publicada, esta chave é mantida encriptada e armazenada com a aplicação lógica. A sua aplicação lógica autoriza apenas os gatilhos que contêm uma assinatura válida criada com a chave secreta. |
|||

Para mais informações sobre a obtenção de acesso com sas, consulte estas secções neste tópico:

* [Chaves de acesso regeneradas](#access-keys)
* [Criar URLs de callback expirando](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Chaves de acesso regeneradas

Para gerar uma nova chave de acesso à segurança a qualquer momento, utilize o portal Azure REST API ou Azure. Todos os URLs anteriormente gerados que usam a chave antiga são invalidados e já não têm autorização para desencadear a aplicação lógica. Os URLs que recupera após a regeneração são assinados com a nova chave de acesso.

1. No [portal Azure,](https://portal.azure.com)abra a app lógica que tem a chave que pretende regenerar.

1. No menu da aplicação lógica, em **Definições,** selecione **Access Keys**.

1. Selecione a chave que pretende regenerar e terminar o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de callback expirando

Se partilhar o URL de ponto final para um gatilho baseado em pedidos com outras partes, pode gerar URLs de callback que usam chaves específicas e têm datas de validade. Dessa forma, pode rodar perfeitamente as teclas ou restringir o acesso ao gatilho da sua aplicação lógica com base numa hora específica. Para especificar uma data de validade para um URL, utilize as [Aplicações Lógicas REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `NotAfter`propriedade usando uma corda de data JSON. Esta propriedade devolve um URL de callback `NotAfter` que é válido apenas até a data e hora.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando gera ou lista URLs de callback para um gatilho baseado em pedidos, pode especificar a chave a utilizar para a assinatura do URL. Para gerar um URL assinado por uma chave específica, use as [Aplicações Lógicas REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua `KeyType` a `Primary` `Secondary`propriedade como ou . Esta propriedade devolve um URL assinado pela chave de segurança especificada.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Ativar o Diretório Ativo Azure OAuth

Se a sua aplicação lógica começar com um gatilho de Pedido, pode ativar a Autenticação Aberta do [Diretório Ativo azure](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth) para autorizar chamadas de entrada para o gatilho do Pedido. Antes de ativar esta autenticação, reveja estas considerações:

* A sua aplicação lógica está limitada a um número máximo de políticas de autorização. Cada política de autorização também tem um número máximo de [reclamações.](../active-directory/develop/developer-glossary.md#claim) Para mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Uma política de autorização deve incluir, pelo menos, a `https://sts.windows.net/` alegação **emitente,** que tem um valor que começa com o ID do emitente da AD Azure.

* Uma chamada de entrada para a sua aplicação lógica pode usar apenas um esquema de autorização, seja azure AD OAuth ou [Shared Access Signatures (SAS)](#sas).

* Os tokens OAuth são suportados apenas para o gatilho do Pedido.

* Apenas os regimes de autorização [do tipo Bearer](../active-directory/develop/active-directory-v2-protocols.md#tokens) são suportados para fichas OAuth.

Para permitir o OAuth Azure AD, siga estes passos para adicionar uma ou mais políticas de autorização à sua aplicação lógica.

1. No [portal Azure,](https://portal.microsoft.com)encontre e abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicações lógicas, em **Definições,** **selecione Autorização**. Depois do painel de autorização abrir, selecione **Adicionar a política**.

   ![Selecione "Authorization" > "Adicionar a política"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Forneça informações sobre a política de autorização especificando os tipos e valores de [reclamação](../active-directory/develop/developer-glossary.md#claim) que a sua aplicação lógica espera no token de autenticação apresentado por cada chamada de entrada para o gatilho do Pedido:

   ![Fornecer informações sobre a política de autorização](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da política** | Sim | O nome que quer usar para a política de autorização |
   | **Afirmações** | Sim | Os tipos e valores de reclamação que a sua aplicação lógica aceita a partir de chamadas de entrada. Aqui estão os tipos de reclamação disponíveis: <p><p>- **Emitente** <br>- **Público** <br>- **Assunto** <br>- **Id JWT** (JSON Web Token ID) <p><p>No mínimo, a lista **de Reclamações** deve incluir a reclamação `https://sts.windows.net/` do **Emitente,** que tem um valor que começa com o ID do emitente da AD Azure. Para obter mais informações sobre estes tipos de reclamações, consulte [reclamações em fichas de segurança da AD Azure](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Também pode especificar o seu próprio tipo de reclamação e valor. |
   |||

1. Para adicionar outra reclamação, selecione a partir destas opções:

   * Para adicionar outro tipo de reclamação, **selecione Adicionar a reclamação padrão,** selecione o tipo de reclamação e especifique o valor da reclamação.

   * Para adicionar a sua própria reclamação, selecione **Adicionar pedido personalizado**, e especificar o valor de reclamação personalizado.

1. Para adicionar outra política de autorização, selecione **Adicionar a política**. Repita os passos anteriores para definir a política.

1. Quando tiver terminado, selecione **Guardar**.

A sua aplicação lógica está agora configurada para utilizar o Azure AD OAuth para autorizar pedidos de entrada. Quando a sua aplicação lógica recebe um pedido de entrada que inclui um símbolo de autenticação, a Azure Logic Apps compara as reclamações do token com as reclamações em cada política de autorização. Se existir uma correspondência entre as alegações do token e todas as reclamações em pelo menos uma apólice, a autorização é bem sucedida para o pedido de entrada. O símbolo pode ter mais reclamações do que o número especificado pela política de autorização.

Por exemplo, suponha que a sua aplicação lógica tem uma política de autorização que requer dois tipos de reclamação, Emitente e Público. Esta amostra de [acesso](../active-directory/develop/access-tokens.md) descodificada inclui ambos os tipos de reclamações:

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

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Restringir os endereços IP de entrada

Juntamente com a Shared Access Signature (SAS), é possível que queira limitar especificamente os clientes que podem ligar para a sua aplicação lógica. Por exemplo, se gerir o seu ponto final de pedido utilizando a Azure API Management, pode restringir a sua aplicação lógica para aceitar pedidos apenas a partir do endereço IP para a instância de Gestão API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Restringir as gamas IP de entrada no portal Azure

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **definições**de Fluxo de Trabalho .

1. Sob **a configuração** > do controlo de acesso**permitida a entrada de endereços IP,** selecione **gamas IP específicas**.

1. Nos **intervalos IP para os gatilhos,** especifique as gamas de endereços IP que o gatilho aceita.

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

Se quiser que a sua aplicação lógica dispare apenas como uma aplicação lógica aninhada, a partir da lista de **endereços IP de entrada permitida,** selecione **Apenas outras Aplicações Lógicas**. Esta opção escreve uma matriz vazia para o seu recurso de aplicação lógica. Desta forma, apenas chamadas do serviço De Aplicações Lógicas (aplicações lógicas dos pais) podem desencadear a aplicação lógica aninhada.

> [!NOTE]
> Independentemente do endereço IP, ainda pode executar uma aplicação lógica `/triggers/<trigger-name>/run` que tem um gatilho baseado em pedidos utilizando através da API Do REST Azure ou através da API Management. No entanto, este cenário ainda requer [autenticação](../active-directory/develop/authentication-scenarios.md) contra a API Do REST Azure. Todos os eventos aparecem no Registo de Auditoria Azure. Certifique-se de que define as políticas de controlo de acesso em conformidade.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Restringir as gamas IP de entrada no modelo do Gestor de Recursos Azure

Se [automatizar a implementação de aplicações lógicas utilizando modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de `accessControl` Gestor de `triggers` Recursos, pode especificar as gamas IP utilizando a secção com a secção na definição de recursos da sua aplicação lógica, por exemplo:

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
            "definition": {<workflow-definition>},
            "parameters": {},
            "accessControl": {
               "triggers": {
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

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Adicionar autenticação aberta do diretório ativo Azure ou outra segurança

Para adicionar mais protocolos de [autenticação](../active-directory/develop/authentication-scenarios.md) à sua aplicação lógica, considere utilizar o serviço de [Gestão API Azure.](../api-management/api-management-key-concepts.md) Este serviço ajuda-o a expor a sua aplicação lógica como API e oferece uma monitorização rica, segurança, política e documentação para qualquer ponto final. A API Management pode expor um ponto final público ou privado para a sua aplicação lógica. Para autorizar o acesso a este ponto final, pode utilizar a Autenticação Aberta do [Diretório Ativo Azure](#azure-active-directory-oauth-authentication) (Azure AD OAuth), o certificado de [cliente,](#client-certificate-authentication)ou outras normas de segurança para autorizar o acesso a esse ponto final. Quando a API Management recebe um pedido, o serviço envia o pedido para a sua aplicação lógica, fazendo também quaisquer transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas a API Management desencadeie a sua aplicação lógica, pode utilizar as definições de gama IP da sua aplicação lógica.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicações lógicas

Só pode permitir que utilizadores ou grupos específicos executem tarefas específicas, tais como gestão, edição e visualização de aplicações lógicas. Para controlar as suas permissões, utilize o [Azure Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) para que possa atribuir funções personalizadas ou incorporadas aos membros da sua subscrição Azure:

* [Logic App Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite-lhe gerir aplicações lógicas, mas não pode alterar o acesso às mesmas.

* [Logic App Operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite-lhe ler, ativar e desativar aplicações lógicas, mas não pode editá-las ou atualizá-las.

Para evitar que outros mudem ou abatam a sua aplicação lógica, pode utilizar o Bloqueio de [Recursos Azure](../azure-resource-manager/management/lock-resources.md). Esta capacidade impede que outros mudem ou abaterem recursos de produção.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acesso a dados de histórico de execução

Durante uma execução de aplicações lógicas, todos os dados são [encriptados durante](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) o trânsito utilizando a Transport Layer Security (TLS) e [em repouso](../security/fundamentals/encryption-atrest.md). Quando a sua aplicação lógica terminar de funcionar, pode ver a história para essa execução, incluindo os passos que correram juntamente com o estado, duração, inputs e saídas para cada ação. Este detalhe rico fornece uma visão de como a sua aplicação lógica funcionava e onde você pode começar a resolver problemas que surjam.

Ao visualizar o histórico de execução da sua aplicação lógica, as Aplicações Lógicas autenticam o seu acesso e, em seguida, fornece links para as inputs e saídas para os pedidos e respostas para cada execução. No entanto, para ações que lidam com quaisquer palavras-passe, segredos, chaves ou outras informações sensíveis, pretende impedir que outros possam visualizar e aceder a esses dados. Por exemplo, se a sua aplicação lógica obtém um segredo do [Azure Key Vault](../key-vault/general/overview.md) para usar ao autenticar uma ação HTTP, você quer esconder esse segredo da vista.

Para controlar o acesso às inputs e saídas no histórico de execução da sua aplicação lógica, tem estas opções:

* [Restringir o acesso por gama de endereços IP](#restrict-ip).

  Esta opção ajuda-o a garantir o acesso ao histórico de execução com base nos pedidos de uma gama específica de endereços IP.

* [Proteja os dados no histórico de execução utilizando a obfuscação](#obfuscate).

  Em muitos gatilhos e ações, você pode garantir as inputs, saídas ou ambos no histórico de execução de uma aplicação lógica.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por gama de endereços IP

Pode limitar o acesso às inputs e saídas no histórico de execução da sua aplicação lógica para que apenas os pedidos de intervalos específicos de endereços IP possam visualizar esses dados. Por exemplo, para bloquear qualquer pessoa de aceder a entradas e `0.0.0.0-0.0.0.0`saídas, especifique uma gama de endereços IP como . Apenas uma pessoa com permissões de administrador pode remover esta restrição, o que proporciona a possibilidade de acesso "just-in-time" aos dados da sua aplicação lógica. Pode especificar as gamas IP para restringir quer através do portal Azure quer num modelo de Gestor de Recursos Azure que utiliza para a implementação de aplicações lógicas.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restringir gamas IP no portal Azure

1. No portal Azure, abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **definições**de Fluxo de Trabalho .

1. Sob **a configuração** > do controlo de acesso**permitida a entrada de endereços IP,** selecione **gamas IP específicas**.

1. Nas **gamas IP para conteúdos,** especifique as gamas de endereços IP que podem aceder ao conteúdo a partir de inputs e saídas. 

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restringir gamas IP no modelo de Gestor de Recursos Azure

Se [automatizar a implementação de aplicações lógicas utilizando modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de `accessControl` Gestor de `contents` Recursos, pode especificar as gamas IP utilizando a secção com a secção na definição de recursos da sua aplicação lógica, por exemplo:

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

<a name="obfuscate"></a>

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Proteja os dados na história da execução usando a obfuscação

Muitos gatilhos e ações têm configurações para garantir inputs, saídas ou ambos a partir do histórico de execução de uma aplicação lógica. Antes de utilizar estas definições para o ajudar a proteger estes dados, [reveja estas considerações](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Inputs e saídas seguras no designer

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

   ![App de lógica aberta no Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou ação onde pretende fixar dados sensíveis, selecione o botão elipses (**...**) e, em seguida, selecione **Definições**.

   ![Abrir definições de gatilho ou de ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ligue as **inputs seguras,** **as saídas seguras,** ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ligue "Entradas seguras" ou "saídas seguras"](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou gatilho mostra agora um ícone de bloqueio na barra de título.

   ![Barra de título de ação ou gatilho mostra ícone de bloqueio](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Tokens que representam saídas seguras de ações anteriores também mostram ícones de bloqueio. Por exemplo, quando seleciona tal saída da lista de conteúdos dinâmicos para utilizar numa ação, esse símbolo mostra um ícone de bloqueio.

   ![Selecione ficha para uma saída segura](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois da aplicação lógica, pode ver a história para essa execução.

   1. No painel de **visão geral** da aplicação lógica, selecione a execução que pretende ver.

   1. No painel de execução de **aplicações Logic,** expanda as ações que pretende rever.

      Se optou por ocultar tanto as inputs como as saídas, esses valores parecem agora escondidos.

      ![Inputs e saídas ocultas na história da execução](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Inputs e saídas seguras na vista de código

Na definição de gatilho ou de `runtimeConfiguration.secureData.properties` ação subjacente, adicione ou atualize a matriz com ambos ou ambos os valores:

* `"inputs"`: Assegura as inputs na história da execução.
* `"outputs"`: Assegura saídas na história da execução.

Aqui ficam [algumas considerações a rever](#obfuscation-considerations) quando utiliza estas definições para o ajudar a proteger estes dados.

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

<a name="obfuscation-considerations"></a>

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Considerações ao assegurar inputs e saídas

* Quando obscurece as inputs ou saídas num gatilho ou ação, as Aplicações Lógicas não enviam os dados seguros para o Azure Log Analytics. Além disso, não é possível adicionar [propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) a esse gatilho ou ação para monitorização.

* A [API de Aplicações Lógicas para lidar com o histórico de fluxos](https://docs.microsoft.com/rest/api/logic/) de trabalho não devolve saídas seguras.

* Para proteger as saídas de uma ação que obscurece as inputs ou obscurece explicitamente as saídas, ligue manualmente as **saídas seguras** nessa ação.

* Certifique-se de que liga **as inputs seguras** ou as **saídas seguras** em ações a jusante, onde espera que o histórico de execução obscureça esses dados.

  **Definição de saídas seguras**

  Quando liga manualmente as **saídas seguras** num gatilho ou ação, as Aplicações Lógicas escondem estas saídas na história da execução. Se uma ação a jusante usa explicitamente estas saídas seguras como inputs, a Logic Apps esconde as inputs desta ação na história da execução, mas *não permite* a definição de **Inputs Seguras** da ação.

  ![Saídas seguras como inputs e impacto a jusante na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações Compor, Parse JSON e Response têm apenas a definição de **Inputs Seguras.** Quando ligado, o cenário também esconde as saídas destas ações. Se estas ações usarem explicitamente as saídas seguras a montante como inputs, a Logic Apps esconde as inputs e saídas destas ações, mas *não permite* a definição de **Inputs Seguras destas** ações. Se uma ação a jusante utilizar explicitamente as saídas ocultas das ações Compor, Parse JSON ou Response como inputs, a Logic Apps *não esconde as inputs ou saídas desta ação a jusante.*

  ![Saídas seguras como inputs com impacto a jusante em ações específicas](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Definição de inputs seguros**

  Quando liga manualmente **as inputs seguras** num gatilho ou ação, as Aplicações Lógicas escondem estas inputs na história da execução. Se uma ação a jusante usar explicitamente as saídas visíveis desse gatilho ou ação como inputs, a Logic Apps esconde esta entrada de ação a jusante na história da execução, mas *não permite* **inputs seguros** nesta ação e não esconde as saídas desta ação.

  ![Inputs seguros e impacto a jusante na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se as ações Compor, Parse JSON e Response usarem explicitamente as saídas visíveis do gatilho ou ação que tem as inputs seguras, a Logic Apps esconde as inputs e saídas destas ações, mas *não permite* a definição de **Inputs Seguras** desta ação. Se uma ação a jusante utilizar explicitamente as saídas ocultas das ações Compor, Parse JSON ou Response como inputs, a Logic Apps *não esconde as inputs ou saídas desta ação a jusante.*

  ![Inputs seguros e impacto a jusante em ações específicas](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso a entradas de parâmetros

Se implementar em diferentes ambientes, considere parametrizar os valores na sua definição de fluxo de trabalho que variam em função desses ambientes. Desta forma, pode evitar dados codificados com código sinuoso utilizando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) para implementar a sua aplicação lógica, proteger dados sensíveis definindo parâmetros seguros e passar esses dados como entradas separadas através [dos parâmetros do modelo](../azure-resource-manager/templates/template-parameters.md) utilizando um ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md).

Por exemplo, se autenticar as ações http com a Autenticação Aberta do [Diretório Ativo Azure](#azure-active-directory-oauth-authentication) (Azure AD OAuth), pode definir e ocultar os parâmetros que aceitam o ID do cliente e o segredo do cliente que são usados para autenticação. Para definir estes parâmetros na sua `parameters` aplicação lógica, use a secção na definição de fluxo de trabalho da sua aplicação lógica e o modelo de Gestor de Recursos para implementação. Para ajudar a garantir valores de parâmetros que não deseja mostrados ao editar a `securestring` sua `secureobject` aplicação lógica ou ver o histórico de execução, defina os parâmetros utilizando o ou tipo e use a codificação conforme necessário. Os parâmetros que têm este tipo não são devolvidos com a definição de recursos e não são acessíveis ao visualizar o recurso após a implantação. Para aceder a estes valores de `@parameters('<parameter-name>')` parâmetros durante o tempo de funcionamento, utilize a expressão dentro da definição de fluxo de trabalho. Esta expressão é avaliada apenas no prazo de funcionamento e é descrita pela [Linguagem definição](../logic-apps/logic-apps-workflow-definition-language.md)de fluxo de trabalho.

> [!NOTE]
> Se utilizar um parâmetro num cabeçalho ou corpo de pedido, esse parâmetro pode ser visível quando visualiza o histórico de execução da sua aplicação lógica e o pedido de HTTP de saída. Certifique-se de que também define as suas políticas de acesso ao conteúdo em conformidade. Também pode usar [a obfuscação](#obfuscate) para ocultar inputs e saídas na sua história de execução. Os cabeçalhos de autorização nunca são visíveis através de inputs ou saídas. Então, se um segredo é usado lá, esse segredo não é recuperável.

Para mais informações, consulte estas secções neste tópico:

* [Parâmetros seguros nas definições de fluxo de trabalho](#secure-parameters-workflow)
* [Proteja os dados na história da execução usando a obfuscação](#obfuscate)

Se [automatizar a implementação de aplicações lógicas utilizando modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Gestor de Recursos, pode `securestring` definir `secureobject` [parâmetros](../azure-resource-manager/templates/template-parameters.md)de modelo seguros , que são avaliados na implementação, utilizando os e tipos. Para definir parâmetros de modelo, utilize `parameters` a secção de nível superior do `parameters` seu modelo, que é separada e diferente da secção da definição de fluxo de trabalho. Para fornecer os valores para parâmetros de modelo, utilize um [ficheiro de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado .

Por exemplo, se utilizar segredos, pode definir e usar parâmetros de modelo seguros que recuperam esses segredos do [Cofre chave Azure](../key-vault/general/overview.md) na implantação. Pode então fazer referência ao cofre da chave e ao segredo no seu ficheiro de parâmetros. Para obter mais informações, veja estes tópicos:

* [Passe valores sensíveis na implantação utilizando o Cofre chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)
* [Parâmetros seguros em modelos de Gestor de Recursos Azure](#secure-parameters-deployment-template) mais tarde neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Parâmetros seguros nas definições de fluxo de trabalho

Para proteger informações sensíveis na definição de fluxo de trabalho da sua aplicação lógica, utilize parâmetros seguros para que esta informação não seja visível depois de guardar a sua aplicação lógica. Por exemplo, suponha que tenha uma ação HTTP requer autenticação básica, que usa um nome de utilizador e senha. Na definição de `parameters` fluxo de `basicAuthPasswordParam` trabalho, a secção define os parâmetros e os `basicAuthUsernameParam` parâmetros utilizando o `securestring` tipo. A definição de ação refere `authentication` então estes parâmetros na secção.

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

Um [modelo de Gestor de](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) `parameters` Recursos para uma aplicação lógica tem várias secções. Para proteger palavras-passe, chaves, segredos e outras informações sensíveis, defina `securestring` parâmetros seguros ao nível do modelo e nível de definição de fluxo de trabalho utilizando o ou `secureobject` o tipo. Em seguida, pode armazenar estes valores no [Cofre de Chaves Azure](../key-vault/general/overview.md) e utilizar o ficheiro de [parâmetro](../azure-resource-manager/templates/parameter-files.md) para fazer referência ao cofre e segredo da chave. O seu modelo recupera então essa informação na implantação. Para mais informações, consulte [os valores sensíveis do Passe na implementação utilizando](../azure-resource-manager/templates/key-vault-parameter.md)o Cofre de Chaves Azure .

Aqui está mais `parameters` informações sobre estas secções:

* No nível superior do `parameters` modelo, uma secção define os parâmetros para os valores que o modelo utiliza na *implementação*. Por exemplo, estes valores podem incluir cordas de ligação para um ambiente de implantação específico. Em seguida, pode armazenar estes valores num [ficheiro de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado, o que facilita a alteração destes valores.

* Dentro da definição de recursos da sua aplicação `parameters` lógica, mas fora da definição de fluxo de trabalho, uma secção especifica os valores para os parâmetros da definição de fluxo de trabalho. Nesta secção, pode atribuir estes valores utilizando expressões de modelo que referenciam os parâmetros do seu modelo. Estas expressões são avaliadas na implantação.

* Dentro da definição `parameters` de fluxo de trabalho, uma secção define os parâmetros que a sua aplicação lógica utiliza no tempo de execução. Em seguida, pode referenciar estes parâmetros dentro do fluxo de trabalho da sua aplicação lógica utilizando expressões de definição de fluxo de trabalho, que são avaliadas no tempo de funcionamento.

Este modelo de exemplo que tem múltiplas `securestring` definições de parâmetros seguros que usam o tipo:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma palavra-passe que `basicAuthPasswordParam` é então passada para o parâmetro da definição de fluxo de trabalho |
| `TemplateUsernameParam` | Um parâmetro de modelo que aceita um nome de utilizador que `basicAuthUserNameParam` é então passado para o parâmetro da definição de fluxo de trabalho |
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
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
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

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Acesso a serviços e sistemas chamados de aplicações lógicas

Aqui estão algumas formas de ajudar a garantir pontos finais que recebem chamadas ou pedidos da sua aplicação lógica:

* Adicione a autenticação aos pedidos de saída.

  Quando trabalha com um gatilho ou ação baseado em HTTP que faz chamadas de saída, como HTTP, HTTP + Swagger ou Webhook, pode adicionar autenticação ao pedido que é enviado pela sua aplicação lógica. Por exemplo, pode selecionar estes tipos de autenticação:

  * [Autenticação básica](#basic-authentication)

  * [Autenticação do certificado de cliente](#client-certificate-authentication)

  * [Autenticação Ativa OAuth OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerida](#managed-identity-authentication)
  
  Para mais informações, consulte [Adicionar autenticação a chamadas de saída](#add-authentication-outbound) mais tarde neste tópico.

* Restringir o acesso a partir de endereços IP da aplicação lógica.

  Todas as chamadas para pontos finais de aplicações lógicas têm origem em endereços IP designados específicos que são baseados nas regiões das suas aplicações lógicas. Pode adicionar filtragem que aceita pedidos apenas a partir desses endereços IP. Para obter estes endereços IP, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md#configuration).

* Melhorar a segurança das ligações aos sistemas no local.

  As Aplicações Lógicas Azure proporcionam integração com estes serviços para ajudar a fornecer uma comunicação mais segura e fiável no local.

  * Gateway de dados no local

    Muitos conectores geridos em Aplicações Lógicas Azure facilitam ligações seguras a sistemas no local, tais como Sistema de Ficheiros, SQL, SharePoint e DB2. O portal envia dados de fontes no local em canais encriptados através do Azure Service Bus. Todo o tráfego origina-se como tráfego de saída seguro do agente de porta. Saiba [como funciona o gateway de dados no local.](logic-apps-gateway-install.md#gateway-cloud-service)

  * Ligue-se através da Gestão API Azure

    A [Azure API Management](../api-management/api-management-key-concepts.md) fornece opções de ligação no local, tais como rede privada virtual local e integração ExpressRoute para procuração segura e comunicação para sistemas no local. A partir do fluxo de trabalho da sua aplicação lógica no Logic App Designer, você pode selecionar uma API que é exposta pela API Management, que fornece acesso rápido a sistemas no local.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a chamadas de saída

OS pontos finais HTTP e HTTPS suportam vários tipos de autenticação. Com base no gatilho ou ação que utiliza para efetuar chamadas ou pedidos de saída que acedam a estes pontos finais, pode selecionar entre gamas variadas de tipos de autenticação. Para se certificar de que protege qualquer informação sensível que a sua aplicação lógica manuseie, utilize parâmetros seguros e codifique os dados conforme necessário. Para obter mais informações sobre a utilização e a fixação de parâmetros, consulte [Acesso às inputs](#secure-action-parameters)dos parâmetros .

> [!NOTE]
> No Logic App Designer, a propriedade **de Autenticação** pode estar escondida em alguns gatilhos e ações onde pode especificar o tipo de autenticação. Para que a propriedade apareça nestes casos, no gatilho ou na ação, abra a **lista adicionar novo parâmetro** e selecione **Autenticação**. Para mais informações, consulte [O acesso authenticado com identidade gerida.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

| Tipo de autenticação | Apoiado por |
|---------------------|--------------|
| [Básico](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Certificado de Cliente](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Diretório Ativo OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Funções Azure, HTTP, HTTP + Swagger, HTTP Webhook |
| [Não processado](#raw-authentication) | Azure API Management, Azure App Services, Funções Azure, HTTP, HTTP + Swagger, HTTP Webhook |
| [Identidade gerida](#managed-identity-authentication) | Azure API Management, Azure App Services, Funções Azure, HTTP, HTTP + Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Se a opção [Basic](../active-directory-b2c/secure-rest-api.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Básico | O tipo de autenticação a utilizar |
| **Nome de utilizador** | `username` | Sim | <*nome do utilizador*>| O nome do utilizador para autenticação do acesso ao ponto final do serviço alvo |
| **Palavra-passe** | `password` | Sim | <*senha*> | A palavra-passe para autenticar o acesso ao ponto final do serviço alvo |
||||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http `type` `Basic` exemplo especifica a autenticação como e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

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

### <a name="client-certificate-authentication"></a>Autenticação do Certificado de Cliente

Se a opção [Certificado cliente](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Certificado de Cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a utilizar para certificados de cliente TLS/SSL <p><p>**Nota**: Enquanto os certificados auto-assinados são suportados, os certificados auto-assinados para TLS/SSL não são suportados. O conector HTTP não suporta certificados intermédios TLS/SSL. |
| **Pfx** | `pfx` | Sim | <*codificado-pfx-file-content*> | O conteúdo codificado base64 de um ficheiro de troca de informações pessoais (PFX) <p><p>Para converter o ficheiro PFX em formato codificado base64, pode utilizar o PowerShell seguindo estes passos: <p>1. Guarde o conteúdo do certificado numa variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converter o conteúdo `ToBase64String()` do certificado utilizando a função e guardar esse conteúdo num ficheiro de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Palavra-passe** | `password`| No | <*password-for-pfx-file*> | A palavra-passe para aceder ao ficheiro PFX |
|||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http `type` `ClientCertificate` exemplo especifica a autenticação como e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

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

Para obter mais informações sobre a garantia de serviços utilizando a autenticação do certificado de cliente, consulte estes tópicos:

* [Melhorar a segurança das APIs utilizando a autenticação de certificado de cliente na Azure API Management](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Melhorar a segurança dos serviços back-end utilizando a autenticação de certificado de cliente na Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Melhorar a segurança do seu serviço RESTfuL utilizando certificados de cliente](../active-directory-b2c/secure-rest-api.md)
* [Credenciais de certificado para autenticação de pedido](../active-directory/develop/active-directory-certificate-credentials.md)
* [Utilize um certificado TLS/SSL no seu código no Serviço de Aplicações Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Autenticação aberta do Diretório Ativo Azure

Nos gatilhos de Pedido, pode utilizar a Autenticação Aberta do [Diretório Ativo Azure](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth), para autenticar chamadas recebidas depois de configurar políticas de [autorização de AD Azure](#enable-oauth) para a sua aplicação lógica. Para todos os outros gatilhos e ações que forneçam o tipo de autenticação **OAuth** do Diretório Ativo para selecionar, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Diretório Ativo OAuth** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a utilizar. As Aplicações Lógicas seguem atualmente o [protocolo OAuth 2.0.](../active-directory/develop/v2-overview.md) |
| **Autoridade** | `authority` | No | <*URL-for-authority-token-emitente*> | O URL para a autoridade que fornece o símbolo de autenticação. Por defeito, `https://login.windows.net`este valor é . |
| **Inquilino** | `tenant` | Sim | <*inquilino-ID*> | A identificação do inquilino do Inquilino Azure |
| **Audiência** | `audience` | Sim | <*recurso-para-autorizar*> | O recurso que pretende utilizar para autorização, por exemplo,`https://management.core.windows.net/` |
| **ID do cliente** | `clientId` | Sim | <*cliente-ID*> | O ID do cliente para a app solicitando autorização |
| **Tipo credencial** | `credentialType` | Sim | Certificado <br>ou <br>Segredo | O tipo credencial que o cliente usa para solicitar autorização. Esta propriedade e valor não aparecem na definição subjacente da sua aplicação lógica, mas determina as propriedades que aparecem para o tipo credencial selecionado. |
| **Segredo** | `secret` | Sim, mas apenas para o tipo de credencial "Segredo". | <*segredo de cliente*> | O segredo do cliente para solicitar autorização |
| **Pfx** | `pfx` | Sim, mas apenas para o tipo de credencial "Certificado" | <*codificado-pfx-file-content*> | O conteúdo codificado base64 de um ficheiro de troca de informações pessoais (PFX) |
| **Palavra-passe** | `password` | Sim, mas apenas para o tipo de credencial "Certificado" | <*password-for-pfx-file*> | A palavra-passe para aceder ao ficheiro PFX |
|||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http `type` `ActiveDirectoryOAuth`exemplo especifica a autenticação como, o tipo credencial como `Secret`, e utiliza a [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

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

### <a name="raw-authentication"></a>Autenticação bruta

Se a opção **Raw** estiver disponível, pode utilizar este tipo de autenticação quando tiver de utilizar esquemas de [autenticação](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) que não sigam o [protocolo OAuth 2.0](https://oauth.net/2/). Com este tipo, cria manualmente o valor do cabeçalho de autorização que envia com o pedido de saída e especifica esse valor de cabeçalho no gatilho ou ação.

Por exemplo, aqui está um cabeçalho de amostra para um pedido HTTPS que segue o [protocolo OAuth 1.0:](https://tools.ietf.org/html/rfc5849)

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
| **Autenticação** | `type` | Sim | Não processado | O tipo de autenticação a utilizar |
| **Valor** | `value` | Sim | <*autorização-cabeçalho-valor*> | O valor do cabeçalho de autorização a utilizar para autenticação |
||||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http `type` `Raw`exemplo especifica a autenticação como , e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

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

### <a name="managed-identity-authentication"></a>Autenticação de identidade gerida

Se a opção [Identidade Gerida](../active-directory/managed-identities-azure-resources/overview.md) estiver disponível, a sua aplicação lógica pode utilizar a identidade atribuída pelo sistema ou uma *única* identidade atribuída manualmente ao utilizador para autenticar o acesso a recursos em outros inquilinos do Azure Ative Directory (Azure AD) sem se inscrever. O Azure gere esta identidade para si e ajuda-o a garantir as suas credenciais porque não tem de fornecer ou rodar segredos. Saiba mais sobre [os serviços Azure que suportam identidades geridas para a autenticação da AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Antes de a sua aplicação lógica poder usar uma identidade gerida, siga os passos no [acesso authenticado aos recursos do Azure utilizando identidades geridas em Aplicações Lógicas Azure](../logic-apps/create-managed-service-identity.md). Estes passos permitem a identidade gerida na sua aplicação lógica e configuram o acesso dessa identidade ao recurso Azure alvo.

1. Antes de uma função Azure poder utilizar uma identidade gerida, primeiro [permitir a autenticação para funções Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. No gatilho ou ação em que pretende utilizar a identidade gerida, especifique estes valores de propriedade:

   | Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Sim | **Identidade Gerida** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a utilizar |
   | **Identidade Gerida** | `identity` | Sim | * **Identidade Gerida Atribuída ao Sistema** <br>ou <br>`SystemAssigned` <p><p>* <*nome de identidade atribuído* ao utilizador> | A identidade gerida para usar |
   | **Audiência** | `audience` | Sim | <*target-resource-ID*> | O ID de recurso para o recurso-alvo a que pretende aceder. <p>Por exemplo, `https://storage.azure.com/` torna os [tokens](../active-directory/develop/access-tokens.md) de acesso para autenticação válidos para todas as contas de armazenamento. No entanto, também pode especificar um `https://fabrikamstorageaccount.blob.core.windows.net` URL de serviço de raiz, como para uma conta de armazenamento específica. <p>**Nota:** A propriedade **do Público** pode estar escondida em alguns gatilhos ou ações. Para tornar esta propriedade visível, no gatilho ou na ação, abra a **lista de novos parâmetros e** selecione **Audience**. <p><p>**Importante:** Certifique-se de que este ID de recurso alvo *corresponde exatamente* ao valor que a Azure AD espera, incluindo quaisquer cortes de rasto necessários. Então, `https://storage.azure.com/` o ID de recursos para todas as contas de armazenamento de Blob Azure requer um corte de rasto. No entanto, o ID de recursos para uma conta de armazenamento específica não requer um corte de rasto. Para encontrar estes IDs de recursos, consulte [os serviços Azure que suportam a Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   |||||

   Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http `type` `ManagedServiceIdentity` exemplo especifica a autenticação como e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

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

## <a name="next-steps"></a>Passos seguintes

* [Implementação automatizada para aplicações lógicas azure](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorizar aplicações lógicas](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnosticar falhas e problemas de aplicações lógicas](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizar a implementação de uma aplicação lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
