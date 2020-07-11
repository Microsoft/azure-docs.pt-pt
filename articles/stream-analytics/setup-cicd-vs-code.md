---
title: Implementar um trabalho Azure Stream Analytics usando pacote de npm CI/CD
description: Este artigo descreve como usar o pacote DE Azure Stream Analytics CI/CD npm para configurar um processo de integração e implementação contínua.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 01/28/2020
ms.openlocfilehash: 75db20bdb746e7d15bef56ce7ac0a064993d3f3a
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86187766"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Implementar um trabalho Azure Stream Analytics usando pacote de npm CI/CD 

Pode utilizar o pacote Azure Stream Analytics CI/CD npm para configurar um processo de integração e implementação contínua para os seus trabalhos stream Analytics. Este artigo descreve como utilizar o pacote npm em geral com qualquer sistema CI/CD, bem como instruções específicas para a implementação com gasodutos Azure.

Para obter mais informações sobre a implementação com o Powershell, consulte [implementar com um ficheiro de modelo do Gestor de Recursos e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Também pode aprender mais sobre como [usar um objeto como parâmetro num modelo de Gestor de Recursos.](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)

## <a name="build-the-vs-code-project"></a>Construir o projeto VS Code

Você pode ativar a integração contínua e implementação para trabalhos Azure Stream Analytics usando o pacote de npm **asa-streamanalytics-cicd.** O pacote npm fornece as ferramentas para gerar modelos de Gestor de Recursos Azure de [projetos stream Analytics Visual Studio Code](quick-create-vs-code.md). Pode ser usado no Windows, macOS e Linux sem instalar o Código do Estúdio Visual.

Você pode [baixar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente, ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) através do `npm install -g azure-streamanalytics-cicd` comando. Esta é a abordagem recomendada, que também pode ser usada numa tarefa de script PowerShell ou Azure CLI de um gasoduto de construção em **Gasodutos Azure**.

Depois de ter instalado a embalagem, utilize o seguinte comando para des output os modelos do Gestor de Recursos Azure. O argumento **scriptPath** é o caminho absoluto para o ficheiro **asaql** no seu projeto. Certifique-se de que a asaproj.jse JobConfig.jsnos ficheiros está na mesma pasta com o ficheiro do script. Se o **outputPath** não for especificado, os modelos serão colocados na pasta **Implementar** sob a pasta do caixote do **lixo** do projeto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemplo (no macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando um projeto stream Analytics Visual Studio Code se constrói com sucesso, gera os seguintes dois ficheiros de modelo do Gestor de Recursos Azure no **lixo/[Debug/Retail]/Pasta de implantação:** 

* Ficheiro de modelo do gestor de recursos

   `[ProjectName].JobTemplate.json`

* Ficheiro de parâmetros do Gestor de Recursos

   `[ProjectName].JobTemplate.parameters.json`   

Os parâmetros padrão no parameters.jsno ficheiro são das definições do seu projeto Visual Studio Code. Se pretender implantar-se noutro ambiente, substitua os parâmetros em conformidade.

> [!NOTE]
> Para todas as credenciais, os valores predefinidos são definidos como nulos. É-lhe exigido que **desloque** os valores antes de se deslocar para a nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Implementar com os Pipelines do Azure

Esta secção detalha como criar gasodutos Azure [construir](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) e [libertar](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) gasodutos usando npm.

Abra um navegador web e navegue para o seu projeto Azure Stream Analytics Visual Studio Code.

1. Sob **Pipelines** no menu de navegação à esquerda, selecione **Builds**. Em seguida, selecione **Novo oleoduto**

   ![Criar novo Gasoduto Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selecione **Utilize o editor clássico** para criar um oleoduto sem YAML.

3. Selecione o seu tipo de origem, projeto de equipa e repositório. Em seguida, selecione **Continuar**.

   ![Selecione projeto Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Na página do modelo Escolha uma página **de modelo,** selecione **Trabalho Vazio**.

### <a name="add-npm-task"></a>Adicionar tarefa npm

1. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Introduza "npm" na pesquisa de tarefas e selecione **npm**.

   ![Selecione tarefa npm](./media/setup-cicd-vs-code/search-npm.png)

2. Dê à tarefa um **nome de Exibição**. Altere a opção **Comando** para *personalizar* e insira o seguinte comando no **Comando e nos argumentos**. Deixe as restantes opções predefinidos.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Introduza configurações para tarefa npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Adicionar tarefa de linha de comando

1. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Procurar **linha de comando**.

2. Dê à tarefa um **nome de Exibição** e introduza o seguinte script. Modifique o script com o nome do seu repositório e nome do projeto.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Introduza configurações para a tarefa da linha de comando](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Adicionar tarefa de ficheiros de cópia

1. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Procurar **por ficheiros Copy**. Em seguida, introduza as seguintes configurações.

   |Parâmetro|Entrada|
   |-|-|
   |Nome a apresentar|Copy Files to: $(build.artifactstagingdirectory)|
   |Pasta de Origem|`$(system.defaultworkingdirectory)`| 
   |Conteúdos| `**\Deploy\**` |
   |Pasta-alvo| `$(build.artifactstagingdirectory)`|

   ![Introduza configurações para a tarefa de cópia](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Adicionar Publicar construir tarefa de artefactos

1. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Procure por **Publicar construa artefactos** e selecione a opção com o ícone de seta preta. 

2. Não altere nenhuma das configurações predefinições.

### <a name="save-and-run"></a>Salvar e correr

Uma vez terminada a adição da npm, linha de comando, ficheiros de cópia e publicação de tarefas de artefactos de construção, **selecione Save & fila**. Quando lhe for solicitado, introduza um comentário de salvamento e **selecione Save and run**.

## <a name="release-with-azure-pipelines"></a>Lançamento com gasodutos Azure

Abra um navegador web e navegue para o seu projeto Azure Stream Analytics Visual Studio Code.

1. Sob **pipelines** no menu de navegação à esquerda, selecione **Versões**. Em seguida, selecione **Novo oleoduto**.

2. Selecione **começar com um trabalho vazio**.

3. Na caixa **de Artefactos,** selecione **+ Adicione um artefacto.** Em **Fonte**, selecione o pipeline de construção que acabou de criar e selecione **Adicionar**.

   ![Insira a construção de artefactos de gasoduto](./media/setup-cicd-vs-code/build-artifact.png)

4. Alterar o nome da **fase 1** para **implementar o trabalho para o ambiente de teste**.

5. Adicione um novo estágio e nomeie-o **Implementar trabalho para o ambiente de produção.**

### <a name="add-tasks"></a>Adicionar tarefas

1. A partir do abandono das tarefas, **selecione Implementar trabalho para testar ambiente**. 

2. Selecione o **+** trabalho ao lado do **Agente** e procure a *implementação do grupo de recursos Azure*. Introduza os seguintes parâmetros:

   |Definição|Valor|
   |-|-|
   |Nome a apresentar| *Implementar myASAJob*|
   |Subscrição do Azure| Escolha a sua subscrição.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de teste que conterá o seu trabalho stream Analytics.|
   |Localização|Escolha a localização do seu grupo de recursos de teste.|
   |Localização do modelo| *Artefacto ligado*|
   |Modelo| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.jsem |
   |Parâmetros do modelo|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.jsem|
   |Substituir os parâmetros do modelo|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Modo de implantação|Incremental|

3. A partir do abandono das tarefas, **selecione Implementar trabalho para o ambiente de produção**.

4. Selecione o **+** trabalho ao lado do **Agente** e procure a *implementação do grupo de recursos Azure*. Introduza os seguintes parâmetros:

   |Definição|Valor|
   |-|-|
   |Nome a apresentar| *Implementar myASAJob*|
   |Subscrição do Azure| Escolha a sua subscrição.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de produção que conterá o seu trabalho stream Analytics.|
   |Localização|Escolha a localização do seu grupo de recursos de produção.|
   |Localização do modelo| *Artefacto ligado*|
   |Modelo| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.jsem |
   |Parâmetros do modelo|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.jsem|
   |Substituir os parâmetros do modelo|-Input_IoTHub1_iotHubNamespace $(nome eventhub)|
   |Modo de implantação|Incremental|

### <a name="create-release"></a>Criar lançamento

Para criar um desbloqueio, selecione **Criar o desbloqueio** no canto superior direito.

![Criar um desbloqueio utilizando gasodutos Azure](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Recursos adicionais

Para utilizar a Identidade Gerida para a Azure Data Lake Store Gen1 como pia de saída, precisa de fornecer acesso ao principal do serviço usando o PowerShell antes de se deslocar para a Azure. Saiba mais sobre como [implementar a ADLS Gen1 com identidade gerida com o modelo de Gestor de Recursos.](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)


## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Criar um trabalho em nuvem Azure Stream Analytics em Código de Estúdio Visual (Pré-visualização)](quick-create-vs-code.md)
* [Teste Stream Analytics consultas localmente com Código de Estúdio Visual (Pré-visualização)](visual-studio-code-local-run.md)
* [Explore a Azure Stream Analytics com Código de Estúdio Visual (Pré-visualização)](visual-studio-code-explore-jobs.md)
