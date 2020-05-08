---
title: Azure HDInsight frequentemente fez perguntas
description: Perguntas frequentes sobre hDInsight
keywords: perguntas frequentemente feitas, faq
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 8a69cb83492fabc692886fe6966a147de3bcbb04
ms.sourcegitcommit: 31236e3de7f1933be246d1bfeb9a517644eacd61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82780849"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Frequently asked questions (Autenticação Pass-through: Perguntas mais frequentes)

Este artigo fornece respostas a algumas das perguntas mais comuns sobre como executar [O Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Criar ou eliminar clusters do HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Como posso fornecer um cluster HDInsight?

Para rever os tipos de clusters HDInsight, e os métodos de provisionamento, consulte [Conjunto de clusters em HDInsight com Apache Hadoop, Apache Spark, Apache Kafka, e muito mais](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Como posso excluir um cluster HDInsight existente?

Para saber mais sobre a eliminação de um cluster quando já não estiver a ser utilizado, consulte [Eliminar um cluster HDInsight](hdinsight-delete-cluster.md).

Tente deixar pelo menos 30 a 60 minutos entre criar e apagar operações. Caso contrário, a operação pode falhar com a seguinte mensagem de erro:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Como seleciono o número correto de núcleos ou nós para a minha carga de trabalho?

O número adequado de núcleos e outras opções de configuração dependem de vários fatores.

Para mais informações, consulte o [planeamento da capacidade para os clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quais são os vários tipos de nós num cluster HDInsight?

Consulte [os tipos de recursos nos clusters Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Componentes Individuais

### <a name="can-i-install-additional-components-on-my-cluster"></a>Posso instalar componentes adicionais no meu cluster?

Sim. Para instalar componentes adicionais ou personalizar a configuração do cluster, utilize:

- Scripts durante ou após a criação. Os scripts são invocados através da ação do [guião.](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux) A ação do script é uma opção de configuração que pode utilizar a partir do portal Azure, HDInsight Windows PowerShell cmdlets ou o HDInsight .NET SDK. Esta opção de configuração pode ser utilizada a partir do portal Azure, HDInsight Windows PowerShell cmdlets, ou do HDInsight .NET SDK.

- [Plataforma de aplicação HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) para instalar aplicações.

Para obter uma lista de componentes suportados, veja [quais são os componentes e versões Apache Hadoop disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Posso atualizar os componentes individuais que estão pré-instalados no cluster?

Se atualizar componentes ou aplicações incorporados que estejam pré-instalados no seu cluster, a configuração resultante não será suportada pela Microsoft. Estas configurações do sistema não foram testadas pela Microsoft. Tente utilizar uma versão diferente do cluster HDInsight que pode já ter a versão atualizada do componente pré-instalada.

Por exemplo, a atualização da Colmeia como componente individual não é suportada. O HDInsight é um serviço gerido, e muitos serviços estão integrados com o servidor Ambari e testados. A atualização de uma Colmeia por si só faz com que os binários indexados de outros componentes mudem e causará problemas de integração de componentes no seu cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>A Spark e a Kafka podem correr no mesmo cluster HDInsight?

Não, não é possível executar Apache Kafka e Apache Spark no mesmo cluster HDInsight. Crie clusters separados para Kafka e Spark para evitar problemas de contenção de recursos.

### <a name="how-do-i-change-timezone-in-ambari"></a>Como posso mudar o fuso horário em Ambari?

1. Abra o Ambari Web `https://CLUSTERNAME.azurehdinsight.net`UI em, onde clusterNAME é o nome do seu cluster.
2. No canto superior direito, selecione administrador / Configurações. 

   ![Definições ambari](media/hdinsight-faq/ambari-settings.png)

3. Na janela Definições do Utilizador, selecione o novo fuso horário do fuso horário e, em seguida, clique em Guardar.

   ![Definições de utilizador ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Como posso migrar da metaloja existente para o Servidor Azure SQL? 

Para migrar do SQL Server para o Servidor SQL Azure, consulte [Tutorial: Migrar o SQL Server para uma única base de dados ou base de dados pooled em Azure SQL Database offline usando DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>A metaloja da Colmeia é apagada quando o cluster é apagado?

Depende do tipo de metaloja que o seu cluster está configurado para usar.

Para uma metaloja predefinida: A metaloja predefinida faz parte do ciclo de vida do cluster. Quando elimina um cluster, a metaloja correspondente e os metadados também são eliminados.

Para uma metaloja personalizada: O ciclo de vida da metaloja não está ligado ao ciclo de vida de um cluster. Assim, pode criar e eliminar clusters sem perder metadados. Metadados como os seus schemas da Hive persistem mesmo depois de eliminar e recriar o cluster HDInsight.

Para mais informações, consulte [Utilize lojas de metadados externos no Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>A migração de uma metaloja da Hive também migra as políticas padrão da base de dados ranger?

Não, a definição de política está na base de dados dos Rangers, por isso, migrar a base de dados dos Rangers vai migrar a sua política.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Pode migrar uma metaloja da Hive de um cluster do Pacote de Segurança Empresarial (ESP) para um cluster não ESP, e o contrário?

Sim, você pode migrar uma metaloja da Colmeia de um ESP para um cluster não ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Como posso estimar o tamanho de uma base de dados de metaloja da Hive?

Uma metaloja da Hive é usada para armazenar os metadados para fontes de dados que são usadas pelo servidor Hive. Os requisitos de tamanho dependem em parte do número e complexidade das suas fontes de dados da Hive. Estes itens não podem ser estimados adiantados. Tal como delineado nas diretrizes da [Metastore da Hive,](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines)pode começar com um nível S2. O nível fornece 50 DTU e 250 GB de armazenamento, e se você vir um estrangulamento, dimensione a base de dados.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Suporta outra base de dados que não a Base de Dados Azure SQL como uma metaloja externa?

Não, a Microsoft suporta apenas a Base de Dados Azure SQL como uma metaloja personalizada externa.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Posso partilhar uma metaloja em vários aglomerados?

Sim, pode partilhar metaloja personalizada em vários clusters desde que utilizem a mesma versão do HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Conectividade e redes virtuais  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quais são as implicações do bloqueio dos portos 22 e 23 na minha rede?

Se bloquear as portas 22 e a porta 23, não terá acesso sSH ao cluster. Estas portas não são utilizadas pelo serviço HDInsight.

Para obter mais informações, consulte os documentos seguintes:

- [Tráfego de rede de controlo](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)

- [Tráfego de entrada seguro para clusters HDInsight em uma rede virtual com ponto final privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Endereços IP de gestão HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Posso implantar uma máquina virtual adicional dentro da mesma sub-rede que um cluster HDInsight?

Sim, pode implantar uma máquina virtual adicional dentro da mesma sub-rede que um cluster HDInsight. As seguintes configurações são possíveis:

- Nós de borda: Pode adicionar outro nó de borda ao cluster, como descrito em [Usar nós de borda vazias em clusters Apache Hadoop em HDInsight](hdinsight-apps-use-edge-node.md).

- Nós autónomos: Pode adicionar uma máquina virtual autónoma à mesma sub-rede e aceder ao cluster `https://<CLUSTERNAME>-int.azurehdinsight.net`a partir dessa máquina virtual utilizando o ponto final privado . Para mais informações, consulte o [tráfego da rede de controlo.](./control-network-traffic.md)

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Devo armazenar dados no disco local de um nó de borda?

Não, armazenar dados num disco local não é uma boa ideia. Se o nó falhar, todos os dados armazenados localmente serão perdidos. Recomendamos armazenar dados no armazenamento de lagos Azure Data Gen2 ou Azure Blob, ou através da montagem de uma quota azure Files para armazenar os dados.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Posso adicionar um cluster HDInsight existente a outra rede virtual?

Não, não podes. A rede virtual deve ser especificada no momento do fornecimento. Se não for especificada nenhuma rede virtual durante o fornecimento, a implementação cria uma rede interna que não é acessível a partir do exterior. Para mais informações, consulte [Adicionar HDInsight a uma rede virtual existente](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Segurança e Certificados

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Quais são as recomendações para a proteção contra malware em clusters Azure HDInsight?

Para obter informações sobre a proteção contra malware, consulte [o Microsoft Antimalware para Serviços de Nuvem Azure e Máquinas Virtuais](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Como posso criar um separador de chaves para um cluster esp HDInsight?

Crie um separador de teclas Kerberos para o seu nome de utilizador de domínio. Mais tarde, pode utilizar este separador de chaves para autenticar aglomerados unidados a domínios remotos sem introduzir uma palavra-passe. O nome de domínio é maiúsculo:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Posso usar um inquilino do Azure Ative Directory existente para criar um cluster HDInsight que tenha o ESP?

Ative Azure Ative Directory Domain Services (Azure AD DS) antes de criar um cluster HDInsight com ESP. Hadoop de código aberto depende de Kerberos para autenticação (ao contrário de OAuth).

Para se juntar a VMs a um domínio, deve ter um controlador de domínio. Azure AD DS é o controlador de domínio gerido, e é considerado uma extensão do Diretório Ativo Azure. A Azure AD DS fornece todos os requisitos kerberos para construir um cluster Hadoop seguro de uma forma gerida. O HDInsight como serviço gerido integra-se com o Azure AD DS para garantir a segurança.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Posso usar um certificado auto-assinado numa configuração LDAP segura AAD-DS e fornecer um cluster ESP?

Recomenda-se a utilização de um certificado emitido por uma autoridade de certificados. Mas a utilização de um certificado auto-assinado também é apoiada na ESP. Para obter mais informações, consulte:

- [Ativar o Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Tutorial: Configure o LDAP seguro para um domínio gerido pelo Azure Ative Directory Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Como posso fazer a atividade de login mostrada no Ranger?

Para os requisitos de auditoria, a Microsoft recomenda ativar os registos do Monitor Azure conforme descrito nos [registos do Monitor De utilização do Monitor Azure para monitorizar os clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Posso desativar `Clamscan` no meu aglomerado?

`Clamscan`é o software antivírus que funciona no cluster HDInsight e é usado pela segurança Azure (azsecd) para proteger os seus clusters de ataques de vírus. A Microsoft recomenda vivamente que os utilizadores `Clamscan` se abstenham de fazer quaisquer alterações à configuração predefinida.

Este processo não interfere ou afasta quaisquer ciclos de outros processos. Cederá sempre a outro processo. Os picos de `Clamscan` CPU só devem ser vistos quando o sistema está inativo.  

Nos cenários em que deve controlar o horário, pode utilizar os seguintes passos:

1. Desative a execução automática utilizando o seguinte comando:
   
   `/usr/local/vbin/azsecd config -s clamav -d Disabled`
   
1. Adicione um trabalho cron que executa o seguinte comando como raiz:
   
   `/usr/local/bin/azsecd manual -s clamav`

Para mais informações sobre como configurar e gerir um trabalho de amigos, ver [como posso configurar um trabalho](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)de Cron?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Porque é que a LLAP está disponível nos clusters Spark ESP?
O LLAP está ativado por razões de segurança (Apache Ranger), não por desempenho. Utilize VMs de nó maiores para acomodar a utilização de recursos de LLAP (por exemplo, d13V2 mínimo). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Como posso adicionar grupos AAD adicionais depois de criar um cluster ESP?
Há duas formas de atingir este objetivo: 1- Pode recriar o cluster e adicionar o grupo adicional no momento da criação do cluster. Se estiver a utilizar sincronização com âmbito em AAD-DS, certifique-se de que o grupo B está incluído na sincronização com âmbito.
2- Adicione o grupo como um subgrupo aninhado do grupo anterior que foi usado para criar o cluster ESP. Por exemplo, se criou um cluster ESP com grupo, `A` `B` pode adicionar grupo como `A` um subgrupo aninhado de e após aproximadamente uma hora será sincronizado e disponível automaticamente no cluster. 

## <a name="storage"></a>Armazenamento

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Posso adicionar um Azure Data Lake Storage Gen2 a um cluster HDInsight existente como uma conta de armazenamento adicional?

Não, atualmente não é possível adicionar uma conta de armazenamento de Armazenamento de Lago De dados Azure gen2 a um cluster que tem armazenamento blob como seu armazenamento principal. Para mais informações, consulte [Compare opções](hdinsight-hadoop-compare-storage-options.md)de armazenamento .

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Como posso encontrar o atual diretor de serviço seletiva para uma conta de armazenamento de Data Lake?

Pode encontrar as suas definições no **acesso da Gen1** de Armazenamento de Data Lake sob as suas propriedades de cluster no portal Azure. Para mais informações, consulte Verificar a [configuração do cluster](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Como posso calcular o uso de contas de armazenamento e recipientes de blob para os meus clusters HDInsight?

Faça uma das seguintes ações:

- [Utilizar o PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Encontre o tamanho do */utilizador/colmeia/. Lixo/pasta* no cluster HDInsight, utilizando a seguinte linha de comando:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Como posso configurar a auditoria para a minha conta de armazenamento de bolhas?

Para auditar as contas de armazenamento de blob, configure a monitorização utilizando o procedimento no Monitor uma conta de [armazenamento no portal Azure](../storage/common/storage-monitor-storage-account.md). Um registo de auditoria HDFS fornece apenas informações de auditoria para o sistema de ficheiros HDFS local apenas (hdfs://mycluster).  Não inclui operações que são feitas em armazenamento remoto.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Como posso transferir ficheiros entre um recipiente de bolhas e um nó de cabeça HDInsight?

Execute um script semelhante ao seguinte script de concha no seu nó de cabeça:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Os *ficheiros nomes.txt* terão o caminho absoluto dos ficheiros nos recipientes blob.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Há plugins ranger para armazenamento?

Atualmente, não existe nenhum plugin Ranger para armazenamento de blob e Azure Data Lake Storage Gen1 ou Gen2. Para aglomerados ESP, você deve usar o Armazenamento do Lago De Dados Azure. Pode, pelo menos, definir permissões de grãos finos manualmente ao nível do sistema de ficheiros utilizando ferramentas HDFS. Além disso, ao utilizar o Armazenamento de Lagos De Dados Azure, os clusters ESP farão parte do controlo de acesso do sistema de ficheiros utilizando o Azure Ative Directory ao nível do cluster. 

Pode atribuir políticas de acesso a dados aos grupos de segurança dos seus utilizadores utilizando o Azure Storage Explorer. Para obter mais informações, consulte:

- [Como posso definir permissões para os utilizadores de Anúncios Azure consultarem dados no Data Lake Storage Gen2 utilizando a Hive ou outros serviços?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Definir permissões de nível de ficheiros e diretórios usando o Azure Storage Explorer com o Azure Data Lake Storage Gen2](/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Posso aumentar o armazenamento de HDFS num cluster sem aumentar o tamanho do disco dos nós dos trabalhadores?

Não. Não pode aumentar o tamanho do disco de nenhum nó de trabalhador. Assim, a única maneira de aumentar o tamanho do disco é largar o cluster e recriá-lo com VMs de maior escrivão. Não utilize o HDFS para armazenar nenhum dos seus dados HDInsight, porque os dados são eliminados se eliminar o seu cluster. Em vez disso, guarde os seus dados em Azure. A escalação do cluster também pode adicionar capacidade adicional ao seu cluster HDInsight.

## <a name="edge-nodes"></a>Nós periféricos

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Posso adicionar um nó de borda depois que o cluster foi criado?

Consulte [A utilização de nós de borda vazias nos clusters Apache Hadoop em HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Como posso ligar-me a um nó de borda?

Depois de criar um nó de borda, pode ligar-se a ele utilizando SSH na porta 22. Pode encontrar o nome do nó de borda do portal do cluster. Os nomes geralmente terminam com *- ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Porque é que os scripts persistidos não estão a funcionar automaticamente em nós de borda recém-criados?

Usa scripts persistentes para personalizar novos nós de trabalhador adicionados ao cluster através de operações de escala. Guiões persistidos não se aplicam a nódoas de borda.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quais são as chamadas da API para tirar uma vista de consulta tez do cluster?

Pode utilizar os seguintes pontos finais REST para retirar as informações necessárias no formato JSON. Utilize cabeçalhos básicos de autenticação para fazer os pedidos.

- `Tez Query View`: *https:\//\<nome do cluster>.azurehdinsight.net/ws/v1/timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https:\//\<nome do cluster>.azurehdinsight.net/ws/v1/timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Como posso recuperar os detalhes de configuração do cluster HDI utilizando um utilizador do Diretório Ativo Azure?

Para negociar fichas de autenticação adequadas com o utilizador AAD, passe pelo portal utilizando o seguinte formato:

* https://`<cluster dnsname>`.azurehdinsight.net/api/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Como uso a Ambari Restful API para monitorizar o desempenho do ARN?

Se ligar para o comando Curl na mesma rede virtual ou numa rede virtual esparsada, o comando é:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Se ligar para o comando de fora da rede virtual ou de uma rede virtual não-evisteia, o formato de comando é:

- Para um cluster não-ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- Para um aglomerado de ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> O curl vai pedir-lhe uma senha. Tem de introduzir uma palavra-passe válida para o nome de utilizador de login do cluster.

## <a name="billing"></a>Faturação

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto custa implementar um cluster HDInsight?

Para obter mais informações sobre preços e FAQ relacionados com faturação, consulte a página de [preços Do Azure HDInsight.](https://azure.microsoft.com/pricing/details/hdinsight/)

### <a name="when-does-hdinsight-billing-start--stop"></a>Quando é que a faturação da HDInsight começa & parar?

A faturação do cluster do HDInsight tem início quando o cluster é criado e termina quando é eliminado. A faturação é a favor por minuto.

### <a name="how-do-i-cancel-my-subscription"></a>Como cancelo a minha assinatura?

Para obter informações sobre como cancelar a sua subscrição, consulte Cancelar a [subscrição do Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Para assinaturas pagas, o que acontece depois de cancelar a minha assinatura?

Para obter informações sobre a sua subscrição depois de cancelada, veja o que acontece depois de [cancelar a minha subscrição?](/azure/billing/billing-how-to-cancel-azure-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Porque é que a versão da Hive aparece como 1.2.1000 em vez de 2.1 no Ambari UI, apesar de eu estar a executar um cluster HDInsight 3.6?

Embora apenas 1.2 apareça no Ambari UI, o HDInsight 3.6 contém tanto a Hive 1.2 como a Hive 2.1.

## <a name="other-faq"></a>Outras FAQ

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>O que o HDInsight oferece para capacidades de processamento de fluxo em tempo real?

Para obter informações sobre as capacidades de integração do processamento de fluxos, consulte A Escolha de uma tecnologia de processamento de [fluxo sonante em Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Existe uma maneira de matar dinamicamente o nó da cabeça do aglomerado quando o aglomerado está inativo por um período específico?

Não pode fazer esta ação com clusters HDInsight. Pode utilizar a Azure Data Factory para estes cenários.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Que ofertas de conformidade o HDInsight oferece?

Para obter informações sobre conformidade, consulte o [Microsoft Trust Center](https://www.microsoft.com/trust-center) e a visão geral da conformidade do Microsoft [Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).
