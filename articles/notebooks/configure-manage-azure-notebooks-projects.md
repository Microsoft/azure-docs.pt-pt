---
title: Configure e gerencie a pré-visualização de cadernos Azure
description: Saiba como gerir metadados de projetos, ficheiros de projetos, ambiente do projeto e etapas de configuração através tanto do Azure Notebooks UI como do acesso ao terminal direto.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280602"
---
# <a name="a-idmanage-and-configure-projects--manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" /> Gerir e configurar projetos na Pré-visualização de cadernos Azure

Um projeto em Azure Notebooks Preview é essencialmente uma configuração da máquina virtual linux subjacente em que os cadernos Jupyter funcionam, juntamente com uma pasta de ficheiros e metadados descritivos. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Dashboard do projeto em blocos de notas do Azure permite-lhe gerir os ficheiros e caso contrário, configure características do projeto:

- O nível de computação em que o projeto funciona, que pode ser o nível livre ou uma máquina virtual Azure.
- Os metadados do projeto, que inclui um nome, descrição, um identificador que é usado na partilha do projeto, e se o projeto é público ou privado.
- O caderno do projeto, dados e outros ficheiros, que gere como qualquer outro sistema de ficheiros.
- O ambiente de um projeto, que gere quer através de scripts de arranque, quer diretamente através do terminal.
- Registos, a que se acede através do terminal.

> [!Note]
> As funcionalidades de gestão e configuração aqui descritas estão disponíveis apenas para o proprietário do projeto que criou o projeto inicialmente. Pode, no entanto, clonar o projeto na sua própria conta, caso em que se torna o proprietário e pode configurar o projeto conforme desejado.

Blocos de notas do Azure inicia a máquina virtual subjacente sempre que executar um bloco de notas ou outro ficheiro. O servidor automaticamente guarda ficheiros e encerra após 60 minutos de inatividade. Também pode parar o servidor a qualquer momento com o comando **'Desligar'** (atalho do teclado: h).

## <a name="compute-tier"></a>Nível de computação

Por padrão, os projetos funcionam no nível **De Computação Livre,** que está limitado a 4GB de memória e 1GB de dados para prevenir abusos. Pode contornar estas limitações e aumentar a potência da computação utilizando uma máquina virtual diferente que você disponibilizou numa subscrição Azure. Para mais informações, consulte [Como utilizar máquinas virtuais](use-data-science-virtual-machine.md)de ciência de dados .

## <a name="edit-project-metadata"></a>Editar metadados do projeto

No painel de instrumentos do projeto, selecione Definições de **Projeto,** em seguida, selecione o separador **Informação,** que contém os metadados do projeto, conforme descrito na tabela seguinte. Pode alterar os metadados de projeto em qualquer altura.

