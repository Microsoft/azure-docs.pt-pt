---
title: Utilizar o SSH Tunneling to access do Azure HDInsight túnel
description: Saiba como utilizar um túnel SSH para procurar em segurança a recursos da web hospedada em nós do HDInsight baseado em Linux.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/28/2019
ms.author: hrasheed
ms.openlocfilehash: 943bf0f4bba014c31a11bb30bf8d3b6a7c11a343
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299358"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-web-uis"></a>Utilizar o túnel SSH para aceder a Apache Ambari web UI, JobHistory, NameNode, Apache Oozie e outras interfaces do usuário da web

Clusters do HDInsight fornecem acesso à web Apache Ambari da interface do Usuário na Internet, mas algumas funcionalidades requerem um túnel SSH. Por exemplo, a IU da web para o serviço de Apache Oozie não é possível aceder através da internet sem um túnel SSh.

## <a name="why-use-an-ssh-tunnel"></a>Por que usar um túnel SSH

Vários dos menus na Ambari só funcionam por meio de um túnel SSH. Esses menus contam com web sites e serviços em execução em outros tipos de nó, como nós de trabalho.

As seguintes interfaces de usuário de Web exigem um túnel SSH:

* JobHistory
* NameNode
* Pilhas de thread
* IU da web do Oozie
* Interface de Usuário principal do HBase e registos

Se utilizar as ações de Script para personalizar o seu cluster, quaisquer serviços ou utilitários que instala e expõem um serviço web exigem um túnel SSH. Por exemplo, se instalar a Hue com uma ação de Script, tem de utilizar um túnel SSH para acessar a web da Hue da interface do Usuário.

> [!IMPORTANT]  
> Se tiver acesso direto ao HDInsight através de uma rede virtual, não é necessário utilizar túneis SSH. Para obter um exemplo de acessar diretamente o HDInsight através de uma rede virtual, consulte a [ligar o HDInsight à sua rede no local](connect-on-premises-network.md) documento.

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

