---
title: Integre e programe com as ferramentas do Stream Analytics continuamente
description: Este artigo descreve como utilizar ferramentas do Visual Studio para o Azure Stream Analytics para configurar uma integração contínua e o processo de implantação.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/27/2017
ms.openlocfilehash: bb5b6a4e9d50a692342f497d27138c51e0dc93e4
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729429"
---
# <a name="continuously-integrate-and-develop-with-stream-analytics-tools"></a>Integre e programe com as ferramentas do Stream Analytics continuamente
Este artigo descreve como utilizar as ferramentas do Azure Stream Analytics para Visual Studio para configurar um processo contínuo de integração e implementação.

Utilizar a versão 2.3.0000.0 ou acima de [ferramentas do Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) obter suporte para MSBuild.

Um pacote NuGet está disponível: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Fornece o MSBuild, execução local e ferramentas de implementação que suportam o processo de integração e implementação contínuas de projetos do Stream Analytics para Visual Studio. 
> [!NOTE] 
O pacote NuGet pode ser utilizado apenas com o 2.3.0000.0 ou acima versão das ferramentas do Stream Analytics para Visual Studio. Se tiver projetos criados em versões anteriores das ferramentas do Visual Studio, basta abri-los com o 2.3.0000.0 ou acima de versão e guarde. Em seguida, as novas funcionalidades estão ativadas. 

Para obter mais informações, consulte [ferramentas do Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Como a experiência padrão do MSBuild do Visual Studio, para criar um projeto tem duas opções. Pode clicar com o botão direito no projeto e, em seguida, escolha **criar**. Também pode utilizar **MSBuild** no pacote NuGet da linha de comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando um projeto do Visual Studio do Stream Analytics baseia-se com êxito, ele gera os seguintes ficheiros de modelo de Gestor de recursos do Azure dois sob o **bin / [depuração/revenda] / implementar** pasta: 

*  Ficheiro de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Ficheiro de parâmetros do Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros de padrão no ficheiro Parameters. JSON são nas definições no seu projeto do Visual Studio. Se pretender implementar para outro ambiente, substitua os parâmetros em conformidade.

> [!NOTE] 
Para todas as credenciais, os valores predefinidos estão definidos como null. Está *necessário* para definir os valores antes de implementar para a cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como [implementar com um ficheiro de modelo do Resource Manager e o Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Saiba mais sobre como [utilize um objeto como um parâmetro num modelo do Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Para utilizar a identidade gerido para geração 1 do Azure Data Lake Store como sink de saída, terá de fornecer acesso ao serviço principal com o PowerShell antes de implementar para o Azure. Saiba mais sobre como [implementar Gen1 ADLS com identidade gerido com o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Ferramenta de linha de comandos

### <a name="build-the-project"></a>Compilar o projeto
O pacote NuGet tem uma ferramenta de linha de comando denominada **SA.exe**. Suporta a compilação do projeto e a realização de testes locais numa máquina arbitrária, o que pode utilizar na sua integração contínua e o processo de entrega contínua. 

Por predefinição, os arquivos de implantação são colocados sob o diretório atual. Pode especificar o caminho de saída usando o parâmetro - OutputPath seguintes:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testar o script localmente

Se seu projeto tiver especificado os ficheiros de entrada locais no Visual Studio, pode executar um teste automatizado de script utilizando o *localrun* comando. O resultado de saída é colocado sob o diretório atual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Gerar um arquivo de definição de tarefa para utilizar com a API de PowerShell do Stream Analytics

O *arm* comando demora o modelo de tarefa e os ficheiros de parâmetros de modelo de tarefa gerados por meio de compilação como entrada. Em seguida, combina-los para um ficheiro JSON de definição de tarefa que pode ser utilizado com a API de PowerShell do Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exemplo:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```


