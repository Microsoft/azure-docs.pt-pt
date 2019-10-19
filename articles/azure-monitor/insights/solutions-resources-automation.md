---
title: Recursos de automação do Azure em soluções de gerenciamento | Microsoft Docs
description: As soluções de gerenciamento normalmente incluirão runbooks na automação do Azure para automatizar processos como coletar e processar dados de monitoramento.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 63e09bacd1ce70f05f04798f092d3eb4b3e36ab5
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555240"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Adicionando recursos de automação do Azure a uma solução de gerenciamento (versão prévia)
> [!NOTE]
> Esta é uma documentação preliminar para criar soluções de gerenciamento que estão atualmente em versão prévia. Qualquer esquema descrito abaixo está sujeito a alterações.   


As [soluções de gerenciamento]( solutions.md) normalmente incluirão Runbooks na automação do Azure para automatizar processos como coletar e processar dados de monitoramento.  Além dos runbooks, as contas de automação incluem ativos, como variáveis e agendas, que dão suporte aos runbooks usados na solução.  Este artigo descreve como incluir runbooks e seus recursos relacionados em uma solução.

> [!NOTE]
> Os exemplos neste artigo usam parâmetros e variáveis que são necessários ou comuns para soluções de gerenciamento e são descritos em [projetar e criar uma solução de gerenciamento no Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo pressupõe que você já esteja familiarizado com as informações a seguir.

- Como [criar uma solução de gerenciamento]( solutions-creating.md).
- A estrutura de um [arquivo de solução]( solutions-solution-file.md).
- Como [criar modelos do Resource Manager](../../azure-resource-manager/resource-group-authoring-templates.md)

## <a name="automation-account"></a>Conta de automatização
Todos os recursos na automação do Azure estão contidos em uma [conta de automação](../../automation/automation-security-overview.md#automation-account-overview).  Conforme descrito em [log Analytics espaço de trabalho e conta de automação]( solutions.md#log-analytics-workspace-and-automation-account) , a conta de automação não está incluída na solução de gerenciamento, mas deve existir antes da instalação da solução.  Se não estiver disponível, a instalação da solução falhará.

O nome de cada recurso de automação inclui o nome de sua conta de automação.  Isso é feito na solução com o parâmetro **accountName** como no exemplo a seguir de um recurso de runbook.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Você deve incluir todos os runbooks usados pela solução no arquivo de solução para que eles sejam criados quando a solução for instalada.  No entanto, você não pode conter o corpo do runbook no modelo, portanto, você deve publicar o runbook em um local público onde ele possa ser acessado por qualquer usuário que esteja instalando sua solução.

Os recursos de [runbook da automação do Azure](../../automation/automation-runbook-types.md) têm um tipo de **Microsoft. Automation/automationAccounts/runbooks** e têm a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
        "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
        "type": "Microsoft.Automation/automationAccounts/runbooks",
        "apiVersion": "[variables('AutomationApiVersion')]",
        "dependsOn": [
        ],
        "location": "[parameters('regionId')]",
        "tags": { },
        "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
                "uri": "[variables('Runbook').Uri]",
                "version": [variables('Runbook').Version]"
            }
        }
    }


As propriedades de runbooks são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Runbooktype necessária |Especifica os tipos do runbook. <br><br> Script-script do PowerShell <br>PowerShell-fluxo de trabalho do PowerShell <br> GraphPowerShell-runbook de script do PowerShell gráfico <br> GraphPowerShellWorkflow-runbook de fluxo de trabalho do PowerShell gráfico |
| logProgress |Especifica se os [registros de andamento](../../automation/automation-runbook-output-and-messages.md) devem ser gerados para o runbook. |
| logVerbose |Especifica se os [registros detalhados](../../automation/automation-runbook-output-and-messages.md) devem ser gerados para o runbook. |
| descrição |Descrição opcional para o runbook. |
| publishContentLink |Especifica o conteúdo do runbook. <br><br>URI-Uri para o conteúdo do runbook.  Esse será um arquivo. ps1 para runbooks do PowerShell e de script e um arquivo de runbook gráfico exportado para um runbook do grafo.  <br> versão-versão do runbook para seu próprio rastreamento. |


