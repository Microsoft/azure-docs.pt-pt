---
title: Utilize túneis SSH para aceder ao Azure HDInsight
description: Aprenda a usar um túnel SSH para navegar de forma segura nos recursos web hospedados nos seus nódosH HDInsight baseados em Linux.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: 9bdf7360ce00637b0eed3de7a3349da8656a3ed0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81314162"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Utilize túneis SSH para aceder a Apache Ambari web UI, JobHistory, NameNode, Apache Oozie, e outros UIs

Os clusters HDInsight fornecem acesso ao Apache Ambari web UI através da Internet. Algumas características requerem um túnel SSH. Por exemplo, a Rede Apache Oozie UI não pode ser acedida através da internet sem um túnel SSH.

## <a name="why-use-an-ssh-tunnel"></a>Por que usar um túnel SSH

Vários dos menus em Ambari só funcionam através de um túnel SSH. Estes menus dependem de web sites e serviços que correm em outros tipos de nós, como nós de trabalhadores.

As seguintes UIs Web requerem um túnel SSH:

* História do Emprego
* Nó de Nome
* Pilhas de fios
* Oozie web UI
* HBase Master e Logs UI

Os serviços instalados com Script Actions que expõem um serviço web exigirão um túnel SSH. O tom instalado com a Ação de Script requer um túnel SSH para aceder à UI web.

> [!IMPORTANT]  
> Se tiver acesso direto ao HDInsight através de uma rede virtual, não precisa de utilizar túneis SSH. Para um exemplo de acesso direto ao HDInsight através de uma rede virtual, consulte o [Connect HDInsight para o documento de rede no local.](connect-on-premises-network.md)

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

