---
title: Configure e gerencie a pré-visualização de cadernos Azure
description: Saiba como gerir metadados de projetos, ficheiros de projetos, ambiente do projeto e etapas de configuração através tanto do Azure Notebooks UI como do acesso ao terminal direto.
ms.topic: how-to
ms.date: 02/28/2020
ms.openlocfilehash: 1674effda2cb9bda45f49c91ca618225b0a75f0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280602"
---
# <a name="manage-and-configure-projects-in-azure-notebooks-preview"></a><a id="manage-and-configure-projects" />Gerir e configurar projetos na Pré-visualização de cadernos Azure

Um projeto em Azure Notebooks Preview é essencialmente uma configuração da máquina virtual linux subjacente em que os cadernos Jupyter funcionam, juntamente com uma pasta de ficheiros e metadados descritivos. 

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

O painel de instrumentos do projeto em Cadernos Azure permite-lhe gerir ficheiros e configurar as características do projeto:

- O nível de computação em que o projeto funciona, que pode ser o nível livre ou uma máquina virtual Azure.
- Os metadados do projeto, que inclui um nome, descrição, um identificador que é usado na partilha do projeto, e se o projeto é público ou privado.
- O caderno do projeto, dados e outros ficheiros, que gere como qualquer outro sistema de ficheiros.
- O ambiente de um projeto, que gere quer através de scripts de arranque, quer diretamente através do terminal.
- Registos, a que se acede através do terminal.

> [!Note]
> As funcionalidades de gestão e configuração aqui descritas estão disponíveis apenas para o proprietário do projeto que criou o projeto inicialmente. Pode, no entanto, clonar o projeto na sua própria conta, caso em que se torna o proprietário e pode configurar o projeto conforme desejado.

Os Cadernos Azure iniciam a máquina virtual subjacente sempre que executa um caderno ou outro ficheiro. O servidor guarda automaticamente ficheiros e desliga-se após 60 minutos de inatividade. Também pode parar o servidor a qualquer momento com o comando **'Desligar'** (atalho do teclado: h).

## <a name="compute-tier"></a>Nível de computação

Por padrão, os projetos funcionam no nível **De Computação Livre,** que está limitado a 4GB de memória e 1GB de dados para prevenir abusos. Pode contornar estas limitações e aumentar a potência da computação utilizando uma máquina virtual diferente que você disponibilizou numa subscrição Azure. Para mais informações, consulte [Como utilizar máquinas virtuais](use-data-science-virtual-machine.md)de ciência de dados .

## <a name="edit-project-metadata"></a>Editar metadados de projeto

No painel de instrumentos do projeto, selecione Definições de **Projeto,** em seguida, selecione o separador **Informação,** que contém os metadados do projeto, conforme descrito na tabela seguinte. Pode alterar os metadados do projeto a qualquer momento.

