---
title: InvalidNetworkConfigurationErrCode error - Azure HDInsight
description: Várias razões para criações falhadas de cluster com InvalidNetconfigurationErrOrCode em Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 01/22/2020
ms.openlocfilehash: f30f92df505abeff108f8d1c503cb33162d2e409
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92533551"
---
# <a name="cluster-creation-fails-with-invalidnetworkconfigurationerrorcode-in-azure-hdinsight"></a>A criação de cluster falha com o InvalidNetworkConfigurationErrOrCode em Azure HDInsight

Este artigo descreve etapas de resolução de problemas e possíveis resoluções para problemas ao interagir com clusters Azure HDInsight.

Se vir código de erro `InvalidNetworkConfigurationErrorCode` com a descrição "A configuração da Rede Virtual não é compatível com o Requisito de Classificação HD", normalmente indica um problema com a [configuração da rede virtual](../hdinsight-plan-virtual-network-deployment.md) para o seu cluster. Com base no resto da descrição do erro, siga as secções abaixo para resolver o seu problema.

## <a name="hostname-resolution-failed"></a>"Resolução do Nome anfitrião falhou"

### <a name="issue"></a>Problema

A descrição do erro contém "Resolução do Nome anfitrião falhou".

### <a name="cause"></a>Causa

Este erro aponta para um problema com a configuração de DNS personalizada. Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para os resolveres recursivos do Azure para resolver os nomes de anfitriões dentro dessa rede virtual (ver [Resolução de Nome em Redes Virtuais](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) para mais detalhes). O acesso às correturas recursivas do Azure é fornecido através do IP virtual 168.63.129.16. Este IP só está acessível a partir dos VMs Azure. Portanto, não funcionará se estiver a utilizar um servidor DNS OnPrem, ou se o seu servidor DNS for um VM Azure, que não faz parte da rede virtual do cluster.

### <a name="resolution"></a>Resolução

1. Ssh no VM que faz parte do cluster, e executar o comando `hostname -f` . Isto devolverá o nome de domínio totalmente qualificado do anfitrião (referido como `<host_fqdn>` nas instruções abaixo).

