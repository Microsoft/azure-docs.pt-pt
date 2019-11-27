---
title: Configurar e gerir projetos de bloco de notas do Azure
description: Como gerir metadados de projeto, arquivos de projeto, passos de configuração e de ambiente do projeto através da IU de blocos de notas do Azure e o acesso direto de terminal.
ms.topic: article
ms.date: 05/13/2019
ms.openlocfilehash: 56c265122894412e79b3d5a7b256964c49ab81a6
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74277647"
---
# <a name="manage-and-configure-projects"></a>Gerir e configurar projetos

Um projeto em blocos de notas do Azure é, essencialmente, uma configuração da máquina de virtual de Linux subjacente no qual executar blocos de notas do Jupyter, juntamente com uma pasta de ficheiros e metadados descritivos. Dashboard do projeto em blocos de notas do Azure permite-lhe gerir os ficheiros e caso contrário, configure características do projeto:

- A camada de computação na qual o projeto é executado, que pode ser a camada gratuita ou uma máquina virtual do Azure.
- Metadados do projeto, que incluem um nome, uma descrição, um identificador que é usado ao compartilhar o projeto e se o projeto é público ou privado.
- O bloco de anotações, os dados e outros arquivos do projeto, que você gerencia como qualquer outro sistema de arquivos.
- O ambiente de um projeto, que você gerencia por meio dos scripts de inicialização ou diretamente por meio do terminal.
- Logs, que você acessa por meio do terminal.

> [!Note]
> Os recursos de gerenciamento e configuração descritos aqui estão disponíveis apenas para o proprietário do projeto que criou o projeto inicialmente. No entanto, você pode clonar o projeto em sua própria conta, caso em que você se torna o proprietário e pode configurar o projeto conforme desejado.

Blocos de notas do Azure inicia a máquina virtual subjacente sempre que executar um bloco de notas ou outro ficheiro. O servidor automaticamente guarda ficheiros e encerra após 60 minutos de inatividade. Você também pode interromper o servidor a qualquer momento com o comando de **desligamento** (atalho de teclado: h).

## <a name="compute-tier"></a>Camada de computação

Por padrão, os projetos são executados na camada de **computação gratuita** , que é limitada a 4 GB de memória e 1GB de dados para evitar abusos. Você pode ignorar essas limitações e aumentar a capacidade de computação usando uma máquina virtual diferente que você provisionou em uma assinatura do Azure. Para obter mais informações, consulte [como usar máquinas virtuais de ciência de dados](use-data-science-virtual-machine.md).

## <a name="edit-project-metadata"></a>Editar metadados do projeto

No painel do projeto, selecione **configurações do projeto**e, em seguida, selecione a guia **informações** , que contém os metadados do projeto, conforme descrito na tabela a seguir. Pode alterar os metadados de projeto em qualquer altura.

