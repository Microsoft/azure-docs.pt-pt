---
title: Criar um ficheiro de solução de gestão no Azure | Documentos da Microsoft
description: Soluções de gerenciamento fornecem os cenários de pacote de gestão que os clientes podem adicionar ao seu ambiente do Azure.  Este artigo fornece detalhes sobre como pode criar soluções de gestão para ser usado em seu próprio ambiente ou disponibilizada aos seus clientes.
services: monitoring
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: monitoring
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0e2f6a8bd8bc8e6f0dd8ba1ec642bff50c9a7977
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56984038"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Criar um ficheiro de solução de gestão no Azure (pré-visualização)
> [!NOTE]
> Esta é a documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrita abaixo está sujeitas a alterações.  

Soluções de gestão do Azure são implementadas como [modelos do Resource Manager](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md).  A principal tarefa em aprender a criar soluções de gestão é aprender como [criar um modelo](../../azure-resource-manager/resource-group-authoring-templates.md).  Este artigo fornece detalhes exclusivos de modelos utilizados para soluções e como configurar recursos de solução típica.


## <a name="tools"></a>Ferramentas

Pode utilizar qualquer editor de texto para trabalhar com arquivos de solução, mas recomendamos aproveitando os recursos fornecidos no Visual Studio ou o Visual Studio Code, conforme descrito nos seguintes artigos.

