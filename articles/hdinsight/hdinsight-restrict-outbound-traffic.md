---
title: Configurar a restrição de tráfego de rede de saída para o Azure HDInsight clusters
description: Saiba como configurar a restrição de tráfego de rede de saída para o Azure HDInsight clusters.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/30/2019
ms.openlocfilehash: 542813e0f82a1a52142a2b82bea3fdb101fdec28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077165"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall-preview"></a>Configurar o tráfego de rede de saída para clusters do HDInsight do Azure através do Firewall (pré-visualização)

Este artigo fornece os passos para proteger o tráfego de saída do seu cluster do HDInsight com o Firewall do Azure. Os passos abaixo partem do princípio de que está a configurar uma Firewall do Azure para um cluster existente. Se estiver implantando um novo cluster e protegido por uma firewall, crie primeiro o seu cluster do HDInsight e a sub-rede e, em seguida, siga os passos neste guia.

## <a name="background"></a>Segundo plano

Os clusters de HDInsight do Azure normalmente são implementados na sua própria rede virtual. O cluster tem dependências nos serviços fora da rede virtual que necessitam de acesso de rede a funcionar corretamente.

Há várias dependências que exigem o tráfego de entrada. Não é possível enviar o tráfego de entrada de gestão através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados [aqui](hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip). Também pode criar regras de grupo de segurança de rede (NSG) com essas informações para proteger o tráfego de entrada para os clusters.

As dependências de tráfego de saída do HDInsight são quase que totalmente definidas com FQDNs, que não têm endereços IP estáticos por trás delas. A falta de endereços estáticos significa que grupos de segurança de rede (NSGs) não pode ser utilizados para bloquear o tráfego de saída de um cluster. Os endereços são alterados com frequência suficiente uma não é possível configurar regras com base na resolução de nome atual e usá-lo para configurar as regras do NSG.

