---
title: Criando um arquivo de solução de gerenciamento no Azure | Microsoft Docs
description: As soluções de gerenciamento fornecem cenários de gerenciamento empacotados que os clientes podem adicionar ao seu ambiente do Azure.  Este artigo fornece detalhes sobre como você pode criar soluções de gerenciamento para serem usadas em seu próprio ambiente ou disponibilizadas para seus clientes.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 47ee691186da7f915ca8fcf87415784ab12ef1e0
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553858"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Criando um arquivo de solução de gerenciamento no Azure (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento no Azure que estão atualmente em versão prévia. Qualquer esquema descrito abaixo está sujeito a alterações.  

As soluções de gerenciamento no Azure são implementadas como [modelos do Resource Manager](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  A principal tarefa em aprender a criar soluções de gerenciamento é aprender a [criar um modelo](../../azure-resource-manager/resource-group-authoring-templates.md).  Este artigo fornece detalhes exclusivos dos modelos usados para soluções e como configurar recursos típicos da solução.


## <a name="tools"></a>Ferramentas

Você pode usar qualquer editor de texto para trabalhar com arquivos de solução, mas é recomendável aproveitar os recursos fornecidos no Visual Studio ou Visual Studio Code conforme descrito nos artigos a seguir.

- [Criando e implantando grupos de recursos do Azure por meio do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Trabalhando com modelos de Azure Resource Manager no Visual Studio Code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>estruturá
A estrutura básica de um arquivo de solução de gerenciamento é a mesma do [modelo do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), que é o seguinte.  Cada uma das seções a seguir descreve os elementos de nível superior e seu conteúdo em uma solução.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros
[Parâmetros](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) são valores que você precisa do usuário ao instalar a solução de gerenciamento.  Há parâmetros padrão que todas as soluções terão, e você pode adicionar parâmetros adicionais conforme necessário para sua solução específica.  A forma como os usuários fornecerão valores de parâmetro quando eles instalam sua solução dependerá do parâmetro específico e de como a solução está sendo instalada.

Quando um usuário [instala sua solução de gerenciamento](solutions.md#install-a-monitoring-solution) por meio do Azure Marketplace ou modelos de início rápido do Azure, eles são solicitados a selecionar um [espaço de trabalho log Analytics e uma conta de automação](solutions.md#log-analytics-workspace-and-automation-account).  Eles são usados para preencher os valores de cada um dos parâmetros padrão.  Não é solicitado que o usuário forneça diretamente valores para os parâmetros padrão, mas eles são solicitados a fornecer valores para quaisquer parâmetros adicionais.


Um parâmetro de exemplo é mostrado abaixo.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela a seguir descreve os atributos de um parâmetro.

| Atributo | Descrição |
|:--- |:--- |
| tipo |Tipo de dados para o parâmetro. O controle de entrada exibido para o usuário depende do tipo de dados.<br><br>bool-caixa suspensa<br>Cadeia de caracteres – caixa de texto<br>int – caixa de texto<br>SecureString-campo de senha<br> |
| categoria |Categoria opcional para o parâmetro.  Os parâmetros na mesma categoria são agrupados juntos. |
| Controlo |Funcionalidade adicional para parâmetros de cadeia de caracteres.<br><br>DateTime-o controle DateTime é exibido.<br>GUID-o valor GUID é gerado automaticamente e o parâmetro não é exibido. |
| descrição |Descrição opcional para o parâmetro.  Exibido em um balão de informações ao lado do parâmetro. |

### <a name="standard-parameters"></a>Parâmetros padrão
A tabela a seguir lista os parâmetros padrão para todas as soluções de gerenciamento.  Esses valores são populados para o usuário em vez de solicitá-los quando sua solução for instalada do Azure Marketplace ou modelos de início rápido.  O usuário deverá fornecer valores para eles se a solução for instalada com outro método.

> [!NOTE]
> A interface do usuário no Azure Marketplace e modelos de início rápido estão esperando os nomes de parâmetro na tabela.  Se você usar nomes de parâmetro diferentes, o usuário será solicitado a fornecê-los e eles não serão preenchidos automaticamente.
>
>

| Parâmetro | Tipo | Descrição |
|:--- |:--- |:--- |
| accountName |string |Nome da conta de automação do Azure. |
| pricingTier |string |Tipo de preço do espaço de trabalho Log Analytics e da conta de automação do Azure. |
| regionId |string |Região da conta de automação do Azure. |
| solutionName |string |Nome da solução.  Se você estiver implantando sua solução por meio de modelos de início rápido, defina solutionName como um parâmetro para que você possa definir uma cadeia de caracteres, em vez de exigir que o usuário especifique um. |
| workspaceName |string |Nome do espaço de trabalho Log Analytics. |
| workspaceRegionId |string |Região do espaço de trabalho Log Analytics. |


A seguir está a estrutura dos parâmetros padrão que você pode copiar e colar em seu arquivo de solução.  

    "parameters": {
        "workspaceName": {
            "type": "string",
            "metadata": {
                "description": "A valid Log Analytics workspace name"
            }
        },
        "accountName": {
               "type": "string",
               "metadata": {
                   "description": "A valid Azure Automation account name"
               }
        },
        "workspaceRegionId": {
               "type": "string",
               "metadata": {
                   "description": "Region of the Log Analytics workspace"
            }
        },
        "regionId": {
            "type": "string",
            "metadata": {
                "description": "Region of the Azure Automation account"
            }
        },
        "pricingTier": {
            "type": "string",
            "metadata": {
                "description": "Pricing tier of both Log Analytics workspace and Azure Automation account"
            }
        }
    }


Você se refere a valores de parâmetro em outros elementos da solução com os **parâmetros de sintaxe (' Parameter Name ')** .  Por exemplo, para acessar o nome do espaço de trabalho, você usaria **parâmetros (' WorkspaceName ')**

## <a name="variables"></a>Variáveis
[Variáveis](../../azure-resource-manager/resource-group-authoring-templates.md#variables) são valores que serão usados no restante da solução de gerenciamento.  Esses valores não são expostos ao usuário que está instalando a solução.  Eles se destinam a fornecer ao autor um único local onde podem gerenciar valores que podem ser usados várias vezes em toda a solução. Você deve colocar quaisquer valores específicos à sua solução em variáveis, em vez de codificá-los de forma rígida no elemento **Resources** .  Isso torna o código mais legível e permite que você altere esses valores facilmente em versões posteriores.

Veja a seguir um exemplo de um elemento **Variables** com parâmetros típicos usados em soluções.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Você se refere a valores de variáveis por meio da solução com as variáveis de sintaxe **(' nome da variável ')** .  Por exemplo, para acessar a variável SolutionName, você usaria **variáveis (' SolutionName ')** .

Você também pode definir variáveis complexas que contenham vários conjuntos de valores.  Elas são particularmente úteis em soluções de gerenciamento nas quais você está definindo várias propriedades para diferentes tipos de recursos.  Por exemplo, você pode reestruturar as variáveis da solução mostradas acima para o seguinte.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Nesse caso, você se refere a valores de variáveis por meio da solução com as variáveis de sintaxe **(' variable name '). Property**.  Por exemplo, para acessar a variável nome da solução, você usaria **variáveis (' Solution '). Nome**.

## <a name="resources"></a>Recursos
Os [recursos](../../azure-resource-manager/resource-group-authoring-templates.md#resources) definem os diferentes recursos que sua solução de gerenciamento instalará e configurará.  Essa será a parte maior e mais complexa do modelo.  Você pode obter a estrutura e a descrição completa dos elementos de recurso na [criação de modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Recursos diferentes que você normalmente definirão são detalhados em outros artigos nesta documentação. 


### <a name="dependencies"></a>Dependências
O elemento **dependy** especifica uma [dependência](../../azure-resource-manager/resource-group-define-dependencies.md) em outro recurso.  Quando a solução é instalada, um recurso não é criado até que todas as suas dependências tenham sido criadas.  Por exemplo, sua solução pode [Iniciar um runbook](solutions-resources-automation.md#runbooks) quando ele é instalado usando um [recurso de trabalho](solutions-resources-automation.md#automation-jobs).  O recurso de trabalho seria dependente do recurso de runbook para garantir que o runbook seja criado antes de o trabalho ser criado.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics o espaço de trabalho e a conta de automação
As soluções de gerenciamento exigem um [espaço de trabalho log Analytics](../../azure-monitor/platform/manage-access.md) para conter exibições e uma [conta de automação](../../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados.  Eles devem estar disponíveis antes de os recursos na solução serem criados e não devem ser definidos na própria solução.  O usuário [especificará um espaço de trabalho e uma conta](solutions.md#log-analytics-workspace-and-automation-account) ao implantar sua solução, mas como o autor, você deve considerar os pontos a seguir.


## <a name="solution-resource"></a>Recurso de solução
Cada solução requer uma entrada de recurso no elemento **Resources** que define a solução em si.  Isso terá um tipo de **Microsoft. OperationsManagement/Solutions** e ter a estrutura a seguir. Isso inclui [parâmetros](#parameters) e [variáveis](#variables) padrão que normalmente são usados para definir as propriedades da solução.


    {
      "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
      "location": "[parameters('workspaceRegionId')]",
      "tags": { },
      "type": "Microsoft.OperationsManagement/solutions",
      "apiVersion": "[variables('LogAnalyticsApiVersion')]",
      "dependsOn": [
        <list-of-resources>
      ],
      "properties": {
        "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaceName'))]",
        "referencedResources": [
            <list-of-referenced-resources>
        ],
        "containedResources": [
            <list-of-contained-resources>
        ]
      },
      "plan": {
        "name": "[concat(variables('Solution').Name, '[' ,parameters('workspaceName'), ']')]",
        "Version": "[variables('Solution').Version]",
        "product": "[variables('ProductName')]",
        "publisher": "[variables('Solution').Publisher]",
        "promotionCode": ""
      }
    }




### <a name="dependencies"></a>Dependências
O recurso de solução deve ter uma [dependência](../../azure-resource-manager/resource-group-define-dependencies.md) em todos os outros recursos na solução, já que eles precisam existir antes que a solução possa ser criada.  Você faz isso adicionando uma entrada para cada recurso no elemento **depende** .

### <a name="properties"></a>Propriedades
O recurso de solução tem as propriedades na tabela a seguir.  Isso inclui os recursos referenciados e contidos na solução que define como o recurso é gerenciado depois que a solução é instalada.  Cada recurso na solução deve ser listado na propriedade **referencedResources** ou **containedResources** .

| Propriedade | Descrição |
|:--- |:--- |
| workspaceResourceId |ID do espaço de trabalho Log Analytics no formato *\<Resource ID do grupo >/providers/Microsoft.OperationalInsights/workspaces/\<Workspace nome \>* . |
| referencedResources |Lista de recursos na solução que não devem ser removidos quando a solução é removida. |
| containedResources |Lista de recursos na solução que devem ser removidos quando a solução é removida. |

O exemplo acima é para uma solução com um runbook, uma agenda e uma exibição.  O agendamento e o runbook são *referenciados* no elemento **Properties** para que não sejam removidos quando a solução for removida.  A exibição está *contida* para que seja removida quando a solução for removida.

### <a name="plan"></a>Plano
A entidade de **plano** do recurso de solução tem as propriedades na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| nome |Nome da solução. |
| versão |Versão da solução como determinada pelo autor. |
| produto |Cadeia de caracteres exclusiva para identificar a solução. |
| Programa |Editor da solução. |



## <a name="next-steps"></a>Passos seguintes
* [Adicionar alertas e pesquisas salvas](solutions-resources-searches-alerts.md) à sua solução de gerenciamento.
* [Adicione exibições](solutions-resources-views.md) à sua solução de gerenciamento.
* [Adicione runbooks e outros recursos de automação](solutions-resources-automation.md) à sua solução de gerenciamento.
* Conheça os detalhes da [criação de modelos de Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquise [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) para obter exemplos de modelos diferentes do Resource Manager.
