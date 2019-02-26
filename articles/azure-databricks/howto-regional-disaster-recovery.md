---
title: Recuperação após desastre regional para o Azure Databricks
description: Este artigo descreve uma abordagem para fazer a recuperação após desastre no Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/27/2018
ms.openlocfilehash: a42d2c75913b2c9fdfa0d2b7c3ec2742525a4c97
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806101"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Recuperação após desastre regional para clusters do Azure Databricks

Este artigo descreve uma arquitetura de recuperação após desastre útil para clusters do Azure Databricks e os passos para realizar esse design.

## <a name="azure-databricks-architecture"></a>Arquitetura do Azure Databricks

Num alto nível, quando cria uma área de trabalho do Azure Databricks no portal do Azure, um [aplicação gerida](../managed-applications/overview.md) é implementada como um recurso do Azure na sua subscrição, na região do Azure escolhido (por exemplo, E.U.A. oeste). Esta aplicação é implementada numa [rede Virtual do Azure](../virtual-network/virtual-networks-overview.md) com um [grupo de segurança de rede](../virtual-network/manage-network-security-group.md) e uma conta de armazenamento do Azure, disponível na sua subscrição. A rede virtual fornece segurança de nível de perímetro para a área de trabalho do Databricks e está protegida através do grupo de segurança de rede. Na área de trabalho, pode criar clusters do Databricks, fornecendo o trabalho e o driver de tipo de VM e a versão de runtime do Databricks. Os dados persistentes estão disponíveis na sua conta de armazenamento, que pode ser armazenamento de Blobs do Azure ou do Azure Data Lake Store. Assim que o cluster é criado, pode executar tarefas por meio de pontos finais ODBC/JDBC de blocos de notas, REST APIs, anexando-los a um cluster específico.

O plano de controlo do Databricks gerencia e monitora o ambiente de área de trabalho do Databricks. Qualquer operação de gestão, tais como criar o cluster serão iniciado do painel de controlo. Todos os metadados, como as tarefas agendadas, é armazenada numa base de dados do Azure com a georreplicação para a tolerância a falhas.

![Arquitetura do Databricks](media/howto-regional-disaster-recovery/databricks-architecture.png)

Uma das vantagens dessa arquitetura é que os utilizadores podem ligar do Azure Databricks para qualquer recurso de armazenamento na respetiva conta. Das principais vantagens é que ambos (Azure Databricks) de computação e armazenamento pode ser dimensionado de forma independente.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Como criar uma topologia de recuperação de desastre regional

Como notar na descrição de arquitetura anterior, isso significa que existem vários componentes utilizados para um pipeline de grandes volumes de dados com o Azure Databricks:  O armazenamento do Azure, base de dados do Azure e outras origens de dados. O Azure Databricks é o *computação* para grandes volumes de dados do pipeline. É *efémeras* por natureza, que significa que, enquanto continua disponíveis no armazenamento do Azure, os seus dados estão a *computação* (cluster do Azure Databricks) pode ser terminada, para que não precisa de pagar por computação quando não precisa dela. O *computação* (Azure Databricks) e origens de armazenamento tem de estar na mesma região, para que as tarefas não experienciar latência elevada.  

