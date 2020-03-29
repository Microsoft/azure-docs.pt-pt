---
title: Erro invalidNetworkConfigurationErrorCode - Azure HDInsight
description: Várias razões para criações falhadas de cluster com InvalidNetworkConfigurationErrorCode in Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: 6dd4db999cb130c9816ad023888a4333e968c224
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720389"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>Criação de cluster falha com InvalidNetworkConfigurationErrorCode no Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

Se vir o `InvalidNetworkConfigurationErrorCode` código de erro com a descrição "A configuração da Rede Virtual não é compatível com o Requisito HDInsight", normalmente indica um problema com a [configuração](../hdinsight-plan-virtual-network-deployment.md) da rede virtual para o seu cluster. Com base no resto da descrição do erro, siga as secções abaixo para resolver o seu problema.

## <a name="hostname-resolution-failed"></a>"Resolução de nome de anfitrião falhou"

### <a name="issue"></a>Problema

A descrição do erro contém "Resolução de nome de anfitrião falhou".

### <a name="cause"></a>Causa

Este erro aponta para um problema com a configuração personalizada de DNS. Os servidores DNS dentro de uma rede virtual podem transmitir consultas de DNS aos resolvers recursivos do Azure para resolver nomes de anfitriões dentro dessa rede virtual (ver [Resolução de Nomes em Redes Virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para mais detalhes). O acesso aos resolvers recursivos do Azure é fornecido através do IP virtual 168.63.129.16. Este IP só é acessível a partir dos VMs Azure. Portanto, não funcionará se estiver a usar um servidor OnPrem DNS, ou se o seu servidor DNS for um Azure VM, que não faz parte da rede virtual do cluster.

### <a name="resolution"></a>Resolução

1. SSH para o VM que faz parte do `hostname -f`cluster, e executar o comando . Isto devolverá o nome de domínio totalmente `<host_fqdn>` qualificado do hospedeiro (referido como nas instruções abaixo).

1. Em seguida, `nslookup <host_fqdn>` executar o `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net`comando (por exemplo, ). Se este comando resolver o nome num endereço IP, significa que o seu servidor DNS está a funcionar corretamente. Neste caso, levante um caso de suporte com o HDInsight, e vamos investigar o seu problema. No seu caso de apoio, inclua os passos de resolução de problemas que executou. Isto ajudar-nos-á a resolver o problema mais rapidamente.

1. Se o comando acima não devolver um `nslookup <host_fqdn> 168.63.129.16` endereço IP, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16`então corra (por exemplo, ). Se este comando for capaz de resolver o IP, significa que ou o seu servidor DNS não está a encaminhar a consulta para o DNS do Azure, ou não é um VM que faz parte da mesma rede virtual que o cluster.

1. Se não tiver um VM Azure que possa funcionar como um servidor DNS personalizado na rede virtual do cluster, então precisa adicionar este primeiro. Crie um VM na rede virtual, que será configurado como avançado do DNS.

1. Assim que tiver um VM implantado na sua rede virtual, configure as regras de encaminhamento do DNS neste VM. Envie todos os pedidos de resolução de nomes iDNS para 168.63.129.16 e o resto para o seu servidor DNS. [Aqui](../hdinsight-plan-virtual-network-deployment.md) está um exemplo desta configuração para um servidor DNS personalizado.

1. Adicione o endereço IP deste VM como primeira entrada dNS para a configuração DNS da rede virtual.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Falha na ligação à Conta de Armazenamento Azure"

### <a name="issue"></a>Problema

A descrição do erro contém "Falha na ligação à Conta de Armazenamento Azure" ou "Falha na ligação ao Azure SQL".

### <a name="cause"></a>Causa

O Azure Storage e o SQL não dispõem de endereços IP fixos, pelo que precisamos de permitir ligações de saída a todos os IPs para permitir o acesso a estes serviços. Os passos exatos de resolução dependem se criou um Grupo de Segurança de Rede (NSG) ou regras definidas pelo utilizador (UDR). Consulte a secção sobre controlo do tráfego de [rede com o HDInsight com grupos](../hdinsight-plan-virtual-network-deployment.md#hdinsight-ip) de segurança de rede e rotas definidas pelo utilizador para obter detalhes sobre estas configurações.

### <a name="resolution"></a>Resolução

* Se o seu cluster utilizar um Grupo de Segurança de [Rede (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Vá ao portal Azure e identifique o NSG que está associado à subrede onde o cluster está a ser implantado. Na secção de regras de segurança de **saída,** permitir o acesso de saída à Internet sem limitação (note que um número **prioritário** menor aqui significa maior prioridade). Além disso, na secção **de sub-redes,** confirme se este NSG é aplicado à sub-rede do cluster.

* Se o seu cluster utilizar uma [Rotas definidas pelo utilizador (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Vá ao portal Azure e identifique a tabela de rotas que está associada à subrede onde o cluster está a ser implantado. Assim que encontrar a tabela de rotas para a sub-rede, inspecione a secção de **rotas** nele.

    Se houver rotas definidas, certifique-se de que existem rotas para endereços IP para a região onde o cluster foi implantado, e o **NextHopType** para cada rota é **internet**. Deve haver uma rota definida para cada endereço IP exigido documentado no artigo acima referido.

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

Valide que 168.63.129.16 está na cadeia DNS personalizada. Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para os resolvers recursivos do Azure para resolver nomes de anfitriões dentro dessa rede virtual. Para mais informações, consulte [a Resolução de Nomes em Redes Virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server). O acesso aos resolvers recursivos do Azure é fornecido através do IP virtual 168.63.129.16.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para se ligar ao seu cluster. Editar o comando abaixo substituindo CLUSTERNAME pelo nome do seu cluster e, em seguida, introduzir o comando:

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

Se não viu o seu problema ou não consegue resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas do Azure através do [Apoio Comunitário de Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente, ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** a partir da barra de menus ou abra o centro de **suporte Ajuda +.** Para obter informações mais detalhadas, reveja [como criar um pedido de apoio azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte do Azure.](https://azure.microsoft.com/support/plans/)
