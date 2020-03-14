---
title: Configure restrição de tráfego de rede de saída - Azure HDInsight
description: Saiba como configurar a restrição de tráfego de rede de saída para os clusters Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 6e0c98cffef06fb6d6345fc2b23bbc22715909b4
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370190"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configure o tráfego de rede de saída para clusters Azure HDInsight usando firewall

Este artigo fornece os passos para que possa proteger o tráfego de saída do seu cluster HDInsight utilizando o Azure Firewall. Os passos abaixo assumem que está a configurar uma Firewall Azure para um cluster existente. Se estiver a implantar um novo cluster e atrás de uma firewall, crie primeiro o seu cluster HDInsight e sub-rede e siga os passos neste guia.

## <a name="background"></a>Segundo plano

Os clusters Azure HDInsight são normalmente implantados na sua própria rede virtual. O cluster tem dependências de serviços fora dessa rede virtual que exigem o acesso da rede para funcionar corretamente.

Há várias dependências que requerem tráfego de entrada. O tráfego de gestão de entrada não pode ser enviado através de um dispositivo de firewall. Os endereços de origem para este tráfego são conhecidos e são publicados [aqui.](hdinsight-management-ip-addresses.md) Também pode criar regras do Network Security Group (NSG) com esta informação para garantir o tráfego de entrada aos clusters.

As dependências de tráfego de saída HDInsight são quase totalmente definidas com FQDNs, que não têm endereços IP estáticos atrás deles. A falta de endereços estáticos significa que os Grupos de Segurança da Rede (NSGs) não podem ser usados para bloquear o tráfego de saída de um cluster. Os endereços mudam muitas vezes o suficiente para que não se possa estabelecer regras com base na resolução de nomeactual e usá-las para estabelecer regras de NSG.

