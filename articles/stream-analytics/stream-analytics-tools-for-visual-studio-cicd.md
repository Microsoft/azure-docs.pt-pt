---
title: Utilize o pacote Azure Stream Analytics CI/CD NuGet
description: Este artigo descreve como usar o pacote Azure Stream Analytics CI/CD NuGet para configurar um processo de integração e implementação contínua.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/15/2019
ms.openlocfilehash: 0b4356c74b2e0c1494456d5d1082efd7b8953a15
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98693380"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Utilize o pacote Azure Stream Analytics CI/CD NuGet para integração e desenvolvimento 
Este artigo descreve como utilizar o pacote Azure Stream Analytics CI/CD NuGet para configurar um processo de integração e implementação contínua.

Utilize a versão 2.3.0000.0 ou superior às [ferramentas Stream Analytics para](./stream-analytics-quick-create-vs.md) o Estúdio Visual obter suporte para MSBuild.

Um pacote NuGet está disponível: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Fornece as ferramentas de msBuild, run local e de implementação que suportam o processo de integração e implementação contínua de [projetos do Stream Analytics Visual Studio](stream-analytics-vs-tools.md). 
> [!NOTE]
> O pacote NuGet só pode ser utilizado com a versão 2.3.0000.0 ou acima do Stream Analytics Tools for Visual Studio. Se tiver projetos criados em versões anteriores de ferramentas Visual Studio, basta abri-los com a versão 2.3.0000.0 ou superior e guardar. Então as novas capacidades estão ativadas. 

Para obter mais informações, consulte [as ferramentas Stream Analytics para o Visual Studio](./stream-analytics-quick-create-vs.md).

## <a name="msbuild"></a>MSBuild
Tal como a experiência padrão do Visual Studio MSBuild, para construir um projeto tem duas opções. Pode clicar com o botão direito no projeto e, em seguida, escolher **Construir.** Também pode utilizar **o MSBuild** no pacote NuGet a partir da linha de comando.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Quando um projeto stream Analytics Visual Studio constrói com sucesso, gera os seguintes dois ficheiros de modelo do Azure Resource Manager no **lixo/[Debug/Retail]/Pasta de implantação:** 

* Ficheiro de modelo do gestor de recursos

   `[ProjectName].JobTemplate.json`

* Ficheiro de parâmetros do Gestor de Recursos
   
   `[ProjectName].JobTemplate.parameters.json`

Os parâmetros padrão no parameters.jsno ficheiro são das definições do seu projeto Visual Studio. Se pretender implantar-se noutro ambiente, substitua os parâmetros em conformidade.

> [!NOTE]
> Para todas as credenciais, os valores predefinidos são definidos como nulos. É-lhe exigido que **desloque** os valores antes de se deslocar para a nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Saiba mais sobre como [implementar com um ficheiro de modelo do Gestor de Recursos e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Saiba mais sobre como [usar um objeto como parâmetro num modelo de Gestor de Recursos.](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters)

Para utilizar a Identidade Gerida para a Azure Data Lake Store Gen1 como pia de saída, precisa de fornecer acesso ao principal do serviço usando o PowerShell antes de se deslocar para a Azure. Saiba mais sobre como [implementar a ADLS Gen1 com identidade gerida com o modelo de Gestor de Recursos.](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)


## <a name="command-line-tool"></a>Ferramenta de linha de comando

### <a name="build-the-project"></a>Compilar o projeto
O pacote NuGet tem uma ferramenta de linha de comando chamada **SA.exe**. Suporta a construção de projetos e testes locais numa máquina arbitrária, que pode utilizar no seu processo de integração contínua e entrega contínua. 

Os ficheiros de implantação são colocados por defeito no diretório atual. Pode especificar o caminho de saída utilizando o seguinte parâmetro -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Teste o guião localmente

Se o seu projeto tiver especificado ficheiros de entrada local no Visual Studio, pode executar um teste de script automatizado utilizando o comando *local.* O resultado da saída é colocado sob o diretório atual.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Gere um ficheiro de definição de emprego para usar com a API do Stream Analytics PowerShell

O comando do *braço* leva o modelo de trabalho e os ficheiros de parâmetros do modelo de trabalho gerados através da construção como entrada. Em seguida, combina-os numa definição de emprego ficheiro JSON que pode ser usado com a API de PowerShell Stream Analytics.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Exemplo:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho em nuvem Azure Stream Analytics em Estúdio Visual](stream-analytics-quick-create-vs.md)
* [Teste Stream Analytics consultas localmente com Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Explore os trabalhos do Azure Stream Analytics com o Visual Studio](stream-analytics-vs-tools.md)