[Secure Shell (SSH) de túnel](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) liga-se uma porta no seu computador local para um nó principal no HDInsight. O tráfego enviado para a porta local é encaminhado através de uma ligação SSH ao nó principal. O pedido foi resolvido como se ele teve origem no nó principal. A resposta, em seguida, é encaminhada através do túnel para a estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, consulte [ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um navegador da web que pode ser configurado para utilizar um proxy de SOCKS5.

    > [!WARNING]  
    > O suporte de proxy de SOCKS incorporado nas configurações de Internet do Windows não suporta SOCKS5 e não funciona com os passos neste documento. Os seguintes browsers contam com as definições de proxy do Windows e não trabalhar com os passos neste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > Google Chrome também conta com as definições de proxy do Windows. No entanto, pode instalar as extensões que suportam SOCKS5. Recomendamos [FoxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="usessh"></a>Criar um túnel com o comando SSH

Utilize o seguinte comando para criar um SSH de túnel utilizando o `ssh` comando. Substitua `sshuser` com um utilizador SSH do seu cluster do HDInsight e substitua `clustername` com o nome do cluster do HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@clustername-ssh.azurehdinsight.net
```

Este comando cria uma ligação que encaminha o tráfego para a porta local 9876 ao cluster através de SSH. As opções são:

* **D 9876** -a porta local que encaminha o tráfego através do túnel.
* **C** -comprimir todos os dados, porque o tráfego da web é principalmente de texto.
* **2** -force SSH para tentar apenas a versão 2 do protocolo.
* **as perguntas e** -modo silencioso.
* **T** -desativar a alocação pseudo tty, uma vez que está a reencaminhar apenas uma porta.
* **n** -impedir a leitura de STDIN, uma vez que está a reencaminhar apenas uma porta.
* **N** -não executar um comando remoto, uma vez que está a reencaminhar apenas uma porta.
* **f** -execute em segundo plano.

Assim que o comando terminar, o tráfego enviado para a porta 9876 no computador local é encaminhado para o nó principal do cluster.

## <a name="useputty"></a>Criar um túnel a utilizar o PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) é um cliente SSH gráfico para Windows. Se não estiver familiarizado com PuTTY, veja a [documentação do PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html). Utilize os seguintes passos para criar um túnel SSH PuTTY a utilizar:

### <a name="create-or-load-a-session"></a>Criar ou carregar uma sessão

1. Abra o PuTTY e certifique-se **sessão** está selecionada no menu da esquerda. Se já guardar uma sessão, selecione o nome da sessão do **sessões guardadas** lista e selecione **carga**.

1. Se ainda não tiver uma sessão guardada, introduza as suas informações de ligação:
    * **Nome do anfitrião (ou endereço IP)** -o SSH endereço para o cluster do HDInsight. Por exemplo, **mycluster-SSH.azurehdinsight.NET**
    * **Porta** - 22
    * **Tipo de ligação** - SSH

1. Selecione **guardar**

    ![criar a sessão SSH](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Na **categoria** secção à esquerda da caixa de diálogo, expanda **ligação**, expanda **SSH**e, em seguida, selecione **túneis**.

1. Forneça as seguintes informações sobre o **encaminhamento de porta de opções de controlo SSH** formulário:
   
   * **Porta de origem** - A porta no cliente que pretende reencaminhar. Por exemplo, **9876**.

   * **Destino** -o SSH endereço para o cluster do HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.

   * **Dinâmico** - Ativa encaminhamento proxy de SOCKS dinâmico.
     
     ![Imagem das opções de túnel](./media/hdinsight-linux-ambari-ssh-tunnel/puttytunnel.png)

1. Selecione **Add** para adicionar as definições e, em seguida, clique em **abrir** para abrir uma ligação SSH.

1. Quando lhe for pedido, inicie sessão servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Utilizar o túnel do seu navegador

> [!IMPORTANT]  
> Os passos nesta secção utilizam o navegador Mozilla FireFox, pois fornece as mesmas definições de proxy para todas as plataformas. Outros navegadores modernos, como o Google Chrome, podem exigir uma extensão, como FoxyProxy para trabalhar com o túnel.

1. Configurar o browser utilize **localhost** e a porta que utilizou quando criar o túnel como um **SOCKS v5** proxy. Eis a aparência as definições do Firefox. Se utilizou uma porta diferente da que 9876, altere a porta para utilizado:
   
    ![Imagem das definições do Firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefoxproxy.png)
   
   > [!NOTE]  
   > Selecionando **DNS remoto** resolve os pedidos de sistema de nomes de domínio (DNS) ao utilizar o cluster do HDInsight. Esta definição resolve DNS com o nó principal do cluster.

2. Certifique-se de que o túnel funciona ao visitar um site como [ https://www.whatismyip.com/ ](https://www.whatismyip.com/). O IP devolvido deve ser usada pelo datacenter do Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Certifique-se com a IU web do Ambari

Depois do cluster tiver sido estabelecido, utilize os seguintes passos para verificar se tem acesso a web do serviço de interfaces do usuário da Ambari Web:

1. No seu browser, aceda a `http://headnodehost:8080`. O `headnodehost` endereço é enviado através do túnel para o cluster e a resolver para o nó principal Ambari em execução no. Quando lhe for pedido, introduza o nome de utilizador de admin (administrador) e a palavra-passe para o seu cluster. Poderá ser solicitado uma segunda vez, a IU web do Ambari. Se assim for, a introduzir as informações.

   > [!NOTE]  
   > Ao utilizar o `http://headnodehost:8080` endereço para ligar ao cluster, está a ligar através do túnel. A comunicação é protegida através do túnel SSH em vez de HTTPS. Para ligar através da internet através de HTTPS, utilize `https://clustername.azurehdinsight.net`, onde `clustername` é o nome do cluster.

2. Na IU da Web do Ambari, selecione o HDFS da lista à esquerda da página.

    ![Imagem com HDFS selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfsservice.png)

3. Quando as informações do serviço HDFS são apresentadas, selecione **ligações rápidas**. É apresentada uma lista de nós principais do cluster. Selecione um de nós principais e, em seguida, selecione **IU NameNode**.

    ![Imagem com o menu de QuickLinks expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenodedropdown.png)

    > [!NOTE]  
    > Quando seleciona __ligações rápidas__, poderá receber um indicador de espera. Esta condição pode ocorrer se tiver uma conexão lenta com a internet. Aguarde um minuto ou dois para que os dados a serem recebidos do servidor e tente novamente a lista.
    >
    > Algumas entradas no **ligações rápidas** menu pode ser cortado pelo lado direito da tela. Se assim for, expanda o menu utilizando o rato e utilize a tecla de seta para a direita para deslocar o ecrã para a direita para ver o resto do menu.

4. É apresentada uma página semelhante à seguinte imagem:

    ![Imagem da interface do Usuário de NameNode](./media/hdinsight-linux-ambari-ssh-tunnel/namenode.png)

    > [!NOTE]  
    > Observe o URL para esta página; deve ser semelhante a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`. Este URI está a utilizar o nome interno de domínio completamente qualificado (FQDN) do nó e só é acessível ao utilizar um túnel SSH.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como criar e utilizar um túnel SSH, consulte o documento seguinte para outras formas de utilizar Ambari:

* [Gerir clusters do HDInsight através do Apache Ambari](hdinsight-hadoop-manage-ambari.md)