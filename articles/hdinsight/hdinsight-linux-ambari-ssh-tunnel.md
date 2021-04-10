---
title: Use túneis SSH para aceder a Azure HDInsight
description: Aprenda a usar um túnel SSH para navegar de forma segura nos recursos web alojados nos seus nós HDInsight baseados em Linux.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 04/14/2020
ms.openlocfilehash: ef7e0450725b456a7fb2b1ab61c50d7edece52ce
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867564"
---
# <a name="use-ssh-tunneling-to-access-apache-ambari-web-ui-jobhistory-namenode-apache-oozie-and-other-uis"></a>Use túneis SSH para aceder a Apache Ambari web UI, JobHistory, NameNode, Apache Oozie, e outros UIs

Os clusters HDInsight fornecem acesso à UI web Apache Ambari através da Internet. Algumas características requerem um túnel SSH. Por exemplo, a web UI Apache Oozie não pode ser acedida através da internet sem um túnel SSH.

## <a name="why-use-an-ssh-tunnel"></a>Por que usar um túnel SSH

Vários dos menus em Ambari só funcionam através de um túnel SSH. Estes menus dependem de sites e serviços que executam outros tipos de nós, como nós de trabalhadores.

Os seguintes UIs web requerem um túnel SSH:

* História do Trabalho
* NomeNode
* Pilhas de fios
* Oozie web UI
* HBase Master e Logs UI

Os serviços instalados com Script Actions que expõem um serviço web exigirão um túnel SSH. A tonalidade instalada com a Script Action requer um túnel SSH para aceder à UI web.

> [!IMPORTANT]  
> Se tiver acesso direto ao HDInsight através de uma rede virtual, não precisa de utilizar túneis SSH. Para um exemplo de acesso direto ao HDInsight através de uma rede virtual, consulte o [Connect HDInsight para o documento de rede no local.](connect-on-premises-network.md)

## <a name="what-is-an-ssh-tunnel"></a>O que é um túnel SSH