A solução para assegurar endereços de saída é utilizar um dispositivo de firewall que possa controlar o tráfego de saída com base em nomes de domínio. O Azure Firewall pode restringir o tráfego HTTP e HTTPS de saída com base no FQDN do destino ou [das etiquetas FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurar firewall azure com HDInsight

Um resumo dos passos para travar a saída do seu HDInsight existente com firewall Azure são:

1. Criar uma sub-rede.
1. Criar uma firewall.
1. Adicione regras de aplicação à firewall
1. Adicione as regras da rede à firewall.
1. Crie uma tabela de encaminhamento.

### <a name="create-new-subnet"></a>Criar nova subrede

Crie uma subnet chamada **AzureFirewallSubnet** na rede virtual onde o seu cluster existe.

### <a name="create-a-new-firewall-for-your-cluster"></a>Crie uma nova firewall para o seu cluster

Crie uma firewall chamada **Test-FW01** utilizando os passos em **implementar a firewall** do [Tutorial: Implementar e configurar firewall Azure utilizando o portal Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configure a firewall com regras de aplicação

Crie uma coleção de regras de aplicação que permita ao cluster enviar e receber comunicações importantes.

1. Selecione a nova firewall **Test-FW01** do portal Azure.

1. Navegue para **Definições** > **Regras** > coleção de regras de **aplicação** > + Adicionar a recolha de regras de **aplicação**.

    ![Título: Adicionar coleção de regras de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. No ecrã de recolha de **regras da aplicação Add,** forneça as seguintes informações:

    **Secção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwAppRule|
    |Prioridade|200|
    |Ação|Permitir|

    **Seção de etiquetas FQDN**

    | Nome | Endereço de origem | Etiqueta FQDN | Notas |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Obrigatório para os serviços de HDI |

    **Secção de FQDNs alvo**

    | Nome | Endereços de origem | Protocolo:Porto | FQDNS alvo | Notas |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Permite atividade de login do Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Permite atividade de login do Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | Substitua `storage_account_name` pelo nome da sua conta de armazenamento. Se o seu cluster for apoiado pelo WASB, adicione uma regra para WASB. Para utilizar apenas ligações https, certifique-se de que [a "transferência segura necessária"](../storage/common/storage-require-secure-transfer.md) está ativada na conta de armazenamento. |

   ![Título: Introduzir detalhes da recolha da regra da aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selecione **Adicionar**.

### <a name="configure-the-firewall-with-network-rules"></a>Configure a firewall com regras de rede

Crie as regras de rede para configurar corretamente o seu cluster HDInsight.

1. Continuando a partir do passo anterior, navegue para a recolha de **regras da Rede** > + Adicionar coleção de regras de **rede.**

1. No ecrã de recolha de **regras da rede Add,** forneça as seguintes informações:

    **Secção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwnetRule|
    |Prioridade|200|
    |Ação|Permitir|

    **Secção de endereços IP**

    | Nome | Protocolo | Endereços de origem | Endereços de destino | Portas de destino | Notas |
    | --- | --- | --- | --- | --- | --- |
    | Rule_1 | UDP | * | * | 123 | Serviço de tempo |
    | Rule_2 | Qualquer | * | DC_IP_Address_1, DC_IP_Address_2 | * | Se estiver a utilizar o Enterprise Security Package (ESP), adicione uma regra de rede na secção endereços IP que permite a comunicação com clusters AAD-DS para clusters ESP. Pode encontrar os endereços IP dos controladores de domínio na secção AAD-DS no portal |
    | Rule_3 | TCP | * | Endereço IP da sua conta de armazenamento de data lake | * | Se estiver a utilizar o Armazenamento de Lagos De Dados Azure, pode adicionar uma regra de rede na secção endereços IP para resolver um problema sni com ADLS Gen1 e Gen2. Esta opção irá encaminhar o tráfego para firewall, o que pode resultar em custos mais elevados para grandes cargas de dados, mas o tráfego será registado e auditável em registos de firewall. Determine o endereço IP da sua conta de Armazenamento de Data Lake. Pode utilizar um comando powershell, como `[System.Net.DNS]::GetHostAddresses("STORAGEACCOUNTNAME.blob.core.windows.net")` para resolver o FQDN num endereço IP.|
    | Rule_4 | TCP | * | * | 12000 | (Opcional) Se estiver a utilizar o Log Analytics, crie uma regra de rede na secção endereços IP para permitir a comunicação com o seu espaço de trabalho log Analytics. |

    **Secção etiquetas de serviço**

    | Nome | Protocolo | Endereços de Origem | Etiquetas de serviço | Portos de Destino | Notas |
    | --- | --- | --- | --- | --- | --- |
    | Rule_7 | TCP | * | SQL | 1433 | Configure uma regra de rede na secção Etiquetas de Serviço para SQL que lhe permitirá registar e auditar o tráfego SQL, a menos que tenha configurado pontos finais de serviço para o Servidor SQL na subnet HDInsight, que irá contornar a firewall. |

   ![Título: Introduzir a recolha de regras de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selecione **Adicionar**.

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Criar uma tabela de rotas com as seguintes entradas:

* Todos os endereços IP dos serviços de [saúde e gestão: Todas as regiões](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) com um próximo tipo de **Internet**hop.

* Dois endereços IP para a região onde o cluster é criado a partir de serviços de [saúde e gestão: regiões específicas](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-specific-regions) com um próximo tipo de **Internet**de lúpulo.

* Uma rota de aparelho virtual para endereço IP 0.0.0.0/0 com o próximo salto sendo o seu endereço IP privado Azure Firewall.

Por exemplo, para configurar a tabela de rotas para um cluster criado na região dos EUA de "Leste dos EUA", utilize os seguintes passos:

1. Selecione o seu firewall Azure **Test-FW01**. Copie o **endereço IP privado** listado na página **'Overview'.** Para este exemplo, usaremos um **endereço de amostra de 10.0.2.4**.

1. Em seguida, navegue para **todos os serviços** > **mesas** de rota > de **rede** e **crie tabela de rotas.**

1. A partir da sua nova rota, navegue até **Definições** > **Rotas** >  **+ Adicionar**. Adicione as seguintes rotas:

| Nome da rota | Prefixo de endereço | Tipo de salto seguinte | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | ND |
| 23.99.5.239 | 23.99.5.239/32 | Internet | ND |
| 168.61.48.131 | 168.61.48.131/32 | Internet | ND |
| 138.91.141.162 | 138.91.141.162/32 | Internet | ND |
| 13.82.225.233 | 13.82.225.233/32 | Internet | ND |
| 40.71.175.99 | 40.71.175.99/32 | Internet | ND |
| 0.0.0.0 | 0.0.0.0/0 | Aplicação virtual | 10.0.2.4 |

Complete a configuração da tabela de rotas:

1. Atribuir a tabela de rotas que criou à sua sub-rede HDInsight selecionando **Subredes** em **Definições**.

1. Selecione **+ Associado**.

1. No ecrã de **sub-rede Associado,** selecione a rede virtual em que o seu cluster foi criado e a **Subnet** utilizada para o seu cluster HDInsight.

1. Selecione **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Tráfego de aplicações com nó de borda ou personalizado

Os passos acima permitirão que o cluster funcione sem problemas. Ainda precisa de configurar dependências para acomodar as suas aplicações personalizadas em execução nos nós de borda, se aplicável.

As dependências da aplicação devem ser identificadas e adicionadas à Firewall Azure ou à tabela de rotas.

Devem ser criadas rotas para o tráfego de aplicações para evitar problemas de encaminhamento assimétricos.

Se as suas aplicações tiverem outras dependências, elas precisam de ser adicionadas à sua Firewall Azure. Criar regras de Aplicação para permitir regras de tráfego http/HTTPS e regras de rede para tudo o resto.

## <a name="logging-and-scale"></a>Exploração madeireira e escala

A Firewall Azure pode enviar registos para alguns sistemas de armazenamento diferentes. Para obter instruções sobre a configuração do registo da sua firewall, siga os passos no [Tutorial: Monitor Azure Firewall registos e métricas](../firewall/tutorial-diagnostics.md).

Depois de concluída a configuração do registo, se estiver a registar dados para o Log Analytics, pode ver o tráfego bloqueado com uma consulta como a seguinte:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

Integrar o seu Firewall Azure com registos Do Monitor Azure é útil quando se consegue obter uma aplicação a funcionar quando não se sabe de todas as dependências da aplicação. Pode saber mais sobre os registos do Monitor Azure a partir de [dados de log da Analyze no Monitor Azure](../azure-monitor/log-query/log-query-overview.md)

Para conhecer os limites de escala do Firewall Azure e solicitar aumentos, consulte [este](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento ou consulte as [FAQs](../firewall/firewall-faq.md).

## <a name="access-to-the-cluster"></a>Acesso ao cluster

Depois de ter a firewall configurada com sucesso, pode utilizar o ponto final interno (`https://CLUSTERNAME-int.azurehdinsight.net`) para aceder ao Ambari a partir de dentro da rede virtual.

Para utilizar o ponto final público (`https://CLUSTERNAME.azurehdinsight.net`) ou o ponto final ssh (`CLUSTERNAME-ssh.azurehdinsight.net`), certifique-se de que tem as rotas certas na tabela de rotas e as regras de NSG para evitar a questão de encaminhamento assimétrico [aqui](../firewall/integrate-lb.md)explicado. Especificamente neste caso, você precisa permitir o endereço IP do cliente nas regras NSG de entrada e também adicioná-lo à tabela de rota definida pelo utilizador com o próximo conjunto de lúpulo como `internet`. Se isto não estiver configurado corretamente, verá um erro de tempo.

## <a name="configure-another-network-virtual-appliance"></a>Configure outro aparelho virtual de rede

> [!Important]
> As seguintes informações **só** são necessárias se pretender configurar um aparelho virtual de rede (NVA) que não seja o Firewall Azure.

As instruções anteriores ajudam-no a configurar o Firewall Azure para restringir o tráfego de saída do seu cluster HDInsight. O Azure Firewall é configurado automaticamente para permitir o tráfego para muitos dos cenários importantes comuns. Se pretender utilizar outro aparelho virtual de rede, terá de configurar manualmente uma série de funcionalidades adicionais. Tenha em mente o seguinte, pois configura o seu aparelho virtual de rede:

* Os serviços de endpoint de serviço devem ser configurados com pontos finais de serviço.
* As dependências de endereçoip são para tráfego não HTTP/S (tanto o tráfego TCP como uDP).
* Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo NVA.
* Os pontos finais wildcard HTTP/HTTPS são dependências que podem variar com base numa série de qualificações.
* Atribua a tabela de rotas que cria à sua sub-rede HDInsight.

### <a name="service-endpoint-capable-dependencies"></a>Dependências de ponto final de serviço

| **Ponto final** |
|---|
| SQL do Azure |
| Storage do Azure |
| Azure Active Directory |

#### <a name="ip-address-dependencies"></a>Dependências de endereços IP

| **Ponto final** | **Detalhes** |
|---|---|
| \*:123 | Verificação do relógio NTP. O tráfego é verificado em vários pontos finais no porto 123 |
| IPs [publicados aqui](hdinsight-management-ip-addresses.md) | Estes são o serviço HDInsight |
| IPs privados AAD-DS para clusters ESP |
| \*:16800 para ativação do Windows KMS |
| \*12000 para Log Analytics |

#### <a name="fqdn-httphttps-dependencies"></a>Dependências fQDN HTTP/HTTPS

> [!Important]
> A lista abaixo dá apenas alguns dos FQDNs mais importantes. Pode obter a lista completa de FQDNs para configurar o seu NVA [neste ficheiro](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Ponto final**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Passos seguintes

* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