A solução para proteger os endereços de saída é utilizar um dispositivo de firewall que pode controlar o tráfego de saída com base nos nomes de domínio. Firewall do Azure pode restringir o tráfego de HTTP e HTTPS de saída com base no FQDN do destino ou [FQDN etiquetas](https://docs.microsoft.com/azure/firewall/fqdn-tags).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurar o Firewall do Azure com o HDInsight

Um resumo dos passos para bloquear a saída de seu HDInsight existente com o Firewall do Azure são:
1. Crie uma firewall.
1. Adicionar regras de aplicações para a firewall
1. Adicione regras de rede à firewall.
1. Crie uma tabela de encaminhamento.

### <a name="create-a-new-firewall-for-your-cluster"></a>Criar uma nova firewall para o seu cluster

1. Crie uma sub-rede designada **AzureFirewallSubnet** na rede virtual onde existe o seu cluster. 
1. Criar uma nova firewall **teste FW01** utilizando os passos no [Tutorial: Implementar e configurar a Firewall do Azure no portal do Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configurar o firewall com regras de aplicações

Crie uma coleção de regras de aplicação que permite que o cluster enviar e receber comunicações importantes.

Selecione o novo firewall **teste FW01** do portal do Azure. Clique em **regras** sob **definições** > **coleção de regras de aplicação** > **adicionar a coleção de regras de aplicação**.

![Título: Adicionar a coleção de regras de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

Sobre o **adicionar a coleção de regras de aplicação** ecrã, conclua os seguintes passos:

1. Introduza um **Name**, **prioridade**e clique em **permitir** do **ação** menu suspenso e introduza as seguintes regras no **Secção de etiquetas de FQDN** :

   | **Nome** | **Endereço de origem** | **Etiqueta do FQDN** | **Notas** |
   | --- | --- | --- | --- |
   | Rule_1 | * | HDInsight e WindowsUpdate | Necessária para os serviços HDI |

1. Adicione as seguintes regras para o **destino FQDNs secção** :

   | **Nome** | **Endereço de origem** | **Porta de protocolo:** | **Destino FQDN** | **Notas** |
   | --- | --- | --- | --- | --- |
   | Rule_2 | * | https:443 | login.windows.net | Permite que a atividade de início de sessão do Windows |
   | Rule_3 | * | https:443,http:80 | <storage_account_name.blob.core.windows.net> | Se o cluster é apoiado pela WASB, em seguida, adicione uma regra para WASB. Para utilizar https apenas Certifique-se de ligações ["transferência segura necessária"](https://docs.microsoft.com/azure/storage/common/storage-require-secure-transfer) está ativada na conta de armazenamento. |

1. Clique em **Adicionar**.

   ![Título: Introduza os detalhes de coleção de regra de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

### <a name="configure-the-firewall-with-network-rules"></a>Configurar o firewall com regras de rede

Crie as regras de rede para configurar corretamente o seu cluster do HDInsight.

1. Selecione o novo firewall **teste FW01** do portal do Azure.
1. Clique em **regras** sob **definições** > **coleção de regras de rede** > **adicionar a coleção de regras de rede**.
1. Na **adicionar a coleção de regras de rede** ecrã, introduza um **nome**, **prioridade**e clique em **permitir** do **ação** menu pendente.
1. Criar as regras seguintes no **endereços IP** secção:

   | **Nome** | **Protocolo** | **Endereço de origem** | **Endereço de destino** | **Porta de destino** | **Notas** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_1 | UDP | * | * | `123` | Serviço de hora |
   | Rule_2 | Qualquer | * | DC_IP_Address_1, DC_IP_Address_2 | `*` | Se estiver a utilizar o pacote de segurança da empresa (ESP), em seguida, adicione uma regra de rede na secção de endereços IP que permite a comunicação com o AAD-DS para clusters do ESP. Pode encontrar os endereços IP dos controladores de domínio na secção de AAD-DS no portal | 
   | Rule_3 | TCP | * | Endereço IP da sua conta de armazenamento do Data Lake | `*` | Se estiver a utilizar o armazenamento do Azure Data Lake, pode adicionar uma regra de rede na secção de endereços IP para resolver um problema SNI com ADLS Gen1 e Gen2. Esta opção irá encaminhar o tráfego à firewall que poderá resultar em custos de superiores para cargas de dados de grande dimensão, mas o tráfego será iniciada e auditada nos registos de firewall. Determine o endereço IP para a sua conta de armazenamento do Data Lake. Pode utilizar um comando do powershell como `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` para resolver o FQDN para um endereço IP.|
   | Rule_4 | TCP | * | * | `12000` | (Opcional) Se estiver a utilizar o Log Analytics, em seguida, crie uma regra de rede na secção de endereços IP para permitir a comunicação com a sua área de trabalho do Log Analytics. |

1. Criar as regras seguintes no **etiquetas de serviço** secção:

   | **Nome** | **Protocolo** | **Endereço de origem** | **Etiquetas de serviço** | **Porta de destino** | **Notas** |
   | --- | --- | --- | --- | --- | --- |
   | Rule_7 | TCP | * | * | `1433,11000-11999,14000-14999` | Configure uma regra de rede na secção de etiquetas de serviço para o SQL que lhe permite iniciar sessão e auditar o tráfego SQL, a menos que os pontos finais de serviço que configurou para o SQL Server na sub-rede do HDInsight que irá ignorar a firewall. |

1. Clique em **adicionar** para concluir a criação da sua coleção de regra de rede.

   ![Título: Introduza os detalhes de coleção de regra de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Crie uma tabela de rotas com as seguintes entradas:

1. Seis aborda desde [esta lista de endereços IP de gestão de HDInsight necessários](../hdinsight/hdinsight-extend-hadoop-virtual-network.md#hdinsight-ip) com um salto seguinte da **Internet**:
    1. Quatro endereços IP para todos os clusters em todas as regiões
    1. Dois endereços IP que são específicos para a região onde o cluster é criado
1. Uma rota de aplicação Virtual para 0.0.0.0/0 de endereço IP com o salto seguinte a ser o seu endereço IP privado do Firewall do Azure.

Por exemplo, para configurar a tabela de rotas para um cluster criado na região E.U.A. de "E.u.a. Central", utilize passos seguintes:

1. Inicie sessão no Portal do Azure.
1. Selecione a firewall do Azure **teste FW01**. Cópia a **endereço IP privado** listados a **descrição geral** página. Neste exemplo utilizaremos um **endereço do 10.1.1.4 de exemplo**
1. Crie uma nova tabela de rota.
1. Clique em **rotas** sob **definições**.
1. Clique em **adicionar** criar rotas para os endereços IP na tabela abaixo.

| Nome da rota | Prefixo de endereço | Tipo de salto seguinte | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | N/D |
| 23.99.5.239 | 23.99.5.239/32 | Internet | N/D |
| 168.61.48.131 | 168.61.48.131/32 | Internet | N/D |
| 138.91.141.162 | 138.91.141.162/32 | Internet | N/D |
| 13.67.223.215 | 13.67.223.215/32 | Internet | N/D |
| 40.86.83.253 | 40.86.83.253/32 | Internet | N/D |
| 0.0.0.0 | 0.0.0.0/0 | Aplicação virtual | 10.1.1.4 |

Conclua a configuração da tabela de rota:

1. Atribuir a tabela de rotas que criou para a sub-rede do HDInsight ao clicar em **sub-redes** sob **definições** e, em seguida **associar**.
1. No **associar sub-rede** ecrã, selecione a rede virtual criada no seu cluster e o **sub-rede do HDInsight** que utilizou para o seu cluster do HDInsight.
1. Clique em **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Nó de extremidade ou tráfego de aplicativo personalizada

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

## <a name="access-to-the-cluster"></a>Acesso ao cluster
Depois de ter a configuração de firewall com êxito, pode utilizar o ponto final interno (`https://<clustername>-int.azurehdinsight.net`) para acessar o Ambari de dentro da VNET. Para utilizar o ponto final público (`https://<clustername>.azurehdinsight.net`) ou ssh ponto final (`<clustername>-ssh.azurehdinsight.net`), certifique-se de que tem as rotas direita na tabela de rotas e regras NSG de configuração para evitar o problema de encaminhamento de assymetric explicado [aqui](https://docs.microsoft.com/azure/firewall/integrate-lb).

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
| SQL do Azure |
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
