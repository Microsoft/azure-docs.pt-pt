---
title: Utilizar SSH com Hadoop - Azure HDInsight
description: Pode aceder ao HDInsight através do Secure Shell (SSH). Este documento fornece informações sobre a ligação ao HDInsight utilizando os comandos ssh de clientes Windows, Linux, Unix ou macOS.
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 02/28/2020
ms.openlocfilehash: b6e8314e87a11deeea1d7c754a366b1bf2214f3c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770296"
---
# <a name="connect-to-hdinsight-apache-hadoop-using-ssh"></a>Connect to HDInsight (Apache Hadoop) using SSH (Ligar ao HDInsight (Apache Hadoop) através de SSH)

Aprenda a usar [Secure Shell (SSH)](https://en.wikipedia.org/wiki/Secure_Shell) para ligar de forma segura ao Apache Hadoop em Azure HDInsight. Para obter informações sobre a ligação através de uma rede virtual, consulte [a arquitetura de rede virtual Azure HDInsight](./hdinsight-virtual-network-architecture.md). Consulte também, [Planeie uma implementação de rede virtual para clusters Azure HDInsight](./hdinsight-plan-virtual-network-deployment.md).

A tabela a seguir contém o endereço e as informações da porta necessárias para a ligação ao HDInsight utilizando um cliente SSH:

| Endereço | Porta | Liga-se a... |
| ----- | ----- | ----- |
| `<clustername>-ssh.azurehdinsight.net` | 22 | Nó principal primário |
| `<clustername>-ssh.azurehdinsight.net` | 23 | Nó principal secundário |
| `<clustername>-ed-ssh.azurehdinsight.net` | 22 | nó de borda (ML Services on HDInsight) |
| `<edgenodename>.<clustername>-ssh.azurehdinsight.net` | 22 | nó de borda (qualquer outro tipo de cluster, se existir um nó de borda) |

Substitua `<clustername>` pelo nome do cluster. Substitua `<edgenodename>` pelo nome do nó de extremidade.

Se o cluster contém um nó de extremidade, recomendamos __ligar sempre ao nó de extremidade__ através de SSH. Os nós principais alojam serviços que são fundamentais para o estado de funcionamento do Hadoop. O nó de extremidade executa apenas o que colocar no mesmo. Para obter mais informações sobre a utilização de nós de extremidade, veja [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight).

> [!TIP]  
> Quando liga pela primeira vez ao HDInsight, o cliente SSH pode apresentar um aviso a indicar que não é possível estabelecer a autenticidade do anfitrião. Quando lhe for pedido selecione "sim" para adicionar o anfitrião à lista de servidores fidedignos do seu cliente SSH.
>
> Se anteriormente tiver ligado a um servidor com o mesmo nome, poderá receber um aviso a indicar que a chave de anfitrião armazenado não corresponde à chave de anfitrião do servidor. Veja a documentação para o cliente SSH sobre como remover a entrada existente para o nome do servidor.

## <a name="ssh-clients"></a>Clientes SSH

Os sistemas Linux, Unix e macOS fornecem os comandos `ssh` e `scp`. O cliente `ssh` é usado normalmente para criar uma sessão de linha de comandos remota com um sistema baseado em Unix ou Linux. O cliente `scp` é usado para copiar com segurança ficheiros entre o cliente e o sistema remoto.

O Microsoft Windows não instala nenhum cliente SSH por padrão. Os clientes `ssh` e `scp` estão disponíveis para o Windows através dos seguintes pacotes:

* [Cliente OpenSsh.](/windows-server/administration/openssh/openssh_install_firstuse) Este cliente é uma funcionalidade opcional introduzida na Atualização de Criadores de outono do Windows 10.

* [Bata no Ubuntu no Windows 10](/windows/wsl/about).

* [Concha de nuvem azul](../cloud-shell/quickstart.md). O Cloud Shell fornece um ambiente bash no seu navegador.

* [Git.](https://git-scm.com/)

Existem também vários clientes gráficos da SSH, como [o PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) e [o MobaXterm.](https://mobaxterm.mobatek.net/) Embora estes clientes possam ser utilizados para ligar ao HDInsight, o processo de ligação é diferente relativamente à utilização do utilitário `ssh`. Para mais informações, consulte a documentação do cliente gráfico que está a usar.

## <a name="authentication-ssh-keys"></a><a id="sshkey"></a>Autenticação: chaves SSH

As teclas SSH utilizam [a criptografia da chave pública](https://en.wikipedia.org/wiki/Public-key_cryptography) para autenticar sessões de SSH. As chaves SSH são mais seguras do que as palavras-passe e proporcionam uma forma fácil de proteger o acesso ao seu cluster do Hadoop.

Se a sua conta SSH for protegida com uma chave, o cliente tem de fornecer a chave privada correspondente quando se ligar:

* A maioria dos clientes podem ser configurados para utilizar uma __chave predefinida__. Por exemplo, o cliente `ssh` procura uma chave privada em `~/.ssh/id_rsa` nos ambientes Linux e Unix.

* Pode especificar o __caminho para uma chave privada__. Com o cliente `ssh`, o parâmetro `-i` é utilizado para especificar o caminho para a chave privada. Por exemplo, `ssh -i ~/.ssh/id_rsa sshuser@myedge.mycluster-ssh.azurehdinsight.net`.

* Se tiver __múltiplas chaves privadas__ para utilização com diferentes servidores, considere utilizar um utilitário, como o [ssh-agent (https://en.wikipedia.org/wiki/Ssh-agent)](https://en.wikipedia.org/wiki/Ssh-agent). O utilitário `ssh-agent` pode ser usado para selecionar automaticamente a chave a utilizar ao estabelecer uma sessão SSH.

> [!IMPORTANT]  
> Se proteger a chave privada com uma frase de acesso, tem de introduzi-la quando utilizar a chave. Utilitários como `ssh-agent` podem colocar em cache a palavra-passe, para sua comodidade.

### <a name="create-an-ssh-key-pair"></a>Criar um par de chaves SSH

Utilize o comando `ssh-keygen` para criar ficheiros de chaves públicas e privadas. O comando seguinte gera um par de chaves RSA de 2048 bits que pode ser utilizado com o HDInsight:

```azurepowershell-interactive
ssh-keygen -t rsa -b 2048
```

É solicitado para obter informações durante o processo de criação chave. Por exemplo, onde estão armazenadas as chaves ou se pretende utilizar uma frase de acesso. Após a conclusão do processo, são criados dois ficheiros: uma chave pública e uma chave privada.

* A __chave pública__ é utilizada para criar um cluster do HDInsight. A chave pública tem a extensão `.pub`.

* A __chave privada__ é utilizada para autenticar o cliente no cluster do HDInsight.

> [!IMPORTANT]  
> Pode utilizar uma frase de acesso para proteger as chaves. Uma frase de acesso é efetivamente uma palavra-passe na sua chave privada. Mesmo se alguém obtiver a sua chave privada, essa pessoa tem de ter a frase de acesso para poder utilizá-la.

### <a name="create-hdinsight-using-the-public-key"></a>Utilizar a chave pública para criar o HDInsight

| Método de criação | Como utilizar a chave pública |
| ------- | ------- |
| Portal do Azure | Desmarque __Utilize a palavra-passe de login do cluster para SSH__ e, em seguida, selecione __A Chave Pública__ como o tipo de autenticação SSH. Por fim, selecione o ficheiro de chave pública ou cole os conteúdos de texto do ficheiro no campo __Chave pública SSH__.</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-public-key.png" alt-text="Caixa de diálogo Chave pública SSH na criação do cluster do HDInsight"::: |
| Azure PowerShell | Utilize o `-SshPublicKey` parâmetro do cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e passe o conteúdo da chave pública como uma corda.|
| CLI do Azure | Utilize o `--sshPublicKey` parâmetro do comando e passe o conteúdo da chave pública como uma [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) corda. |
| Modelo do Resource Manager | Para obter um exemplo de como utilizar chaves SSH com um modelo, veja [Deploy HDInsight on Linux with SSH key](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-publickey/) (Implementar o HDInsight no Linux com uma chave SSH). O elemento `publicKeys` no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-publickey/azuredeploy.json) é utilizado para transmitir as chaves ao Azure ao criar o cluster. |

## <a name="authentication-password"></a>Autenticação: palavra-passe

As contas SSH podem ser protegidas através de palavra-passe. Quando se conecta ao HDInsight utilizando o SSH, é-lhe solicitado que introduza a palavra-passe.

> [!WARNING]  
> A Microsoft não recomenda utilizar a autenticação por palavra-passe para SSH. As palavras-passe podem ser descobertas e são vulneráveis a ataques de força bruta. Em vez disso, recomendamos que utilize [chaves SSH para a autenticação](#sshkey).

> [!IMPORTANT]  
> A palavra-passe da conta SSH expira 70 dias após a criação do cluster do HDInsight. Se a palavra-passe expirar, pode alterá-la ao utilizar as informações no documento [Gerir o HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords).

### <a name="create-hdinsight-using-a-password"></a>Utilizar uma palavra-passe para criar o HDInsight

| Método de criação | Como especificar a palavra-passe |
| --------------- | ---------------- |
| Portal do Azure | Por predefinição, a conta de utilizador SSH tem a mesma palavra-passe da conta de início de sessão do cluster. Para utilizar uma palavra-passe diferente, desmarque use a __palavra-passe de login do cluster para SSH__ e, em seguida, introduza a palavra-passe no campo __de palavras-passe SSH.__</br>:::image type="content" source="./media/hdinsight-hadoop-linux-use-ssh-unix/create-hdinsight-ssh-password.png" alt-text="Caixa de diálogo Palavra-passe SSH na criação do cluster do HDInsight":::|
| Azure PowerShell | Utilize o `--SshCredential` parâmetro do cmdlet [New-AzHdinsightCluster](/powershell/module/az.hdinsight/new-azhdinsightcluster) e passe um `PSCredential` objeto que contenha o nome e senha da conta de utilizador SSH. |
| CLI do Azure | Utilize o `--ssh-password` parâmetro do comando e forneça o valor da [`az hdinsight create`](/cli/azure/hdinsight#az_hdinsight_create) palavra-passe. |
| Modelo do Resource Manager | Para obter um exemplo de como utilizar a palavra-passe com um modelo, veja [Deploy HDInsight on Linux with SSH password](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-ssh-password/) (Implementar o HDInsight no Linux com uma palavra-passe SSH). O elemento `linuxOperatingSystemProfile` no ficheiro [azuredeploy.json](https://github.com/Azure/azure-quickstart-templates/blob/master/101-hdinsight-linux-ssh-password/azuredeploy.json) é utilizado para transmitir o nome e a palavra-passe da conta SSH ao Azure ao criar o cluster.|

### <a name="change-the-ssh-password"></a>Alterar a palavra-passe SSH

Para obter informações sobre como alterar a palavra-passe da conta do utilizador SSH, veja a secção __Change passwords__ (Alterar palavras-passe) do documento [Manage HDInsight](hdinsight-administer-use-portal-linux.md#change-passwords) (Gerir o HDInsight).

## <a name="authentication-domain-joined-hdinsight"></a>Domínio de autenticação juntou-se a HDInsight

Se estiver a utilizar um __cluster HDInsight ligado a domínio,__ tem de utilizar o `kinit` comando depois de se ligar ao utilizador local SSH. Este comando pede-lhe um utilizador e uma palavra-passe de domínio e autentica a sua sessão com o domínio do Azure Active Directory associado ao cluster.

Também pode ativar a autenticação Kerberos em cada nó de unção de domínio (por exemplo, nó de cabeça, nó de borda) para ssh usando a conta de domínio. Para editar este ficheiro de configuração de sshd:

```bash
sudo vi /etc/ssh/sshd_config
```

anule os comentários e altere `KerberosAuthentication` para `yes`

```bash
sudo service sshd restart
```

Use `klist` o comando para verificar se a autenticação Kerberos foi bem sucedida.

Para obter mais informações, veja [Configure domain-joined HDInsight](./domain-joined/apache-domain-joined-configure-using-azure-adds.md) (Configurar o HDInsight associado a um domínio).

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

Uma vez ligado, o pedido de alteração altera-se para indicar o nome de utilizador SSH e o nó a que está ligado. Por exemplo, quando estiver ligado ao nó principal primário como `sshuser`, a linha de comandos é `sshuser@<active-headnode-name>:~$`.

### <a name="connect-to-worker-and-apache-zookeeper-nodes"></a>Conecte-se aos acenos de guarda zoológico e trabalhador e Apache Zookeeper

Os nós operários e os nós do Zookeeper não são diretamente acessíveis a partir da internet. Podem ser acedidos a partir dos nós principais ou nós de extremidade do cluster. Seguem-se os passos gerais necessários para ligar a outros nós:

1. Utilizar o SSH para ligar a um nó principal ou de extremidade:

    ```bash
    ssh sshuser@myedge.mycluster-ssh.azurehdinsight.net
    ```

2. A partir da ligação SSH ao nó principal ou de extremidade, utilize o comando `ssh` para ligar a um nó de trabalho no cluster:

    ```bash
    ssh sshuser@wn0-myhdi
    ```

    Para obter uma lista dos nomes dos nós, consulte o [Manage HDInsight utilizando o documento API Apache Ambari REST.](hdinsight-hadoop-manage-ambari-rest-api.md#get-the-fqdn-of-cluster-nodes)

Se a conta SSH for protegida por __palavra-passe__, introduza-a ao ligar.

Se a conta SSH for protegida por __chaves SSH__, certifique-se de que o encaminhamento SSH está ativado no cliente.

> [!NOTE]  
> Outra forma de aceder diretamente a todos os nós do cluster é instalar HDInsight numa Rede Virtual do Azure. Depois, pode associar o computador remoto à mesma rede virtual e aceder diretamente a todos os nós no cluster.
>
> Para obter mais informações, consulte [Plan uma rede virtual para HDInsight](hdinsight-plan-virtual-network-deployment.md).

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

    Se nada for devolvido, `ssh-agent` então não vai correr. Para obter mais informações, veja as informações de scripts de arranque do agente em [Using ssh-agent with ssh (http://mah.everybody.org/docs/ssh)](http://mah.everybody.org/docs/ssh) (Utilizar o ssh-agent com ssh) ou veja a documentação do cliente SSH.

4. Assim que verificar que o **agente ssh** está em execução, use o seguinte para adicionar a sua chave privada SSH ao agente:

    ```bash
    ssh-add ~/.ssh/id_rsa
    ```

    Se a chave privada estiver armazenada num ficheiro diferente, substitua `~/.ssh/id_rsa` pelo caminho para o ficheiro.

5. Utilize SSH para ligar aos nós de extremidade ou principais do cluster. Em seguida, utilize o comando SSH para ligar a um nó de trabalho ou zookeeper. A ligação é estabelecida com a chave reencaminhada.

## <a name="next-steps"></a>Passos seguintes

* [Use SSH tunneling with HDInsight](hdinsight-linux-ambari-ssh-tunnel.md) (Utilizar túnel SSH com o HDInsight)
* [Use edge nodes in HDInsight](hdinsight-apps-use-edge-node.md#access-an-edge-node) (Utilizar nós de extremidade no HDInsight)
* [Utilizar o SCP com o HDInsight](./use-scp.md)