[O túnel Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) liga uma porta da sua máquina local a um nó de cabeça no HDInsight. O tráfego enviado para o porto local é encaminhado através de uma ligação SSH ao nó da cabeça. O pedido é resolvido como se tivesse origem no nó da cabeça. A resposta é então direcionada de volta através do túnel para a sua estação de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para mais informações, consulte [Connect to HDInsight (Apache Hadoop) utilizando O SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um navegador web que pode ser configurado para usar um proxy SOCKS5.

    > [!WARNING]  
    > O suporte proxy SOCKS incorporado nas definições da Internet do Windows não suporta socks5, e não funciona com os passos deste documento. Os seguintes navegadores dependem das definições de procuração do Windows, e não funcionam atualmente com os passos deste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > O Google Chrome também conta com as definições de procuração do Windows. No entanto, pode instalar extensões que suportam SOCKS5. Recomendamos [foxyProxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Crie um túnel usando o comando SSH

Utilize o seguinte comando para criar um `ssh` túnel SSH utilizando o comando. Substitua-o `sshuser` por um utilizador SSH para `CLUSTERNAME` o seu cluster HDInsight e substitua-o pelo nome do seu cluster HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Este comando cria uma ligação que encaminha o tráfego para o porto local 9876 para o aglomerado sobre o SSH. As opções são:

    |Opção |Descrição |
    |---|---|
    |D 9876|O porto local que faz o tráfego através do túnel.|
    |C|Comprima todos os dados, porque o tráfego web é principalmente texto.|
    |2|Force o SSH a tentar apenas a versão protocolar 2.|
    |q|Modo silencioso.|
    |T|Desative a atribuição de pseudo-tty, já que está apenas a reencaminhar uma porta.|
    |n|Evite a leitura do STDIN, já que está apenas a encaminhar uma porta.|
    |N|Não execute um comando remoto, já que está apenas a encaminhar uma porta.|
    |f|Corra ao fundo.|

Uma vez que o comando termina, o tráfego enviado para o porto 9876 no computador local é encaminhado para o nó da cabeça do cluster.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Crie um túnel usando PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) é um cliente sSH gráfico para Windows. Se não está familiarizado com a PuTTY, consulte a documentação da [PuTTY.](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html) Utilize os seguintes passos para criar um túnel SSH utilizando putty:

### <a name="create-or-load-a-session"></a>Criar ou carregar uma sessão

1. Abra putty e certifique-se de que a **Sessão** é selecionada no menu esquerdo. Se já guardou uma sessão, selecione o nome da sessão na lista **de Sessões Guardadas** e selecione **Load**.

1. Se ainda não tiver uma sessão guardada, insira as informações de ligação:

    |Propriedade |Valor |
    |---|---|
    |Nome do anfitrião (ou endereço IP)|O endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.|
    |Porta|22|
    |Tipo de Ligação|SSH|

1. Selecione **Guardar**

    ![HDInsight criar sessão de putty](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png)

1. Na secção **categoria** à esquerda do diálogo, expanda **a Ligação,** expanda **o SSH**e, em seguida, selecione **Túneis**.

1. Fornecer as seguintes informações sobre o formulário de **encaminhamento da porta SSH:**

    |Propriedade |Valor |
    |---|---|
    |Porta de origem|A porta sobre o cliente que deseja encaminhar. Por exemplo, **9876**.|
    |Destino|O endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.|
    |Dinâmica|Permite o encaminhamento dinâmico de procuração SOCKS.|

    ![Opções de túnel de configuração PuTTY](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png)

1. Selecione **Adicionar** para adicionar as definições e, em seguida, selecione **Abrir** para abrir uma ligação SSH.

1. Quando solicitado, inscreva-se no servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Use o túnel do seu navegador

> [!IMPORTANT]  
> Os passos nesta secção utilizam o navegador Mozilla FireFox, uma vez que fornece as mesmas configurações de procuração em todas as plataformas. Outros navegadores modernos, como o Google Chrome, podem exigir uma extensão como a FoxyProxy para trabalhar com o túnel.

1. Configure o navegador para utilizar o **local host** e a porta que usou ao criar o túnel como um proxy **SOCKS v5.** Aqui está o aspeto das configurações do Firefox. Se usou uma porta diferente do 9876, mude a porta para a que usou:

    ![configurações de proxy do navegador firefox](./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png)

   > [!NOTE]  
   > A seleção **remota de DNS** resolve os pedidos do Sistema de Nome de Domínio (DNS) utilizando o cluster HDInsight. Esta definição resolve o DNS utilizando o nó da cabeça do cluster.

2. Verifique se o túnel funciona visitando um local como [https://www.whatismyip.com/](https://www.whatismyip.com/). O IP devolvido deve ser utilizado pelo centro de dados Microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verifique com ambari web UI

Uma vez estabelecido o cluster, utilize os seguintes passos para verificar se pode aceder a UIs web de serviço a partir da Ambari Web:

1. No browser, aceda a `http://headnodehost:8080`. O `headnodehost` endereço é enviado sobre o túnel para o aglomerado e decide para o nó de cabeça que Ambari está correndo. Quando solicitado, introduza o nome de utilizador administrativo (administrador) e a palavra-passe para o seu cluster. Pode ser solicitado uma segunda vez pela UI web ambari. Em caso afirmativo, reintroduza a informação.

   > [!NOTE]  
   > Ao utilizar `http://headnodehost:8080` o endereço para ligar ao cluster, está a ligar-se através do túnel. A comunicação é segura utilizando o túnel SSH em vez de HTTPS. Para ligar através da internet `https://clustername.azurehdinsight.net`usando `clustername` HTTPS, utilize, onde está o nome do cluster.

2. A partir da Ambari Web UI, selecione HDFS da lista à esquerda da página.

    ![Serviço Apache Ambari hdfs selecionado](./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png)

3. Quando as informações do serviço HDFS forem apresentadas, selecione **Quick Links**. Uma lista dos nós da cabeça do cluster aparece. Selecione um dos nós da cabeça e, em seguida, selecione **NameNode UI**.

    ![Imagem com o menu QuickLinks expandido](./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png)

    > [!NOTE]  
    > Quando selecionar __Ligações Rápidas,__ poderá obter um indicador de espera. Esta condição pode ocorrer se tiver uma ligação lenta à Internet. Aguarde um minuto ou dois para que os dados sejam recebidos do servidor e tente novamente a lista.
    >
    > Algumas entradas no menu **Quick Links** podem ser cortadas pelo lado direito do ecrã. Em caso afirmativo, expanda o menu utilizando o rato e utilize a tecla de seta direita para deslocar o ecrã para a direita para ver o resto do menu.

4. É apresentada uma página semelhante à seguinte imagem:

    ![Imagem do Hadoop NameNode UI](./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png)

    > [!NOTE]  
    > Repare no URL desta página; deve ser semelhante `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster`a . Este URI está a utilizar o nome de domínio interno totalmente qualificado (FQDN) do nó, e só é acessível quando se utiliza um túnel SSH.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar e usar um túnel SSH, veja o seguinte documento para outras formas de usar Ambari:

* [Gerir os clusters HDInsight utilizando apache Ambari](hdinsight-hadoop-manage-ambari.md)
