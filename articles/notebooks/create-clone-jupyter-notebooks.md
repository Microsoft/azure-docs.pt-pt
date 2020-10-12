---
title: Criar e clonar cadernos Jupyter - Pré-visualização de cadernos Azure
description: Os projetos de pré-visualização de Cadernos Azure gerem uma coleção de cadernos e ficheiros relacionados, que pode criar novos ou clones a partir de outra fonte.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: 4a51f9b12ca24d16858b41357627ff26d233357f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86181506"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Criar e clonar projetos em Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

A Azure Notebooks organiza os seus cadernos Jupyter e ficheiros relacionados em grupos lógicos chamados *projetos.* Cria-se um projeto primeiro como recipiente, depois cria-se ou clona-se um ou mais cadernos dentro de uma pasta ao lado de outros ficheiros do projeto. (Este processo é demonstrado no [tutorial](tutorial-create-run-jupyter-notebook.md).)

Um projeto também mantém metadados e outras configurações que afetam o servidor em que os cadernos funcionam, incluindo etapas de configuração personalizadas e instalação de pacotes. Para mais informações, consulte [Gerir e configurar projetos.](configure-manage-azure-notebooks-projects.md)

## <a name="use-the-my-projects-dashboard"></a>Use o painel My Projects

O painel **My Projects** é `https://notebooks.azure.com/<userID>/projects` onde vê, gere e cria projetos:

