---
title: Criação de um ficheiro de solução de gestão no Azure Microsoft Docs
description: As soluções de gestão proporcionam cenários de gestão embalados que os clientes podem adicionar ao seu ambiente Azure.  Este artigo fornece detalhes sobre como pode criar soluções de gestão para ser usado no seu próprio ambiente ou disponibilizado aos seus clientes.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/09/2018
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 999177f821b98adfa015520252bd3323d0892533
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275181"
---
# <a name="creating-a-management-solution-file-in-azure-preview"></a>Criação de um ficheiro de solução de gestão em Azure (Pré-visualização)
> [!NOTE]
> Esta é uma documentação preliminar para a criação de soluções de gestão no Azure que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.  

As soluções de gestão em Azure são implementadas como modelos de [Gestor de Recursos.](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)  A principal tarefa na aprendizagem de soluções de gestão de autores é aprender a [autorar um modelo.](../../azure-resource-manager/templates/template-syntax.md)  Este artigo fornece detalhes únicos de modelos usados para soluções e como configurar recursos típicos de solução.


## <a name="tools"></a>Ferramentas

Pode utilizar qualquer editor de texto para trabalhar com ficheiros de soluções, mas recomendamos alavancar as funcionalidades fornecidas no Visual Studio ou visual Studio Code, conforme descrito nos seguintes artigos.

- [Criar e implementar grupos de recursos do Azure através do Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md)
- [Trabalhar com Modelos do Azure Resource Manager no Visual Studio Code](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)




## <a name="structure"></a>Estrutura
A estrutura básica de um ficheiro de solução de gestão é a mesma que um Modelo de Gestor de [Recursos,](../../azure-resource-manager/templates/template-syntax.md#template-format)que é o seguinte.  Cada uma das secções abaixo descreve os elementos de nível superior e o seu conteúdo numa solução.  

    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0",
       "parameters": {  },
       "variables": {  },
       "resources": [  ],
       "outputs": {  }
    }

