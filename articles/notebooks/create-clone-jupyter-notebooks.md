---
title: Criar e clonar os blocos de notas do Jupyter no Azure
description: Projetos de blocos de notas do Azure gerir uma coleção de blocos de notas e arquivos relacionados, o que pode criar um novo ou clonar a partir de outra origem.
ms.topic: article
ms.date: 02/25/2019
ms.openlocfilehash: fc5425312637710f0b9f94493b8cfb4a48582236
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277567"
---
# <a name="create-and-clone-projects"></a>Criar e clonar projetos

Azure Notebooks organiza seus blocos de anotações do Jupyter e os arquivos relacionados em grupos lógicos chamados *projetos*. Criar um projeto primeiro como um contêiner, em seguida, crie ou clonar um ou mais blocos de notas dentro de uma pasta em conjunto com outros ficheiros de projeto. (Esse processo é demonstrado no [tutorial](tutorial-create-run-jupyter-notebook.md).)

Um projeto também mantém metadados e outras definições de configuração que afetam o servidor nos quais blocos de notas que executar, incluindo os passos de configuração personalizada e instalação do pacote. Para obter mais informações, consulte [gerenciar e configurar projetos](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Utilize o dashboard de meus projetos

O painel **meus projetos** em `https://notebooks.azure.com/<userID>/projects` é onde você exibe, gerencia e cria projetos:

[![painel meus projetos no Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

O que pode fazer no dashboard depende se iniciou sessão com a conta que possui o ID de utilizador:

| Comando | Disponível para | Descrição |
| --- | --- | --- |
| **Funcionam** | Proprietário | Inicia o servidor de projeto e abre a pasta de projeto no Jupyter. (Mais comum, que navegue primeiro para uma pasta do projeto, em seguida, iniciar um bloco de notas a partir daí.) |
| **Transferência** | Qualquer pessoa | Transfere uma cópia do projeto selecionado como um ficheiro ZIP. |
| **Partilhar** | Qualquer pessoa | Exibe o pop-up de compartilhamento por meio do qual você pode obter uma URL para um projeto selecionado, compartilhar com mídia social, enviar um email com a URL e obter código HTML ou de redução para com um selo de "Iniciar bloco de anotações" (consulte [obter uma notificação de inicialização](#obtain-a-launch-badge)) com a URL. |
| **Eliminar** | Proprietário | Elimina o projeto selecionado. Esta operação não pode ser anulada. |
| **Componentes** | Proprietário | Inicia o servidor de projeto, em seguida, abre-se de uma nova janela do browser com o bash para esse servidor de terminal. |
| **+ Novo projeto** | Proprietário | Cria um novo projeto. Consulte [criar um novo projeto](#create-a-new-project). |
| **Carregar repositório GitHub** | Proprietário | Importa um projeto do GitHub. [Importe um projeto do GitHub](#import-a-project-from-github). |
| **8i** | Qualquer pessoa | Copia um projeto selecionado para a sua própria conta. Pede-lhe para iniciar sessão em se não o tiver feito. Consulte [clonar um projeto](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Obter um destaque de lançamento

Ao usar o comando **compartilhar** e selecionar a guia **Inserir** , você pode copiar um código HTML ou uma redução que cria um selo de "Iniciar bloco de anotações":

![Inicie o destaque do bloco de notas](https://notebooks.azure.com/launch.png)

Se não tiver um projeto de blocos de notas do Azure, pode criar uma ligação que clona do GitHub diretamente com os modelos seguintes, substituindo o nome de utilizador apropriado e nomes de repositório:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Criar um novo projeto

Quando você usa o comando **+ New Project** , Azure notebooks exibe um pop-up **criar novo projeto** . Nesse pop-up, insira as informações a seguir e, em seguida, selecione **criar**:

| Campo | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. Por exemplo, "meu projeto de notebook". |
| ID do Projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |
| Inicializar esse projeto com um ficheiro Leia-me | Se definido, cria um arquivo *README.MD* padrão no projeto. Um arquivo *README.MD* é onde você fornece documentação para seu projeto, se desejar. |

### <a name="reserved-project-ids"></a>IDs de projeto reservadas

As palavras reservadas a seguir não podem ser usadas por si mesmas como IDs de projeto. No entanto, essas palavras reservadas podem ser usadas como parte de IDs de projeto mais longas.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| pensar | conta | administração | api | bloga | turma |
| conteúdo | dashboard | apresenta | faq | Obter ajuda | html |
| Casa | importe | biblioteca | gestão | novo | D430 |
| notebooks | pdf | pré-visualização | refere | profile | procurando |
| status | support | test | | | |

Se você tentar usar uma dessas palavras como uma ID do projeto, os pop-up **criar novo projeto** e **configurações do projeto** indicarão "a ID da biblioteca é um identificador reservado".

Como uma ID de projeto também faz parte da URL de um projeto, o software bloqueador do AD pode bloquear o uso de determinadas palavras-chave, como "anunciar". Nesses casos, use uma palavra diferente na ID do projeto.

## <a name="import-a-project-from-github"></a>Importar um projeto do GitHub

Você pode importar facilmente um repositório GitHub público inteiro como um projeto, incluindo dados e arquivos *README.MD* . Use o comando **carregar repositório GitHub** , forneça os seguintes detalhes no pop-up e selecione **importar**:

| Campo | Descrição |
| --- | --- |
| Repositório do GitHub | O nome do repositório de código-fonte no github.com. Por exemplo, para clonar os blocos de anotações do Jupyter para serviços cognitivas do Azure em [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), insira "Microsoft/cognitiva-Services-notebooks".  |
| Clonar recursivamente | Repositórios do GitHub podem conter vários repositórios de subordinados. Defina esta opção se pretender clonar o repositório principal e todos os respetivos subordinados. Como é possível para um repositório ter várias crianças, deixe esta opção clara, a menos que sabe o que for preciso. |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. |
| ID do Projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |

Também importar um repositório do GitHub importa o respetivo histórico. Pode utilizar comandos de Git padrão a partir do terminal para consolidar as alterações novas, tirar alterações do GitHub e assim por diante.

## <a name="clone-a-project"></a>Clonar um projeto

A clonagem cria uma cópia de um projeto existente na sua própria conta, onde pode, em seguida, executar e modificar qualquer bloco de notas ou outro arquivo no projeto. Também pode utilizar a clonagem para fazer cópias dos seus próprios projetos em que fazer experiências ou outro trabalho sem perturbar o projeto original.

Para clonar um projeto:

1. No painel **meus projetos** , clique com o botão direito do mouse no projeto desejado e selecione **clonar** (atalho de teclado: c).

    ![Comando de clonagem no menu de contexto do projeto](media/clone-command.png)

1. No pop-up **clonar projeto** , insira um nome e uma ID para o clone e especifique se o clone é público. Essas configurações são as mesmas para um [novo projeto](#create-a-new-project).

    ![Pop-up de projeto de clone](media/clone-project.png)

1. Depois de selecionar o botão **clonar** , Azure notebooks navega diretamente para a cópia.

## <a name="next-steps"></a>Passos seguintes

- [Explorar blocos de anotações de exemplo](azure-notebooks-samples.md)
- [Como: configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Como instalar pacotes de dentro de um bloco de anotações](install-packages-jupyter-notebook.md)
- [Como: apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
- [Como trabalhar com arquivos de dados](work-with-project-data-files.md)
- [Como acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Como: usar Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