[![My Projects dashboard em Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

O que pode fazer no painel de instrumentos depende se está inscrito na conta que detém o ID do utilizador:

| Comando | Disponível para | Descrição |
| --- | --- | --- |
| **Correr** | Proprietário | Inicia o servidor do projeto e abre a pasta do projeto em Jupyter. (Mais comummente, navega-se primeiro numa pasta de projeto, depois começa-se um caderno a partir daí.) |
| **Transferência** | Qualquer pessoa | Descarrega uma cópia do projeto selecionado como um ficheiro ZIP. |
| **Partilhar** | Qualquer pessoa | Exibe o popup de partilha através do qual pode obter um URL para um projeto selecionado, partilhar para as redes sociais, enviar um e-mail com o URL e obter o código HTML ou Markdown para com um crachá de "bloco de lançamento" (ver [obter um crachá de lançamento)](#obtain-a-launch-badge)com o URL. |
| **Eliminar** | Proprietário | Elimina o projeto selecionado. Esta operação não pode ser anulada. |
| **Terminal** | Proprietário | Inicia o servidor do projeto e abre uma nova janela do navegador com o terminal de bash para esse servidor. |
| **+ Novo Projeto** | Proprietário | Cria um novo projeto. Ver [Criar um novo projeto.](#create-a-new-project) |
| **Upload GitHub Repo** | Proprietário | Importa um projeto do GitHub. [Importe um projeto do GitHub.](#import-a-project-from-github) |
| **Clone** | Qualquer pessoa | Copia um projeto selecionado na sua própria conta. Pede-lhe que assine se já não. Ver [Clone um projeto.](#clone-a-project) |

### <a name="obtain-a-launch-badge"></a>Obtenha um crachá de lançamento

Quando utilizar o comando **Partilhar** e selecionar o separador **Embed,** pode copiar o código HTML ou o Markdown que cria um crachá de "bloco de lançamento":

![Insígnia de caderno de lançamento](https://notebooks.azure.com/launch.png)

Se não tiver um projeto Azure Notebooks, pode criar um link que clones do GitHub utilizando diretamente os seguintes modelos, substituindo o nome de utilizador e os nomes de repositório apropriados:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Criar um novo projeto

Quando utiliza o comando **+ New Project,** os Cadernos Azure exibem um popup **Create New Project.** Neste pop-up, introduza as seguintes informações e, em seguida, **selecione Criar**:

| Campo | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que a Azure Notebooks usa para fins de exibição. Por exemplo, "O Meu Projeto De Caderno". |
| ID do Projeto | Um identificador personalizado que se torna parte do URL que usa para partilhar um projeto (o formulário `https://notebooks.azure.com/<user_id>/projects/<project_id>` é). Este ID só pode usar letras, números e hífens, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](#reserved-project-ids). Se não tem a certeza do que usar, uma convenção comum é usar uma versão minúscula do seu nome de projeto onde os espaços são transformados em hífenes, como "my-notebook-project" (truncado se necessário para encaixar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com o link aceda ao projeto. Ao criar um projeto privado, limpe esta opção. |
| Inicializar este projeto com uma README | Se for definido, cria um ficheiro *de README.md* predefinido no projeto. Um *ficheiro README.md* é onde fornece documentação para o seu projeto, se desejar. |

### <a name="reserved-project-ids"></a>IDs de projeto reservados

As seguintes palavras reservadas não podem ser usadas por si mesmas como IDs do projeto. Estas palavras reservadas podem, no entanto, ser usadas como parte de iDs de projeto mais longos.

- acerca de
- conta
- administração
- api
- blogue
- sala de aula
- conteúdo
- Dashboard
- explorar
- Perguntas Frequentes
- Ajuda
- html
- home
- importar
- biblioteca
- gestão
- novo
- bloco de notas
- notebooks
- pdf
- preview
- preços
- perfil
- pesquisar
- status
- suporte
- test

Se tentar usar uma destas palavras como ID de projeto, os popups **Create New Project** e Project **Settings** indicam: "O id da biblioteca é um identificador reservado".

Como um ID de projeto também faz parte do URL de um projeto, o software de bloqueador de anúncios pode bloquear o uso de certas palavras-chave, como "anúncio". Nesses casos, use uma palavra diferente no ID do projeto.

## <a name="import-a-project-from-github"></a>Importar um projeto do GitHub

Você pode facilmente importar um repo público inteiro GitHub como um projeto, incluindo quaisquer dados e *README.md* ficheiros. Utilize o comando **Upload GitHub Repo,** forneça os seguintes detalhes no pop-up e, em seguida, selecione **Import**:

| Campo | Descrição |
| --- | --- |
| Repositório do GitHub | O nome do repositório de origem na github.com. Por exemplo, para clonar os cadernos Jupyter para Azure Cognitive Services [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks) em , insira "Microsoft/cognitive-services-notebooks".  |
| Clone recursivamente | Os repositórios do GitHub podem conter vários repositórios infantis. Desista esta opção se quiser clonar o repositório dos pais e todos os seus filhos. Porque é possível um repositório ter muitos filhos, deixar esta opção clara a menos que saiba que precisa dela. |
| Nome do projeto | Um nome amigável para o seu projeto que a Azure Notebooks usa para fins de exibição. |
| ID do Projeto | Um identificador personalizado que se torna parte do URL que usa para partilhar um projeto (o formulário `https://notebooks.azure.com/<user_id>/projects/<project_id>` é). Este ID só pode usar letras, números e hífens, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](#reserved-project-ids). Se não tem a certeza do que usar, uma convenção comum é usar uma versão minúscula do seu nome de projeto onde os espaços são transformados em hífenes, como "my-notebook-project" (truncado se necessário para encaixar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com o link aceda ao projeto. Ao criar um projeto privado, limpe esta opção. |

Importar um repositório do GitHub também importa a sua história. Pode utilizar comandos Git padrão do terminal para cometer novas alterações, retirar mudanças do GitHub, e assim por diante.

## <a name="clone-a-project"></a>Clone um projeto

A clonagem cria uma cópia de um projeto existente na sua própria conta, onde pode então executar e modificar qualquer caderno ou outro ficheiro do projeto. Você também pode usar a clonagem para fazer cópias de seus próprios projetos em que você faz experiências ou outros trabalhos sem perturbar o projeto original.

Para clonar um projeto:

1. No painel **My Projects,** clique à direita no projeto pretendido e selecione **Clone** (atalho de teclado: c).

    ![Comando de clone no menu de contexto do projeto](media/clone-command.png)

1. No popup do **Projeto Clone,** insira um nome e iD para o clone, e especifique se o clone é público. Estas configurações são as mesmas que para um [novo projeto.](#create-a-new-project)

    ![Popup do Projeto Clone](media/clone-project.png)

1. Depois de selecionar o botão **Clone,** os Blocos de Notas Azure navegam diretamente para a cópia.

## <a name="next-steps"></a>Passos seguintes

- [Explore cadernos de amostras](azure-notebooks-samples.md)
- [Como: Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Instalar pacotes a partir de um caderno](install-packages-jupyter-notebook.md)
- [Como: Apresentar uma apresentação de diapositivos](present-jupyter-notebooks-slideshow.md)
- [Como: Trabalhar com ficheiros de dados](work-with-project-data-files.md)
- [Como: Aceder aos recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Como: Utilizar a aprendizagem automática Azure](use-machine-learning-services-jupyter-notebooks.md)
