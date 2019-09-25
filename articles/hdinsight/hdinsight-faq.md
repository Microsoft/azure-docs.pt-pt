---
title: Azure HDInsight-perguntas frequentes
description: Fornece uma visão geral do Azure HDInsight
author: Ramakoni1
ms.author: ramakoni
ms.reviewer: v-miegge
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/20/2019
ms.openlocfilehash: 4accd8c2f58dd89fb5f918ca6e58e71caeaed57f
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71212748"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Perguntas mais frequentes

Este artigo fornece respostas para algumas das perguntas mais comuns sobre como executar o [Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Criando ou excluindo clusters HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Como fazer provisionar um cluster HDInsight?

Para examinar os tipos de clusters HDInsight disponíveis e os métodos de provisionamento, consulte [Configurar clusters no HDInsight com Apache Hadoop, Apache Spark, Apache Kafka e muito mais](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-provision-linux-clusters).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Como fazer excluir um cluster HDInsight existente?

Para saber mais sobre como excluir um cluster quando ele não estiver mais em uso, consulte [excluir um cluster HDInsight usando seu navegador, PowerShell ou o CLI do Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-delete-cluster). É recomendável que haja pelo menos 30 a 60 minutos entre a operação de criação e exclusão em um único cluster. Caso contrário, a operação poderá falhar e retornar a seguinte mensagem de erro:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Como fazer selecionar o número correto de núcleos ou nós para minha carga de trabalho?

O número apropriado de núcleos e outras opções de configuração dependem de vários fatores.

Para obter mais informações, consulte [planejamento de capacidade para clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning).

### <a name="what-can-i-do-when-cluster-provisioning-fails-because-of-a-capacity-issue"></a>O que posso fazer quando o provisionamento de cluster falhar devido a um problema de capacidade?

Os erros comuns de problema de capacidade e técnicas de mitigação são fornecidos nesta seção.

#### <a name="error-the-deployment-would-exceed-the-quota-of-800"></a>Erro: A implantação excederia a cota de ' 800 '

O Azure tem um limite de quota de 800 implementações por grupo de recursos. Cotas diferentes são aplicadas por grupo de recursos, assinatura, conta ou outros escopos. Por exemplo, a subscrição pode ser configurada para limitar o número de núcleos de uma região. Se você tentar implantar uma máquina virtual que tenha mais núcleos do que a quantidade permitida, receberá uma mensagem de erro indicando que a cota foi excedida.

Para resolver esse problema, exclua as implantações que não são mais necessárias usando o portal do Azure, a CLI ou o PowerShell.

Para obter mais informações, veja [Resolver erros de quotas de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quota-errors).

#### <a name="error-the-maximum-node-exceeded-the-available-cores-in-this-region"></a>Erro: O nó máximo excedeu os núcleos disponíveis nesta região

A subscrição pode ser configurada para limitar o número de núcleos de uma região. Se você tentar implantar um recurso que tenha mais núcleos do que o valor permitido, receberá uma mensagem de erro indicando que a cota foi excedida.

Para pedir um aumento da quota, sigas os passos seguintes:

1. Vá para a portal do Azure e selecione ajuda + suporte.
2. Selecione nova solicitação de suporte.
3. Na página nova solicitação de suporte, selecione as seguintes opções na guia noções básicas:

   * Tipo de problema: Limites de serviço e subscrição (quotas)
   * Subscrição: A assinatura que você deseja modificar
   * Tipo de quota: HDInsight

Para obter mais informações, consulte [criar um tíquete de suporte para aumentar os núcleos](https://docs.microsoft.com/azure/hdinsight/hdinsight-capacity-planning#quotas).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Quais são os vários tipos de nós em um cluster HDInsight?

Os clusters do Azure HDInsight têm diferentes tipos de máquinas virtuais ou nós. Cada tipo de nó desempenha uma função na operação do sistema.

Para obter mais informações, consulte [tipos de recursos em clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-virtual-network-architecture#resource-types-in-azure-hdinsight-clusters).

## <a name="individual-components"></a>Componentes individuais

### <a name="can-i-install-additional-components-on-my-cluster"></a>Posso instalar componentes adicionais no meu cluster?

Sim, você pode instalar componentes adicionais ou personalizar a configuração do cluster usando qualquer um dos seguintes métodos:

* Usando scripts durante ou após a criação. Esses scripts são invocados por meio de uma [ação de script](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-customize-cluster-linux). Essa opção de configuração pode ser usada no portal do Azure, nos cmdlets do Windows PowerShell do HDInsight ou no SDK do .NET do HDInsight. 
* Usando o sudo ou outros métodos depois que o cluster é provisionado.
* Usando a [plataforma de aplicativo do HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) para instalar aplicativos de ecossistema.

No entanto, Suporte da Microsoft equipes podem oferecer suporte apenas para as seguintes situações:

* Problemas ou erros que ocorrem durante o carregamento do script. Todos os erros que ocorrem durante a execução de scripts personalizados estão fora do escopo de um tíquete de suporte. 
* Aplicativos adicionais que fazem parte do processo de criação de cluster. 

Para obter uma lista de componentes com suporte [, consulte Quais são os componentes Apache Hadoop e as versões disponíveis com o HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#apache-hadoop-components-available-with-different-hdinsight-versions)

o suporte para componentes individuais também pode variar por tipo de cluster. Por exemplo, o Spark não tem suporte em um cluster Kafka e vice-versa.

Para aplicativos ou serviços fora do processo de criação de cluster, entre em contato com o respectivo fornecedor ou provedor de serviços para obter suporte. Você também pode usar sites da Comunidade para obter suporte para essas ações. Muitos sites de comunidade estão disponíveis. Exemplos incluem o [Fórum do MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/home?forum=hdinsight) e [Stack Overflow](https://stackoverflow.com/questions/tagged/hdinsight). Os projetos do Apache também têm sites de projetos no [site do Apache](https://apache.org/). Um exemplo é o [Hadoop](https://hadoop.apache.org/).

 Para obter mais perguntas relacionadas ao suporte do Azure, examine as [perguntas frequentes sobre o suporte do Azure](https://azure.microsoft.com/en-us/support/faq/).

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Posso atualizar os componentes individuais que estão pré-instalados no cluster?

Se você atualizar componentes internos ou aplicativos pré-instalados em seu cluster, a configuração resultante não será suportada pela Microsoft. Essas configurações do sistema não foram testadas pela Microsoft. Tente usar uma versão diferente do cluster HDInsight que já pode ter a versão atualizada do componente pré-instalada.

Por exemplo, não há suporte para a atualização do hive como um componente individual. O HDInsight é um serviço gerenciado, e muitos serviços são integrados ao servidor Ambari e testados. Atualizar um Hive por conta própria faz com que os binários indexados de outros componentes sejam alterados e causarão problemas de integração de componentes no cluster.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>O Spark e o Kafka podem ser executados no mesmo cluster HDInsight?

Não, não é possível executar Apache Kafka e Apache Spark no mesmo cluster HDInsight. Os usuários devem criar clusters separados para cada um para evitar problemas de contenção de recursos.

### <a name="how-do-i-change-timezone-in-ambari"></a>Como fazer alterar o fuso horário em Ambari?

1. Abra a interface do usuário da https://CLUSTERNAME.azurehdinsight.net Web do amAmbari em, em que ClusterName é o nome do cluster.
2. No canto superior direito, selecione Admin | Configurações. 

   ![Configurações de Ambari](media/hdinsight-faq/ambari-settings.png)

3. Na janela Configurações do usuário, selecione o fuso horário novo na lista suspensa fuso horário e clique em salvar.

   ![Configurações de usuário do Ambari](media/hdinsight-faq/ambari-user-settings.png)

## <a name="metastore"></a>Metastore

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-server"></a>Como posso migrar do metastore existente para o Azure SQL Server? 

Para migrar do SQL Server para o SQL Server do [Azure, consulte o tutorial: Migre SQL Server para um banco de dados individual ou banco de dados em pool no banco de](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-azure-sql)dados SQL do Azure offline usando DMS.

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>O metastore do Hive excluído quando o cluster é excluído?

Depende do tipo de metastore que o cluster está configurado para usar.

Para um metastore padrão: O metastore padrão faz parte do ciclo de vida do cluster. Quando você exclui um cluster, o metastore correspondente e os metadados também são excluídos.

Para um metastore personalizado: O ciclo de vida do metastore não está vinculado ao ciclo de vida de um cluster. Portanto, você pode criar e excluir clusters sem perder metadados. Os metadados como os esquemas do hive persistem mesmo depois que você exclui e recria o cluster HDInsight.

Para obter mais informações, consulte [usar repositórios de metadados externos no Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>A migração de um metastore do Hive também migra as políticas padrão do banco de dados do Ranger?

Não, a definição de política está no banco de dados do Ranger. Portanto, migrar o banco de dados do Ranger não migra a política.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-vice-versa"></a>É possível migrar um metastore do Hive de um cluster de Enterprise Security Package (ESP) para um cluster não ESP e vice-versa?

Sim, você pode migrar um metastore do Hive de um ESP para um cluster não ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Como posso estimar o tamanho de um banco de dados metastore do Hive?

Um metastore do Hive é usado para armazenar os metadados de fontes de dados que são usados pelo servidor do hive. Portanto, os requisitos de tamanho são afetados pelo número de fontes de dados que você pode precisar usar para o hive e pela complexidade das fontes de dados. Portanto, o tamanho não pode ser estimado antecipadamente. Conforme descrito em [metastore do hive práticas recomendadas](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-external-metadata-stores#hive-metastore-best-practices), a partir de uma camada S2 fornece 50 DTU e 250 GB de armazenamento. Se você encontrar um afunilamento, poderá escalar verticalmente o banco de dados.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Há suporte para qualquer outro banco de dados que não seja o banco de dados SQL do Azure como um metastore externo?

Não, a Microsoft dá suporte apenas ao banco de dados SQL do Azure como um metastore personalizado externo.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Posso compartilhar um metastore em vários clusters?

Sim, você pode compartilhar o metastore personalizado em vários clusters, desde que eles estejam usando a mesma versão do HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Conectividade e redes virtuais  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Quais são as implicações de bloquear as portas 22 e 23 em minha rede?

Se você bloquear as portas 22 e 23, não terá acesso SSH ao cluster. Essas portas não são usadas pelo serviço HDInsight.

Para obter mais informações, consulte os seguintes documentos:

* [Controlando o tráfego de rede](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic)
* [Proteger o tráfego de entrada para clusters HDInsight em uma rede virtual com ponto de extremidade privado](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)
* [Endereços IP de gerenciamento do HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-management-ip-addresses)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Posso implantar uma máquina virtual adicional na mesma sub-rede que um cluster HDInsight?

Sim, você pode implantar uma máquina virtual adicional na mesma sub-rede que um cluster HDInsight. As seguintes configurações são possíveis:

* Nós de borda: Você pode adicionar outro nó de borda ao cluster, conforme descrito em [usar nós de borda vazios em clusters de Apache Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node). 
* Nós autônomos:  Você pode adicionar uma máquina virtual autônoma à mesma sub-rede e acessar o cluster a partir dessa máquina virtual usando o ponto `https://<CLUSTERNAME>-int.azurehdinsight.net`de extremidade privado. Para obter mais informações, consulte [controlando o tráfego de rede](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#networktraffic).

### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Posso adicionar um cluster HDInsight existente a outra rede virtual?

Não, você não pode adicionar um cluster HDInsight existente a outra rede virtual. A rede virtual deve ser especificada no momento do provisionamento. Se nenhuma rede virtual for especificada durante o provisionamento, a implantação criará uma rede interna que não pode ser acessada de fora. Para obter mais informações, consulte [Adicionar o HDInsight a uma rede virtual existente](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#existingvnet).

## <a name="security-and-certificates"></a>Segurança e certificados

Quais são as recomendações da Microsoft para proteção contra malware em clusters de HD insights do Azure?

Para obter informações sobre proteção contra malware, consulte [Microsoft antimalware para serviços de nuvem do Azure e máquinas virtuais](https://docs.microsoft.com/azure/security/fundamentals/antimalware).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Como fazer criar um keytab para um cluster de ESP do HDInsight?

Você pode criar um Kerberos keytab para seu nome de usuário de domínio. Posteriormente, você pode usar esse keytab para autenticar em clusters remotos ingressados no domínio sem inserir uma senha. Observe os caracteres MAIÚSCULOs no nome de domínio:

```
o ktutil 
o ktutil: addent -password -p stXX@ DOMAIN.COM -k 1 -e RC4-HMAC 
o Password for stXX@DOMAIN.COM: provided by lab instructors 
o ktutil: wkt stXX.keytab 
o ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Posso usar um locatário de Azure Active Directory existente para criar um cluster HDInsight que tem o ESP?

Habilitar Azure Active Directory Domain Services (AAD-DS) é um pré-requisito antes de criar um cluster HDInsight com ESP. O Hadoop de software livre depende do Kerberos para autenticação (em oposição ao OAuth).

Para unir máquinas virtuais a um domínio, um controlador de domínio é necessário. AAD-DS é o controlador de domínio gerenciado. Ele é considerado uma extensão do AAD que fornece todos os requisitos de Kerberos para criar um cluster Hadoop seguro de uma maneira gerenciada. O HDInsight como um serviço gerenciado integra-se ao AAD-DS para fornecer a segurança de ponta a ponta.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Posso usar um certificado autoassinado em uma instalação LDAP segura do AAD-DS e provisionar um cluster ESP?

Embora o uso de um certificado emitido por uma autoridade de certificação (CA) seja recomendado, o uso de um certificado autoassinado também tem suporte no ESP.

Para obter mais informações, consulte:

* [Habilitar Azure AD – DS](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds#enable-azure-ad-ds)
* [Tutorial: Configurar o LDAP seguro para um domínio gerenciado Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-configure-ldaps)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Como posso efetuar pull da atividade de logon mostrada no Ranger?

Para requisitos de auditoria, a Microsoft recomenda Habilitar logs de Azure Monitor conforme descrito em [usar logs de Azure monitor para monitorar clusters HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-oms-log-analytics-tutorial).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Posso desabilitar o clamscan em meu cluster?

Clamscan é o software antivírus executado no cluster HDInsight e é usado pela azsecd (segurança do Azure) para proteger seus clusters contra ataques de vírus. A Microsoft recomenda enfaticamente que os usuários evitem fazer alterações na configuração padrão do clamscan.

Esse processo não interfere nem demora nenhum ciclo para outros processos. Ele sempre resultará em outro processo. Os picos de CPU de clamscan devem ser vistos somente quando o sistema estiver ocioso.  

Em cenários nos quais você deve controlar a agenda, você pode usar as seguintes etapas:

1. Desabilite a execução automática usando o seguinte comando:

   ``/usr/local/vbin/azsecd config -s clamav -d Disabled``

2. Crie um trabalho cron que execute este comando como raiz:

   ``/usr/local/bin/azsecd manual -s clamav``

Para obter mais informações sobre como configurar e executar um trabalho cron, consulte [como fazer configurar um trabalho cron](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

## <a name="storage"></a>Armazenamento

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Posso adicionar um Azure Data Lake Storage Gen2 a um cluster HDInsight existente como uma conta de armazenamento adicional?

Não. No momento, não é possível adicionar um Azure Data Lake Storage Gen2 a um cluster HDInsight existente que usa o armazenamento de BLOBs como seu armazenamento primário. Para obter mais informações, consulte [comparar as opções de armazenamento para uso com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options). 

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Como posso encontrar a entidade de serviço vinculada atualmente para uma conta de armazenamento Data Lake?

Você pode verificar essas configurações examinando Data Lake Storage Gen1 acesso nas propriedades do cluster no Portal. Para obter mais informações, consulte [verificar a configuração do cluster](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-hdinsight-hadoop-use-portal#verify-cluster-set-up).
 
Como calcular o uso de contas de armazenamento e contêineres de BLOB para meus clusters HDInsight?
Você pode usar um dos seguintes procedimentos:

* [Utilizar o PowerShell](https://docs.microsoft.com/azure/storage/scripts/storage-blobs-container-calculate-size-powershell)
* Você também pode encontrar o tamanho do/User/Hive/. Trash/pasta nos clusters HDI usando o seguinte comando:

   ``hdfs dfs -du -h /user/hive/.Trash/``

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Como posso configurar a auditoria para minha conta de armazenamento de BLOBs?

Para auditar contas de armazenamento de BLOBs, você precisa configurar o monitoramento para a conta de armazenamento de BLOBs usando o procedimento documentado em [monitorar uma conta de armazenamento no portal do Azure](https://docs.microsoft.com/azure/storage/common/storage-monitor-storage-account). Um log HDFS-Audit fornece apenas informações de auditoria somente para o sistema de arquivos do HDFS local (hdfs://mycluster).  Ele não inclui operações que são feitas no armazenamento remoto.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Como posso transferir arquivos entre um contêiner de BLOB e um nó principal do HDInsight?

Você pode transferir arquivos entre um contêiner de BLOB e um nó de cabeçalho do HDInsight executando um script de Shell em seu nó de cabeçalho, semelhante ao exemplo a seguir:

```
for i in cat filenames.txt

do
         hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> O arquivo file *names. txt* terá o caminho absoluto dos arquivos nos contêineres de BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Há algum plug-in do Ranger para armazenamento?

No momento, não existem plug-ins do Ranger para armazenamento de BLOBs, Azure Data Lake Storage (ADLS) Gen1 ou Azure Data Lake Storage Gen2. Para clusters ESP, use ADLS como uma prática recomendada e defina as permissões refinadas manualmente no nível do sistema de arquivos usando as ferramentas do HDFS. Além disso, os clusters ESP farão parte do controle de acesso do sistema de arquivos usando o AAD no nível do cluster quando você usar o ADLS. 

Você deve ser capaz de usar o Gerenciador de Armazenamento do Azure para atribuir políticas de acesso de dados a grupos de segurança em que os usuários estão localizados usando os procedimentos documentados nos seguintes artigos:

* [Como fazer definir permissões para que os usuários do Azure AD consultem dados em Data Lake Storage Gen2 usando o hive ou outros serviços?](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
* [Definir permissões de nível de arquivo e diretório usando Gerenciador de Armazenamento do Azure com Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-how-to-set-permissions-storage-explorer)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Posso aumentar o armazenamento do HDFS em um cluster sem aumentar o tamanho do disco dos nós de trabalho?

Não, você não pode aumentar o tamanho do disco de nenhum nó de trabalho. A única maneira de aumentar o tamanho do disco é descartar o cluster e recriá-lo em máquinas virtuais de trabalho maiores.

A Microsoft não recomenda que você use o HDFS para armazenar qualquer um dos seus dados no HDInsight, pois os dados são excluídos quando você exclui o cluster. Em vez disso, recomendamos que você armazene seus dados no Azure. Escalar verticalmente o cluster também pode adicionar mais capacidade ao seu cluster HDInsight.

## <a name="edge-nodes"></a>Nós periféricos

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Posso adicionar um nó de borda após a criação do cluster?

Sim, você pode adicionar um nó de borda vazio a um cluster HDInsight existente ou a um novo cluster ao criar o cluster.

Para obter mais informações, veja [Utilizar nós periféricos vazios em clusters do Apache Hadoop no HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-apps-use-edge-node).

### <a name="how-can-i-connect-to-an-edge-node"></a>Como posso me conectar a um nó de borda?

Depois de criar um nó de borda, você pode se conectar ao nó de borda usando o SSH na porta 22. Você pode encontrar o nome do nó de borda no portal do cluster (os nomes geralmente terminam em "-Ed").

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Por que os scripts persistentes não são executados automaticamente em nós de borda recém-criados?

Os scripts persistentes são usados para personalizar novos nós de trabalho que são adicionados ao cluster por meio de operações de dimensionamento e não se aplicam a nós de borda.

## <a name="rest-api"></a>API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Quais são as chamadas à API REST para efetuar pull de uma exibição de consulta tez do cluster?

Você pode usar os seguintes pontos de extremidade REST para efetuar pull das informações necessárias em que a resposta estará no formato JSON. Os cabeçalhos de autenticação básica podem ser usados para fazer essas solicitações.

* Exibição "tez Query" – https://`<cluster name>`. azurehdinsight.net/WS/v1/Timeline/HIVE_QUERY_ID/

* "Tez DAG" View- https://`<cluster name>`. azurehdinsight.net/WS/v1/Timeline/TEZ_DAG_ID/

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Como fazer recuperar os detalhes de configuração do cluster HDI usando um usuário Azure Active Directory?

Para negociar tokens de autenticação apropriados com o usuário do AAD, percorra o gateway usando o seguinte formato:

* https://`<cluster dnsname>`. azurehdinsight.NET/API/v1/clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Como fazer usar a API RESTful Ambari para monitorar o desempenho do YARN?

Se o comando de ondulação for chamado na mesma VNet ou VNet emparelhada, use o seguinte comando:

``curl -u <cluster login username> -sS -G http://headnodehost:8080/api/v1/clusters/anssenllap/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
Se o comando for chamado de fora da VNet ou de uma VNet não emparelhada, o formato do comando será:
 
* Para um cluster não ESP:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``
 
* Para um cluster ESP:``curl -u <cluster login username> -sS -G https://ClusterName.azurehdinsight.net/api/v1/clusters/ClusterName/services/YARN/components/NODEMANAGER?fields=metrics/cpu``

> [!NOTE]
> A ondulação solicitará uma senha. Você deve inserir uma senha válida para o nome de usuário de logon do cluster.

## <a name="billing"></a>Faturação

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Quanto custa para implantar um cluster HDInsight?

Para obter mais informações sobre preços e perguntas frequentes relacionadas à cobrança, consulte a página de [preços do Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="how-do-i-cancel-my-subscription"></a>Como fazer cancelar minha assinatura?

Para obter informações sobre como cancelar sua assinatura, consulte [cancelar sua assinatura do Azure](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Para assinaturas pagas conforme o uso, o que acontece depois de cancelar minha assinatura?

Para obter informações sobre sua assinatura após sua cancelamento, consulte [o que acontece depois de cancelar minha assinatura?](https://docs.microsoft.com/azure/billing/billing-how-to-cancel-azure-subscription#what-happens-after-i-cancel-my-subscription)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-i-am-running-an-hdinsight-36-cluster"></a>Por que a versão do hive aparece como 1.2.1000 em vez de 2,1 na interface do usuário do Ambari embora eu esteja executando um cluster HDInsight 3,6?

Embora apenas 1,2 apareça na interface do usuário do amAmbari, o HDInsight 3,6 contém o hive 1,2 e o hive 2,1.

## <a name="other-faq"></a>Outras perguntas frequentes

### <a name="what-does-hdinsight-offer-in-terms-of-real-time-stream-processing-capabilities"></a>O que o HDInsight oferece em termos de recursos de processamento de fluxo em tempo real?

Para obter informações sobre os recursos de integração do processamento de fluxo no Azure HDInsight, consulte [escolhendo uma tecnologia de processamento de fluxo no Azure](https://docs.microsoft.com/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-terminate-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Há uma maneira de encerrar dinamicamente o nó principal do cluster quando o cluster está ocioso por um período específico?

Não, não é possível encerrar dinamicamente o nó principal do cluster. Você pode usar Azure Data Factory para este cenário.
