---
title: Criar e clonar os blocos de notas do Jupyter no Azure
description: Projetos de blocos de notas do Azure gerir uma coleção de blocos de notas e arquivos relacionados, o que pode criar um novo ou clonar a partir de outra origem.
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
ms.openlocfilehash: 13615d319af600234dcc23e04f82ce46b8f97780
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970094"
---
# <a name="create-and-clone-projects"></a>Criar e clonar projetos

Blocos de notas do Azure organiza seus blocos de notas do Jupyter e arquivos relacionados em grupos lógicos chamados *projetos*. Criar um projeto primeiro como um contêiner, em seguida, crie ou clonar um ou mais blocos de notas dentro de uma pasta em conjunto com outros ficheiros de projeto. (Este processo é demonstrado a [tutorial](tutorial-create-run-jupyter-notebook.md).)

Um projeto também mantém metadados e outras definições de configuração que afetam o servidor nos quais blocos de notas que executar, incluindo os passos de configuração personalizada e instalação do pacote. Para obter mais informações, consulte [gerir e configurar projetos](configure-manage-azure-notebooks-projects.md).

## <a name="use-the-my-projects-dashboard"></a>Utilize o dashboard de meus projetos

Sua **meus projetos** dashboard em `https://notebooks.azure.com/<userID>/projects` é onde pode ver, gere e criar projetos:

