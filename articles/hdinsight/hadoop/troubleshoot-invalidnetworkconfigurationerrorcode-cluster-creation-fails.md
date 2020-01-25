---
title: Erro InvalidNetworkConfigurationErrorCode-Azure HDInsight
description: Vários motivos para as criações de cluster com falha com o InvalidNetworkConfigurationErrorCode no Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720389"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Falha na criação do cluster com InvalidNetworkConfigurationErrorCode no Azure HDInsight

Este artigo descreve as etapas de solução de problemas e as possíveis resoluções para problemas ao interagir com clusters do Azure HDInsight.

Se vir o código de erro `InvalidNetworkConfigurationErrorCode` com a descrição "A configuração da Rede Virtual não é compatível com o Requisito HDInsight", normalmente indica um problema com a [configuração](../hdinsight-plan-virtual-network-deployment.md) da rede virtual para o seu cluster. Com base no restante da descrição do erro, siga as seções abaixo para resolver o problema.

## <a name="hostname-resolution-failed"></a>"Falha na resolução do nome do host"

### <a name="issue"></a>Problema

A descrição do erro contém "falha na resolução do nome do host".

### <a name="cause"></a>Causa

Esse erro aponta para um problema com a configuração de DNS personalizada. Os servidores DNS em uma rede virtual podem encaminhar consultas DNS para resolvedores recursivos do Azure para resolver nomes de host dentro dessa rede virtual (consulte [resolução de nomes em redes virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para obter detalhes). O acesso aos resolvedores recursivos do Azure é fornecido por meio do 168.63.129.16 de IP virtual. Esse IP só pode ser acessado por meio das VMs do Azure. Portanto, não funcionará se estiver a usar um servidor OnPrem DNS, ou se o seu servidor DNS for um Azure VM, que não faz parte da rede virtual do cluster.

### <a name="resolution"></a>Resolução

1. Ssh na VM que faz parte do cluster e execute o comando `hostname -f`. Isto devolverá o nome de domínio totalmente qualificado do hospedeiro (referido como `<host_fqdn>` nas instruções abaixo).

1. Em seguida, executar o comando `nslookup <host_fqdn>` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`). Se esse comando resolver o nome para um endereço IP, significa que o servidor DNS está funcionando corretamente. Neste caso, levante um caso de suporte com o HDInsight, e vamos investigar o seu problema. Em seu caso de suporte, inclua as etapas de solução de problemas que você executou. Isso nos ajudará a resolver o problema mais rapidamente.

1. Se o comando acima não devolver um endereço IP, então faça `nslookup <host_fqdn> 168.63.129.16` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`). Se este comando for capaz de resolver o IP, significa que ou o seu servidor DNS não está a encaminhar a consulta para o DNS do Azure, ou não é um VM que faz parte da mesma rede virtual que o cluster.

1. Se não tiver um VM Azure que possa funcionar como um servidor DNS personalizado na rede virtual do cluster, então precisa adicionar este primeiro. Crie um VM na rede virtual, que será configurado como avançado do DNS.

1. Assim que tiver um VM implantado na sua rede virtual, configure as regras de encaminhamento do DNS neste VM. Encaminhe todas as solicitações de resolução de nomes de iDNS para 168.63.129.16 e o restante para o servidor DNS. [Aqui](../hdinsight-plan-virtual-network-deployment.md) está um exemplo dessa configuração para um servidor DNS personalizado.

1. Adicione o endereço IP dessa VM como a primeira entrada DNS para a configuração de DNS da rede virtual.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Falha ao conectar-se à conta de armazenamento do Azure"

### <a name="issue"></a>Problema

A descrição do erro contém "falha ao se conectar à conta de armazenamento do Azure" ou "falha ao conectar-se ao SQL do Azure".

### <a name="cause"></a>Causa

O Azure Storage e o SQL não dispõem de endereços IP fixos, pelo que precisamos de permitir ligações de saída a todos os IPs para permitir o acesso a estes serviços. As etapas exatas de resolução dependem se você configurou um NSG (grupo de segurança de rede) ou UDR (regras definidas pelo usuário). Consulte a seção sobre como [controlar o tráfego de rede com o HDInsight com grupos de segurança de rede e rotas definidas pelo usuário](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) para obter detalhes sobre essas configurações.

