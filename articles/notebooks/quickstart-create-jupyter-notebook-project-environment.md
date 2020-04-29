---
title: Crie um projeto de pré-visualização de cadernos Azure com um ambiente personalizado
description: Crie um novo projeto em Folhetos Azure que esteja configurado com um conjunto específico de pacotes instalados e scripts de arranque.
ms.topic: quickstart
ms.date: 12/04/2018
ms.openlocfilehash: 6388cb7997cac5bef25975043a13c4e080f288d4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "78196846"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Quickstart: Criar um projeto com um ambiente personalizado na Pré-visualização de Cadernos Azure

Um projeto em Cadernos Azure é uma coleção de ficheiros, como cadernos, ficheiros de dados, documentação, imagens, e assim por diante, juntamente com um ambiente que pode ser configurado com comandos de configuração específicos. Ao definir o ambiente com o projeto, quem clonar o projeto na sua própria conta de Cadernos Azure tem toda a informação necessária para recriar o ambiente necessário.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Criar um projeto

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inscreva-se nos Cadernos Azure).](quickstart-sign-in-azure-notebooks.md)

1. Na sua página de perfil público, selecione **My Projects** no topo da página:

    ![My Projects link no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n); o botão só **+** pode aparecer como se a janela do navegador fosse estreita:

    ![Novo comando do Projeto na página my projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduza ou detetete os seguintes detalhes, em seguida, selecione **Criar:**

    - **Nome do projeto**: Projeto com um ambiente personalizado
    - **Id do projeto**: ambiente personalizado do projeto
    - **Projeto público**: (apurado)
    - **Criar um README.md:**(limpo)

1. Após alguns momentos, os Cadernos Azure navegam para o novo projeto. Adicione um caderno ao projeto selecionando o **+ Novo** drop-down (que pode aparecer como apenas **+**), em seguida, selecionando O **Caderno**.

1. Dê ao caderno um nome como *Custom environment.ipynb,* selecione **Python 3.6** para o idioma e selecione **New**.

## <a name="add-a-custom-setup-step"></a>Adicione um passo de configuração personalizado

1. Na página do projeto, selecione **Definições**de Projeto .

    ![Comando de configurações do projeto](media/quickstarts/project-settings-command.png)

1. Nas **definições** do projeto popup, selecione o separador **Ambiente** e, em seguida, em passos de **configuração ambiente,** selecione **+ Adicione**:

    ![Comando para adicionar um novo passo de configuração ambiental](media/quickstarts/environment-add-command.png)

1. O comando **+ Adicionar** cria um passo definido por uma operação e um ficheiro alvo que é selecionado a partir dos ficheiros do seu projeto. São apoiadas as seguintes operações:

   | Operação | Descrição |
   | --- | --- |
   | Requisitos.txt | Os projetos Python definem as suas dependências num ficheiro requisitos.txt. Com esta opção, selecione o ficheiro apropriado da lista de ficheiros do projeto e selecione também a versão Python no drop-down adicional que aparece. Se necessário, selecione **Cancelar** para voltar ao projeto, carregar ou criar o ficheiro, em seguida, volte ao**separador** Ambiente de **Definições** > do Projeto e crie um novo passo. Com este passo no lugar, executar um caderno no projeto executa automaticamente`pip install -r <file>` |
   | Roteiro shell | Utilize para indicar um script de concha de bash (normalmente um ficheiro com a extensão *.sh)* que contém quaisquer comandos que deseje executar para inicializar o ambiente. |
   | Ambiente.yml | Um projeto Python que usa conda para gerir um ambiente usa um ficheiro *ambiente.yml* para descrever dependências. Com esta opção, selecione o ficheiro apropriado da lista de ficheiros do projeto. |

   > [!WARNING]
   > Como este é um serviço de pré-visualização em `Environment.yml` desenvolvimento, existe atualmente uma questão conhecida em que a definição não é aplicada ao seu projeto como esperado. O projeto e os cadernos Jupyter no interior não carregam atualmente o ficheiro ambiente especificado.

1. Para remover qualquer passo de configuração, selecione o **X** à direita do passo.

1. Quando todos os passos de configuração estiverem no lugar, selecione **Guardar**. (Selecione **Cancelar** para descartar alterações).

1. Para testar o seu ambiente, crie e execute um novo caderno, em seguida, crie `import` uma célula de código com declarações que dependem de um pacote no ambiente, como usar uma declaração python. Se a declaração for bem sucedida, então o pacote necessário foi instalado com sucesso no ambiente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir e configurar projetos em Cadernos Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Tutorial: criar um caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
