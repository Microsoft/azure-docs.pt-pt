---
title: Crie um projeto de pré-visualização de cadernos Azure com um ambiente personalizado
description: Crie um novo projeto no Azure Notebooks Preview que esteja configurado com um conjunto específico de pacotes instalados e scripts de arranque.
ms.topic: quickstart
ms.date: 12/04/2018
ms.custom: tracking-python
ms.openlocfilehash: 03935b9672c25db11b91733fac1c63f1f5978b87
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84559737"
---
# <a name="quickstart-create-a-project-with-a-custom-environment-in-azure-notebooks-preview"></a>Quickstart: Criar um projeto com um ambiente personalizado em Azure Notebooks Preview

Um projeto em Azure Notebooks é uma coleção de ficheiros, como cadernos, ficheiros de dados, documentação, imagens, e assim por diante, juntamente com um ambiente que pode ser configurado com comandos de configuração específicos. Ao definir o ambiente com o projeto, quem clona o projeto na sua própria conta Azure Notebooks tem toda a informação de que precisa para recriar o ambiente necessário.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="create-a-project"></a>Criar um projeto

1. Vá aos [Cadernos Azure](https://notebooks.azure.com) e inscreva-se. (Para mais detalhes, consulte [Quickstart - Inicie súm em Azure Notebooks](quickstart-sign-in-azure-notebooks.md)).

1. Na sua página de perfil público, selecione **Os Meus Projetos** no topo da página:

    ![O meu link de projetos no topo da janela do navegador](media/quickstarts/my-projects-link.png)

1. Na página **My Projects,** selecione **+ Novo Projeto** (atalho de teclado: n); o botão só pode aparecer como **+** se a janela do navegador fosse estreita:

    ![Novo comando do projeto na página My Projects](media/quickstarts/new-project-command.png)

1. No popup **Create New Project** que aparece, introduz ou define os seguintes detalhes e, em seguida, selecione **Criar**:

    - **Nome do**projeto : Projeto com um ambiente personalizado
    - **ID do projeto**: projecto-ambiente personalizado
    - **Projeto público**: (limpo)
    - **Criar um README.md:**(limpo)

1. Após alguns momentos, a Azure Notebooks navega para o novo projeto. Adicione um caderno ao projeto selecionando o **+ Novo** drop-down (que pode aparecer como apenas **+** ), selecionando então **o Caderno**.

1. Dê ao caderno um nome como *Custom environment.ipynb,* selecione **Python 3.6** para o idioma, e selecione **New**.

## <a name="add-a-custom-setup-step"></a>Adicione um passo de configuração personalizado

1. Na página do projeto, selecione **Definições de Projeto**.

    ![Comando de definições de projeto](media/quickstarts/project-settings-command.png)

1. No popup **'Definições de Projecto',** selecione o **separador Ambiente** e, em seguida, em **Passos de Configuração do Ambiente**, selecione + **Adicionar**:

    ![Comando para adicionar um novo passo de configuração do ambiente](media/quickstarts/environment-add-command.png)

1. O comando **+ Adicionar** cria um passo definido por uma operação e um ficheiro-alvo que é selecionado a partir dos ficheiros do seu projeto. São suportadas as seguintes operações:

   | Operação | Description |
   | --- | --- |
   | Requisitos.txt | Os projetos python definem as suas dependências num ficheiro requirements.txt. Com esta opção, selecione o ficheiro apropriado da lista de ficheiros do projeto e selecione também a versão Python no drop-down adicional que aparece. Se necessário, **selecione Cancelar** para voltar ao projeto, carregar ou criar o ficheiro, em seguida, voltar ao separador **Ambiente Definições**de Projeto e criar um novo  >  **Environment** passo. Com este passo no lugar, executar um caderno no projeto corre automaticamente`pip install -r <file>` |
   | Script de concha | Utilize para indicar um script de concha de bash (normalmente um ficheiro com a extensão *.sh)* que contém quaisquer comandos que deseje executar para inicializar o ambiente. |
   | Ambiente.yml | Um projeto Python que usa conda para gerir um ambiente usa um ficheiro *environments.yml* para descrever dependências. Com esta opção, selecione o ficheiro apropriado da lista de ficheiros do projeto. |

   > [!WARNING]
   > Como este é um serviço de pré-visualização em desenvolvimento, existe atualmente uma questão conhecida em que a `Environment.yml` configuração não é aplicada ao seu projeto como esperado. O projeto e os cadernos Jupyter no interior não carregam atualmente o ficheiro ambiental especificado.

1. Para remover qualquer passo de configuração, selecione o **X** à direita do degrau.

1. Quando todos os passos de configuração estiverem no lugar, **selecione Save**. (Selecione **Cancelar** para descartar alterações).

1. Para testar o seu ambiente, crie e execute um novo caderno, em seguida, crie uma célula de código com declarações que dependam de um pacote no ambiente, como usar uma `import` declaração python. Se a declaração for bem sucedida, então o pacote necessário foi instalado com sucesso no ambiente.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Gerir e configurar projetos em Cadernos Azure](configure-manage-azure-notebooks-projects.md)

> [!div class="nextstepaction"]
> [Tutorial: criar uma corrida a um caderno Jupyter para fazer regressão linear](tutorial-create-run-jupyter-notebook.md)
