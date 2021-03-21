---
title: Crie um laboratório para ensinar ciência de dados com os cadernos Python e Jupyter | Microsoft Docs
description: Aprenda a criar um laboratório para ensinar ciência de dados usando os cadernos Python e Jupyter.
author: emaher
ms.topic: article
ms.date: 09/29/2020
ms.author: enewman
ms.openlocfilehash: d4034f889334bcf1e4eaa3710a32db60b6a9936b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94648026"
---
# <a name="set-up-a-lab-to-teach-data-science-with-python-and-jupyter-notebooks"></a>Crie um laboratório para ensinar ciência de dados com pítons e cadernos jupyter
Este artigo descreve como configurar uma máquina virtual modelo (VM) nos Serviços de Laboratório com as ferramentas necessárias para ensinar os alunos a usar [os Cadernos Jupyter,](http://jupyter-notebook.readthedocs.io/)e como os alunos podem ligar-se aos seus cadernos nas suas máquinas virtuais (VMs).

Jupyter Notebooks é um projeto de código aberto que permite combinar facilmente texto rico e código fonte Python executável numa única tela chamada caderno. A execução de um caderno resulta num registo linear de entradas e saídas. Essas saídas podem incluir texto, tabelas de informação, parcelas de dispersão, e muito mais.

## <a name="set-up-the-lab"></a>Configurar o laboratório

### <a name="lab-configuration"></a>Configuração de laboratório
Para configurar este laboratório, precisa de acesso a uma assinatura do Azure e a uma conta de laboratório. Discuta com o administrador da sua organização para ver se consegue ter acesso a uma subscrição do Azure existente. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Assim que tiver uma assinatura Azure, crie uma nova conta de laboratório nos Serviços Azure Lab seguindo instruções no tutorial: [Configurar uma conta de laboratório](tutorial-setup-lab-account.md). Também pode usar uma conta de laboratório existente.

### <a name="lab-account-settings"></a>Definições de conta de laboratório
Ativar as definições descritas na tabela abaixo para a conta de laboratório. Para obter mais informações sobre como ativar imagens de marketplace, consulte [as imagens do Marketplace disponíveis para os criadores de laboratório.](specify-marketplace-images.md)

| Definição de conta de laboratório | Instruções |
| ------------------- | ------------ |
| Imagem do Marketplace | Dentro da sua conta de laboratório, ative uma das imagens do Azure Marketplace com base nas necessidades do seu sistema operativo: <br/><ul><li>Data Science Virtual Machine - Windows Server 2019</li><li>Data Science Virtual Machine – Ubuntu 18.04</li></ul> |

> [!NOTE]
> Este artigo utiliza as imagens de máquinas virtuais data science disponíveis no mercado Azure porque estão pré-configuradas com o Jupyter Notebook. Estas imagens, no entanto, também incluem muitas outras ferramentas de desenvolvimento e modelação para a ciência de dados. Se você não quer essas ferramentas extras e quer uma configuração leve apenas com cadernos Jupyter, crie uma imagem VM personalizada. Por exemplo, [instalar o JupyperHub no Azure](http://tljh.jupyter.org/en/latest/install/azure.html). Uma vez criada a imagem personalizada, pode enviá-la para uma galeria de imagens partilhada para utilizar a imagem dentro dos Serviços Azure Lab. Saiba mais sobre [a utilização da Galeria de Imagens Partilhadas nos Serviços Azure Lab](how-to-attach-detach-shared-image-gallery.md). 

### <a name="lab-settings"></a>Configurações de laboratório
Configure o **tamanho da máquina virtual** e as definições de imagem da máquina **virtual,** como mostrado na tabela seguinte ao configurar um laboratório de sala de aula. Para obter instruções sobre a criação de um laboratório de sala de aula, consulte [configurar um laboratório de sala de aula.](tutorial-setup-classroom-lab.md)

| Configurações de laboratório | Valor/instruções |
| ------------ | ------------------ | 
| Tamanho da máquina virtual | <p>O tamanho que escolhe aqui depende da carga de trabalho que quer executar:</p><ul><li>Small or Medium – bom para uma configuração básica de acesso a Cadernos Jupyter</li><li>Pequena GPU (Compute) – mais adequada para aplicações computacionalmente intensivas e intensivas em rede, como Inteligência Artificial e Deep Learning</li></ul> | 
| Imagem de máquina virtual | <p>Escolha uma das seguintes imagens com base nas necessidades do seu sistema operativo:</p><ul><li>[Data Science Virtual Machine - Windows Server 2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.dsvm-win-2019)</li><li>[Data Science Virtual Machine – Ubuntu 18.04](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804?tab=Overview)</li></ul> |


### <a name="template-virtual-machine"></a>Máquina virtual de modelo
Assim que criar um laboratório, será criado um modelo VM com base no tamanho e imagem da máquina virtual que escolheu. Você configura o modelo VM com tudo o que você deseja fornecer aos seus alunos para esta aula. Para saber mais, consulte [como gerir a máquina virtual do modelo.](how-to-create-manage-template.md) 

As imagens VM da Data Science por padrão vêm com muitos dos quadros e ferramentas de ciência de dados necessários para este tipo de classe. Por exemplo, as imagens incluem:

- [Jupyter Notebooks](http://jupyter-notebook.readthedocs.io/): Uma aplicação web que permite aos cientistas de dados pegar em dados brutos, executar computações e ver os resultados todos no mesmo ambiente. Será executado localmente no modelo VM.  
- [Visual Studio Code](https://code.visualstudio.com/): Um ambiente de desenvolvimento integrado (IDE) que proporciona uma experiência interativa rica ao escrever e testar um caderno. Para obter mais informações, consulte [Working with Jupyter Notebooks in Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support).

### <a name="provide-notebooks-for-the-class"></a>Fornecer cadernos para a classe
A próxima tarefa é fornecer aos alunos cadernos que você quer que eles usem. Para fornecer os seus próprios cadernos, pode guardar cadernos localmente no modelo VM. 

Se quiser utilizar cadernos de amostras da Azure Machine Learning, veja [como configurar um ambiente com cadernos Jupyter](../machine-learning/how-to-configure-environment.md#jupyter). 

### <a name="optional-enable-graphical-desktop-for-linux"></a>Opcional: ativar o ambiente de trabalho gráfico para o Linux 
A Máquina Virtual de Ciência de Dados – Imagem **Ubuntu** já está a for forada com o servidor X2GO e está pronta para aceitar ligações com o cliente. Não são necessários mais passos ao configurar o modelo VM. 

### <a name="publish-the-template-machine"></a>Publique a máquina de modelo
Quando publicar o modelo, cada aluno registado no seu laboratório receberá uma cópia do modelo VM com todas as ferramentas e cadernos locais que instalou nele.

## <a name="how-students-connect-to-jupyter-notebooks"></a>Como os alunos se ligam aos Cadernos Jupyter?
Assim que publicar o modelo, cada aluno terá acesso a um VM que vem com tudo o que pré-configurado para a aula, incluindo os Cadernos Jupyter. As seguintes secções mostram diferentes formas de os alunos se conectarem aos Cadernos Jupyter. 

### <a name="for-windows-vms"></a>Para VMs do Windows
Se forneceu aos alunos VMs windows, eles precisam de se conectar aos seus VMs e usar cadernos Jupyter que estão disponíveis localmente neles. 

Para se ligar a um VM do Windows, um aluno pode utilizar uma ligação de ambiente de trabalho remoto (RDP). Para passos detalhados, veja [como aceder a um laboratório de sala de aula.](how-to-use-classroom-lab.md) 

Um estudante que utilize um Mac ou Chromebook pode seguir instruções de seguir artigos para ligar ao Data Science Windows VM. 

- [Ligue-se a um VM usando RDP num Mac](connect-virtual-machine-mac-remote-desktop.md)
- [Ligue-se a um VM usando RDP num Chromebook](connect-virtual-machine-chromebook-remote-desktop.md)

### <a name="for-linux-vms"></a>Para VMs do Linux
Se forneceu aos alunos VMs Linux, existem várias opções que os alunos podem usar para se conectarem aos seus Cadernos Jupyter nos VMs:

- Aceda aos cadernos jupyter localmente após a ligação ao VM
    - SSH para o VM para sessões terminais
     - Ligação X2Go ao VM para sessões gráficas
- Utilize túneis SSH para ligar do computador local do aluno diretamente ao Servidor Jupyter no VM. 

As secções seguintes fornecem detalhes sobre estas formas de se conectar aos cadernos Jupyter. 

#### <a name="ssh-to-virtual-machine"></a>SSH para máquina virtual
Os alunos podem ligar-se via SSH aos seus VMs Linux a partir de uma sessão terminal. Para passos detalhados, veja [como aceder a um laboratório de sala de aula.](how-to-use-classroom-lab.md) Se estiverem a utilizar uma máquina cliente Windows, terão de ativar um cliente SSH, descarregando [o PuTTY](https://www.putty.org/) ou permitindo que o [OpenSSH no Windows](/windows-server/administration/openssh/openssh_install_firstuse) seja o SSH a partir da ída ponto de comando. 

1.  Inicie a VM.
2.  Uma vez que o VM esteja em execução, clique em **Connect**, que irá aparecer uma caixa de diálogo que fornece a cadeia de comando SSH, que será parecida com a seguinte amostra:
    
     ```shell
    ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3.  Vá ao seu comando ou terminal, e cole neste comando e, em seguida, prima **ENTER**.
4.  Introduza a palavra-passe para iniciar sinser para o VM. 

Uma vez que os alunos estão ligados a VMs, eles podem aceder e executar Cadernos Jupyter localmente.

#### <a name="x2go-to-virtual-machine"></a>X2Go para máquina virtual
A Máquina Virtual de Ciência de Dados – Imagem **Ubuntu** já está a for forada com o X2GO Server e está pronta para aceitar ligações com o cliente. Para se conectarem ao ambiente de trabalho gráfico da máquina Linux, os alunos precisam seguir estes passos únicos para configurar o X2Go nas suas máquinas clientes:

1.  Faça o download e instale o [cliente X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient) para a sua plataforma cliente.
2.  No [portal Azure Lab Services,](https://labs.azure.com)certifique-se de que o VM Linux a que pretende ligar é iniciado.
3.  Uma vez que o VM esteja em execução, clique em **Connect**, que irá abrir uma caixa de diálogo que fornece a cadeia de comando SSH, que será parecida com a seguinte amostra:

    ```
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```    
4. Assim que tiver esta informação, abra a aplicação do cliente X2Go e crie uma nova sessão. 
5.  Preencha os seguintes valores no painel de preferências de **sessão:**
    - **Nome da sessão**: Pode ser o que quiser, mas recomendamos usar o nome do seu VM de laboratório.
     - **Anfitrião:**`ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com`
     - Início de **sessão**: estudante
     - **Porta SSH**: 12345
     - **Tipo de sessão**: XFCE
6. Selecione **OK**. 

    > [!NOTE]
     > Ao criar uma nova sessão X2Go, certifique-se de que utiliza a porta SSH, **não** a porta RDP.

Agora, para ligar ao VM, siga estes passos:    

1.  No cliente X2Go, clique duas vezes no VM a que pretende ligar. 

    ![Cliente X2Go](./media/class-type-jupyter-notebook/x2go-client.png)
2. Introduza a palavra-passe para ligar ao VM. (Pode ter de dar permissão ao X2Go para contornar a sua firewall para terminar a ligação.)
3.  Deve agora ver a interface gráfica para o seu Ubuntu Data Science VM.


#### <a name="ssh-tunnel-to-jupyter-server-on-the-vm"></a>Túnel SSH para o servidor Jupyter no VM
Alguns estudantes podem querer ligar-se diretamente do computador local diretamente ao servidor Jupyter dentro dos seus VMs. O protocolo SSH permite o encaminhamento de portas entre o computador local e um servidor remoto (no nosso caso, o VM do laboratório do aluno), de modo que uma aplicação em execução numa determinada porta do servidor é **escavada** para a porta de mapeamento no computador local. Os estudantes devem seguir estes passos até ao túnel SSH até ao servidor Jupyter nos seus VMs de laboratório:

1.  No [portal Azure Lab Services,](https://labs.azure.com)certifique-se de que o VM Linux que pretende ligar é iniciado.
2.  Uma vez que o VM esteja em execução, clique em **Connect**, que irá abrir uma caixa de diálogo que fornece a cadeia de comando SSH, que será parecida com a seguinte cadeia:

    ```bash
     ssh -p 12345 student@ml-lab-00000000-0000-0000-0000-000000000000.eastus2.cloudapp.azure.com
     ```
3. No seu computador local, lance um terminal ou um pedido de comando e copie a cadeia de ligação SSH. Em seguida, adicione `-L 8888:localhost:8888` à cadeia de comando, que cria o **túnel** entre as portas. A corda final deve parecer:

    ```bash
     ssh –L 8888:localhost:8888 -p 12345 student@ml-lab-b720853e-570f-49ac-9cb2-bd0bd2aeec35.eastus.cloudapp.azure.com
     ```
4. Prima **ENTER** para executar o comando. 
5.  Quando solicitado, forneça a senha para ligar ao VM do laboratório. 
6.  Assim que estiver ligado ao VM, inicie o servidor Jupyter utilizando este comando: 

    ```bash
     jupyter notebook
     ```
7. Executar o comando fornecer-lhe-á um URL no terminal ou na solicitação de comando. A URL deve parecer:

    ```bash
     http://localhost:8888/?token=8c09ecfc93e6a8cbedf9c66dffdae19670a64acc1d37
     ```
8. Cole este URL num browser no seu computador local para ligar e trabalhar no seu Caderno Jupyter. 

    > [!NOTE]
    > O Código do Estúdio Visual também permite uma grande [experiência de edição de Caderno Jupyter.](https://code.visualstudio.com/docs/python/jupyter-support) Pode seguir as instruções sobre [como ligar-se a um servidor Jupyter remoto](https://code.visualstudio.com/docs/python/jupyter-support#_connect-to-a-remote-jupyter-server) e utilizar o mesmo URL do passo anterior para ligar a partir do Código VS em vez de do navegador. 


## <a name="cost-estimate"></a>Estimativa de custos
Vamos cobrir uma possível estimativa de custos para esta aula. Usaremos uma turma de 25 alunos. Há 20 horas de horário de aula. Além disso, cada aluno recebe uma quota de 10 horas para trabalhos de casa ou tarefas fora do horário de aulas programado. O tamanho VM que escolhemos foi a pequena GPU (computação), que é de 139 unidades de laboratório. Se quiser utilizar o tamanho pequeno (20 unidades de laboratório) ou de tamanho médio (42 unidades de laboratório), pode substituir a parte da unidade de laboratório na equação abaixo pelo número correto.  

Aqui está um exemplo de uma possível estimativa de custos para esta aula: 25 alunos * (20 horas programadas + 10 horas de quota) * 139 unidades de laboratório * 0,01 USD por hora = 1042,5 USD

Mais detalhes sobre os preços, consulte o [Azure Lab Services Pricing](https://azure.microsoft.com/pricing/details/lab-services/).

## <a name="conclusion"></a>Conclusão
Neste artigo, percorremos os degraus para criar um laboratório para uma aula de Cadernos Jupyter. Você pode usar uma configuração semelhante para outras aulas de machine learning.

## <a name="next-steps"></a>Passos seguintes

Os próximos passos são comuns para montar qualquer laboratório.

- [Criar e gerir um modelo](how-to-create-manage-template.md)
- [Adicionar utilizadores](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Quota definida](how-to-configure-student-usage.md#set-quotas-for-users)
- [Definir um horário](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Links de inscrição de e-mail para estudantes](how-to-configure-student-usage.md#send-invitations-to-users)