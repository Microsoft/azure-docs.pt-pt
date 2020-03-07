---
title: Utilizar SSH com Hadoop - Azure HDInsight
description: Pode aceder ao HDInsight através do Secure Shell (SSH). Este documento fornece informações sobre como ligar ao HDInsight através dos comandos ssh e scp dos clientes Windows, Linux, Unix ou macOS.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 31e85876d60ae6fcd8f3b29633506d698a323acb
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78386407"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Ligue-se ao HDInsight (Apache Hadoop) utilizando o SSH

Aprenda a utilizar a [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) para ligar de forma segura ao Apache Hadoop no Azure HDInsight. Para obter informações sobre a ligação através de uma rede virtual, consulte a arquitetura de [rede virtual Azure HDInsight](./hdinsight-virtual-network-architecture.md) e [planeie uma implementação de rede virtual para clusters Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

A tabela seguinte contém o endereço e as informações da porta necessárias para a ligação ao HDInsight utilizando um cliente SSH:

| Endereço | Porta | Liga-se a... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nó principal primário |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nó principal secundário |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | nó de borda (Serviços ML no HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | nó de borda (qualquer outro tipo de cluster, se um nó de borda existir) |

Substitua `<clustername>` pelo nome do cluster. Substitua `<edgenodename>` pelo nome do nó de extremidade.

Se o cluster contém um nó de extremidade, recomendamos __ligar sempre ao nó de extremidade__ através de SSH. Os nós principais alojam serviços que são fundamentais para o estado de funcionamento do Hadoop. O nó de extremidade executa apenas o que colocar no mesmo. Para obter mais informações sobre a utilização de nós de extremidade, veja [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight).

> [!TIP]  
> Quando liga pela primeira vez ao HDInsight, o cliente SSH pode apresentar um aviso a indicar que não é possível estabelecer a autenticidade do anfitrião. Quando lhe for pedido selecione "sim" para adicionar o anfitrião à lista de servidores fidedignos do seu cliente SSH.
>
> Se anteriormente tiver ligado a um servidor com o mesmo nome, poderá receber um aviso a indicar que a chave de anfitrião armazenado não corresponde à chave de anfitrião do servidor. Veja a documentação para o cliente SSH sobre como remover a entrada existente para o nome do servidor.

## <a name="ssh-clients"></a>Clientes SSH

Os sistemas Linux, Unix e macOS fornecem os comandos `ssh` e `scp`. O cliente `ssh` é usado normalmente para criar uma sessão de linha de comandos remota com um sistema baseado em Unix ou Linux. O cliente `scp` é usado para copiar com segurança ficheiros entre o cliente e o sistema remoto.

O Microsoft Windows não instala clientes SSH por padrão. Os clientes `ssh` e `scp` estão disponíveis para o Windows através dos seguintes pacotes:

* [Openssh Cliente](https://docs.microsoft.com/windows-server/administration/openssh/openssh_install_firstuse). Este cliente é uma funcionalidade opcional introduzida na Atualização de Criadores de outono do Windows 10.

* [Bata em Ubuntu no Windows 10](https://docs.microsoft.com/windows/wsl/about).

* [Concha de nuvem azure.](../cloud-shell/quickstart.md) A Cloud Shell fornece um ambiente Bash no seu navegador.

* [Git](https://git-scm.com/).

Existem também vários clientes sSH gráficos, como [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) e [MobaXterm.](https://mobaxterm.mobatek.net/) Embora estes clientes possam ser utilizados para ligar ao HDInsight, o processo de ligação é diferente relativamente à utilização do utilitário `ssh`. Para mais informações, consulte a documentação do cliente gráfico que está a usar.

## <a id="sshkey"></a>Autenticação: chaves SSH

As teclas SSH usam [criptografia de chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) para autenticar sessões de SSH. As chaves SSH são mais seguras do que as palavras-passe e proporcionam uma forma fácil de proteger o acesso ao seu cluster do Hadoop.

Se a sua conta SSH for protegida com uma chave, o cliente tem de fornecer a chave privada correspondente quando se ligar:

* A maioria dos clientes podem ser configurados para utilizar uma __chave predefinida__. Por exemplo, o cliente `ssh` procura uma chave privada em `~/.ssh/id_rsa` nos ambientes Linux e Unix.

* Pode especificar o __caminho para uma chave privada__. Com o cliente `ssh`, o parâmetro `-i` é utilizado para especificar o caminho para a chave privada. Por exemplo, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se tiver __múltiplas chaves privadas__ para utilização com diferentes servidores, considere utilizar um utilitário, como o [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). O utilitário `ssh-agent` pode ser usado para selecionar automaticamente a chave a utilizar ao estabelecer uma sessão SSH.

> [!IMPORTANT]  
> Se proteger a chave privada com uma frase de acesso, tem de introduzi-la quando utilizar a chave. Utilitários como `ssh-agent` podem colocar em cache a palavra-passe, para sua comodidade.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Utilize o comando `ssh-keygen` para criar ficheiros de chaves públicas e privadas. O comando seguinte gera um par de chaves RSA de 2048 bits que pode ser utilizado com o HDInsight:

    ssh-keygen -t rsa -b 2048

Foi solicitado para obter informações durante o processo de criação. Por exemplo, onde estão armazenadas as chaves ou se pretende utilizar uma frase de acesso. Após a conclusão do processo, são criados dois ficheiros: uma chave pública e uma chave privada.

* A __chave pública__ é utilizada para criar um cluster do HDInsight. A chave pública tem a extensão `.pub`.

* A __chave privada__ é utilizada para autenticar o cliente no cluster do HDInsight.

> [!IMPORTANT]  
> Pode utilizar uma frase de acesso para proteger as chaves. Uma frase de acesso é efetivamente uma palavra-passe na sua chave privada. Mesmo se alguém obtiver a sua chave privada, essa pessoa tem de ter a frase de acesso para poder utilizá-la.

### <a name="create-hdinsight-using-the-public-key"></a>Utilizar a chave pública para criar o HDInsight

| Método de criação | Como utilizar a chave pública |
| ------- | ------- |
| Portal do Azure | Desverificar Utilize a palavra-passe de login do __cluster para SSH__e, em seguida, selecione a chave __pública__ como o tipo de autenticação SSH. Por fim, selecione o ficheiro de chave pública ou cole os conteúdos de texto do ficheiro no campo __Chave pública SSH__.</br>![Caixa de diálogo Chave pública SSH na criação do cluster do HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png) |
| Azure PowerShell | Utilize o parâmetro `-SshPublicKey` do [cmdlet New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) e passe o conteúdo da chave pública como uma corda.|
| CLI do Azure | Utilize o parâmetro `--sshPublicKey` do [az hdinsight criar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) comando e passar o conteúdo da chave pública como uma corda. |
| Modelo do Resource Manager | Para obter um exemplo de como utilizar chaves SSH com um modelo, veja [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Implementar o HDInsight no Linux com uma chave SSH). O elemento `publicKeys` no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) é utilizado para transmitir as chaves ao Azure ao criar o cluster. |

## <a name="authentication-password"></a>Autenticação: Palavra-passe

As contas SSH podem ser protegidas através de palavra-passe. Quando se liga ao HDInsight utilizando sSH, é-lhe pedido que introduza a palavra-passe.

> [!WARNING]  
> A Microsoft não recomenda utilizar a autenticação por palavra-passe para SSH. As palavras-passe podem ser descobertas e são vulneráveis a ataques de força bruta. Em vez disso, recomendamos que utilize [chaves SSH para a autenticação](#sshkey).

> [!IMPORTANT]  
> A palavra-passe da conta SSH expira 70 dias após a criação do cluster do HDInsight. Se a palavra-passe expirar, pode alterá-la ao utilizar as informações no documento [Gerir o HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Utilizar uma palavra-passe para criar o HDInsight

| Método de criação | Como especificar a palavra-passe |
| --------------- | ---------------- |
| Portal do Azure | Por predefinição, a conta de utilizador SSH tem a mesma palavra-passe da conta de início de sessão do cluster. Para utilizar uma palavra-passe diferente, desmarque __utilize a palavra-passe de login do cluster para SSH__e introduza a palavra-passe no campo de __palavra-passe SSH.__</br>![Caixa de diálogo Palavra-passe SSH na criação do cluster do HDInsight](./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png)|
| Azure PowerShell | Utilize o parâmetro `--SshCredential` do [cmdlet New-AzHdinsightCluster](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) e passe um objeto `PSCredential` que contenha o nome e a palavra-passe da conta do utilizador SSH. |
| CLI do Azure | Utilize o parâmetro `--sshPassword` do [az hdinsight criar](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) comando e fornecer o valor da palavra-passe. |
| Modelo do Resource Manager | Para obter um exemplo de como utilizar a palavra-passe com um modelo, veja [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Implementar o HDInsight no Linux com uma palavra-passe SSH). O elemento `linuxOperatingSystemProfile` no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) é utilizado para transmitir o nome e a palavra-passe da conta SSH ao Azure ao criar o cluster.|

### <a name="change-the-ssh-password"></a>Alterar a palavra-passe SSH

Para obter informações sobre como alterar a palavra-passe da conta do utilizador SSH, veja a secção __Change passwords__ (Alterar palavras-passe) do documento [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) (Gerir o HDInsight).

## <a name="authentication-domain-joined-hdinsight"></a>Domínio de autenticação juntou-se ao HDInsight

Se estiver a utilizar um __cluster HDInsight unido__ao domínio, deve utilizar o comando `kinit` depois de se ligar ao utilizador local SSH. Este comando pede-lhe um utilizador e uma palavra-passe de domínio e autentica a sua sessão com o domínio do Azure Active Directory associado ao cluster.

Também pode ativar a Autenticação Kerberos em cada domínio junto ao nó (por exemplo, nó de cabeça, nó de borda) para ssh usando a conta de domínio. Para editar este ficheiro de configuração de sshd:

```bash
sudo vi /etc/ssh/sshd_config
```

anule os comentários e altere `KerberosAuthentication` para `yes`

```bash
sudo service sshd restart
```

Use `klist` comando para verificar se a autenticação Kerberos foi bem sucedida.

Para obter mais informações, veja [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure.md) (Configurar o HDInsight associado a um domínio).

## <a name="connect-to-nodes"></a>Ligar a nós

Os nós da cabeça e o nó de borda (se houver um) podem ser acedidos através da internet nas portas 22 e 23.

* Ao estabelecer ligação com os __nós principais__, utilize a porta __22__ ligar ao nó principal primário e a porta __23__ para ligar ao nó principal secundário. O nome de domínio completamente qualificado a utilizar é `clustername-ssh.azurehdinsight.net`, em que `clustername` é o nome do cluster.

    ```bash
    # Connect to primary head node
    # port not specified since 22 is the default
    ssh sshuser@clustername-ssh.azurehdinsight.net

    # Connect to secondary head node
    ssh -p 23 sshuser@clustername-ssh.azurehdinsight.net
    ```

* Quando ligar ao __nó de extremidade__, utilize a porta 22. O nome de domínio completamente qualificado é `edgenodename.clustername-ssh.azurehdinsight.net`, em que `edgenodename` é um nome que forneceu quando criou o nó de extremidade. `clustername` é o nome do cluster.

    ```bash
    # Connect to edge node
    ssh sshuser@edgnodename.clustername-ssh.azurehdinsight.net
    ```

> [!IMPORTANT]  
> Os exemplos anteriores pressupõem que está a utilizar a autenticação por palavra-passe ou a autenticação de certificados está a ocorrer automaticamente. Se utilizar um par de chaves SSH para a autenticação e o certificado não for utilizado automaticamente, utilize o parâmetro `-i` para especificar a chave privada. Por exemplo, `ssh -i ~/.ssh/mykey sshuser@clustername-ssh.azurehdinsight.net`.

Uma vez ligado, o aviso altera-se para indicar o nome do utilizador SSH e o nó a que está ligado. Por exemplo, quando estiver ligado ao nó principal primário como `sshuser`, a linha de comandos é `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Ligue-se aos nóóis do trabalhador e do Zookeeper Apache

Os nós operários e os nós do Zookeeper não são acessíveis diretamente a partir da internet. Podem ser acedidos a partir dos nós principais ou nós de extremidade do cluster. Seguem-se os passos gerais necessários para ligar a outros nós:

1. Utilizar o SSH para ligar a um nó principal ou de extremidade:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. A partir da ligação SSH ao nó principal ou de extremidade, utilize o comando `ssh` para ligar a um nó de trabalho no cluster:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Para obter uma lista dos nomes do nó, consulte o [Manage HDInsight utilizando o documento Apache Ambari REST API.](hdinsight-hadoop-manage-ambari-rest-api.md#example-get-the-fqdn-of-cluster-nodes)

Se a conta SSH for protegida por __palavra-passe__, introduza-a ao ligar.

Se a conta SSH for protegida por __chaves SSH__, certifique-se de que o encaminhamento SSH está ativado no cliente.

> [!NOTE]  
> Outra forma de aceder diretamente a todos os nós do cluster é instalar HDInsight numa Rede Virtual do Azure. Depois, pode associar o computador remoto à mesma rede virtual e aceder diretamente a todos os nós no cluster.
>
> Para mais informações, consulte [Plan a virtual network for HDInsight](hdinsight-plan-virtual-network-deployment.md).

### <a name="configure-ssh-agent-forwarding"></a>Configurar o reencaminhamento de agentes SSH

> [!IMPORTANT]  
> Os passos seguintes pressupõem um sistema baseado em Linux ou UNIX e funcionam com o Bash no Windows 10. Se não funcionarem no seu sistema, poderá ter de ver a documentação do seu cliente SSH.

1. Utilizando um editor de texto, abra `~/.ssh/config`. Se este ficheiro não existir, pode criá-lo ao introduzir `touch ~/.ssh/config` na linha de comandos.

2. Adicione o texto seguinte ao ficheiro `config`.

    ```
    Host <edgenodename>.<clustername>-ssh.azurehdinsight.net
        ForwardAgent yes
    ```

    Substitua as informações do __Anfitrião__ pelo endereço do nó ao qual se liga com SSH. O exemplo anterior utiliza o nó de extremidade. Esta entrada configura o reencaminhamento de agentes SSH para o nó especificado.

3. Teste o reencaminhamento de agente SSH ao utilizar o comando seguinte no terminal:

    ```bash
    echo "$SSH_AUTH_SOCK"
    ```

    Este comando devolve informações semelhantes ao texto seguinte:

    ```output
    /tmp/ssh-rfSUL1ldCldQ/agent.1792
    ```

    Se nada for devolvido, `ssh-agent` não está a correr. Para obter mais informações, veja as informações de scripts de arranque do agente em [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Utilizar o ssh-agent com ssh) ou veja a documentação do cliente SSH.

4. Depois de verificar que o **agente SSH** está em execução, use o seguinte para adicionar a sua chave privada SSH ao agente:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Se a chave privada estiver armazenada num ficheiro diferente, substitua `~/.ssh/id_rsa` pelo caminho para o ficheiro.

5. Utilize SSH para ligar aos nós de extremidade ou principais do cluster. Em seguida, utilize o comando SSH para ligar a um nó de trabalho ou zookeeper. A ligação é estabelecida com a chave reencaminhada.

## <a name="copy-files"></a>Copiar ficheiros

O utilitário `scp` pode ser utilizado para copiar ficheiros de e para os nós individuais do cluster. Por exemplo, o comando seguinte copia o diretório `test.txt` do sistema local para o nó principal primário:

```bash
scp test.txt sshuser@clustername-ssh.azurehdinsight.net:
```

Uma vez que não foi especificado nenhum caminho após `:`, o ficheiro é colocado no diretório raiz `sshuser`.

O exemplo seguinte copia o ficheiro `test.txt` do diretório raiz `sshuser` no nó principal primário para o sistema local:

```bash
scp sshuser@clustername-ssh.azurehdinsight.net:test.txt .
```

> [!IMPORTANT]  
> `scp` apenas pode aceder ao sistema de ficheiros de nós individuais dentro do cluster. Não pode ser utilizado para aceder a dados no armazenamento compatível com HDFS do cluster.
>
> Utilize `scp` quando tiver de carregar um recurso para utilização a partir de uma sessão SSH. Por exemplo, carregue um script de Python e, em seguida, execute o script a partir de uma sessão SSH.
>
> Para obter informações sobre o carregamento de dados diretamente para o armazenamento compatível com HDFS, consulte os seguintes documentos:
>
> * [HDInsight com o Armazenamento do Azure](hdinsight-hadoop-use-blob-storage.md).
>
> * [HDInsight usando o armazenamento](hdinsight-hadoop-use-data-lake-store.md)do lago De dados Azure .

## <a name="next-steps"></a>Passos seguintes

* [Use SSH tunneling with HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) (Utilizar túnel SSH com o HDInsight)
* [Planeie uma rede virtual com o HDInsight](hdinsight-plan-virtual-network-deployment.md)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight)
