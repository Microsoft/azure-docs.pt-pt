---
title: 'Início rápido: criar um DSVM do Ubuntu'
description: Configure e crie um Máquina Virtual de Ciência de Dados para Linux (Ubuntu) para fazer análises e aprendizado de máquina.
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: quickstart
ms.date: 12/31/2019
ms.openlocfilehash: 263c12b344e8634c639167aa3e455032f0817e2f
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75612092"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Início rápido: configurar o Máquina Virtual de Ciência de Dados para Linux (Ubuntu)

Comece a executar o Máquina Virtual de Ciência de Dados Ubuntu 18, 4.

## <a name="prerequisites"></a>Pré-requisitos

Para criar um Máquina Virtual de Ciência de Dados Ubuntu 18, 4, você deve ter uma assinatura do Azure. [Experimente o Azure gratuitamente](https://azure.com/free).
Observe que as contas gratuitas do Azure não dão suporte a SKUs de máquina virtual habilitadas para GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Criar a sua máquina de Virtual de ciência de dados para Linux

Estas são as etapas para criar uma instância do Máquina Virtual de Ciência de Dados Ubuntu 18, 4:

1. Vá para a [portal do Azure](https://portal.azure.com) você pode ser solicitado a entrar em sua conta do Azure, se você ainda não tiver entrado.
1. Localize a listagem de máquinas virtuais digitando "máquina virtual de ciência de dados" e selecionando "Máquina Virtual de Ciência de Dados-Ubuntu 18, 4 Preview".

1. Na janela subsequente, selecione **criar**.

1. Você deve ser redirecionado para a folha "criar uma máquina virtual".
   
1. Insira as informações a seguir para configurar cada etapa do assistente:

    1. **Noções básicas**:
    
       * **Assinatura**: se você tiver mais de uma assinatura, selecione aquela em que a máquina será criada e cobrada. Tem de ter privilégios de criação de recursos para esta subscrição.
       * **Grupo de recursos**: Crie um novo grupo ou use um existente.
       * **Nome da máquina virtual**: Insira o nome da máquina virtual. É assim que ela aparecerá na sua portal do Azure.
       * **Região**: selecione o datacenter mais apropriado. Para acesso mais rápido à rede, é o datacenter que tem a maior parte dos seus dados ou está mais próximo de sua localização física. Saiba mais sobre [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
       * **Imagem**: Deixe o valor padrão.
       * **Tamanho**: isso deve ser preenchido automaticamente com um tamanho apropriado para cargas de trabalho gerais. Leia mais sobre os [tamanhos de VM do Linux no Azure](../../virtual-machines/linux/sizes.md).
       * **Tipo de autenticação**: para configuração mais rápida, selecione "senha". 
         
         > [!NOTE]
         > Se você pretende usar JupyterHub, certifique-se de selecionar "senha", já que JupyterHub *não* está configurado para usar chaves públicas SSH.

       * **Nome de usuário**: Insira o nome de usuário do administrador. Esse é o nome de usuário que você usará para fazer logon em sua máquina virtual e não precisa ser o mesmo que o seu nome de usuário do Azure. *Não* use letras em maiúsculas.
         
         > [!NOTE]
         > Se você usar letras em maiúsculas em seu nome de usuário, o JupyterHub não funcionará e você encontrará um erro de servidor interno 500.

       * **Senha**: Insira a senha que você usará para fazer logon em sua máquina virtual.    
    
   1. Selecione **Rever + criar**.
   1. **Examinar + criar**
      * Certifique-se de que todas as informações que introduziu estão corretas. 
      * Selecione **Criar**.
    
    O aprovisionamento, deve demorar cerca de 5 minutos. O status é exibido na portal do Azure.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Como acessar o Ubuntu Máquina Virtual de Ciência de Dados

Você pode acessar o Ubuntu DSVM de uma das três maneiras:

  * SSH para sessões de terminal
  * X2Go para sessões de gráficos
  * JupyterHub e JupyterLab para blocos de notas do Jupyter

Você também pode anexar um Máquina Virtual de Ciência de Dados ao Azure Notebooks para executar blocos de anotações do Jupyter na VM e ignorar as limitações da camada de serviço gratuita. Para obter mais informações, consulte [gerenciar e configurar projetos de Azure notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier).

### <a name="ssh"></a>SSH

Depois que a VM for criada, se ela tiver sido configurada com acesso SSH, você poderá entrar nela usando SSH. Utilize as credenciais da conta que criou no **Noções básicas** secção do passo 3 para a interface do shell de texto. No Windows, você pode baixar uma ferramenta de cliente SSH [como saída](https://www.putty.org). Se preferir uma área de trabalho gráfica (sistema de janelas X), você poderá usar o encaminhamento X11 na saída.

> [!NOTE]
> O cliente de X2Go executada melhor do que em testes de reencaminhamento de X11. Recomendamos que utilize o cliente de X2Go para uma interface gráfica de área de trabalho.

### <a name="x2go"></a>X2Go

A VM do Linux já está provisionada com o servidor X2Go e está pronta para aceitar conexões de cliente. Para ligar-se na área de trabalho de gráfico de VM do Linux, execute o seguinte procedimento no cliente:

1. Transferir e instalar o cliente de X2Go para a sua plataforma de cliente do [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Anote o endereço IP público da máquina virtual, que pode ser encontrado no portal do Azure abrindo a máquina virtual que você acabou de criar.

   ![Endereço IP do computador Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Execute o cliente X2Go. Se a janela "nova sessão" não aparecer automaticamente, vá para sessão-> nova sessão.

1. Na janela configuração resultante, insira os seguintes parâmetros de configuração:
   * **Separador de sessão**:
     * **Host**: Insira o endereço IP da sua VM, que você fez anotar anteriormente.
     * **Logon**: Insira o nome de usuário na VM do Linux.
     * **Porta SSH**: deixá-lo em 22, o valor predefinido.
     * **Tipo de sessão**: Altere o valor para **XFCE**. Atualmente, a VM Linux dá suporte apenas à área de trabalho XFCE.
   * **Guia de suporte de dados**: pode desativar o suporte para som e cliente impressão se não precisar usá-los.
   * **Pastas compartilhadas**: Se pretender que diretórios das suas máquinas de cliente montadas na VM do Linux, adicione os diretórios de máquina de cliente que pretende partilhar com a VM neste separador.

   ![Configuração do X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecione **OK**.
1. Clique na caixa no painel à direita da janela X2Go para abrir a tela de logon para sua VM.
1. Insira a senha para sua VM.
1. Selecione **OK**.
1. Talvez seja necessário conceder permissão ao X2Go para ignorar o firewall a fim de concluir a conexão.
1. Agora você deve ver a interface gráfica para o Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub e JupyterLab

O Ubuntu DSVM executa o [JupyterHub](https://github.com/jupyterhub/jupyterhub), um servidor Jupyter multiusuário. Para se conectar, execute as seguintes etapas:

   1. Anote o endereço IP público para sua VM, pesquisando e selecionando sua VM no portal do Azure.
      ![endereço IP do computador Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. No computador local, abra um navegador da Web e navegue até https:\//Your-VM-IP: 8000, substituindo "Your-VM-IP" pelo endereço IP que você fez antes.
   1. Insira o nome de usuário e a senha que você usou para criar a VM e entre. 

      ![Insira o logon do Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

   1. Procure os vários blocos de anotações de exemplo disponíveis.

JupyterLab, a próxima geração de blocos de notas do Jupyter e JupyterHub, também está disponível. Para acessá-lo, entre no JupyterHub e navegue até a URL https:\//Your-VM-IP: 8000/User/Your-username/Lab, substituindo "Your-username" pelo nome de usuário que você escolheu ao configurar a VM.

Você pode definir JupyterLab como o servidor de notebook padrão adicionando essa linha a `/etc/jupyterhub/jupyterhub_config.py`:

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Passos seguintes

Eis como pode continuar a sua aprendizagem e a exploração:

* A [ciência de dados no guia máquina virtual de ciência de dados para Linux](linux-dsvm-walkthrough.md) mostra como fazer várias tarefas comuns de ciência de dados com o DSVM do Linux provisionado aqui. 
* Explore as várias ferramentas de ciência de dados no DSVM experimentando as ferramentas descritas neste artigo. Você também pode executar `dsvm-more-info` no Shell dentro da máquina virtual para obter uma introdução básica e ponteiros para obter mais informações sobre as ferramentas instaladas na VM.  
* Aprenda a criar soluções de análise de ponto-a-ponto sistematicamente utilizando o [Team Data Science Process](https://aka.ms/tdsp).
* Visite o [Galeria de IA do Azure](https://gallery.azure.ai/) para machine learning e os dados analytics exemplos que utilizam os serviços de IA do Azure.
* Consulte a [documentação de referência](./reference-ubuntu-vm.md) apropriada para esta máquina virtual.
