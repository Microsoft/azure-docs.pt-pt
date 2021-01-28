---
title: Configure restrição de tráfego de rede de saída - Azure HDInsight
description: Saiba como configurar a restrição de tráfego de rede de saída para clusters Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/17/2020
ms.openlocfilehash: 79e3349f009f71c5cd387a7c7265ad4904f2a40d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932131"
---
# <a name="configure-outbound-network-traffic-for-azure-hdinsight-clusters-using-firewall"></a>Configure o tráfego de rede de saída para clusters Azure HDInsight usando firewall

Este artigo fornece os passos para que você proteja o tráfego de saída do seu cluster HDInsight usando Azure Firewall. Os passos abaixo assumem que está a configurar uma Firewall Azure para um cluster existente. Se estiver a implantar um novo cluster atrás de uma firewall, crie primeiro o seu cluster HDInsight e sub-rede. Em seguida, siga os passos neste guia.

## <a name="background"></a>Fundo

Os clusters HDInsight são normalmente implantados numa rede virtual. O cluster tem dependências de serviços fora daquela rede virtual.

O tráfego de gestão de entrada não pode ser enviado através de uma firewall. Pode utilizar etiquetas de serviço NSG para o tráfego de entrada, conforme documentado [aqui.](./hdinsight-service-tags.md) 

As dependências de tráfego de saída HDInsight são quase inteiramente definidas com FQDNs. Que não têm endereços IP estáticos por trás deles. A falta de endereços estáticos significa que os Grupos de Segurança da Rede (NSGs) não podem bloquear o tráfego de saída de um cluster. Os endereços IP mudam muitas vezes o suficiente para não se poder estabelecer regras com base na resolução e utilização do nome atual.

Proteja os endereços de saída com uma firewall que possa controlar o tráfego de saída com base em FQDNs. O Azure Firewall restringe o tráfego de saída com base no FQDN do destino ou [tags FQDN](../firewall/fqdn-tags.md).

## <a name="configuring-azure-firewall-with-hdinsight"></a>Configurar firewall Azure com HDInsight

Um resumo dos passos para bloquear a saída do seu HDInsight existente com a Azure Firewall são:

1. Criar uma sub-rede.
1. Criar uma firewall.
1. Adicione regras de aplicação à firewall
1. Adicione regras de rede à firewall.
1. Crie uma mesa de encaminhamento.

### <a name="create-new-subnet"></a>Criar nova sub-rede

Crie uma sub-rede chamada **AzureFirewallSubnet** na rede virtual onde o seu cluster existe.

### <a name="create-a-new-firewall-for-your-cluster"></a>Crie uma nova firewall para o seu cluster

