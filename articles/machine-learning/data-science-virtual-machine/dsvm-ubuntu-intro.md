---
title: 'Quickstart: Criar uma máquina virtual Ubuntu Data Science'
titleSuffix: Azure Data Science Virtual Machine
description: Configure e crie uma máquina virtual de ciência de dados para linux (Ubuntu) para fazer analítica e aprendizagem automática.
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: quickstart
ms.date: 03/10/2020
ms.openlocfilehash: 97283a096d1b1549b2c7fa8f34a32b4bb4dca1eb
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349057"
---
# <a name="quickstart-set-up-the-data-science-virtual-machine-for-linux-ubuntu"></a>Quickstart: Configurar a Máquina Virtual de Ciência de Dados para Linux (Ubuntu)

Levante-se ecorda com a Máquina Virtual Ubuntu 18.04 Data Science.

## <a name="prerequisites"></a>Pré-requisitos

Para criar uma Máquina Virtual Ubuntu 18.04 Data Science, tem de ter uma subscrição do Azure. [Experimente Azure de graça.](https://azure.com/free)

>[!NOTE]
>As contas gratuitas do Azure não suportam SKUs de máquina virtual ativada por GPU.

## <a name="create-your-data-science-virtual-machine-for-linux"></a>Crie a sua máquina virtual de ciência de dados para o Linux

Aqui estão os passos para criar uma instância da Máquina Virtual de Ciência de Dados Ubuntu 18.04:

1. Aceda ao [portal do Azure](https://portal.azure.com). Pode ser solicitado a assinar na sua conta Azure se ainda não tiver assinado.
1. Encontre a listagem de máquinas virtuais digitando em "data science virtual machine" e selecionando "Data Science Virtual Machine- Ubuntu 18.04"

1. Na janela seguinte, **selecione Criar**.

1. Deve ser redirecionado para a lâmina "Criar uma máquina virtual".
   
1. Introduza as seguintes informações para configurar cada passo do assistente:

    1. **Básicos:**
    
       * **Subscrição** : Se tiver mais de uma subscrição, selecione aquela em que a máquina será criada e faturada. Tem de ter privilégios de criação de recursos para esta subscrição.
       * **Grupo de recursos** : Criar um novo grupo ou utilizar um existente.
       * **Nome da máquina virtual** : Introduza o nome da máquina virtual. Este nome será usado no seu portal Azure.
       * **Região** : Selecione o datacenter que é mais apropriado. Para um acesso mais rápido à rede, é o datacenter que tem a maioria dos seus dados ou está mais próximo da sua localização física. Saiba mais sobre [as Regiões Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
       * **Imagem** : Deixe o valor predefinido.
       * **Tamanho** : Esta opção deve autopovoar-se automaticamente com um tamanho adequado para cargas de trabalho gerais. Leia mais sobre [os tamanhos Linux VM em Azure](../../virtual-machines/sizes.md).
       * **Tipo de autenticação** : Para uma configuração mais rápida, selecione "Password". 
         
         > [!NOTE]
         > Se pretender utilizar o JupyterHub, certifique-se de selecionar "Password", uma vez que o JupyterHub *não* está configurado para utilizar as teclas públicas SSH.

       * **Nome de utilizador** : Introduza o nome de utilizador do administrador. Utilizará este nome de utilizador para iniciar sessão na sua máquina virtual. Este nome de utilizador não precisa de ser o mesmo que o seu nome de utilizador Azure. *Não* utilize letras maiúsculas.
         
         > [!IMPORTANT]
         > Se utilizar letras maiúsculas no seu nome de utilizador, o JupyterHub não funcionará e encontrará um erro de 500 servidor interno.

       * **Palavra-passe** : Introduza a palavra-passe que utilizará para iniciar sessão na sua máquina virtual.    
    
   1. Selecione **Rever + criar**.
   1. **Review+criar**
      * Verifique se toda a informação que introduziu está correta. 
      * Selecione **Criar**.
    
    O provisionamento deve demorar cerca de 5 minutos. O estado é apresentado no portal Azure.

## <a name="how-to-access-the-ubuntu-data-science-virtual-machine"></a>Como aceder à Máquina Virtual Ubuntu Data Science

Pode aceder ao Ubuntu DSVM de uma de três formas:

  * SSH para sessões de terminal
  * X2Go para sessões gráficas
  * JupyterHub e JupyterLab para blocos de notas do Jupyter

Também pode anexar uma Máquina Virtual de Ciência de Dados a Cadernos Azure para executar cadernos Jupyter no VM e contornar as limitações do nível de serviço gratuito. Para mais informações, consulte [Gerir e configurar projetos de Cadernos Azure.](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier)

### <a name="ssh"></a>SSH

Se configurar o seu VM com autenticação SSH, pode recorrer às credenciais de conta que criou na secção **Básico** do passo 3 para a interface da concha de texto. No Windows, pode descarregar uma ferramenta de cliente SSH como [a PuTTY](https://www.putty.org). Se preferir um ambiente de trabalho gráfico (X Window System), pode utilizar o reencaminhamento X11 no PuTTY.

> [!NOTE]
> O cliente X2Go apresentou-se melhor do que o reencaminhamento X11 nos testes. Recomendamos a utilização do cliente X2Go para uma interface gráfica de ambiente de trabalho.

### <a name="x2go"></a>X2Go

O Linux VM já está a provisionado com o X2Go Server e pronto para aceitar ligações com o cliente. Para se ligar ao ambiente de trabalho gráfico da VM do Linux, conclua o seguinte procedimento no seu cliente:

1. Transfira e instale o X2Go Client na sua plataforma de cliente a partir do [X2Go](https://wiki.x2go.org/doku.php/doc:installation:x2goclient).
1. Tome nota do endereço IP público da máquina virtual, que pode encontrar no portal Azure abrindo a máquina virtual que criou.

   ![Endereço IP da máquina Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

1. Execute o X2Go Client. Se a janela "New Session" não aparecer automaticamente, vá a Session -> New Session.

1. Na janela de configuração resultante, introduza os seguintes parâmetros de configuração:
   * **Separador Sessão** :
     * **Anfitrião** : Introduza o endereço IP da sua VM, que anotou anteriormente.
     * **Iniciar sessão** : introduza o nome de utilizador na VM do Linux.
     * **Porta SSH** : deixe-a em 22, o valor predefinido.
     * **Tipo de Sessão** : Altere o valor para **XFCE**. Atualmente, a VM do Linux apenas suporta o ambiente de trabalho XFCE.
   * **Separador Multimédia** : pode desativar o suporte de som e a impressão de cliente se não precisar de utilizá-los.
   * **Pastas partilhadas** : Utilize este separador para adicionar o diretório de máquinas do cliente que gostaria de montar no VM. 

   ![Configuração X2go](./media/dsvm-ubuntu-intro/x2go-ubuntu.png)
1. Selecione **OK**.
1. Clique na caixa no painel direito da janela X2Go para trazer o ecrã de login para o seu VM.
1. Introduza a palavra-passe da sua VM.
1. Selecione **OK**.
1. Talvez seja necessário conceder permissão ao X2Go para ignorar a firewall para concluir a ligação.
1. Deverá agora ver a interface gráfica do seu Ubuntu DSVM. 


### <a name="jupyterhub-and-jupyterlab"></a>JupyterHub e JupyterLab

O Ubuntu DSVM gere [o JupyterHub,](https://github.com/jupyterhub/jupyterhub)um servidor Jupyter multiusador. Para ligar, tome os seguintes passos:

   1. Tome nota do endereço IP público para o seu VM, procurando e selecionando o seu VM no portal Azure.
      ![Endereço IP da máquina Ubuntu](./media/dsvm-ubuntu-intro/ubuntu-ip-address.png)

   1. A partir da sua máquina local, abra um navegador web e navegue para https: \/ /your-vm-ip:8000, substituindo "your-vm-ip" pelo endereço IP que tomou nota anterior.
   1. O seu navegador provavelmente irá impedi-lo de abrir a página diretamente, dizendo-lhe que há um erro de certificado. O DSVM está a fornecer segurança através de um certificado auto-assinado. A maioria dos navegadores permitir-lhe-á clicar após este aviso. Muitos navegadores continuarão a fornecer algum tipo de aviso visual sobre o certificado durante a sua sessão web.

      >[!NOTE]
      > Se vir a `ERR_EMPTY_RESPONSE` mensagem de erro no seu navegador, certifique-se de que acede à máquina utilizando explicitamente o protocolo *HTTPS* e não utilizando *HTTP* ou apenas o endereço web. Se escrever o endereço web sem `https://` a linha de endereço, a maioria dos navegadores irá por defeito `http` , e verá este erro.

   1. Introduza o nome de utilizador e a palavra-passe que usou para criar o VM e faça o sessão. 

      ![Insira o login de Jupyter](./media/dsvm-ubuntu-intro/jupyter-login.png)

      >[!NOTE]
      > Se receber um Erro 500 nesta fase, é provável que tenha usado letras maiúsculas no seu nome de utilizador. Esta é uma interação conhecida entre Jupyter Hub e o PAMAuthenticator que usa. Se receber um erro de "Não pode chegar a esta página", é provável que as permissões do Grupo de Segurança da Rede precisem de ser ajustadas. No portal Azure, encontre o recurso do Grupo de Segurança da Rede dentro do seu Grupo de Recursos. Para aceder ao JupyterHub a partir da Internet pública, você deve ter porta 8000 aberta. (A imagem mostra que este VM está configurado para acesso just-in-time, o que é altamente recomendado. Consulte [as portas de gestão Secure com acesso just-in time](../../security-center/security-center-just-in-time.md).) ![Configuração do Grupo de Segurança da Rede](./media/dsvm-ubuntu-intro/nsg-permissions.png)

   1. Navegue nos muitos cadernos de amostras que estão disponíveis.

O JupyterLab, a próxima geração de cadernos Jupyter e JupyterHub, também está disponível. Para aceder ao mesmo, inscreva-se no JupyterHub e, em seguida, navegue no URL https: \/ /your-vm-ip:8000/user/your-username/lab, substituindo "o seu nome de utilizador" pelo nome de utilizador que escolheu ao configurar o VM. Mais uma vez, pode ser inicialmente impedido de aceder ao site devido a um erro de certificado.

Pode definir o JupyterLab como o servidor de portátil predefinido adicionando esta linha a `/etc/jupyterhub/jupyterhub_config.py` :

```python
c.Spawner.default_url = '/lab'
```

## <a name="next-steps"></a>Próximos passos

Eis como pode continuar a sua aprendizagem e exploração:

* A [ciência dos dados na Máquina Virtual de Ciência de Dados para o linux](linux-dsvm-walkthrough.md) walkthrough mostra-lhe como fazer várias tarefas comuns de ciência de dados com o Linux DSVM aqui previsto. 
* Explore as várias ferramentas de ciência de dados no DSVM experimentando as ferramentas descritas neste artigo. Também pode funcionar `dsvm-more-info` na concha dentro da máquina virtual para uma introdução básica e ponteiros para mais informações sobre as ferramentas instaladas no VM.  
* Saiba como construir sistematicamente soluções analíticas utilizando o [Processo de Ciência de Dados de Equipa.](../team-data-science-process/index.yml)
* Visite a [Galeria Azure AI](https://gallery.azure.ai/) para análise de máquinas e análise de dados que utilizam os serviços Azure AI.
* Consulte a [documentação](./reference-ubuntu-vm.md) de referência adequada para esta máquina virtual.