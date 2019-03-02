---
title: Monitorizar e gerir o HDInsight do Azure utilizando a IU Web do Ambari
description: Saiba como utilizar Ambari para monitorizar e gerir clusters do HDInsight baseado em Linux. Neste documento, irá aprender a utilizar a interface do Usuário da Web de Ambari incluídos com clusters do HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 5e2a9ae474aaacbf688103efce3cd5ae9b96acde
ms.sourcegitcommit: c712cb5c80bed4b5801be214788770b66bf7a009
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2019
ms.locfileid: "57216922"
---
# <a name="manage-hdinsight-clusters-by-using-the-apache-ambari-web-ui"></a>Gerir clusters do HDInsight através da IU do Apache Ambari Web

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari simplifica a gestão e monitorização de um cluster do Apache Hadoop, fornecendo uma fácil de utilizar a IU da web e REST API. Ambari está incluído nos clusters do HDInsight baseado em Linux e é utilizado para monitorizar o cluster e fazer alterações de configuração.

Neste documento, irá aprender a utilizar a interface do Usuário da Web de Ambari com um cluster do HDInsight.

## <a id="whatis"></a>O que é o Apache Ambari?

[Apache Ambari](https://ambari.apache.org) simplifica a gestão de Hadoop, fornecendo uma interface do Usuário de web fácil de usar. Pode utilizar Ambari para gerir e monitorizar clusters do Hadoop. Os desenvolvedores podem integrar estas capacidades nas suas aplicações ao utilizar o [APIs de REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

A interface do Usuário da Web de Ambari é fornecido por predefinição com clusters do HDInsight que utilizam o sistema operativo Linux.

> [!IMPORTANT]  
> O Linux é o único sistema operativo utilizado na versão 3.4 ou superior do HDInsight. Para obter mais informações, veja [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Desativação do HDInsight no Windows). 

## <a name="connectivity"></a>Conectividade

A interface do Usuário da Web de Ambari está disponível no seu cluster do HDInsight em HTTPS://CLUSTERNAME.azurehdinsight.net, onde **CLUSTERNAME** é o nome do seu cluster.

> [!IMPORTANT]  
> Ligar ao Ambari no HDInsight requer HTTPS. Quando lhe for pedido para a autenticação, utilize o nome da conta de administrador e a palavra-passe fornecida quando o cluster foi criado.

## <a name="ssh-tunnel-proxy"></a>Túnel SSH (proxy)

Embora o Ambari para o cluster está acessível diretamente através da Internet, alguns links da IU da Web do Ambari (como para o JobTracker) não são expostas na internet. Para aceder a estes serviços, tem de criar um túnel SSH. Para obter mais informações, consulte [Use SSH Tunneling túnel com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Ambari Web UI

> [!WARNING]  
> Nem todas as funcionalidades da IU da Web do Ambari são suportadas no HDInsight. Para obter mais informações, consulte a [não suportadas operações](#unsupported-operations) seção deste documento.

Ao ligar à interface do usuário da Web de Ambari, lhe for pedido para autenticar para a página. Utilize o utilizador de administrador de cluster (predefinido administrador) e a palavra-passe que utilizou durante a criação do cluster.

Quando a página é aberta, tenha em atenção a barra na parte superior. Esta barra contém as seguintes informações e controles:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logótipo do Ambari** -abre o dashboard, o que pode ser utilizado para monitorizar o cluster.

* **Operações de n. º de nome de cluster** -mostra o número de operações em curso do Ambari. Selecionar o nome de cluster ou **# ops** apresenta uma lista de operações em segundo plano.

* **# alertas** -exibe os avisos e alertas críticos, caso haja algum, para o cluster.

* **Dashboard** -apresenta o dashboard.

* **Serviços** -informações e definições de configuração para os serviços do cluster.

* **Anfitriões** -informações e definições de configuração para os nós do cluster.

* **Alertas** -um log de informações, avisos e alertas críticos.

* **Administrador** -pilha/serviços de Software que estão instalados no cluster, as informações de conta de serviço e segurança do Kerberos.

* **Botão de administrador** -Ambari gestão, as definições de utilizador e de fim de sessão.

## <a name="monitoring"></a>Monitorização

### <a name="alerts"></a>Alertas

A lista seguinte contém os Estados de alerta comuns utilizados pelo Ambari:

* **OK**
* **Aviso**
* **CRITICAL**
* **UNKNOWN**

Diferente de alertas **OK** fazer com que o **# alertas** entrada na parte superior da página para apresentar o número de alertas. Selecionar esta entrada apresenta os alertas e o respetivo estado.

Ambari oferece vários alertas predefinidas. Os seguintes alertas ajudam a monitorizar a disponibilidade de um cluster:

| Nome do Alerta                               | Descrição                                                                                                                                                                                  |
|------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Estado do Monitor de métrica                    | Este alerta indica o estado do processo de Monitor de métricas, conforme determinado pelo script de estado do monitor.                                                                                   |
| Heartbeat de agente do Ambari                   | Este alerta é acionado se o servidor tiver perdido o contacto com um agente.                                                                                                                        |
| Processo de servidor zooKeeper                 | Este alerta ao nível do anfitrião é acionada se o processo de servidor ZooKeeper não pode ser determinado a ser a cópia de segurança e à escuta na rede.                                                               |
| Estado do servidor IOCache metadados           | Este alerta ao nível do anfitrião é acionada se o servidor de metadados IOCache não pode ser determinado ficar operacional e está a responder aos pedidos dos clientes                                                            |
| IU da Web do JournalNode                       | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web de JournalNode está inacessível.                                                                                                                 |
| Servidor de Thrift Spark2                     | Este alerta ao nível do anfitrião é acionada se o servidor de Thrift Spark2 não é possível determinar para ficar operacional.                                                                                                |
| Processo de servidor de histórico                   | Este alerta ao nível do anfitrião é acionada se o processo do servidor de histórico não pode ser estabelecida para ficar operacional e à escuta na rede.                                                                |
| Histórico servidor Web da interface do Usuário                    | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web do servidor de histórico está inacessível.                                                                                                              |
| IU da Web do ResourceManager                   | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web de ResourceManager está inacessível.                                                                                                             |
| Resumo do Estado de funcionamento do NodeManager               | Este alerta de nível de serviço é acionada se existirem NodeManagers mau estado de funcionamento                                                                                                                    |
| Aplicação Web de linha cronológica da interface do Usuário                      | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web de servidor de linha cronológica de aplicação não está acessível.                                                                                                         |
| Resumo do Estado de funcionamento do DataNode                  | Este alerta de nível de serviço é acionada se existirem DataNodes mau estado de funcionamento                                                                                                                       |
| IU da Web do NameNode                          | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web de NameNode está inacessível.                                                                                                                    |
| Processo de controlador de ativação pós-falha do zooKeeper    | Este alerta ao nível do anfitrião é acionada se o processo do controlador de ativação pós-falha do ZooKeeper não pode ser confirmada para ficar operacional e à escuta na rede.                                                   |
| IU da Web do servidor de Oozie                      | Este alerta ao nível do anfitrião é acionada se o servidor de Oozie IU da Web está inacessível.                                                                                                                |
| Oozie Server Status                      | Este alerta ao nível do anfitrião é acionada se o servidor de Oozie não pode ser determinado ficar operacional e está a responder aos pedidos dos clientes.                                                                      |
| Processo de Metastore Hive                   | Este alerta ao nível do anfitrião é acionada se o processo de Hive Metastore não pode ser determinado a ser a cópia de segurança e à escuta na rede.                                                                 |
| Processo de HiveServer2                      | Este alerta ao nível do anfitrião é acionada se a HiveServer não pode ser determinado ficar operacional e está a responder aos pedidos dos clientes.                                                                        |
| Estado do servidor de WebHCat                    | Este alerta ao nível do anfitrião é acionada se o estado do servidor templeton não está em bom estado.                                                                                                            |
| ZooKeeper percentagem de servidores disponíveis      | Este alerta é acionado se o número de para baixo de servidores de ZooKeeper no cluster é superior ao limiar crítico configurado. Agrega os resultados de verificações de processo do ZooKeeper.     |
| Servidor do Spark2 Livy                       | Este alerta ao nível do anfitrião é acionada se o servidor de Livy2 não é possível determinar para ficar operacional.                                                                                                        |
| Servidor de histórico de Spark2                    | Este alerta ao nível do anfitrião é acionada se o servidor de histórico de Spark2 não é possível determinar para ficar operacional.                                                                                               |
| Processo de Recoletor de métricas                | Este alerta é acionado se o Recoletor de métricas não pode ser confirmada para ficar operacional e à escuta na porta configurada para o número de segundos iguais ao limiar.                                 |
| Recoletor de métricas - processo de mestre de HBase | Este alerta é acionado se processos principais do HBase do Recoletor de métricas não podem ser confirmada para ficar operacional e à escuta na rede para o limiar configurado crítico, tendo em conta em segundos. |
| Monitores de percentagem de métricas disponíveis       | Este alerta é acionado se uma percentagem do Monitor de métricas de processos não estão operacionais e à escuta na rede para o aviso configurado e dos limiares críticos.                             |
| Percentagem NodeManagers disponíveis           | Este alerta é acionado se o número de baixo NodeManagers do cluster é superior ao limiar crítico configurado. Agrega os resultados de verificações de processo NodeManager.        |
| Estado de funcionamento do NodeManager                       | Este alerta ao nível do anfitrião verifica a propriedade de estado de funcionamento de nó disponível a partir do componente de NodeManager.                                                                                              |
| IU da Web do NodeManager                       | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web de NodeManager está inacessível.                                                                                                                 |
| O estado de funcionamento do NameNode alta disponibilidade        | Este alerta de nível de serviço é acionado se o Active Directory NameNode ou NameNode de reserva não está em execução.                                                                                     |
| Processo de DataNode                         | Este alerta ao nível do anfitrião é acionada se os processos de DataNode individuais não podem ser estabelecida para ficar operacional e à escuta na rede.                                                         |
| IU da Web do DataNode                          | Este alerta ao nível do anfitrião é acionada se a interface do Usuário da Web de DataNode está inacessível.                                                                                                                    |
| Percentagem JournalNodes disponíveis           | Este alerta é acionado se o número de baixo JournalNodes do cluster é superior ao limiar crítico configurado. Agrega os resultados de verificações de processo JournalNode.        |
| Percentagem DataNodes disponíveis              | Este alerta é acionado se o número de baixo DataNodes do cluster é superior ao limiar crítico configurado. Agrega os resultados de verificações de processo DataNode.              |
| Estado do servidor Zeppelin                   | Este alerta ao nível do anfitrião é acionada se o servidor de Zeppelin não pode ser determinado ficar operacional e está a responder aos pedidos dos clientes.                                                                   |
| Processo interativo do HiveServer2          | Este alerta ao nível do anfitrião é acionada se a HiveServerInteractive não pode ser determinado ficar operacional e está a responder aos pedidos dos clientes.                                                             |
| Aplicação de LLAP                         | Este alerta é acionado se a aplicação de LLAP não pode ser determinado ficar operacional e está a responder a pedidos.                                                                                    |


Alertas são organizados em vários grupos predefinidos, que podem ser visualizados a partir da **alertas** página.

![Página de alertas](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Pode gerir os grupos com o **ações** menu e selecionando **gerir grupos de alertas**.

![Gerir a caixa de diálogo de grupos de alertas](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Também pode gerir métodos de alertas e criar notificações de alerta da **ações** menu selecionando __gerir notificações de alerta__. Quaisquer notificações atuais são apresentadas. Também pode criar notificações a partir daqui. Notificações podem ser enviadas **E-Mail** ou **SNMP** quando ocorrem combinações específicas/gravidade do alerta. Por exemplo, pode enviar uma mensagem de e-mail quando qualquer um dos alertas no **predefinido do YARN** grupo é definido como **crítico**.

![Criar caixa de diálogo de alerta](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Por fim, selecionando __gerir definições de alerta__ partir a __ações__ menu permite-lhe definir o número de vezes que um alerta deve ocorrer antes do envio de uma notificação. Esta definição pode ser utilizada para impedir as notificações para erros transitórios.

### <a name="cluster"></a>Cluster

O **métricas** separador do dashboard contém uma série de widgets que tornam fácil monitorizar o estado do seu cluster rapidamente. Vários widgets, tal como **utilização da CPU**, fornecem informações adicionais, quando clicado.

![dashboard com métricas](./media/hdinsight-hadoop-manage-ambari/metrics.png)

O **mapas térmicos** separador apresenta métricas, como mapas térmicos coloridos, vai verde para vermelho.

![dashboard com mapas térmicos](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Para obter mais informações sobre os nós no cluster, selecione **anfitriões**. Em seguida, selecione o nó específico que está interessado.

![Detalhes do anfitrião](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Serviços

O **serviços** barra lateral no dashboard fornece uma visão rápida sobre o estado dos serviços em execução no cluster. Vários ícones são utilizados para indicar o estado ou ações que deverão ser executadas. Por exemplo, um símbolo de reciclagem amarelo é apresentado se um serviço tem de ser reciclado.

![barra lateral de serviços](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]  
> Os serviços apresentados são diferentes entre versões e tipos de cluster do HDInsight. Os serviços apresentados aqui podem ser diferentes que os serviços apresentados para o seu cluster.

A seleção de um serviço apresenta informações mais detalhadas sobre o serviço.

![informações de resumo do serviço](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Hiperligações rápidas

Alguns serviços de apresentar um **ligações rápidas** link na parte superior da página. Isso pode ser utilizado para aceder a específico do serviço web interfaces do usuário, tais como:

* **Histórico de tarefas** -histórico de tarefas do MapReduce.
* **Gestor de recursos** -IU de ResourceManager do YARN.
* **NameNode** -interface do Usuário NameNode (HDFS sistema) de ficheiros distribuído do Hadoop.
* **IU da Web do Oozie** -interface do Usuário do Oozie.

Selecionar qualquer uma destas ligações é aberto um novo separador no browser, que apresenta a página selecionada.

> [!NOTE]  
> Selecionar o **ligações rápidas** entrada para um serviço pode devolver um erro de "servidor não encontrado". Se encontrar este erro, tem de utilizar um túnel SSH ao utilizar o **ligações rápidas** entrada para este serviço. Para obter informações, consulte [Use SSH Tunneling túnel com o HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Gestão

### <a name="ambari-users-groups-and-permissions"></a>Os utilizadores de Ambari, grupos e permissões

Trabalhar com os utilizadores, grupos e as permissões são suportadas quando utilizar um [associados a um domínio](./domain-joined/apache-domain-joined-introduction.md) cluster do HDInsight. Para obter informações sobre como utilizar a interface do Usuário de gestão do Ambari num cluster associado a um domínio, consulte [gerir clusters do HDInsight associados a um domínio](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]  
> Não altere a palavra-passe de watchdog o Ambari (hdinsightwatchdog) no seu cluster do HDInsight baseado em Linux. Alterar a palavra-passe quebra a capacidade de utilizar as ações de script ou efetuar operações de dimensionamento com o seu cluster.

### <a name="hosts"></a>Anfitriões

O **anfitriões** página apresenta uma lista de todos os anfitriões do cluster. Para gerir anfitriões, siga estes passos.

![página de anfitriões](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]  
> Adicionar, desativar e recommissioning um anfitrião não devem ser utilizadas com clusters do HDInsight.

1. Selecione o anfitrião que pretende gerir.

2. Utilize o **ações** menu para selecionar a ação que deseja executar:

   * **Iniciar todos os componentes** -iniciar todos os componentes no anfitrião.

   * **Parar todos os componentes** -parar todos os componentes no anfitrião.

   * **Reinicie todos os componentes** - parar e iniciar todos os componentes no anfitrião.

   * **Ativar o modo de manutenção** -suprime alertas para o anfitrião. Esse modo deve ser ativado se estiver a efetuar ações que geram alertas. Por exemplo, parar e iniciar um serviço.

   * **Desativar o modo de manutenção** -devolve o host para alertar normal.

   * **Parar** -DataNode paradas ou NodeManagers no anfitrião.

   * **Iniciar** -DataNode é iniciado ou NodeManagers no anfitrião.

   * **Reiniciar** -paragens e inícios DataNode ou NodeManagers no anfitrião.

   * **Desativar** -remove um anfitrião do cluster.

     > [!NOTE]  
     > Não utilize esta ação em clusters do HDInsight.

   * **Recommission** -adiciona um anfitrião anteriormente desativado para o cluster.

     > [!NOTE]  
     > Não utilize esta ação em clusters do HDInsight.

### <a id="service"></a>Serviços

Do **Dashboard** ou **serviços** página, utilize o **ações** na parte inferior da lista de serviços para parar e iniciar todos os serviços.

![ações de serviço](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]  
> Embora **Adicionar serviço** está listado neste menu, ele não deve ser usado para adicionar serviços ao cluster do HDInsight. Novos serviços devem ser adicionados com uma ação de Script durante o aprovisionamento do cluster. Para obter mais informações sobre como utilizar as ações de Script, consulte [HDInsight personalizar clusters com ações de Script](hdinsight-hadoop-customize-cluster-linux.md).

Embora o **ações** botão pode reiniciar todos os serviços, muitas vezes, pretende iniciar, parar ou reiniciar um serviço específico. Utilize os seguintes passos para efetuar ações num serviço individual:

1. Partir do **Dashboard** ou **serviços** , selecione um serviço.

2. Na parte superior do **resumo** separador, utilize o **ações do serviço** botão e selecione a ação a tomar. Isto reinicia o serviço em todos os nós.

    ![ação de serviço](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]  
   > Reiniciar alguns serviços, enquanto o cluster está em execução, pode gerar alertas. Para evitar alertas, pode utilizar o **ações de serviço** botão para ativar a **modo de manutenção** para o serviço antes de executar o reinício.

3. Depois de uma ação tiver sido selecionada, o **# op** entrada na parte superior do aumenta de página para mostrar que uma operação em segundo plano está a ocorrer. Se configurado para apresentar, é apresentada a lista de operações em segundo plano.

   > [!NOTE]  
   > Se ativou **modo de manutenção** para o serviço, lembre-se de o desativar utilizando o **ações do serviço** botão assim que a operação estiver concluída.

Para configurar um serviço, utilize os seguintes passos:

1. Partir do **Dashboard** ou **serviços** , selecione um serviço.

2. Selecione o **configurações** separador. A configuração atual é apresentada. Também é apresentada uma lista de configurações anteriores.

    ![Configurações](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Utilize os campos apresentados para modificar a configuração e, em seguida, selecione **guardar**. Ou selecione uma configuração anterior e, em seguida, selecione **tornar atual** reverter para as definições anteriores.

## <a name="ambari-views"></a>Vistas Ambari

As vistas Ambari permitem que os desenvolvedores conectar os elementos de interface do Usuário a IU Web do Ambari, utilizando o [Framework de vistas do Apache Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight fornece as seguintes vistas com tipos de clusters do Hadoop:


* Vista do Hive: A vista do Hive permite-lhe executar consultas do Hive diretamente a partir do seu navegador da web. Pode guardar consultas, ver os resultados, salvar os resultados para o armazenamento de cluster ou transferir os resultados para o seu sistema local. Para obter mais informações sobre como utilizar as vistas do Hive, consulte [utilização Apache Hive modos de exibição com o HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Modo de exibição do Tez: O modo de exibição do Tez permite-lhe compreender melhor e a otimizar tarefas. Pode ver informações sobre como as tarefas do Tez são executadas e que recursos são utilizados.

## <a name="unsupported-operations"></a>Operações não suportadas

As seguintes operações de Ambari não são suportadas no HDInsight:

* __Mover o serviço do Recoletor de métricas__. Ao visualizar informações sobre o serviço do Recoletor de métricas, uma das ações disponíveis no menu ações de serviço é __recoletor de métricas de mover__. Isto não é suportado com o HDInsight.

## <a name="next-steps"></a>Passos Seguintes

Saiba como utilizar o [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md) com HDInsight.
