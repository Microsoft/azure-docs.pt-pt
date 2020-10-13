---
title: Utilize Azure DevOps para criar um pipeline CI/CD para um trabalho de Stream Analytics
description: Este artigo descreve como configurar um pipeline de integração e implantação contínua (CI/CD) para um trabalho de Azure Stream Analytics em Azure DevOps
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: d9b6dfc977aab7d8907b5d3c3851a22f96227d78
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91757763"
---
# <a name="use-azure-devops-to-create-a-cicd-pipeline-for-a-stream-analytics-job"></a>Utilize Azure DevOps para criar um pipeline CI/CD para um trabalho de Stream Analytics

Neste artigo, aprende-se a criar a Azure DevOps [a construir](/azure/devops/pipelines/get-started/pipelines-get-started) e [a libertar](/azure/devops/pipelines/release/define-multistage-release-process) oleodutos utilizando ferramentas CI/CD do Azure Stream Analytics.

## <a name="commit-your-stream-analytics-project"></a>Comprometa o seu projeto Stream Analytics

Antes de começar, comprometa os seus projetos stream analytics completos como ficheiros de origem para um repositório [Azure DevOps.](/azure/devops/user-guide/source-control) Pode fazer referência a este [repositório](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo) de amostras e [código fonte de projeto Stream Analytics](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) em Pipelines Azure.

Os passos deste artigo usam um projeto stream Analytics Visual Studio Code. Se estiver a utilizar um projeto visual Studio, siga os passos em [construções, testes e implementações de um trabalho Azure Stream Analytics utilizando ferramentas CI/CD](cicd-tools.md).

## <a name="create-a-build-pipeline"></a>Criar um pipeline de compilação

