---
title: Recursos da Azure Automation em soluções de gestão Microsoft Docs
description: As soluções de gestão incluirão normalmente livros de execução na Azure Automation para automatizar processos como a recolha e processamento de dados de monitorização.  Este artigo descreve como incluir livros de execução e os seus recursos conexos numa solução.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/24/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a3b1b134afbc4a13d7888281a82609d444cee377
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83682869"
---
# <a name="adding-azure-automation-resources-to-a-management-solution-preview"></a>Adicionar recursos da Automação Azure a uma solução de gestão (Pré-visualização)
> [!NOTE]
> Esta é uma documentação preliminar para a criação de soluções de gestão que estão atualmente em pré-visualização. Qualquer esquema descrito abaixo está sujeito a alterações.   


[As soluções]( solutions.md) de gestão incluirão normalmente livros de execução na Azure Automation para automatizar processos como a recolha e processamento de dados de monitorização.  Além dos livros de execução, as contas de Automação incluem ativos como variáveis e horários que suportam os livros de execução utilizados na solução.  Este artigo descreve como incluir livros de execução e os seus recursos conexos numa solução.

> [!NOTE]
> As amostras deste artigo utilizam parâmetros e variáveis que são necessários ou comuns a soluções de gestão e descritas no [Design e constroem uma solução de gestão em Azure]( solutions-creating.md) 


## <a name="prerequisites"></a>Pré-requisitos
Este artigo assume que já está familiarizado com as seguintes informações.

- Como [criar uma solução de gestão.]( solutions-creating.md)
- A estrutura de um ficheiro de [solução.]( solutions-solution-file.md)
- Como [autor de modelos de Gestor](../../azure-resource-manager/templates/template-syntax.md) de Recursos