| Definição | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. Por exemplo, "Hello World no Python". |
| ID do Projeto | Um identificador personalizado que se tornará parte do URL que utilizar para partilhar um projeto. Este ID só pode usar letras, números e hífenes, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](create-clone-jupyter-notebooks.md#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Projeto público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |
| Ocultar clones | Se definido, outros utilizadores não podem ver uma lista de clones que foram feitos para este projeto. Ocultar clones é útil para os projetos que são partilhados com muitas pessoas que não fazem parte da mesma organização, tal como quando utiliza um bloco de notas para ensinar uma classe. |

> [!Important]
>
> Alterar o ID de projeto invalida todas as ligações para o projeto que talvez tenha partilhado anteriormente.

## <a name="manage-project-files"></a>Gerir ficheiros de projeto

Dashboard do projeto mostra o conteúdo do sistema de pasta do projeto. Pode utilizar vários comandos para gerenciar esses arquivos.

### <a name="create-new-files-and-folders"></a>Criar novos ficheiros e pastas

O **comando + Novo** (atalho de teclado: n) cria novos ficheiros ou pastas. Ao utilizar o comando, primeiro selecione o tipo de item para criar:

| Tipo de item | Descrição | Comportamento do comando |
| --- | --- | --- |
| **Caderno** | Um bloco de notas do Jupyter | Apresenta um pop-up no qual especifique os nome de ficheiro e o idioma do bloco de notas. |
| **Pasta** | Uma subpasta | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome da pasta. |
| **Arquivo Em Branco** | Um ficheiro no qual pode armazenar qualquer conteúdo, como texto, dados, etc. | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome de ficheiro. |
| **Marcação** | Um ficheiro de Markdown. | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome de ficheiro. |

### <a name="upload-files"></a>Carregar ficheiros

O comando **upload** fornece duas opções para importar dados de outros locais: **De URL** e **From Computer**. Para mais informações, consulte [Trabalhar com ficheiros de dados em projetos do Caderno Azure.](work-with-project-data-files.md)

### <a name="select-file-specific-commands"></a>Selecione a comandos específicos de ficheiro

Cada item na lista de ficheiros do projeto fornece comandos por meio de um menu de contexto de contexto:

![Comandos num menu de contexto do ficheiro](media/project-file-commands.png)

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Executar | r (ou clique) | Executa um ficheiro do bloco de notas. Outros tipos de arquivos estão abertos para visualização.  |
| Copiar ligação | Y | Copia um link para o ficheiro para a área de transferência. |
| Execute no laboratório do Jupyter | J | Executa um bloco de notas no JupyterLab, que é que o Jupyter normalmente fornece uma interface mais orientadas a desenvolvedores. |
| Pré-visualização | p | Abre-se de uma visualização HTML do arquivo; para blocos de notas, a pré-visualização é um processamento só de leitura do bloco de notas. Para mais informações, consulte a secção [Preview.](#preview) |
| Editar ficheiro | Eu | Abre o arquivo para edição. |
| Transferência | d | Transfere um ficheiro zip que contém o ficheiro ou o conteúdo de uma pasta. |
| Mudar o nome | a | Pedidos de um novo nome para o ficheiro ou pasta. |
| Eliminar | x | Solicita a confirmação e, em seguida, remove permanentemente o arquivo do projeto. As eliminações não podem ser anuladas. |
| Mover | m | Move um ficheiro para uma pasta diferente no mesmo projeto. |

#### <a name="preview"></a>Pré-visualização

Uma visualização de um ficheiro ou o bloco de notas é uma vista só de leitura do conteúdo; executar células de bloco de notas está desativada. Uma pré-visualização é mostrada a qualquer pessoa que tem uma ligação para o ficheiro ou o bloco de notas, mas não iniciou sessão em blocos de notas do Azure. Depois de iniciar sessão, um utilizador pode clonar o bloco de notas para sua própria conta, ou pode transferir o bloco de notas para o respetivo computador local.

A página de pré-visualização suporta vários comandos de barra de ferramentas com atalhos de teclado:

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Partilha | s | Apresenta o popup partilha a partir da qual pode obter uma ligação, partilhar a mídia social, obter o HTML para incorporar e enviar um e-mail. |
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

Os primeiros Cadernos Azure criam um servidor para o projeto, procura um ficheiro no projeto chamado *aznbsetup.sh*. Se este ficheiro estiver presente, os Cadernos Azure executam-no. A saída do script é armazenada na sua pasta de projeto como *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Passos de configuração do ambiente

Pode utilizar definições de ambiente do projeto para criar passos individuais que configurar o ambiente.

No painel de instrumentos do projeto, selecione Definições de **Projeto,** em seguida, selecione o separador **Ambiente** no qual adiciona, remove e modifica os passos de configuração para o projeto:

![Pop-up de definições de projeto com o separador de ambiente selecionado](media/project-settings-environment-steps.png)

Para adicionar um passo, primeiro selecione **+ Adicione,** em seguida, selecione um tipo de passo na lista de drop-down **da Operação:**

![Seletor de operação para um novo passo de configuração do ambiente](media/project-settings-environment-details.png)

As informações que, em seguida, do projeto dependem do tipo de operação que escolheu:

- **Requisitos.txt**: Na segunda lista de drop-down, selecione um ficheiro *requirements.txt* que já está no projeto. Em seguida, selecione uma versão de Python a terceiro na lista pendente que é apresentada. Ao utilizar um ficheiro *requirements.txt,* os Notebooks Do Azure executam `pip install -r` com o ficheiro *requirements.txt* ao iniciar um servidor de portátil. Não precisa de instalar explicitamente os pacotes a partir do bloco de notas.

- **Script shell**: Na segunda lista de drop-down, selecione um script de concha de bash no projeto (tipicamente um ficheiro com a extensão *.sh)* que contenha quaisquer comandos que deseje executar para inicializar o ambiente.

- **Ambiente.yml**: Na segunda lista de descidas, selecione um ficheiro *ambientes.yml* para projetos Python utilizando um ambiente conda.

   > [!WARNING]
   > Como este é um serviço de pré-visualização em desenvolvimento, existe atualmente uma questão conhecida em que a definição de `Environment.yml` não é aplicada ao seu projeto como esperado. O projeto e os cadernos Jupyter no interior não carregam atualmente o ficheiro ambiente especificado.

Quando terminar de adicionar passos, selecione **Guardar**.

### <a name="use-the-terminal"></a>Utilizar o terminal

No painel de instrumentos do projeto, o comando **Terminal** abre um terminal Linux que lhe dá acesso direto ao servidor. No terminal pode transferir dados, editar ou gerir ficheiros, Inspecione os processos e até mesmo usar ferramentas como o vi e nano.

> [!Note]
> Se tiver scripts de inicialização no ambiente do seu projeto, abrir o terminal pode exibir uma mensagem a indicar que a instalação ainda está em curso.

Pode emitir quaisquer comandos de Linux padrão no terminal. Também pode utilizar `ls` na pasta inicial para ver os diferentes ambientes que existem na máquina virtual, como *anaconda2_501,* *anaconda3_420,* *anaconda3_501,* *IfSharp*e *R,* juntamente com uma pasta de *projeto* que contém o projeto:

![Terminal de projeto em blocos de notas do Azure](media/project-terminal.png)

Para afetar um ambiente específico, altere os diretórios dentro dessa pasta de ambiente em primeiro lugar.

Para os ambientes Python, você pode encontrar `pip` e `conda` *na* pasta bin de cada ambiente. Também pode utilizar aliases incorporadas para os ambientes:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

As alterações feitas no servidor aplicam-se apenas à sessão atual, com exceção dos ficheiros e pastas que cria na própria pasta do *projeto.* Por exemplo, a edição de um ficheiro na pasta do projeto é persistente entre sessões, mas pacotes com `pip install` não são.

> [!Note]
> Se utilizar `python` ou `python3`, invoque as versões instaladas pelo sistema de Python, que não são utilizadas para cadernos. Também não tem permissões para operações como `pip install`, por isso não se esqueça de usar os pseudónimos específicos da versão.

## <a name="access-notebook-logs"></a>Aceder aos registos de bloco de notas

Se tiver problemas ao executar um caderno, a saída da Jupyter é armazenada numa pasta chamada *.nb.log*. Pode aceder a estes registos através do comando **terminal** ou do painel de instrumentos do projeto.

Normalmente, quando estiver a executar localmente Jupyter talvez tenha começado-lo a partir de uma janela de terminal. A janela de terminal mostra a saída, tais como estado de kernel.

Para ver os registos, utilize o comando seguinte no terminal:

```bash
cat .nb.log
```

Também pode utilizar o comando a partir de uma célula de código num bloco de notas do Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Passos Seguintes

- [Como: Trabalhar com ficheiros de dados do projeto](work-with-project-data-files.md)
- [Aceda aos dados da nuvem num caderno](access-data-resources-jupyter-notebooks.md)
