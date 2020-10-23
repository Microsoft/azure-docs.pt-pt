---
title: Criar uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc
description: Criar uma base de dados de Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: d2eef20b4c5648b1b11f16d8e46b956fc1497181
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92364427"
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

Se preferir experimentar as coisas sem providenciar um ambiente completo, inicie-se rapidamente com [o Azure Arc Jumpstart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) no Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.


## <a name="login-to-the-azure-arc-data-controller"></a>Faça login no controlador de dados Azure Arc

Antes de criar um caso, tem primeiro de iniciar sessão no controlador de dados Azure Arc. Se já tiver iniciado sessão no controlador de dados, pode saltar este passo.

```console
azdata login
```

Em seguida, será solicitado o nome de utilizador, a palavra-passe e o espaço de nome do sistema.  

> Se usou o script para criar o controlador de dados, então o seu espaço de nome deve ser **arco**

```console
Namespace: arc
Username: arcadmin
Password:
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Passo preliminar e temporário apenas para utilizadores openshift

Implemente este passo antes de passar para o próximo passo. Para implementar o grupo de servidores PostgreSQL Hyperscale no Red Hat OpenShift num projeto que não seja o padrão, é necessário executar os seguintes comandos contra o seu cluster para atualizar as restrições de segurança. Este comando concede os privilégios necessários às contas de serviço que irão executar o seu grupo de servidores De Hiperescala PostgreSQL. A restrição de contexto de segurança (SCC) **_arc-data-scc_** é a que adicionou quando implementou o controlador de dados Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**O nome do grupo do servidor** é o nome do grupo de servidor que irá criar durante o próximo passo._
   
Para mais detalhes sobre SCCs em OpenShift, consulte a [documentação OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Pode agora implementar o próximo passo.

## <a name="create-an-azure-database-for-postgresql-hyperscale-server-group"></a>Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL

Para criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc, utilize o seguinte comando:

```console
azdata arc postgres server create -n <name> --workers <# worker nodes with #>=2> --storage-class-data <storage class name> --storage-class-logs <storage class name> --storage-class-backups <storage class name>

#Example
#azdata arc postgres server create -n postgres01 --workers 2
```

> [!NOTE]
> - **Existem outros parâmetros de linha de comando disponíveis.  Consulte a lista completa de opções em execução `azdata arc postgres server create --help` .**
> - A classe de armazenamento utilizada para cópias de segurança _(--storage-class-backups -scb_) não é fornecida na classe de armazenamento de dados do controlador de dados se não for fornecida.
> - A unidade aceite pelos parâmetros --volume-tamanho-* é uma quantidade de recurso Kubernetes (um inteiro seguido por um destes si suficientes (T, G, M, K, m) ou o seu poder de dois equivalentes (Ti, Gi, Mi, Ki)).
> - Os nomes devem ter 12 caracteres ou menos de comprimento e estar em conformidade com as convenções de nomeação do DNS.
> - Será solicitado que introduza a palavra-passe para o utilizador administrativo padrão _de postgres._  Pode saltar a solicitação interativa definindo a variável ambiente de `AZDATA_PASSWORD` sessão antes de executar o comando criar.
> - Se implementou o controlador de dados utilizando variáveis de ambiente de sessão de AZDATA_USERNAME e AZDATA_PASSWORD na mesma sessão terminal, então os valores para AZDATA_PASSWORD serão utilizados para implantar também o grupo de servidores de hiperescala PostgreSQL. Se preferir utilizar outra palavra-passe, ou (1) atualizar o valor para AZDATA_PASSWORD ou (2) eliminar a variável ambiente AZDATA_PASSWORD ou eliminar o seu valor ser solicitado a introduzir uma palavra-passe interativamente quando criar um grupo de servidor.
> - O nome do utilizador de administrador predefinido para o motor de base de dados de hiperescala PostgreSQL é _postgres_ e não pode ser alterado neste momento.
> - A criação de um grupo de servidores de hiperescala PostgreSQL não registará imediatamente recursos em Azure. Como parte do processo de envio de inventário de [recursos](upload-metrics-and-logs-to-azure-monitor.md)  ou dados de [utilização](view-billing-data-in-azure.md) para a Azure, os recursos serão criados em Azure e poderá ver os seus recursos no portal Azure.



## <a name="list-your-azure-database-for-postgresql-server-groups-created-in-your-arc-setup"></a>Liste a sua Base de Dados de Azure para grupos de servidores PostgreSQL criados na sua configuração Arc

Para ver os grupos de servidores de hiperescala PostgreSQL no Azure Arc, utilize o seguinte comando:

```console
azdata arc postgres server list
```


```output
Name        State     Workers
----------  --------  ---------
postgres01  Ready     2
```

## <a name="get-the-endpoints-to-connect-to-your-azure-database-for-postgresql-server-groups"></a>Obtenha os pontos finais para ligar à sua Base de Dados Azure para grupos de servidores PostgreSQL

Para visualizar os pontos finais de uma instância PostgreSQL, executar o seguinte comando:

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

Pode utilizar o ponto final de Instância PostgreSQL para ligar ao grupo de servidores de hiperescala PostgreSQL a partir da sua ferramenta preferida:  [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio), [pgcli](https://www.pgcli.com/) psql, pgAdmin, etc.

Se estiver a utilizar um Azure VM para testar, siga as instruções abaixo:

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

- Leia os conceitos e guias de como fazer a Base de Dados Azure para a Hiperescala Pós-SQL para distribuir os seus dados através de vários nós de hiperescala postgreSQL e para beneficiar de toda a potência da Base de Dados Azure para a Escala de Hiperescala Pós-SQL. :
    * [Nós e tabelas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar o tipo de aplicação](../../postgresql/concepts-hyperscale-app-type.md)
    * [Escolher uma coluna de distribuição](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocalização de tabela](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribuir e modificar tabelas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Desenhe uma base de dados multi-inquilinos](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Desenhe um painel de análise em tempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > \* Nos documentos acima, salte as secções **Iniciar sessão No portal Azure,**& **Criar uma Base de Dados Azure para PostgreSQL - Hiperescala (Citus)**. Implemente os passos restantes na sua implantação do Arco Azure. Estas secções são específicas da Base de Dados Azure para a Hiperescala Pós-SQL (Citus) oferecida como um serviço PaaS na nuvem Azure, mas as outras partes dos documentos são diretamente aplicáveis à sua Hiperescala Pós-Altura pós-SQL ativada pelo Arco Azure.

- [Aumentar horizontalmente o grupo de servidores de Hyperscale da Base de Dados do Azure para PostgreSQL](scale-out-postgresql-hyperscale-server-group.md)
- [Configuração de armazenamento e conceitos de armazenamento de Kubernetes](storage-configuration.md)
- [Expansão das reclamações de volume persistentes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Modelo de recurso Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)