## <a name="automation-account"></a>Conta de automatização
Todos os recursos da Automação Azure estão contidos numa [conta de Automação.](../../automation/automation-security-overview.md)  Conforme descrito no [log analytics workspace e na conta Automation,]( solutions.md#log-analytics-workspace-and-automation-account) a conta Automation não está incluída na solução de gestão, mas deve existir antes de a solução ser instalada.  Se não estiver disponível, a instalação da solução falhará.

O nome de cada recurso Automation inclui o nome da sua conta Automation.  Isto é feito na solução com o parâmetro **name** name como no exemplo seguinte de um recurso de livro de execução.

    "name": "[concat(parameters('accountName'), '/MyRunbook'))]"


## <a name="runbooks"></a>Runbooks
Deve incluir todos os livros de execução utilizados pela solução no ficheiro de solução para que sejam criados quando a solução estiver instalada.  No entanto, não é possível conter o corpo do livro de execução no modelo, pelo que deve publicar o livro de execução num local público onde possa ser acedido por qualquer utilizador que instale a sua solução.

Os recursos do [livro de execução Da Automatização Azure](../../automation/automation-runbook-types.md) têm um tipo de **Microsoft.Automation/automationAccounts/runbooks** e têm a seguinte estrutura. Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros. 

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


As propriedades para livros de execução são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| livro de corridasType |Especifica os tipos do livro de execução. <br><br> Script - PowerShell script <br>PowerShell - Fluxo de trabalho PowerShell <br> GraphPowerShell - Livro de execução de scripts Graphical PowerShell <br> GraphPowerShellWorkflow - Fluxo de fluxo de fluxo de fluxo de fluxo de fluxo de powershell gráfico |
| logProgress |Especifica se os [registos](../../automation/automation-runbook-output-and-messages.md) de progresso devem ser gerados para o livro de execução. |
| logVerbose |Especifica se [os registos verbosos](../../automation/automation-runbook-output-and-messages.md) devem ser gerados para o livro de execução. |
| descrição |Descrição opcional para o livro de execução. |
| publicarContentLink |Especifica o conteúdo do livro de execução. <br><br>uri - Uri ao conteúdo do livro de corridas.  Este será um ficheiro .ps1 para os livros powerShell e Script, e um ficheiro de rumbook gráfico exportado para um livro de execução de gráficos.  <br> versão - Versão do livro de execução para o seu próprio tracking. |


## <a name="automation-jobs"></a>Trabalhos de automação
Quando se inicia um livro de execução na Azure Automation, cria-se um trabalho de automação.  Pode adicionar um recurso de trabalho de automação à sua solução para iniciar automaticamente um livro de execução quando a solução de gestão estiver instalada.  Este método é normalmente utilizado para iniciar livros de execução que são usados para a configuração inicial da solução.  Para iniciar um livro de corridas em intervalos regulares, crie um [horário](#schedules) e um horário de [trabalho](#job-schedules)

Os recursos de emprego têm um tipo de **Microsoft.Automation/automationAccounts/jobs** e têm a seguinte estrutura.  Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros. 

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

As propriedades para trabalhos de automação são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| runbook |Entidade de nome único com o nome do livro de execução para começar. |
| parâmetros |Entidade para cada valor de parâmetro exigido pelo livro de execução. |

O trabalho inclui o nome do livro de corridas e quaisquer valores de parâmetro a enviar para o livro de execução.  O trabalho deve [depender]( solutions-solution-file.md#resources) do livro de execução que está a começar, uma vez que o livro de corridas tem de ser criado antes do trabalho.  Se tiver vários livros de execução que devem ser iniciados, pode definir a sua encomenda tendo um emprego dependente de quaisquer outros empregos que devam ser executados primeiro.

O nome de um recurso de trabalho deve conter um GUID que é tipicamente atribuído por um parâmetro.  Pode ler mais sobre parâmetros GUID na Criação de um ficheiro de solução de [gestão em Azure]( solutions-solution-file.md#parameters).  


## <a name="certificates"></a>Certificados
Os [certificados Azure Automation](../../automation/automation-certificates.md) têm um tipo de **Microsoft.Automation/automationAccounts/certificates** e têm a seguinte estrutura. Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros. 

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



As propriedades dos recursos certificados são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| base64Valor |Valor base 64 para o certificado. |
| impressão de polegar |Impressão digital para o certificado. |



## <a name="credentials"></a>Credenciais
As [credenciais Azure Automation](../../automation/automation-credentials.md) têm um tipo de **Microsoft.Automation/automationAccounts/credentials** e têm a seguinte estrutura.  Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros. 


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

As propriedades para recursos credenciais são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| userName |Nome do utilizador para a credencial. |
| palavra-passe |Senha para a credencial. |


## <a name="schedules"></a>Agendas
Os horários da [Automação Azure](../../automation/automation-schedules.md) têm um tipo de **Microsoft.Automation/automationAccounts/schedules** e têm a seguinte estrutura. Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros. 

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

As propriedades para recursos de horário são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| descrição |Descrição opcional para o horário. |
| startTime |Especifica a hora de início de um horário como um objeto DateTime. Uma corda pode ser fornecida se puder ser convertida para um DataTime válido. |
| isEnabled |Especifica se o horário está ativado. |
| intervalo |O tipo de intervalo para o horário.<br><br>day<br>hour |
| frequência |Frequência que o horário deve disparar em número de dias ou horas. |

Os horários devem ter um início com um valor superior ao atual.  Não pode fornecer este valor com uma variável, uma vez que não teria como saber quando vai ser instalado.

Utilize uma das duas estratégias seguintes ao utilizar os recursos de programação numa solução.

- Utilize um parâmetro para a hora de início do horário.  Isto irá levar o utilizador a fornecer um valor quando instalar a solução.  Se tiver vários horários, pode utilizar um único valor de parâmetro para mais de um deles.
- Crie os horários utilizando um livro de execução que comece quando a solução estiver instalada.  Isto elimina a exigência do utilizador de especificar uma hora, mas não pode conter o horário da sua solução para que seja removido quando a solução for removida.


### <a name="job-schedules"></a>Agendas de tarefas
Os recursos do horário de trabalho ligam um livro de corridas com um horário.  Têm um tipo de **Microsoft.Automation/automationAccounts/jobSchedules** e têm a seguinte estrutura.  Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros. 

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


As propriedades para horários de trabalho são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| nome de agenda |Entidade **de nome** único com o nome da programação. |
| nome do livro de corridas  |Entidade **de nome** único com o nome do livro de execução.  |



## <a name="variables"></a>Variáveis
[As variáveis Azure Automation](../../automation/automation-variables.md) têm um tipo de **Microsoft.Automation/automationAccounts/variables** e têm a seguinte estrutura.  Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros.

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

As propriedades para recursos variáveis são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| descrição | Descrição opcional para a variável. |
| isEncrypted | Especifica se a variável deve ser encriptada. |
| tipo | Esta propriedade atualmente não tem efeito.  O tipo de dados da variável será determinado pelo valor inicial. |
| valor | Valor para a variável. |

> [!NOTE]
> A propriedade **tipo** atualmente não tem qualquer efeito sobre a variável que está sendo criada.  O tipo de dados para a variável será determinado pelo valor.  

Se definir o valor inicial da variável, deve ser configurado como o tipo de dados correto.  A tabela que se segue fornece os diferentes tipos de dados admissíveis e a sua sintaxe.  Note que os valores em JSON devem ser sempre incluídos em citações com quaisquer caracteres especiais dentro das cotações.  Por exemplo, um valor de cadeia seria especificado por citações em torno da cadeia (utilizando o carácter de fuga \\ ()) enquanto um valor numérico seria especificado com um conjunto de citações.

| Tipo de dados | Descrição | Exemplo | Resolve-se |
|:--|:--|:--|:--|
| string   | Valor de encerramento em cotações duplas.  | " \" Olá \" mundo" | "Olá mundo" |
| numeric  | Valor numérico com cotações individuais.| "64" | 64 |
| boolean  | **verdadeiro** ou **falso** em aspas.  Note que este valor deve ser minúsculo. | "Verdadeiro" | true |
| datetime | Valor de data serializada.<br>Pode utilizar o cmdlet ConvertTo-Json no PowerShell para gerar este valor para uma determinada data.<br>Exemplo: data de validade "5/24/2017 13:14:57" \| ConvertTo-Json | " \\ /Data(1495656897378) \\ /" | 2017-05-24 13:14:57 |

## <a name="modules"></a>Módulos
A sua solução de gestão não precisa de definir [módulos globais utilizados](../../automation/automation-integration-modules.md) pelos seus livros de execução, pois estarão sempre disponíveis na sua conta Automation.  Precisa de incluir um recurso para qualquer outro módulo utilizado pelos seus livros de execução.

[Os módulos](../../automation/automation-integration-modules.md) de integração têm um tipo de **Microsoft.Automation/automationAccounts/modules** e têm a seguinte estrutura.  Isto inclui variáveis e parâmetros comuns para que possa copiar e colar este código no seu ficheiro de solução e alterar os nomes dos parâmetros.

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


As propriedades para os recursos do módulo são descritas na tabela seguinte.

| Propriedade | Descrição |
|:--- |:--- |
| contentLink |Especifica o conteúdo do módulo. <br><br>uri - Uri ao conteúdo do módulo.  Este será um ficheiro .ps1 para os livros powerShell e Script, e um ficheiro de rumbook gráfico exportado para um livro de execução de gráficos.  <br> versão - Versão do módulo para o seu próprio rastreio. |

O livro de recortes deve depender do recurso do módulo para garantir que é criado antes do livro de execução.

### <a name="updating-modules"></a>Atualizar módulos
Se atualizar uma solução de gestão que inclui um livro de execução que utiliza um calendário, e a nova versão da sua solução tem um novo módulo utilizado por esse livro de execução, então o livro de execução pode utilizar a versão antiga do módulo.  Deve incluir os seguintes livros de execução na sua solução e criar um trabalho para executá-los antes de quaisquer outros livros de execução.  Isto garantirá que quaisquer módulos sejam atualizados conforme necessário antes de os livros de execução serem carregados.

* [Atualização-MódulosinAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/1.03/) garantirá que todos os módulos utilizados por livros de execução na sua solução são a versão mais recente.  
* [ReRegisterAutomationSchedule-MS-Mgmt](https://www.powershellgallery.com/packages/ReRegisterAutomationSchedule-MS-Mgmt/1.0/) reregistrará todos os recursos de agenda para garantir que os livros de execução ligados aos mesmos com a utilização dos módulos mais recentes.




## <a name="sample"></a>Sample
Segue-se uma amostra de uma solução que inclui os seguintes recursos:

- Runbook.  Este é um livro de amostras armazenado num repositório público do GitHub.
- Trabalho de automação que inicia o livro de execução quando a solução é instalada.
- Horário e horário de trabalho para iniciar o livro de corridas em intervalos regulares.
- Um certificado.
- Credencial.
- Variável.
- O módulo.  Este é o [módulo OMSIngestionAPI](https://www.powershellgallery.com/packages/OMSIngestionAPI/1.5) para escrever dados para Log Analytics. 

A amostra utiliza variáveis de parâmetros de [solução padrão]( solutions-solution-file.md#parameters) que normalmente seriam usadas numa solução em oposição aos valores de codificação de dificuldade nas definições de recursos.


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
* [Adicione uma visão à sua solução]( solutions-resources-views.md) para visualizar os dados recolhidos.
