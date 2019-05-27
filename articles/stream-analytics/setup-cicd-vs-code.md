---
title: Integre e desenvolver com o pacote de npm do Azure Stream Analytics CI/CD continuamente
description: Este artigo descreve como utilizar o pacote de npm do Azure Stream Analytics CI/CD para configurar uma integração contínua e o processo de implantação.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: fa5a57afa379c6bbe027be80f400fc176800d289
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66158511"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-cicd-npm-package"></a>Integre e desenvolver com o pacote de npm do Stream Analytics CI/CD continuamente
Este artigo descreve como utilizar o pacote de npm do Azure Stream Analytics CI/CD para configurar uma integração contínua e o processo de implantação.

## <a name="build-the-vs-code-project"></a>Compilar o projeto de código VS

Pode ativar a integração contínua e implementação para as tarefas de Azure Stream Analytics com o **asa-streamanalytics-CI/CD** pacote npm. O pacote npm fornece as ferramentas para gerar modelos Azure Resource Manager de [projetos do Stream Analytics Visual Studio Code](quick-create-vs-code.md). Ele pode ser usado no Windows, macOS e Linux sem instalar o Visual Studio Code.

Assim que tiver [transferido o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd), utilize o seguinte comando para exportar os modelos Azure Resource Manager. O **scriptPath** argumento é o caminho absoluto para o **asaql** ficheiro no seu projeto. Certifique-se que os ficheiros de JobConfig.json e asaproj.json estão na mesma pasta com o ficheiro de script. Se o **outputPath** não for especificado, os modelos vão ser colocados a **Deploy** pasta dentro do projeto **bin** pasta.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemplo (em macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando um projeto do Stream Analytics Visual Studio Code baseia-se com êxito, ele gera os seguintes ficheiros de modelo de Gestor de recursos do Azure dois sob o **bin / [depuração/revenda] / implementar** pasta: 

*  Ficheiro de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Ficheiro de parâmetros do Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros de padrão no ficheiro Parameters. JSON são nas definições no seu projeto do Visual Studio Code. Se pretender implementar para outro ambiente, substitua os parâmetros em conformidade.

> [!NOTE]
> Para todas as credenciais, os valores predefinidos estão definidos como null. Está **necessário** para definir os valores antes de implementar para a cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como [implementar com um ficheiro de modelo do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Saiba mais sobre como [utilize um objeto como um parâmetro num modelo do Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Para utilizar a identidade gerido para geração 1 do Azure Data Lake Store como sink de saída, terá de fornecer acesso ao serviço principal com o PowerShell antes de implementar para o Azure. Saiba mais sobre como [implementar Gen1 ADLS com identidade gerido com o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar uma tarefa de cloud do Azure Stream Analytics no Visual Studio Code (pré-visualização)](quick-create-vs-code.md)
* [Testar consultas do Stream Analytics localmente com o Visual Studio Code (pré-visualização)](vscode-local-run.md)
* [Explorar o Azure Stream Analytics com o Visual Studio Code (pré-visualização)](vscode-explore-jobs.md)
