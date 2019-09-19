---
title: Usar o túnel SSH para acessar o Azure HDInsight
description: Saiba como usar um túnel SSH para navegar com segurança em recursos da Web hospedados em seus nós HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: d976826fe90946697a32c5b1edb9dd323b01cc1c
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71105459"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Usar o túnel SSH para acessar a IU da Web do Apache Ambari, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário

Os clusters HDInsight fornecem acesso à interface do usuário da Web do Apache Ambari pela Internet, mas alguns recursos exigem um túnel SSH. Por exemplo, a interface do usuário da Web para o serviço Apache Oozie não pode ser acessada pela Internet sem um túnel SSh.

## <a name="why-use-an-ssh-tunnel"></a>Por que usar um túnel SSH

Vários dos menus no Ambari só funcionam por meio de um túnel SSH. Esses menus dependem de sites e serviços em execução em outros tipos de nó, como nós de trabalho.

As seguintes interfaces do site da Web exigem um túnel SSH:

* JobHistory
* NameNode
* Pilhas de thread
* Interface do usuário da Web do amOozie
* HBase Master e logs interface do usuário

Se você usar ações de script para personalizar o cluster, quaisquer serviços ou utilitários que você instalar que exponham um serviço Web exigirão um túnel SSH. Por exemplo, se você instalar o matiz usando uma ação de script, deverá usar um túnel SSH para acessar a interface do usuário da Web do matiz.

> [!IMPORTANT]  
> Se você tiver acesso direto ao HDInsight por meio de uma rede virtual, não será necessário usar túneis SSH. Para obter um exemplo de acesso direto ao HDInsight por meio de uma rede virtual, consulte o documento [conectar o hdinsight ao seu local de rede](connect-on-premises-network.md) .

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