### <a name="resolution"></a>Resolução

* Se o cluster usar um [NSG (grupo de segurança de rede)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Vá para a portal do Azure e identifique o NSG associado à sub-rede em que o cluster está sendo implantado. Na seção **regras de segurança de saída** , permita o acesso de saída à Internet sem limitação (Observe que um número de **prioridade** menor aqui significa prioridade mais alta). Além disso, na seção **sub-redes** , confirme se esse NSG é aplicado à sub-rede do cluster.

* Se o cluster usar uma [UDR (rotas definidas pelo usuário)](../../virtual-network/virtual-networks-udr-overview.md).

    Vá para a portal do Azure e identifique a tabela de rotas associada à sub-rede em que o cluster está sendo implantado. Depois de encontrar a tabela de rotas para a sub-rede, inspecione a seção **rotas** nela.

    Se houver rotas definidas, verifique se há rotas para endereços IP para a região em que o cluster foi implantado e o **NextHopType** para cada rota é **Internet**. Deve haver uma rota definida para cada endereço IP necessário documentado no artigo mencionado anteriormente.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A configuração da rede virtual não é compatível com o requisito HDInsight"

### <a name="issue"></a>Problema

As descrições de erros contêm mensagens semelhantes às seguintes:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Causa

Provavelmente um problema com a configuração personalizada do DNS.

### <a name="resolution"></a>Resolução

Valide que 168.63.129.16 está na cadeia DNS personalizada. Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para os resolvers recursivos do Azure para resolver nomes de anfitriões dentro dessa rede virtual. Para mais informações, consulte [a Resolução de Nomes em Redes Virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). O acesso aos resolvedores recursivos do Azure é fornecido por meio do 168.63.129.16 de IP virtual.

1. Use o [comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se conectar ao cluster. Edite o comando a seguir substituindo CLUSTERname pelo nome do cluster e, em seguida, digite o comando:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Execute o seguinte comando:

    ```bash
    cat /etc/resolv.conf | grep nameserver*
    ```

    Deverá ver algo semelhante ao seguinte:

    ```output
    nameserver 168.63.129.16
    nameserver 10.21.34.43
    nameserver 10.21.34.44
    ```

    Com base no resultado - escolha um dos seguintes passos a seguir:

#### <a name="1686312916-is-not-in-this-list"></a>168.63.129.16 não está nesta lista

**Opção 1**  
Adicione 168.63.129.16 como o primeiro DNS personalizado para a rede virtual utilizando os passos descritos no [Plano uma rede virtual para Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Estes passos só são aplicáveis se o seu servidor DNS personalizado funcionar no Linux.

**Opção 2**  
Implemente um VM de servidor DNS para a rede virtual. Isto envolve os seguintes passos:

* Crie um VM na rede virtual, que será configurado como avançado dNS (pode ser um Linux ou Windows VM).
* Configure as regras de encaminhamento de DNS neste VM (encaminhar todos os pedidos de resolução de nomes iDNS para 168.63.129.16 e o resto para o seu servidor DNS).
* Adicione o endereço IP deste VM como primeira entrada dNS para a configuração DNS da rede virtual.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 está na lista

Neste caso, por favor crie um caso de suporte com o HDInsight, e vamos investigar o seu problema. Inclua o resultado dos comandos abaixo no seu caso de suporte. Isto ajudar-nos-á a investigar e resolver o problema mais rapidamente.

A partir de uma sessão de sh no nó da cabeça, edite e, em seguida, executar o seguinte:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

* Obtenha respostas de especialistas do Azure por meio do [suporte da Comunidade do Azure](https://azure.microsoft.com/support/community/).

* Conecte-se com o [@AzureSupport](https://twitter.com/azuresupport) -a conta de Microsoft Azure oficial para melhorar a experiência do cliente conectando a Comunidade do Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, você poderá enviar uma solicitação de suporte do [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecione **suporte** na barra de menus ou abra o Hub **ajuda + suporte** . Para obter informações mais detalhadas, consulte [como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). O acesso ao gerenciamento de assinaturas e ao suporte de cobrança está incluído na sua assinatura do Microsoft Azure, e o suporte técnico é fornecido por meio de um dos [planos de suporte do Azure](https://azure.microsoft.com/support/plans/).
