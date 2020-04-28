---
title: Utilize o pacote NuGet CI/CD Azure Stream Analytics
description: Este artigo descreve como usar o pacote NuGet CI/CD CD do Azure Stream Analytics para configurar um processo contínuo de integração e implementação.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 11e68aaa7c70d4f888c0009bc28d9bb90f431f3b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75354449"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Utilize o pacote NuGet CI/CD Azur analytics do Azure Stream Analytics para integração e desenvolvimento 
Este artigo descreve como usar o pacote NuGet CI/CD CD do Azure Stream Analytics para configurar um processo contínuo de integração e implementação.

Utilize a versão 2.3.0000.0 ou acima das [ferramentas Stream Analytics para](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) o Estúdio Visual para obter suporte para mSBuild.

Um pacote NuGet está disponível: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Fornece as ferramentas MSBuild, run local e implementação que suportam o processo contínuo de integração e implementação de [projetos do Stream Analytics Visual Studio.](stream-analytics-vs-tools.md) 
> [!NOTE]
> O pacote NuGet só pode ser utilizado com a versão 2.3.0000.0 ou superior das Ferramentas Stream Analytics para Estúdio Visual. Se tiver projetos criados em versões anteriores de ferramentas do Visual Studio, basta abri-los com a versão 2.3.0000.0 ou acima e economizar. Em seguida, as novas capacidades estão ativadas. 

Para mais informações, consulte [as ferramentas Stream Analytics para O Estúdio Visual](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio).

## <a name="msbuild"></a>MSBuild
Tal como a experiência padrão do Estúdio Visual MSBuild, para construir um projeto tem duas opções. Pode clicar no projeto e, em seguida, escolher **Build**. Também pode utilizar o **MSBuild** no pacote NuGet a partir da linha de comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando um projeto stream analytics visual studio constrói com sucesso, gera os seguintes dois ficheiros de modelo de Gestor de Recursos Azure sob a **pasta bin/[Debug/Retail]/Deploy:** 

*  Ficheiro de modelo do Gestor de Recursos

       [ProjectName].JobTemplate.json 

*  Ficheiro de parâmetros do Gestor de Recursos

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros predefinidos no ficheiro parâmetros.json são das definições do seu projeto Visual Studio. Se quiser implantar para outro ambiente, substitua os parâmetros em conformidade.

> [!NOTE]
> Para todas as credenciais, os valores predefinidos são definidos como nulos. É **necessário** definir os valores antes de se deslocar para a nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como implementar com um ficheiro de modelo de Gestor de [Recursos e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Saiba mais sobre como [usar um objeto como parâmetro num modelo](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)de Gestor de Recursos .

Para utilizar a Identidade Gerida para azure Data Lake Store Gen1 como pia de saída, você precisa fornecer Acesso ao principal de serviço usando powerShell antes de ser implantado para O Azure. Saiba mais sobre como [implementar a ADLS Gen1 com identidade gerida com modelo](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)de Gestor de Recursos .


## <a name="command-line-tool"></a>Ferramenta de linha de comando

### <a name="build-the-project"></a>Compilar o projeto
O pacote NuGet tem uma ferramenta de linha de comando chamada **SA.exe**. Suporta a construção de projetos e testes locais numa máquina arbitrária, que pode utilizar no seu processo de integração contínua e entrega contínua. 

Os ficheiros de implantação são colocados sob o diretório atual por padrão. Pode especificar o caminho de saída utilizando o seguinte parâmetro -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Teste o guião localmente

Se o seu projeto tiver especificado ficheiros de entrada locais no Estúdio Visual, pode executar um teste de script automatizado utilizando o comando *local.* O resultado da saída é colocado sob o diretório atual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Gerar um ficheiro de definição de trabalho para usar com a API Stream Analytics PowerShell

O comando do *braço* pega no modelo de trabalho e nos ficheiros de parâmetros do modelo de trabalho gerados através da construção como entrada. Em seguida, combina-os num ficheiro JSON de definição de trabalho que pode ser usado com a API Stream Analytics PowerShell.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exemplo:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho de nuvem Azure Stream Analytics no Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Test Stream Analytics consultas localmente com Estúdio Visual](stream-analytics-vs-tools-local-run.md)
* [Explore os trabalhos da Azure Stream Analytics com o Estúdio Visual](stream-analytics-vs-tools.md)