| Definição | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que os Cadernos Azure usam para fins de exibição. Por exemplo, "Olá Mundo em Python". |
| ID do Projeto | Um identificador personalizado que se torna parte do URL que usa para partilhar um projeto. Este ID só pode usar letras, números e hífenes, está limitado a 30 caracteres, e não pode ser um [ID de projeto reservado](create-clone-jupyter-notebooks.md#reserved-project-ids). Se não tem a certeza do que usar, uma convenção comum é usar uma versão minúscula do seu nome de projeto onde os espaços são transformados em hífens, como "my-notebook-project" (truncado se necessário para se ajustar ao limite de comprimento). |
| Projeto público | Se definido, permite que qualquer pessoa com o link aceda ao projeto. Ao criar um projeto privado, limpe esta opção. |
| Ocultar clones | Se definido, outros utilizadores não podem ver uma lista de clones que foram feitos para este projeto. Esconder clones é útil para projetos que são partilhados com muitas pessoas que não fazem parte da mesma organização, como quando se usa um caderno para dar aulas. |

> [!Important]
>
> Alterar o ID do projeto invalida quaisquer ligações ao projeto que possa ter partilhado anteriormente.

## <a name="manage-project-files"></a>Gerir ficheiros de projeto

O painel de instrumentos do projeto mostra o conteúdo do sistema de pastas do projeto. Pode usar vários comandos para gerir esses ficheiros.

### <a name="create-new-files-and-folders"></a>Criar novos ficheiros e pastas

O **comando + Novo** (atalho de teclado: n) cria novos ficheiros ou pastas. Ao utilizar o comando, selecione primeiro o tipo de item para criar:

| Tipo de item | Descrição | Comportamento de comando |
| --- | --- | --- |
| **Bloco de Notas** | Um caderno jupyter | Exibe um pop-up no qual especifica o nome de ficheiro e a linguagem do caderno. |
| **Pasta** | Uma subpasta | Cria um campo de edição na lista de ficheiros do projeto no qual introduz o nome da pasta. |
| **Arquivo Em Branco** | Um ficheiro no qual pode armazenar qualquer conteúdo, como texto, dados, etc. | Cria um campo de edição na lista de ficheiros do projeto no qual introduz o nome do ficheiro. |
| **Markdown** | Um ficheiro Markdown. | Cria um campo de edição na lista de ficheiros do projeto no qual introduz o nome do ficheiro. |

### <a name="upload-files"></a>Carregar ficheiros

O comando **upload** fornece duas opções para importar dados de outros locais: **De URL** e **From Computer**. Para mais informações, consulte [Trabalhar com ficheiros de dados em projetos do Caderno Azure.](work-with-project-data-files.md)

### <a name="select-file-specific-commands"></a>Selecione comandos específicos de ficheiros

Cada item na lista de ficheiros do projeto fornece comandos através de um menu de contexto de clique direito:

![Comandos em um menu de contexto de arquivo](media/project-file-commands.png)

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Executar | r (ou clique) | Tem um ficheiro de caderno. Outros tipos de ficheiros estão abertos para visualização.  |
| Copiar Ligação | S | Copia um link para o ficheiro para a área de recção. |
| Correr no Jupyter Lab | j | Executa um caderno no JupyterLab, que é uma interface mais orientada para o desenvolvimento do que a Jupyter normalmente fornece. |
| Pré-visualização | p | Abre uma pré-visualização HTML do ficheiro; para os cadernos, a pré-visualização é uma renderização apenas de leitura do caderno. Para mais informações, consulte a secção [Preview.](#preview) |
| Editar ficheiro | i | Abre o ficheiro para edição. |
| Transferência | d | Descarrega um ficheiro zip que contém o ficheiro ou o conteúdo de uma pasta. |
| Mudar o Nome | a | Solicita um novo nome para o ficheiro ou pasta. |
| Eliminar | x | Solicitações para confirmação e, em seguida, remove permanentemente o ficheiro do projeto. As supressões não podem ser desfeitas. |
| Mover | m | Move um ficheiro para uma pasta diferente no mesmo projeto. |

#### <a name="preview"></a>Pré-visualização

Uma pré-visualização de um ficheiro ou caderno é uma visão apenas para leitura dos conteúdos; as células de caderno sintetizadas são desativadas. Uma pré-visualização é mostrada a qualquer pessoa que tenha um link para o arquivo ou caderno, mas não tenha assinado em Cadernos Azure. Uma vez inscrito, um utilizador pode clonar o caderno na sua própria conta, ou pode transferir o caderno para o seu computador local.

A página de pré-visualização suporta vários comandos de barra de ferramentas com atalhos de teclado:

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Partilhado | t | Exibe o popup de partilha a partir do qual pode obter um link, partilhar para as redes sociais, obter HTML para incorporação e enviar um e-mail. |
| Clone | c  | Clone o caderno na sua conta. |
| Executar | r | Executa o caderno se lhe for permitido fazê-lo. |
| Transferência | d | Descarrega uma cópia do caderno. |

## <a name="configure-the-project-environment"></a>Configure o ambiente do projeto

Existem três formas de configurar o ambiente da máquina virtual subjacente em que os seus cadernos funcionam:

- Inclua um roteiro de inicialização única
- Utilize as definições ambientais do projeto para especificar passos de configuração
- Aceda à máquina virtual através de um terminal.

Todas as formas de configuração do projeto são aplicadas sempre que a máquina virtual é iniciada, afetando assim todos os cadernos dentro do projeto.

### <a name="one-time-initialization-script"></a>Roteiro de inicialização única

Os primeiros Cadernos Azure criam um servidor para o projeto, procura um ficheiro no projeto chamado *aznbsetup.sh*. Se este ficheiro estiver presente, os Cadernos Azure executam-no. A saída do script é armazenada na sua pasta de projeto como *.aznbsetup.log*.

### <a name="environment-setup-steps"></a>Etapas de configuração do ambiente

Pode utilizar as configurações ambientais do projeto para criar passos individuais que configurem o ambiente.

No painel de instrumentos do projeto, selecione Definições de **Projeto,** em seguida, selecione o separador **Ambiente** no qual adiciona, remove e modifica os passos de configuração para o projeto:

![Configurações do projeto popup com separador Ambiente selecionado](media/project-settings-environment-steps.png)

Para adicionar um passo, primeiro selecione **+ Adicione,** em seguida, selecione um tipo de passo na lista de drop-down **da Operação:**

![Selecionador de operação para um novo passo de configuração do Ambiente](media/project-settings-environment-details.png)

A informação que então projeta depende do tipo de operação que escolheu:

- **Requisitos.txt**: Na segunda lista de drop-down, selecione um ficheiro *requirements.txt* que já está no projeto. Em seguida, selecione uma versão Python da terceira lista de drop-down que aparece. Ao utilizar um ficheiro *requirements.txt,* `pip install -r` os Notebooks Do Azure executam o ficheiro *requisitos.txt* ao iniciar um servidor de portátil. Não precisa de instalar explicitamente pacotes dentro do próprio caderno.

- **Script shell**: Na segunda lista de drop-down, selecione um script de concha de bash no projeto (tipicamente um ficheiro com a extensão *.sh)* que contenha quaisquer comandos que deseje executar para inicializar o ambiente.

- **Ambiente.yml**: Na segunda lista de descidas, selecione um ficheiro *ambientes.yml* para projetos Python utilizando um ambiente conda.

   > [!WARNING]
   > Como este é um serviço de pré-visualização em `Environment.yml` desenvolvimento, existe atualmente uma questão conhecida em que a definição não é aplicada ao seu projeto como esperado. O projeto e os cadernos Jupyter no interior não carregam atualmente o ficheiro ambiente especificado.

Quando terminar de adicionar passos, selecione **Guardar**.

### <a name="use-the-terminal"></a>Use o terminal

No painel de instrumentos do projeto, o comando **Terminal** abre um terminal Linux que lhe dá acesso direto ao servidor. Dentro do terminal pode descarregar dados, editar ou gerir ficheiros, inspecionar processos e até mesmo usar ferramentas como vi e nano.

> [!Note]
> Se tiver scripts de arranque no ambiente do seu projeto, a abertura do terminal pode apresentar uma mensagem indicando que a configuração ainda está em curso.

Pode emitir quaisquer comandos linux padrão no terminal. Também pode `ls` utilizar na pasta inicial para ver os diferentes ambientes que existem na máquina virtual, como *anaconda2_501*, *anaconda3_420*, *anaconda3_501,* *IfSharp*e *R*, juntamente com uma pasta de *projeto* que contém o projeto:

![Terminal de projeto sintetário sintetários em Cadernos Azure](media/project-terminal.png)

Para afetar um ambiente específico, mude os diretórios para a pasta do ambiente primeiro.

Para os ambientes Python, `pip` `conda` você pode encontrar e na pasta de *lixo* de cada ambiente. Também pode usar pseudónimos incorporados para os ambientes:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

As alterações feitas no servidor aplicam-se apenas à sessão atual, com exceção dos ficheiros e pastas que cria na própria pasta do *projeto.* Por exemplo, a edição de um ficheiro na pasta do `pip install` projeto é persistente entre sessões, mas os pacotes com não são.

> [!Note]
> Se utilizar `python` `python3`ou invocar as versões instaladas pelo sistema de Python, que não são utilizadas para cadernos. Também não tem permissões `pip install` para operações como esta, por isso não se esqueça de usar os pseudónimos específicos da versão.

## <a name="access-notebook-logs"></a>Aceder a registos de cadernos

Se tiver problemas ao executar um caderno, a saída da Jupyter é armazenada numa pasta chamada *.nb.log*. Pode aceder a estes registos através do comando **terminal** ou do painel de instrumentos do projeto.

Normalmente, quando se dirige o Jupyter localmente, pode supor-se a partir de uma janela terminal. A janela do terminal mostra saída sintetizando o estado do núcleo.

Para visualizar os registos, utilize o seguinte comando no terminal:

```bash
cat .nb.log
```

Também pode usar o comando a partir de uma célula de código num caderno Python:

```bash
!cat .nb.log
```

## <a name="next-steps"></a>Passos seguintes

- [Como: Trabalhar com ficheiros de dados do projeto](work-with-project-data-files.md)
- [Aceder a dados da cloud num bloco de notas](access-data-resources-jupyter-notebooks.md)
