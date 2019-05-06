---
title: Explorar o Azure Stream Analytics com o Visual Studio Code (pré-visualização)
description: Este artigo mostra-lhe como exportar uma tarefa do Azure Stream Analytics para um projeto do local, lista de tarefas e entidades de tarefa de exibição e definir um pipeline de CI/CD para a tarefa de Stream Analytics.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.date: 05/06/2019
ms.topic: conceptual
ms.openlocfilehash: 640a81fcb94194e2e907e9339f016bd35e279fdd
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65079214"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Explorar o Azure Stream Analytics com o Visual Studio Code (pré-visualização)

O Azure Stream Analytics para a extensão do Visual Studio Code fornece aos desenvolvedores uma experiência leve para gerenciar seus trabalhos do Stream Analytics. Com a extensão do Azure Stream Analytics, pode:

- [Criar](quick-create-vs-code.md), iniciar e parar tarefas
- Exportar tarefas existentes para um projeto de local
- Executar consultas localmente
- Configure um pipeline CI/CD

## <a name="export-a-job-to-a-local-project"></a>Exportar uma tarefa para um projeto de local

Para exportar uma tarefa para um projeto local, localize a tarefa que pretende exportar na **Explorer do Stream Analytics** no Visual Studio code. Em seguida, selecione uma pasta para o seu projeto. O projeto é exportado para a pasta selecionada e, pode continuar a gerir a tarefa a partir do Visual Studio Code. Para obter mais informações sobre como usar o Visual Studio Code para gerir as tarefas do Stream Analytics, consulte o Visual Studio Code [guia de introdução](quick-create-vs-code.md).

![Tarefa de exportação do ASA no Visual Studio Code](./media/vs-code-how-to/export-job.png)

## <a name="run-queries-locally"></a>Executar consultas localmente

Pode utilizar a extensão do Azure Stream Analytics para Visual Studio Code para testar as suas tarefas do Stream Analytics localmente com dados de exemplo.

1. Depois de criar a tarefa de Stream Analytics, utilize **Ctrl + Shift + P** para abrir o palete de comando. Em seguida, escreva e selecione **ASA: Adicionar entrada de**.

    ![Adicionar entrada de ASA no Visual Studio code](./media/vs-code-how-to/add-input.png)

2. Selecione **entrada Local**.

    ![Adicionar entrada de local do ASA no Visual Studio code](./media/vs-code-how-to/add-local-input.png)

3. Selecione **+ nova entrada Local**.

    ![Adicionar um ASA novo local de entrada no Visual Studio code](./media/vs-code-how-to/add-new-local-input.png)

4. Introduza o alias de entrada mesmo que utilizou na sua consulta.

    ![Adicionar um novo alias entrado local ASA](./media/vs-code-how-to/new-local-input-alias.png)

5. Na **LocalInput_DefaultLocalStream.json** de ficheiros, introduza o caminho do ficheiro onde está localizado o ficheiro de dados local.

    ![Introduza o caminho do ficheiro local no Visual Studio](./media/vs-code-how-to/local-file-path.png)

6. Regresse ao seu editor de consultas e selecione **executar localmente**.

    ![Selecionar executar localmente no editor de consultas](./media/vs-code-how-to/run-locally.png)

## <a name="set-up-a-cicd-pipeline"></a>Configure um pipeline CI/CD

Pode ativar a integração contínua e implementação para as tarefas de Azure Stream Analytics com o **ferramentas do asa-CI/CD** pacote NPM. O pacote NPM fornece as ferramentas para a implementação automática de projetos do Stream Analytics Visual Studio Code. Ele pode ser usado no Windows, macOS e Linux sem instalar o Visual Studio Code.

Assim que tiver [transferido o pacote](https://usqldownload.blob.core.windows.net/ext/asa/asa-cicd-0.0.1-preview-beta.tar), utilize o seguinte comando para exportar os modelos Azure Resource Manager. Se o **outputPath** não for especificado, os modelos vão ser colocados a **Deploy** pasta dentro do projeto **bin** pasta.

```powershell
asa-cicd build -scriptPath <scriptFullPath> -outputPath <outputPath>
```

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma tarefa de cloud do Azure Stream Analytics no Visual Studio Code (pré-visualização)](quick-create-vs-code.md)