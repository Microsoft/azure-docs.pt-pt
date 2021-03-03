---
title: Azure HDInsight frequentemente fez perguntas
description: Perguntas frequentes sobre HDInsight
keywords: perguntas frequentes, faq
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 84124f33f6aa02f63c0c47a24bd7d2a71ced2d11
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101699305"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Frequently asked questions (Autenticação Pass-through: Perguntas mais frequentes)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre como executar [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Criar ou eliminar clusters do HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Como posso providenciar um cluster HDInsight?

Para rever os tipos de clusters HDInsight, e os métodos de provisionamento, consulte [Configurar clusters em HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, e muito mais](./hdinsight-hadoop-provision-linux-clusters.md).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Como posso eliminar um cluster HDInsight existente?

Para saber mais sobre a eliminação de um cluster quando já não estiver em uso, consulte [Eliminar um cluster HDInsight](hdinsight-delete-cluster.md).

Tente deixar pelo menos 30 a 60 minutos entre criar e apagar operações. Caso contrário, a operação poderá falhar com a seguinte mensagem de erro:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Como seleciono o número correto de núcleos ou nós para a minha carga de trabalho?

O número adequado de núcleos e outras opções de configuração dependem de vários fatores.

Para obter mais informações, consulte [o planeamento da capacidade para clusters HDInsight](./hdinsight-capacity-planning.md).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quais são os vários tipos de nós num aglomerado HDInsight?