- [Criar e implementar grupos de recursos do Azure através do Visual Studio](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)
- [Trabalhar com modelos Azure Resource Manager no Visual Studio Code](../../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>estrutura
A estrutura básica de um ficheiro de solução de gestão é o mesmo que um [modelo do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#template-format), que é o seguinte.  Cada uma das secções abaixo descreve os elementos de nível superior e seu conteúdo numa solução.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros
[Parâmetros](../../azure-resource-manager/resource-group-authoring-templates.md#parameters) são valores que exigem do usuário durante a instalação da solução de gestão.  Existem parâmetros padrão, que terão todas as soluções e pode adicionar parâmetros adicionais conforme necessário para a sua solução específica.  Como os utilizadores irão fornecer valores de parâmetros durante a instalação sua solução dependerá o parâmetro específico e como a solução está a ser instalada.

Quando um usuário [instala a sua solução de gestão](solutions.md#install-a-monitoring-solution) através de modelos do Azure Marketplace ou do início rápido do Azure, são-lhe pedido para selecionar um [área de trabalho do Log Analytics e a conta de automatização](solutions.md#log-analytics-workspace-and-automation-account).  Estes são utilizados para preencher os valores de cada um dos parâmetros padrão.  Não é pedido ao utilizador diretamente fornecer valores para os parâmetros padrão, mas são lhe for pedidos para fornecer valores para todos os parâmetros adicionais.


Um parâmetro de exemplo é mostrado abaixo.  

    "startTime": {
        "type": "string",
        "metadata": {
            "description": "Enter time for starting VMs by resource group.",
            "control": "datetime",
            "category": "Schedule"
        }

A tabela seguinte descreve os atributos de um parâmetro.

| Atributo | Descrição |
|:--- |:--- |
| tipo |Tipo de dados para o parâmetro. O controlo de entrada apresentado para o utilizador depende do tipo de dados.<br><br>bool - caixa pendente<br>cadeia de caracteres - caixa de texto<br>Int - caixa de texto<br>SecureString - campo palavra-passe<br> |
| categoria |Categoria opcional para o parâmetro.  Parâmetros da mesma categoria são agrupados em conjunto. |
| Controlo |Funcionalidades adicionais para os parâmetros de cadeia de caracteres.<br><br>DateTime - controle de Datetime é apresentado.<br>GUID - valor Guid é gerado automaticamente e o parâmetro não é apresentado. |
| descrição |Descrição opcional para o parâmetro.  Apresentado num balão de informações junto do parâmetro. |

### <a name="standard-parameters"></a>Parâmetros padrão
A tabela seguinte lista os parâmetros padrão para todas as soluções de gestão.  Estes valores são preenchidos para o utilizador em vez de solicitar para os mesmos, quando a sua solução é instalada a partir do Azure Marketplace ou modelos de início rápido.  O utilizador tem de fornecer valores para os mesmos se a solução estiver instalada com outro método.

> [!NOTE]
> A interface de utilizador no Azure Marketplace e modelos de início rápido está à espera os nomes dos parâmetros na tabela.  Se utilizar os nomes de parâmetros diferentes, em seguida, será pedido ao utilizador para os mesmos e eles não estarão automaticamente preenchidos.
>
>

| Parâmetro | Type | Descrição |
|:--- |:--- |:--- |
| accountName |string |Nome da conta de automatização do Azure. |
| pricingTier |string |Escalão de preço da área de trabalho do Log Analytics e a conta de automatização do Azure. |
| regionId |string |Região da conta de automatização do Azure. |
| solutionName |string |Nome da solução.  Se estiver a implementar sua solução por meio de modelos de início rápido, em seguida, deve definir solutionName como um parâmetro para que pode definir uma cadeia de caracteres em vez disso, exigir que o utilizador especifique um. |
| workspaceName |string |Nome de área de trabalho do log Analytics. |
| workspaceRegionId |string |Região da área de trabalho do Log Analytics. |


Segue-se a estrutura dos parâmetros padrão que pode copiar e colar no seu arquivo de solução.  

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


Consulte a valores de parâmetro em outros elementos da solução com a sintaxe **parâmetros ('nome do parâmetro')**.  Por exemplo, para acessar o nome de área de trabalho, usaria **parameters('workspaceName')**

## <a name="variables"></a>Variáveis
[Variáveis](../../azure-resource-manager/resource-group-authoring-templates.md#variables) são valores que irá utilizar no restante da solução de gestão.  Estes valores não são expostos ao utilizador instalar a solução.  Eles têm a finalidade de fornecer o autor com uma única localização onde é possível gerenciar os valores que podem ser utilizadas várias vezes em toda a solução. Deve colocar todos os valores específicos à sua solução em variáveis em vez de pré-programá-la-os na **recursos** elemento.  Isso torna o código mais legível e permite-lhe facilmente alterar estes valores nas versões posteriores.

Segue-se um exemplo de um **variáveis** elemento com parâmetros comuns utilizados em soluções.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Consulte os valores das variáveis através da solução com a sintaxe **variáveis ("nome da variável")**.  Por exemplo, para aceder à variável SolutionName, usaria **variables('SolutionName')**.

Também pode definir variáveis de complexo esse múltiplo de conjuntos de valores.  Estes são particularmente úteis em soluções de gestão onde está a definir várias propriedades para diferentes tipos de recursos.  Por exemplo, pode reestruturar as variáveis de solução mostradas acima para o seguinte.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Neste caso, consultar os valores das variáveis através da solução com a sintaxe **variables('variable name').property**.  Por exemplo, para acessar a variável do nome da solução, usaria **variables('Solution'). Nome**.

## <a name="resources"></a>Recursos
[Recursos](../../azure-resource-manager/resource-group-authoring-templates.md#resources) definem os recursos diferentes que sua solução de gestão irão instalar e configurar.  Esta será a parte maior e mais complexa do modelo.  Pode obter a estrutura e a descrição completa dos elementos de recurso na [modelos Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md#resources).  Recursos diferentes que, normalmente, irá definir são detalhados em outros artigos nesta documentação. 


### <a name="dependencies"></a>Dependências
O **dependsOn** elemento Especifica um [dependência](../../azure-resource-manager/resource-group-define-dependencies.md) outro recurso.  Quando a solução é instalada, um recurso não é criado até que todas as dependências foram criadas.  Por exemplo, talvez a sua solução [iniciar um runbook](solutions-resources-automation.md#runbooks) quando é instalado com uma [recursos da tarefa](solutions-resources-automation.md#automation-jobs).  O recurso de tarefa seria dependente do recurso de runbook para se certificar de que o runbook é criado antes da tarefa é criada.

### <a name="log-analytics-workspace-and-automation-account"></a>Área de trabalho do log Analytics e a conta de automatização
Soluções de gestão requerem uma [área de trabalho do Log Analytics](../../azure-monitor/platform/manage-access.md) para conter as vistas e um [conta de automatização](../../automation/automation-security-overview.md#automation-account-overview) para conter runbooks e recursos relacionados.  Estes têm de estar disponíveis antes dos recursos da solução são criados e não devem ser definidos na solução em si.  O utilizador irá [especifique uma área de trabalho e conta](solutions.md#log-analytics-workspace-and-automation-account) quando implementar a sua solução, mas como o autor deve considerar os seguintes pontos.


## <a name="solution-resource"></a>Recursos de solução
Cada solução requer uma entrada de recurso no **recursos** elemento que define a solução em si.  Isto irá ter um tipo de **Microsoft.OperationsManagement/solutions** e ter a seguinte estrutura. Isto inclui [parâmetros padrão](#parameters) e [variáveis](#variables) que são normalmente utilizadas para definir propriedades da solução.


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
O recurso de solução tem de ter uma [dependência](../../azure-resource-manager/resource-group-define-dependencies.md) em todos os outros recursos da solução, uma vez que eles têm de existir antes da solução pode ser criada.  Fazer isso adicionando uma entrada para cada recurso o **dependsOn** elemento.

### <a name="properties"></a>Propriedades
O recurso de solução tem as propriedades na tabela seguinte.  Isto inclui os recursos referenciados e contidas pela solução que define como o recurso é gerido depois da solução está instalada.  Cada recurso na solução deverá ser listado em ambos os **referencedResources** ou o **containedResources** propriedade.

| Propriedade | Descrição |
|:--- |:--- |
| workspaceResourceId |ID da área de trabalho do Log Analytics no formulário  *<Resource Group ID>/providers/Microsoft.OperationalInsights/workspaces/\<nome da área de trabalho\>*. |
| referencedResources |Lista de recursos na solução que não devem ser removidos quando a solução é removida. |
| containedResources |Lista de recursos na solução que devem ser removidos quando a solução é removida. |

O exemplo acima é uma solução com um runbook, uma agenda e o modo de exibição.  A agenda e um runbook são *referenciado* no **propriedades** , de modo que eles não são removidos quando a solução é removida.  A visualização é *contidos* para que este é removido quando a solução é removida.

### <a name="plan"></a>Planear
O **plano** entidade do recurso de solução tem as propriedades na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| nome |Nome da solução. |
| versão |Versão da solução conforme determinado pelo autor. |
| produto |Cadeia de caracteres exclusiva para identificar a solução. |
| publicador |Editor da solução. |



## <a name="next-steps"></a>Passos Seguintes
* [Adicionar pesquisas guardadas e alertas](solutions-resources-searches-alerts.md) à sua solução de gestão.
* [Adicionar vistas](solutions-resources-views.md) à sua solução de gestão.
* [Adicionar runbooks e outros recursos de automatização](solutions-resources-automation.md) à sua solução de gestão.
* Saiba os detalhes de [modelos Authoring Azure Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquisa [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) exemplos dos diferentes modelos do Resource Manager.
