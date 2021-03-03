---
title: Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc
description: Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: 046f9d80c034e1ac1f2e7ffe144b4f389861b043
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101687945"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc

Este documento descreve os passos para criar um grupo de servidores de hiperescala PostgreSQL em Azure Arc.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>Introdução
Se já está familiarizado com os tópicos abaixo, pode ignorar este parágrafo.
Há tópicos importantes que pode querer ler antes de prosseguir com a criação:
- [Visão geral do Azure Arc habilitado serviços de dados](overview.md)
- [Modos e requisitos de conectividade](connectivity.md)
- [Configuração de armazenamento e conceitos de armazenamento de Kubernetes](storage-configuration.md)
- [Modelo de recurso Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

Se preferir experimentar coisas sem providenciar um ambiente completo, inicie-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.


## <a name="login-to-the-azure-arc-data-controller"></a>Faça login no controlador de dados Azure Arc

Antes de criar um caso, tem primeiro de iniciar sessão no controlador de dados Azure Arc. Se já tiver iniciado sessão no controlador de dados, pode saltar este passo.

```console
azdata login
```

Em seguida, será solicitado o nome de utilizador, palavra-passe e o espaço de nome do sistema.  

> Se usou o script para criar o controlador de dados, então o seu espaço de nome deve ser **arco**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Passo preliminar e temporário apenas para utilizadores openshift
Implemente este passo antes de passar para o próximo passo. Para implementar o grupo de servidores PostgreSQL Hyperscale no Red Hat OpenShift num projeto que não seja o padrão, é necessário executar os seguintes comandos contra o seu cluster para atualizar as restrições de segurança. Este comando concede os privilégios necessários às contas de serviço que irão executar o seu grupo de servidores De Hiperescala PostgreSQL. A restrição de contexto de segurança (SCC) arc-data-scc é a que adicionou quando implementou o controlador de dados Azure Arc.

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

**O nome do grupo do servidor é o nome do grupo de servidor que irá criar durante o próximo passo.**

Para mais detalhes sobre SCCs em OpenShift, consulte a [documentação OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html). Pode agora implementar o próximo passo.


## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc

Para criar um grupo de servidor de hiperescala pós-escala ativado pelo Arco, utilizará o comando `azdata arc postgres server create` ao qual passará vários parâmetros.

Para mais detalhes sobre todos os parâmetros que pode definir no momento da criação, reveja a saída do comando:
```console
azdata arc postgres server create --help
```

Os principais parâmetros devem ser considerados:
- **o nome do grupo de servidores** que pretende implementar. Indicar `--name` ou `-n` seguir um nome cujo comprimento não deve exceder 11 caracteres.

- **a versão do motor PostgreSQL** que pretende implantar: por padrão é a versão 12. Para implementar a versão 12, pode omitir este parâmetro ou passar um dos seguintes parâmetros: `--engine-version 12` ou `-ev 12` . Para implementar a versão 11, indique `--engine-version 11` ou `-ev 11` .

- **o número de nós de trabalhadores** que pretende implantar para escalar e potencialmente alcançar melhores desempenhos. Antes de prosseguir aqui, leia os [conceitos sobre a Escala De Pós-Escala.](concepts-distributed-postgres-hyperscale.md) Para indicar o número de nós de trabalhador a implantar, utilize o parâmetro `--workers` ou seguido de um número inteiro maior ou igual a `-w` 2. Por exemplo, se pretender implantar um grupo de servidor com 2 nós de trabalhador, indique `--workers 2` ou `-w 2` . Isto criará três cápsulas, uma para o nó/exemplo coordenador e duas para os nós/instâncias do trabalhador (uma para cada um dos trabalhadores).

- **as aulas de armazenamento** que deseja que o seu grupo de servidor utilize. É importante definir a classe de armazenamento corretamente no momento em que implementa um grupo de servidor, uma vez que este não pode ser alterado após a sua implantação. Se alterar a classe de armazenamento após a implementação, terá de extrair os dados, eliminar o seu grupo de servidores, criar um novo grupo de servidores e importar os dados. Pode especificar as aulas de armazenamento para utilizar para os dados, registos e cópias de segurança. Por predefinição, se não indicar aulas de armazenamento, serão utilizadas as classes de armazenamento do controlador de dados.
    - para definir a classe de armazenamento para os dados, indicar o parâmetro `--storage-class-data` ou seguido pelo nome da classe de `-scd` armazenamento.
    - para definir a classe de armazenamento para os registos, indicar o parâmetro `--storage-class-logs` ou seguido pelo nome da classe de `-scl` armazenamento.
    - para definir a classe de armazenamento para as cópias de segurança: nesta pré-visualização do Arco Azure habilitado a Escala de Hiperescala PostgreSQL existem duas maneiras de definir aulas de armazenamento dependendo dos tipos de operações de backup/restauro que pretende fazer. Estamos a trabalhar na simplificação desta experiência. Indicará uma classe de armazenamento ou um suporte de reivindicação de volume. Um suporte de reivindicação de volume é um par de uma reivindicação de volume persistente existente (no mesmo espaço de identificação) e tipo de volume (e metadados opcionais dependendo do tipo de volume) separados por cólon. O volume persistente será montado em cada cápsula para o grupo de servidores PostgreSQL.
        - se pretender fazer apenas restauros completos da base de dados, desajei o parâmetro `--storage-class-backups` ou seguido pelo nome da classe de `-scb` armazenamento.
        - se pretender fazer restauros de base de dados completos e ponto no tempo, desaceie o parâmetro `--volume-claim-mounts` ou seguido pelo nome de uma `-vcm` reclamação de volume e de um tipo de volume.

Note que quando executar o comando de criação, será solicitado que introduza a palavra-passe do `postgres` utilizador administrativo predefinido. O nome desse utilizador não pode ser alterado nesta Pré-visualização. Pode saltar a solicitação interativa definindo a variável ambiente de `AZDATA_PASSWORD` sessão antes de executar o comando criar.

### <a name="examples"></a>Exemplos

**Para implementar um grupo de servidores da versão 12 do Postgres, com 2 nóns de trabalhador que utiliza as mesmas classes de armazenamento que o controlador de dados, executar o seguinte comando:**
```console
azdata arc postgres server create -n postgres01 --workers 2
```

**Para implementar um grupo de servidores da versão 12 do Postgres, com 2 nós de trabalhadores que usam as mesmas classes de armazenamento que o controlador de dados para dados e registos, mas a sua classe de armazenamento específica para fazer restauros completos e ponto no tempo restaura, use os seguintes passos:**

 Este exemplo pressupõe que o seu grupo de servidores está hospedado num cluster Azure Kubernetes Service (AKS). Este exemplo usa o azurefile-premium como nome de classe de armazenamento. Pode ajustar o exemplo abaixo para corresponder ao seu ambiente. Note que **o AccessMdes ReadWriteMany é necessário** para esta configuração.  

Em primeiro lugar, crie um ficheiro YAML que contenha a descrição abaixo do PVC de backup e nomeie-o CreateBackupPVC.yml, por exemplo:
```console
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: backup-pvc
  namespace: arc
spec:
  accessModes:
    - ReadWriteMany
  volumeMode: Filesystem
  resources:
    requests:
      storage: 100Gi
  storageClassName: azurefile-premium
```

Em seguida, crie um PVC utilizando a definição armazenada no ficheiro YAML:

```console
kubectl create -f e:\CreateBackupPVC.yml -n arc
``` 

Em seguida, crie o grupo de servidores:

```console
azdata arc postgres server create -n postgres01 --workers 2 -vcm backup-pvc:backup
```

> [!IMPORTANT]
> - Leia as [limitações atuais relacionadas com a cópia de segurança/restauro.](limitations-postgresql-hyperscale.md#backup-and-restore)


> [!NOTE]  
> - Se implementou o controlador de dados utilizando `AZDATA_USERNAME` e `AZDATA_PASSWORD` sessão de variáveis ambiente na mesma sessão de terminais, então os valores para `AZDATA_PASSWORD` serão usados para implantar o grupo de servidores de hiperescala PostgreSQL também. Se preferir utilizar outra palavra-passe, ou (1) atualizar o valor para `AZDATA_PASSWORD` ou (2) eliminar a `AZDATA_PASSWORD` variável ambiente ou (3) eliminar o seu valor a ser solicitado para introduzir uma palavra-passe interativamente quando criar um grupo de servidor.
> - A criação de um grupo de servidores de hiperescala PostgreSQL não registará imediatamente recursos em Azure. Como parte do processo de envio de inventário de [recursos](upload-metrics-and-logs-to-azure-monitor.md)  ou dados de [utilização](view-billing-data-in-azure.md) para a Azure, os recursos serão criados em Azure e poderá ver os seus recursos no portal Azure.



## <a name="list-the-postgresql-hyperscale-server-groups-deployed-in-your-arc-data-controller"></a>Listar os grupos de servidores de hiperescala PostgreSQL implantados no seu controlador de dados Arc

Para listar os grupos de servidores de hiperescala PostgreSQL implantados no seu controlador de dados Arc, execute o seguinte comando:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>Obtenha os pontos finais para ligar aos grupos de servidores de hiperescala pós-escala do Azure Arc

Para visualizar os pontos finais de um grupo de servidores PostgreSQL, executar o seguinte comando:

```console
azdata arc postgres endpoint list -n <server group name>
```
Por exemplo:
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

Pode utilizar o ponto final de Instância PostgreSQL para ligar ao grupo de servidores de hiperescala PostgreSQL a partir da sua ferramenta preferida:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin, etc. Quando o fizer, ligue-se ao nó/instância do coordenador que cuida de encaminhar a consulta para os nós/instâncias do trabalhador apropriados se tiver criado tabelas distribuídas. Para mais detalhes, leia os [conceitos de Azure Arc habilitado a Hiperescala PostgreSQL.](concepts-distributed-postgres-hyperscale.md)

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Nota especial sobre as implementações de máquinas virtuais Azure

Quando estiver a utilizar uma máquina virtual Azure, o endereço IP do ponto final não mostrará o endereço IP _público._ Para localizar o endereço IP público, utilize o seguinte comando:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Em seguida, pode combinar o endereço IP público com a porta para fazer a sua ligação.

Também poderá ser necessário expor a porta do grupo de servidores De Hiperescala PostgreSQL através do gateway de segurança da rede (NSG). Para permitir o tráfego através do (NSG) terá de adicionar uma regra que pode fazer utilizando o seguinte comando:

Para definir uma regra, precisará saber o nome do seu NSG. Determina o NSG utilizando o comando abaixo:

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Uma vez que tenha o nome do NSG, pode adicionar uma regra de firewall usando o seguinte comando. Os valores de exemplo aqui criam uma regra NSG para a porta 30655 e permite a ligação a partir de **qualquer** endereço IP de origem.  Esta não é uma boa prática de segurança!  Pode bloquear melhor as coisas especificando um valor de prefixos de endereço de fonte-fonte específico para o endereço IP do seu cliente ou um intervalo de endereço IP que cubra os endereços IP da sua equipa ou organização.

Substitua o valor do parâmetro --destination-port-ranges abaixo pelo número de porta que obteve do comando 'azdata arc postgres server list' acima.

```azurecli
az network nsg rule create -n db_port --destination-port-ranges 30655 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="connect-with-azure-data-studio"></a>Ligação ao Azure Data Studio

Abra o Azure Data Studio e ligue-se ao seu caso com o endereço IP do ponto final externo e o número de porta acima, e a palavra-passe especificada no momento em que criou o caso.  Se o PostgreSQL não estiver disponível no dropdown *do tipo Connection,* pode instalar a extensão PostgreSQL procurando postgreSQL no separador extensões.

> [!NOTE]
> Terá de clicar no botão [Avançado] no painel de ligação para introduzir o número da porta.

Lembre-se, se estiver a utilizar um VM Azure necessitará do endereço IP _público_ que está acessível através do seguinte comando:

```azurecli
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

## <a name="connect-with-psql"></a>Conecte-se com o psql

Para aceder ao seu grupo de servidores de hiperescala PostgreSQL, passe o ponto final externo do grupo de servidores de hiperescala PostgreSQL que recuperou de cima:

Agora pode ligar qualquer um dos psql:

```console
psql postgresql://postgres:<EnterYourPassword>@10.0.0.4:30655
```

## <a name="next-steps"></a>Passos seguintes

- Leia os conceitos e guias de como fazer a Base de Dados Azure para a Hiperescala Pós-SQL para distribuir os seus dados através de vários nosmos de hiperescala PostgreSQL e beneficiar de melhores desempenhos potencialmente:
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicação](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalização de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Desenhe uma base de dados multi-inquilinos](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Desenhe um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nos documentos acima, salte as secções **Iniciar sessão No portal Azure,**& **Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus)**. Implemente os passos restantes na sua implantação do Arco Azure. Estas secções são específicas da Base de Dados Azure para a Hiperescala Pós-SQL (Citus) oferecida como um serviço PaaS na nuvem Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua Hiperescala Pós-Altura pós-SQL ativada pelo Arco Azure.

- [Dimensione o seu Arco Azure ativado para o grupo de servidores de hiperescala PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento de Kubernetes](storage-configuration.md)
- [Expansão das reclamações de volume persistentes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modelo de recurso Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)
