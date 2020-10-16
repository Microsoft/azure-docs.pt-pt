---
title: Configurar e gerir a pré-visualização de cadernos Azure
description: Saiba como gerir metadados de projetos, ficheiros de projetos, ambiente do projeto e etapas de configuração através da UI Azure Notebooks e acesso direto ao terminal.
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: devx-track-python
ms.openlocfilehash: 7674c2151922d26e069b5cd285cb311d7a18fa98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87852808"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" /> Gerir e configurar projetos em Pré-visualização de Cadernos Azure

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Um projeto em Azure Notebooks Preview é essencialmente uma configuração da máquina virtual Linux subjacente na qual funcionam os cadernos Jupyter, juntamente com uma pasta de ficheiros e metadados descritivos. 

O painel de instrumentos do projeto em Azure Notebooks permite-lhe gerir ficheiros e configurar as características do projeto:

- O nível de cálculo em que o projeto funciona, que pode ser o nível livre ou uma máquina virtual Azure.
- Os metadados do projeto, que incluem um nome, descrição, um identificador que é usado na partilha do projeto, e se o projeto é público ou privado.
- O caderno, dados e outros ficheiros do projeto, que gere como qualquer outro sistema de ficheiros.
- O ambiente de um projeto, que você gere através de scripts de arranque ou diretamente através do terminal.
- Registos, aos quais acessa através do terminal.

> [!Note]
> As funcionalidades de gestão e configuração aqui descritas estão disponíveis apenas para o proprietário do projeto que criou o projeto inicialmente. Pode, no entanto, clonar o projeto na sua própria conta, caso em que se torna proprietário e pode configurar o projeto conforme desejado.

A Azure Notebooks inicia a máquina virtual subjacente sempre que executar um caderno ou outro ficheiro. O servidor guarda automaticamente ficheiros e desliga-se após 60 minutos de inatividade. Também pode parar o servidor a qualquer momento com o comando **'Desligar'** (atalho de teclado: h).

## <a name="compute-tier"></a>Escalão de computação

Por padrão, os projetos funcionam no nível **de Computação Livre,** que está limitado a 4GB de memória e 1GB de dados para prevenir abusos. Pode contornar estas limitações e aumentar a potência de computação utilizando uma máquina virtual diferente que a provisionou numa subscrição do Azure. Para obter mais informações, consulte [Como utilizar máquinas virtuais da Ciência de Dados.](use-data-science-virtual-machine.md)

## <a name="edit-project-metadata"></a>Editar metadados de projeto

No painel de **instrumentos,** selecione Definições de Projeto, em seguida, selecione o **separador Informação,** que contém os metadados do projeto, conforme descrito na tabela seguinte. Pode alterar metadados de projeto a qualquer momento.