[O túnel Secure Shell (SSH)](https://en.wikipedia.org/wiki/Tunneling_protocol#Secure_Shell_tunneling) liga uma porta da sua máquina local a um nó de cabeça em HDInsight. O tráfego enviado para o porto local é encaminhado através de uma ligação SSH ao nó de cabeça. O pedido é resolvido como se tivesse origem no nó de cabeça. A resposta é então encaminhada de volta através do túnel para o seu posto de trabalho.

## <a name="prerequisites"></a>Pré-requisitos

* Um cliente SSH. Para obter mais informações, veja [Ligar ao HDInsight (Apache Hadoop) através de SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

* Um navegador web que pode ser configurado para usar um proxy SOCKS5.

    > [!WARNING]  
    > O suporte de procuração SOCKS incorporado nas definições da Internet do Windows não suporta sOCKS5 e não funciona com os passos deste documento. Os seguintes navegadores dependem das definições de procuração do Windows e não funcionam atualmente com os passos deste documento:
    >
    > * Microsoft Edge
    > * Microsoft Internet Explorer
    >
    > O Google Chrome também se baseia nas definições de procuração do Windows. No entanto, pode instalar extensões que suportem SOCKS5. Recomendamos [foxyproxy Standard](https://chrome.google.com/webstore/detail/foxyproxy-standard/gcknhkkoolaabfmlnjonogaaifnjlfnp).

## <a name="create-a-tunnel-using-the-ssh-command"></a><a name="usessh"></a>Criar um túnel usando o comando SSH

Utilize o seguinte comando para criar um túnel SSH utilizando o `ssh` comando. Substitua `sshuser` por um utilizador SSH para o seu cluster HDInsight e `CLUSTERNAME` substitua-o pelo nome do seu cluster HDInsight:

```cmd
ssh -C2qTnNf -D 9876 sshuser@CLUSTERNAME-ssh.azurehdinsight.net
```

Este comando cria uma ligação que liga o tráfego ao porto local 9876 para o aglomerado sobre a SSH. As opções são:

|Opção |Descrição |
|---|---|
|D 9876|O porto local que atravessa o túnel.|
|C|Comprimia todos os dados, porque o tráfego web é principalmente texto.|
|2|Force o SSH a tentar apenas a versão 2 do protocolo.|
|q|Modo tranquilo.|
|T|Desative a atribuição de pseudo-tty, já que está apenas a encaminhar um porto.|
|n|Evite a leitura do STDIN, já que está apenas a encaminhar um porto.|
|N|Não execute um comando remoto, já que está apenas a encaminhar uma porta.|
|f|Corra ao fundo.|

Uma vez terminado o comando, o tráfego enviado para a porta 9876 no computador local é encaminhado para o nó de cabeça de cluster.

## <a name="create-a-tunnel-using-putty"></a><a name="useputty"></a>Criar um túnel usando PuTTY

[PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty) é um cliente gráfico da SSH para windows. Se não está familiarizado com a PuTTY, consulte a documentação do [PuTTY.](https://www.chiark.greenend.org.uk/~sgtatham/putty/docs.html) Utilize os seguintes passos para criar um túnel SSH utilizando o PuTTY:

### <a name="create-or-load-a-session"></a>Criar ou carregar uma sessão

1. Abra o PuTTY e certifique-se de que a **Sessão** está selecionada no menu esquerdo. Se já guardou uma sessão, selecione o nome da sessão na lista **de Sessões Salvas** e selecione **Load**.

1. Se ainda não tiver uma sessão guardada, insira as informações de ligação:

    |Propriedade |Valor |
    |---|---|
    |Nome do anfitrião (ou endereço IP)|O endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.|
    |Porta|22|
    |Tipo de Ligação|SSH|

1. Selecione **Guardar**

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-create-putty-session.png" alt-text="HDInsight criar sessão de putty":::

1. Na secção **categoria** à esquerda do diálogo, expanda a **Ligação,** expanda o **SSH** e, em seguida, selecione **Túneis**.

1. Fornecer as seguintes informações sobre as **opções que controlam o formulário de encaminhamento da porta SSH:**

    |Propriedade |Valor |
    |---|---|
    |Porta de origem|O porto do cliente que deseja encaminhar. Por exemplo, **9876**.|
    |Destino|O endereço SSH para o cluster HDInsight. Por exemplo, **mycluster-ssh.azurehdinsight.net**.|
    |Dinâmica|Permite um encaminhamento dinâmico de procuração SOCKS.|

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-putty-tunnel.png" alt-text="Opções de túneis de configuração putty":::

1. **Selecione Adicionar** para adicionar as definições e, em seguida, selecione **Abrir** para abrir uma ligação SSH.

1. Quando solicitado, inscreva-se no servidor.

## <a name="use-the-tunnel-from-your-browser"></a>Use o túnel a partir do seu navegador

> [!IMPORTANT]  
> Os passos nesta secção utilizam o navegador Mozilla FireFox, uma vez que fornece as mesmas configurações de procuração em todas as plataformas. Outros navegadores modernos, como o Google Chrome, podem necessitar de uma extensão como a FoxyProxy para trabalhar com o túnel.

1. Configuure o navegador para usar o **local** e a porta que usou ao criar o túnel como um proxy **SOCKS v5.** Aqui está como são as configurações do Firefox. Se usou uma porta diferente de 9876, mude a porta para a que utilizou:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/firefox-proxy-settings.png" alt-text="configurações de procuração de navegador firefox":::

   > [!NOTE]  
   > A seleção **de DNS remotos** resolve os pedidos do Sistema de Nome de Domínio (DNS) utilizando o cluster HDInsight. Esta definição resolve o DNS utilizando o nó de cabeça do cluster.

2. Verifique se o túnel funciona visitando um local como [https://www.whatismyip.com/](https://www.whatismyip.com/) . O IP devolvido deve ser utilizado pelo centro de dados microsoft Azure.

## <a name="verify-with-ambari-web-ui"></a>Verifique com a UI web Ambari

Uma vez estabelecido o cluster, utilize os seguintes passos para verificar se pode aceder a UI de serviço a partir da Web Ambari:

1. No browser, aceda a `http://headnodehost:8080`. O `headnodehost` endereço é enviado sobre o túnel para o aglomerado e resolve-se com o nó de cabeça em que Ambari está correndo. Quando solicitado, insira o nome de utilizador administrativo (administrador) e a palavra-passe para o seu cluster. Pode ser solicitado uma segunda vez pela UI web Ambari. Em caso afirmativo, reentre na informação.

   > [!NOTE]  
   > Ao utilizar o `http://headnodehost:8080` endereço para ligar ao cluster, está a ligar-se através do túnel. A comunicação é assegurada utilizando o túnel SSH em vez de HTTPS. Para ligar através da internet utilizando HTTPS, use `https://clustername.azurehdinsight.net` , onde está o nome do `clustername` cluster.

2. A partir da UI Web Ambari, selecione HDFS da lista à esquerda da página.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdfs-service-selected.png" alt-text="Serviço Apache Ambari hdfs selecionado":::

3. Quando a informação do serviço HDFS for apresentada, selecione **Links Rápidos**. Aparece uma lista dos nós da cabeça do cluster. Selecione um dos nós da cabeça e, em seguida, selecione **NameNode UI**.

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/namenode-drop-down-menu.png" alt-text="Imagem com o menu QuickLinks expandido":::

    > [!NOTE]  
    > Quando selecionar __Links Rápidos,__ poderá obter um indicador de espera. Esta condição pode ocorrer se tiver uma ligação lenta à internet. Aguarde um minuto ou dois para que os dados sejam recebidos do servidor e, em seguida, tente a lista novamente.
    >
    > Algumas entradas no menu **Links Rápidos** podem ser cortadas pelo lado direito do ecrã. Em caso afirmativo, expanda o menu utilizando o rato e utilize a tecla de seta direita para deslocar o ecrã para a direita para ver o resto do menu.

4. É exibida uma página semelhante à seguinte imagem:

    :::image type="content" source="./media/hdinsight-linux-ambari-ssh-tunnel/hdinsight-namenode-ui.png" alt-text="Imagem do Hadoop NameNode UI":::

    > [!NOTE]  
    > Note o URL para esta página; deve ser semelhante a `http://hn1-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8088/cluster` . Este URI está a utilizar o nome de domínio interno totalmente qualificado (FQDN) do nó, e só é acessível quando se utiliza um túnel SSH.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a criar e usar um túnel SSH, consulte o seguinte documento para outras formas de usar Ambari:

* [Gerir clusters HDInsight utilizando Apache Ambari](hdinsight-hadoop-manage-ambari.md)