## <a name="parameters"></a>Parâmetros
[Os parâmetros](../../azure-resource-manager/templates/template-syntax.md#parameters) são valores que o utilizador necessita quando instalam a solução de gestão.  Existem parâmetros padrão que todas as soluções terão, e pode adicionar parâmetros adicionais conforme necessário para a sua solução específica.  A forma como os utilizadores fornecerão valores de parâmetros quando instalarem a sua solução dependerá do parâmetro específico e da forma como a solução está a ser instalada.

Quando um utilizador [instala a sua solução](solutions.md#install-a-monitoring-solution) de gestão através dos modelos Azure Marketplace ou Azure QuickStart, é solicitado a selecionar um espaço de trabalho de Log Analytics e uma conta de [Automação](solutions.md#log-analytics-workspace-and-automation-account).  Estes são usados para povoar os valores de cada um dos parâmetros padrão.  O utilizador não é solicitado a fornecer diretamente valores para os parâmetros padrão, mas é solicitado a fornecer valores para quaisquer parâmetros adicionais.


Um parâmetro de amostra é mostrado abaixo.  

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
| tipo |Tipo de dados para o parâmetro. O controlo de entrada apresentado para o utilizador depende do tipo de dados.<br><br>bool - Caixa de descida<br>cadeia - Caixa de texto<br>int - Caixa de texto<br>string de segurança - Campo de senha<br> |
| categoria |Categoria opcional para o parâmetro.  Os parâmetros da mesma categoria estão agrupados. |
| control |Funcionalidade adicional para parâmetros de cordas.<br><br>data - É apresentado o controlo da data.<br>guia - O valor orientador é gerado automaticamente e o parâmetro não é apresentado. |
| descrição |Descrição opcional para o parâmetro.  Apresentado num balão de informação junto ao parâmetro. |

### <a name="standard-parameters"></a>Parâmetros padrão
A tabela seguinte enumera os parâmetros padrão para todas as soluções de gestão.  Estes valores são povoados para o utilizador em vez de os solicitar quando a sua solução é instalada a partir dos modelos Azure Marketplace ou Quickstart.  O utilizador deve fornecer-lhes valores se a solução for instalada com outro método.

> [!NOTE]
> A interface de utilizador nos modelos Azure Marketplace e Quickstart está à espera dos nomes dos parâmetros na tabela.  Se utilizar diferentes nomes de parâmetros, o utilizador será solicitado para os mesmos e não será automaticamente povoado.
>
>

| Parâmetro | Tipo | Descrição |
|:--- |:--- |:--- |
| accountName |string |Nome da conta Azure Automation. |
| preçosTier |string |Nível de preços tanto do espaço de trabalho log analytics como da conta Azure Automation. |
| regiãoId |string |Região da conta De Automação Azure. |
| nome da solução |string |Nome da solução.  Se estiver a implementar a sua solução através de modelos Quickstart, então deve definir o nome da solução como parâmetro para que possa definir uma cadeia, em vez disso, exigindo que o utilizador especifique um. |
| workspaceName |string |Log Analytics nome do espaço de trabalho. |
| espaço de trabalhoRegionId |string |Região do espaço de trabalho Log Analytics. |


Segue-se a estrutura dos parâmetros padrão que pode copiar e colar no seu ficheiro de solução.  

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


Refere-se aos valores dos parâmetros noutros elementos da solução com os parâmetros de sintaxe **('nome parâmetro')**.  Por exemplo, para aceder ao nome do espaço de trabalho, utilizaria **parâmetros ('workspaceName')**

## <a name="variables"></a>Variáveis
[As variáveis](../../azure-resource-manager/templates/template-syntax.md#variables) são valores que utilizará no resto da solução de gestão.  Estes valores não estão expostos ao utilizador que instala a solução.  Destinam-se a fornecer ao autor um único local onde possam gerir valores que possam ser usados várias vezes ao longo da solução. Deve colocar quaisquer valores específicos da sua solução em variáveis em vez de codificar duramente no elemento **de recursos.**  Isto torna o código mais legível e permite-lhe alterar facilmente estes valores em versões posteriores.

Segue-se um exemplo de um elemento variável com **parâmetros** típicos utilizados em soluções.

    "variables": {
        "SolutionVersion": "1.1",
        "SolutionPublisher": "Contoso",
        "SolutionName": "My Solution",
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Refere-se a valores variáveis através da solução com as **variáveis sintaxe ('nome variável')**.  Por exemplo, para aceder à variável Nome da Solução, utilizaria **variáveis ('Nome da solução')**.

Também pode definir variáveis complexas que múltiplos conjuntos de valores.  Estes são particularmente úteis em soluções de gestão onde você está definindo múltiplas propriedades para diferentes tipos de recursos.  Por exemplo, poderia reestruturar as variáveis de solução acima apresentadas para o seguinte.

    "variables": {
        "Solution": {
          "Version": "1.1",
          "Publisher": "Contoso",
          "Name": "My Solution"
        },
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31"
    },

Neste caso, refere-se a valores variáveis através da solução com as **variáveis sintaxe ('nome variável').propriedade**.  Por exemplo, para aceder à variável Nome da Solução, utilizaria **variáveis('Solução'). Nome.**

## <a name="resources"></a>Recursos
[Os recursos](../../azure-resource-manager/templates/template-syntax.md#resources) definem os diferentes recursos que a sua solução de gestão irá instalar e configurar.  Esta será a maior e mais complexa parte do modelo.  Você pode obter a estrutura e descrição completa de elementos de recursos em modelos de Gestor de [Recursos De Autoria Azure](../../azure-resource-manager/templates/template-syntax.md#resources).  Diferentes recursos que irá definir normalmente são detalhados em outros artigos nesta documentação. 


### <a name="dependencies"></a>Dependências
O elemento **dependsOn** especifica uma [dependência](../../azure-resource-manager/templates/define-resource-dependency.md) de outro recurso.  Quando a solução é instalada, um recurso não é criado até que todas as suas dependências tenham sido criadas.  Por exemplo, a sua solução pode iniciar um livro de [execução](solutions-resources-automation.md#runbooks) quando é instalado usando um recurso de [trabalho](solutions-resources-automation.md#automation-jobs).  O recurso de trabalho dependeria do recurso do livro de recorridos para garantir que o livro de execução é criado antes da criação do trabalho.

### <a name="log-analytics-workspace-and-automation-account"></a>Log Analytics workspace e conta de Automação
As soluções de gestão requerem um espaço de [trabalho log Analytics](../../azure-monitor/platform/manage-access.md) para conter vistas e uma conta de [Automação](../../automation/automation-security-overview.md#automation-account-overview) para conter livros de execução e recursos relacionados.  Estes devem estar disponíveis antes da criação dos recursos da solução e não devem ser definidos na própria solução.  O utilizador irá especificar um espaço de [trabalho e uma conta](solutions.md#log-analytics-workspace-and-automation-account) quando implementar a sua solução, mas como autor deve considerar os seguintes pontos.


## <a name="solution-resource"></a>Recurso de solução
Cada solução requer uma entrada de recursos no elemento **de recursos** que define a própria solução.  Isto terá um tipo de **Microsoft.OperationsManagement/solutions** e terá a seguinte estrutura. Isto inclui [parâmetros padrão](#parameters) e [variáveis](#variables) que são normalmente usadas para definir propriedades da solução.


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
O recurso de solução deve ter uma [dependência](../../azure-resource-manager/templates/define-resource-dependency.md) de todos os outros recursos da solução, uma vez que precisam de existir antes de a solução poder ser criada.  Faça-o adicionando uma entrada para cada recurso no elemento **dependsOn.**

### <a name="properties"></a>Propriedades
O recurso de solução tem as propriedades na tabela seguinte.  Isto inclui os recursos referenciados e contidos pela solução que define como o recurso é gerido após a instalação da solução.  Cada recurso na solução deve ser listado na **propriedade de Recursos referenciados** ou na propriedade **de Recursos contidos.**

| Propriedade | Descrição |
|:--- |:--- |
| espaço de trabalhoResourceId |ID do espaço de trabalho Log Analytics no formulário * \<Id>/fornecedores do\<Grupo\>de Recursos/Microsoft.OperationalInsights/workspace/ Workspace Name*. |
| recursos referenciados |Lista de recursos na solução que não deve ser removida quando a solução é removida. |
| recursos contidos |Lista de recursos na solução que deve ser removida quando a solução for removida. |

O exemplo acima é para uma solução com um livro de execução, um horário e vista.  O calendário e o rinque são *referenciados* no elemento **de propriedades** para que não sejam removidos quando a solução é removida.  A vista está *contida* para que seja removida quando a solução for removida.

### <a name="plan"></a>Planear
A entidade do **plano** do recurso de solução tem os imóveis na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| nome |Nome da solução. |
| versão |Versão da solução determinada pelo autor. |
| produto |Corda única para identificar a solução. |
| publicador |Editor da solução. |



## <a name="next-steps"></a>Passos seguintes
* [Adicione pesquisas e alertas guardados](solutions-resources-searches-alerts.md) para a sua solução de gestão.
* [Adicione pontos de vista](solutions-resources-views.md) à sua solução de gestão.
* [Adicione livros de execução e outros recursos de Automação](solutions-resources-automation.md) à sua solução de gestão.
* Conheça os detalhes dos modelos de Gestor de [Recursos Do Azure.](../../azure-resource-manager/templates/template-syntax.md)
* Procure [modelos Azure Quickstart](https://azure.microsoft.com/documentation/templates) para amostras de diferentes modelos de Gestor de Recursos.