1. Em seguida, executar o comando `nslookup <host_fqdn>` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net` ). Se este comando resolver o nome num endereço IP, significa que o seu servidor DNS está a funcionar corretamente. Neste caso, levante um caso de apoio com a HDInsight, e vamos investigar o seu problema. No seu caso de apoio, inclua os passos de resolução de problemas que executou. Isto ajudar-nos-á a resolver o problema mais rapidamente.

1. Se o comando acima não devolver um endereço IP, então corra `nslookup <host_fqdn> 168.63.129.16` (por exemplo, `nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net 168.63.129.16` ). Se este comando for capaz de resolver o IP, significa que ou o seu servidor DNS não está a encaminhar a consulta para o DNS do Azure, ou não é um VM que faz parte da mesma rede virtual que o cluster.

1. Se não tiver um VM Azure que possa funcionar como um servidor DNS personalizado na rede virtual do cluster, então precisa adicionar isto primeiro. Crie um VM na rede virtual, que será configurado como reencaminhador de DNS.

1. Uma vez implantado um VM na sua rede virtual, configuure as regras de encaminhamento de DNS neste VM. Reencamúva todos os pedidos de resolução de nomes iDNS para 168.63.129.16 e o resto para o seu servidor DNS. [Aqui](../hdinsight-plan-virtual-network-deployment.md) está um exemplo desta configuração para um servidor DNS personalizado.

1. Adicione o Endereço IP deste VM como primeira entrada DNS para a configuração DNS da Rede Virtual.

---

## <a name="failed-to-connect-to-azure-storage-account"></a>"Falhou na ligação à Conta de Armazenamento Azure"

### <a name="issue"></a>Problema

A descrição do erro contém "Falha na ligação à Conta de Armazenamento Azure" ou "Falha na ligação ao Azure SQL".

### <a name="cause"></a>Causa

O Azure Storage e o SQL não têm endereços IP fixos, por isso precisamos de permitir ligações de saída a todos os IPs para permitir o acesso a estes serviços. As medidas exatas de resolução dependem da criação de um Grupo de Segurança de Rede (NSG) ou User-Defined Regras (UDR). Consulte a secção de controlo do [tráfego de rede com HDInsight com grupos de segurança de rede e rotas definidas](../control-network-traffic.md) pelo utilizador para obter detalhes sobre estas configurações.

### <a name="resolution"></a>Resolução

* Se o seu cluster utilizar um [Grupo de Segurança de Rede (NSG)](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

    Vá ao portal Azure e identifique o NSG que está associado à sub-rede onde o cluster está a ser implantado. Na secção **de regras de segurança outbound,** permita o acesso de saída à Internet sem limitações (note que um número **de prioridade** menor aqui significa maior prioridade). Além disso, na secção **de sub-redes,** confirme se este NSG é aplicado na sub-rede do cluster.

* Se o seu cluster utilizar uma [Rota definida pelo Utilizador (UDR)](../../virtual-network/virtual-networks-udr-overview.md).

    Vá ao portal Azure e identifique a tabela de rotas que está associada à sub-rede onde o cluster está a ser implantado. Assim que encontrar a tabela de rotas para a sub-rede, inspecione a secção **de rotas** nele.

    Se houver rotas definidas, certifique-se de que existem rotas para endereços IP para a região onde o cluster foi implantado, e o **NextHopType** para cada rota é **Internet.** Deve haver uma rota definida para cada endereço IP necessário documentado no artigo acima mencionado.

---

## <a name="virtual-network-configuration-is-not-compatible-with-hdinsight-requirement"></a>"A configuração da rede virtual não é compatível com o requisito hdInsight"

### <a name="issue"></a>Problema

As descrições de erros contêm mensagens semelhantes:

```
ErrorCode: InvalidNetworkConfigurationErrorCode
ErrorDescription: Virtual Network configuration is not compatible with HDInsight Requirement. Error: 'Failed to connect to Azure Storage Account; Failed to connect to Azure SQL; HostName Resolution failed', Please follow https://go.microsoft.com/fwlink/?linkid=853974 to fix it.
```

### <a name="cause"></a>Causa

Provavelmente um problema com a configuração personalizada de DNS.

### <a name="resolution"></a>Resolução

Valide que 168.63.129.16 está na cadeia de DNS personalizada. Os servidores DNS dentro de uma rede virtual podem encaminhar consultas de DNS para as resoluções recursivas do Azure para resolver os nomes de anfitriões dentro dessa rede virtual. Para obter mais informações, consulte [a Resolução de Nomes em Redes Virtuais.](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) O acesso às correturas recursivas do Azure é fornecido através do IP virtual 168.63.129.16.

1. Utilize [o comando ssh](../hdinsight-hadoop-linux-use-ssh-unix.md) para ligar ao seu cluster. Edite o comando abaixo substituindo o CLUSTERNAME pelo nome do seu cluster e, em seguida, insira o comando:

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
Adicione 168.63.129.16 como o primeiro DNS personalizado para a rede virtual usando os passos descritos no [Plano uma rede virtual para Azure HDInsight](../hdinsight-plan-virtual-network-deployment.md). Estes passos só são aplicáveis se o seu servidor DNS personalizado for executado no Linux.

**Opção 2**  
Implemente um VM do servidor DNS para a rede virtual. Trata-se dos seguintes passos:

* Crie um VM na rede virtual, que será configurado como reencaminhador DENS (pode ser um Linux ou janelas VM).
* Configure as regras de encaminhamento de DNS sobre este VM (encaminhe todos os pedidos de resolução de nomes iDNS para 168.63.129.16 e os restantes para o seu servidor DNS).
* Adicione o Endereço IP deste VM como primeira entrada DNS para configuração DNS de Rede Virtual.

#### <a name="1686312916-is-in-the-list"></a>168.63.129.16 está na lista

Neste caso, por favor, crie um caso de apoio com a HDInsight, e vamos investigar o seu problema. Inclua o resultado dos comandos abaixo no seu caso de suporte. Isto ajudar-nos-á a investigar e a resolver o assunto mais rapidamente.

A partir de uma sessão de ssh no nó de cabeça, edite e, em seguida, executar o seguinte:

```bash
hostname -f
nslookup <headnode_fqdn> (e.g.nslookup hn1-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
dig @168.63.129.16 <headnode_fqdn> (e.g. dig @168.63.129.16 hn0-hditest.5h6lujo4xvoe1kprq3azvzmwsd.hx.internal.cloudapp.net)
```

---

### <a name="next-steps"></a>Passos seguintes

Se não viu o seu problema ou não conseguir resolver o seu problema, visite um dos seguintes canais para obter mais apoio:

* Obtenha respostas de especialistas da Azure através do [Apoio Comunitário Azure.](https://azure.microsoft.com/support/community/)

* Conecte-se com [@AzureSupport](https://twitter.com/azuresupport) - a conta oficial do Microsoft Azure para melhorar a experiência do cliente ligando a comunidade Azure aos recursos certos: respostas, suporte e especialistas.

* Se precisar de mais ajuda, pode submeter um pedido de apoio do [portal Azure.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecione **Suporte** na barra de menu ou abra o hub **de suporte Help +.** Para obter informações mais [detalhadas, reveja como criar um pedido de suporte Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). O acesso à Gestão de Subscrições e suporte à faturação está incluído na subscrição do Microsoft Azure, e o Suporte Técnico é fornecido através de um dos Planos de [Suporte Azure](https://azure.microsoft.com/support/plans/).