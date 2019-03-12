---
title: Criar um projeto de blocos de notas do Azure com um ambiente personalizado
description: Crie um novo projeto em blocos de notas do Azure que está configurado com um conjunto específico de pacotes instalados e scripts de inicialização.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: e049f591-27a7-440f-a1a3-c5bef25e8a28
ms.service: azure
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 35b5f1e29ae125dcac79c278578e900a73ffc7be
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771397"
---
# <a name="quickstart-create-a-project-with-a-custom-environment"></a>Início rápido: Criar um projeto com um ambiente personalizado

Um projeto em blocos de notas do Azure é uma coleção de arquivos, como blocos de notas, ficheiros de dados, documentação, imagens e assim por diante, juntamente com um ambiente que pode ser configurado com os comandos de configuração específicos. Ao definir o ambiente com o projeto, qualquer pessoa que clona o projeto em sua própria conta de blocos de notas do Azure tem todas as informações que precisam para recriar o ambiente necessário.

## <a name="create-a-project"></a>Criar um projeto

1. Aceda a [blocos de notas do Azure](https://notebooks.azure.com) e iniciar sessão. (Para obter detalhes, consulte [início rápido - início de sessão para blocos de notas do Azure](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Sobre o **meus projetos** página, selecione **+ novo projeto** (atalho de teclado: n); o botão poderá aparecer apenas como **+** se a janela do browser estreita:

    ![Comando de novo projeto na página meus projetos](media/quickstarts/new-project-command.png)

1. Na **criar novo projeto** pop-up apresentado, introduza ou defina os seguintes detalhes, em seguida, selecione **criar**:

    - **Nome do projeto**: Com um ambiente personalizado do projeto
    - **ID do projeto**: ambiente de projeto personalizado
    - **Projeto público**: (limpo)
    - **Criar um README.md**: (limpo)

1. Após alguns instantes, o Azure blocos de notas navega para o novo projeto. Adicionar um bloco de notas para o projeto, selecionando o **+ novo** pendente (que podem aparecer apenas como **+**), em seguida, selecionar **bloco de notas**.

1. Dê um nome semelhante ao bloco de notas *personalizado environment.ipynb*, selecione **Python 3.6** para o idioma, selecione **New**.

## <a name="add-a-custom-setup-step"></a>Adicione um passo de configuração personalizada

1. Na página do projeto, selecione **definições do projeto**.

    ![Comando de definições do projeto](media/quickstarts/project-settings-command.png)

1. Na **definições do projeto** pop-up, selecione a **ambiente** separador, em seguida, em **passos de configuração do ambiente**, selecione **+ adicionar**:

    ![Comando para adicionar um novo passo de configuração do ambiente](media/quickstarts/environment-add-command.png)

1. O **+ adicionar** comando cria uma etapa que é definida por uma operação e um ficheiro de destino que está selecionado a partir dos ficheiros no seu projeto. São suportadas as seguintes operações:

    | Operação | Descrição |
    | --- | --- |
    | Requirements. txt | Projekty v Pythonu define as respetivas dependências num ficheiro Requirements. txt. Com esta opção, selecione o ficheiro adequado na lista de ficheiros do projeto e selecione também a versão de Python em adicional pendente que é apresentada. Se necessário, selecione **Cancelar** para voltar para o projeto, carregar ou criar o ficheiro, em seguida, regressar à **definições do projeto** > **ambiente** separador e criar um novo passo. Com este passo em vigor, executar automaticamente um bloco de notas no projeto é executado `pip install -r <file>` |
    | Script de shell | Utilize para indicar um script de shell do bash (normalmente, um ficheiro com o *. SH* extensão) que contém todos os comandos que pretende executar para inicializar o ambiente. |
    | Environment.yml | Um projeto de Python que usa conda para gerir um ambiente utiliza um *environments.yml* ficheiro para descrever as dependências. Com esta opção, selecione o ficheiro adequado da lista de ficheiros do projeto. |

1. Para remover qualquer passo de configuração, selecione o **X** à direita do passo.

1. Quando todos os passos de configuração implementadas, selecione **guardar**. (Selecione **Cancelar** para rejeitar as alterações).

1. Para testar o seu ambiente, criar e executar um novo bloco de notas, em seguida, crie uma célula de código com as instruções que dependem de um pacote no ambiente, tais como utilizar o Python `import` instrução. Se a instrução for bem-sucedida, em seguida, o pacote necessário foi instalado com êxito no ambiente.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Gerir e configurar projetos em blocos de notas do Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de notas do Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
