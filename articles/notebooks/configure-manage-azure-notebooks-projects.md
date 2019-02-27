---
title: Configurar e gerir projetos de bloco de notas do Azure
description: Como gerir metadados de projeto, arquivos de projeto, passos de configuração e de ambiente do projeto através da IU de blocos de notas do Azure e o acesso direto de terminal.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 35dd6ff1-a14a-4a2e-b173-6d8467de3e89
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2019
ms.author: kraigb
ms.openlocfilehash: 2c81369bc278a0995af04a38b0143eb0df8ba3a5
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56877372"
---
# <a name="manage-and-configure-projects"></a>Gerir e configurar projetos

Um projeto em blocos de notas do Azure é, essencialmente, uma configuração da máquina de virtual de Linux subjacente no qual executar blocos de notas do Jupyter, juntamente com uma pasta de ficheiros e metadados descritivos. Dashboard do projeto em blocos de notas do Azure permite-lhe gerir os ficheiros e caso contrário, configure características do projeto:

- A escala de computação em que o projeto é executado, que pode ser o escalão gratuito ou de uma máquina virtual do Azure.
- Metadados de projeto, que incluem um nome, descrição, um identificador que é usado quando o projeto de partilha e se o projeto é pública ou privada.
- Bloco de notas do projeto, dados e outros ficheiros, o que pode gerir, como qualquer outro sistema de ficheiros.
- Ambiente de um projeto, que gere através de scripts de inicialização ou diretamente por meio de terminal.
- Registos, o que pode aceder através do terminal.

> [!Note]
> As funcionalidades de gestão e configuração descritas aqui só estão disponíveis para o proprietário do projeto que criou o projeto inicialmente. No entanto, pode, clonar o projeto para a sua própria conta, caso em que se tornar o proprietário e pode configurar o projeto conforme pretendido.

Blocos de notas do Azure inicia a máquina virtual subjacente sempre que executar um bloco de notas ou outro ficheiro. O servidor automaticamente guarda ficheiros e encerra após 60 minutos de inatividade. Também pode parar o servidor em qualquer altura com o **encerramento** comando (atalho de teclado: h).

## <a name="compute-tier"></a>Escalão de computação

O **executar** na lista pendente no dashboard do projeto é onde seleciona o escalão de computação em que o projeto é executado. Por predefinição, os projetos de executar no **computação gratuita** escalão, o que está limitado a 4 GB de memória e 1 GB de dados para evitar abusos:

![Lista pendente de camada no dashboard do projeto de computação](media/project-compute-tier-list.png)

Pode ignorar estas limitações ao utilizar uma máquina virtual diferente que já aprovisionados numa subscrição do Azure. Tem de instalar e executar JupyterHub nessa máquina virtual. As imagens de máquina de Virtual de ciência de dados (qualquer sistema operativo) são boas opções, pois eles incluem JupyterHub por predefinição.

Assim que tiver uma máquina virtual do Azure configurada convenientemente, selecione o **computação direto** opção na lista pendente, que pede-lhe uma porta (normalmente 8000, a porta predefinida para o qual e endereço IP da VM, nome (para mostrar na lista) JupyterHub escuta) e as credenciais VM:

![Linha de comandos para recolher informações do servidor para a opção de computação direto](media/project-compute-tier-direct.png)

Se as seguintes condições forem verdadeiras, também mostra a lista pendente [Máquina Virtual de ciência de dados (DSVM)](/azure/machine-learning/data-science-virtual-machine) instâncias. (Se alguma dessas condições não forem cumpridas, pode ligar a DSVM usando a opção de computação direto e introduzir os valores obtidos a partir do portal do Azure.)

- Tem sessão iniciada em blocos de notas do Azure com uma conta que utiliza o Azure Active Directory (AAD), como uma conta da empresa.
- Sua conta está ligada a uma subscrição do Azure.
- Tiver uma ou mais máquinas virtuais nessa subscrição, com, pelo menos, acesso de leitor, o que utilizar a máquina de Virtual de ciência de dados para a imagem do Linux (Ubuntu).)

![Instâncias de Máquina Virtual de ciência de dados na lista pendente no dashboard do projeto](media/project-compute-tier-dsvm.png)

Quando seleciona uma instância DSVM, blocos de notas do Azure pode solicitar-lhe as credenciais de máquina específica que utilizou quando criou a VM.

Para criar uma nova instância DSVM, siga as instruções [criar uma VM de ciência de dados do Ubuntu](/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro). Utilize o **Máquina Virtual de ciência de dados para Linux (Ubuntu)** de imagem se pretender que a DSVM a apareça na lista pendente em blocos de notas do Azure.  Se por outros motivos, tem de utilizar a imagem do Windows ou CentOS, pode utilizar o **computação direto** opção para ligar ao DSVM manualmente.

