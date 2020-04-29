---
title: Implemente um trabalho de Análise de Fluxo Salédis Azure utilizando pacote ci/CD npm
description: Este artigo descreve como utilizar o pacote ci/CD npm do Azure Stream Analytics para configurar um processo contínuo de integração e implementação.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: deb6c2439cc84f196b7f42fd9f49d3ebfd057cbb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76962226"
---
# <a name="deploy-an-azure-stream-analytics-job-using-cicd-npm-package"></a>Implemente um trabalho de Análise de Fluxo Salédis Azure utilizando pacote ci/CD npm 

Pode utilizar o pacote de npm CI/CD do Azure Stream Analytics para configurar um processo de integração e implantação contínuo para os seus trabalhos de Streaming Analytics. Este artigo descreve como utilizar o pacote npm em geral com qualquer sistema CI/CD, bem como instruções específicas para a implantação com pipelines Azure.

Para obter mais informações sobre a implementação com powershell, consulte a implementação com um ficheiro de [modelo de Gestor de Recursos e Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Também pode aprender mais sobre como [usar um objeto como parâmetro num modelo](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters)de Gestor de Recursos .

## <a name="build-the-vs-code-project"></a>Construa o projeto vs Código

Pode permitir a integração e implantação contínuas para trabalhos de Azure Stream Analytics utilizando o pacote **npm asa-streamanalytics-cicd** npm. O pacote npm fornece as ferramentas para gerar modelos de Gestor de Recursos Azure de [projetos de Código de Estúdio Visual Stream Analytics.](quick-create-vs-code.md) Pode ser usado em Windows, macOS e Linux sem instalar o Visual Studio Code.

Pode [descarregar o pacote](https://www.npmjs.com/package/azure-streamanalytics-cicd) diretamente ou instalá-lo [globalmente](https://docs.npmjs.com/downloading-and-installing-packages-globally) através do `npm install -g azure-streamanalytics-cicd` comando. Esta é a abordagem recomendada, que também pode ser usada numa tarefa de script PowerShell ou Azure CLI de um pipeline de construção em **Pipelines Azure**.

Depois de ter instalado a embalagem, utilize o seguinte comando para obter os modelos do Gestor de Recursos Azure. O argumento **scriptPath** é o caminho absoluto para o arquivo **asaql** no seu projeto. Certifique-se de que os ficheiros asaproj.json e JobConfig.json estão na mesma pasta com o ficheiro script. Se o **outputPath** não for especificado, os modelos serão colocados na pasta **Deploy** sob a pasta do **lixo** do projeto.

```powershell
azure-streamanalytics-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```
Exemplo (no macOS)
```powershell
azure-streamanalytics-cicd build -scriptPath "/Users/roger/projects/samplejob/script.asaql" 
```

Quando um projeto stream analytics visual studio code constrói com sucesso, gera os seguintes dois ficheiros de modelo de Gestor de Recursos Azure sob a pasta **bin/[Debug/Retail]/Deploy:** 

*  Ficheiro de modelo do Gestor de Recursos

       [ProjectName].JobTemplate.json 

*  Ficheiro de parâmetros do Gestor de Recursos

       [ProjectName].JobTemplate.parameters.json   

Os parâmetros predefinidos no ficheiro parâmetros.json são das definições do seu projeto Visual Studio Code. Se quiser implantar para outro ambiente, substitua os parâmetros em conformidade.

> [!NOTE]
> Para todas as credenciais, os valores predefinidos são definidos como nulos. É **necessário** definir os valores antes de se deslocar para a nuvem.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

## <a name="deploy-with-azure-pipelines"></a>Implementar com os Pipelines do Azure

Esta secção detalha como criar oleodutos Azure [para construir](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav) e [lançar](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) oleodutos usando o npm.

Abra um navegador web e navegue para o seu projeto de Estúdio Visual Azure Stream Analytics.

1. Em **Pipelines** no menu de navegação à esquerda, selecione **Builds**. Em seguida, selecione **Novo oleoduto**

   ![Criar novo Oleoduto Azure](./media/setup-cicd-vs-code/new-pipeline.png)

2. Selecione **Use o editor clássico** para criar um pipeline sem YAML.

3. Selecione o seu tipo de origem, projeto de equipa e repositório. Em seguida, selecione **Continuar**.

   ![Selecione projeto Azure Stream Analytics](./media/setup-cicd-vs-code/select-repo.png)

4. Na página Escolha uma página de **modelo,** selecione **Trabalho Vazio**.

### <a name="add-npm-task"></a>Adicionar tarefa npm

1. Na página **Tarefas,** selecione o sinal de mais ao lado do **trabalho do Agente 1**. Introduza "npm" na pesquisa de tarefas e selecione **npm**.

   ![Selecione tarefa npm](./media/setup-cicd-vs-code/search-npm.png)

2. Dê à tarefa um **nome de exibição**. Altere a opção **Comando** para *personalizar* e introduza o seguinte comando em **Comando e argumentos**. Deixe as restantes opções predefinidas.

   ```cmd
   install -g azure-streamanalytics-cicd
   ```

   ![Introduza configurações para tarefa npm](./media/setup-cicd-vs-code/npm-config.png)

### <a name="add-command-line-task"></a>Adicionar tarefa de linha de comando

1. Na página **Tarefas,** selecione o sinal de mais ao lado do **trabalho do Agente 1**. Procurar a **linha comando.**

2. Dê à tarefa um **nome de exibição** e introduza o seguinte script. Modifique o script com o seu nome de repositório e nome do projeto.

   ```cmd
   azure-streamanalytics-cicd build -scriptPath $(Build.SourcesDirectory)/myASAProject/myASAProj.asaql
   ```

   ![Introduza configurações para tarefa de linha de comando](./media/setup-cicd-vs-code/commandline-config.png)

### <a name="add-copy-files-task"></a>Adicionar tarefa de ficheiros de cópia

1. Na página **Tarefas,** selecione o sinal de mais ao lado do **trabalho do Agente 1**. Pesquisar **ficheiros Copy**. Em seguida, introduza as seguintes configurações.

   |Parâmetro|Input|
   |-|-|
   |Nome a apresentar|Copiar Ficheiros para: $(build.artifactstagingdirectory)|
   |Pasta fonte|`$(system.defaultworkingdirectory)`| 
   |Conteúdos| `**\Deploy\**` |
   |Pasta-alvo| `$(build.artifactstagingdirectory)`|

   ![Introduza configurações para tarefa de cópia](./media/setup-cicd-vs-code/copy-config.png)

### <a name="add-publish-build-artifacts-task"></a>Adicionar publicar tarefa de artefactos de construção

1. Na página **Tarefas,** selecione o sinal de mais ao lado do **trabalho do Agente 1**. Procure **por Publicar construir artefactos** e selecione a opção com o ícone de seta preta. 

2. Não altere nenhuma das configurações predefinidas.

### <a name="save-and-run"></a>Salvar e correr

Uma vez concluída a adição da npm, linha de comando, ficheiros de cópias e publicar tarefas de construção de artefactos, selecione **Guardar & fila**. Quando for solicitado, introduza um comentário de salvamento e selecione **Guardar e executar**.

## <a name="release-with-azure-pipelines"></a>Lançamento com Oleodutos Azure

Abra um navegador web e navegue para o seu projeto de Estúdio Visual Azure Stream Analytics.

1. Em **Pipelines** no menu de navegação à esquerda, selecione **Lançamentos**. Em seguida, selecione **Novo pipeline**.

2. Selecione **começar com um trabalho Vazio**.

3. Na caixa **artefactos,** selecione **+ Adicione um artefacto**. Em **Origem,** selecione o pipeline de construção que acabou de criar e selecione **Adicionar**.

   ![Insira o artefacto do oleoduto de construção](./media/setup-cicd-vs-code/build-artifact.png)

4. Altere o nome da **Fase 1** para implementar o trabalho para testar o **ambiente**.

5. Adicione uma nova fase e nomeie-o **Implementar trabalho para o ambiente de produção.**

### <a name="add-tasks"></a>Adicionar tarefas

1. A partir das tarefas de dropdown, selecione **Implementar trabalho para testar ambiente**. 

2. Selecione o **+** **trabalho** ao lado do Agente e procure a implantação do grupo de recursos *Azure*. Introduza os seguintes parâmetros:

   |Definição|Valor|
   |-|-|
   |Nome a apresentar| *Implementar o myASAJob*|
   |Subscrição do Azure| Escolha a sua subscrição.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de teste que contenha o seu trabalho de Stream Analytics.|
   |Localização|Escolha a localização do seu grupo de recursos de teste.|
   |Localização do modelo| *Artefacto ligado*|
   |Modelo| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parâmetros do modelo|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Substituir os parâmetros do modelo|-Input_IoTHub1_iotHubNamespace $(test_eventhubname)|
   |Modo de implantação|Incremental|

3. A partir das tarefas de dropdown, selecione **Implementar trabalho para o ambiente**de produção .

4. Selecione o **+** **trabalho** ao lado do Agente e procure a implantação do grupo de recursos *Azure*. Introduza os seguintes parâmetros:

   |Definição|Valor|
   |-|-|
   |Nome a apresentar| *Implementar o myASAJob*|
   |Subscrição do Azure| Escolha a sua subscrição.|
   |Ação| *Criar ou atualizar o grupo de recursos*|
   |Grupo de recursos| Escolha um nome para o grupo de recursos de produção que contenha o seu trabalho de Stream Analytics.|
   |Localização|Escolha a localização do seu grupo de recursos de produção.|
   |Localização do modelo| *Artefacto ligado*|
   |Modelo| $(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.json |
   |Parâmetros do modelo|($(Build.ArtifactStagingDirectory)\drop\myASAJob.JobTemplate.parameters.json|
   |Substituir os parâmetros do modelo|-Input_IoTHub1_iotHubNamespace $(eventhubname)|
   |Modo de implantação|Incremental|

### <a name="create-release"></a>Criar lançamento

Para criar um lançamento, selecione **Criar lançamento** no canto superior direito.

![Criar uma versão usando pipelines Azure](./media/setup-cicd-vs-code/create-release.png)

## <a name="additional-resources"></a>Recursos adicionais

Para utilizar a Identidade Gerida para azure Data Lake Store Gen1 como pia de saída, você precisa fornecer Acesso ao principal de serviço usando powerShell antes de ser implantado para O Azure. Saiba mais sobre como [implementar a ADLS Gen1 com identidade gerida com modelo](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)de Gestor de Recursos .


## <a name="next-steps"></a>Passos seguintes

* [Quickstart: Crie um trabalho em nuvem Azure Stream Analytics em Código de Estúdio Visual (Pré-visualização)](quick-create-vs-code.md)
* [Test Stream Analytics consultas localmente com Código de Estúdio Visual (Pré-visualização)](visual-studio-code-local-run.md)
* [Explore o Azure Stream Analytics com o Código do Estúdio Visual (Pré-visualização)](visual-studio-code-explore-jobs.md)