Consulte [os tipos de recursos nos clusters Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Quais são as melhores práticas para criar grandes clusters HDInsight?

1. Recomenda a configuração de clusters HDInsight com um [DB Ambari personalizado](./hdinsight-custom-ambari-db.md) para melhorar a escalabilidade do cluster.
2. Use [a Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) para criar clusters HDInsight para tirar partido de uma largura de banda mais alta e outras características de desempenho da Azure Data Lake Storage Gen2.
3. Os headnodes devem ser suficientemente grandes para acomodar múltiplos serviços principais em execução nestes nós.
4. Algumas cargas de trabalho específicas, como a Interactive Query, também precisarão de nóns maiores do Zookeeper. Por favor, considere o mínimo de 8 VMs de núcleo.
5. No caso da Colmeia e da Faísca, utilize [a metásta de colmeia externa.](./hdinsight-use-external-metadata-stores.md)

## <a name="individual-components"></a>Componentes Individuais

### <a name="can-i-install-additional-components-on-my-cluster"></a>Posso instalar componentes adicionais no meu cluster?

Sim. Para instalar componentes adicionais ou personalizar a configuração do cluster, utilize:

- Scripts durante ou após a criação. Os scripts são invocados através da [ação do script](./hdinsight-hadoop-customize-cluster-linux.md). A ação do script é uma opção de configuração que pode utilizar a partir do portal Azure, cmdlets HDInsight Windows PowerShell ou o HDInsight .NET SDK. Esta opção de configuração pode ser utilizada a partir do portal Azure, cmdlets HDInsight Windows PowerShell ou HDInsight .NET SDK.

- [Plataforma de Aplicação HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) para instalar aplicações.

Para uma lista de componentes suportados consulte [quais são os componentes e versões Apache Hadoop disponíveis com HDInsight?](./hdinsight-component-versioning.md)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Posso atualizar os componentes individuais que estão pré-instalados no cluster?

Se atualizar componentes ou aplicações incorporadas que estejam pré-instaladas no seu cluster, a configuração resultante não será suportada pela Microsoft. Estas configurações do sistema não foram testadas pela Microsoft. Tente utilizar uma versão diferente do cluster HDInsight que pode já ter a versão atualizada do componente pré-instalado.

Por exemplo, atualizar a Colmeia como componente individual não é suportado. O HDInsight é um serviço gerido, e muitos serviços estão integrados com o servidor Ambari e testados. A atualização de uma Colmeia por si só faz com que os binários indexados de outros componentes mudem e causará problemas de integração de componentes no seu cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Spark e Kafka podem correr no mesmo cluster HDInsight?

Não, não é possível executar Apache Kafka e Apache Spark no mesmo aglomerado hdinsight. Crie clusters separados para Kafka e Spark para evitar problemas de contenção de recursos.

### <a name="how-do-i-change-timezone-in-ambari"></a>Como mudo o tempo de ausso em Ambari?

1. Abra o Ambari Web UI em `https://CLUSTERNAME.azurehdinsight.net` , onde CLUSTERNAME é o nome do seu cluster.
2. No canto superior direito, selecione administrador | Configurações. 

   ![Definições Ambari](media/hdinsight-faq/ambari-settings.png)

3. Na janela Definições do Utilizador, selecione o novo timezone a partir da queda do Timezone e, em seguida, clique em Guardar.

   ![Definições do utilizador Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Como posso migrar da metastore existente para a Base de Dados Azure SQL? 

Para migrar do SQL Server para a Base de Dados Azure SQL, consulte [Tutorial: Migrar o SQL Server para uma única base de dados ou base de dados em conjunto na Base de Dados Azure SQL offline utilizando DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>A metastore da Colmeia é eliminada quando o cluster é eliminado?

Depende do tipo de metástase que o seu cluster está configurado para usar.

Para uma metásta predefinido: A metástore predefinido faz parte do ciclo de vida do cluster. Quando elimina um cluster, a metástase correspondente e os metadados também são eliminados.

Para uma metásta personalizada: O ciclo de vida da metastore não está ligado ao ciclo de vida de um cluster. Assim, pode criar e eliminar clusters sem perder metadados. Metadados como os seus esquemas de Hive persistem mesmo depois de eliminar e recriar o cluster HDInsight.

Para obter mais informações, consulte [utilizar lojas de metadados externos no Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>A migração de uma metásta de Colmeia também migra as políticas padrão da base de dados ranger?

Não, a definição de política está na base de dados dos Rangers, por isso migrar a base de dados dos Rangers migrará a sua política.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Pode migrar uma metásta de Colmeia de um cluster de Pacote de Segurança Empresarial (ESP) para um cluster não-ESP, e o contrário?

Sim, você pode migrar uma metástore hive de um ESP para um cluster não-ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Como posso estimar o tamanho de uma base de dados de metástasias da Hive?

Uma metástaia de Colmeia é usada para armazenar os metadados para fontes de dados que são usadas pelo servidor Hive. Os requisitos de tamanho dependem em parte do número e complexidade das suas fontes de dados da Hive. Estes itens não podem ser estimados adiantado. Como delineado nas [diretrizes de metástasias da Hive,](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)pode começar com um nível S2. O nível fornece 50 DTU e 250 GB de armazenamento, e se vir um gargalo, escale a base de dados.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Suporta outra base de dados que não a Base de Dados Azure SQL como uma meta-loja externa?

Não, a Microsoft suporta apenas a Base de Dados Azure SQL como uma metástaria personalizada externa.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Posso partilhar uma metástaca em vários aglomerados?

Sim, pode partilhar uma metástore personalizada em vários clusters, desde que utilizem a mesma versão do HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Conectividade e redes virtuais  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quais são as implicações do bloqueio das portas 22 e 23 na minha rede?

Se bloquear as portas 22 e a porta 23, não terá acesso SSH ao cluster. Estas portas não são utilizadas pelo serviço HDInsight.

Para obter mais informações, consulte os documentos seguintes:

- [Portas utilizadas pelos serviços apache Hadoop em HDInsight](./hdinsight-hadoop-port-settings-for-services.md)

- [Tráfego de entrada segura para clusters HDInsight em uma rede virtual com ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Gestão de endereços IP do HDInsight](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Posso colocar uma máquina virtual adicional dentro da mesma sub-rede que um cluster HDInsight?

Sim, pode colocar uma máquina virtual adicional dentro da mesma sub-rede que um cluster HDInsight. As seguintes configurações são possíveis:

- Nós de borda: Pode adicionar outro nó de borda ao cluster, como descrito no [Use os nós de borda vazia nos aglomerados Apache Hadoop em HDInsight](hdinsight-apps-use-edge-node.md).

- Nós autónomos: Pode adicionar uma máquina virtual autónoma à mesma sub-rede e aceder ao cluster a partir dessa máquina virtual utilizando o ponto final privado `https://<CLUSTERNAME>-int.azurehdinsight.net` . Para obter mais informações, consulte [o tráfego da rede de controlo.](./control-network-traffic.md)

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Devo armazenar dados no disco local de um nó de borda?

Não, armazenar dados num disco local não é uma boa ideia. Se o nó falhar, todos os dados armazenados localmente serão perdidos. Recomendamos armazenar dados no armazenamento de Azure Data Lake Storage Gen2 ou Azure Blob, ou através da montagem de uma partilha de Ficheiros Azure para armazenar os dados.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Posso adicionar um cluster HDInsight existente a outra rede virtual?

Não, não podes. A rede virtual deve ser especificada no momento do provisionamento. Se nenhuma rede virtual for especificada durante o fornecimento, a implementação cria uma rede interna que não está acessível a partir do exterior. Para obter mais informações, consulte [Add HDInsight a uma rede virtual existente.](hdinsight-plan-virtual-network-deployment.md#existingvnet)

## <a name="security-and-certificates"></a>Segurança e Certificados

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Quais são as recomendações para a proteção de malware nos clusters Azure HDInsight?

Para obter informações sobre a proteção contra malware, consulte [o Microsoft Antimalware para serviços de nuvem azure e máquinas virtuais.](../security/fundamentals/antimalware.md)

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Como posso criar um keytab para um cluster HDInsight ESP?

Crie um keytab Kerberos para o seu nome de utilizador de domínio. Mais tarde, pode utilizar este teclado para autenticar em agrupamentos remotos de união de domínios sem introduzir uma palavra-passe. O nome de domínio é maiúscula:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Posso usar um inquilino existente do Azure Ative Directory para criar um cluster HDInsight que tenha o ESP?

Ativar os Serviços de Domínio do Diretório Ativo Azure (Azure AD DS) antes de criar um cluster HDInsight com ESP. A Hadoop de código aberto conta com Kerberos para autenticação (em oposição à OAuth).

Para juntar VMs a um domínio, tem de ter um controlador de domínio. Azure AD DS é o controlador de domínio gerido, e é considerado uma extensão do Azure Ative Directory. A Azure AD DS fornece todos os requisitos de Kerberos para construir um cluster Hadoop seguro de forma gerida. HDInsight como um serviço gerido integra-se com Azure AD DS para fornecer segurança.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Posso usar um certificado auto-assinado numa configuração LDAP segura da AAD-DS e providenciar um cluster ESP?

Recomenda-se a utilização de um certificado emitido por uma autoridade de certificados. Mas a utilização de um certificado auto-assinado também é apoiada em ESP. Para obter mais informações, consulte:

- [Ativar o Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Tutorial: Configurar LDAP seguro para um domínio gerido por Azure Ative Directory Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Como posso puxar a atividade de login mostrada no Ranger?

Para os requisitos de auditoria, a Microsoft recomenda que os registos do Monitor Azure, conforme descrito nos [registos do Monitor de Utilização, monitorizem os clusters HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Posso desativar `Clamscan` o meu aglomerado?

`Clamscan` é o software antivírus que funciona no cluster HDInsight e é usado pela segurança Azure (azsecd) para proteger os seus clusters de ataques de vírus. A Microsoft recomenda vivamente que os utilizadores se abstenham de esmundo em fazer quaisquer alterações à `Clamscan` configuração padrão.

Este processo não interfere nem retira quaisquer ciclos de outros processos. Sempre se renderá a outros processos. Os picos de CPU `Clamscan` só devem ser vistos quando o sistema está inativo.  

Em cenários em que deve controlar o horário, pode utilizar os seguintes passos:

1. Desativar a execução automática utilizando o seguinte comando:
   
  sudo `usr/local/bin/azsecd config -s clamav -d Disabled` sudo serviço azsecd reiniciar 
   
1. Adicione um trabalho cron que executa o seguinte comando como raiz:
   
   `/usr/local/bin/azsecd manual -s clamav`

Para mais informações sobre como configurar e gerir um trabalho de cron, vê [como é que eu criei um trabalho cron?](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Porque é que a LLAP está disponível em clusters Spark ESP?
LLAP está ativado por razões de segurança (Apache Ranger), não desempenho. Utilize VMs de nó maior para acomodar para a utilização de recursos de LLAP (por exemplo, d13V2 mínimo). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Como posso adicionar grupos AAD adicionais depois de criar um cluster ESP?
Há duas formas de atingir esse objetivo: 1- Pode recriar o cluster e adicionar o grupo adicional no momento da criação do cluster. Se estiver a utilizar uma sincronização de âmbito em AAD-DS, certifique-se de que o grupo B está incluído na sincronização de âmbito.
2- Adicione o grupo como um subgrupo aninhado do grupo anterior que foi usado para criar o cluster ESP. Por exemplo, se criou um cluster ESP com `A` grupo, pode mais tarde adicionar o grupo `B` como um subgrupo aninhado `A` de e após aproximadamente uma hora será sincronizado e disponível automaticamente no cluster. 

## <a name="storage"></a>Armazenamento

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Posso adicionar um Azure Data Lake Storage Gen2 a um cluster HDInsight existente como uma conta de armazenamento adicional?

Não, atualmente não é possível adicionar uma conta de armazenamento Azure Data Lake Gen2 a um cluster que tem armazenamento de bolhas como o seu armazenamento principal. Para obter mais informações, consulte [as opções de armazenamento compare.](hdinsight-hadoop-compare-storage-options.md)

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Como posso encontrar o diretor de serviço atualmente ligado para uma conta de armazenamento do Data Lake?

Pode encontrar as suas definições no **acesso da Data Lake Storage Gen1** sob as suas propriedades de cluster no portal Azure. Para obter mais informações, consulte [verificar a configuração do cluster](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Como posso calcular o uso de contas de armazenamento e recipientes blob para os meus clusters HDInsight?

Faça uma das seguintes ações:

- [Utilizar o PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Encontre o tamanho do */user/hiv/. Lixo/* pasta no cluster HDInsight, utilizando a seguinte linha de comando:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Como posso fazer auditoria para a minha conta de armazenamento de bolhas?

Para auditar as contas de armazenamento de bolhas, configurar a monitorização utilizando o procedimento no [Monitor uma conta de armazenamento no portal Azure](../storage/common/manage-storage-analytics-logs.md). Um registo de auditoria HDFS fornece apenas informações de auditoria para o sistema de ficheiros HDFS local (hdfs://mycluster).  Não inclui operações que são feitas em armazenamento remoto.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Como posso transferir ficheiros entre um recipiente de bolhas e um nó de cabeça HDInsight?

Executar um script semelhante ao seguinte script da concha no nó da cabeça:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> O ficheiro *filenames.txt* terá o caminho absoluto dos ficheiros nos recipientes blob.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Há algum plugin ranger para armazenamento?

Atualmente, não existe nenhum plugin Ranger para armazenamento de bolhas e Azure Data Lake Storage Gen1 ou Gen2. Para clusters ESP, você deve usar Azure Data Lake Storage. Pode pelo menos definir permissões de grãos finos manualmente ao nível do sistema de ficheiros utilizando ferramentas HDFS. Além disso, ao utilizar o Azure Data Lake Storage, os clusters ESP farão parte do controlo de acesso ao sistema de ficheiros utilizando o Azure Ative Directory ao nível do cluster. 

Pode atribuir políticas de acesso a dados aos grupos de segurança dos seus utilizadores utilizando o Azure Storage Explorer. Para obter mais informações, consulte:

- [Como posso definir permissões para os utilizadores de Azure AD consultarem dados na Data Lake Storage Gen2 utilizando a Hive ou outros serviços?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Definir permissões de arquivo e nível de diretório usando Azure Storage Explorer com Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Posso aumentar o armazenamento de HDFS num cluster sem aumentar o tamanho do disco dos nós dos trabalhadores?

N.º Não se pode aumentar o tamanho do disco de qualquer nó de trabalhador. Assim, a única maneira de aumentar o tamanho do disco é largar o cluster e recriá-lo com VMs maiores. Não utilize o HDFS para armazenar nenhum dos seus dados HDInsight, porque os dados são eliminados se eliminar o seu cluster. Em vez disso, guarde os seus dados em Azure. A ampliação do cluster também pode adicionar capacidade adicional ao seu cluster HDInsight.

## <a name="edge-nodes"></a>Nós periféricos

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Posso adicionar um nó de borda depois de o cluster ter sido criado?

Ver [Utilizar nós de borda vazia nos aglomerados Apache Hadoop em HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Como posso ligar-me a um nó de borda?

Depois de criar um nó de borda, pode ligar-se a ele utilizando o SSH na porta 22. Pode encontrar o nome do nó de borda do portal de cluster. Os nomes geralmente terminam com *-ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Porque é que os scripts persistidos não são funcionando automaticamente em nós de borda recém-criados?

Usa scripts persistidos para personalizar novos nós de trabalhador adicionados ao cluster através de operações de escala. Scripts persistidos não se aplicam aos nós de borda.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>O que é que a API REST chama para tirar uma vista de consulta de Tez do cluster?

Pode utilizar os seguintes pontos finais REST para retirar as informações necessárias no formato JSON. Utilize cabeçalhos básicos de autenticação para fazer os pedidos.

- `Tez Query View`: *https: \/ / \<cluster name> .azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https: \/ / \<cluster name> .azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Como posso recuperar os detalhes de configuração do cluster HDI utilizando um utilizador do Azure Ative Directory?

Para negociar fichas de autenticação adequadas com o seu utilizador AAD, passe pelo gateway utilizando o seguinte formato:

* https:// `<cluster dnsname>` .azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Como uso a AMBAri Restful API para monitorizar o desempenho do YARN?

Se ligar para o comando Curl na mesma rede virtual ou numa rede virtual esprevada, o comando é:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Se ligar para o comando de fora da rede virtual ou de uma rede virtual não espreitada, o formato de comando é:

- Para um cluster não-ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Para um cluster ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> O curl vai pedir-lhe uma senha. Tem de introduzir uma palavra-passe válida para o nome de utilizador do login do cluster.

## <a name="billing"></a>Faturação

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto custa implantar um cluster HDInsight?

Para obter mais informações sobre preços e FAQ relacionados com a faturação, consulte a página [de preços Azure HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>Quando é que a faturação da HDInsight começa & parar?

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é a favor por minuto.

### <a name="how-do-i-cancel-my-subscription"></a>Como cancelo a minha assinatura?

Para obter informações sobre como cancelar a sua subscrição, consulte [cancelar a subscrição do Azure](../cost-management-billing/manage/cancel-azure-subscription.md).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Para assinaturas pay-as-you-go, o que acontece depois de eu cancelar a minha assinatura?

Para obter informações sobre a sua subscrição depois de ser cancelada, veja [o que acontece depois de eu cancelar a minha subscrição?](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Porque é que a versão Hive aparece como 1.2.1000 em vez de 2.1 na UI Ambari, apesar de estar a gerir um cluster HDInsight 3.6?

Embora apenas 1.2 apareça na UI Ambari, HDInsight 3.6 contém tanto a Colmeia 1.2 como a Hive 2.1.

## <a name="other-faq"></a>Outras FAQ

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>O que é que o HDInsight oferece para capacidades de processamento de fluxo em tempo real?

Para obter informações sobre as capacidades de integração do processamento de streaming, consulte [escolher uma tecnologia de processamento de fluxo em Azure.](/azure/architecture/data-guide/technology-choices/stream-processing)

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existe uma maneira de matar dinamicamente o nó da cabeça do aglomerado quando o cluster está inativo por um período específico?

Não podes fazer esta ação com clusters HDInsight. Pode utilizar a Azure Data Factory para estes cenários.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Que ofertas de conformidade a HDInsight oferece?

Para obter informações de conformidade, consulte o [Microsoft Trust Center](https://www.microsoft.com/trust-center) e a [visão geral da conformidade do Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).