O [túnel Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) conecta uma porta no computador local a um nó de cabeçalho no HDInsight. O tráfego enviado para a porta local é roteado por meio de uma conexão SSH para o nó principal. A solicitação é resolvida como se tivesse sido originada no nó principal. Em seguida, a resposta é roteada de volta pelo túnel para sua estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, consulte [conectar-se ao HDInsight (Apache Hadoop) usando o ssh](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um navegador da Web que pode ser configurado para usar um proxy SOCKS5.

    > [!WARNING]  
    > O suporte de proxy Socks nas configurações da Internet do Windows não oferece suporte a SOCKS5 e não funciona com as etapas neste documento. Os navegadores a seguir dependem das configurações de proxy do Windows e, no momento, não funcionam com as etapas deste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > O Google Chrome também se baseia nas configurações de proxy do Windows. No entanto, você pode instalar extensões que dão suporte a SOCKS5. Recomendamos o [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Criar um túnel usando o comando SSH

Use o comando a seguir para criar um túnel SSH usando `ssh` o comando. Substitua `sshuser` por um usuário SSH para seu cluster hdinsight e substitua `clustername` pelo nome do seu cluster hdinsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Esse comando cria uma conexão que roteia o tráfego para a porta local 9876 para o cluster por SSH. As opções são:

* **D 9876** -a porta local que roteia o tráfego pelo túnel.
* **C** -compactar todos os dados, pois o tráfego da Web é principalmente texto.
* **2** -forçar SSH para testar a versão 2 do protocolo apenas.
* **q** -modo silencioso.
* **T** -desabilitar a alocação pseudo TTY, já que você está apenas encaminhando uma porta.
* **n** -impedir a leitura de stdin, já que você está apenas encaminhando uma porta.
* **N** -não execute um comando remoto, pois você está apenas encaminhando uma porta.
* **f** -executar em segundo plano.

Quando o comando for concluído, o tráfego enviado para a porta 9876 no computador local será roteado para o nó principal do cluster.

## <a name="useputty"></a>Criar um túnel usando a saída

A [saída é um](https://www.chiark.greenend.org.uk/~sgtatham/putty) cliente SSH gráfico para o Windows. Se você não estiver familiarizado com a reproduzida, consulte a [documentação](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html)de saída. Use as seguintes etapas para criar um túnel SSH usando a recriação:

### <a name="create-or-load-a-session"></a>Criar ou carregar uma sessão

1. Abra o acabamento e verifique se a **sessão** está selecionada no menu à esquerda. Se você já tiver salvo uma sessão, selecione o nome da sessão na lista **sessões salvas** e selecione **carregar**.

1. Se você ainda não tiver uma sessão salva, insira suas informações de conexão:
    * **Nome do host (ou endereço IP)** -o endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-SSH.azurehdinsight.net**
    * **Porta** -22
    * **Tipo de conexão** -SSH

1. Selecione **guardar**

    ![Criar sessão de saída do HDInsight](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Na seção **categoria** à esquerda da caixa de diálogo, expanda **conexão**, expanda **SSH**e selecione **túneis**.

1. Forneça as seguintes informações sobre as **opções que controlam o formulário de encaminhamento de porta SSH** :

   * **Porta de origem** - A porta no cliente que pretende reencaminhar. Por exemplo, **9876**.

   * **Destino** -o endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

   * **Dinâmico** - Ativa encaminhamento proxy de SOCKS dinâmico.

     ![Opções de túnel de configuração de saída](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Selecione **Adicionar** para adicionar as configurações e, em seguida, clique em **abrir** para abrir uma conexão SSH.

1. Quando solicitado, entre no servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Usar o túnel do seu navegador

> [!IMPORTANT]  
> As etapas nesta seção usam o navegador Mozilla FireFox, pois ele fornece as mesmas configurações de proxy em todas as plataformas. Outros navegadores modernos, como o Google Chrome, podem exigir uma extensão como FoxyProxy para trabalhar com o túnel.

1. Configure o navegador para usar **localhost** e a porta usada ao criar o túnel como um proxy **SOCKS v5** . Veja como as configurações do Firefox se parecem. Se você usou uma porta diferente de 9876, altere a porta para aquela que você usou:

    ![configurações de proxy do navegador Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > A seleção de **DNS remoto** resolve solicitações de DNS (sistema de nomes de domínio) usando o cluster HDInsight. Essa configuração resolve o DNS usando o nó principal do cluster.

2. Verifique se o túnel funciona visitando um site como [https://www.whatismyip.com/](https://www.whatismyip.com/). O IP retornado deve ser um usado pelo datacenter Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verificar com a interface do usuário da Web do amAmbari

Depois que o cluster tiver sido estabelecido, use as seguintes etapas para verificar se você pode acessar as interfaces do serviço da Web do Ambari:

1. No navegador, vá para `http://headnodehost:8080`. O `headnodehost` endereço é enviado pelo túnel para o cluster e resolve para o nó principal em que Ambari está sendo executado. Quando solicitado, insira o nome de usuário do administrador (admin) e a senha para o cluster. Você pode ser solicitado uma segunda vez pela interface do usuário da Web do amAmbari. Em caso afirmativo, insira novamente as informações.

   > [!NOTE]  
   > Ao usar o `http://headnodehost:8080` endereço para se conectar ao cluster, você está se conectando por meio do túnel. A comunicação é protegida usando o túnel SSH em vez de HTTPS. Para se conectar pela Internet usando HTTPS, use `https://clustername.azurehdinsight.net`, em `clustername` que é o nome do cluster.

2. Na interface do usuário da Web do amAmbari, selecione HDFS na lista à esquerda da página.

    ![Serviço de HDFS do Apache Ambari selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Quando as informações do serviço HDFS forem exibidas, selecione **links rápidos**. É exibida uma lista dos nós de cabeçalho do cluster. Selecione um dos nós de cabeçalho e, em seguida, selecione **interface do usuário do amNameNode**.

    ![Imagem com o menu QuickLinks expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Ao selecionar __links rápidos__, você pode obter um indicador de espera. Essa condição pode ocorrer se você tiver uma conexão de Internet lenta. Aguarde um minuto ou dois para que os dados sejam recebidos do servidor e tente a lista novamente.
    >
    > Algumas entradas no menu **links rápidos** podem ser cortadas pelo lado direito da tela. Nesse caso, expanda o menu usando o mouse e use a tecla de seta para a direita para rolar a tela para a direita para ver o restante do menu.

4. Uma página semelhante à imagem a seguir é exibida:

    ![Imagem da interface do usuário do NameNode do Hadoop](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Observe a URL desta página; Ele deve ser semelhante a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Esse URI está usando o FQDN (nome de domínio totalmente qualificado) interno do nó e só pode ser acessado ao usar um túnel SSH.

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu como criar e usar um túnel SSH, consulte o documento a seguir para outras maneiras de usar o Ambari:

* [Gerenciar clusters HDInsight usando o Apache Ambari](hdinsight-hadoop-manage-ambari.md)
