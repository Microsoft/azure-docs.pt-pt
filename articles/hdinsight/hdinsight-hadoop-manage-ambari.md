---
title: Monitorize e gere a Azure HDInsight usando a Ambari Web UI
description: Aprenda a usar a UI Apache Ambari para monitorizar e gerir clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 01/12/2021
ms.openlocfilehash: 8195cffed077a77c0ad66fda1126e2f3ea53d5f0
ms.sourcegitcommit: c136985b3733640892fee4d7c557d40665a660af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/13/2021
ms.locfileid: "98179151"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerir clusters do HDInsight através da IU da Web do Apache Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica a gestão e monitorização de um cluster Apache Hadoop. Esta simplificação é feita proporcionando uma UI web fácil de usar e REST API. Ambari está incluído em clusters HDInsight, e é usado para monitorizar o cluster e fazer alterações de configuração.

Neste documento, aprende-se a usar o UI Web Ambari com um cluster HDInsight.

## <a name="what-is-apache-ambari"></a><a id="whatis"></a>O que é Apache Ambari?

[Apache Ambari](https://ambari.apache.org) simplifica a gestão da Hadoop fornecendo uma UI web fácil de usar. Você pode usar Ambari para gerir e monitorizar os clusters Hadoop. Os desenvolvedores podem integrar estas capacidades nas suas aplicações utilizando as [APIs Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Conectividade

O Ambari Web UI está disponível no seu cluster HDInsight em `https://CLUSTERNAME.azurehdinsight.net` , onde está o nome do seu `CLUSTERNAME` cluster.

> [!IMPORTANT]  
> A ligação a Ambari no HDInsight requer HTTPS. Quando solicitado para a autenticação, utilize o nome da conta de administração e a palavra-passe que forneceu quando o cluster foi criado. Se não for solicitado para obter credenciais, verifique as definições da sua rede para confirmar que não existe qualquer problema de conectividade entre o cliente e os Clusters Azure HDInsight.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Embora o Ambari para o seu cluster esteja acessível diretamente pela Internet, algumas ligações da UI Web Ambari (como o JobTracker) não estão expostas na internet. Para aceder a estes serviços, tem de criar um túnel SSH. Para obter mais informações, consulte [o Uso de Túneis SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> Nem todas as funcionalidades do Ambari Web UI são suportadas no HDInsight. Para mais informações, consulte a secção de [operações não suportadas](#unsupported-operations) deste documento.

Ao ligar-se à UI web Ambari, é solicitado que autente para autenticar a página. Utilize o utilizador de administração de cluster (administrador predefinido) e a palavra-passe que utilizou durante a criação do cluster.

Quando a página abrir, note o bar no topo. Esta barra contém as seguintes informações e controlos:

![Visão geral do painel de ambari de Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |Descrição |
|---|---|
|Logotipo Ambari|Abre o painel de instrumentos, que pode ser utilizado para monitorizar o cluster.|
|Nome do cluster # ops|Mostra o número de operações Ambari em curso. Selecionar o nome do cluster ou **# ops** apresenta uma lista de operações de fundo.|
|# alertas|Apresenta avisos ou alertas críticos, se houver, para o cluster.|
|Dashboard|Exibe o painel de instrumentos.|
|Serviços|Definições de informação e configuração para os serviços no cluster.|
|Anfitriões|Definições de informação e configuração para os nós no cluster.|
|Alertas|Um registo de informações, avisos e alertas críticos.|
|Admin|Pilha de software/serviços instalados no cluster, informações de conta de serviço e segurança Kerberos.|
|Botão de administração|Gestão Ambari, configurações do utilizador e assine.|

## <a name="monitoring"></a>Monitorização

### <a name="alerts"></a>Alertas

A lista que se segue contém os estados de alerta comuns utilizados por Ambari:

* **OK**
* **Aviso**
* **CRÍTICO**
* **DESCONHECIDO**

Alertas que não **ok** fazem com que a entrada **# alerta** no topo da página para mostrar o número de alertas. A seleção desta entrada mostra os alertas e o seu estado.

Os alertas são organizados em vários grupos predefinidos, que podem ser vistos na página **Alertas.**

![Apache Ambari alerta resumo da página](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Pode gerir os grupos utilizando o menu **Ações** e selecionando **Grupos de Alerta de Gestão**.

![Apache Ambari gere grupos de alerta](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Gere métodos de alerta e cria notificações de alerta a partir do menu **Ações** selecionando __Notificações de Gestão__. Quaisquer notificações atuais são apresentadas. Crie notificações a partir daqui. As notificações podem ser enviadas via **EMAIL** ou **SNMP** quando ocorrem combinações específicas de alerta/gravidade. Por exemplo, pode enviar uma mensagem de e-mail quando qualquer um dos alertas do grupo **YARN Default** estiver definido para **Critical**.

![Apache Ambari cria notificação de alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, a seleção de Definições de Alerta de __Gestão__ a partir do menu __Ações__ permite-lhe definir o número de vezes que um alerta deve ocorrer antes de uma notificação ser enviada. Esta definição pode ser utilizada para evitar notificações de erros transitórios.

Para obter um tutorial de uma notificação de alerta utilizando uma [conta SendGrid](../sendgrid-dotnet-how-to-send-email.md)gratuita, consulte [notificações de e-mail Configure Apache Ambari em Azure HDInsight](./apache-ambari-email.md).

### <a name="cluster"></a>Cluster

O separador **Métricas** do painel contém uma série de widgets que facilitam a monitorização do estado do seu cluster num ápice. Vários widgets, como **o Uso do CPU,** fornecem informações adicionais quando clicados.

![Painel Apache Ambari com métricas](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

O **separador Heatmaps** apresenta métricas como máscaras de calor coloridas, passando de verde para vermelho.

![Painel Apache Ambari com máscaras de calor](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Para obter mais informações sobre os nós dentro do cluster, **selecione Hosts**. Em seguida, selecione o nó específico que lhe interessa.

![Apache Ambari acolhe detalhes do resumo](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Serviços

A barra lateral **dos Serviços** no painel de instrumentos fornece uma visão rápida do estado dos serviços em funcionamento no cluster. Vários ícones são usados para indicar o estado ou as ações que devem ser tomadas. Por exemplo, é apresentado um símbolo de reciclagem amarelo se um serviço tiver de ser reciclado.


![Bar lateral de serviços Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Os serviços apresentados diferem entre os tipos e versões de cluster HDInsight. Os serviços apresentados aqui podem ser diferentes dos serviços apresentados para o seu cluster.

A seleção de um serviço apresenta informações mais detalhadas sobre o serviço.

![Informação sumária do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Ligações rápidas

Alguns serviços exibem uma **ligação de Links Rápidos** no topo da página. Este link pode ser usado para aceder a UI web específicos de serviço, tais como:

* **História do Trabalho** - MapReduce histórico de emprego.
* **Gestor de Recursos** - YARN Resource Manager UI.
* **NameNode** - Hadoop Distributed File System (HDFS) NameNode UI.
* **Oozie Web UI** - Oozie UI.

A seleção de qualquer uma destas ligações abre um novo separador no seu navegador, que exibe a página selecionada.

> [!NOTE]  
> Selecionar a entrada **de Links Rápidos** para um serviço pode devolver um erro de "servidor não encontrado". Se encontrar este erro, deve utilizar um túnel SSH quando utilizar a entrada **de Links Rápidos** para este serviço. Para obter informações, consulte [o Uso de Túneis SSH com HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gestão

### <a name="ambari-users-groups-and-permissions"></a>Utilizadores, grupos e permissões Ambari

É suportado o trabalho com utilizadores, grupos e permissões. Para a administração local, consulte [os utilizadores autorizados para vistas Apache Ambari.](./hdinsight-authorize-users-to-ambari.md) Para clusters unidos por domínio, consulte [Gerir os clusters HDInsight unidos pelo domínio.](./domain-joined/hdinsight-security-overview.md)

> [!WARNING]  
> Não elimine nem altere a palavra-passe do cão de guarda Ambari (hdinsightwatchdog) no seu cluster HDInsight baseado em Linux. Alterar a palavra-passe quebra a capacidade de usar ações de script ou realizar operações de escala com o seu cluster.

### <a name="hosts"></a>Anfitriões

A página **hostes** lista todos os anfitriões no cluster. Para gerir os anfitriões, siga estes passos.

![Apache Ambari acolhe visão geral da página](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> A adição, o desmantelamento e a reativação de um hospedeiro não devem ser utilizados com clusters HDInsight.

1. Selecione o anfitrião que deseja gerir.

2. Utilize o menu **Ações** para selecionar a ação que deseja fazer:

    |Item |Descrição |
    |---|---|
    |Inicie todos os componentes|Inicie todos os componentes do hospedeiro.|
    |Parar todos os componentes|Pare todos os componentes do hospedeiro.|
    |Reiniciar todos os componentes|Pare e inicie todos os componentes do hospedeiro.|
    |Ligue o modo de manutenção|Suprime alertas para o hospedeiro. Este modo deve ser ativado se estiver a fazer ações que gerem alertas. Por exemplo, parar e iniciar um serviço.|
    |Desligue o modo de manutenção|Devolve o hospedeiro ao alerta normal.|
    |Parar|Para dataNode ou NodeManagers no anfitrião.|
    |Iniciar|Inicia dataNode ou NodeManagers no anfitrião.|
    |Reiniciar|Para e inicia dataNode ou NodeManagers no anfitrião.|
    |Desmantelamento|Remove um hospedeiro do aglomerado. **Não use esta ação em clusters HDInsight.**|
    |Recomissão|Adiciona um hospedeiro previamente desativado ao cluster. **Não use esta ação em clusters HDInsight.**|

### <a name="services"></a><a id="service"></a>Serviços

Na página **Dashboard** ou **Serviços,** utilize o botão **Ações** na parte inferior da lista de serviços para parar e iniciar todos os serviços.

:::image type="content" source="./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png" alt-text="Lista de ações de serviço Apache Ambari." border="true":::

> [!WARNING]  
> Os novos serviços devem ser adicionados utilizando uma Ação de Script durante o fornecimento de clusters. Para obter mais informações sobre a utilização de ações de script, consulte [os clusters HDInsight personalizados utilizando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Enquanto o botão **Ações** pode reiniciar todos os serviços, muitas vezes pretende iniciar, parar ou reiniciar um serviço específico. Utilize os seguintes passos para fazer ações num serviço individual:

1. Na página **Dashboard** ou **Serviços,** selecione um serviço.

2. A partir da parte superior do separador **Resumo,** utilize o botão **Ações de Serviço** e selecione as ações a tomar. Esta ação reinicia o serviço em todos os nós.

    ![Ações individuais de serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar alguns serviços enquanto o cluster está a funcionar pode gerar alertas. Para evitar alertas, pode utilizar o botão **Ações de Serviço** para ativar o **modo manutenção** do serviço antes de efetuar o reinício.

3. Uma vez selecionada uma ação, a entrada **#op** no topo da página incrementa para mostrar que está a ocorrer uma operação de fundo. Se configurar para visualizar, é apresentada a lista de operações de fundo.

   > [!NOTE]  
   > Se ativar o **modo manutenção** do serviço, lembre-se de desativá-lo utilizando o botão **Ações de Serviço** uma vez terminada a operação.

Para configurar um serviço, utilize os seguintes passos:

1. Na página **Dashboard** ou **Serviços,** selecione um serviço.

2. Selecione o **separador Configs.** A configuração atual é apresentada. É também apresentada uma lista de configurações anteriores.

    ![Configuração de serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Utilize os campos apresentados para modificar a configuração e, em seguida, **selecione Guardar**. Ou selecione uma configuração anterior e, em seguida, **selecione Faça** a corrente para reverter para as definições anteriores.

## <a name="ambari-views"></a>Vistas de Ambari

O Ambari Views permite que os desenvolvedores liguem elementos de UI à UI Web Ambari usando o Apache Ambari Views Framework. HDInsight fornece as seguintes vistas com tipos de cluster Hadoop:

* Hive View: A Hive View permite-lhe executar consultas de Hive diretamente a partir do seu navegador web. Pode guardar consultas, ver resultados, guardar resultados para o armazenamento do cluster ou baixar resultados para o seu sistema local. Para obter mais informações sobre a utilização de Vistas de Colmeia, consulte [Use Apache Hive Views com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Tez View: O Tez View permite-lhe compreender melhor e otimizar os empregos. Você pode ver informações sobre como os trabalhos tez são executados e que recursos são usados.

## <a name="unsupported-operations"></a>Operações não apoiadas

As seguintes operações de Ambari não são suportadas na HDInsight:

* __Mover o serviço de Coletor Métricas__. Ao visualizar informações sobre o serviço Metrics Collector, uma das ações disponíveis no menu De ações de Serviço é o __coletor Move Metrics__. Esta ação não é apoiada com HDInsight.

## <a name="next-steps"></a>Passos seguintes

* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) com HDInsight.
* [Use Apache Ambari to optimize HDInsight cluster configurations](./hdinsight-changing-configs-via-ambari.md) (Utilizar o Apache Ambari para otimizar as configurações do cluster do HDInsight)
* [Clusters Azure HDInsight](./hdinsight-scaling-best-practices.md)
