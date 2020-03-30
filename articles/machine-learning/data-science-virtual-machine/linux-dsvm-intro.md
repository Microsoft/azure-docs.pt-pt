---
title: 'Quickstart: Criar uma máquina virtual de ciência de dados centosos'
titleSuffix: Azure Data Science Virtual Machine
description: Configure e crie uma Máquina Virtual de Ciência de Dados para linux (CentOS) para fazer análise e machine learning.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 09/13/2019
ms.openlocfilehash: 73541b31125ee6e99dc2351e26f6a564a1603487
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "77526030"
---
# <a name="quickstart-set-up-a-centos-linux-data-science-virtual-machine-in-azure"></a>Quickstart: Criar uma máquina virtual de ciência de dados CentOS (Linux) em Azure

Suba e a funcionar com uma máquina virtual de ciência de dados baseada em CentOS.

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma Máquina Virtual CentOS Data Science, deve ter uma **subscrição Azure.** [Criar uma subscrição gratuita.](https://azure.com/free)

## <a name="create-your-centos-data-science-virtual-machine"></a>Crie a sua máquina virtual centosa de ciência de dados

Aqui estão os passos para criar uma instância da Máquina Virtual CentOS Data Science:

1. Vá ao [portal Azure.](https://portal.azure.com) Pode ser solicitado que inscreva-se na sua conta Azure se ainda não tiver assinado. 
1. Digite "data science virtual machine" na barra de pesquisa e selecione o CentOS DSVM.

    ![Resultado da pesquisa centosa](./media/linux-dsvm-intro/search-centos.png)

1. Na janela seguinte, selecione **Criar**.

    [![](media/linux-dsvm-intro/create-centos.png "Button to create a CentOS machine")](media/linux-dsvm-intro/create-centos-expanded.png#lightbox)

1. Deve ser redirecionado para a lâmina "Criar uma máquina virtual".
   
   ![Separador básico correspondente à Máquina Virtual CentOS](./media/linux-dsvm-intro/review-create-centos.png)

1. Introduza as seguintes informações para configurar cada passo do assistente:

    1. **Bases:**
    
       * **Subscrição**: Se tiver mais de uma subscrição, selecione aquela em que a máquina será criada e faturada. Tem de ter privilégios de criação de recursos para esta subscrição.
       * **Grupo de recursos**: Criar um novo grupo ou utilizar um existente.
       * **Nome**da máquina virtual : Introduza o nome da máquina virtual. É assim que vai aparecer no seu portal Azure.
       * **Região**: Selecione o datacenter mais adequado. Para acesso mais rápido à rede, é o datacenter que tem a maioria dos seus dados ou está mais próximo da sua localização física. Saiba mais sobre [as Regiões Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
       * **Imagem**: Deixe o valor predefinido.
       * **Tamanho**: Isto deve povoar automaticamente com um tamanho adequado para cargas de trabalho gerais. Leia mais sobre [tamanhos de VM Linux em Azure](../../virtual-machines/linux/sizes.md).
       * **Tipo de autenticação**: Para uma configuração mais rápida, selecione "Password". 
         
         > [!NOTE]
         > Se pretender utilizar o JupyterHub, certifique-se de selecionar "Password", uma vez que o JupyterHub *não* está configurado para utilizar chaves públicas SSH.

       * **Nome de utilizador**: Introduza o nome de utilizador do administrador. Este é o nome de utilizador que utilizará para iniciar sessão na sua máquina virtual, e não precisa de ser o mesmo que o seu nome de utilizador Azure. *Não* utilize letras maiúsculas.
         
         > [!NOTE]
         > Se utilizar letras maiúsculas no seu nome de utilizador, o JupyterHub não funcionará e encontrará um erro de 500 servidores internos.

       * **Palavra-passe**: Introduza a palavra-passe que utilizará para iniciar sessão na sua máquina virtual.    
    
   1. Selecione **Rever + criar**.
   1. **Review+criar**
      * Verifique se todas as informações que inseriu estão corretas. 
      * Selecione **Criar**.
    
    O fornecimento deve demorar cerca de 5 minutos. O estado está exposto no portal Azure.

## <a name="how-to-access-the-centos-data-science-virtual-machine"></a>Como aceder à Máquina Virtual de Ciência de Dados CentOS

Pode aceder ao DSVM CentOS de uma de três maneiras:

  * SSH para sessões de terminal
  * X2Go para sessões gráficas
  * JupyterHub e JupyterLab para blocos de notas do Jupyter

Também pode anexar uma Máquina Virtual de Ciência de Dados aos Cadernos Azure para executar cadernos Jupyter no VM e contornar as limitações do nível de serviço gratuito. Para mais informações, consulte [Gerir e configurar projetos de Cadernos Azure.](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)

### <a name="ssh"></a>SSH

Após a criação do VM, se foi configurado com acesso SSH, pode iniciar sessão utilizando o SSH. Utilize as credenciais de conta que criou na secção **Basics** do passo 3 para a interface da concha de texto. No Windows, pode descarregar uma ferramenta de cliente SSH como [a PuTTY](https://www.putty.org). Se preferir um ambiente de trabalho gráfico (X Window System), pode utilizar o reencaminhado X11 para o PuTTY.

> [!NOTE]
> O cliente X2Go teve um desempenho melhor do que o X11 reencaminhamento em testes. Recomendamos a utilização do cliente X2Go para uma interface gráfica de ambiente de trabalho.

### <a name="x2go"></a>X2Go

O Linux VM já está aprovisionado com o X2Go Server e pronto para aceitar ligações ao cliente. Para ligar ao ambiente de trabalho gráfico Linux VM, complete o seguinte procedimento no seu cliente:

1. Descarregue e instale o cliente X2Go para a sua plataforma cliente a partir de [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Tome nota do endereço IP público da máquina virtual, que pode encontrar no portal Azure abrindo a máquina virtual que acabou de criar.

   ![Endereço IP da máquina CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

1. Gereo cliente X2Go. Se a janela "New Session" não aparecer automaticamente, vá à Sessão -> Nova Sessão.

1. Na janela de configuração resultante, introduza os seguintes parâmetros de configuração:
   * **Separador de sessão**:
     * **Anfitrião**: Introduza o endereço IP do seu VM, que tomou nota anteriormente.
     * **Início de sessão**: Introduza o nome de utilizador no VM Linux.
     * **Porta SSH**: Deixe-o a 22, o valor predefinido.
     * Tipo de **sessão**: Alterar o valor para **XFCE**. Atualmente, o Linux VM suporta apenas o ambiente de trabalho XFCE.
   * **Separador de mídia:** Pode desligar o suporte de som e a impressão do cliente se não precisar de usá-los.
   * **Pastas partilhadas**: Se quiser que os diretórios das suas máquinas de clientes sejam montados no VM Linux, adicione os diretórios de máquinas de clientes que pretende partilhar com o VM neste separador.

   ![Configuração X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecione **OK**.
1. Clique na caixa no painel direito da janela X2Go para elevar o ecrã de login para o seu VM.
1. Introduza a palavra-passe para o seu VM.
1. Selecione **OK**.
1. Poderá ter de dar permissão ao X2Go para contornar a firewall para terminar a ligação.
1. Deverá agora ver a interface gráfica para o seu DSVM CentOS. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub e JupyterLab

O CentOS DSVM gere o [JupyterHub,](https://github.com/jupyterhub/jupyterhub)um servidor Jupyter multisuser. Para se ligar, tome os seguintes passos:

   1. Tome nota do endereço IP público para o seu VM, procurando e selecionando o seu VM no portal Azure.

       ![Endereço IP da máquina CentOS](./media/linux-dsvm-intro/centos-ip-address.png)

   1. A partir da sua máquina local, abra\/um navegador web e navegue para https: /your-vm-ip:8000, substituindo "your-vm-ip" pelo endereço IP que tomou nota anteriormente.
   1. Introduza o nome de utilizador e a palavra-passe que usou para criar o VM e faça o seu insto. 

      ![Insira o login de Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Navegue nos muitos cadernos de amostras disponíveis.

O JupyterLab, a próxima geração de cadernos Jupyter e JupyterHub, também está disponível. Para aceder ao mesmo, inscreva-se no JupyterHub e, em seguida, navegue para o URL https:\//your-vm-ip:8000/user/your-username/lab, substituindo "your-username" pelo nome de utilizador que escolheu ao configurar o VM.

Pode definir o JupyterLab como o servidor `/etc/jupyterhub/jupyterhub_config.py`de portátil predefinido adicionando esta linha a:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Passos seguintes

Eis como pode continuar a sua aprendizagem e exploração:

* A ciência dos [dados walkthrough na Máquina Virtual](linux-dsvm-walkthrough.md) de Ciência de Dados para Linux mostra-lhe como fazer várias tarefas comuns de ciência de dados com o Linux DSVM aprovisionado aqui. 
* Explore as várias ferramentas de ciência de dados no DSVM experimentando as ferramentas descritas neste artigo. Também pode `dsvm-more-info` correr na concha na máquina virtual para uma introdução básica e para ponteiros para mais informações sobre as ferramentas instaladas no DSVM.  
* Aprenda a construir soluções analíticas de ponta a ponta de forma sistemática utilizando o Processo de Ciência de Dados da [Equipa](https://aka.ms/tdsp).
* Visite a [Galeria Azure AI](https://gallery.azure.ai/) para obter amostras de machine learning e análise de dados que utilizam os serviços azure AI.
* Consulte a [documentação](./reference-centos-vm.md) de referência adequada para esta máquina virtual.