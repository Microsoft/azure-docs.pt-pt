---
title: Usar o pacote Azure Stream Analytics CI/CD NPM
description: Este artigo descreve como usar Azure Stream Analytics pacote de NPM CI/CD para configurar um processo de implantação e integração contínua.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: ed1f6ebda81a7f036b5e09d15ef4a27323aa9b0d
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/04/2020
ms.locfileid: "75660873"
---
# <a name="use-the-stream-analytics-cicd-npm-package"></a>Usar o pacote Stream Analytics CI/CD NPM
Este artigo descreve como usar o pacote Azure Stream Analytics CI/CD NPM para configurar um processo de implantação e integração contínua.

## <a name="build-the-vs-code-project"></a>Criar o projeto de VS Code

Você pode habilitar a integração e a implantação contínuas para trabalhos de Azure Stream Analytics usando o pacote NPM- **streamanalytics-cICD** . O pacote NPM fornece as ferramentas para gerar Azure Resource Manager modelos de [Stream Analytics projetos de Visual Studio Code](quick-create-vs-code.md). Ele pode ser usado no Windows, no macOS e no Linux sem instalar Visual Studio Code.

Você pode [baixar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) por meio do comando `npm install -g azure-streamanalytics-cicd`. Essa é a abordagem recomendada, que também pode ser usada em uma tarefa de script do PowerShell ou CLI do Azure de um pipeline de compilação em **pipelines do Azure DevOps**.

Depois de instalar o pacote, use o comando a seguir para gerar os modelos de Azure Resource Manager. O argumento **ScriptPath** é o caminho absoluto para o arquivo **asaql** em seu projeto. Verifique se os arquivos asaproj. JSON e JobConfig. JSON estão na mesma pasta com o arquivo de script. Se o **outputPath** não for especificado, os modelos serão colocados na pasta **Deploy** na pasta **bin** do projeto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemplo (no macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando um Stream Analytics Visual Studio Code projeto é compilado com êxito, ele gera os dois arquivos de modelo de Azure Resource Manager a seguir na pasta **bin/[Debug/Retail]/Deploy** : 

*  Arquivo de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Arquivo de parâmetros do Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros padrão no arquivo Parameters. JSON são das configurações no seu projeto Visual Studio Code. Se você quiser implantar em outro ambiente, substitua os parâmetros de acordo.

> [!NOTE]
> Para todas as credenciais, os valores padrão são definidos como NULL. Você precisa **definir** os valores antes de implantar na nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como [implantar com um arquivo de modelo do Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Saiba mais sobre como [usar um objeto como um parâmetro em um modelo do Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Para usar a identidade gerenciada para Azure Data Lake Store Gen1 como coletor de saída, você precisa fornecer acesso à entidade de serviço usando o PowerShell antes de implantar no Azure. Saiba mais sobre como [implantar ADLS Gen1 com identidade gerenciada com o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).
## <a name="next-steps"></a>Passos seguintes

* [Início rápido: criar um trabalho de Azure Stream Analytics nuvem no Visual Studio Code (visualização)](quick-create-vs-code.md)
* [Testar Stream Analytics consultas localmente com Visual Studio Code (versão prévia)](visual-studio-code-local-run.md)
* [Explorar Azure Stream Analytics com Visual Studio Code (versão prévia)](visual-studio-code-explore-jobs.md)