Nesta secção, aprende-se a criar um oleoduto de construção. Pode fazer referência a esta amostra [de construção automática e teste do gasoduto](https://dev.azure.com/wenyzou/_git/azure-streamanalytics-cicd-demo?path=%2FmyASAProject) em Azure DevOps.

1. Abra um navegador web e navegue para o seu projeto em Azure DevOps.  

1. Sob **Pipelines** no menu de navegação à esquerda, selecione **Builds**. Em seguida, selecione **Novo oleoduto**.

   :::image type="content" source="media/set-up-cicd-pipeline/new-pipeline.png" alt-text="Criar novo Gasoduto Azure":::

1. Selecione **Utilize o editor clássico** para criar um oleoduto sem YAML.

1. Selecione o seu tipo de origem, projeto de equipa e repositório. Em seguida, **selecione Continue**.

   :::image type="content" source="media/set-up-cicd-pipeline/select-repo.png" alt-text="Criar novo Gasoduto Azure":::

1. Na página do modelo Escolha uma página **de modelo,** selecione **Trabalho Vazio**.

## <a name="install-npm-package"></a>Instalar pacote npm

1. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Introduza *npm* na pesquisa de tarefas e selecione **npm**.

   :::image type="content" source="media/set-up-cicd-pipeline/search-npm.png" alt-text="Criar novo Gasoduto Azure":::

2. Dê à tarefa um **nome de Exibição**. Altere a opção **Comando** para *personalizar* e insira o seguinte comando no **Comando e nos argumentos**. Deixe as restantes opções predefinidos.

   ```bash
   install -g azure-streamanalytics-cicd
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/npm-config.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="add-a-build-task"></a>Adicione uma tarefa de construção

1. Na página **'Variáveis',** selecione **+ Adicionar** **variáveis pipeline**. Adicione as seguintes variáveis. Desaça os seguintes valores de acordo com a sua preferência:

   |Nome da variável|Valor|
   |-|-|
   |projectRootPath|[O Seu Nome DeProjecto]|
   |saídaapata|Saída|
   |implementarPata|Implementação|

2. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Procurar **linha de comando**.

3. Dê à tarefa um **nome de Exibição** e introduza o seguinte script. Modifique o script com o nome do seu repositório e nome do projeto.

   ```bash
   azure-streamanalytics-cicd build -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(deployPath)
   ```

   A imagem abaixo utiliza um projeto Stream Analytics Visual Studio Code como exemplo.

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-build.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="add-a-test-task"></a>Adicione uma tarefa de teste

1. Na página **'Variáveis',** selecione **+ Adicionar** **variáveis pipeline**. Adicione as seguintes variáveis. Modifique os valores com o seu percurso de saída e nome de repositório.

   |Nome da variável|Valor|
   |-|-|
   |testPath|Teste|

   :::image type="content" source="media/set-up-cicd-pipeline/pipeline-variables-test.png" alt-text="Criar novo Gasoduto Azure":::

2. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Procurar **linha de comando**.

3. Dê à tarefa um **nome de Exibição** e introduza o seguinte script. Modifique o script com o nome do ficheiro do seu projeto e o caminho para o ficheiro de configuração de teste. 

   Consulte [instruções de teste automatizadas](cicd-tools.md#automated-test) para obter informações sobre como adicionar e configurar os casos de teste.

   ```bash
   azure-streamanalytics-cicd test -project $(projectRootPath)/asaproj.json -outputpath $(projectRootPath)/$(outputPath)/$(testPath) -testConfigPath $(projectRootPath)/test/testConfig.json 
   ```

   :::image type="content" source="media/set-up-cicd-pipeline/command-line-config-test.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="add-a-copy-files-task"></a>Adicionar uma tarefa de ficheiros de cópia

É necessário adicionar uma tarefa de ficheiro de cópia para copiar o ficheiro de resumo do teste e os ficheiros de modelo do Azure Resource Manager na pasta de artefactos. 

1. Na página **Tarefas,** selecione o **+** trabalho seguinte ao Agente **1**. Procurar **por ficheiros Copy**. Em seguida, introduza as seguintes configurações. Ao atribuir `**` ao **Conteúdo,** todos os ficheiros dos resultados dos testes são copiados.

   |Parâmetro|Input|
   |-|-|
   |Nome a apresentar|Copy Files to: $(build.artifactstagingdirectory)|
   |Pasta de Origem|`$(system.defaultworkingdirectory)/$(outputPath)/`|
   |Conteúdos| `**` |
   |Pasta-alvo| `$(build.artifactstagingdirectory)`|

2. Expandir **opções de controlo**. Selecione **Mesmo que uma tarefa anterior tenha falhado, a menos que a construção tenha sido cancelada** em Executar esta **tarefa**.

   :::image type="content" source="media/set-up-cicd-pipeline/copy-config.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="add-a-publish-build-artifacts-task"></a>Adicione uma tarefa de construção de artefactos de construção de publicação

1. Na página **Tarefas,** selecione o sinal de mais ao lado **do trabalho do Agente 1**. Procure por **Publicar construa artefactos** e selecione a opção com o ícone de seta preta.

2. Expandir **opções de controlo**. Selecione **Mesmo que uma tarefa anterior tenha falhado, a menos que a construção tenha sido cancelada** em Executar esta **tarefa**.

   :::image type="content" source="media/set-up-cicd-pipeline/publish-config.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="save-and-run"></a>Salvar e correr

Uma vez terminada a adição do pacote npm, linha de comando, ficheiros de cópia e publicação de tarefas de artefactos de construção, **selecione Save & fila**. Quando lhe for solicitado, introduza um comentário de salvamento e **selecione Save and run**. Pode descarregar os resultados dos testes a partir da página **sumária** do pipeline.

## <a name="check-the-build-and-test-results"></a>Verifique os resultados da construção e dos testes

O ficheiro de resumo do teste e os ficheiros de modelo do Gestor de Recursos Azure podem ser encontrados na pasta **Publicada.**

   :::image type="content" source="media/set-up-cicd-pipeline/check-build-test-result.png" alt-text="Criar novo Gasoduto Azure":::

   :::image type="content" source="media/set-up-cicd-pipeline/check-drop-folder.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="release-with-azure-pipelines"></a>Lançamento com gasodutos Azure

Nesta secção, aprende-se a criar um oleoduto de libertação. Pode fazer referência a este [gasoduto de libertação de](https://dev.azure.com/wenyzou/azure-streamanalytics-cicd-demo/_release?_a=releases&view=mine&definitionId=2&preserve-view=true) amostras em Azure DevOps.

Abra um navegador web e navegue para o seu projeto Azure Stream Analytics Visual Studio Code.

1. Sob **pipelines** no menu de navegação à esquerda, selecione **Versões**. Em seguida, selecione **Novo oleoduto**.

2. Selecione **começar com um trabalho vazio**.

3. Na caixa **de Artefactos,** selecione **+ Adicione um artefacto.** Em **Fonte**, selecione o pipeline de construção que criou e selecione **Adicionar**.

   :::image type="content" source="media/set-up-cicd-pipeline/build-artifact.png" alt-text="Criar novo Gasoduto Azure":::

4. Alterar o nome da **fase 1** para **implementar o trabalho para o ambiente de teste**.

5. Adicione um novo estágio e nomeie-o **Implementar trabalho para o ambiente de produção.**

### <a name="add-deploy-tasks"></a>Adicionar tarefas de implementação

1. A partir do abandono das tarefas, **selecione Implementar trabalho para testar ambiente**.

2. Selecione o **+** trabalho ao lado do **Agente** e procure a **implementação do modelo ARM**. Introduza os seguintes parâmetros:

   |Parâmetro|Valor|
   |-|-|
   |Nome a apresentar| *Implementar o myASAProject*|
   |Subscrição do Azure| Escolha a sua subscrição.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de teste que conterá o seu trabalho stream Analytics.|
   |Localização|Escolha a localização do seu grupo de recursos de teste.|
   |Localização do modelo| Artefacto ligado|
   |Modelo| $(System.DefaultWorkingDirect)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |Parâmetros do modelo|$(System.DefaultWorkingDirect)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |Substituir os parâmetros do modelo|- <arm_template_parameter> "o seu valor". Pode definir os parâmetros utilizando **variáveis.**|
   |Modo de implantação|Incremental|

3. A partir do abandono das tarefas, **selecione Implementar trabalho para o ambiente de produção**.

4. Selecione o **+** trabalho ao lado do **Agente** e procure a *implementação do modelo ARM*. Introduza os seguintes parâmetros:

   |Parâmetro|Valor|
   |-|-|
   |Nome a apresentar| *Implementar o myASAProject*|
   |Subscrição do Azure| Escolha a sua subscrição.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de produção que conterá o seu trabalho stream Analytics.|
   |Localização|Escolha a localização do seu grupo de recursos de produção.|
   |Localização do modelo| *Artefacto ligado*|
   |Modelo| $(System.DefaultWorkingDirect)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.json |
   |Parâmetros do modelo|$(System.DefaultWorkingDirect)/_azure-streamanalytics-cicd-demo-CI-Deploy/drop/myASAProject.JobTemplate.parameters.json |
   |Substituir os parâmetros do modelo|- <arm_template_parameter> "o seu valor"|
   |Modo de implantação|Incremental|

### <a name="create-a-release"></a>Criar um lançamento

Para criar um desbloqueio, selecione **Criar o desbloqueio** no canto superior direito.

:::image type="content" source="media/set-up-cicd-pipeline/create-release.png" alt-text="Criar novo Gasoduto Azure":::

## <a name="next-steps"></a>Passos seguintes

* [Integração contínua e implantação contínua para Azure Stream Analytics](cicd-overview.md)
* [Automatizar a construção, teste e implantação de um trabalho Azure Stream Analytics utilizando ferramentas CI/CD](cicd-tools.md)