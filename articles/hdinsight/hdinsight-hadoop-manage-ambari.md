---
title: Monitorize e gerencie o Azure HDInsight utilizando a Ambari Web UI
description: Aprenda a usar Ambari para monitorizar e gerir os clusters HDInsight baseados em Linux. Neste documento, aprende-se a utilizar o Ambari Web UI incluído com clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: d8cb8bfa32db958b6dfdda0df23429669ce2a439
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063803"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerir os clusters HDInsight utilizando o Apache Ambari Web UI

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica a gestão e monitorização de um cluster Apache Hadoop, fornecendo uma UI web e API REST de fácil utilização. Ambari está incluído em clusters HDInsight, e é usado para monitorizar o cluster e fazer alterações de configuração.

Neste documento, aprende-se a utilizar o Ambari Web UI com um cluster HDInsight.

## <a id="whatis"></a>O que é Apache Ambari?

[Apache Ambari](https://ambari.apache.org) simplifica a gestão hadoop fornecendo um UI web fácil de usar. Pode usar Ambari para gerir e monitorizar os aglomerados Hadoop. Os desenvolvedores podem integrar estas capacidades nas suas aplicações utilizando as [APIs Ambari REST](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Conectividade

O Ambari Web UI está disponível no seu cluster HDInsight em `https://CLUSTERNAME.azurehdinsight.net`, onde `CLUSTERNAME` é o nome do seu cluster.

> [!IMPORTANT]  
> Ligar a Ambari no HDInsight requer HTTPS. Quando solicitado para autenticação, utilize o nome da conta de administração e a palavra-passe que forneceu quando o cluster foi criado. Se não for solicitado por credenciais, verifique as definições da sua rede para confirmar que não existe qualquer problema de conectividade entre o cliente e os Clusters Azure HDInsight.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (procuração)

Enquanto Ambari para o seu cluster é acessível diretamente através da Internet, algumas ligações do Ambari Web UI (como o JobTracker) não estão expostas na internet. Para aceder a estes serviços, tem de criar um túnel SSH. Para mais informações, consulte [Utilize o Túnel SSH com hDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface do usuário da Web do amAmbari

> [!WARNING]  
> Nem todas as funcionalidades do Ambari Web UI são suportadas no HDInsight. Para mais informações, consulte a secção de [operações não apoiadas](#unsupported-operations) deste documento.

Ao ligar-se à Ambari Web UI, é solicitado que autenticar a página. Utilize o utilizador de administrador do cluster (Administrador predefinido) e a palavra-passe que utilizou durante a criação do cluster.

Quando a página abrir, note o bar no topo. Esta barra contém as seguintes informações e controlos:

![Visão geral do painel do Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |Descrição |
|---|---|
|Logotipo de Ambari|Abre o painel de instrumentos, que pode ser usado para monitorizar o cluster.|
|Nome do cluster # ops|Mostra o número de operações ambari em curso. A seleção do nome do cluster ou **# ops** apresenta uma lista de operações de fundo.|
|# alertas|Apresenta avisos ou alertas críticos, se houver, para o cluster.|
|Dashboard|Exibe o painel de instrumentos.|
|Serviços|Definições de informação e configuração para os serviços do cluster.|
|Anfitriões|Definições de informação e configuração para os nós do cluster.|
|Alertas|Um registo de informações, avisos e alertas críticos.|
|administrador|Pilha de software/serviços que estão instalados no cluster, informações de conta de serviço e segurança Kerberos.|
|Botão de administração|Gestão ambari, configurações do utilizador e assinar.|

## <a name="monitoring"></a>Monitorização

### <a name="alerts"></a>Alertas

A lista que se segue contém os estados de alerta comuns utilizados por Ambari:

* **Ok**
* **Aviso**
* **CRÍTICO**
* **DESCONHECIDO**

Os alertas que não o **OK** causam a entrada **de # alertas** na parte superior da página para mostrar o número de alertas. A seleção desta entrada mostra os alertas e o seu estado.

Os alertas são organizados em vários grupos predefinidos, que podem ser visualizados a partir da página **Alertas.**

![Apache Ambari alerta resumo da página](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Pode gerir os grupos utilizando o menu **Ações** e selecionando Grupos de **Alerta de Gestão**.

![Apache Ambari gere grupos de alerta](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Também pode gerir métodos de alerta e criar notificações de alerta a partir do menu **Ações** selecionando __'Gerir Notificações'.__ São apresentadas notificações atuais. Pode também criar notificações a partir daqui. As notificações podem ser enviadas via **E-MAIL** ou **SNMP** quando ocorrem combinações específicas de alerta/gravidade. Por exemplo, pode enviar uma mensagem de e-mail quando qualquer um dos alertas do grupo **Padrão YARN** estiver definido para **Critical**.

![Apache Ambari cria notificação de alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, selecionar Definições de __'Gerir alerta'__ do menu __Ações__ permite-lhe definir o número de vezes que um alerta deve ocorrer antes de ser enviada uma notificação. Esta definição pode ser utilizada para evitar notificações de erros transitórios.

### <a name="cluster"></a>Cluster

O separador **Métricas** do painel contém uma série de widgets que facilitam a monitorização do estado do seu cluster num ápice. Vários widgets, como o **uso do CPU,** fornecem informações adicionais quando clicados.

![Painel apache Ambari com métricas](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

O separador **Heatmaps** apresenta métricas como mapas de calor coloridos, passando de verde para vermelho.

![Painel apache Ambari com mapas de calor](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Para mais informações sobre os nós dentro do cluster, selecione **Hosts**. Em seguida, selecione o nó específico em que está interessado.

![Apache Ambari acolhe detalhes sumários](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Serviços

A barra lateral **de serviços** no tablier fornece uma visão rápida do estado dos serviços em funcionamento no cluster. Vários ícones são usados para indicar o estado ou as ações que devem ser tomadas. Por exemplo, é apresentado um símbolo de reciclagem amarelo se um serviço precisar de ser reciclado.

![Barra lateral dos serviços Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Os serviços apresentados diferem entre os tipos de cluster HDInsight e as versões. Os serviços aqui apresentados podem ser diferentes dos serviços apresentados para o seu cluster.

A seleção de um serviço apresenta informações mais detalhadas sobre o serviço.

![Informação sumária do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Ligações rápidas

Alguns serviços exibem um link **Quick Links** no topo da página. Isto pode ser usado para aceder a UIs web específicos do serviço, tais como:

* **Histórico de Emprego** - MapReduce história do trabalho.
* **Gestor de Recursos** - YARN ResourceManager UI.
* **NameNode** - Hadoop Distributed File System (HDFS) NameNode UI.
* **Oozie Web UI** - Oozie UI.

A seleção de qualquer um destes links abre um novo separador no seu navegador, que exibe a página selecionada.

> [!NOTE]  
> A seleção da entrada **Quick Links** para um serviço pode devolver um erro de "servidor não encontrado". Se encontrar este erro, deve utilizar um túnel SSH ao utilizar a entrada **Quick Links** para este serviço. Para obter informações, consulte [Utilize o Túnel SSH com hDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gestão

### <a name="ambari-users-groups-and-permissions"></a>Utilizadores, grupos e permissões ambari

Trabalhar com utilizadores, grupos e permissões são suportados quando se utiliza um cluster HDInsight [de domínio.](./domain-joined/hdinsight-security-overview.md) Para obter informações sobre a utilização do UI de Gestão Ambari num cluster unido ao domínio, consulte [Gerir os clusters HDInsight unidos](./domain-joined/hdinsight-security-overview.md)pelo domínio .

> [!WARNING]  
> Não altere a senha do Watchdog do Ambari (hdinsightwatchdog) em seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar ações de script ou executar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Anfitriões

A página **dos Anfitriões** lista todos os anfitriões do agrupamento. Para gerir os anfitriões, siga estes passos.

![Apache Ambari acolhe visão geral da página](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Adicionar, desmantelar e reativar um hospedeiro não deve ser utilizado com clusters HDInsight.

1. Selecione o anfitrião que deseja gerir.

2. Utilize o menu **Ações** para selecionar a ação que pretende realizar:

    |Item |Descrição |
    |---|---|
    |Iniciar todos os componentes|Inicie todos os componentes do hospedeiro.|
    |Parar todos os componentes|Pare todos os componentes do hospedeiro.|
    |Reiniciar todos os componentes|Pare e inicie todos os componentes do hospedeiro.|
    |Ligue o modo de manutenção|Suprime alertas para o hospedeiro. Este modo deve ser ativado se estiver a realizar ações que gerem alertas. Por exemplo, parar e iniciar um serviço.|
    |Desligue o modo de manutenção|Devolve o hospedeiro ao alerta normal.|
    |Parar|Para DataNode ou NodeManagers no hospedeiro.|
    |Iniciar|Inicia DataNode ou NodeManagers no anfitrião.|
    |Reiniciar|Para e inicia DataNode ou NodeManagers no hospedeiro.|
    |Desmantelamento|Remove um hospedeiro do aglomerado. **Não utilize esta ação em clusters HDInsight.**|
    |Recomissão|Adiciona um hospedeiro previamente desativado ao cluster. **Não utilize esta ação em clusters HDInsight.**|

### <a id="service"></a>Serviços

A partir da página **dashboard** ou **Serviços,** use o botão **Ações** na parte inferior da lista de serviços para parar e iniciar todos os serviços.

![Lista de ações de serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Enquanto o **Add Service** está listado neste menu, este não deve ser utilizado para adicionar serviços ao cluster HDInsight. Os novos serviços devem ser adicionados através de uma Ação de Script durante o fornecimento de clusters. Para obter mais informações sobre a utilização de Ações de Script, consulte [os clusters Personalizados HDInsight utilizando as ações](hdinsight-hadoop-customize-cluster-linux.md)do script .

Enquanto o botão **Ações** pode reiniciar todos os serviços, muitas vezes pretende iniciar, parar ou reiniciar um serviço específico. Utilize os seguintes passos para realizar ações num serviço individual:

1. A partir da página **dashboard** ou **Serviços,** selecione um serviço.

2. A partir da parte superior do separador **Resumo,** utilize o botão Ações de **Serviço** e selecione a ação a tomar. Isto reinicia o serviço em todos os nós.

    ![Ações individuais de serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar alguns serviços enquanto o cluster está em execução pode gerar alertas. Para evitar alertas, pode utilizar o botão Ações de **Serviço** para ativar o **modo de manutenção** do serviço antes de efetuar o reinício.

3. Uma vez selecionada uma ação, a entrada **#op** no topo dos incrementos da página para mostrar que está a ocorrer uma operação de fundo. Se configurado para visualizar, a lista de operações de fundo é apresentada.

   > [!NOTE]  
   > Se ativou o modo de **manutenção** do serviço, lembre-se de o desativar utilizando o botão **Desativação** de Assistência quando a operação estiver concluída.

Para configurar um serviço, utilize os seguintes passos:

1. A partir da página **dashboard** ou **Serviços,** selecione um serviço.

2. Selecione o separador **Configs.** A configuração atual é apresentada. Também é apresentada uma lista de configurações anteriores.

    ![Configuração do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Utilize os campos visualizados para modificar a configuração e, em seguida, **selecione Guardar**. Ou selecione uma configuração anterior e, em seguida, selecione **Fazer a corrente** para voltar às definições anteriores.

## <a name="ambari-views"></a>Vistas de Ambari

A Ambari Views permite que os desenvolvedores liguem elementos uI no UI Web Ambari utilizando o [Apache Ambari Views Framework](https://cwiki.apache.org/confluence/display/AMBARI/Views). O HDInsight fornece as seguintes vistas com tipos de cluster Hadoop:

* Vista hive: A Vista da Colmeia permite-lhe executar consultas da Hive diretamente do seu navegador web. Pode guardar consultas, visualizar resultados, guardar resultados para o armazenamento do cluster ou baixar resultados para o seu sistema local. Para obter mais informações sobre a utilização de Vistas da Colmeia, consulte [Use Apache Hive Views com HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Visão tez: A Visão tez permite-lhe compreender melhor e otimizar empregos. Pode ver informações sobre como os empregos da Tez são executados e quais os recursos utilizados.

## <a name="unsupported-operations"></a>Operações não apoiadas

As seguintes operações ambari não são suportadas no HDInsight:

* __Mover o serviço de Colecionador de Métricas.__ Ao visualizar informações sobre o serviço De coleção de métricas, uma das ações disponíveis no menu de Ações de Serviço é o __colecionador de Métricas de Movimento__. Isto não é suportado com hDInsight.

## <a name="next-steps"></a>Passos seguintes

Aprenda a utilizar a [API Apache Ambari REST](hdinsight-hadoop-manage-ambari-rest-api.md) com HDInsight.