| Definição | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. Por exemplo, "Hello World no Python". |
| ID do Projeto | Um identificador personalizado que se tornará parte do URL que utilizar para partilhar um projeto. Essa ID pode usar apenas letras, números e hifens, é limitada a 30 caracteres e não pode ser uma [ID de projeto reservada](create-clone-jupyter-notebooks.md#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Projeto público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |
| Ocultar clones | Se definido, outros utilizadores não podem ver uma lista de clones que foram feitos para este projeto. Ocultar clones é útil para os projetos que são partilhados com muitas pessoas que não fazem parte da mesma organização, tal como quando utiliza um bloco de notas para ensinar uma classe. |

> [!Important]
>
> Alterar o ID de projeto invalida todas as ligações para o projeto que talvez tenha partilhado anteriormente.

## <a name="manage-project-files"></a>Gerir ficheiros de projeto

Dashboard do projeto mostra o conteúdo do sistema de pasta do projeto. Pode utilizar vários comandos para gerenciar esses arquivos.

### <a name="create-new-files-and-folders"></a>Criar novos ficheiros e pastas

O comando **+ novo** (atalho de teclado: n) cria novos arquivos ou pastas. Ao utilizar o comando, primeiro selecione o tipo de item para criar:

| Tipo de item | Descrição | Comportamento do comando |
| --- | --- | --- |
| **D430** | Um bloco de notas do Jupyter | Apresenta um pop-up no qual especifique os nome de ficheiro e o idioma do bloco de notas. |
| **Pasta** | Uma subpasta | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome da pasta. |
| **Arquivo em branco** | Um ficheiro no qual pode armazenar qualquer conteúdo, como texto, dados, etc. | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome de ficheiro. |
| **Markdown** | Um ficheiro de Markdown. | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome de ficheiro. |

### <a name="upload-files"></a>Carregar ficheiros

O comando **upload** fornece duas opções para importar dados de outros locais: **da URL** e **do computador**. Para obter mais informações, consulte [trabalhar com arquivos de dados em projetos do bloco de anotações do Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Selecione a comandos específicos de ficheiro

Cada item na lista de ficheiros do projeto fornece comandos por meio de um menu de contexto de contexto:

![Comandos num menu de contexto do ficheiro](media/project-file-commands.png)

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Executar | r (ou clique) | Executa um ficheiro do bloco de notas. Outros tipos de arquivos estão abertos para visualização.  |
| Copiar ligação | Y | Copia um link para o ficheiro para a área de transferência. |
| Execute no laboratório do Jupyter | J | Executa um bloco de notas no JupyterLab, que é que o Jupyter normalmente fornece uma interface mais orientadas a desenvolvedores. |
| Pré-visualização | p | Abre-se de uma visualização HTML do arquivo; para blocos de notas, a pré-visualização é um processamento só de leitura do bloco de notas. Para obter mais informações, consulte a seção [Visualização](#preview) . |
| Editar ficheiro | Eu | Abre o arquivo para edição. |
| Transferência | d | Transfere um ficheiro zip que contém o ficheiro ou o conteúdo de uma pasta. |
| Mudar o Nome | a | Pedidos de um novo nome para o ficheiro ou pasta. |
| Eliminar | x | Solicita a confirmação e, em seguida, remove permanentemente o arquivo do projeto. As eliminações não podem ser anuladas. |
| Mover | m | Move um ficheiro para uma pasta diferente no mesmo projeto. |

#### <a name="preview"></a>Pré-visualização

Uma visualização de um ficheiro ou o bloco de notas é uma vista só de leitura do conteúdo; executar células de bloco de notas está desativada. Uma pré-visualização é mostrada a qualquer pessoa que tem uma ligação para o ficheiro ou o bloco de notas, mas não iniciou sessão em blocos de notas do Azure. Depois de iniciar sessão, um utilizador pode clonar o bloco de notas para sua própria conta, ou pode transferir o bloco de notas para o respetivo computador local.

A página de pré-visualização suporta vários comandos de barra de ferramentas com atalhos de teclado:

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Partilhar | s | Apresenta o popup partilha a partir da qual pode obter uma ligação, partilhar a mídia social, obter o HTML para incorporar e enviar um e-mail. |
| Clone | c  | Clone o bloco de notas à sua conta. |
| Executar | R | Se tem permissão para fazer isso, é executado o bloco de notas. |
| Transferência | d | Transfere uma cópia do bloco de notas. |

## <a name="configure-the-project-environment"></a>Configurar o ambiente de projeto

Existem três formas de configurar o ambiente da máquina virtual subjacente no qual executar seus blocos de notas:

- Incluir um script de inicialização única
- Utilize as definições de ambiente do projeto para especificar os passos de configuração
- Aceda à máquina virtual através de um terminal.

São aplicados a todos os formulários de configuração do projeto sempre que a máquina virtual é iniciada e, portanto, afeta todos os blocos de notas dentro do projeto.

### <a name="one-time-initialization-script"></a>Script de inicialização única

O Azure Notebooks da primeira vez que cria um servidor para o projeto, ele procura um arquivo no projeto chamado *aznbsetup.sh*. Se esse arquivo estiver presente, Azure Notebooks o executará. A saída do script é armazenada na pasta do projeto como *. aznbsetup. log*.

### <a name="environment-setup-steps"></a>Passos de configuração do ambiente

Pode utilizar definições de ambiente do projeto para criar passos individuais que configurar o ambiente.

No painel do projeto, selecione **configurações do projeto**e, em seguida, selecione a guia **ambiente** na qual você adiciona, remove e modifica as etapas de configuração para o projeto:

![Pop-up de definições de projeto com o separador de ambiente selecionado](media/project-settings-environment-steps.png)

Para adicionar uma etapa, primeiro selecione **+ Adicionar**e, em seguida, selecione um tipo de etapa na lista suspensa **operação** :

![Seletor de operação para um novo passo de configuração do ambiente](media/project-settings-environment-details.png)

As informações que, em seguida, do projeto dependem do tipo de operação que escolheu:

- **Requirements. txt**: na segunda lista suspensa, selecione um arquivo *requirements. txt* que já está no projeto. Em seguida, selecione uma versão de Python a terceiro na lista pendente que é apresentada. Usando um arquivo *requirements. txt* , o Azure notebooks é executado `pip install -r` com o arquivo *requirements. txt* ao iniciar um servidor de notebook. Não precisa de instalar explicitamente os pacotes a partir do bloco de notas.

- **Script de shell**: na segunda lista suspensa, selecione um script de shell bash no projeto (normalmente um arquivo com a extensão *. sh* ) que contém os comandos que você deseja executar para inicializar o ambiente.

- **Environment. yml**: na segunda lista suspensa, selecione um arquivo *Environments. yml* para projetos do Python usando um ambiente Conda.

Quando você terminar de adicionar etapas, selecione **salvar**.

### <a name="use-the-terminal"></a>Utilizar o terminal

No painel do projeto, o comando **terminal** abre um terminal Linux que lhe dá acesso direto ao servidor. No terminal pode transferir dados, editar ou gerir ficheiros, Inspecione os processos e até mesmo usar ferramentas como o vi e nano.

> [!Note]
> Se tiver scripts de inicialização no ambiente do seu projeto, abrir o terminal pode exibir uma mensagem a indicar que a instalação ainda está em curso.

Pode emitir quaisquer comandos de Linux padrão no terminal. Você também pode usar `ls` na pasta base para ver os diferentes ambientes que existem na máquina virtual, como *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*e *R*, juntamente com uma pasta de *projeto* que contém o projeto:

![Terminal de projeto em blocos de notas do Azure](media/project-terminal.png)

Para afetar um ambiente específico, altere os diretórios dentro dessa pasta de ambiente em primeiro lugar.

Para os ambientes do Python, você pode encontrar `pip` e `conda` na pasta *bin* de cada ambiente. Também pode utilizar aliases incorporadas para os ambientes:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

As alterações feitas no servidor aplicam-se somente à sessão atual, exceto para arquivos e pastas que você cria na própria pasta do *projeto* . Por exemplo, a edição de um arquivo na pasta do projeto é persistida entre as sessões, mas os pacotes com `pip install` não são.

> [!Note]
> Se você usar `python` ou `python3`, você invocará as versões instaladas pelo sistema do Python, que não são usadas para blocos de anotações. Você não tem permissões para operações como `pip install`, portanto, certifique-se de usar os aliases específicos de versão.

## <a name="access-notebook-logs"></a>Aceder aos registos de bloco de notas

Se você tiver problemas ao executar um bloco de anotações, a saída de Jupyter será armazenada em uma pasta chamada *. NB. log*. Você pode acessar esses logs por meio do comando **terminal** ou do painel do projeto.

Normalmente, quando estiver a executar localmente Jupyter talvez tenha começado-lo a partir de uma janela de terminal. A janela de terminal mostra a saída, tais como estado de kernel.

Para ver os registos, utilize o comando seguinte no terminal:

```bash
cat .nb.log
```

Também pode utilizar o comando a partir de uma célula de código num bloco de notas do Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Passos seguintes

- [Como trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
- [Acessar dados de nuvem em um bloco de anotações](access-data-resources-jupyter-notebooks.md)
