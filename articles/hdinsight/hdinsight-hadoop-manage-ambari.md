---
title: Monitorar e gerenciar o Azure HDInsight usando a interface do usuário da Web do amAmbari
description: Saiba como usar o Ambari para monitorar e gerenciar clusters HDInsight baseados em Linux. Neste documento, você aprenderá a usar a interface do usuário do amAmbari Web incluída com clusters HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 2f46f90edcdd1c4cdf7583c7e628aee205b312e1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098646"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerenciar clusters HDInsight usando a interface do usuário do Apache amAmbari Web

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

O Apache Ambari simplifica o gerenciamento e o monitoramento de um cluster Apache Hadoop fornecendo uma interface do usuário da Web fácil de usar e a API REST. O Ambari está incluído em clusters HDInsight e é usado para monitorar o cluster e fazer alterações de configuração.

Neste documento, você aprenderá a usar a interface do usuário da Web do amAmbari com um cluster HDInsight.

## <a id="whatis"></a>O que é o Apache Ambari?

O [Apache Ambari](https://ambari.apache.org) simplifica o gerenciamento do Hadoop, fornecendo uma interface do usuário da Web fácil de usar. Você pode usar o Ambari para gerenciar e monitorar clusters Hadoop. Os desenvolvedores podem integrar esses recursos em seus aplicativos usando as [APIs REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

## <a name="connectivity"></a>Conectividade

A interface do usuário da Web do amAmbari está disponível `https://CLUSTERNAME.azurehdinsight.net`em seu `CLUSTERNAME` cluster HDInsight em, em que é o nome do cluster.

> [!IMPORTANT]  
> Conectar-se ao Ambari no HDInsight requer HTTPS. Quando a autenticação for solicitada, use o nome e a senha da conta de administrador que você forneceu quando o cluster foi criado.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Embora o Ambari para o cluster seja acessível diretamente pela Internet, alguns links da interface do usuário da Web do amAmbari (por exemplo, para o JobTracker) não são expostos na Internet. Para acessar esses serviços, você deve criar um túnel SSH. Para obter mais informações, consulte [usar o túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interface do usuário da Web do amAmbari

> [!WARNING]  
> Nem todos os recursos da interface do usuário da Web do amAmbari têm suporte no HDInsight. Para obter mais informações, consulte a seção [operações sem suporte](#unsupported-operations) deste documento.

Ao conectar-se à interface do usuário da Web do amAmbari, você será solicitado a autenticar a página. Use o usuário administrador do cluster (administrador padrão) e a senha usada durante a criação do cluster.

Quando a página for aberta, observe a barra na parte superior. Esta barra contém as seguintes informações e controles:

![Visão geral do painel do Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-dashboard.png)

|Item |Descrição |
|---|---|
|Logotipo do Ambari|Abre o painel, que pode ser usado para monitorar o cluster.|
|Nome do cluster # Ops|Exibe o número de operações de Ambari em andamento. Selecionar o nome do cluster ou **# Ops** exibe uma lista de operações em segundo plano.|
|n º de alertas|Exibe avisos ou alertas críticos, se houver, para o cluster.|
|Dashboard|Exibe o painel.|
|Serviços|Informações e definições de configuração para os serviços no cluster.|
|Anfitriões|Informações e definições de configuração para os nós no cluster.|
|Alertas|Um log de informações, avisos e alertas críticos.|
|administrador|Pilha de software/serviços que estão instalados no cluster, informações de conta de serviço e segurança Kerberos.|
|Botão administrador|Gerenciamento de Ambari, configurações de usuário e sair.|

## <a name="monitoring"></a>Monitorização

### <a name="alerts"></a>Alertas

A lista a seguir contém os status de alerta comuns usados pelo Ambari:

* **OK**
* **Alerta**
* **CRITICAL**
* **CONHECIDOS**

Alertas diferentes de **OK** fazem com que a entrada **# Alerts** na parte superior da página exiba o número de alertas. A seleção dessa entrada exibe os alertas e seu status.

Os alertas são organizados em vários grupos padrão, que podem ser exibidos na página **alertas** .

![Resumo da página de alertas do Apache Ambari](./media/hdinsight-hadoop-manage-ambari/hdinsight-alerts-page.png)

Você pode gerenciar os grupos usando o menu **ações** e selecionando **gerenciar grupos de alertas**.

![Apache Ambari gerenciar grupos de alertas](./media/hdinsight-hadoop-manage-ambari/ambari-manage-alerts.png)

Você também pode gerenciar os métodos de alerta e criar notificações de alerta no menu **ações** selecionando __gerenciar notificações de alerta__. Todas as notificações atuais são exibidas. Você também pode criar notificações aqui. As notificações podem ser enviadas por **email** ou **SNMP** quando ocorrem combinações específicas de alerta/severidade. Por exemplo, você pode enviar uma mensagem de email quando qualquer um dos alertas no grupo **padrão yarn** for definido como **crítico**.

![Notificação de alerta de criação do Apache Ambari](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, a seleção de __gerenciar configurações de alerta__ no menu __ações__ permite que você defina o número de vezes que um alerta deve ocorrer antes que uma notificação seja enviada. Essa configuração pode ser usada para evitar notificações para erros transitórios.

### <a name="cluster"></a>Cluster

A guia **métricas** do painel contém uma série de widgets que facilitam o monitoramento do status do cluster em um relance. Vários widgets, como o **uso da CPU**, fornecem informações adicionais quando clicados.

![Painel do Apache Ambari com métricas](./media/hdinsight-hadoop-manage-ambari/hdi-metrics-dashboard.png)

A guia **calor** exibe as métricas como calor coloridas, indo de verde para vermelho.

![Painel do Apache Ambari com calor](./media/hdinsight-hadoop-manage-ambari/hdi-heatmap-dashboard.png)

Para obter mais informações sobre os nós no cluster, selecione **hosts**. Em seguida, selecione o nó específico em que você está interessado.

![Detalhes do resumo do host Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-host-details1.png)

### <a name="services"></a>Serviços

A barra lateral **Serviços** no painel fornece uma visão rápida do status dos serviços em execução no cluster. Vários ícones são usados para indicar o status ou as ações que devem ser tomadas. Por exemplo, um símbolo de reciclagem amarelo será exibido se um serviço precisar ser reciclado.

![Barra lateral dos serviços Apache Ambari](./media/hdinsight-hadoop-manage-ambari/apache-ambari-service-bar.png)

> [!NOTE]  
> Os serviços exibidos diferem entre as versões e os tipos de cluster HDInsight. Os serviços exibidos aqui podem ser diferentes dos serviços exibidos para o cluster.

A seleção de um serviço exibe informações mais detalhadas sobre o serviço.

![Informações de resumo do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-details.png)

#### <a name="quick-links"></a>Ligações rápidas

Alguns serviços exibem um link **links rápidos** na parte superior da página. Isso pode ser usado para acessar interfaces do site da Web específicas do serviço, como:

* **Histórico de trabalhos** -histórico de trabalhos MapReduce.
* **Resource Manager** -interface do usuário do ResourceManager yarn.
* Interface do usuário do **NameNode** -Hadoop sistema de arquivos distribuído (HDFS) NameNode.
* UI **da Web do amOozie** -interface do usuário do Oozie.

A seleção de qualquer um desses links abre uma nova guia no navegador, que exibe a página selecionada.

> [!NOTE]  
> A seleção da entrada **links rápidos** para um serviço pode retornar um erro "servidor não encontrado". Se você encontrar esse erro, deverá usar um túnel SSH ao usar a entrada **links rápidos** para este serviço. Para obter informações, consulte [usar o túnel SSH com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gestão

### <a name="ambari-users-groups-and-permissions"></a>Ambari usuários, grupos e permissões

Há suporte para o trabalho com usuários, grupos e permissões ao usar um cluster HDInsight [ingressado no domínio](./domain-joined/hdinsight-security-overview.md) . Para obter informações sobre como usar a interface do usuário de gerenciamento do Ambari em um cluster ingressado no domínio, consulte [gerenciar clusters HDInsight ingressados no domínio](./domain-joined/hdinsight-security-overview.md).

> [!WARNING]  
> Não altere a senha do Watchdog do Ambari (hdinsightwatchdog) em seu cluster HDInsight baseado em Linux. A alteração da senha interrompe a capacidade de usar ações de script ou executar operações de dimensionamento com o cluster.

### <a name="hosts"></a>Anfitriões

A página **hosts** lista todos os hosts no cluster. Para gerenciar hosts, siga estas etapas.

![Visão geral da página de hosts Apache Ambari](./media/hdinsight-hadoop-manage-ambari/hdinsight-hosts-page.png)

> [!NOTE]  
> Adicionar, encerrar e recomissionar um host não deve ser usado com clusters HDInsight.

1. Selecione o host que você deseja gerenciar.

2. Use o menu **ações** para selecionar a ação que você deseja executar:

    |Item |Descrição |
    |---|---|
    |Iniciar todos os componentes|Inicie todos os componentes no host.|
    |Parar todos os componentes|Pare todos os componentes no host.|
    |Reiniciar todos os componentes|Pare e inicie todos os componentes no host.|
    |Ativar o modo de manutenção|Suprime alertas para o host. Esse modo deve ser habilitado se você estiver executando ações que geram alertas. Por exemplo, parar e iniciar um serviço.|
    |Desligar o modo de manutenção|Retorna o host para alertas normais.|
    |Parar|Interrompe datanode ou NodeManagers no host.|
    |Start|Inicia datanode ou NodeManagers no host.|
    |Reiniciar|Para e inicia o datanode ou NodeManagers no host.|
    |Encerrar|Remove um host do cluster. **Não use essa ação em clusters HDInsight.**|
    |Reativação|Adiciona um host anterior descomissionado ao cluster. **Não use essa ação em clusters HDInsight.**|

### <a id="service"></a>Serviços

Na página **painel** ou **Serviços** , use o botão **ações** na parte inferior da lista de serviços para parar e iniciar todos os serviços.

![Lista de ações do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-actions.png)

> [!WARNING]  
> Embora **Adicionar serviço** esteja listado neste menu, ele não deve ser usado para adicionar serviços ao cluster HDInsight. Novos serviços devem ser adicionados usando uma ação de script durante o provisionamento do cluster. Para obter mais informações sobre como usar as ações de script, consulte [Personalizar clusters HDInsight usando ações de script](hdinsight-hadoop-customize-cluster-linux.md).

Embora o botão **ações** possa reiniciar todos os serviços, muitas vezes você deseja iniciar, parar ou reiniciar um serviço específico. Use as etapas a seguir para executar ações em um serviço individual:

1. Na página **painel** ou **Serviços** , selecione um serviço.

2. Na parte superior da guia **Resumo** , use o botão **ações de serviço** e selecione a ação a ser tomada. Isso reinicia o serviço em todos os nós.

    ![Ações de serviço individual do Apache Ambari](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar alguns serviços enquanto o cluster está em execução pode gerar alertas. Para evitar alertas, você pode usar o botão **ações de serviço** para habilitar o modo de **manutenção** para o serviço antes de executar a reinicialização.

3. Depois que uma ação for selecionada, a entrada **# op** na parte superior da página será incrementada para mostrar que uma operação em segundo plano está ocorrendo. Se estiver configurado para exibição, a lista de operações em segundo plano será exibida.

   > [!NOTE]  
   > Se você tiver habilitado o **modo de manutenção** para o serviço, lembre-se de desabilitá-lo usando o botão **ações de serviço** quando a operação for concluída.

Para configurar um serviço, use as seguintes etapas:

1. Na página **painel** ou **Serviços** , selecione um serviço.

2. Selecione o **configurações** separador. A configuração atual é exibida. Uma lista de configurações anteriores também é exibida.

    ![Configuração do serviço Apache Ambari](./media/hdinsight-hadoop-manage-ambari/ambari-service-configs.png)

3. Use os campos exibidos para modificar a configuração e, em seguida, selecione **salvar**. Ou selecione uma configuração anterior e, em seguida, selecione **tornar atual** para reverter para as configurações anteriores.

## <a name="ambari-views"></a>Vistas Ambari

Os Ambari views permitem que os desenvolvedores conectem elementos da interface do usuário à interface do usuário da Web do amAmbari usando a [estrutura de modos de exibição](https://cwiki.apache.org/confluence/display/AMBARI/Views) O HDInsight fornece as seguintes exibições com tipos de cluster Hadoop:

* Exibição do hive: A exibição do hive permite executar consultas do hive diretamente do seu navegador da Web. Você pode salvar as consultas, exibir os resultados, salvar os resultados no armazenamento do cluster ou baixar os resultados para o sistema local. Para obter mais informações sobre como usar exibições do hive, consulte [usar exibições de Apache Hive com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Exibição de tez: A exibição tez permite que você entenda e otimize melhor os trabalhos. Você pode exibir informações sobre como os trabalhos do tez são executados e quais recursos são usados.

## <a name="unsupported-operations"></a>Operações sem suporte

As operações Ambari a seguir não têm suporte no HDInsight:

* __Movendo o serviço do coletor de métricas__. Ao exibir informações sobre o serviço do coletor de métricas, uma das ações disponíveis no menu ações de serviço é __mover o coletor de métricas__. Não há suporte para isso com o HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Saiba como usar a [API REST do Apache Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) com o HDInsight.