> [!IMPORTANT]
> Ao utilizar máquinas de virtuais de ciência de dados ou de computação direto, os blocos de notas que executar nos mesmos tem de ser totalmente autônomos. No momento, blocos de notas do Azure copia apenas os *.ipynb* ficheiro para a VM, mas não copia todos os outros ficheiros no projeto. Como resultado, os blocos de notas em execução em outras VMs falharem localizar outros arquivos do projeto.
>
> Pode contornar este comportamento de duas formas:
>
> 1. Copie manualmente os arquivos de projeto para a VM.
>
> 2. Os ficheiros dentro de um bloco de notas de configuração de incorporação que execute primeiro antes do bloco de notas primário. O bloco de notas de configuração, crie uma célula de código para cada ficheiro em que a célula que contém o conteúdo do ficheiro. Em seguida, na parte superior de cada célula, insira o comando `%%writefile <filename>`, onde `<filename>` é o nome do ficheiro para receber o conteúdo. Ao executar o bloco de notas, ele cria todos esses arquivos na VM. Por exemplo, veja a [setup.ipynb ficheiro na demonstração Microsoft animal de estimação detetor](https://github.com/Microsoft/connect-petdetector/blob/master/setup.ipynb) (GitHub).
>
>     ![Usando um % % writefile comando no início de uma célula de código](media/setup-notebook-writefile-command.png)

## <a name="edit-project-metadata"></a>Editar metadados do projeto

No dashboard do projeto, selecione **definições do projeto**, em seguida, selecione a **informações** tab, contendo os metadados do projeto, conforme descrito na tabela seguinte. Pode alterar os metadados de projeto em qualquer altura.

| Definição | Descrição |
| --- | --- |
| Nome do projeto | Um nome amigável para o seu projeto que blocos de notas do Azure utiliza para fins de exibição. Por exemplo, "Hello World no Python". |
| ID do Projeto | Um identificador personalizado que se tornará parte do URL que utilizar para partilhar um projeto. Este ID pode utilizar apenas letras, números e hífenes, está limitado a 30 carateres e não pode ser um [reservado ID do projeto](create-clone-jupyter-notebooks.md#reserved-project-ids). Se tiver dúvidas sobre o que usar, uma convenção comum é utilizar uma versão em minúsculas do nome do seu projeto em que os espaços são transformados em hífenes, por exemplo, "meu-bloco de notas-project" (truncado se necessário, para ajustar o limite de comprimento). |
| Projeto público | Se definido, permite que qualquer pessoa com a ligação para acessar o projeto. Ao criar um projeto privado, desmarque esta opção. |
| Ocultar clones | Se definido, outros utilizadores não podem ver uma lista de clones que foram feitos para este projeto. Ocultar clones é útil para os projetos que são partilhados com muitas pessoas que não fazem parte da mesma organização, tal como quando utiliza um bloco de notas para ensinar uma classe. |

> [!Important]
>
> Alterar o ID de projeto invalida todas as ligações para o projeto que talvez tenha partilhado anteriormente.

## <a name="manage-project-files"></a>Gerir ficheiros de projeto

Dashboard do projeto mostra o conteúdo do sistema de pasta do projeto. Pode utilizar vários comandos para gerenciar esses arquivos.

### <a name="create-new-files-and-folders"></a>Criar novos ficheiros e pastas

O **+ novo** comando (atalho de teclado: n) cria novos ficheiros ou pastas. Ao utilizar o comando, primeiro selecione o tipo de item para criar:

| Tipo de item | Descrição | Comportamento do comando |
| --- | --- | --- |
| **Bloco de notas** | Um bloco de notas do Jupyter | Apresenta um pop-up no qual especifique os nome de ficheiro e o idioma do bloco de notas. |
| **Pasta** | Uma subpasta | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome da pasta. |
| **Ficheiro em branco** | Um ficheiro no qual pode armazenar qualquer conteúdo, como texto, dados, etc. | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome de ficheiro. |
| **Markdown** | Um ficheiro de Markdown. | Cria um campo de edição na lista de ficheiros do projeto no qual inserir o nome de ficheiro. |

### <a name="upload-files"></a>Carregar ficheiros

O **carregar** comando fornece duas opções para importar dados a partir de outras localizações: **Partir do URL** e **do computador**. Para obter mais informações, consulte [trabalhar com arquivos de dados em projetos de bloco de notas do Azure](work-with-project-data-files.md).

### <a name="select-file-specific-commands"></a>Selecione a comandos específicos de ficheiro

Cada item na lista de ficheiros do projeto fornece comandos por meio de um menu de contexto de contexto:

![Comandos num menu de contexto do ficheiro](media/project-file-commands.png)

| Comando | Atalho de teclado | Ação |
| --- | --- | --- |
| Executar | r (ou clique) | Executa um ficheiro do bloco de notas. Outros tipos de arquivos estão abertos para visualização.  |
| Copiar ligação | Y | Copia um link para o ficheiro para a área de transferência. |
| Execute no laboratório do Jupyter | J | Executa um bloco de notas no JupyterLab, que é que o Jupyter normalmente fornece uma interface mais orientadas a desenvolvedores. |
| Pré-visualização | p | Abre-se de uma visualização HTML do arquivo; para blocos de notas, a pré-visualização é um processamento só de leitura do bloco de notas. Para obter mais informações, consulte a [pré-visualização](#preview) secção. |
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

Os blocos de notas do Azure de iniciantes cria um servidor para o projeto, ele procura por um arquivo no projeto chamado *aznbsetup.sh*. Se este ficheiro está presente, blocos de notas do Azure executa-o. A saída do script é armazenada na pasta do projeto, como *. aznbsetup.log*.

### <a name="environment-setup-steps"></a>Passos de configuração do ambiente

Pode utilizar definições de ambiente do projeto para criar passos individuais que configurar o ambiente.

No dashboard do projeto, selecione **definições do projeto**, em seguida, selecione a **ambiente** separador em que adicionar, remover e modificar os passos de configuração para o projeto:

![Pop-up de definições de projeto com o separador de ambiente selecionado](media/project-settings-environment-steps.png)

Para adicionar um passo, selecione **+ adicionar**, em seguida, selecione um tipo de passo no **operação** na lista pendente:

![Seletor de operação para um novo passo de configuração do ambiente](media/project-settings-environment-details.png)

As informações que, em seguida, do projeto dependem do tipo de operação que escolheu:

- **Requirements. txt**: Na segunda lista pendente, selecione um *Requirements. txt* ficheiros que já se encontra no projeto. Em seguida, selecione uma versão de Python a terceiro na lista pendente que é apresentada. Ao utilizar um *Requirements. txt* execuções de blocos de notas do Azure, de ficheiros `pip install -r` com o *Requirements. txt* quando a partir de um servidor de ficheiros. Não precisa de instalar explicitamente os pacotes a partir do bloco de notas.

- **Script de shell**: Na segunda lista pendente, selecione um script de shell de bash no projeto (normalmente, um ficheiro com o *. SH* extensão) que contém todos os comandos que pretende executar para inicializar o ambiente.

- **Environment.yml**: Na segunda lista pendente, selecione um *environments.yml* ficheiro para projetos Python usando um ambiente de conda.

Quando tiver terminado de adicionar passos, selecione **guardar**.

### <a name="use-the-terminal"></a>Utilizar o terminal

No dashboard do projeto, o **Terminal** comando abre um terminal do Linux que lhe dá acesso direto ao servidor. No terminal pode transferir dados, editar ou gerir ficheiros, Inspecione os processos e até mesmo usar ferramentas como o vi e nano.

> [!Note]
> Se tiver scripts de inicialização no ambiente do seu projeto, abrir o terminal pode exibir uma mensagem a indicar que a instalação ainda está em curso.

Pode emitir quaisquer comandos de Linux padrão no terminal. Também pode utilizar `ls` na pasta raiz para ver os diferentes ambientes que existe na máquina virtual, tais como *anaconda2_501*, *anaconda3_420*, *anaconda3_501*, *IfSharp*, e *R*, juntamente com um *projeto* pasta que contém o projeto:

![Terminal de projeto em blocos de notas do Azure](media/project-terminal.png)

Para afetar um ambiente específico, altere os diretórios dentro dessa pasta de ambiente em primeiro lugar.

Para os ambientes do Python, pode encontrar `pip` e `conda` na *bin* pasta de cada ambiente. Também pode utilizar aliases incorporadas para os ambientes:

```bash
# Anaconda 2 5.3.0/Python 2.7: python27
python27 -m pip install <package>

# Anaconda 3 4.2.0/Python 3.5: python35
python35 -m pip install <package>

# Anaconda 3 5.3.0/Python 3.6: python36
python36 -m pip install <package>
```

As alterações efetuadas no servidor aplicam-se apenas à sessão atual, exceto para os ficheiros e pastas que criar no *projeto* pasta em si. Por exemplo, editar um arquivo na pasta de projeto é mantido entre sessões, mas de pacotes com `pip install` não são.

> [!Note]
> Se usar `python` ou `python3`, invoca as versões de instalação de sistema de mensagens em fila do Python, que não é usadas para blocos de notas. Não tem permissões para operações como `pip install` pode ser, por isso, não se esqueça de utilizar os aliases de versão específico.

## <a name="access-notebook-logs"></a>Aceder aos registos de bloco de notas

Caso se depare com problemas ao executar um bloco de notas, a saída do Jupyter é armazenada numa pasta chamada *. nb.log*. Pode aceder a estes registos através da **Terminal** comando ou dashboard do projeto.

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

- [How to: Trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
- [Aceder a dados na cloud num bloco de notas](access-data-resources-jupyter-notebooks.md)
