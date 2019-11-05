---
title: Criar e clonar notebooks Jupyter no Azure
description: Azure Notebooks projetos gerenciam uma coleção de blocos de anotações e arquivos relacionados, que você pode criar novos ou clonar de outra fonte.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 9b6a49e2-1d71-4c0b-9e5d-16e059427e38
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 3e0c49d1141d976558ece325baaa99fc9981275e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496715"
---
# <a name="create-and-clone-projects"></a>Criar e clonar projetos

Azure Notebooks organiza seus blocos de anotações do Jupyter e os arquivos relacionados em grupos lógicos chamados *projetos*. Você cria um projeto primeiro como um contêiner e, em seguida, cria ou clona um ou mais blocos de anotações dentro de uma pasta junto com outros arquivos de projeto. (Esse processo é demonstrado no [tutorial](tutorial-create-run-jupyter-notebook.md).)

Um projeto também mantém metadados e outras definições de configuração que afetam o servidor em que os notebooks são executados, incluindo etapas de instalação personalizadas e instalação de pacote. Para obter mais informações, consulte [gerenciar e configurar projetos](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Usar o painel meus projetos

O painel **meus projetos** em `https://notebooks.azure.com/<userID>/projects` é onde você exibe, gerencia e cria projetos:

[![painel meus projetos no Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

O que você pode fazer no painel depende se você está conectado com a conta que possui a ID de usuário:

| Comando | Disponível para | Descrição |
| --- | --- | --- |
| **Funcionam** | Proprietário | Inicia o Project Server e abre a pasta do projeto no Jupyter. (Mais comumente, você navega primeiro em uma pasta de projeto e, em seguida, inicia um bloco de anotações a partir daí.) |
| **Transferência** | Qualquer pessoa | Baixa uma cópia do projeto selecionado como um arquivo ZIP. |
| **Compartilhar** | Qualquer pessoa | Exibe o pop-up de compartilhamento por meio do qual você pode obter uma URL para um projeto selecionado, compartilhar com mídia social, enviar um email com a URL e obter código HTML ou de redução para com um selo de "Iniciar bloco de anotações" (consulte [obter uma notificação de inicialização](#obtain-a-launch-badge)) com a URL. |
| **Eliminar** | Proprietário | Exclui o projeto selecionado. Esta operação não pode ser anulada. |
| **Componentes** | Proprietário | Inicia o Project Server e abre uma nova janela do navegador com o terminal Bash para esse servidor. |
| **+ Novo projeto** | Proprietário | Cria um novo projeto. Consulte [criar um novo projeto](#create-a-new-project). |
| **Carregar repositório GitHub** | Proprietário | Importa um projeto do GitHub. [Importe um projeto do GitHub](#import-a-project-from-github). |
| **8i** | Qualquer pessoa | Copia um projeto selecionado em sua própria conta. Solicita que você entre se ainda não o tiver feito. Consulte [clonar um projeto](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Obter uma notificação de lançamento

Ao usar o comando **compartilhar** e selecionar a guia **Inserir** , você pode copiar um código HTML ou uma redução que cria um selo de "Iniciar bloco de anotações":

![Iniciar notificação do bloco de anotações](https://notebooks.azure.com/launch.png)

Se você não tiver um projeto Azure Notebooks, poderá criar um link que clone do GitHub diretamente usando os seguintes modelos, substituindo os nomes de nome de usuário e de repositório apropriados:

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
| Nome do projeto | Um nome amigável para o projeto que Azure Notebooks usa para fins de exibição. Por exemplo, "meu projeto de notebook". |
| ID do Projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](#reserved-project-ids). Se você não tiver certeza do que usar, uma convenção comum é usar uma versão em minúsculas do nome do projeto onde os espaços são transformados em hifens, como "My-Notebook-Project" (truncado se necessário para se ajustar ao limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com o link acesse o projeto. Ao criar um projeto privado, desmarque essa opção. |
| Inicializar este projeto com um LEIAme | Se definido, cria um arquivo *README.MD* padrão no projeto. Um arquivo *README.MD* é onde você fornece documentação para seu projeto, se desejar. |

### <a name="reserved-project-ids"></a>IDs de projeto reservadas

As palavras reservadas a seguir não podem ser usadas por si mesmas como IDs de projeto. No entanto, essas palavras reservadas podem ser usadas como parte de IDs de projeto mais longas.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| pensar | conta | administrar | api | bloga | turma |
| conteúdo | dashboard | apresenta | Perguntas Freqüentes | Ajuda | html |
| Casa | importe | Biblioteca | gestão | novo | D430 |
| notebooks | formato | pré-visualização | Refere | criar | Procurando |
| status | Support | test | | | |

Se você tentar usar uma dessas palavras como uma ID do projeto, os pop-up **criar novo projeto** e **configurações do projeto** indicarão "a ID da biblioteca é um identificador reservado".

Como uma ID de projeto também faz parte da URL de um projeto, o software bloqueador do AD pode bloquear o uso de determinadas palavras-chave, como "anunciar". Nesses casos, use uma palavra diferente na ID do projeto.

## <a name="import-a-project-from-github"></a>Importar um projeto do GitHub

Você pode importar facilmente um repositório GitHub público inteiro como um projeto, incluindo dados e arquivos *README.MD* . Use o comando **carregar repositório GitHub** , forneça os seguintes detalhes no pop-up e selecione **importar**:

| Campo | Descrição |
| --- | --- |
| Repositório do GitHub | O nome do repositório de origem em github.com. Por exemplo, para clonar os blocos de anotações do Jupyter para serviços cognitivas do Azure em [https://github.com/Microsoft/cognitive-services-notebooks](https://github.com/Microsoft/cognitive-services-notebooks), insira "Microsoft/cognitiva-Services-notebooks".  |
| Clonar recursivamente | Os repositórios do GitHub podem conter vários repositórios filho. Defina esta opção se você quiser clonar o repositório pai e todos os seus filhos. Como é possível que um repositório tenha muitos filhos, deixe essa opção desmarcada, a menos que você saiba que precisa dela. |
| Nome do projeto | Um nome amigável para o projeto que Azure Notebooks usa para fins de exibição. |
| ID do Projeto | Um identificador personalizado que se torna parte da URL que você usa para compartilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](#reserved-project-ids). Se você não tiver certeza do que usar, uma convenção comum é usar uma versão em minúsculas do nome do projeto onde os espaços são transformados em hifens, como "My-Notebook-Project" (truncado se necessário para se ajustar ao limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com o link acesse o projeto. Ao criar um projeto privado, desmarque essa opção. |

A importação de um repositório do GitHub também importa seu histórico. Você pode usar comandos git padrão do terminal para confirmar novas alterações, efetuar pull de alterações do GitHub e assim por diante.

## <a name="clone-a-project"></a>Clonar um projeto

A clonagem cria uma cópia de um projeto existente em sua própria conta, na qual você pode executar e modificar qualquer bloco de anotações ou outro arquivo no projeto. Você também pode usar a clonagem para fazer cópias de seus próprios projetos nos quais realizam experimentos ou outros trabalhos sem perturbar o projeto original.

Para clonar um projeto:

1. No painel **meus projetos** , clique com o botão direito do mouse no projeto desejado e selecione **clonar** (atalho de teclado: c).

    ![Comando clonar no menu de contexto do projeto](media/clone-command.png)

1. No pop-up **clonar projeto** , insira um nome e uma ID para o clone e especifique se o clone é público. Essas configurações são as mesmas para um [novo projeto](#create-a-new-project).

    ![Pop-up clonar projeto](media/clone-project.png)

1. Depois de selecionar o botão **clonar** , Azure notebooks navega diretamente para a cópia.

## <a name="next-steps"></a>Passos seguintes

- [Explorar blocos de anotações de exemplo](azure-notebooks-samples.md)
- [Como: configurar e gerenciar projetos](configure-manage-azure-notebooks-projects.md)
- [Como instalar pacotes de dentro de um bloco de anotações](install-packages-jupyter-notebook.md)
- [Como: apresentar uma apresentação de slides](present-jupyter-notebooks-slideshow.md)
- [Como trabalhar com arquivos de dados](work-with-project-data-files.md)
- [Como acessar recursos de dados](access-data-resources-jupyter-notebooks.md)
- [Como: usar Azure Machine Learning](use-machine-learning-services-jupyter-notebooks.md)
