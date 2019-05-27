---
title: Configurar a restrição de tráfego de rede de saída para o Azure HDInsight clusters
description: Saiba como configurar a restrição de tráfego de rede de saída para o Azure HDInsight clusters.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/13/2019
ms.openlocfilehash: 44b6f099b5b17329976b9fec3c0ac38b5e394221
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978004"
---
# <a name="configure-outbound-network-traffic-restriction-for-azure-hdinsight-clusters-preview"></a>Configurar a restrição de tráfego de rede de saída para o Azure HDInsight clusters (pré-visualização)

Este artigo fornece os passos para proteger o tráfego de saída do seu cluster do HDInsight com o Firewall do Azure. Os passos abaixo partem do princípio de que está a configurar uma Firewall do Azure para um cluster existente. Se estiver implantando um novo cluster e protegido por uma firewall, crie primeiro o seu cluster do HDInsight e a sub-rede e, em seguida, siga os passos neste guia.

## <a name="background"></a>Segundo plano

Os clusters de HDInsight do Azure normalmente são implementados na sua própria rede virtual. O cluster tem dependências nos serviços fora da rede virtual que necessitam de acesso de rede a funcionar corretamente.

Há várias dependências que exigem o tráfego de entrada. Não é possível enviar o tráfego de entrada de gestão através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados [aqui](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Também pode criar regras de grupo de segurança de rede (NSG) com essas informações para proteger o tráfego de entrada para os clusters.

As dependências de tráfego de saída do HDInsight são quase que totalmente definidas com FQDNs, que não têm endereços IP estáticos por trás delas. A falta de endereços estáticos significa que grupos de segurança de rede (NSGs) não pode ser utilizados para bloquear o tráfego de saída de um cluster. Os endereços são alterados com frequência suficiente uma não é possível configurar regras com base na resolução de nome atual e usá-lo para configurar as regras do NSG.

A solução para proteger os endereços de saída é utilizar um dispositivo de firewall que pode controlar o tráfego de saída com base nos nomes de domínio. Firewall do Azure pode restringir o tráfego de HTTP e HTTPS de saída com base no FQDN do destino ou [FQDN etiquetas](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurar o Firewall do Azure com o HDInsight

Um resumo dos passos para bloquear a saída de seu HDInsight existente com o Firewall do Azure são:
1. Ative pontos finais de serviço.
1. Crie uma firewall.
1. Adicionar regras de aplicações para a firewall
1. Adicione regras de rede à firewall.
1. Crie uma tabela de encaminhamento.

### <a name="enable-service-endpoints"></a>Ativar pontos finais de serviço

Se pretender ignorar a firewall (por exemplo, para poupar no custo na transferência de dados), em seguida, pode ativar pontos finais de serviço para SQL e armazenamento na sua sub-rede do HDInsight. Quando tem pontos finais de serviço ativados para o Azure SQL, todas as dependências de SQL do Azure que tenha o seu cluster devem ser configuradas com pontos finais de serviço também.

Para ativar os pontos de extremidade de serviço correto, conclua os seguintes passos:

1. Inicie sessão no portal do Azure e selecione a rede virtual implementada no seu cluster do HDInsight.
1. Selecione **sub-redes** sob **definições**.
1. Selecione a sub-rede onde o cluster é implementado.
1. No ecrã para editar as definições de sub-rede, clique em **Microsoft. SQL** e/ou **Microsoft. Storage** partir os **pontos finais de serviço**  >   **Serviços** na caixa pendente.
1. Se estiver a utilizar um cluster do ESP, então deve também selecionar a **Microsoft. azureactivedirectory** ponto final de serviço.
1. Clique em **Guardar**.

### <a name="create-a-new-firewall-for-your-cluster"></a>Criar uma nova firewall para o seu cluster

1. Crie uma sub-rede designada **AzureFirewallSubnet** na rede virtual onde existe o seu cluster. 
1. Criar uma nova firewall **teste FW01** utilizando os passos no [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).
1. Selecione o novo firewall do portal do Azure. Clique em **regras** sob **definições** > **coleção de regras de aplicação** > **adicionar a coleção de regras de aplicação**.

    ![Título: Adicionar conjunto de regras de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

### <a name="configure-the-firewall-with-application-rules"></a>Configurar o firewall com regras de aplicações

Crie uma coleção de regras de aplicação que permite que o cluster enviar e receber comunicações importantes.

Selecione o novo firewall **teste FW01** do portal do Azure. Clique em **regras** sob **definições** > **coleção de regras de aplicação** > **adicionar a coleção de regras de aplicação**.

Sobre o **adicionar a coleção de regras de aplicação** ecrã, conclua os seguintes passos:

1. Introduza um **Name**, **prioridade**e clique em **permitir** do **ação** menu pendente.
1. Adicione as seguintes regras:
    1. Uma regra para permitir tráfego do HDInsight e o Windows Update:
        1. Na **etiquetas FQDN** secção, forneça um **nome**e defina **endereços de origem** para `*`.
        1. Selecione **HDInsight** e o **WindowsUpdate** partir o **FQDN etiquetas** menu pendente.
    1. Uma regra para permitir que a atividade de início de sessão do Windows:
        1. Na **destino FQDNs** secção, forneça um **nome**e defina **endereços de origem** para `*`.
        1. Introduza `https:443` sob **protocolo: porta** e `login.windows.net` sob **FQDNS de destino**.
    1. Uma regra para permitir a telemetria SQM:
        1. Na **destino FQDNs** secção, forneça um **nome**e defina **endereços de origem** para `*`.
        1. Introduza `https:443` sob **protocolo: porta** e `sqm.telemetry.microsoft.com` sob **FQDNS de destino**.
    1. Se o cluster está protegido por WASB e não estiver a utilizar os pontos finais de serviço acima, em seguida, adicione uma regra para WASB:
        1. Na **destino FQDNs** secção, forneça um **nome**e defina **endereços de origem** para `*`.
        1. Introduza `http` ou [https] dependendo se estiver a utilizar wasb: / / ou wasbs: / / em **protocolo: porta** e o url da conta de armazenamento sob **destino FQDNS**.
1. Clique em **Adicionar**.

![Título: Introduza os detalhes de coleção de regra de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configurar o firewall com regras de rede

Crie as regras de rede para configurar corretamente o seu cluster do HDInsight.

> [!Important]
> Pode escolher entre a utilização de etiquetas de serviço do SQL na firewall utilizar regras de rede, conforme descrito nesta secção, ou o SQL serviço ponto final de um descrito num [a secção sobre pontos finais de serviço](#enable-service-endpoints). Se optar por utilizar etiquetas SQL nas regras de rede, pode iniciar sessão e auditar o tráfego SQL. Com um ponto de final de serviço, terá a ignorar a firewall de tráfego SQL.

1. Selecione o novo firewall **teste FW01** do portal do Azure.
1. Clique em **regras** sob **definições** > **coleção de regras de rede** > **adicionar a coleção de regras de rede**.
1. Na **adicionar a coleção de regras de rede** ecrã, introduza um **nome**, **prioridade**e clique em **permitir** do **ação** menu pendente.
1. Crie as seguintes regras:
    1. Uma regra de rede que permite que o cluster para realizar a sincronização do relógio usando NTP.
        1. Na **regras** secção, forneça um **nome** e selecione **qualquer** do **protocolo** lista pendente.
        1. Definir **endereços de origem** e **endereços de destino** para `*`.
        1. Definir **portas de destino** para 123.
    1. Se estiver a utilizar o pacote de segurança da empresa (ESP), em seguida, adicione uma regra de rede que permite a comunicação com o AAD-DS para clusters do ESP.
        1. Determine os dois endereços IP para os controladores de domínio.
        1. Na próxima linha a **regras** secção, forneça um **nome** e selecione **qualquer** do **protocolo** lista pendente.
        1. Definir **endereços de origem** `*`.
        1. Introduza todos os endereços IP para os controladores de domínio no **endereços de destino** separados por vírgulas.
        1. Definir **portas de destino** para `*`.
    1. Se estiver a utilizar o armazenamento do Azure Data Lake, em seguida, pode adicionar uma regra de rede para resolver um problema SNI com ADLS Gen1 e Gen2. Esta opção irá encaminhar o tráfego à firewall que poderá resultar em custos de superiores para cargas de dados de grande dimensão, mas o tráfego será iniciada e auditada.
        1. Determine o endereço IP para a sua conta de armazenamento do Data Lake. Pode utilizar um comando do powershell como `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` para resolver o FQDN para um endereço IP.
        1. Na próxima linha a **regras** secção, forneça um **nome** e selecione **qualquer** do **protocolo** lista pendente.
        1. Definir **endereços de origem** `*`.
        1. Introduza o endereço IP da sua conta de armazenamento no **endereços de destino**.
        1. Definir **portas de destino** para `*`.
    1. Uma regra de rede para permitir a comunicação com o serviço de gestão da chave para o Windows ativação.
        1. Na próxima linha a **regras** secção, forneça um **nome** e selecione **qualquer** do **protocolo** lista pendente.
        1. Definir **endereços de origem** `*`.
        1. Definir **endereços de destino** para `*`.
        1. Definir **portas de destino** para `1688`.
    1. Se estiver a utilizar o Log Analytics, em seguida, crie uma regra de rede para permitir a comunicação com a sua área de trabalho do Log Analytics.
        1. Na próxima linha a **regras** secção, forneça um **nome** e selecione **qualquer** do **protocolo** lista pendente.
        1. Definir **endereços de origem** `*`.
        1. Definir **endereços de destino** para `*`.
        1. Definir **portas de destino** para `12000`.
    1. Configure uma etiqueta de serviço do SQL que lhe permite iniciar sessão e auditar o tráfego SQL.
        1. Na próxima linha a **regras** secção, forneça um **nome** e selecione **qualquer** do **protocolo** lista pendente.
        1. Definir **endereços de origem** `*`.
        1. Definir **endereços de destino** para `*`.
        1. Selecione **Sql** partir do **etiquetas de serviço** lista pendente.
        1. Definir **portas de destino** para `1433,11000-11999,14000-14999`.
1. Clique em **adicionar** para concluir a criação da sua coleção de regra de rede.

![Título: Introduza os detalhes de coleção de regra de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Crie uma tabela de rotas com as seguintes entradas:

1. Sete aborda desde [esta lista de endereços IP de gestão de HDInsight necessários](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) com um salto seguinte da **Internet**:
    1. Quatro endereços IP para todos os clusters em todas as regiões
    1. Dois endereços IP que são específicos para a região onde o cluster é criado
    1. Um endereço IP para o resolvedor recursivo do Azure
1. Uma rota de aplicação Virtual para 0.0.0.0/0 de endereço IP com o salto seguinte a ser o seu endereço IP privado do Firewall do Azure.

Por exemplo, para configurar a tabela de rotas para um cluster criado na região E.U.A. de "E.u.a. Central", utilize passos seguintes:

1. Inicie sessão no Portal do Azure.
1. Selecione a firewall do Azure **teste FW01**. Cópia a **endereço IP privado** listados a **descrição geral** página. Neste exemplo utilizaremos um **endereço do 10.1.1.4 de exemplo**
1. Crie uma nova tabela de rota.
1. Clique em **rotas** sob **definições**.
1. Clique em **adicionar** criar rotas para os endereços IP na tabela abaixo.

| Nome da rota | Prefixo de endereço | Tipo de salto seguinte | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | ND |
| 23.99.5.239 | 23.99.5.239/32 | Internet | ND |
| 168.61.48.131 | 168.61.48.131/32 | Internet | ND |
| 138.91.141.162 | 138.91.141.162/32 | Internet | ND |
| 13.67.223.215 | 13.67.223.215/32 | Internet | ND |
| 40.86.83.253 | 40.86.83.253/32 | Internet | ND |
| 168.63.129.16 | 168.63.129.16/32 | Internet | ND |
| 0.0.0.0 | 0.0.0.0/0 | Aplicação virtual | 10.1.1.4 |

![Título: Criar uma tabela de rotas](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-route-table.png)

Conclua a configuração da tabela de rota:

1. Atribuir a tabela de rotas que criou para a sub-rede do HDInsight ao clicar em **sub-redes** sob **definições** e, em seguida **associar**.
1. Sobre o **associar sub-rede** ecrã, selecione a rede virtual que o cluster foi criado em e o **AzureFirewallSubnet** que criou para utilização com a firewall.
1. Clique em **OK**.

![Título: Criar uma tabela de rotas](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-route-table-associate-subnet.png)

## <a name="edge-node-application-traffic"></a>Tráfego de aplicativo do nó de extremidade

Os passos acima permitirá que o cluster a funcionar sem problemas. Ainda tem de configurar as dependências para acomodar as suas aplicações personalizadas em execução em nós de extremidade, se aplicável.

Dependências de aplicações tem de ser identificadas e adicionadas para o Firewall do Azure ou a tabela de rotas.

As rotas têm de ser criadas para o tráfego de aplicativo evitar problemas de encaminhamento assimétrico.

Se seus aplicativos tenham outras dependências, terá de ser adicionado à Firewall do Azure. Crie regras de aplicações para permitir tráfego HTTP/HTTPS e regras de rede para todo o resto.

## <a name="logging"></a>Registo

Firewall do Azure pode enviar registos para alguns sistemas de armazenamento diferente. Para obter instruções sobre como configurar o registo para a firewall, siga os passos em [Tutorial: Monitorizar métricas e registos de Firewall do Azure](../firewall/tutorial-diagnostics.md).

Depois de concluir a configuração de registo, se estiver a dados de registo para o Log Analytics, pode ver o tráfego bloqueado com uma consulta, como o seguinte:

```
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrar o Firewall do Azure com os registos do Azure Monitor é útil ao primeiro obter uma aplicação a funcionar quando não estiver ciente de todas as dependências de aplicativo. Pode saber mais sobre os registos do Azure Monitor de [analisar dados de registo no Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

## <a name="configure-another-network-virtual-appliance"></a>Configure outra aplicação de rede virtual

>[!Important]
> As seguintes informações são **apenas** necessário se pretender configurar uma aplicação virtual de rede (NVA), que não seja a Firewall do Azure.

As instruções anteriores ajudar a configurar a Firewall do Azure para restringir o tráfego de saída do seu cluster do HDInsight. Firewall do Azure é automaticamente configurado para permitir o tráfego para muitos dos cenários comuns de importantes. Se quiser utilizar outra aplicação de rede virtual, terá de configurar manualmente vários recursos adicionais. Tenha em atenção, como o seguinte a configurar a sua aplicação de rede virtual:

* Serviços com capacidade de ponto final de serviço devem ser configurados com pontos finais de serviço.
* Dependências de endereço IP são para o tráfego de não-HTTP/S (tráfego TCP e UDP).
* Pontos finais HTTP/HTTPS de FQDN podem ser colocados no seu dispositivo da NVA.
* Pontos finais HTTP/HTTPS de caráter universal são dependências que podem variar com base num número de qualificadores.
* Atribua a tabela de rotas que criar para a sub-rede do HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Dependências com capacidade de ponto final de serviço

| **Endpoint** |
|---|
| Azure SQL |
| Storage do Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dependências de endereço IP

| **Endpoint** | **Detalhes** |
|---|---|
| \*:123 | Verificação de relógio NTP. O tráfego é verificado em vários pontos de extremidade na porta 123 |
| IPs publicado [aqui](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) | Estes são o serviço do HDInsight |
| AAD-DS IPs privados para o ESP clusters |
| \*: 16800 Windows para ativação do KMS |
| \*12000 para o Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Dependências de FQDN HTTP/HTTPS

>[!Important]
> A lista a seguir fornece apenas alguns dos mais importante FQDN. Pode obter a lista completa de FQDNs para configurar a NVA [neste ficheiro](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoint**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registo 1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                |
| ocsp.digicert.com:80                                                |

## <a name="next-steps"></a>Passos Seguintes

* [Arquitetura de rede virtual do Azure HDInsight](hdinsight-virtual-network-architecture.md)