Para criar seu próprio topologia de recuperação de desastre regional, siga estes requisitos:

   1. Aprovisione várias áreas de trabalho do Azure Databricks em regiões do Azure separadas. Por exemplo, crie a área de trabalho principal do Azure Databricks no e.u.a. Leste 2. Crie a área de trabalho do Azure Databricks de recuperação de desastres secundário numa região separado, por exemplo, E.U.A. oeste.

   2. Uso [armazenamento georredundante](../storage/common/storage-redundancy-grs.md#read-access-geo-redundant-storage). Os dados que associados do Azure Databricks é armazenado por padrão no armazenamento do Azure. Os resultados de tarefas do Databricks também são armazenados no armazenamento de Blobs do Azure, para que os dados processados são duráveis e permanecem altamente disponíveis depois do cluster é encerrado. Como o cluster de armazenamento e o Databricks estão localizadas conjuntamente, tem de utilizar armazenamento georredundante, para que os dados podem ser acedidos numa região secundária se a região primária já não está acessível.

   3. Depois de criar a região secundária, tem de migrar os utilizadores, pastas de utilizador, blocos de notas, configuração de cluster, configuração de tarefas, bibliotecas, armazenamento, scripts de inicialização e reconfigurar o controlo de acesso. Detalhes adicionais descritos na secção seguinte.

## <a name="detailed-migration-steps"></a>Passos de migração detalhadas

1. **Configurar a interface de linha de comandos do Databricks no seu computador**

   Este artigo mostra vários exemplos de código que use a interface de linha de comandos para a maioria dos passos automatizados, uma vez que é um wrapper de fácil-a-utilizador ao longo da API de REST do Azure Databricks.

   Antes de executar quaisquer passos de migração, instale a cli do databricks no computador de secretária ou uma máquina virtual em que planeja fazer o trabalho. Para obter mais informações, consulte [instalar a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Quaisquer scripts do python fornecidas neste artigo devem funcionar com o Python 2.7 + < 3.x.

2. **Configure dois perfis.**

   Configure um para a área de trabalho principal e outro para a área de trabalho secundária:

   ```bash
   databricks configure --profile primary
   databricks configure --profile secondary
   ```

   O código bloqueia deste comutador artigo entre perfis de cada passo seguinte com o comando de área de trabalho correspondente. Certifique-se de que os nomes dos perfis que cria são substituídos em cada bloco de código.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   Pode alternar manualmente na linha de comandos se for necessário:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrar os utilizadores do Azure Active Directory**

   Adicione manualmente os mesmos utilizadores do Azure Active Directory para a área de trabalho secundária que existem na área de trabalho principal.

4. **Migrar as pastas de utilizador e os blocos de notas**

   Utilize o seguinte código de python para migrar os ambientes de usuário em área restrita, que incluem a estrutura de pastas aninhadas e blocos de notas por utilizador.

   > [!NOTE]
   > Bibliotecas não são copiadas neste passo, como a API subjacente não suporta os.

   Copie e guarde o seguinte script de python para um ficheiro e executá-lo na sua linha de comandos do Databricks. Por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = user_list_out.splitlines()

   # Export sandboxed environment (folders, notebooks) for each user and import into new workspace.
   # Libraries are not included with these APIs / commands.

   for user in user_list:
     print "Trying to migrate workspace for user " + user

     call("mkdir -p " + user, shell=True)
     export_exit_status = call("databricks workspace export_dir /Users/" + user + " ./" + user + " --profile " + EXPORT_PROFILE, shell=True)

     if export_exit_status==0:
       print "Export Success"
       import_exit_status = call("databricks workspace import_dir ./" + user + " /Users/" + user + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print "Import Success"
       else:
         print "Import Failure"
     else:
       print "Export Failure"

   print "All done"
   ```

5. **Migrar as configurações de cluster**

   Depois de terem sido migrados blocos de notas, opcionalmente, pode migrar as configurações de cluster para a nova área de trabalho. É quase uma etapa de totalmente automatizada com a cli do databricks, a menos que gostaria de fazer a migração de configuração de cluster seletiva, em vez de todos.

   > [!NOTE]
   > Infelizmente não existe nenhum ponto de final de configuração de cluster create, e esse script tenta criar cada cluster imediatamente. Caso não haja núcleos suficientes disponíveis na sua subscrição, a criação de cluster poderá falhar. A falha pode ser ignorada, desde que a configuração é transferida com êxito.

   O seguinte script fornecido imprime um mapeamento do antigo para o novo cluster IDs, que pode ser utilizado para a migração de trabalho mais tarde (para tarefas que estão configurados para utilizar clusters existentes).

   Copie e guarde o seguinte script de python para um ficheiro e executá-lo na sua linha de comandos do Databricks. Por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Get all clusters info from old workspace 
   clusters_out = check_output(["databricks", "clusters", "list", "--profile", EXPORT_PROFILE]) clusters_info_list = clusters_out.splitlines()

   # Create a list of all cluster ids 
   clusters_list = [] for cluster_info in clusters_info_list:   clusters_list.append(cluster_info.split(None, 1)[0])

   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements 
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf"," node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","sp ark_env_vars","autotermination_minutes","enable_elastic_disk"]

   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {} for cluster in clusters_list:   print "Trying to migrate cluster " + cluster

   cluster_get_out = check_output(["databricks", "clusters", "get", "--cluster-id", cluster, "--profile", EXPORT_PROFILE])
   print "Got cluster config from old workspace"

   # Remove extra content from the config, as we need to build create request with allowed elements only
   cluster_req_json = json.loads(cluster_get_out)    
   cluster_json_keys = cluster_req_json.keys()   

   for key in cluster_json_keys:     
      if key not in cluster_req_elems:       
         cluster_req_json.pop(key, None)
  
   # Create the cluster, and store the mapping from old to new cluster ids
   cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE]) 
   cluster_create_out_json = json.loads(cluster_create_out)   
   cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']

   print "Sent cluster create request to new workspace successfully"

   print "Cluster mappings: " + json.dumps(cluster_old_new_mappings)
   print "All done"
   ```

6. **Migrar a configuração de tarefas**

   Se tiver migrado as configurações de cluster no passo anterior, pode optar por migrar as configurações da tarefa para a nova área de trabalho. É uma etapa totalmente automatizada usando o cli do databricks, a menos que gostaria de fazer a migração de configuração de tarefa seletiva, em vez de fazê-lo para todas as tarefas.

   > [!NOTE]
   > A configuração para uma tarefa agendada contém as informações de "agenda" bem, portanto, por predefinição que irá começar a trabalhar de acordo com a temporização configurada assim que tenha migrado. Por conseguinte, o bloco de código seguinte remove quaisquer informações de agenda durante a migração (para evitar execuções duplicadas em áreas de trabalho antigas e novas). Configurar as agendas para essas tarefas, quando estiver pronto para transferência.

   A configuração da tarefa requer definições para um novo ou um cluster existente. Se utilizar o cluster existente, /code o script abaixo irá tentar substituir o ID de cluster antigo com o novo ID de cluster.

   Copie e guarde o python seguinte script para um ficheiro. Substitua o valor para `old_cluster_id` e `new_cluster_id`, com a saída de migração de cluster feita no passo anterior. Executá-lo na sua cli do databricks-a linha de comandos, por exemplo, `python scriptname.py`.

   ```python
   from subprocess import call, check_output
   import json

   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"

   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"old_cluster_id": "new_cluster_id"}

   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print "No jobs to migrate"
     sys.exit(0)

   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])

   # Optionally filter job ids out manually, so as to create only required ones in new workspace

   # Create each job in the new workspace based on corresponding settings in the old workspace

   for job in jobs_list:
     print "Trying to migrate " + job

     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print "Got job config from old workspace"

     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']

     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)

     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print "Mapping not available for old cluster id " + job_req_settings_json['existing_cluster_id']
         continue

     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print "Sent job create request to new workspace successfully"

   print "All done"
   ```

7. **Migrar de bibliotecas**

   Atualmente, não há nenhuma maneira simples de migrar bibliotecas de uma área de trabalho para outro. Em vez disso, reinstale manualmente dessas bibliotecas em nova área de trabalho. É possível automatizar usando a combinação de [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples) para carregar bibliotecas personalizadas para a área de trabalho e [bibliotecas CLI](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrar o armazenamento de Blobs do Azure e monta do Azure Data Lake Store**

   Manualmente voltar a montar todas [armazenamento de Blobs do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html) e [do Azure Data Lake Store (Gen 1)](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html) usando uma solução baseada no bloco de notas de pontos de montagem. Os recursos de armazenamento seriam ter foi montados na área de trabalho principal e que tem de ser repetido na área de trabalho secundária. Não há nenhuma API externo para monta.

9. **Migrar os scripts de inicialização de cluster**

   Quaisquer scripts de inicialização de cluster podem ser migradas do antigo para nova área de trabalho com o [DBFS CLI](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Primeiro, copie os scripts necessários de `dbfs:/dat abricks/init/..` à sua área de trabalho local ou a máquina virtual. Em seguida, copie esses scripts para a nova área de trabalho no mesmo caminho.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Reconfigurar e volte a aplicar o controlo de acesso manualmente.**

   Se a sua área de trabalho principal existente estiver configurada para utilizar o escalão Premium (SKU), é provável que também está a utilizar o [a funcionalidade de controlo de acesso](https://docs.azuredatabricks.net/administration-guide/admin-settings/index.html#manage-access-control).

   Se utilizar a funcionalidade de controlo de acesso, reaplicar manualmente o controlo de acesso aos recursos (blocos de notas, Clusters, tarefas, tabelas).

## <a name="next-steps"></a>Passos Seguintes
Para obter mais informações, consulte [documentação do Azure Databricks](https://docs.azuredatabricks.net/user-guide/index.html).
