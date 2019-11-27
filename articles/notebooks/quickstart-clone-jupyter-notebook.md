---
title: Clonar um bloco de notas Jupyter do GitHub com blocos de notas do Azure
description: Clonar um bloco de notas do Jupyter partir de um repositório do GitHub e executá-lo na sua conta de blocos de notas do Azure rapidamente.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 500bd3f85409bb2f5e7b73be0478694695cc9c00
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277531"
---
# <a name="quickstart-clone-a-notebook"></a>Início rápido: Clonar um bloco de notas

Muitos cientistas de dados e desenvolvedores armazenam seus notebooks em [repositórios GitHub](https://github.com), um serviço gratuito que fornece armazenamento e controle de versão para muitos tipos diferentes de projetos. GitHub é frequentemente utilizado como um meio de colaboração em blocos de notas do Jupyter que são executados localmente. Nesses casos, cada colaborador mantém uma cópia local do repositório e executa os blocos de notas dessa cópia.

A clonagem, cria uma cópia de um bloco de notas do GitHub na sua conta de blocos de notas do Azure em vez disso. Este clone é independente do seu repositório original; as alterações são armazenadas na sua conta de blocos de notas do Azure apenas e não afetam o original. Como seu clone é na cloud, pode partilhar o projeto com outros funcionários que não precisam fazer quaisquer cópias locais ou até mesmo ter Jupyter instalado em seus próprios computadores. Também pode clonar um bloco de notas simplesmente como um ponto de partida para um projeto de seu próprio ou para obter ficheiros de dados.

## <a name="clone-azure-cognitive-services-notebooks"></a>Clonar blocos de notas de serviços cognitivos do Azure

1. Vá para [Azure notebooks](https://notebooks.azure.com) e entre. (Para obter detalhes, consulte [início rápido-entrar no Azure notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na página de seu perfil público, selecione **meus projetos** na parte superior da página:

    ![Meu link de projetos na parte superior da janela do browser](media/quickstarts/my-projects-link.png)

1. Na página **meus projetos** , selecione o botão de seta para cima (atalho de teclado: U; o botão é exibido como **carregar repositório GitHub** quando a janela do navegador é grande o suficiente):

    ![Comando carregar repositório do GitHub na página meus projetos](media/quickstarts/upload-github-repo-command.png)

1. No **repositório GitHub de upload** que aparece, insira ou defina os seguintes detalhes e, em seguida, selecione **importar**:

   - **Repositório GitHub**: Microsoft/cognitiva-Services-notebooks (esse nome clona os notebooks Jupyter para serviços cognitivas do Azure em [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks)).
   - **Clonar recursivamente**: (limpo)
   - **Nome do projeto**: clone de serviços cognitivas
   - **ID do projeto**: cognitiva-Services-clone
   - **Público**: (desmarcado)

     ![Carregar pop-up do repositório do GitHub para coletar informações do repositório](media/quickstarts/upload-github-repo-popup.png)

1. Seja paciente, enquanto o processo for concluído; um repositório de clonagem pode demorar alguns minutos.

1. Após a conclusão da clonagem, blocos de notas do Azure leva-o para o novo projeto onde pode ver as cópias de todos os arquivos.

    [![](media/quickstarts/completed-clone.png "View of a completed clone")](media/quickstarts/completed-clone.png#lightbox)

## <a name="share-a-notebook"></a>Partilhar um bloco de notas

1. Para compartilhar sua cópia do projeto clonado, use o controle de **compartilhamento** ou obtenha um link, obtenha HTML ou código de redução que contém o link ou crie uma mensagem de email com o link:

    ![Comando de partilha do projeto](media/quickstarts/share-project-command.png)

1. Como você desmarcou a opção **pública** ao clonar o projeto, o clone é privado. Para tornar sua cópia pública, selecione **configurações do projeto**, defina a opção **projeto público** no pop-up e, em seguida, selecione **salvar**.

1. Selecione um bloco de notas no projeto para executá-lo. Cada bloco de anotações no repositório de serviços cognitivos do Azure, por exemplo, é sua própria guia de introdução autossuficiente. A imagem abaixo mostra o resultado da utilização do bloco de notas BingImageSearchAPI, depois de adicionar uma chave de assinatura de API dos serviços cognitivos e alterando o termo de pesquisa "puppies" para "bunnies":

    ![A executar o bloco de notas do Jupyter clonou do GitHub](media/quickstarts/clone-notebook-result.png)

1. Quando você terminar de executar o bloco de anotações, selecione **arquivo** > **fechar e parar** para fechar o bloco de anotações e a janela do navegador.

1. Para compartilhar um bloco de anotações individual no projeto, clique com o botão direito do mouse no bloco de anotações e selecione **Copiar link** (atalho de teclado: y):

    ![Comando de menu de contexto para copiar uma ligação para um bloco de anotações individual](media/quickstarts/copy-link-to-individual-notebook.png)

1. Para editar arquivos que não sejam blocos de anotações, clique com o botão direito do mouse no arquivo no projeto e selecione **Editar arquivo** (atalho de teclado: i). A ação padrão, **executar** (atalho de teclado: r), mostra apenas o conteúdo do arquivo e não permite edição.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: criar uma execução de um bloco de anotações Jupyter para fazer a regressão linear](tutorial-create-run-jupyter-notebook.md)