## <a name="automation-jobs"></a>Trabalhos de automação
Quando você inicia um runbook na automação do Azure, ele cria um trabalho de automação.  Você pode adicionar um recurso de trabalho de automação à sua solução para iniciar automaticamente um runbook quando a solução de gerenciamento for instalada.  Esse método é normalmente usado para iniciar runbooks que são usados para a configuração inicial da solução.  Para iniciar um runbook em intervalos regulares, crie uma [agenda](#schedules) e uma [agenda de trabalho](#job-schedules)

Os recursos de trabalho têm um tipo de **Microsoft. Automation/automationAccounts/Jobs** e têm a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', parameters('Runbook').JobGuid)]",
      "type": "Microsoft.Automation/automationAccounts/jobs",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
      ],
      "tags": { },
      "properties": {
        "runbook": {
          "name": "[variables('Runbook').Name]"
        },
        "parameters": {
          "Parameter1": "[[variables('Runbook').Parameter1]",
          "Parameter2": "[[variables('Runbook').Parameter2]"
        }
      }
    }

As propriedades de trabalhos de automação são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| runbook |Entidade de nome único com o nome do runbook a ser iniciado. |
| parâmetros |Entidade para cada valor de parâmetro exigido pelo runbook. |

O trabalho inclui o nome do runbook e quaisquer valores de parâmetro a serem enviados ao runbook.  O trabalho deve [depender]( solutions-solution-file.md#resources) do runbook que está sendo iniciado, uma vez que o runbook deve ser criado antes do trabalho.  Se você tiver vários runbooks que devem ser iniciados, poderá definir sua ordem fazendo com que um trabalho dependa de quaisquer outros trabalhos que devam ser executados primeiro.

O nome de um recurso de trabalho deve conter um GUID que normalmente é atribuído por um parâmetro.  Você pode ler mais sobre parâmetros GUID na [criação de um arquivo de solução de gerenciamento no Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificados
Os [certificados de automação do Azure](../../automation/automation-certificates.md) têm um tipo de **Microsoft. Automation/automationAccounts/Certificates** e têm a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
      "type": "Microsoft.Automation/automationAccounts/certificates",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "base64Value": "[variables('Certificate').Base64Value]",
        "thumbprint": "[variables('Certificate').Thumbprint]"
      }
    }



As propriedades dos recursos de certificados são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| base64Value |Valor base 64 do certificado. |
| digitais |Impressão digital do certificado. |



## <a name="credentials"></a>Credenciais
[As credenciais de automação do Azure](../../automation/automation-credentials.md) têm um tipo de **Microsoft. Automation/automationAccounts/Credentials** e têm a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 


    {
      "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
      "type": "Microsoft.Automation/automationAccounts/credentials",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "userName": "[parameters('credentialUsername')]",
        "password": "[parameters('credentialPassword')]"
      }
    }

As propriedades de recursos de credencial são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Usu |Nome de usuário da credencial. |
| palavra-passe |Senha da credencial. |


## <a name="schedules"></a>Agendas
Os [agendamentos de automação do Azure](../../automation/automation-schedules.md) têm um tipo de **Microsoft. Automation/automationAccounts/Schedules** e têm a seguinte estrutura. Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
      "type": "microsoft.automation/automationAccounts/schedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Schedule').Description]",
        "startTime": "[parameters('scheduleStartTime')]",
        "timeZone": "[parameters('scheduleTimeZone')]",
        "isEnabled": "[variables('Schedule').IsEnabled]",
        "interval": "[variables('Schedule').Interval]",
        "frequency": "[variables('Schedule').Frequency]"
      }
    }

As propriedades de recursos de agendamento são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| descrição |Descrição opcional para a agenda. |
| startTime |Especifica a hora de início de uma agenda como um objeto DateTime. Uma cadeia de caracteres pode ser fornecida se puder ser convertida em um DateTime válido. |
| isEnabled |Especifica se a agenda está habilitada. |
| intervalo |O tipo de intervalo para a agenda.<br><br>diário<br>dia |
| frequência |Frequência em que a agenda deve ser acionada em número de dias ou horas. |

Os agendamentos devem ter uma hora de início com um valor maior que a hora atual.  Você não pode fornecer esse valor com uma variável, pois não teria como saber quando ele será instalado.

Use uma das duas estratégias a seguir ao usar os recursos de agendamento em uma solução.

- Use um parâmetro para a hora de início do agendamento.  Isso solicitará que o usuário forneça um valor ao instalar a solução.  Se você tiver vários agendamentos, poderá usar um único valor de parâmetro para mais de um deles.
- Crie os agendamentos usando um runbook que é iniciado quando a solução é instalada.  Isso remove a necessidade do usuário de especificar uma hora, mas você não pode conter a agenda em sua solução, de modo que ela será removida quando a solução for removida.


