---
title: Criar e clonar cadernos Jupyter - Pré-visualização de cadernos Azure
description: Os projetos de pré-visualização de Cadernos Azure gerem uma coleção de cadernos e ficheiros relacionados, que pode criar novos ou clones a partir de outra fonte.
ms.topic: how-to
ms.date: 02/25/2019
ms.openlocfilehash: b29ff336c09a3bbf05a57c8a3a503b1875b76e54
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280576"
---
# <a name="create-and-clone-projects-in-azure-notebooks-preview"></a>Criar e clonar projetos na Pré-visualização de cadernos Azure

Os Cadernos Azure organizam os seus cadernos Jupyter e ficheiros relacionados em grupos lógicos *chamados projetos.* Criar um projeto primeiro como um contêiner, em seguida, crie ou clonar um ou mais blocos de notas dentro de uma pasta em conjunto com outros ficheiros de projeto. (Este processo é demonstrado no [tutorial](tutorial-create-run-jupyter-notebook.md).)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Um projeto também mantém metadados e outras definições de configuração que afetam o servidor nos quais blocos de notas que executar, incluindo os passos de configuração personalizada e instalação do pacote. Para mais informações, consulte [Gerir e configurar projetos.](configure-manage-azure-notebooks-projects.md)

## <a name="use-the-my-projects-dashboard"></a>Utilize o dashboard de meus projetos

O painel **My Projects** no `https://notebooks.azure.com/<userID>/projects` é onde vê, gere e cria projetos:

