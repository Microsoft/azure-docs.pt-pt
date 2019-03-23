---
title: Proteger o acesso ao Azure Logic Apps | Documentos da Microsoft
description: Adicionar segurança para o Azure Logic Apps, incluindo acionadores, entradas e saídas, parâmetros e outros serviços
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 6f878e05255be8f65868d8d1dcdbacb3ccfa2729
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369943"
---
# <a name="secure-access-in-azure-logic-apps"></a>Proteger o acesso no Azure Logic Apps

Aqui estão os elementos na sua aplicação lógica em que pode proteger o acesso:

* [Acionadores de pedido ou Webhook](#secure-triggers)
* [Operações, tais como gerir, editar ou visualizar](#secure-operations) a aplicação lógica
* [Entradas e saídas](#secure-run-history) histórico de execução da sua aplicação lógica
* [Parâmetros de ação e de entradas](#secure-action-parameters)
* [Serviços que obtém pedidos](#secure-requests) da sua aplicação lógica

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>Acesso seguro aos acionadores de pedido

Quando a aplicação lógica utiliza um acionador com base no pedido HTTP, tais como o [solicitação](../connectors/connectors-native-reqres.md) ou [Webhook](../connectors/connectors-native-webhook.md) acionador, pode restringir o acesso para os clientes autorizados apenas podem começar a sua aplicação lógica. Todos os pedidos recebidos por uma aplicação lógica são encriptados e protegidos com o protocolo Secure Sockets Layer (SSL). Seguem-se pode proteger o acesso a este tipo de Acionador de formas diferentes:

* [Gerar assinaturas de acesso partilhado](#sas)
* [Restringir os endereços IP recebidos](#restrict-incoming-ip-addresses)
* [Adicionar Azure Active Directory, OAuth ou outra segurança](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>Gerar assinaturas de acesso partilhado

Cada ponto de extremidade do pedido numa aplicação lógica inclui um [assinatura de acesso partilhado (SAS)](../storage/common/storage-dotnet-shared-access-signature-part-1.md) no URL do ponto de extremidade. Cada URL contém um `sp`, `sv`, e `sig` parâmetro de consulta:

* `sp` Especifica as permissões, que mapeiam para os métodos HTTP permitidos para utilização.
* `sv` Especifica a versão utilizada para gerar a assinatura.
* `sig` é utilizado para autenticar o acesso ao acionador.

A assinatura é gerada usando o algoritmo SHA256 com uma chave de acesso a segredos em todos os caminhos de URL e propriedades. A chave secreta nunca é exposta ou publicada e são mantidos encriptados e armazenados com a aplicação lógica. A aplicação lógica autoriza apenas esses disparadores que contêm uma assinatura válida criada com a chave secreta. 

Aqui estão mais algumas informações sobre como proteger o acesso com a assinatura de acesso partilhado:

* [Voltar a gerar chaves de acesso](#access-keys)
* [Criar os URLs de retorno de chamada prestes a expirar](#expiring-URLs)
* [Criar URLs com a chave primária ou secundária](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>Voltar a gerar chaves de acesso

Para voltar a gerar uma nova chave de acesso seguro em qualquer altura, utilize a API REST do Azure ou o portal do Azure. Todos os gerado anteriormente URLs que utilize a chave antiga são também invalidados e já não estão autorizados a acionar a aplicação lógica. Os URLs que recuperar após a regeneração tem sessão iniciada com a nova chave de acesso.

1. No portal do Azure, abra a aplicação lógica com a chave que pretende regenerar.

1. No menu da aplicação lógica, sob **configurações**, selecione **chaves de acesso**.

1. Selecione a chave que pretende voltar a gerar e concluir o processo.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>Criar os URLs de retorno de chamada com datas de expiração

Se partilhar URL de um acionador com base no pedido de ponto final com outras partes, pode gerar URLs de retorno de chamada com chaves específicas e datas de expiração conforme necessário. Pode, em seguida, perfeitamente distribuir chaves ou restringir o acesso para acionar a sua aplicação lógica para um intervalo de tempo específico. Pode especificar uma data de expiração para um URL com o [API de REST do Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, incluem o `NotAfter`propriedade usando um JSON de cadeia de caracteres de data. Essa propriedade retorna um URL de retorno de chamada que é válido apenas até o `NotAfter` data e hora.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>Criar URLs com a chave secreta primária ou secundária

Quando gerar ou URLs de retorno de chamada de lista de acionadores com base no pedido, também pode especificar a chave a utilizar para o URL de assinatura. Pode gerar um URL que esteja assinado por uma chave específica ao utilizar o [API de REST do Logic Apps](https://docs.microsoft.com/rest/api/logic/workflowtriggers), por exemplo:

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

No corpo, incluem os `KeyType` propriedade como um `Primary` ou `Secondary`. Essa propriedade retorna uma URL que esteja assinada pelo nome da chave segura.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>Restringir os endereços IP recebidos

Juntamente com a assinatura de acesso partilhado, pode querer limitar clientes específicos que podem chamar a sua aplicação lógica.  
Por exemplo, se gerencia o ponto final do pedido com a API Management do Azure, pode restringir a sua aplicação lógica para aceitar pedidos apenas a partir de endereço IP da instância de gestão de API. 

#### <a name="set-ip-ranges---azure-portal"></a>Definir intervalos IP - portal do Azure

Para configurar esta restrição no portal do Azure, aceda às definições da sua aplicação lógica: 

1. No portal do Azure, abra a aplicação lógica no Estruturador da aplicação lógica. 

1. No menu da sua aplicação lógica, sob **configurações**, selecione **definições de fluxo de trabalho**.

1. Sob **configuração do controlo de acesso** > 
**entrados endereços IP permitidos**, selecione **intervalos IP específicos**.

1. Sob **intervalos de IP para acionadores**, especifique os intervalos de endereços IP que aceita o acionador. Um intervalo IP válido usa esses formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x* 

Se pretender que a aplicação lógica são disparados apenas como uma aplicação lógica aninhada, a partir da **permitidos endereços IP de receção** lista, selecione **apenas outras aplicações lógicas**. Esta opção escreve uma matriz vazia para o recurso de aplicação lógica, para que somente chamadas de serviço do Logic Apps (aplicações de lógica principal) podem acionar a aplicação lógica aninhada.

> [!NOTE]
> Independentemente do endereço IP, pode continuar a executar uma aplicação lógica que tem um acionador com base no pedido com `/triggers/{triggerName}/run` através da API de REST do Azure ou através da gestão de API. No entanto, este cenário ainda requer a autenticação com base na API de REST do Azure e todos os eventos são apresentados no registo de auditoria do Azure. Certifique-se de que definir políticas de controlo de acesso em conformidade.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>Definir intervalos IP - modelo de implementação de aplicação lógica

Se estiver a automatizar implementações de aplicações lógicas através de um [modelo de implementação Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), pode definir os intervalos de IP nesse modelo, por exemplo:

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Adicionar Azure Active Directory, OAuth ou outra segurança

Para adicionar protocolos de autorização mais a sua aplicação lógica, considere usar [gestão de API do Azure](https://azure.microsoft.com/services/api-management/). Este serviço oferece a monitorização avançada, segurança, a política e a documentação para qualquer ponto final e dá-lhe a capacidade de expor a sua aplicação lógica como uma API. Gestão de API pode expor um ponto de extremidade público ou privado para a sua aplicação lógica, que, em seguida, pode utilizar o Azure Active Directory, OAuth, certificado ou outros padrões de segurança. Quando a gestão de API recebe um pedido, o serviço envia o pedido à sua aplicação lógica, também efetuar quaisquer transformações necessárias ou restrições ao longo do caminho. Para permitir que apenas a gestão de API acionar a sua aplicação lógica, pode utilizar definições de intervalo IP recebidas da sua aplicação lógica. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>Proteger o acesso às operações de aplicação lógica

Para permitir que apenas utilizadores específicos ou grupos de executar as operações na sua aplicação lógica, pode restringir o acesso em tarefas, como gerenciamento, edição e exibição. O Logic Apps suporta [controlo de acesso de controlo (RBAC)](../role-based-access-control/role-assignments-portal.md), que pode personalizar ou atribuir funções incorporadas para membros na sua subscrição, por exemplo:

* **Contribuidor da aplicação lógica**: Os utilizadores podem ver, editar e atualizar a sua aplicação lógica. Esta função não é possível eliminar a aplicação lógica ou executar operações de administrador.
* **Operador de aplicação lógica**: Os utilizadores podem ver a aplicação lógica e o histórico de execuções e ativar ou desativar a sua aplicação lógica. Esta função não pode editar ou atualizar a sua aplicação lógica.

Para impedir que outros utilizadores de alterar ou eliminar a aplicação lógica, pode utilizar [bloqueio de recursos do Azure](../azure-resource-manager/resource-group-lock-resources.md). Esta capacidade ajuda a impedir que outras pessoas de alterar ou eliminar recursos de produção.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>Proteger o acesso ao histórico de execução da aplicação lógica

Para proteger conteúdo transmitido como entradas ou saídas de execuções de aplicação lógica anteriores, pode restringir o acesso a intervalos de endereços IP específicos. Esse recurso oferece mais controle de acesso. Todos os dados em execução de uma aplicação lógica são encriptados durante o trânsito e em inatividade. Quando solicita o histórico de execução de uma aplicação lógica, o Logic Apps autentica esse pedido e fornece ligações para as entradas e saídas das solicitações e respostas no fluxo de trabalho da sua aplicação lógica. Pode proteger estas ligações para que apenas pedidos de um endereço IP específico retornam esse conteúdo. Por exemplo, pode até especificar um endereço IP como `0.0.0.0-0.0.0.0` para que ninguém pode acessar as entradas e saídas. Apenas uma pessoa com permissões de administrador pode remover esta restrição, fornecendo a possibilidade de acesso de "just-in-time" conteúdos da sua aplicação lógica.

### <a name="set-ip-ranges---azure-portal"></a>Definir intervalos IP - portal do Azure

Para configurar esta restrição no portal do Azure, aceda às definições da sua aplicação lógica:

1. No portal do Azure, abra a aplicação lógica no Estruturador da aplicação lógica. 

1. No menu da sua aplicação lógica, sob **configurações**, selecione **definições de fluxo de trabalho**.

1. Sob **configuração do controlo de acesso** > 
    **entrados endereços IP permitidos**, selecione **intervalos IP específicos**.

1. Sob **intervalos de IP para conteúdos**, especifique os intervalos de endereços IP que podem aceder a conteúdo de entradas e saídas. 
   Um intervalo IP válido usa esses formatos: *x.x.x.* ou *x.x.x. x-x.x.x. x* 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>Definir intervalos IP - modelo de implementação de aplicação lógica

Se estiver a automatizar implementações de aplicações lógicas através de um [modelo de implementação Azure Resource Manager](../logic-apps/logic-apps-create-deploy-template.md), pode definir os intervalos de IP nesse modelo, por exemplo:

``` json
{
   "properties": {
      "definition": {},
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
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>Parâmetros de ação e entradas de seguros

Quando implementar em vários ambientes, pode querer parametrizar os elementos específicos na definição de fluxo de trabalho da sua aplicação lógica. Dessa forma, pode fornecer entradas com base em ambientes de usar e proteger informações confidenciais. Por exemplo, se estiver a autenticar ações de HTTP com [do Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication), definir e proteger os parâmetros que aceitam o ID de cliente e o segredo de cliente utilizado para autenticação. Para estes parâmetros, a definição da aplicação lógica tem o seu próprio `parameters` secção.
Para aceder a valores de parâmetro durante o tempo de execução, pode utilizar o `@parameters('parameterName')` expressão, que é fornecido pela [linguagem de definição de fluxo de trabalho](https://aka.ms/logicappsdocs). 

Para proteger os parâmetros e valores, não quer mostrado ao editar a sua aplicação lógica ou o histórico de visualização de executar, pode definir os parâmetros com o `securestring` escreva e utilize a codificação conforme necessário. Parâmetros que têm este tipo não são devolvidos com a definição do recurso e não estão acessíveis ao visualizar o recurso após a implementação.

> [!NOTE]
> Se utilizar um parâmetro em cabeçalhos de um pedido ou corpo, esse parâmetro pode ser visível ao aceder ao histórico de execução da sua aplicação lógica e o pedido de HTTP de saída. Certifique-se de que também definir as políticas de acesso ao conteúdo em conformidade.
> Cabeçalhos de autorização nunca são visíveis através de entradas ou saídas. Portanto, se um segredo é utilizado aqui, esse segredo não é recuperável.

Para obter mais informações sobre como proteger a parâmetros na lógica de definições de aplicação, consulte [Secure parâmetros na lógica de definições de aplicação](#secure-parameters-workflow) posteriormente nesta página.

Se estiver a automatizar Implantações com o [modelos de implementação Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md#parameters), também pode utilizar o protegido parâmetros nesses modelos. Por exemplo, pode usar parâmetros para obter segredos do Cofre de chaves ao criar a sua aplicação lógica. A definição de modelo de implementação tem a sua própria `parameters` secção, separada da sua aplicação lógica `parameters` secção. Para obter mais informações sobre como proteger a parâmetros nos modelos de implementação, consulte [Secure parâmetros nos modelos de implementação](#secure-parameters-deployment-template) posteriormente nesta página.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>Proteger os parâmetros na lógica de definições de aplicação

Para proteger informações confidenciais em sua definição de fluxo de trabalho da aplicação de lógica, utilize parâmetros seguros para que estas informações não estão visíveis depois de guardar a aplicação lógica. Por exemplo, suponha que estiver usando `Basic` autenticação numa definição de ação de HTTP. Este exemplo inclui um `parameters` seção que define os parâmetros para a definição de ação além de um `authentication` secção que aceita `username` e `password` valores de parâmetros. Para fornecer valores para estes parâmetros, pode utilizar um ficheiro de parâmetros separados, por exemplo:

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
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
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

Se utilizar segredos, pode obter os segredos no momento da implantação usando [Cofre de chaves do Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Proteger os parâmetros de modelos de implementação Azure Resource Manager

Este exemplo mostra um modelo de implementação do Resource Manager que utiliza mais do que um parâmetro de tempo de execução com o `securestring` tipo:

* `armTemplatePasswordParam`, que é a entrada para a definição de aplicação de lógica `logicAppWfParam` parâmetro

* `logicAppWfParam`, que é a entrada para a ação de HTTP usando a autenticação básica

Este exemplo inclui uma interna `parameters` seção, que pertence a definição de fluxo de trabalho da sua aplicação lógica e uma externa `parameters` seção, que pertence ao seu modelo de implementação. Para especificar os valores de ambiente para parâmetros, pode utilizar um ficheiro de parâmetros separados. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
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
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
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
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
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
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

Se utilizar segredos, pode obter os segredos no momento da implantação usando [Cofre de chaves do Azure Resource Manager](../azure-resource-manager/resource-manager-keyvault-parameter.md).

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>Proteger o acesso aos serviços que recebem pedidos

Aqui estão algumas formas a que se pode proteger qualquer ponto de extremidade em que a aplicação lógica precisa de acesso e envia pedidos.

### <a name="add-authentication-on-outbound-requests"></a>Adicionar a autenticação nos pedidos de saída

Ao trabalhar com um HTTP, HTTP + Swagger (API aberta) ou ação do Webhook, pode adicionar autenticação à solicitação enviada pela sua aplicação lógica. Por exemplo, pode usar autenticação básica, a autenticação de certificado ou a autenticação do Azure Active Directory. Para obter mais informações, consulte [autenticar ações ou acionadores](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication).

### <a name="restrict-access-to-logic-app-ip-addresses"></a>Restringir o acesso a endereços IP de aplicação lógica

Todas as chamadas de aplicações lógicas são provenientes dos endereços IP designados específicos com base na região. Pode adicionar filtragem que aceite pedidos apenas a partir desses endereços IP. Para esses endereços IP, consulte [limites e configuração para o Azure Logic Apps](logic-apps-limits-and-config.md#configuration).

### <a name="secure-on-premises-connectivity"></a>Proteger a conectividade no local

O Azure Logic Apps fornece integração com estes serviços para seguras e fiáveis no local comunicação.

#### <a name="on-premises-data-gateway"></a>Gateway de dados no local

Muitos conetores geridos para o Azure Logic Apps fornecem ligações seguras a sistemas no local, como o sistema de ficheiros, SQL, SharePoint, DB2 e outros. O gateway envia dados de origens no local em canais encriptados através do Azure Service Bus. Todo o tráfego de origem como proteger o tráfego de saída do agente do gateway. Saiba mais [como funciona o gateway de dados no local](logic-apps-gateway-install.md#gateway-cloud-service).

#### <a name="azure-api-management"></a>API Management do Azure

[Gestão de API do Azure](https://azure.microsoft.com/services/api-management/) fornece opções de ligação no local, como o site para site rede privada virtual e a integração do ExpressRoute para o proxy segura e comunicação para sistemas no local. No Estruturador da aplicação lógica, pode selecionar uma API exposta pela gestão de API do fluxo de trabalho da sua aplicação lógica, fornecendo acesso rápido a sistemas no local.

## <a name="next-steps"></a>Passos Seguintes

* [Criar um modelo de implementação](logic-apps-create-deploy-template.md)  
* [Processamento de exceções](logic-apps-exception-handling.md)  
* [Monitorizar as aplicações lógicas](logic-apps-monitor-your-logic-apps.md)  
* [Diagnosticar problemas e falhas de aplicação lógica](logic-apps-diagnosing-failures.md)  