### <a name="job-schedules"></a>Agendas de tarefas
Os recursos de agendamento de trabalho vinculam um runbook a um agendamento.  Eles têm um tipo de **Microsoft. Automation/automationAccounts/jobSchedules** e têm a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro. 

    {
      "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
      "type": "microsoft.automation/automationAccounts/jobSchedules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "location": "[parameters('regionId')]",
      "dependsOn": [
        "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
        "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
      ],
      "tags": {
      },
      "properties": {
        "schedule": {
          "name": "[variables('Schedule').Name]"
        },
        "runbook": {
          "name": "[variables('Runbook').Name]"
        }
      }
    }


As propriedades para agendas de trabalho são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| Nome da agenda |Entidade de **nome** único com o nome da agenda. |
| nome do runbook  |Entidade de **nome** único com o nome do runbook.  |



## <a name="variables"></a>Variáveis
As [variáveis de automação do Azure](../../automation/automation-variables.md) têm um tipo de **Microsoft. Automation/automationAccounts/Variables** e têm a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
      "type": "microsoft.automation/automationAccounts/variables",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "tags": { },
      "dependsOn": [
      ],
      "properties": {
        "description": "[variables('Variable').Description]",
        "isEncrypted": "[variables('Variable').Encrypted]",
        "type": "[variables('Variable').Type]",
        "value": "[variables('Variable').Value]"
      }
    }

As propriedades de recursos de variáveis são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| descrição | Descrição opcional para a variável. |
| isEncrypted | Especifica se a variável deve ser criptografada. |
| tipo | Essa propriedade não tem efeito no momento.  O tipo de dados da variável será determinado pelo valor inicial. |
| valor | Valor da variável. |

> [!NOTE]
> A propriedade **Type** atualmente não tem nenhum efeito na variável que está sendo criada.  O tipo de dados da variável será determinado pelo valor.  