[![Painéis dos meus projetos em cadernos Azure](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

O que pode fazer no dashboard depende se iniciou sessão com a conta que possui o ID de utilizador:

| Comando | Disponível para | Descrição |
| --- | --- | --- |
| **Correr** | Proprietário | Inicia o servidor de projeto e abre a pasta de projeto no Jupyter. (Mais comum, que navegue primeiro para uma pasta do projeto, em seguida, iniciar um bloco de notas a partir daí.) |
| **Transferência** | Qualquer pessoa | Transfere uma cópia do projeto selecionado como um ficheiro ZIP. |
| **Partilhar** | Qualquer pessoa | Exibe o popup de partilha através do qual pode obter um URL para um projeto selecionado, partilhar para as redes sociais, enviar um e-mail com o URL, e obter código HTML ou Markdown para com um crachá de "caderno de lançamento" (ver [obter um crachá](#obtain-a-launch-badge)de lançamento ) com o URL. |
| **Eliminar** | Proprietário | Elimina o projeto selecionado. Esta operação não pode ser anulada. |
| **Terminal** | Proprietário | Inicia o servidor de projeto, em seguida, abre-se de uma nova janela do browser com o bash para esse servidor de terminal. |
| **+ Novo Projeto** | Proprietário | Cria um novo projeto. Ver [Criar um novo projeto.](#create-a-new-project) |
| **Upload GitHub Repo** | Proprietário | Importa um projeto do GitHub. [Importar um projeto do GitHub.](#import-a-project-from-github) |
| **Clone** | Qualquer pessoa | Copia um projeto selecionado para a sua própria conta. Pede-lhe para iniciar sessão em se não o tiver feito. Veja [clone um projeto.](#clone-a-project) |

### <a name="obtain-a-launch-badge"></a>Obter um destaque de lançamento

Quando utilizar o comando **Partilhar** e selecionar o separador **Incorporado,** pode copiar o código HTML ou o Markdown que cria um crachá de "caderno de lançamento":

![Inicie o destaque do bloco de notas](https://notebooks.azure.com/launch.png)

Se não tiver um projeto de blocos de notas do Azure, pode criar uma ligação que clona do GitHub diretamente com os modelos seguintes, substituindo o nome de utilizador apropriado e nomes de repositório:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Criar um novo projeto

Quando utiliza o comando **+ Novo Projeto,** os Notebooks Azure exibem um popup **Create New Project.** Neste popup, introduza as seguintes informações e, em seguida, selecione **Criar:**

| Campo | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. Por exemplo, "My Notebook Project". |
| ID do Projeto | Um identificador personalizado que se torna parte do URL que usa para partilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Este ID só pode usar letras, números e hífenes, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |
| Inicializar esse projeto com um ficheiro Leia-me | Se definido, cria um ficheiro *de README.md* padrão no projeto. Um ficheiro *README.md* é onde fornece documentação para o seu projeto, se desejar. |

### <a name="reserved-project-ids"></a>IDs de projeto reservados

As seguintes palavras reservadas não podem ser usadas por si próprias como identificação do projeto. Estas palavras reservadas podem, no entanto, ser usadas como parte de iDs de projeto mais longos.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| sobre | conta | administração | api | blog | sala de aula |
| conteúdo | dashboard | explorar | faq | ajuda | html |
| home page | importação | biblioteca | gestão | Novo | caderno |
| notebooks | pdf | pré-visualização | preços | profile | pesquisa |
| status | apoio | teste | | | |

Se tentar usar uma destas palavras como id de projeto, os popups **Create New Project** e Project **Settings** indicam: "O id da biblioteca é um identificador reservado."

Como um ID de projeto também faz parte do URL de um projeto, o software de bloqueador de anúncios pode bloquear o uso de certas palavras-chave, como "anúncio". Nesses casos, use uma palavra diferente no ID do projeto.

## <a name="import-a-project-from-github"></a>Importar um projeto do GitHub

Você pode facilmente importar um repo público inteiro como um projeto, incluindo quaisquer dados e *README.md* ficheiros. Utilize o comando **Upload GitHub Repo,** forneça os seguintes detalhes no popup e, em seguida, selecione **Import:**

| Campo | Descrição |
| --- | --- |
| Repositório do GitHub | O nome do repositório de código-fonte no github.com. Por exemplo, para clonar os cadernos Jupyter para serviços cognitivos Azure em [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), insira "Microsoft/cognitive-services-notebooks".  |
| Clonar recursivamente | Repositórios do GitHub podem conter vários repositórios de subordinados. Defina esta opção se pretender clonar o repositório principal e todos os respetivos subordinados. Como é possível para um repositório ter várias crianças, deixe esta opção clara, a menos que sabe o que for preciso. |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. |
| ID do Projeto | Um identificador personalizado que se torna parte do URL que usa para partilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Este ID só pode usar letras, números e hífenes, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |

Também importar um repositório do GitHub importa o respetivo histórico. Pode utilizar comandos de Git padrão a partir do terminal para consolidar as alterações novas, tirar alterações do GitHub e assim por diante.

## <a name="clone-a-project"></a>Clonar um projeto

A clonagem cria uma cópia de um projeto existente na sua própria conta, onde pode, em seguida, executar e modificar qualquer bloco de notas ou outro arquivo no projeto. Também pode utilizar a clonagem para fazer cópias dos seus próprios projetos em que fazer experiências ou outro trabalho sem perturbar o projeto original.

Para clonar um projeto:

1. No painel my **projects,** clique à direita no projeto pretendido e selecione **Clone** (atalho de teclado: c).

    ![Comando de clonagem no menu de contexto do projeto](media/clone-command.png)

1. No popup do **Projeto Clone,** introduza um nome e identificação para o clone e especifique se o clone é público. Estas configurações são as mesmas que para um [novo projeto.](#create-a-new-project)

    ![Pop-up de projeto de clone](media/clone-project.png)

1. Depois de selecionar o botão **Clone,** os Cadernos Azure navegam diretamente para a cópia.

## <a name="next-steps"></a>Passos Seguintes

- [Explore os cadernos de amostras](azure-notebooks-samples.md)
- [Como: Configurar e gerir projetos](configure-manage-azure-notebooks-projects.md)
- [Como: Instalar pacotes dentro de um caderno](install-packages-jupyter-notebook.md)
- [Como: Apresentar uma apresentação de diapositivos](present-jupyter-notebooks-slideshow.md)
- [Como: Trabalhar com ficheiros de dados](work-with-project-data-files.md)
- [Como: Aceder aos recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Como: Utilizar machine learning azure](use-machine-learning-services-jupyter-notebooks.md)