[painel de projetos ![My no Azure Notebooks](media/my-projects-dashboard.png)](media/my-projects-dashboard.png#lightbox)

O que pode fazer no dashboard depende se iniciou sessão com a conta que possui o ID de utilizador:

| Comando | Disponível para | Descrição |
| --- | --- | --- |
| **Executar** | Proprietário | Inicia o servidor de projeto e abre a pasta de projeto no Jupyter. (Mais comum, que navegue primeiro para uma pasta do projeto, em seguida, iniciar um bloco de notas a partir daí.) |
| **Transferência** | Qualquer pessoa | Transfere uma cópia do projeto selecionado como um ficheiro ZIP. |
| **Partilha** | Qualquer pessoa | Apresenta o popup partilha através do qual pode obter um URL para um projeto selecionado, partilhe a mídia social, enviar um e-mail com o URL e obter o código HTML ou Markdown para com um destaque de "Bloco de notas de lançamento" (consulte [obter um destaque de lançamento](#obtain-a-launch-badge)) com o URL. |
| **Eliminar** | Proprietário | Elimina o projeto selecionado. Esta operação não pode ser anulada. |
| **Terminal** | Proprietário | Inicia o servidor de projeto, em seguida, abre-se de uma nova janela do browser com o bash para esse servidor de terminal. |
| **+ Novo projeto** | Proprietário | Cria um novo projeto. Ver [criar um novo projeto](#create-a-new-project). |
| **Carregar repositório GitHub** | Owner | Importa um projeto do GitHub. [Importar um projeto do GitHub](#import-a-project-from-github). |
| **Clone** | Qualquer pessoa | Copia um projeto selecionado para a sua própria conta. Pede-lhe para iniciar sessão em se não o tiver feito. Ver [clonar um projeto](#clone-a-project). |

### <a name="obtain-a-launch-badge"></a>Obter um destaque de lançamento

Quando utiliza a **partilha** de comandos e selecione o **incorporação** separador, pode copiar o código HTML ou Markdown que cria um distintivo "Bloco de notas de lançamento":

![Inicie o destaque do bloco de notas](https://notebooks.azure.com/launch.png)

Se não tiver um projeto de blocos de notas do Azure, pode criar uma ligação que clona do GitHub diretamente com os modelos seguintes, substituindo o nome de utilizador apropriado e nomes de repositório:

```html
<a href="https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>"><img src="https://notebooks.azure.com/launch.png" /></a>
```

```markdown
[![Azure Notebooks](https://notebooks.azure.com/launch.png)](https://notebooks.azure.com/import/gh/<GitHub_username>/<repository_name>)
```

## <a name="create-a-new-project"></a>Criar um novo projeto

Quando utiliza a **+ novo projeto** comando, blocos de notas do Azure apresenta uma **criar novo projeto** pop-up. Neste pop-up, introduza as informações seguintes, em seguida, selecione **criar**:

| Campo | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. Por exemplo, "meu projeto de notebook". |
| ID do Projeto | Um identificador personalizado que se tornará parte do URL que utilizar para partilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |
| Inicializar esse projeto com um ficheiro Leia-me | Se definido, cria uma predefinição *README.md* arquivo no projeto. R *README.md* ficheiro é onde fornecer documentação para o seu projeto, se assim o desejar. |

### <a name="reserved-project-ids"></a>IDs de projeto reservadas

As palavras reservadas a seguir não podem ser usadas por si mesmas como IDs de projeto. No entanto, essas palavras reservadas podem ser usadas como parte de IDs de projeto mais longas.

| | | | | | |
| --- | --- | --- | --- | --- | --- |
| pensar | conta | administrar | api | bloga | turma |
| conteúdo | dashboard | apresenta | faq | Obter ajuda | html |
| Casa | importe | biblioteca | gestão | novo | D430 |
| notebooks | pdf | pré-visualização | preços | profile | procurar |
| status | suporte | test | | | |

Se você tentar usar uma dessas palavras como uma ID do projeto, os pop-up **criar novo projeto** e **configurações do projeto** indicarão "a ID da biblioteca é um identificador reservado".

Como uma ID de projeto também faz parte da URL de um projeto, o software bloqueador do AD pode bloquear o uso de determinadas palavras-chave, como "anunciar". Nesses casos, use uma palavra diferente na ID do projeto.

## <a name="import-a-project-from-github"></a>Importar um projeto do GitHub

Pode importar facilmente um repositório do GitHub público inteiro como um projeto, incluindo todos os dados e *README.md* ficheiros. Utilize o **carregar repositório do GitHub** comando, forneça os detalhes seguintes no pop-up, em seguida, selecione **importação**:

| Campo | Descrição |
| --- | --- |
| Repositório do GitHub | O nome do repositório de código-fonte no github.com. Por exemplo, para clonar os blocos de notas do Jupyter para os serviços cognitivos do Azure no [ https://github.com/Microsoft/cognitive-services-notebooks ](https://github.com/Microsoft/cognitive-services-notebooks), introduza "Microsoft/cognitivos-services-blocos de notas".  |
| Clonar recursivamente | Repositórios do GitHub podem conter vários repositórios de subordinados. Defina esta opção se pretender clonar o repositório principal e todos os respetivos subordinados. Como é possível para um repositório ter várias crianças, deixe esta opção clara, a menos que sabe o que for preciso. |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. |
| ID do Projeto | Um identificador personalizado que se tornará parte do URL que utilizar para partilhar um projeto (o formulário é `https://notebooks.azure.com/<user_id>/projects/<project_id>`). Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |

Também importar um repositório do GitHub importa o respetivo histórico. Pode utilizar comandos de Git padrão a partir do terminal para consolidar as alterações novas, tirar alterações do GitHub e assim por diante.

## <a name="clone-a-project"></a>Clonar um projeto

A clonagem cria uma cópia de um projeto existente na sua própria conta, onde pode, em seguida, executar e modificar qualquer bloco de notas ou outro arquivo no projeto. Também pode utilizar a clonagem para fazer cópias dos seus próprios projetos em que fazer experiências ou outro trabalho sem perturbar o projeto original.

Para clonar um projeto:

1. Sobre o **meus projetos** dashboard, clique com o botão direito no projeto pretendido e selecione **Clone** (atalho de teclado: c).

    ![Comando de clonagem no menu de contexto do projeto](media/clone-command.png)

1. Na **projeto de Clone** pop-up, introduza um nome e ID para o clone e especifique se o clone público. Estas definições são os mesmos que para uma [novo projeto](#create-a-new-project).

    ![Pop-up de projeto de clone](media/clone-project.png)

1. Depois de selecionar o **Clone** navega de botão, blocos de notas do Azure diretamente para a cópia.

## <a name="next-steps"></a>Passos Seguintes

- [Explore os blocos de notas de exemplo](azure-notebooks-samples.md)
- [How to: Configurar e gerenciar projetos @ no__t-0
- [How to: Instalar pacotes de dentro de um bloco de anotações @ no__t-0
- [How to: Apresentar uma apresentação de slides @ no__t-0
- [How to: Trabalhar com arquivos de dados @ no__t-0
- [How to: Acessar recursos de dados @ no__t-0
- [How to: Usar os serviços de Azure Machine Learning @ no__t-0