Se você definir o valor inicial para a variável, ela deverá ser configurada como o tipo de dados correto.  A tabela a seguir fornece os diferentes tipos de dados permitidos e sua sintaxe.  Observe que os valores em JSON devem sempre ser colocados entre aspas com caracteres especiais entre aspas.  Por exemplo, um valor de cadeia de caracteres seria especificado por aspas ao contrário da cadeia de caracteres (usando o caractere de escape (\\), enquanto um valor numérico seria especificado com um conjunto de aspas.

| Data type | Descrição | Exemplo | Resolve para |
|:--|:--|:--|:--|
| string   | Coloque o valor entre aspas duplas.  | "\" do \"Hello World" | "Olá, mundo" |
| numeric  | Valor numérico com aspas simples.| "64" | 64 |
| boolean  | **true** ou **false** entre aspas.  Observe que esse valor deve estar em minúsculas. | true | true |
| datetime | Valor de data serializado.<br>Você pode usar o cmdlet ConvertTo-JSON no PowerShell para gerar esse valor para uma data específica.<br>Exemplo: Get-Date "5/24/2017 13:14:57" \| ConvertTo-JSON | "\\/Date (1495656897378) \\/" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Módulos
Sua solução de gerenciamento não precisa definir [módulos globais](../../automation/automation-integration-modules.md) usados por seus runbooks porque eles sempre estarão disponíveis em sua conta de automação.  Você precisa incluir um recurso para qualquer outro módulo usado por seus runbooks.

Os [módulos de integração](../../automation/automation-integration-modules.md) têm um tipo de **Microsoft. Automation/automationAccounts/modules** e têm a seguinte estrutura.  Isso inclui variáveis e parâmetros comuns para que você possa copiar e colar esse trecho de código em seu arquivo de solução e alterar os nomes de parâmetro.

    {
      "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
      "type": "Microsoft.Automation/automationAccounts/modules",
      "apiVersion": "[variables('AutomationApiVersion')]",
      "dependsOn": [
      ],
      "properties": {
        "contentLink": {
          "uri": "[variables('Module').Uri]"
        }
      }
    }


As propriedades para recursos de módulo são descritas na tabela a seguir.

| Propriedade | Descrição |
|:--- |:--- |
| contentLink |Especifica o conteúdo do módulo. <br><br>URI-Uri para o conteúdo do módulo.  Esse será um arquivo. ps1 para runbooks do PowerShell e de script e um arquivo de runbook gráfico exportado para um runbook do grafo.  <br> versão-versão do módulo para seu próprio controle. |

O runbook deve depender do recurso de módulo para garantir que ele seja criado antes do runbook.

### <a name="updating-modules"></a>Atualizando módulos
Se você atualizar uma solução de gerenciamento que inclui um runbook que usa uma agenda e a nova versão da sua solução tiver um novo módulo usado por esse runbook, o runbook poderá usar a versão antiga do módulo.  Você deve incluir os seguintes runbooks em sua solução e criar um trabalho para executá-los antes de quaisquer outros runbooks.  Isso garantirá que todos os módulos sejam atualizados conforme necessário antes que os runbooks sejam carregados.

* [Update-ModulesinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) garantirá que todos os módulos usados por runbooks em sua solução sejam a versão mais recente.  
* [ReRegisterAutomationSchedule-MS-MGMT](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) registrará novamente todos os recursos de agendamento para garantir que os runbooks vinculados a eles usem os módulos mais recentes.




## <a name="sample"></a>Exemplo
Veja a seguir um exemplo de uma solução que inclui os seguintes recursos:

- Runbook.  Este é um runbook de exemplo armazenado em um repositório GitHub público.
- Trabalho de automação que inicia o runbook quando a solução é instalada.
- Agenda e agenda de trabalho para iniciar o runbook em intervalos regulares.
- Certificate.
- Provedores.
- Ela.
- Modulo.  Este é o [módulo OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) para gravar dados em log Analytics. 

O exemplo usa variáveis de [parâmetros de solução padrão]( solutions-solution-file.md#parameters) que normalmente seriam usadas em uma solução em vez de codificar valores nas definições de recurso.


    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Log Analytics workspace."
          }
        },
        "accountName": {
          "type": "string",
          "metadata": {
            "Description": "Name of Automation account."
          }
        },
        "workspaceregionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Log Analytics workspace."
          }
        },
        "regionId": {
          "type": "string",
          "metadata": {
            "Description": "Region of Automation account."
          }
        },
        "pricingTier": {
          "type": "string",
          "metadata": {
            "Description": "Pricing tier of both Log Analytics workspace and Azure Automation account."
          }
        },
        "certificateBase64Value": {
          "type": "string",
          "metadata": {
            "Description": "Base 64 value for certificate."
          }
        },
        "certificateThumbprint": {
          "type": "securestring",
          "metadata": {
            "Description": "Thumbprint for certificate."
          }
        },
        "credentialUsername": {
          "type": "string",
          "metadata": {
            "Description": "Username for credential."
          }
        },
        "credentialPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Password for credential."
          }
        },
        "scheduleStartTime": {
          "type": "string",
          "metadata": {
            "Description": "Start time for schedule."
          }
        },
        "scheduleTimeZone": {
          "type": "string",
          "metadata": {
            "Description": "Time zone for schedule."
          }
        },
        "scheduleLinkGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the schedule link to runbook.",
            "control": "guid"
          }
        },
        "runbookJobGuid": {
          "type": "string",
          "metadata": {
            "description": "GUID for the runbook job.",
            "control": "guid"
          }
        }
      },
      "variables": {
        "SolutionName": "MySolution",
        "SolutionVersion": "1.0",
        "SolutionPublisher": "Contoso",
        "ProductName": "SampleSolution",
    
        "LogAnalyticsApiVersion": "2015-11-01-preview",
        "AutomationApiVersion": "2015-10-31",
    
        "Runbook": {
          "Name": "MyRunbook",
          "Description": "Sample runbook",
          "Type": "PowerShell",
          "Uri": "https://raw.githubusercontent.com/user/myrepo/master/samples/MyRunbook.ps1",
          "JobGuid": "[parameters('runbookJobGuid')]"
        },
    
        "Certificate": {
          "Name": "MyCertificate",
          "Base64Value": "[parameters('certificateBase64Value')]",
          "Thumbprint": "[parameters('certificateThumbprint')]"
        },
    
        "Credential": {
          "Name": "MyCredential",
          "UserName": "[parameters('credentialUsername')]",
          "Password": "[parameters('credentialPassword')]"
        },
    
        "Schedule": {
          "Name": "MySchedule",
          "Description": "Sample schedule",
          "IsEnabled": "true",
          "Interval": "1",
          "Frequency": "hour",
          "StartTime": "[parameters('scheduleStartTime')]",
          "TimeZone": "[parameters('scheduleTimeZone')]",
          "LinkGuid": "[parameters('scheduleLinkGuid')]"
        },
    
        "Variable": {
          "Name": "MyVariable",
          "Description": "Sample variable",
          "Encrypted": 0,
          "Type": "string",
          "Value": "'This is my string value.'"
        },
    
        "Module": {
          "Name": "OMSIngestionAPI",
          "Uri": "https://devopsgallerystorage.blob.core.windows.net/packages/omsingestionapi.1.3.0.nupkg"
        }
      },
      "resources": [
        {
          "name": "[concat(variables('SolutionName'), '[' ,parameters('workspacename'), ']')]",
          "location": "[parameters('workspaceRegionId')]",
          "tags": { },
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "[variables('LogAnalyticsApiVersion')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
            "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
          ],
          "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspacename'))]",
            "referencedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/modules/', parameters('accountName'), variables('Module').Name)]"
            ],
            "containedResources": [
              "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobs/', parameters('accountName'), variables('Runbook').JobGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/certificates/', parameters('accountName'), variables('Certificate').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/credentials/', parameters('accountName'), variables('Credential').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]",
              "[resourceId('Microsoft.Automation/automationAccounts/jobSchedules/', parameters('accountName'), variables('Schedule').LinkGuid)]",
              "[resourceId('Microsoft.Automation/automationAccounts/variables/', parameters('accountName'), variables('Variable').Name)]"
            ]
          },
          "plan": {
            "name": "[concat(variables('SolutionName'), '[' ,parameters('workspaceName'), ']')]",
            "Version": "[variables('SolutionVersion')]",
            "product": "[variables('ProductName')]",
            "publisher": "[variables('SolutionPublisher')]",
            "promotionCode": ""
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').Name)]",
          "type": "Microsoft.Automation/automationAccounts/runbooks",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "location": "[parameters('regionId')]",
          "tags": { },
          "properties": {
            "runbookType": "[variables('Runbook').Type]",
            "logProgress": "true",
            "logVerbose": "true",
            "description": "[variables('Runbook').Description]",
            "publishContentLink": {
              "uri": "[variables('Runbook').Uri]",
              "version": "1.0.0.0"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Runbook').JobGuid)]",
          "type": "Microsoft.Automation/automationAccounts/jobs",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[concat('Microsoft.Automation/automationAccounts/', parameters('accountName'), '/runbooks/', variables('Runbook').Name)]"
          ],
          "tags": { },
          "properties": {
            "runbook": {
              "name": "[variables('Runbook').Name]"
            },
            "parameters": {
              "targetSubscriptionId": "[subscription().subscriptionId]",
              "resourcegroup": "[resourceGroup().name]",
              "automationaccount": "[parameters('accountName')]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Certificate').Name)]",
          "type": "Microsoft.Automation/automationAccounts/certificates",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "Base64Value": "[variables('Certificate').Base64Value]",
            "Thumbprint": "[variables('Certificate').Thumbprint]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Credential').Name)]",
          "type": "Microsoft.Automation/automationAccounts/credentials",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "userName": "[variables('Credential').UserName]",
            "password": "[variables('Credential').Password]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').Name)]",
          "type": "microsoft.automation/automationAccounts/schedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Schedule').Description]",
            "startTime": "[variables('Schedule').StartTime]",
            "timeZone": "[variables('Schedule').TimeZone]",
            "isEnabled": "[variables('Schedule').IsEnabled]",
            "interval": "[variables('Schedule').Interval]",
            "frequency": "[variables('Schedule').Frequency]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Schedule').LinkGuid)]",
          "type": "microsoft.automation/automationAccounts/jobSchedules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "location": "[parameters('regionId')]",
          "dependsOn": [
            "[resourceId('Microsoft.Automation/automationAccounts/runbooks/', parameters('accountName'), variables('Runbook').Name)]",
            "[resourceId('Microsoft.Automation/automationAccounts/schedules/', parameters('accountName'), variables('Schedule').Name)]"
          ],
          "tags": {
          },
          "properties": {
            "schedule": {
              "name": "[variables('Schedule').Name]"
            },
            "runbook": {
              "name": "[variables('Runbook').Name]"
            }
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Variable').Name)]",
          "type": "microsoft.automation/automationAccounts/variables",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "tags": { },
          "dependsOn": [
          ],
          "properties": {
            "description": "[variables('Variable').Description]",
            "isEncrypted": "[variables('Variable').Encrypted]",
            "type": "[variables('Variable').Type]",
            "value": "[variables('Variable').Value]"
          }
        },
        {
          "name": "[concat(parameters('accountName'), '/', variables('Module').Name)]",
          "type": "Microsoft.Automation/automationAccounts/modules",
          "apiVersion": "[variables('AutomationApiVersion')]",
          "dependsOn": [
          ],
          "properties": {
            "contentLink": {
              "uri": "[variables('Module').Uri]"
            }
          }
        }
    
      ],
      "outputs": { }
    }




## <a name="next-steps"></a>Passos seguintes
* [Adicione uma exibição à sua solução]( solutions-resources-views.md) para visualizar os dados coletados.