Criar uma firewall chamada **Test-FW01** utilizando os passos em **Implementar a firewall a** partir de [Tutorial: Implementar e configurar a Firewall Azure utilizando o portal Azure](../firewall/tutorial-firewall-deploy-portal.md#deploy-the-firewall).

### <a name="configure-the-firewall-with-application-rules"></a>Configure a firewall com regras de aplicação

Crie uma coleção de regras de aplicação que permita ao cluster enviar e receber comunicações importantes.

1. Selecione a nova firewall **Test-FW01** do portal Azure.

1. Navegar para **Definições**  >  **Regras**  >  **Regras Recolha de regras de aplicação**  >  **+ Adicionar coleção de regras de aplicação**.

    ![Denominação: Adicionar a recolha de regras de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection.png)

1. No ecrã de **recolha de regras de aplicação Adicionar,** forneça as seguintes informações:

    **Secção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwAppRule|
    |Prioridade|200|
    |Ação|Permitir|

    **Secção de tags FQDN**

    | Name | Endereço de origem | Tag FQDN | Notas |
    | --- | --- | --- | --- |
    | Rule_1 | * | WindowsUpdate e HDInsight | Obrigatório para os serviços de HDI |

    **Seção FQDNs alvo**

    | Name | Endereços de origem | Protocolo:Porta | Alvo FQDNS | Notas |
    | --- | --- | --- | --- | --- |
    | Rule_2 | * | https:443 | login.windows.net | Permite atividade de login do Windows |
    | Rule_3 | * | https:443 | login.microsoftonline.com | Permite atividade de login do Windows |
    | Rule_4 | * | https:443,http:80 | storage_account_name.blob.core.windows.net | `storage_account_name`Substitua-o pelo nome da sua conta de armazenamento real. Para utilizar apenas as ligações https, certifique-se de que [a "transferência segura necessária"](../storage/common/storage-require-secure-transfer.md) está ativada na conta de armazenamento. Se estiver a utilizar o ponto final privado para aceder às contas de armazenamento, este passo não é necessário e o tráfego de armazenamento não é encaminhado para a firewall.|

   ![Denominação: Introduzir detalhes da recolha de regras de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-app-rule-collection-details.png)

1. Selecione **Adicionar**.

### <a name="configure-the-firewall-with-network-rules"></a>Configure a firewall com regras de rede

Crie as regras de rede para configurar corretamente o seu cluster HDInsight.

1. Continuando a partir do passo anterior, navegue para a **coleção de regras da rede**+ Adicione a recolha de  >  **regras de rede**.

1. No ecrã de **recolha de regras de rede Add,** forneça as seguintes informações:

    **Secção superior**

    | Propriedade|  Valor|
    |---|---|
    |Nome| FwNetrule|
    |Prioridade|200|
    |Ação|Permitir|

    **Secção de Etiquetas de Serviço**

    | Name | Protocolo | Endereços de Origem | Etiquetas de Serviço | Portos de Destino | Notas |
    | --- | --- | --- | --- | --- | --- |
    | Rule_5 | TCP | * | SQL | 1433 | Se estiver a utilizar os servidores sql predefinidos fornecidos pela HDInsight, configuure uma regra de rede na secção Tags de Serviço para SQL que lhe permitirá registar e auditar o tráfego DE SQL. A menos que tenha configurado pontos de final de serviço para o SQL Server na sub-rede HDInsight, que irá contornar a firewall. Se estiver a utilizar o servidor SQL personalizado para as metástases Ambari, Oozie, Ranger e Hive, então só precisa de permitir o tráfego para os seus próprios Servidores SQL personalizados.|
    | Rule_6 | TCP | * | Azure Monitor | * | (opcional) Os clientes que pretendam utilizar a função de escala automática devem adicionar esta regra. |
    
   ![Denominação: Introduzir a recolha da regra de aplicação](./media/hdinsight-restrict-outbound-traffic/hdinsight-restrict-outbound-traffic-add-network-rule-collection.png)

1. Selecione **Adicionar**.

### <a name="create-and-configure-a-route-table"></a>Criar e configurar uma tabela de rotas

Criar uma tabela de rotas com as seguintes entradas:

* Todos os endereços IP dos serviços de [saúde e gestão](../hdinsight/hdinsight-management-ip-addresses.md#health-and-management-services-all-regions) com um próximo tipo de **Internet** hop. Deve incluir 4 IPs das regiões genéricas, bem como 2 IPs para a sua região específica. Esta regra só é necessária se o ResourceProviderConnection estiver definido para *Entrada*. Se o ResourceProviderConnection estiver definido para *Outbound,* estes IPs não são necessários na UDR. 

* Uma rota de Aparelho Virtual para endereço IP 0.0.0.0/0 com o próximo lúpulo sendo o seu endereço IP privado Azure Firewall.

Por exemplo, para configurar a tabela de rotas para um cluster criado na região dos EUA de "Leste dos EUA", use os seguintes passos:

1. Selecione o seu Teste Azure firewall **Test-FW01**. Copie o **endereço IP privado** listado na página **'Vista Geral'.** Para este exemplo, usaremos um **endereço de amostra de 10.0.2.4**.

1. Em seguida, navegue para **todas as tabelas**  >  **de rota de networking de**  >  **serviços** e crie tabela de **rotas.**

1. A partir da sua nova rota, navegue para  >  **Definições Rotas**  >  **+ Adicionar**. Adicione as seguintes rotas:

| Nome da rota | Prefixo de endereço | Tipo de salto seguinte | Endereço do próximo salto |
|---|---|---|---|
| 168.61.49.99 | 168.61.49.99/32 | Internet | ND |
| 23.99.5.239 | 23.99.5.239/32 | Internet | ND |
| 168.61.48.131 | 168.61.48.131/32 | Internet | ND |
| 138.91.141.162 | 138.91.141.162/32 | Internet | ND |
| 13.82.225.233 | 13.82.225.233/32 | Internet | ND |
| 40.71.175.99 | 40.71.175.99/32 | Internet | ND |
| 0.0.0.0 | 0.0.0.0/0 | Aplicação virtual | 10.0.2.4 |

Preencha a configuração da tabela de rotas:

1. Atribua a tabela de rotas criada para a sua sub-rede HDInsight selecionando **sub-redes** em **Definições**.

1. Selecione **+ Associado**.

1. No ecrã da **sub-rede Associate,** selecione a rede virtual em que o seu cluster foi criado. E a **sub-rede** que usaste para o teu cluster HDInsight.

1. Selecione **OK**.

## <a name="edge-node-or-custom-application-traffic"></a>Tráfego de aplicações de nó de borda ou personalizado

Os passos acima permitirão que o cluster funcione sem problemas. Você ainda precisa configurar dependências para acomodar suas aplicações personalizadas que estão correndo nos nós de borda, se aplicável.

As dependências da aplicação devem ser identificadas e adicionadas à Firewall Azure ou à tabela de rotas.

Devem ser criadas vias para o tráfego de aplicações, além de problemas de encaminhamento assimétricos.

Se as suas aplicações tiverem outras dependências, elas têm de ser adicionadas ao seu Azure Firewall. Crie regras de aplicação para permitir o tráfego HTTP/HTTPS e as regras da Rede para tudo o resto.

## <a name="logging-and-scale"></a>Registo e escala

O Azure Firewall pode enviar registos para alguns sistemas de armazenamento diferentes. Para obter instruções sobre a configuração do registo de registo para a sua firewall, siga os passos em [Tutorial: Monitor Azure Firewall registos e métricas](../firewall/firewall-diagnostics.md).

Uma vez concluída a configuração de registo, se estiver a utilizar o Log Analytics, pode visualizar o tráfego bloqueado com uma consulta como:

```Kusto
AzureDiagnostics | where msg_s contains "Deny" | where TimeGenerated >= ago(1h)
```

A integração do Azure Firewall com os registos do Azure Monitor é útil quando se inicia uma aplicação a funcionar. Especialmente quando não se sabe de todas as dependências da aplicação. Pode saber mais sobre os registos do Azure Monitor a partir de dados de [registo de análise no Azure Monitor](../azure-monitor/log-query/log-query-overview.md)

Para conhecer os limites de escala do Azure Firewall e solicitar aumentos, consulte [este](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-firewall-limits) documento ou consulte as [FAQs](../firewall/firewall-faq.yml).

## <a name="access-to-the-cluster"></a>Acesso ao cluster

Depois de ter a firewall configurada com sucesso, pode utilizar o ponto final interno `https://CLUSTERNAME-int.azurehdinsight.net` para aceder ao Ambari a partir de dentro da rede virtual.

Para utilizar o ponto final público ( `https://CLUSTERNAME.azurehdinsight.net` ) ou ponto final ssh ( ), `CLUSTERNAME-ssh.azurehdinsight.net` certifique-se de que tem as rotas certas na tabela de rotas e regras NSG para evitar a questão do encaminhamento assimétrico [aqui](../firewall/integrate-lb.md)explicado. Especificamente neste caso, você precisa permitir o endereço IP do cliente nas regras NSG de entrada e também adicioná-lo à tabela de rota definida pelo utilizador com o próximo conjunto de lúpulo como `internet` . Se o encaminhamento não estiver corretamente configurado, verá um erro de tempo limite.

## <a name="next-steps"></a>Próximos passos

* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
* [Configurar aplicação virtual de rede](./network-virtual-appliance.md)