| Definições | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que a Azure Notebooks usa para fins de exibição. Por exemplo, "Olá Mundo em Python". |
| ID do Projeto | Um identificador personalizado que se torna parte do URL que usa para partilhar um projeto. Este ID só pode usar letras, números e hífens, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](create-clone-jupyter-notebooks.md#reserved-project-ids). Se não tem a certeza do que usar, uma convenção comum é usar uma versão minúscula do seu nome de projeto onde os espaços são transformados em hífenes, como "my-notebook-project" (truncado se necessário para encaixar o limite de comprimento). |
| Projeto público | Se definido, permite que qualquer pessoa com o link aceda ao projeto. Ao criar um projeto privado, limpe esta opção. |
| Esconder clones | Se definidos, outros utilizadores não podem ver uma lista de clones que foram feitos para este projeto. Esconder clones é útil para projetos que são partilhados com muitas pessoas que não fazem parte da mesma organização, como quando se usa um caderno para ensinar uma aula. |

> [!Important]
>
> Alterar o ID do projeto invalida quaisquer ligações ao projeto que possa ter partilhado anteriormente.

## <a name="manage-project-files"></a>Gerir ficheiros de projetos

O painel de instrumentos do projeto mostra o conteúdo do sistema de pastas do projeto. Pode utilizar vários comandos para gerir esses ficheiros.

### <a name="create-new-files-and-folders"></a>Criar novos ficheiros e pastas

O comando **+ Novo** (atalho de teclado: n) cria novos ficheiros ou pastas. Ao utilizar o comando, selecione primeiro o tipo de item para criar:

| Tipo de artigo | Descrição | Comportamento do comando |
| --- | --- | --- |
| **Bloco de Notas** | Um caderno Jupyter | Exibe um pop-up no qual especifica o nome e o idioma do portátil do caderno. |
| **Pasta** | Uma sub-página | Cria um campo de edição na lista de ficheiros do projeto no qual introduz o nome da pasta. |
| **Arquivo em branco** | Um ficheiro no qual pode armazenar qualquer conteúdo, como texto, dados, etc. | Cria um campo de edição na lista de ficheiros do projeto no qual introduz o nome do ficheiro. |
| **Markdown** | Um ficheiro Markdown. | Cria um campo de edição na lista de ficheiros do projeto no qual introduz o nome do ficheiro. |

### <a name="upload-files"></a>Carregar ficheiros

O comando **Upload** fornece duas opções para importar dados de outros locais: **A partir de URL** e de **Computador**. Para obter mais informações, consulte [Trabalhar com ficheiros de dados em projetos Azure Notebook.](work-with-project-data-files.md)

### <a name="select-file-specific-commands"></a>Selecione comandos específicos de ficheiros

Cada item na lista de ficheiros do projeto fornece comandos através de um menu de contexto de clique à direita:

![Comandos em um menu de contexto de ficheiro](media/project-file-commands.png)

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Executar | r (ou clique) | Executa um ficheiro de caderno. Outros tipos de ficheiros são abertos para visualização.  |
| Copiar Ligação | y | Copia um link para o ficheiro para a área de transferência. |
| Correr no Jupyter Lab | j | Executa um caderno no JupyterLab, que é uma interface mais orientada para o desenvolvimento do que o Jupyter normalmente fornece. |
| Pré-visualizar | p | Abre uma pré-visualização HTML do ficheiro; para cadernos, a pré-visualização é uma renderização apenas de leitura do caderno. Para mais informações, consulte a secção [Pré-visualização.](#preview) |
| Editar ficheiro | i | Abre o ficheiro para edição. |
| Download | d | Descarrega um ficheiro zip que contém o ficheiro ou o conteúdo de uma pasta. |
| Mudar o Nome | um | Solicita um novo nome para o ficheiro ou pasta. |
| Eliminar | x | Solicitações de confirmação e, em seguida, remove permanentemente o ficheiro do projeto. As supressões não podem ser desfeitas. |
| Mover | m | Move um ficheiro para uma pasta diferente no mesmo projeto. |

#### <a name="preview"></a>Pré-visualizar

Uma pré-visualização de um ficheiro ou caderno é uma visão apenas de leitura do conteúdo; as células de cadernos em execução são desativadas. É mostrada uma pré-visualização a qualquer pessoa que tenha um link para o ficheiro ou caderno, mas não tenha assinado em Azure Notebooks. Uma vez assinado, um utilizador pode clonar o caderno para a sua própria conta, ou pode descarregar o caderno para o seu computador local.

A página de pré-visualização suporta vários comandos da barra de ferramentas com atalhos de teclado:

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Partilhar | t | Exibe o popup de partilha a partir do qual pode obter um link, partilhar para as redes sociais, obter HTML para incorporação e enviar um e-mail. |
| Clone | c  | Clone o caderno na sua conta. |
| Executar | r | Executa o caderno se lhe for permitido fazê-lo. |
| Download | d | Descarrega uma cópia do caderno. |

## <a name="configure-the-project-environment"></a>Configure o ambiente do projeto

Existem três formas de configurar o ambiente da máquina virtual subjacente em que os seus cadernos funcionam:

- Inclua um roteiro de inicialização único
- Utilize as definições ambientais do projeto para especificar as etapas de configuração
- Aceda à máquina virtual através de um terminal.

Todas as formas de configuração do projeto são aplicadas sempre que a máquina virtual é iniciada, afetando assim todos os cadernos dentro do projeto.

### <a name="one-time-initialization-script"></a>Roteiro de inicialização única

A primeira vez que o Azure Notebooks cria um servidor para o projeto, procura um ficheiro no projeto chamado *aznbsetup.sh*. Se este ficheiro estiver presente, a Azure Notebooks executa-o. A saída do script é armazenada na sua pasta de projeto como *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Etapas de configuração do ambiente

Pode utilizar as definições ambientais do projeto para criar passos individuais que configuram o ambiente.

No painel de **instrumentos**do projeto, selecione Definições de Projeto, em seguida, selecione o **separador Ambiente** no qual adiciona, remove e modifica os passos de configuração para o projeto:

![Definições de projeto popup com separador Ambiente selecionado](media/project-settings-environment-steps.png)

Para adicionar um passo, selecione primeiro **+ Adicione,** em seguida, selecione um tipo de passo na lista de abandono da **Operação:**

![Seletor de operação para um novo passo de configuração do Ambiente](media/project-settings-environment-details.png)

A informação que então projeta depende do tipo de operação que escolheu:

- **Requirements.txt**: Na segunda lista de drop-down, selecione um ficheiro *requirements.txt* que já está no projeto. Em seguida, selecione uma versão Python da terceira lista de drop-down que aparece. Ao utilizar um ficheiro *requirements.txt, * o Azure Notebooks funciona `pip install -r` com o ficheiro *requirements.txt* ao iniciar um servidor de portátil. Não é necessário instalar explicitamente pacotes a partir do próprio caderno.

- **Script de concha**: Na segunda lista de drop-down, selecione um script de concha de bash no projeto (normalmente um ficheiro com a extensão *.sh)* que contém quaisquer comandos que deseje executar para inicializar o ambiente.

- **Environment.yml**: Na segunda lista de drop-down, selecione um ficheiro *ambiente.yml* para projetos Python utilizando um ambiente conda.

   > [!WARNING]
   > Como este é um serviço de pré-visualização em desenvolvimento, existe atualmente uma questão conhecida em que a `Environment.yml` configuração não é aplicada ao seu projeto como esperado. O projeto e os cadernos Jupyter no interior não carregam atualmente o ficheiro ambiental especificado.

Quando terminar de adicionar passos, **selecione Save**.

### <a name="use-the-terminal"></a>Use o terminal

No painel de instrumentos do projeto, o comando **Terminal** abre um terminal Linux que lhe dá acesso direto ao servidor. Dentro do terminal pode descarregar dados, editar ou gerir ficheiros, inspecionar processos e até usar ferramentas como vi e nano.

> [!Note]
> Se tiver scripts de arranque no ambiente do seu projeto, a abertura do terminal pode apresentar uma mensagem indicando que a configuração ainda está em andamento.

Pode emitir todos os comandos Linux padrão no terminal. Também pode utilizar `ls` na pasta principal para ver os diferentes ambientes existentes na máquina virtual, tais como *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, e *R,* juntamente com uma pasta de *projeto* que contém o projeto:

![Terminal de projeto em Cadernos Azure](media/project-terminal.png)

Para afetar um ambiente específico, mude primeiro os diretórios para essa pasta de ambiente.

Para os ambientes Python, você pode encontrar `pip` e `conda` em *bin* pasta de cada ambiente. Também pode usar pseudónimos embutidos para os ambientes:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

As alterações efetuadas no servidor aplicam-se apenas à sessão atual, com exceção dos ficheiros e pastas que cria na própria pasta do *projeto.* Por exemplo, a edição de um ficheiro na pasta do projeto é persistiu entre as sessões, mas os pacotes com `pip install` eles não são.

> [!Note]
> Se utilizar `python` ou invocar as `python3` versões instaladas no sistema de Python, que não são utilizadas para cadernos. Também não tem permissões para operações `pip install` como as, por isso não se esqueça de usar os pseudónimos específicos da versão.

## <a name="access-notebook-logs"></a>Aceder a registos de cadernos

Se tiver problemas ao executar um caderno, a saída do Jupyter é armazenada numa pasta chamada *.nb.log*. Pode aceder a estes registos através do comando **Terminal** ou do painel de instrumentos do projeto.

Normalmente, quando se dirige o Jupyter localmente, pode ter começado a partir de uma janela terminal. A janela do terminal mostra a saída, tal como o estado do núcleo.

Para visualizar registos, utilize o seguinte comando no terminal:

```bash
cat .nb.log
```

Também pode utilizar o comando a partir de uma célula de código num caderno Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Passos seguintes

- [Como: Trabalhar com ficheiros de dados do projeto](work-with-project-data-files.md)
- [Aceda aos dados da nuvem de acesso em um caderno](access-data-resources-jupyter-notebooks.md)
