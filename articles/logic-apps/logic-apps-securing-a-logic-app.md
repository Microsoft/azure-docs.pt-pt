---
title: Proteger o acesso e os dados-aplicativos lógicos do Azure
description: Proteger entradas de parâmetro, gatilhos de solicitação HTTP, histórico de execução, operações de aplicativo lógico e conexões com outros serviços em aplicativos lógicos do Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/28/2019
ms.openlocfilehash: f27dfd1f907d106ddb3b1b9dd7534d56380149c2
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385493"
---
# <a name="secure-access-and-data-in-azure-logic-apps"></a>Proteger o acesso e os dados no aplicativo lógico do Azure

Para controlar o acesso e proteger os dados nos aplicativos lógicos do Azure, você pode configurar a segurança nessas áreas:

* [Acesso a gatilhos de solicitação HTTP](#secure-triggers)
* [Acesso a operações de aplicativo lógico](#secure-operations)
* [Acesso a entradas e saídas do histórico de execução](#secure-run-history)
* [Acesso a entradas de parâmetro](#secure-action-parameters)
* [Acesso a serviços e sistemas chamados de aplicativos lógicos](#secure-requests)

<a name="secure-triggers"></a>

## <a name="access-to-http-request-triggers"></a>Acesso a gatilhos de solicitação HTTP

Quando seu aplicativo lógico usa um gatilho baseado em solicitação HTTP, como o gatilho de [solicitação](../connectors/connectors-native-reqres.md) ou webhook, você pode limitar o acesso para que somente clientes autorizados possam iniciar seu aplicativo lógico. [](../connectors/connectors-native-webhook.md) Todas as solicitações recebidas por um aplicativo lógico são criptografadas e protegidas com o protocolo protocolo SSL (SSL). 

Aqui estão as maneiras como você pode proteger o acesso a esse tipo de gatilho:

* [Gerar assinaturas de acesso compartilhado](#sas)
* [Restringir endereços IP de entrada](#restrict-incoming-ip-addresses)
* [Adicionar Azure Active Directory, OAuth ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures-sas"></a>Gerar SAS (assinaturas de acesso compartilhado)

Cada ponto de extremidade de solicitação em um aplicativo lógico tem uma [assinatura de acesso compartilhado (SAS)](https://docs.microsoft.com/rest/api/storageservices/constructing-a-service-sas) na URL do ponto de extremidade, que segue este formato:

`https://<request-endpoint-URI>sp=<permissions>sv=<SAS-version>sig=<signature>`

Cada URL contém um `sp`parâmetro `sv`de consulta `sig` , e, conforme descrito nesta tabela:

| Parâmetro de consulta | Descrição |
|-----------------|-------------|
| `sp` | Especifica as permissões para os métodos HTTP permitidos a serem usados. |
| `sv` | Especifica a versão SAS a ser usada para gerar a assinatura. |
| `sig` | Especifica a assinatura a ser usada para autenticar o acesso ao gatilho. Essa assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso secreta em todos os caminhos e propriedades de URL. Nunca exposto ou publicado, essa chave é mantida criptografada e armazenada com o aplicativo lógico. Seu aplicativo lógico autoriza somente os gatilhos que contêm uma assinatura válida criada com a chave secreta. |
|||

Para obter mais informações sobre como proteger o acesso com a assinatura de acesso compartilhado, consulte estas seções neste tópico:

* [Regenerar chaves de acesso](#access-keys)
* [Criar URLs de retorno de chamada expirando](#expiring-urls)
* [Criar URLs com chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Regenerar chaves de acesso

Para gerar uma nova chave de acesso seguro a qualquer momento, use a API REST do Azure ou portal do Azure. Todas as URLs geradas anteriormente que usam a chave antiga são invalidadas e não têm mais autorização para disparar o aplicativo lógico. As URLs que você recupera após a regeneração são assinadas com a nova chave de acesso.

1. No portal do Azure, abra o aplicativo lógico que tem a chave que você deseja regenerar.

1. No menu do aplicativo lógico, em **configurações**, selecione **chaves de acesso**.

1. Selecione a chave que você deseja regenerar e conclua o processo.

<a name="expiring-urls"></a>

#### <a name="create-expiring-callback-urls"></a>Criar URLs de retorno de chamada expirando

Se você compartilhar a URL do ponto de extremidade para um gatilho baseado em solicitação HTTP com outras partes, poderá gerar URLs de retorno de chamada que usam chaves específicas e têm datas de expiração. Dessa forma, você pode reverter as chaves ou restringir o acesso para disparar seu aplicativo lógico com base em um período de tempo específico. Para especificar uma data de validade para uma URL, use a [API REST dos aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `NotAfter`propriedade usando uma cadeia de caracteres de data JSON. Essa propriedade retorna uma URL de retorno de chamada que é válida `NotAfter` somente até a data e hora.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com chave secreta primária ou secundária

Ao gerar ou listar URLs de retorno de chamada para gatilhos baseados em solicitação HTTP, você pode especificar a chave a ser usada para assinar a URL. Para gerar uma URL que é assinada por uma chave específica, use a [API REST de aplicativos lógicos](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST /subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/workflows/<workflow-name>/triggers/<trigger-name>/listCallbackUrl?api-version=2016-06-01
```

No corpo, inclua a `KeyType` propriedade `Primary` como ou `Secondary`. Essa propriedade retorna uma URL que é assinada pela chave segura especificada.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Restringir endereços IP de entrada

Junto com a assinatura de acesso compartilhado, talvez você queira limitar os clientes específicos que podem chamar seu aplicativo lógico. Por exemplo, se você gerenciar seu ponto de extremidade de solicitação usando o gerenciamento de API do Azure, poderá restringir seu aplicativo lógico para aceitar solicitações somente do endereço IP da instância de gerenciamento de API.

#### <a name="restrict-incoming-ip-ranges-in-azure-portal"></a>Restringir os intervalos de IP de entrada no portal do Azure

1. No portal do Azure, abra seu aplicativo lógico no designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **configurações de fluxo de trabalho**.

1. Em **configuração** > de controle de acesso**permitidos endereços IP de entrada**, selecione **intervalos de IP específicos**.

1. Em **intervalos de IP para gatilhos**, especifique os intervalos de endereços IP que o gatilho aceita.

   Um intervalo de IP válido usa estes formatos: x. x. x *. x/x* ou *x* . x. x. x. x. x

Se você quiser que seu aplicativo lógico seja disparado apenas como um aplicativo lógico aninhado, na lista **endereços IP de entrada permitidos** , selecione **somente outros aplicativos lógicos**. Essa opção grava uma matriz vazia em seu recurso de aplicativo lógico. Dessa forma, somente chamadas do serviço de aplicativos lógicos (aplicativos lógicos pai) podem disparar o aplicativo lógico aninhado.

> [!NOTE]
> Independentemente do endereço IP, você ainda pode executar um aplicativo lógico que tenha um gatilho baseado em solicitação HTTP usando `/triggers/<trigger-name>/run` por meio da API REST do Azure ou por meio do gerenciamento de API. No entanto, esse cenário ainda requer autenticação na API REST do Azure. Todos os eventos aparecem no log de auditoria do Azure. Verifique se você definiu as políticas de controle de acesso de forma adequada.

#### <a name="restrict-incoming-ip-ranges-in-azure-resource-manager-template"></a>Restringir os intervalos de IP de entrada no modelo de Azure Resource Manager

Se você automatizar implantações de aplicativo lógico usando um [modelo de Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), poderá especificar os intervalos de IP usando `accessControl` a seção com `triggers` a seção na definição de recurso do aplicativo lógico, por exemplo:

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

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Adicionar Azure Active Directory, OAuth ou outra segurança

Para adicionar mais protocolos de autorização ao seu aplicativo lógico, considere usar o [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md). Esse serviço oferece a capacidade de expor seu aplicativo lógico como uma API e oferece monitoramento, segurança, política e documentação avançados para qualquer ponto de extremidade. O gerenciamento de API pode expor um ponto de extremidade público ou privado para seu aplicativo lógico, que pode usar Azure Active Directory, OAuth, certificado ou outros padrões de segurança. Quando o gerenciamento de API recebe uma solicitação, o serviço envia a solicitação para seu aplicativo lógico, também fazendo todas as transformações ou restrições necessárias ao longo do caminho. Para permitir que apenas o gerenciamento de API dispare seu aplicativo lógico, você pode usar as configurações de intervalo de IP de entrada do aplicativo lógico.

<a name="secure-operations"></a>

## <a name="access-to-logic-app-operations"></a>Acesso a operações de aplicativo lógico

Você pode permitir que somente usuários ou grupos específicos executem operações específicas, como gerenciar, editar e exibir aplicativos lógicos. Para controlar suas permissões, use o [RBAC (controle de acesso baseado em função) do Azure](../role-based-access-control/role-assignments-portal.md) para atribuir funções personalizadas ou internas aos membros em sua assinatura do Azure:

* [Colaborador do aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-contributor): Permite que você gerencie aplicativos lógicos, mas não pode alterar o acesso a eles.

* [Operador de aplicativo lógico](../role-based-access-control/built-in-roles.md#logic-app-operator): Permite que você leia, habilite e desabilite aplicativos lógicos, mas não pode editá-los ou atualizá-los.

Para impedir que outras pessoas alterem ou excluam seu aplicativo lógico, você pode usar o [bloqueio de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md), o que impede que outras pessoas alterem ou excluam recursos de produção.

<a name="secure-run-history"></a>

## <a name="access-to-run-history-data"></a>Acesso a dados de histórico de execução

Durante a execução de um aplicativo lógico, todos os dados são criptografados durante o trânsito e em repouso. Quando seu aplicativo lógico terminar de ser executado, você poderá exibir o histórico dessa execução, incluindo as etapas que foram executadas junto com o status, a duração, as entradas e as saídas de cada ação. Esse detalhe rico fornece informações sobre como seu aplicativo lógico foi executado e onde você pode começar a solucionar problemas que surgem.

Quando você acessa o histórico de execução do aplicativo lógico, os aplicativos lógicos autenticam seu acesso e fornecem links para as entradas e saídas das solicitações e respostas na execução do seu aplicativo lógico. No entanto, para ações que lidam com senhas, segredos, chaves ou outras informações confidenciais, você deseja impedir que outras pessoas exibam e acessem esses dados. Por exemplo, se seu aplicativo lógico obtiver um segredo de [Azure Key Vault](../key-vault/key-vault-whatis.md) para usar ao autenticar uma ação http, você deseja ocultar esse segredo da exibição.

Para controlar o acesso às entradas e saídas no histórico de execução do aplicativo lógico, você tem estas opções:

* [Restringir o acesso por intervalo de endereços IP](#restrict-ip).

  Essa opção permite proteger o acesso ao histórico de execução com base nas solicitações de um intervalo de endereços IP específico.

* [Ocultar dados do histórico de execução usando ofuscação](#obfuscate).

  Em muitos gatilhos e ações, você pode ocultar suas entradas, saídas ou ambos do histórico de execução de um aplicativo lógico.

<a name="restrict-ip"></a>

### <a name="restrict-access-by-ip-address-range"></a>Restringir o acesso por intervalo de endereços IP

Você pode limitar o acesso às entradas e saídas no histórico de execução do aplicativo lógico para que somente as solicitações de intervalos de endereços IP específicos possam exibir esses dados. Por exemplo, para impedir que qualquer pessoa acesse entradas e saídas, especifique um intervalo `0.0.0.0-0.0.0.0`de endereços IP, como. Somente uma pessoa com permissões de administrador pode remover essa restrição, que fornece a possibilidade de acesso "Just-in-time" aos dados do seu aplicativo lógico. Você pode especificar os intervalos de IP para restringir usando o portal do Azure ou em um modelo de Azure Resource Manager que você usa para a implantação de aplicativo lógico.

#### <a name="restrict-ip-ranges-in-azure-portal"></a>Restringir intervalos de IP no portal do Azure

1. No portal do Azure, abra seu aplicativo lógico no designer do aplicativo lógico.

1. No menu do aplicativo lógico, em **configurações**, selecione **configurações de fluxo de trabalho**.

1. Em **configuração** > de controle de acesso**permitidos endereços IP de entrada**, selecione **intervalos de IP específicos**.

1. Em **intervalos IP para conteúdo**, especifique os intervalos de endereços IP que podem acessar o conteúdo de entradas e saídas. 

   Um intervalo de IP válido usa estes formatos: x. x. x *. x/x* ou *x* . x. x. x. x. x

#### <a name="restrict-ip-ranges-in-azure-resource-manager-template"></a>Restringir intervalos de IP no modelo de Azure Resource Manager

Se você automatizar implantações de aplicativo lógico usando um [modelo de Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), poderá especificar os intervalos de IP usando `accessControl` a seção com `contents` a seção na definição de recurso do aplicativo lógico, por exemplo:

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

Muitos gatilhos e ações têm configurações para ocultar entradas, saídas ou ambos do histórico de execução de um aplicativo lógico. Aqui estão algumas [considerações a serem examinadas](#obfuscation-considerations) ao usar essas configurações para proteger esses dados.

#### <a name="secure-inputs-and-outputs-in-the-designer"></a>Proteger entradas e saídas no designer

1. Se seu aplicativo lógico ainda não estiver aberto no [portal do Azure](https://portal.azure.com), abra seu aplicativo lógico no designer do aplicativo lógico.

   ![Abrir aplicativo lógico de exemplo](media/logic-apps-securing-a-logic-app/sample-logic-app.png)

1. No gatilho ou na ação em que você deseja proteger os dados, selecione o botão de reticências ( **...** ) e, em seguida, selecione **configurações**.

   ![Abrir "configurações"](media/logic-apps-securing-a-logic-app/open-settings.png)

1. Ative as **entradas seguras**, as **saídas seguras**ou ambas. Quando tiver terminado, selecione **Concluído**.

   ![Ativar entradas ou saídas seguras](media/logic-apps-securing-a-logic-app/turn-on-secure-inputs-outputs.png)

   A ação ou gatilho agora mostra um ícone de cadeado na barra de título.

   ![Ícone de cadeado na barra de título](media/logic-apps-securing-a-logic-app/title-bar-lock-icon.png)

   Os tokens que representam saídas seguras de ações anteriores também mostram ícones de bloqueio. Por exemplo, quando você seleciona tal saída da lista de conteúdo dinâmico a ser usada em uma ação, esse token mostra um ícone de cadeado.

   ![Selecionar saída](media/logic-apps-securing-a-logic-app/select-secured-token.png)

1. Depois que o aplicativo lógico for executado, você poderá exibir o histórico dessa execução.

   1. No painel **visão geral** do aplicativo lógico, selecione a execução que você deseja exibir.

   1. No painel **execução do aplicativo lógico** , expanda as ações que você deseja examinar.

      Se você optar por proteger as entradas e saídas, esses valores agora aparecerão ocultos.

      ![Dados ocultos no histórico de execuções](media/logic-apps-securing-a-logic-app/hidden-data-run-history.png)

<a name="secure-data-code-view"></a>

#### <a name="secure-inputs-and-outputs-in-code-view"></a>Proteger entradas e saídas no modo de exibição de código

Na definição de gatilho ou ação subjacente, adicione ou atualize a `runtimeConfiguration.secureData.properties` matriz com um ou ambos os valores:

* `"inputs"`: Protege as entradas no histórico de execução.
* `"outputs"`: Protege as saídas no histórico de execução.

Aqui estão algumas [considerações a serem examinadas](#obfuscation-considerations) ao usar essas configurações para proteger esses dados.

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

* Quando você protege as entradas ou saídas em um gatilho ou ação, os aplicativos lógicos não enviam os dados protegidos para o Azure Log Analytics. Além disso, você não pode adicionar [Propriedades rastreadas](logic-apps-monitor-your-logic-apps.md#azure-diagnostics-event-settings-and-details) a esse gatilho ou ação para monitoramento.

* A [API dos aplicativos lógicos para manipular o histórico de fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/) não retorna saídas seguras.

* Em uma ação em que você protege entradas ou que usam explicitamente saídas protegidas e essa ação retorna uma resposta com saídas que incluem esses dados protegidos, você precisa ativar manualmente as **saídas seguras** nessa ação para proteger essas saídas.

* Certifique-se de ativar **entradas seguras** ou **proteger as saídas** em ações de downstream em que você espera que o histórico de execução proteja esses dados.

  **Configuração de saídas seguras**

  Quando você ativa manualmente as **saídas seguras** em um gatilho ou ação, os aplicativos lógicos protegem essas saídas no histórico de execuções. Se uma ação downstream usar explicitamente essas saídas protegidas como entradas, os aplicativos lógicos ocultarão as entradas dessa ação no histórico de execuções, mas *não habilitarão* a configuração de **entradas seguras** da ação.

  ![Saídas protegidas como entradas e impacto de downstream na maioria das ações](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow.png)

  As ações compor, analisar JSON e resposta têm apenas a configuração **entradas seguras** . Quando ativado, a configuração também oculta as saídas das ações. Se essas ações usarem explicitamente as saídas protegidas de upstream como entradas, os aplicativos lógicos ocultarão as entradas e saídas das ações, mas *não habilitarão* a configuração de **entradas seguras** de ações. Se uma ação downstream usar explicitamente as saídas ocultas das ações compor, analisar JSON ou resposta como entradas, os aplicativos lógicos *não ocultarão as entradas ou saídas da ação downstream*.

  ![Saídas protegidas como entradas com impacto de downstream em ações específicas](media/logic-apps-securing-a-logic-app/secure-outputs-as-inputs-flow-special.png)

  **Configuração de entradas seguras**

  Quando você ativa manualmente as **entradas seguras** em um gatilho ou ação, os aplicativos lógicos protegem essas entradas no histórico de execuções. Se uma ação downstream usar explicitamente as saídas visíveis desse gatilho ou ação como entradas, os aplicativos lógicos ocultarão as entradas dessa ação do downstream no histórico de execuções, mas *não habilitarão* **entradas seguras** nessa ação e não ocultarão essa ação produz.

  ![Entradas seguras e impacto de downstream na maioria das ações](media/logic-apps-securing-a-logic-app/secure-inputs-flow.png)

  Se as ações redigir, analisar JSON e resposta usarem explicitamente as saídas visíveis do gatilho ou da ação que tem as entradas protegidas, os aplicativos lógicos ocultarão as entradas e saídas das ações, mas *não habilitarão* as **entradas seguras** da ação configuração. Se uma ação downstream usar explicitamente as saídas ocultas das ações compor, analisar JSON ou resposta como entradas, os aplicativos lógicos *não ocultarão as entradas ou saídas da ação downstream*.

  ![Entradas seguras e impacto de downstream em ações específicas](media/logic-apps-securing-a-logic-app/secure-inputs-flow-special.png)

<a name="secure-action-parameters"></a>

## <a name="access-to-parameter-inputs"></a>Acesso a entradas de parâmetro

Se você implantar em ambientes diferentes, considere a possibilidade de parametrização dos valores na definição do fluxo de trabalho que variam de acordo com esses ambientes. Dessa forma, você pode usar um [modelo de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters) para implantar seu aplicativo lógico, proteger informações confidenciais definindo parâmetros protegidos e fornecer essas entradas de parâmetro separadamente por meio dos parâmetros do modelo usando um [parâmetro arquivo](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values).

Por exemplo, se você autenticar ações HTTP com [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), poderá definir e proteger os parâmetros que aceitam a ID do cliente e o segredo do cliente usados para autenticação. Para definir esses parâmetros para seu aplicativo lógico, use a `parameters` seção dentro da definição de fluxo de trabalho do aplicativo lógico. Para proteger os valores de parâmetro que você não deseja mostrar ao editar seu aplicativo lógico ou exibir o histórico de execução, defina os `securestring` parâmetros `secureobject` usando o tipo ou e use a codificação conforme necessário. Parâmetros que têm esse tipo não são retornados com a definição de recurso e não são acessíveis ao exibir o recurso após a implantação. Para acessar esses valores de parâmetro durante o tempo de `@parameters('<parameter-name>')` execução, use a expressão dentro de sua definição de fluxo de trabalho. Essa expressão é avaliada apenas em tempo de execução e é descrita pela [linguagem de definição de fluxo de trabalho](../logic-apps/logic-apps-workflow-definition-language.md).

> [!NOTE]
> Se você usar um parâmetro em cabeçalhos ou corpo de uma solicitação HTTP, esse parâmetro poderá ser visível quando você exibir o histórico de execução do aplicativo lógico e a solicitação HTTP de saída. Certifique-se de também definir suas políticas de acesso de conteúdo adequadamente. Os cabeçalhos de autorização nunca são visíveis por meio de entradas ou saídas. Portanto, se um segredo for usado lá, esse segredo não será recuperável.

Para obter mais informações, consulte [proteger parâmetros em definições de fluxo de trabalho](#secure-parameters-workflow) mais adiante neste tópico.

Ao automatizar implantações com [modelos de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), você pode definir parâmetros de modelo protegidos, que são avaliados na implantação, `securestring` usando `secureobject` os tipos e. Para definir parâmetros de modelo, use a seção de nível `parameters` superior do modelo, que é separada e diferente da seção da `parameters` sua definição de fluxo de trabalho. Para fornecer os valores para parâmetros de modelo, use um [arquivo de parâmetro](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)separado.

Por exemplo, se você usar segredos, poderá definir e usar parâmetros de modelo protegidos que recuperem esses segredos de [Azure Key Vault](../key-vault/key-vault-whatis.md) na implantação. Em seguida, você pode fazer referência ao cofre de chaves e ao segredo em seu arquivo de parâmetro. Para obter mais informações, consulte estes tópicos:

* [Usar Azure Key Vault para passar valores de parâmetros seguros na implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md)
* [Proteger os parâmetros em modelos de Azure Resource Manager](#secure-parameters-deployment-template) mais adiante neste tópico

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-workflow-definitions"></a>Proteger parâmetros em definições de fluxo de trabalho

Para proteger informações confidenciais na definição de fluxo de trabalho do aplicativo lógico, use parâmetros protegidos para que essas informações não fiquem visíveis depois que você salvar seu aplicativo lógico. Por exemplo, suponha que você tenha uma ação HTTP requer autenticação básica, que usa um nome de usuário e senha. Na definição de fluxo de trabalho `parameters` , a seção `basicAuthPasswordParam` define `basicAuthUsernameParam` os parâmetros e usando `securestring` o tipo. Em seguida, a definição de ação faz referência `authentication` a esses parâmetros na seção.

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

Um modelo do Resource Manager para um aplicativo lógico tem `parameters` várias seções. Para proteger senhas, chaves, segredos e outras informações confidenciais, defina parâmetros protegidos no nível do modelo e nível de definição de fluxo de trabalho `securestring` usando `secureobject` o tipo ou. Em seguida, você pode armazenar esses valores em [Azure Key Vault](../key-vault/key-vault-whatis.md) e usar o [arquivo de parâmetro](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values) para fazer referência ao cofre de chaves e ao segredo. Em seguida, o modelo recupera essas informações na implantação. Para obter mais informações, consulte [usar Azure Key Vault para passar valores de parâmetros seguros na implantação](../azure-resource-manager/resource-manager-keyvault-parameter.md).

Aqui estão mais informações sobre estas `parameters` seções:

* No nível superior do modelo, uma `parameters` seção define os parâmetros para os valores que o modelo usa na *implantação*. Por exemplo, esses valores podem incluir cadeias de conexão para um ambiente de implantação específico. Você pode armazenar esses valores em um arquivo de [parâmetro](../azure-resource-manager/resource-group-template-deploy.md#pass-parameter-values)separado, o que torna a alteração desses valores mais fácil.

* Dentro da definição de recurso do aplicativo lógico, mas fora de sua definição de `parameters` fluxo de trabalho, uma seção especifica os valores para os parâmetros da definição de fluxo de trabalho. Nesta seção, você pode atribuir esses valores usando expressões de modelo que fazem referência aos parâmetros do modelo. Essas expressões são avaliadas na implantação.

* Dentro de sua definição de fluxo `parameters` de trabalho, uma seção define os parâmetros que seu aplicativo lógico usa no tempo de execução. Em seguida, você pode fazer referência a esses parâmetros dentro do fluxo de trabalho do aplicativo lógico usando expressões de definição de fluxo de trabalho, que são avaliadas no tempo de execução.

Este modelo de exemplo que tem várias definições de parâmetros protegidas `securestring` que usam o tipo:

| Nome do parâmetro | Descrição |
|----------------|-------------|
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita uma senha que é passada para o parâmetro da `basicAuthPasswordParam` definição de fluxo de trabalho |
| `TemplatePasswordParam` | Um parâmetro de modelo que aceita um nome de usuário que é passado para o parâmetro `basicAuthUserNameParam` da definição de fluxo de trabalho |
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

Aqui estão algumas maneiras de proteger pontos de extremidade em que seu aplicativo lógico precisa de acesso para enviar solicitações:

* Adicionar autenticação em solicitações de saída.

  Quando você trabalha com uma ação HTTP, HTTP + Swagger (API aberta) ou webhook, pode adicionar autenticação à solicitação enviada pelo seu aplicativo lógico. Por exemplo, você pode usar autenticação básica, autenticação de certificado ou Azure Active Directory autenticação. Para obter mais informações, consulte [autenticar gatilhos ou ações](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

* Restringir o acesso de endereços IP do aplicativo lógico.

  Todas as chamadas para pontos de extremidade de aplicativos lógicos originam-se de endereços IP designados específicos que se baseiam nas regiões dos seus aplicativos lógicos. Você pode adicionar a filtragem que aceita solicitações somente desses endereços IP. Para obter esses endereços IP, consulte [limites e configuração para aplicativos lógicos do Azure](logic-apps-limits-and-config.md#configuration).

* Autentique e acesse recursos como identidades gerenciadas.

  Para acessar recursos em outros locatários do Azure Active Directory (AD do Azure), seu aplicativo lógico pode autenticar sua identidade sem entrar usando uma identidade gerenciada (anteriormente conhecida como Identidade de Serviço Gerenciada ou MSI), em vez de credenciais ou segredos. O Azure gerencia essa identidade para você e ajuda a proteger suas credenciais porque você não precisa fornecer ou girar segredos. Para obter mais informações sobre como configurar e usar uma identidade gerenciada atribuída pelo sistema para seu aplicativo lógico, consulte [autenticar e acessar recursos com identidades gerenciadas em aplicativos lógicos do Azure](../logic-apps/create-managed-service-identity.md).

* Proteger conexões com sistemas locais.

  Os aplicativos lógicos do Azure fornecem integração com esses serviços para comunicação local segura e confiável.

  * Gateway de dados local

    Muitos conectores gerenciados nos aplicativos lógicos do Azure fornecem conexões seguras para sistemas locais, como sistema de arquivos, SQL, SharePoint e DB2. O gateway envia dados de fontes locais em canais criptografados por meio do barramento de serviço do Azure. Todo o tráfego é originado como tráfego de saída seguro do agente de gateway. Saiba [como funciona o gateway de dados local](logic-apps-gateway-install.md#gateway-cloud-service).

  * Conectar-se por meio do gerenciamento de API do Azure

    O [Gerenciamento de API do Azure](../api-management/api-management-key-concepts.md) fornece opções de conexão locais, como a rede privada virtual site a site e a integração do ExpressRoute para o proxy seguro e a comunicação com sistemas locais. Do fluxo de trabalho do aplicativo lógico no designer do aplicativo lógico, você pode selecionar uma API que é exposta pelo gerenciamento de API, que fornece acesso rápido a sistemas locais.

## <a name="next-steps"></a>Passos Seguintes

* [Criar modelos de implantação](logic-apps-create-deploy-template.md)  
* [Monitorizar as aplicações lógicas](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosticar problemas e falhas do aplicativo lógico](logic-apps-diagnosing-failures.md)  
* [Automatizar a implantação do aplicativo lógico](logic-apps-azure-resource-manager-templates-overview.md)
