---
title: Usar o pacote NuGet Azure Stream Analytics CI/CD para integração e desenvolvimento
description: Este artigo descreve como usar Azure Stream Analytics pacote NuGet CI/CD para configurar um processo de implantação e integração contínua.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 47bcd29ca8a1da0c42f7bc39aeb4ffc1ad8e8571
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172905"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Usar o pacote NuGet Azure Stream Analytics CI/CD para integração e desenvolvimento 
Este artigo descreve como usar o pacote NuGet Azure Stream Analytics CI/CD para configurar um processo de implantação e integração contínua.

Use a versão 2.3.0000.0 ou superior do [Stream Analytics Tools para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) para obter suporte para MSBuild.

Um pacote NuGet está disponível: [Microsoft. Azure. Stream Analytics. CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Ele fornece as ferramentas de MSBuild, execução local e implantação que dão suporte ao processo de implantação e integração contínua de [projetos do Stream Analytics Visual Studio](stream-analytics-vs-tools.md). 
> [!NOTE]
> O pacote NuGet pode ser usado somente com a versão 2.3.0000.0 ou superior do Stream Analytics Tools para Visual Studio. Se você tiver projetos criados em versões anteriores do Visual Studio Tools, basta abri-los com a versão 2.3.0000.0 ou superior e salvar. Em seguida, os novos recursos são habilitados. 

Para obter mais informações, consulte [ferramentas de Stream Analytics para Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Como a experiência padrão do MSBuild do Visual Studio, para criar um projeto, você tem duas opções. Você pode clicar com o botão direito do mouse no projeto e escolher **Compilar**. Você também pode usar o **MSBuild** no pacote NuGet da linha de comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando um projeto do Visual Studio Stream Analytics é compilado com êxito, ele gera os dois arquivos de modelo de Azure Resource Manager a seguir na pasta **bin/[Debug/Retail]/Deploy** : 

*  Arquivo de modelo do Resource Manager

       [ProjectName].JobTemplate.json 

*  Arquivo de parâmetros do Resource Manager

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros padrão no arquivo Parameters. JSON são das configurações em seu projeto do Visual Studio. Se você quiser implantar em outro ambiente, substitua os parâmetros de acordo.

> [!NOTE]
> Para todas as credenciais, os valores padrão são definidos como NULL. Você precisa definir os valores antes de implantar na nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como [implantar com um arquivo de modelo do Resource Manager e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Saiba mais sobre como [usar um objeto como um parâmetro em um modelo do Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Para usar a identidade gerenciada para Azure Data Lake Store Gen1 como coletor de saída, você precisa fornecer acesso à entidade de serviço usando o PowerShell antes de implantar no Azure. Saiba mais sobre como [implantar ADLS Gen1 com identidade gerenciada com o modelo do Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Ferramenta de linha de comando

### <a name="build-the-project"></a>Compilar o projeto
O pacote NuGet tem uma ferramenta de linha de comando chamada **SA. exe**. Ele dá suporte à compilação do projeto e ao teste local em um computador arbitrário, que você pode usar em seu processo de integração contínua e de entrega contínua. 

Os arquivos de implantação são colocados sob o diretório atual por padrão. Você pode especificar o caminho de saída usando o seguinte parâmetro-OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Testar o script localmente

Se o projeto tiver especificado arquivos de entrada locais no Visual Studio, você poderá executar um teste de script automatizado usando o comando *localrun* . O resultado da saída é colocado sob o diretório atual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Gerar um arquivo de definição de trabalho para usar com a API Stream Analytics PowerShell

O comando *ARM* usa o modelo de trabalho e os arquivos de parâmetro de modelo de trabalho gerados por meio de Build como entrada. Em seguida, ele os combina em um arquivo JSON de definição de trabalho que pode ser usado com a API Stream Analytics PowerShell.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exemplo:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Passos Seguintes

* [Quickstart: Criar um trabalho de nuvem Azure Stream Analytics no Visual Studio](stream-analytics-quick-create-vs.md)
* [Testar consultas do Stream Analytics localmente com o Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Explorar Azure Stream Analytics trabalhos com o Visual Studio](stream-analytics-vs-tools.md)
