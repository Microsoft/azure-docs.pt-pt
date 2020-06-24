---
title: Proteger o acesso e os dados
description: Acesso seguro a entradas, saídas, gatilhos baseados em pedidos, histórico de execução, tarefas de gestão e acesso a outros recursos em Apps Azure Logic
services: logic-apps
ms.suite: integration
ms.reviewer: rarayudu, logicappspm
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: dec14f54c0c0994594e86793c998d02ca6781801
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296904"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Acesso seguro e dados em Azure Logic Apps

Para controlar o acesso e proteger dados sensíveis em Azure Logic Apps, pode configurar a segurança para estas áreas:

* [Acesso a gatilhos baseados em pedidos](#secure-triggers)
* [Acesso a operações de aplicações lógicas](#secure-operations)
* [Acesso a executar entradas e saídas de histórico](#secure-run-history)
* [Acesso às entradas de parâmetros](#secure-action-parameters)
* [Acesso a serviços e sistemas chamados de aplicações lógicas](#secure-outbound-requests)
* [Bloquear a criação de ligações para conectores específicos](#block-connections)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Acesso a gatilhos baseados em pedidos

Se a sua aplicação lógica utilizar um gatilho baseado em pedidos, que recebe chamadas ou pedidos de entrada, como o detonador [Pedido](../connectors/connectors-native-reqres.md) ou [Webhook,](../connectors/connectors-native-webhook.md) pode limitar o acesso para que apenas os clientes autorizados possam ligar para a sua aplicação lógica. Todos os pedidos recebidos por uma aplicação lógica são encriptados e protegidos com o protocolo De Segurança da Camada de Transporte (TLS), anteriormente conhecido como Camada de Tomadas Seguras (SSL).

Aqui estão as opções que podem ajudá-lo a garantir o acesso a este tipo de gatilho:

* [Gerar assinaturas de acesso partilhado](#sas)
* [Ativar a autenticação aberta do Diretório Ativo Azure (Azure AD OAuth)](#enable-oauth)
* [Restringir endereços IP de entrada](#restrict-inbound-ip-addresses)
* [Adicionar autenticação aberta ao diretório ativo Azure (Azure AD OAuth) ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar assinaturas de acesso partilhado (SAS)

Cada ponto final de pedido de uma aplicação lógica tem uma [Assinatura de Acesso Partilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) no URL do ponto final, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL contém o `sp` `sv` parâmetro , e `sig` consulta, conforme descrito nesta tabela:

| Parâmetro de consulta | Description |
|-----------------|-------------|
| `sp` | Especifica permissões para os métodos HTTP autorizados a utilizar. |
| `sv` | Especifica a versão SAS para utilizar para gerar a assinatura. |
| `sig` | Especifica a assinatura a utilizar para autenticar o acesso ao gatilho. Esta assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades url. Nunca exposta ou publicada, esta chave é mantida encriptada e armazenada com a aplicação lógica. A sua aplicação lógica autoriza apenas os gatilhos que contêm uma assinatura válida criada com a chave secreta. |
|||

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

Se partilhar o URL do ponto final para um gatilho baseado em pedidos com outras partes, pode gerar URLs de retorno que usam chaves específicas e têm datas de validade. Desta forma, pode enrolar perfeitamente as teclas ou restringir o acesso a desencadear a sua aplicação lógica com base num período de tempo específico. Para especificar uma data de validade para um URL, utilize a [API de Apps Lógicas REST,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `NotAfter` propriedade usando uma cadeia de data JSON. Esta propriedade devolve um URL de retorno que é válido apenas até a `NotAfter` data e hora.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Quando gera ou lista URLs de retorno para um gatilho baseado em pedidos, pode especificar a chave a utilizar para a assinatura do URL. Para gerar um URL assinado por uma chave específica, utilize a [API logic Apps REST](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

```http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `KeyType` propriedade como qualquer um ou `Primary` `Secondary` . Esta propriedade devolve um URL assinado pela chave de segurança especificada.

<a name="enable-oauth"></a>

### <a name="enable-azure-active-directory-oauth"></a>Ativar o Azure Ative Directory OAuth

Se a sua aplicação lógica começar com um [gatilho 'Pedido',](../connectors/connectors-native-reqres.md)pode ativar [a Autenticação Aberta do Diretório Azure](../active-directory/develop/about-microsoft-identity-platform.md) Ative (Azure AD OAuth) criando uma política de autorização para chamadas de entrada no gatilho Do Pedido. Antes de ativar esta autenticação, reveja estas considerações:

* Uma chamada de entrada para a sua aplicação lógica pode usar apenas um esquema de autorização, seja a Azure AD OAuth ou [As Assinaturas de Acesso Partilhado (SAS)](#sas). Apenas os regimes de autorização [do tipo Portador](../active-directory/develop/active-directory-v2-protocols.md#tokens) são suportados para tokens OAuth, que são suportados apenas para o gatilho do Pedido.

* A sua aplicação lógica está limitada a um número máximo de políticas de autorização. Cada política de autorização também tem um número máximo de [reclamações.](../active-directory/develop/developer-glossary.md#claim) Para obter mais informações, consulte [Limites e configuração para Aplicações Lógicas Azure](../logic-apps/logic-apps-limits-and-config.md#authentication-limits).

* Uma política de autorização deve incluir pelo menos a **reclamação do Emitente,** que tem um valor que começa com `https://sts.windows.net/` ou `https://login.microsoftonline.com/` (OAuth V2) como o ID do emitente Azure AD. Para obter mais informações sobre os tokens de acesso, consulte [os tokens de acesso à plataforma de identidade da Microsoft.](../active-directory/develop/access-tokens.md)

Para ativar o Azure AD OAuth, siga estes passos para adicionar uma ou mais políticas de autorização à sua aplicação lógica.

1. No [portal Azure,](https://portal.microsoft.com)encontre e abra a sua aplicação lógica no Logic App Designer.

1. No menu de aplicativos lógico, em **Definições,** selecione **Autorização**. Depois de abrir o painel de autorização, **selecione Adicionar a política**.

   ![Selecione "Autorização" > "Adicionar política"](./media/logic-apps-securing-a-logic-app/add-azure-active-directory-authorization-policies.png)

1. Forneça informações sobre a política de autorização especificando os tipos e valores de [reclamação](../active-directory/develop/developer-glossary.md#claim) que a sua aplicação lógica espera no token de autenticação apresentado por cada chamada de entrada para o gatilho do Pedido:

   ![Fornecer informações para a política de autorização](./media/logic-apps-securing-a-logic-app/set-up-authorization-policy.png)

   | Propriedade | Necessário | Descrição |
   |----------|----------|-------------|
   | **Nome da política** | Yes | O nome que quer usar para a política de autorização |
   | **Pedidos** | Yes | Os tipos e valores de reclamação que a sua aplicação lógica aceita a partir de chamadas de entrada. Aqui estão os tipos de reclamação disponíveis: <p><p>- **Emitente** <br>- **Público** <br>- **Assunto** <br>- **JWT ID** (JSON Web ToKen ID) <p><p>No mínimo, a lista **de Reclamações** deve incluir a **reclamação do Emitente,** que tem um valor que começa com `https://sts.windows.net/` o ou como `https://login.microsoftonline.com/` iD do emitente Azure AD. Para obter mais informações sobre estes tipos de reclamações, consulte [as fichas de segurança Azure AD](../active-directory/azuread-dev/v1-authentication-scenarios.md#claims-in-azure-ad-security-tokens). Também pode especificar o seu próprio tipo de reclamação e valor. |
   |||

1. Para adicionar outra reclamação, selecione a partir destas opções:

   * Para adicionar outro tipo de reclamação, **selecione Adicionar a reclamação padrão,** selecione o tipo de reclamação e especifique o valor de reclamação.

   * Para adicionar a sua própria reclamação, **selecione Adicionar reclamação personalizada**e especificar o valor de reclamação personalizado.

1. Para adicionar outra política de autorização, **selecione Adicionar a política**. Repita os passos anteriores para definir a política.

1. Quando tiver terminado, selecione **Guardar**.

A sua aplicação lógica está agora configurada para usar o Azure AD OAuth para autorizar pedidos de entrada. Quando a sua aplicação lógica recebe um pedido de entrada que inclui um token de autenticação, a Azure Logic Apps compara as reclamações do token com as reclamações em cada política de autorização. Se existir uma correspondência entre as reclamações do token e todas as reclamações em pelo menos uma apólice, a autorização é bem sucedida para o pedido de entrada. O token pode ter mais reclamações do que o número especificado pela política de autorização.

Por exemplo, suponha que a sua aplicação lógica tem uma política de autorização que requer dois tipos de reclamação, Emitente e Público. Esta amostra descodificada [token](../active-directory/develop/access-tokens.md) de acesso inclui ambos os tipos de reclamação:

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

### <a name="restrict-inbound-ip-addresses"></a>Restringir endereços IP de entrada

Juntamente com a Assinatura de Acesso Partilhado (SAS), é possível que queira limitar especificamente os clientes que podem ligar para a sua aplicação lógica. Por exemplo, se gerir o seu ponto final de pedido utilizando a Azure API Management, pode restringir a sua aplicação lógica para aceitar pedidos apenas a partir do endereço IP para a instância de Gestão da API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Restringir as gamas IP de entrada no portal Azure

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **as definições do Fluxo de Trabalho**.

1. Na **configuração do controlo de acesso**Permitido  >  **endereços IP de entrada**, selecione **gamas IP específicas**.

1. Nos **intervalos IP para gatilhos**, especifique os intervalos de endereço IP que o gatilho aceita.

   Uma gama IP válida utiliza estes formatos: *x.x.x.x/x* ou *x.x.x-x.x.x.x.x.x.x.x*

Se pretender que a sua aplicação lógica desencadeie apenas como uma aplicação lógica aninhada, a partir da lista **de endereços IP de entrada permitida,** selecione **Apenas outras Aplicações Lógicas**. Esta opção escreve um conjunto vazio para o seu recurso de aplicação lógica. Desta forma, apenas as chamadas do serviço De Aplicações Lógicas (aplicações lógicas dos pais) podem desencadear a aplicação lógica aninhada.

> [!NOTE]
> Independentemente do endereço IP, ainda pode executar uma aplicação lógica que tenha um gatilho baseado em pedidos utilizando `/triggers/<trigger-name>/run` através da AZure REST API ou através da API Management. No entanto, este cenário ainda requer [autenticação](../active-directory/develop/authentication-scenarios.md) contra a API Azure REST. Todos os eventos aparecem no Registo de Auditoria do Azure. Certifique-se de que define as políticas de controlo de acesso em conformidade.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Restringir as gamas IP de entrada no modelo do Gestor de Recursos Azure

If you [automate deployment for logic apps by using Resource Manager templates](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), you can specify the IP ranges in *x.x.x.x/x* or *x.x.x.x-x.x.x.x* format by using the `accessControl` section and by including the `triggers` and `actions` sections in your logic app's resource definition, for example:

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
                  "allowedCallerIpAddresses:" : []
               }
            },
            "endpointsConfiguration": {}
         }
      }
   ],
   "outputs": {}
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-open-authentication-or-other-security"></a>Adicionar autenticação aberta ao Diretório Ativo Azure ou outra segurança

Para adicionar mais protocolos [de autenticação](../active-directory/develop/authentication-scenarios.md) à sua aplicação lógica, considere utilizar o serviço [de Gestão API da Azure.](../api-management/api-management-key-concepts.md) Este serviço ajuda-o a expor a sua aplicação lógica como uma API e oferece uma monitorização, segurança, política e documentação rica para qualquer ponto final. A API Management pode expor um ponto final público ou privado para a sua aplicação lógica. Para autorizar o acesso a este ponto final, pode utilizar [a Azure Ative Directory Open Authentication](#azure-active-directory-oauth-authentication) (Azure AD OAuth), [o certificado do cliente](#client-certificate-authentication)ou outras normas de segurança para autorizar o acesso a esse ponto final. Quando a API Management recebe um pedido, o serviço envia o pedido para a sua aplicação lógica, fazendo também as transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas a API Management desencadeie a sua aplicação lógica, pode utilizar as definições de gama IP de entrada da sua aplicação lógica.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicações lógicas

Só é possível permitir que utilizadores ou grupos específicos executem tarefas específicas, tais como gerir, editar e visualizar aplicações lógicas. Para controlar as suas permissões, utilize [o Controlo de Acesso Baseado em Funções (RBAC) para](../role-based-access-control/role-assignments-portal.md) que possa atribuir funções personalizadas ou integradas aos membros da sua subscrição Azure:

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

Pode limitar o acesso às entradas e saídas no histórico de execução da sua aplicação lógica para que apenas os pedidos de intervalos específicos de endereços IP possam visualizar esses dados. Por exemplo, para bloquear qualquer pessoa de aceder a entradas e saídas, especifique um intervalo de endereço IP, como `0.0.0.0-0.0.0.0` . Apenas uma pessoa com permissões de administrador pode remover esta restrição, o que proporciona a possibilidade de acesso "just-in-time" aos dados da sua aplicação lógica. Pode especificar as gamas IP para restringir, utilizando o portal Azure ou num modelo de Gestor de Recursos Azure que utiliza para a implementação de aplicações lógicas.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restringir as gamas IP no portal Azure

1. No portal Azure, abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **as definições do Fluxo de Trabalho**.

1. Na **configuração do controlo de acesso**Permitido  >  **endereços IP de entrada**, selecione **gamas IP específicas**.

1. Nos **intervalos IP para conteúdos, especifique**as gamas de endereços IP que podem aceder ao conteúdo a partir de entradas e saídas. 

   Uma gama IP válida utiliza estes formatos: *x.x.x.x/x* ou *x.x.x-x.x.x.x.x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restringir as gamas IP no modelo do Gestor de Recursos Azure

Se [automatizar a implementação de aplicações lógicas utilizando modelos de Gestor de Recursos,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode especificar as gamas IP utilizando a `accessControl` secção com a secção na `contents` definição de recursos da sua aplicação lógica, por exemplo:

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

### <a name="secure-data-in-run-history-by-using-obfuscation"></a>Dados seguros na história da execução utilizando a obfuscação

Muitos gatilhos e ações têm configurações para garantir entradas, saídas ou ambos do histórico de execução de uma aplicação lógica. Antes de utilizar estas definições para ajudá-lo a proteger estes dados, [reveja estas considerações](#obfuscation-considerations).

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Entradas e saídas seguras no designer

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

   ![Aplicativo de lógica aberta no Logic App Designer](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou ação onde pretende proteger dados sensíveis, selecione o botão elipses **(...**) e, em seguida, selecione **Definições**.

   ![Abra as definições de gatilho ou ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ligue as **entradas seguras,** **as saídas seguras**ou ambas. Quando tiver terminado, selecione **Concluído**.

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

#### <a name="considerations-when-securing-inputs-and-outputs"></a>Considerações ao assegurar entradas e saídas

* Quando obscurece as entradas ou saídas num gatilho ou ação, as Aplicações Lógicas não enviam os dados protegidos para o Azure Log Analytics. Além disso, não é possível adicionar [propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) ao gatilho ou ação para monitorização.

* A [API de Aplicações Lógicas para lidar com o histórico de fluxos de trabalho](https://docs.microsoft.com/rest/api/logic/) não devolve saídas seguras.

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

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso às entradas de parâmetros

Se implementar em diferentes ambientes, considere parametrizar os valores na definição de fluxo de trabalho que variam em função desses ambientes. Desta forma, pode evitar dados codificados através de um [modelo de Gestor de Recursos Azure](../azure-resource-manager/templates/overview.md) para implementar a sua aplicação lógica, proteger dados sensíveis definindo parâmetros seguros e passar esses dados como entradas separadas através dos parâmetros do modelo utilizando um ficheiro de [parâmetros.](../azure-resource-manager/templates/template-parameters.md) [parameter file](../azure-resource-manager/templates/parameter-files.md)

Por exemplo, se autenticar ações HTTP com [Azure Ative Directory Open Authentication](#azure-active-directory-oauth-authentication) (Azure AD OAuth), pode definir e ocultar os parâmetros que aceitam o ID do cliente e o segredo do cliente que são utilizados para a autenticação. Para definir estes parâmetros na sua aplicação lógica, utilize a `parameters` secção na definição de fluxo de trabalho da sua aplicação lógica e no modelo de Gestor de Recursos para implementação. Para ajudar a garantir valores de parâmetros que não deseja mostrados ao editar a sua aplicação lógica ou visualizar o histórico de execução, defina os parâmetros utilizando o `securestring` ou tipo e use a `secureobject` codificação conforme necessário. Os parâmetros que têm este tipo não são devolvidos com a definição de recurso e não estão acessíveis ao visualizar o recurso após a implementação. Para aceder a estes valores de parâmetro durante o tempo de funcionamento, utilize a expressão dentro da `@parameters('<parameter-name>')` definição de fluxo de trabalho. Esta expressão é avaliada apenas em tempo de execução e é descrita pela Linguagem de Definição do [Fluxo de Trabalho.](../logic-apps/logic-apps-workflow-definition-language.md)

> [!NOTE]
> Se utilizar um parâmetro num cabeçalho ou corpo de pedido, esse parâmetro poderá ser visível quando visualizar o histórico de execução da sua aplicação lógica e o pedido HTTP de saída. Certifique-se de que também define as suas políticas de acesso ao conteúdo em conformidade. Também pode usar [a obfuscação](#obfuscate) para ocultar entradas e saídas no seu histórico de execução. Os cabeçalhos de autorização nunca são visíveis através de entradas ou saídas. Então, se um segredo é usado lá, esse segredo não é recuperável.

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

| Nome do parâmetro | Description |
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

Aqui estão algumas formas de ajudar a proteger pontos finais que recebem chamadas ou pedidos da sua aplicação lógica:

* Adicione autenticação a pedidos de saída.

  Quando trabalha com um gatilho ou ação baseado em HTTP que faz chamadas de saída, tais como HTTP, HTTP + Swagger ou Webhook, pode adicionar autenticação ao pedido enviado pela sua aplicação lógica. Por exemplo, pode selecionar estes tipos de autenticação:

  * [Autenticação básica](#basic-authentication)

  * [Autenticação de certificado de cliente](#client-certificate-authentication)

  * [Autenticação ativa do Diretório OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerida](#managed-identity-authentication)
  
  Para mais informações, consulte [Adicionar autenticação às chamadas de saída](#add-authentication-outbound) mais tarde neste tópico.

* Restringir o acesso a partir de endereços IP de aplicações lógicas.

  Todas as chamadas para pontos finais de aplicações lógicas têm origem em endereços IP designados específicos que são baseados nas regiões das suas aplicações lógicas. Pode adicionar filtragem que aceita pedidos apenas a partir desses endereços IP. Para obter estes endereços IP, consulte [Limites e configuração para Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

* Melhorar a segurança das ligações aos sistemas no local.

  A Azure Logic Apps proporciona integração com estes serviços para ajudar a fornecer uma comunicação mais segura e fiável no local.

  * Gateway de dados no local

    Muitos conectores geridos em Azure Logic Apps facilitam ligações seguras a sistemas no local, tais como Sistema de Ficheiros, SQL, SharePoint e DB2. O gateway envia dados de fontes no local em canais encriptados através do Azure Service Bus. Todo o tráfego tem origem como tráfego de saída seguro do agente de gateway. Saiba [como funciona o portal de dados no local.](logic-apps-gateway-install.md#gateway-cloud-service)

  * Conecte-se através da Azure API Management

    [A Azure API Management](../api-management/api-management-key-concepts.md) fornece opções de conexão no local, tais como rede privada virtual local-a-local e integração ExpressRoute para sistemas de procuração e comunicação seguros para sistemas no local. A partir do fluxo de trabalho da sua aplicação lógica no Logic App Designer, pode selecionar uma API que esteja exposta pela API Management, que proporciona acesso rápido aos sistemas no local.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a chamadas de saída

Os pontos finais HTTP e HTTPS suportam vários tipos de autenticação. Em alguns gatilhos e ações que utiliza para o envio de chamadas ou pedidos de saída para estes pontos finais, pode especificar um tipo de autenticação. No Logic App Designer, os gatilhos e ações que suportam a escolha de um tipo de autenticação têm uma propriedade **autenticação.** No entanto, esta propriedade pode nem sempre aparecer por defeito. Nestes casos, no gatilho ou ação, abra a nova lista **de parâmetros** e selecione **Autenticação**.

> [!IMPORTANT]
> Para proteger informações sensíveis que a sua aplicação lógica trata, utilize parâmetros seguros e codifique os dados conforme necessário. Para obter mais informações sobre a utilização e fixação de parâmetros, consulte [acesso às entradas de parâmetros](#secure-action-parameters).

Esta tabela identifica os tipos de autenticação que estão disponíveis nos gatilhos e ações onde pode selecionar um tipo de autenticação:

| Tipo de autenticação | Disponibilidade |
|---------------------|--------------|
| [Básica](#basic-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Certificado de Cliente](#client-certificate-authentication) | Azure API Management, Azure App Services, HTTP, HTTP + Swagger, HTTP Webhook |
| [Diretório Ativo OAuth](#azure-active-directory-oauth-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Não processado](#raw-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
| [Identidade gerida](#managed-identity-authentication) | Azure API Management, Azure App Services, Azure Functions, HTTP, HTTP + Swagger, HTTP Webhook |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Se a opção [Basic](../active-directory-b2c/secure-rest-api.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | Básica | O tipo de autenticação a utilizar |
| **Nome de utilizador** | `username` | Yes | <*nome do utilizador*>| O nome de utilizador para autenticar o acesso ao ponto final do serviço alvo |
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

### <a name="client-certificate-authentication"></a>Autenticação do Certificado de Cliente

Se estiver disponível a opção ['Cliente's](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) Certificate' especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | **Certificado de Cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a utilizar. Pode gerir certificados com [a Azure API Management.](../api-management/api-management-howto-mutual-certificates.md) <p></p>**Nota:** Os conectores personalizados não suportam a autenticação baseada em certificados tanto para chamadas de entrada como para saída. |
| **Pfx** | `pfx` | Yes | <*codificado-pfx-file-conteúdo*> | O conteúdo codificado de base64 a partir de um ficheiro de Troca de Informações Pessoais (PFX) <p><p>Para converter o ficheiro PFX em formato codificado base64, pode utilizar o PowerShell seguindo estes passos: <p>1. Guarde o conteúdo do certificado numa variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converter o conteúdo do certificado utilizando a `ToBase64String()` função e guardar esse conteúdo num ficheiro de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
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
* [Utilize um certificado TLS/SSL no seu código no Serviço de Aplicações Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-open-authentication"></a>Autenticação aberta do Diretório Ativo Azure

Nos detonadores de pedidos, pode utilizar [a Azure Ative Directory Open Authentication](../active-directory/develop/about-microsoft-identity-platform.md) (Azure AD OAuth), para autenticar chamadas recebidas depois de [configurar as políticas de autorização AZure AD](#enable-oauth) para a sua aplicação lógica. Para todos os outros gatilhos e ações que forneçam o tipo de autenticação **OAuth do Diretório Ativo** para que possa selecionar, especifique estes valores de propriedade:

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Description |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Yes | **Diretório Ativo OAuth** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a utilizar. As Aplicações Lógicas seguem atualmente o [protocolo OAuth 2.0](../active-directory/develop/v2-overview.md). |
| **Autoridade** | `authority` | No | <*URL-para-autoridade-emitente-simbólico*> | A URL para a autoridade que fornece o sinal de autenticação. Por predefinição, este valor é `https://login.windows.net` . |
| **Inquilino** | `tenant` | Yes | <*inquilino-ID*> | A iD do inquilino para o inquilino da Ad Azure |
| **Audiência** | `audience` | Yes | <*recursos para autorizar*> | O recurso que pretende utilizar para autorização, por exemplo,`https://management.core.windows.net/` |
| **ID do cliente** | `clientId` | Yes | <*iD cliente*> | O ID do cliente para a app solicitando autorização |
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

### <a name="raw-authentication"></a>Autenticação bruta

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

| Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Description |
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

### <a name="managed-identity-authentication"></a>Autenticação de identidade gerida

Se a opção [Identidade Gerida](../active-directory/managed-identities-azure-resources/overview.md) estiver disponível, a sua aplicação lógica pode utilizar a identidade atribuída ao sistema ou uma *única* identidade atribuída manualmente ao utilizador para autenticar o acesso a outros recursos protegidos pelo Azure Ative Directory (Azure AD) sem iniciar sessão. O Azure gere esta identidade para si e ajuda-o a proteger as suas credenciais porque não tem de fornecer ou rodar segredos. Saiba mais sobre [os serviços Azure que suportam identidades geridas para a autenticação AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Antes da sua aplicação lógica poder utilizar uma identidade gerida, siga os passos no [acesso autenticado aos recursos do Azure utilizando identidades geridas em Azure Logic Apps](../logic-apps/create-managed-service-identity.md). Estes passos permitem a identidade gerida na sua aplicação lógica e configuram o acesso dessa identidade ao recurso Azure alvo.

1. Antes de uma função Azure poder utilizar uma identidade gerida, [primeiro permitir a autenticação para funções Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. No gatilho ou ação em que pretende utilizar a identidade gerida, especifique estes valores de propriedade:

   | Propriedade (designer) | Propriedade (JSON) | Necessário | Valor | Description |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Yes | **Identidade Gerida** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a utilizar |
   | **Identidade Gerida** | `identity` | Yes | * **Identidade gerida atribuída ao sistema** <br>ou <br>`SystemAssigned` <p><p>* <*nome de identidade atribuído pelo utilizador*> | A identidade gerida para usar |
   | **Audiência** | `audience` | Yes | <*destino-recursos-ID*> | O ID de recursos para o recurso-alvo a que pretende aceder. <p>Por exemplo, `https://storage.azure.com/` torna os [tokens de acesso para](../active-directory/develop/access-tokens.md) autenticação válidos para todas as contas de armazenamento. No entanto, também pode especificar um URL de serviço de raiz, como por exemplo `https://fabrikamstorageaccount.blob.core.windows.net` para uma conta de armazenamento específica. <p>**Nota:** A propriedade **do Público** pode estar escondida em alguns gatilhos ou ações. Para tornar esta propriedade visível, no gatilho ou ação, abra a nova lista **de parâmetros** e selecione **Audience**. <p><p>**Importante**: Certifique-se de que este ID de recurso-alvo *corresponde exatamente* ao valor que a Azure AD espera, incluindo quaisquer cortes de rasto necessários. Assim, a identificação de `https://storage.azure.com/` recursos para todas as contas de Armazenamento Azure Blob requer um corte de fuga. No entanto, a identificação de recursos para uma conta de armazenamento específica não requer um corte de fuga. Para encontrar estes IDs de recursos, consulte [os serviços Azure que suportam a Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   |||||

   Quando utilizar [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num [modelo do Azure Resource Manager para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros em tempo de execução. Este exemplo de definição de ação HTTP especifica a autenticação `type` como e utiliza a `ManagedServiceIdentity` [função parâmetros()](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

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

<a name="block-connections"></a>

## <a name="block-creating-connections"></a>Bloquear a criação de ligações

Se a sua organização não permitir a ligação a recursos específicos utilizando os seus conectores em Azure Logic Apps, pode [bloquear a capacidade de criar essas ligações](../logic-apps/block-connections-connectors.md) para conectores específicos em fluxos de trabalho de aplicações lógicas utilizando a [Azure Policy.](../governance/policy/overview.md) Para obter mais informações, consulte [as ligações do Bloco criadas por conectores específicos em Azure Logic Apps](../logic-apps/block-connections-connectors.md).

## <a name="next-steps"></a>Passos seguintes

* [Automatizar a implementação para apps Azure Logic](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)  
* [Monitorizar aplicações lógicas](../logic-apps/monitor-logic-apps-log-analytics.md)  
* [Diagnosticar falhas e problemas de aplicações lógicas](../logic-apps/logic-apps-diagnosing-failures.md)  
* [Automatizar a implementação de uma aplicação lógica](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
