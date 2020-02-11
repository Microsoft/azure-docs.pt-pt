---
title: Proteger o acesso e os dados
description: Acesso seguro a inputs, saídas, gatilhos baseados em pedidos, histórico de execução, tarefas de gestão e acesso a outros recursos em Aplicações Lógicas Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: 47b9c0f89cb3db1610b8e3d98f408283c6ff9980
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77116928"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteger o acesso e os dados no aplicativo lógico do Azure

Para controlar o acesso e proteger os dados nos aplicativos lógicos do Azure, você pode configurar a segurança nessas áreas:

* [Acesso a gatilhos baseados em pedidos](#secure-triggers)
* [Acesso a operações de aplicações lógicas](#secure-operations)
* [Acesso a inputs e saídas de histórico](#secure-run-history)
* [Acesso a entradas de parâmetros](#secure-action-parameters)
* [Acesso a serviços e sistemas chamados de aplicações lógicas](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-request-based-triggers"></a>Acesso a gatilhos baseados em solicitação

Se a sua aplicação lógica utilizar um gatilho baseado em pedidos, que recebe chamadas ou pedidos recebidos, como o [gatilho De pedido](../connectors/connectors-native-reqres.md) ou [webhook,](../connectors/connectors-native-webhook.md) pode limitar o acesso para que apenas os clientes autorizados possam ligar para a sua aplicação lógica. Todas as solicitações recebidas por um aplicativo lógico são criptografadas e protegidas com o protocolo protocolo SSL (SSL).

Aqui estão as maneiras como você pode proteger o acesso a esse tipo de gatilho:

* [Gerar assinaturas de acesso partilhado](#sas)
* [Restringir os endereços IP de entrada](#restrict-inbound-ip-addresses)
* [Adicione o Diretório Ativo Azure OAuth ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar SAS (assinaturas de acesso compartilhado)

Cada ponto final de pedido numa aplicação lógica tem uma [Assinatura de Acesso Partilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) no URL do ponto final, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL contém o parâmetro de `sp`, `sv`e `sig` consulta, conforme descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica as permissões para os métodos HTTP permitidos a serem usados. |
| `sv` | Especifica a versão SAS a ser usada para gerar a assinatura. |
| `sig` | Especifica a assinatura a ser usada para autenticar o acesso ao gatilho. Essa assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades de URL. Nunca exposto ou publicado, essa chave é mantida criptografada e armazenada com o aplicativo lógico. Seu aplicativo lógico autoriza somente os gatilhos que contêm uma assinatura válida criada com a chave secreta. |
|||

Para obter mais informações sobre como proteger o acesso com SAS, consulte estas seções neste tópico:

* [Chaves de acesso regeneradas](#access-keys)
* [Criar URLs de callback expirando](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para gerar uma nova chave de acesso seguro a qualquer momento, use a API REST do Azure ou portal do Azure. Todas as URLs geradas anteriormente que usam a chave antiga são invalidadas e não têm mais autorização para disparar o aplicativo lógico. As URLs que você recupera após a regeneração são assinadas com a nova chave de acesso.

1. No [portal Azure,](https://portal.azure.com)abra a app lógica que tem a chave que pretende regenerar.

1. No menu da aplicação lógica, em **Definições,** selecione **Access Keys**.

1. Selecione a chave que você deseja regenerar e conclua o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de retorno de chamada expirando

Se você compartilhar a URL do ponto de extremidade para um gatilho baseado em solicitação com outras partes, poderá gerar URLs de retorno de chamada que usam chaves específicas e têm datas de expiração. Dessa forma, você pode reverter as chaves ou restringir o acesso para disparar seu aplicativo lógico com base em um período de tempo específico. Para especificar uma data de validade para um URL, utilize as [Aplicações Lógicas REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)por exemplo:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `NotAfter`usando uma corda de data JSON. Esta propriedade devolve um URL de callback que é válido apenas até a data e hora `NotAfter`.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Ao gerar ou listar URLs de retorno de chamada para um gatilho baseado em solicitação, você pode especificar a chave a ser usada para assinar a URL. Para gerar um URL assinado por uma chave específica, use as [Aplicações Lógicas REST API,](https://docs.microsoft.com/rest/api/logic/workflowtriggers)por exemplo:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a propriedade `KeyType` como `Primary` ou `Secondary`. Essa propriedade retorna uma URL que é assinada pela chave segura especificada.

<a name="restrict-inbound-ip"></a>

### <a name="restrict-inbound-ip-addresses"></a>Restringir endereços IP de entrada

Junto com a SAS (assinatura de acesso compartilhado), talvez você queira limitar especificamente os clientes que podem chamar seu aplicativo lógico. Por exemplo, se você gerenciar seu ponto de extremidade de solicitação usando o gerenciamento de API do Azure, poderá restringir seu aplicativo lógico para aceitar solicitações somente do endereço IP da instância de gerenciamento de API.

#### <a name="restrict-inbound-ip-ranges-in-azure-portal"></a>Restringir intervalos de IP de entrada no portal do Azure

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

1. No menu da sua aplicação lógica, em **Definições,** selecione **definições**de Fluxo de Trabalho .

1. Sob **a configuração do controlo** de acesso > permitido **endereços IP de entrada,** selecione **gamas IP específicas**.

1. Nos **intervalos IP para os gatilhos,** especifique as gamas de endereços IP que o gatilho aceita.

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

Se quiser que a sua aplicação lógica dispare apenas como uma aplicação lógica aninhada, a partir da lista de **endereços IP de entrada permitida,** selecione **Apenas outras Aplicações Lógicas**. Essa opção grava uma matriz vazia em seu recurso de aplicativo lógico. Dessa forma, somente chamadas do serviço de aplicativos lógicos (aplicativos lógicos pai) podem disparar o aplicativo lógico aninhado.

> [!NOTE]
> Independentemente do endereço IP, ainda pode executar uma aplicação lógica que tem um gatilho baseado em pedidos, utilizando `/triggers/<trigger-name>/run` através da API Do REST Azure ou através da API Management. No entanto, esse cenário ainda requer autenticação na API REST do Azure. Todos os eventos aparecem no log de auditoria do Azure. Verifique se você definiu as políticas de controle de acesso de forma adequada.

#### <a name="restrict-inbound-ip-ranges-in-azure-resource-manager-template"></a>Restringir intervalos de IP de entrada no modelo de Azure Resource Manager

Se [automatizar a implementação de aplicações lógicas utilizando modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Gestor de Recursos, pode especificar as gamas IP utilizando a secção `accessControl` com a secção `triggers` na definição de recursos da sua aplicação lógica, por exemplo:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Adicionar Azure Active Directory OAuth ou outra segurança

Para adicionar mais protocolos de autorização à sua aplicação lógica, considere utilizar o serviço de [Gestão API Azure.](../api-management/api-management-key-concepts.md) Esse serviço ajuda você a expor seu aplicativo lógico como uma API e oferece monitoramento, segurança, política e documentação avançados para qualquer ponto de extremidade. O gerenciamento de API pode expor um ponto de extremidade público ou privado para seu aplicativo lógico. Para autorizar o acesso a este ponto final, pode utilizar [o Azure Ative Directory OAuth,](#azure-active-directory-oauth-authentication) [certificado de cliente,](#client-certificate-authentication)ou outras normas de segurança para autorizar o acesso a esse ponto final. Quando o gerenciamento de API recebe uma solicitação, o serviço envia a solicitação para seu aplicativo lógico, também fazendo todas as transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas o gerenciamento de API dispare seu aplicativo lógico, você pode usar as configurações de intervalo de IP de entrada do aplicativo lógico.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicativo lógico

Você pode permitir que somente usuários ou grupos específicos executem tarefas específicas, como gerenciar, editar e exibir aplicativos lógicos. Para controlar as suas permissões, utilize o [Azure Role-Based Access Control (RBAC)](../role-based-access-control/role-assignments-portal.md) para que possa atribuir funções personalizadas ou incorporadas aos membros da sua subscrição Azure:

* [Logic App Contributor](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite-lhe gerir aplicações lógicas, mas não pode alterar o acesso às mesmas.

* [Logic App Operator](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite-lhe ler, ativar e desativar aplicações lógicas, mas não pode editá-las ou atualizá-las.

Para evitar que outros mudem ou abatam a sua aplicação lógica, pode utilizar o Bloqueio de [Recursos Azure](../azure-resource-manager/management/lock-resources.md). Essa funcionalidade impede que outras pessoas alterem ou excluam recursos de produção.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acesso a dados de histórico de execução

Durante uma execução de aplicações lógicas, todos os dados são [encriptados durante](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit) o trânsito utilizando a Transport Layer Security (TLS) e [em repouso](../security/fundamentals/encryption-atrest.md). Quando seu aplicativo lógico terminar de ser executado, você poderá exibir o histórico dessa execução, incluindo as etapas que foram executadas junto com o status, a duração, as entradas e as saídas de cada ação. Esse detalhe rico fornece informações sobre como seu aplicativo lógico foi executado e onde você pode começar a solucionar problemas que surgem.

Quando você exibe o histórico de execução do aplicativo lógico, os aplicativos lógicos autenticam seu acesso e fornecem links para as entradas e saídas para as solicitações e respostas para cada execução. No entanto, para ações que lidam com senhas, segredos, chaves ou outras informações confidenciais, você deseja impedir que outras pessoas exibam e acessem esses dados. Por exemplo, se a sua aplicação lógica obtém um segredo do [Azure Key Vault](../key-vault/key-vault-overview.md) para usar ao autenticar uma ação HTTP, você quer esconder esse segredo da vista.

Para controlar o acesso às entradas e saídas no histórico de execução do aplicativo lógico, você tem estas opções:

* [Restringir o acesso por gama de endereços IP](#restrict-ip).

  Essa opção permite proteger o acesso ao histórico de execução com base nas solicitações de um intervalo de endereços IP específico.

* Ocultar dados do histórico de [execução utilizando a obfuscção](#obfuscate).

  Em muitos gatilhos e ações, você pode ocultar suas entradas, saídas ou ambos do histórico de execução de um aplicativo lógico.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por intervalo de endereços IP

Você pode limitar o acesso às entradas e saídas no histórico de execução do aplicativo lógico para que somente as solicitações de intervalos de endereços IP específicos possam exibir esses dados. Por exemplo, para bloquear qualquer pessoa de aceder a entradas e saídas, especifique uma gama de endereços IP como `0.0.0.0-0.0.0.0`. Somente uma pessoa com permissões de administrador pode remover essa restrição, que fornece a possibilidade de acesso "Just-in-time" aos dados do seu aplicativo lógico. Você pode especificar os intervalos de IP para restringir usando o portal do Azure ou em um modelo de Azure Resource Manager que você usa para a implantação de aplicativo lógico.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restringir intervalos de IP no portal do Azure

1. No portal do Azure, abra seu aplicativo lógico no designer do aplicativo lógico.

1. No menu da sua aplicação lógica, em **Definições,** selecione **definições**de Fluxo de Trabalho .

1. Sob **a configuração do controlo** de acesso > permitido **endereços IP de entrada,** selecione **gamas IP específicas**.

1. Nas **gamas IP para conteúdos,** especifique as gamas de endereços IP que podem aceder ao conteúdo a partir de inputs e saídas. 

   A valid IP range uses these formats: *x.x.x.x/x* or *x.x.x.x-x.x.x.x*

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restringir intervalos de IP no modelo de Azure Resource Manager

Se [automatizar a implementação de aplicações lógicas utilizando modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Gestor de Recursos, pode especificar as gamas IP utilizando a secção `accessControl` com a secção `contents` na definição de recursos da sua aplicação lógica, por exemplo:

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

### <a name="hide-data-from-run-history-by-using-obfuscation"></a>Ocultar dados do histórico de execução usando ofuscação

Muitos gatilhos e ações têm configurações para ocultar entradas, saídas ou ambos do histórico de execução de um aplicativo lógico. Aqui ficam [algumas considerações a rever](#obfuscation-considerations) quando utiliza estas definições para proteger estes dados.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Proteger entradas e saídas no designer

1. No [portal Azure,](https://portal.azure.com)abra a sua aplicação lógica no Logic App Designer.

   ![Abrir o aplicativo lógico no designer de aplicativo lógico](./media/logic-apps-securing-a-logic-app/open-sample-logic-app-in-designer.png)

1. No gatilho ou ação onde pretende guardar dados, selecione o botão elipses ( **...** ) e, em seguida, selecione **Definições**.

   ![Abrir configurações de gatilho ou ação](./media/logic-apps-securing-a-logic-app/open-action-trigger-settings.png)

1. Ligue as **inputs seguras,** **as saídas seguras,** ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ativar entradas ou saídas seguras](./media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou gatilho agora mostra um ícone de cadeado na barra de título.

   ![A barra de título da ação ou do gatilho mostra o ícone de cadeado](./media/logic-apps-securing-a-logic-app/lock-icon-action-trigger-title-bar.png)

   Os tokens que representam saídas seguras de ações anteriores também mostram ícones de bloqueio. Por exemplo, quando você seleciona tal saída da lista de conteúdo dinâmico a ser usada em uma ação, esse token mostra um ícone de cadeado.

   ![Selecionar token para saída segura](./media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois que o aplicativo lógico for executado, você poderá exibir o histórico dessa execução.

   1. No painel de **visão geral** da aplicação lógica, selecione a execução que pretende ver.

   1. No painel de execução de **aplicações Logic,** expanda as ações que pretende rever.

      Se você optar por proteger as entradas e saídas, esses valores agora aparecerão ocultos.

      ![Entradas e saídas ocultas no histórico de execuções](./media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteger entradas e saídas no modo de exibição de código

Na definição de gatilho ou de ação subjacente, adicione ou atualize o conjunto de `runtimeConfiguration.secureData.properties` com ambos ou ambos os valores:

* `"inputs"`: Assegura as inputs na história da execução.
* `"outputs"`: Assegura saídas na história da execução.

Aqui ficam [algumas considerações a rever](#obfuscation-considerations) quando utiliza estas definições para proteger estes dados.

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

#### <a name="considerations-when-hiding-inputs-and-outputs"></a>Considerações ao ocultar entradas e saídas

* Quando você protege as entradas ou saídas em um gatilho ou ação, os aplicativos lógicos não enviam os dados protegidos para o Azure Log Analytics. Além disso, não é possível adicionar [propriedades rastreadas](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data) a esse gatilho ou ação para monitorização.

* A [API de Aplicações Lógicas para lidar com o histórico de fluxos](https://docs.microsoft.com/rest/api/logic/) de trabalho não devolve saídas seguras.

* Para proteger as saídas de uma ação que proteja as inputs ou utilize explicitamente saídas seguras, ligue manualmente as **saídas seguras** nessa ação.

* Certifique-se de que liga **as inputs seguras** ou as **saídas seguras** em ações a jusante, onde espera que o histórico de execução garanta esses dados.

  **Definição de saídas seguras**

  Quando liga manualmente as **saídas seguras** num gatilho ou ação, as Aplicações Lógicas asseguram estas saídas no histórico de execução. Se uma ação a jusante usa explicitamente estas saídas seguras como inputs, a Logic Apps esconde as inputs desta ação na história da execução, mas *não permite* a definição de **Inputs Seguras** da ação.

  ![Saídas protegidas como entradas e impacto de downstream na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações Compor, Parse JSON e Response têm apenas a definição de **Inputs Seguras.** Quando ativado, a configuração também oculta as saídas das ações. Se estas ações usarem explicitamente as saídas seguras a montante como inputs, a Logic Apps esconde as inputs e saídas destas ações, mas *não permite* a definição de **Inputs Seguras destas** ações. Se uma ação a jusante utilizar explicitamente as saídas ocultas das ações Compor, Parse JSON ou Response como inputs, a Logic Apps *não esconde as inputs ou saídas desta ação a jusante.*

  ![Saídas protegidas como entradas com impacto de downstream em ações específicas](./media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Definição de inputs seguros**

  Quando liga manualmente **as inputs seguras** num gatilho ou ação, as Aplicações Lógicas asseguram estas inputs na história da execução. Se uma ação a jusante usar explicitamente as saídas visíveis desse gatilho ou ação como inputs, a Logic Apps esconde esta entrada de ação a jusante na história da execução, mas *não permite* **inputs seguros** nesta ação e não esconde as saídas desta ação.

  ![Entradas seguras e impacto de downstream na maioria das ações](./media/logic-apps-securing-a-logic-app/secure-inputs-impact-on-downstream.png)

  Se as ações Compor, Parse JSON e Response usarem explicitamente as saídas visíveis do gatilho ou ação que tem as inputs seguras, a Logic Apps esconde as inputs e saídas destas ações, mas *não permite* a definição de **Inputs Seguras** desta ação. Se uma ação a jusante utilizar explicitamente as saídas ocultas das ações Compor, Parse JSON ou Response como inputs, a Logic Apps *não esconde as inputs ou saídas desta ação a jusante.*

  ![Entradas seguras e impacto de downstream em ações específicas](./media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso a entradas de parâmetro

Se você implantar em ambientes diferentes, considere a possibilidade de parametrização dos valores na definição do fluxo de trabalho que variam de acordo com esses ambientes. Desta forma, pode evitar dados codificados com código sinuoso utilizando um modelo de Gestor de [Recursos Azure](../azure-resource-manager/templates/overview.md) para implementar a sua aplicação lógica, proteger dados sensíveis definindo parâmetros seguros e passar esses dados como entradas separadas através [dos parâmetros do modelo](../azure-resource-manager/templates/template-parameters.md) utilizando um ficheiro de [parâmetros](../azure-resource-manager/templates/parameter-files.md).

Por exemplo, se autenticar as ações do HTTP com o [Azure Ative Directory OAuth,](#azure-active-directory-oauth-authentication)pode definir e fixar os parâmetros que aceitam o ID do cliente e o segredo do cliente que são usados para autenticação. Para definir estes parâmetros na sua aplicação lógica, use a secção `parameters` na definição de fluxo de trabalho da sua aplicação lógica e modelo de Gestor de Recursos para implementação. Para ocultar valores de parâmetros que não quer mostrados ao editar a sua aplicação lógica ou ver o histórico de execução, defina os parâmetros utilizando o tipo `securestring` ou `secureobject` e use a codificação conforme necessário. Parâmetros que têm esse tipo não são retornados com a definição de recurso e não são acessíveis ao exibir o recurso após a implantação. Para aceder a estes valores de parâmetros durante o tempo de funcionamento, utilize a expressão `@parameters('<parameter-name>')` dentro da definição de fluxo de trabalho. Esta expressão é avaliada apenas no prazo de funcionamento e é descrita pela [Linguagem definição](../logic-apps/logic-apps-workflow-definition-language.md)de fluxo de trabalho.

> [!NOTE]
> Se você usar um parâmetro em um cabeçalho ou corpo de solicitação, esse parâmetro poderá ser visível quando você exibir o histórico de execução do aplicativo lógico e a solicitação HTTP de saída. Certifique-se de também definir suas políticas de acesso de conteúdo adequadamente. Também pode usar [a obfuscação](#obfuscate) para ocultar inputs e saídas na sua história de execução. Os cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Portanto, se um segredo for usado lá, esse segredo não será recuperável.

Para obter mais informações, consulte estas seções neste tópico:

* [Parâmetros seguros nas definições de fluxo de trabalho](#secure-parameters-workflow)
* [Ocultar dados do histórico de execução usando a obfuscção](#obfuscate)

Se [automatizar a implementação de aplicações lógicas utilizando modelos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)de Gestor de Recursos, pode definir [parâmetros](../azure-resource-manager/templates/template-parameters.md)de modelo seguros , que são avaliados na implementação, utilizando os tipos `securestring` e `secureobject`. Para definir parâmetros de modelo, utilize a secção `parameters` de nível superior do seu modelo, que é separada e diferente da secção `parameters` da definição de fluxo de trabalho. Para fornecer os valores para parâmetros de modelo, utilize um [ficheiro de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado .

Por exemplo, se utilizar segredos, pode definir e usar parâmetros de modelo seguros que recuperam esses segredos do [Cofre chave Azure](../key-vault/key-vault-overview.md) na implantação. Em seguida, você pode fazer referência ao cofre de chaves e ao segredo em seu arquivo de parâmetro. Para mais informações, consulte estes tópicos:

* [Passe valores sensíveis na implantação utilizando o Cofre chave Azure](../azure-resource-manager/templates/key-vault-parameter.md)
* [Parâmetros seguros em modelos de Gestor de Recursos Azure](#secure-parameters-deployment-template) mais tarde neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteger parâmetros em definições de fluxo de trabalho

Para proteger informações confidenciais na definição de fluxo de trabalho do aplicativo lógico, use parâmetros protegidos para que essas informações não fiquem visíveis depois que você salvar seu aplicativo lógico. Por exemplo, suponha que você tenha uma ação HTTP requer autenticação básica, que usa um nome de usuário e senha. Na definição de fluxo de trabalho, a secção `parameters` define os parâmetros `basicAuthPasswordParam` e `basicAuthUsernameParam` utilizando o tipo `securestring`. A definição de ação refere então estes parâmetros na secção `authentication`.

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

### <a name="secure-parameters-in-azure-resource-manager-templates"></a>Proteger parâmetros em modelos de Azure Resource Manager

Um [modelo de Gestor de Recursos](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) para uma aplicação lógica tem várias secções `parameters`. Para proteger palavras-passe, chaves, segredos e outras informações sensíveis, defina parâmetros seguros ao nível do modelo e nível de definição de fluxo de trabalho utilizando o tipo `securestring` ou `secureobject`. Em seguida, pode armazenar estes valores no [Cofre de Chaves Azure](../key-vault/key-vault-overview.md) e utilizar o ficheiro de [parâmetro](../azure-resource-manager/templates/parameter-files.md) para fazer referência ao cofre e segredo da chave. Em seguida, o modelo recupera essas informações na implantação. Para mais informações, consulte [os valores sensíveis do Passe na implementação utilizando](../azure-resource-manager/templates/key-vault-parameter.md)o Cofre de Chaves Azure .

Aqui está mais informações sobre estas secções `parameters`:

* No nível superior do modelo, uma secção `parameters` define os parâmetros para os valores que o modelo utiliza na *implementação*. Por exemplo, esses valores podem incluir cadeias de conexão para um ambiente de implantação específico. Em seguida, pode armazenar estes valores num [ficheiro de parâmetro](../azure-resource-manager/templates/parameter-files.md)separado, o que facilita a alteração destes valores.

* Dentro da definição de recursos da sua aplicação lógica, mas fora da definição de fluxo de trabalho, uma secção `parameters` especifica os valores para os parâmetros da definição de fluxo de trabalho. Nesta seção, você pode atribuir esses valores usando expressões de modelo que fazem referência aos parâmetros do modelo. Essas expressões são avaliadas na implantação.

* Dentro da definição de fluxo de trabalho, uma secção `parameters` define os parâmetros que a sua aplicação lógica utiliza no tempo de execução. Em seguida, você pode fazer referência a esses parâmetros dentro do fluxo de trabalho do aplicativo lógico usando expressões de definição de fluxo de trabalho, que são avaliadas no tempo de execução.

Este modelo de exemplo que tem múltiplas definições de parâmetros seguros que usam o tipo `securestring`:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma palavra-passe que é então passada para o parâmetro de `basicAuthPasswordParam` da definição de fluxo de trabalho |
| `TemplateUsernameParam` | Um parâmetro de modelo que aceita um nome de utilizador que é então passado para o parâmetro de `basicAuthUserNameParam` da definição de fluxo de trabalho |
| `basicAuthPasswordParam` | Um parâmetro de definição de fluxo de trabalho que aceita a senha para autenticação básica em uma ação HTTP |
| `basicAuthUserNameParam` | Um parâmetro de definição de fluxo de trabalho que aceita o nome de usuário para autenticação básica em uma ação HTTP |
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

<a name="secure-requests"></a>

## <a name="access-to-services-and-systems-called-from-logic-apps"></a>Acesso a serviços e sistemas chamados de aplicativos lógicos

Aqui estão algumas maneiras que você pode proteger pontos de extremidade que recebem chamadas ou solicitações de seu aplicativo lógico:

* Adicionar autenticação a solicitações de saída.

  Quando você trabalha com um gatilho ou uma ação baseada em HTTP que faz chamadas de saída, como HTTP, HTTP + Swagger ou webhook, você pode adicionar autenticação à solicitação enviada pelo seu aplicativo lógico. Por exemplo, você pode usar esses tipos de autenticação:

  * [Autenticação básica](#basic-authentication)

  * [Autenticação do certificado de cliente](#client-certificate-authentication)

  * [Autenticação Ativa OAuth OAuth](#azure-active-directory-oauth-authentication)

  * [Autenticação de identidade gerida](#managed-identity-authentication)
  
  Para mais informações, consulte [Adicionar autenticação a chamadas de saída](#add-authentication-outbound) mais tarde neste tópico.

* Restringir o acesso de endereços IP do aplicativo lógico.

  Todas as chamadas para pontos de extremidade de aplicativos lógicos originam-se de endereços IP designados específicos que se baseiam nas regiões dos seus aplicativos lógicos. Você pode adicionar a filtragem que aceita solicitações somente desses endereços IP. Para obter estes endereços IP, consulte [Limites e configuração para Aplicações Lógicas Azure](logic-apps-limits-and-config.md#configuration).

* Proteger conexões com sistemas locais.

  Os aplicativos lógicos do Azure fornecem integração com esses serviços para comunicação local segura e confiável.

  * Gateway de dados no local

    Muitos conectores gerenciados nos aplicativos lógicos do Azure fornecem conexões seguras para sistemas locais, como sistema de arquivos, SQL, SharePoint e DB2. O gateway envia dados de fontes locais em canais criptografados por meio do barramento de serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba [como funciona o gateway de dados no local.](logic-apps-gateway-install.md#gateway-cloud-service)

  * Conectar-se por meio do gerenciamento de API do Azure

    A [Azure API Management](../api-management/api-management-key-concepts.md) fornece opções de ligação no local, tais como rede privada virtual local e integração ExpressRoute para procuração segura e comunicação para sistemas no local. Do fluxo de trabalho do aplicativo lógico no designer do aplicativo lógico, você pode selecionar uma API que é exposta pelo gerenciamento de API, que fornece acesso rápido a sistemas locais.

<a name="add-authentication-outbound"></a>

## <a name="add-authentication-to-outbound-calls"></a>Adicionar autenticação a chamadas de saída

Os pontos de extremidade HTTP e HTTPS dão suporte a vários tipos de autenticação. Com base no gatilho ou na ação que você usa para fazer chamadas ou solicitações de saída que acessam esses pontos de extremidade, você pode selecionar entre intervalos variados de tipos de autenticação. Para garantir que você proteja todas as informações confidenciais que seu aplicativo lógico manipula, use parâmetros protegidos e codifique os dados conforme necessário. Para obter mais informações sobre a utilização e a fixação de parâmetros, consulte [Acesso às inputs](#secure-action-parameters)dos parâmetros .

> [!NOTE]
> No Logic App Designer, a propriedade **de Autenticação** pode estar escondida em alguns gatilhos e ações onde pode especificar o tipo de autenticação. Para que a propriedade apareça nestes casos, no gatilho ou na ação, abra a **lista adicionar novo parâmetro** e selecione **Autenticação**. Para mais informações, consulte [O acesso authenticado com identidade gerida.](../logic-apps/create-managed-service-identity.md#authenticate-access-with-identity)

| Tipo de autenticação | Suportado por |
|---------------------|--------------|
| [Básica](#basic-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, HTTP, HTTP + Swagger, webhook HTTP |
| [Certificado de Cliente](#client-certificate-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, HTTP, HTTP + Swagger, webhook HTTP |
| [Diretório Ativo OAuth](#azure-active-directory-oauth-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, HTTP + Swagger, webhook HTTP |
| [Cru](#raw-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, HTTP + Swagger, webhook HTTP |
| [Identidade gerida](#managed-identity-authentication) | Gerenciamento de API do Azure, serviços Azure Apps, Azure Functions, HTTP, HTTP + Swagger, webhook HTTP |
|||

<a name="basic-authentication"></a>

### <a name="basic-authentication"></a>Autenticação básica

Se a opção [Basic](../active-directory-b2c/secure-rest-api-dotnet-basic-auth.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (Designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Básica | O tipo de autenticação a ser usado |
| **Nome de Utilizador** | `username` | Sim | < *> de nome de utilizador*| O nome de usuário para autenticar o acesso ao ponto de extremidade de serviço de destino |
| **Palavra-passe** | `password` | Sim | < *> de senha* | A senha para autenticar o acesso ao ponto de extremidade de serviço de destino |
||||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http exemplo especifica a autenticação `type` como `Basic` e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

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

### <a name="client-certificate-authentication"></a>Autenticação de certificado de cliente

Se a opção [Certificado cliente](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) estiver disponível, especifique estes valores de propriedade:

| Propriedade (Designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Certificado de Cliente** <br>ou <br>`ClientCertificate` | O tipo de autenticação a ser usado para certificados de cliente protocolo SSL (SSL). Embora haja suporte para certificados autoassinados, não há suporte para certificados autoassinados para SSL. |
| **Pfx** | `pfx` | Sim | <*codificado-pfx-file-content*> | O conteúdo codificado em Base64 de um arquivo de troca de informações pessoais (PFX) <p><p>Para converter o arquivo PFX em formato codificado em base64, você pode usar o PowerShell seguindo estas etapas: <p>1. Salve o conteúdo do certificado em uma variável: <p>   `$pfx_cert = get-content 'c:\certificate.pfx' -Encoding Byte` <p>2. Converter o conteúdo do certificado utilizando a função `ToBase64String()` e guardar esse conteúdo num ficheiro de texto: <p>   `[System.Convert]::ToBase64String($pfx_cert) | Out-File 'pfx-encoded-bytes.txt'` |
| **Palavra-passe** | `password`| Não | < *> de ficheiro saque-para-pfx* | A senha para acessar o arquivo PFX |
|||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http exemplo especifica a autenticação `type` como `ClientCertificate` e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

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

Para obter mais informações sobre como proteger serviços usando a autenticação de certificado do cliente, consulte estes tópicos:

* [APIs seguros que utilizam autenticação de certificado de cliente na Gestão da API Azure](../api-management/api-management-howto-mutual-certificates-for-clients.md)
* [Serviços back-end seguros usando autenticação de certificado de cliente na Azure API Management](../api-management/api-management-howto-mutual-certificates.md)
* [Proteja o seu serviço RESTfuL utilizando certificados de cliente](../active-directory-b2c/secure-rest-api-dotnet-certificate-auth.md)
* [Credenciais de certificado para autenticação de pedido](../active-directory/develop/active-directory-certificate-credentials.md)
* [Utilize um certificado SSL no seu código de aplicação no Serviço de Aplicações Azure](../app-service/configure-ssl-certificate-in-code.md)

<a name="azure-active-directory-oauth-authentication"></a>

### <a name="azure-active-directory-oauth-authentication"></a>Azure Active Directory Autenticação OAuth

Se a opção [OAuth](../active-directory/develop/about-microsoft-identity-platform.md) do Diretório Ativo estiver disponível, especifique estes valores de propriedade:

| Propriedade (Designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | **Diretório Ativo OAuth** <br>ou <br>`ActiveDirectoryOAuth` | O tipo de autenticação a utilizar. As Aplicações Lógicas seguem atualmente o [protocolo OAuth 2.0.](../active-directory/develop/v2-overview.md) |
| **Autoridade** | `authority` | Não | <*URL-for-authority-token-emitente*> | A URL para a autoridade que fornece o token de autenticação. Por predefinição, este valor é `https://login.windows.net`. |
| **Inquilino** | `tenant` | Sim | < *> de identificação de inquilino* | A ID de locatário para o locatário do Azure AD |
| **Público** | `audience` | Sim | <*recurso-para autorizar*> | O recurso que pretende utilizar para autorização, por exemplo, `https://management.core.windows.net/` |
| **ID do cliente** | `clientId` | Sim | <> *de identificação do cliente* | A ID do cliente para o aplicativo que solicita autorização |
| **Tipo credencial** | `credentialType` | Sim | Certificado <br>ou <br>Segredo | O tipo de credencial que o cliente usa para solicitar autorização. Essa propriedade e o valor não aparecem na definição subjacente do aplicativo lógico, mas determina as propriedades que aparecem para o tipo de credencial selecionado. |
| **Segredo** | `secret` | Sim, mas apenas para o tipo de credencial "segredo" | <> *segredo de cliente* | O segredo do cliente para solicitar autorização |
| **Pfx** | `pfx` | Sim, mas somente para o tipo de credencial "certificado" | <*codificado-pfx-file-content*> | O conteúdo codificado em Base64 de um arquivo de troca de informações pessoais (PFX) |
| **Palavra-passe** | `password` | Sim, mas somente para o tipo de credencial "certificado" | < *> de ficheiro saque-para-pfx* | A senha para acessar o arquivo PFX |
|||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http exemplo especifica a autenticação `type` como `ActiveDirectoryOAuth`, o tipo credencial como `Secret`, e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores do parâmetro:

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

Se a opção **Raw** estiver disponível, pode utilizar este tipo de autenticação quando tiver de utilizar esquemas de [autenticação](https://iana.org/assignments/http-authschemes/http-authschemes.xhtml) que não sigam o [protocolo OAuth 2.0](https://oauth.net/2/). Com esse tipo, você cria manualmente o valor do cabeçalho de autorização que você envia com a solicitação de saída e especifica esse valor de cabeçalho em seu gatilho ou ação.

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

No gatilho ou ação que dá suporte à autenticação bruta, especifique estes valores de propriedade:

| Propriedade (Designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
|---------------------|-----------------|----------|-------|-------------|
| **Autenticação** | `type` | Sim | Cru | O tipo de autenticação a ser usado |
| **Valor** | `value` | Sim | <> *de valor de autorização-cabeçalho* | O valor do cabeçalho de autorização a ser usado para autenticação |
||||||

Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http exemplo especifica a autenticação `type` como `Raw`, e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

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

Se a opção [Identidade Gerida](../active-directory/managed-identities-azure-resources/overview.md) estiver disponível, a sua aplicação lógica pode utilizar a identidade atribuída pelo sistema ou uma *única* identidade atribuída manualmente ao utilizador para autenticar o acesso a recursos em outros inquilinos do Azure Ative Directory (Azure AD) sem se inscrever. O Azure gere esta identidade para si e ajuda a garantir as suas credenciais porque não tem de fornecer ou rodar segredos. Saiba mais sobre [os serviços Azure que suportam identidades geridas para a autenticação da AD Azure.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

1. Antes de a sua aplicação lógica poder usar uma identidade gerida, siga os passos no [acesso authenticado aos recursos do Azure utilizando identidades geridas em Aplicações Lógicas Azure](../logic-apps/create-managed-service-identity.md). Essas etapas habilitam a identidade gerenciada em seu aplicativo lógico e configuram o acesso da identidade ao recurso do Azure de destino.

1. Antes de uma função Azure poder utilizar uma identidade gerida, primeiro [permitir a autenticação para funções Azure](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

1. No gatilho ou na ação em que você deseja usar a identidade gerenciada, especifique estes valores de propriedade:

   | Propriedade (Designer) | Propriedade (JSON) | Necessário | Valor | Descrição |
   |---------------------|-----------------|----------|-------|-------------|
   | **Autenticação** | `type` | Sim | **Identidade Gerida** <br>ou <br>`ManagedServiceIdentity` | O tipo de autenticação a ser usado |
   | **Identidade Gerida** | `identity` | Sim | *** Sistema De Identidade Gerida Atribuída** <br>ou <br>`SystemAssigned` <p><p>* < nome de*identidade atribuído* ao utilizador> | A identidade gerida para usar |
   | **Público** | `audience` | Sim | <> *de identificação de recursos-alvo* | A ID de recurso para o recurso de destino que você deseja acessar. <p>Por exemplo, `https://storage.azure.com/` torna os tokens de acesso para autenticação válidos para todas as contas de armazenamento. No entanto, também pode especificar um URL de serviço de raiz, como `https://fabrikamstorageaccount.blob.core.windows.net` para uma conta de armazenamento específica. <p>**Nota:** A propriedade **do Público** pode estar escondida em alguns gatilhos ou ações. Para tornar esta propriedade visível, no gatilho ou na ação, abra a **lista de novos parâmetros e** selecione **Audience**. <p><p>**Importante:** Certifique-se de que este ID de recurso alvo *corresponde exatamente* ao valor que a Azure AD espera, incluindo quaisquer cortes de rasto necessários. Assim, o `https://storage.azure.com/` identificação de recursos para todas as contas de Armazenamento De Blob Azure requer um corte de rasto. No entanto, o ID de recursos para uma conta de armazenamento específica não requer um corte de rasto. Para encontrar estes IDs de recursos, consulte [os serviços Azure que suportam a Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   |||||

   Quando utiliza [parâmetros seguros](#secure-action-parameters) para manusear e proteger informações sensíveis, por exemplo, num modelo do Gestor de [Recursos Azure para automatizar a implementação,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)pode utilizar expressões para aceder a estes valores de parâmetros no tempo de execução. Esta definição de ação http exemplo especifica a autenticação `type` como `ManagedServiceIdentity` e utiliza a [função de parâmetros](../logic-apps/workflow-definition-language-functions-reference.md#parameters) para obter os valores dos parâmetros:

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
* [Implementação de aplicativos lógicos automate](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
