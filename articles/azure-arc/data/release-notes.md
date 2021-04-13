---
title: Azure Arc habilitado serviços de dados - Notas de lançamento
description: Últimas notas de lançamento
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 1fe5974bafddcb4e474ef59a062836e071ab9461
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304924"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>Notas de lançamento - Azure Arc habilitado serviços de dados (Pré-visualização)

Este artigo destaca capacidades, funcionalidades e melhorias recentemente lançadas ou melhoradas para serviços de dados ativados pelo Azure Arc. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>março de 2021

O lançamento de março de 2021 foi inicialmente introduzido a 5 de abril de 2021, e as fases finais de lançamento foram concluídas a 9 de abril de 2021.

Rever limitações desta versão em [questões conhecidas - Azure Arc permitiu serviços de dados (Preview)](known-issues.md).

Número da versão Azure Data CLI `azdata` ( 20.3.2. Pode instalar `azdata` a partir do [Azure Data CLI `azdata` ()](/sql/azdata/install/deploy-install-azdata).

### <a name="data-controller"></a>Controlador de dados

- Implementar O Azure Arc ativou o controlador de dados dos serviços de dados no modo de ligação direta a partir do portal. Iniciar a partir do controlador de [dados implementar - modo de ligação direta - pré-requisitos](deploy-data-controller-direct-mode-prerequisites.md).

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>Azure Arc habilitado a hiperescala pósgresql

Ambas as definições de recursos personalizados (CRD) para PostgreSQL foram consolidadas num único CRD. Consulte a tabela seguinte.

|Libertar |CRD |
|-----|-----|
|Fevereiro de 2021 e anterior| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|Início de março de 2021 | postgresqls.arcdata.microsoft.com

Eliminará os CRDs anteriores à medida que limpa as instalações passadas. Ver [Limpeza de instalações passadas.](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)

### <a name="azure-arc-enabled-sql-managed-instance"></a>SQL Managed Instance preparado para o Azure Arc

- Pode agora criar uma instância gerida pelo SQL a partir do portal Azure no modo ligado diretamente.

- Pode agora restaurar uma base de dados para SQL Managed Instance com 3 réplicas e será automaticamente adicionada ao grupo de disponibilidade. 

- Agora pode ligar-se a um ponto final secundário apenas de leitura em SQL Casos Geridos implantados com 3 réplicas. Utilize `azdata arc sql endpoint list` para ver o ponto final de ligação de leitura secundária.

### <a name="known-issues"></a>Problemas conhecidos

- No modo ligado diretamente, o upload de utilização, métricas e registos de utilização `azdata arc dc upload` está atualmente bloqueado. A utilização é automaticamente carregada. O upload para o controlador de dados criado em modo indireto conectado deve continuar a funcionar.
- A implementação do controlador de dados em modo direto só pode ser feita a partir do portal Azure, e não está disponível a partir de ferramentas do cliente como azdata, Azure Data Studio ou kubectl.
- A implementação do Azure Arc ativado SQL Managed Instance em modo direto só pode ser feita a partir do portal Azure, e não está disponível a partir de ferramentas como azdata, Azure Data Studio ou kubectl.
- A implementação da hiperescala postgeSQL ativada no modo direto não está atualmente disponível.
- O upload automático dos dados de utilização no modo de conectividade direta não será bem sucedido se utilizar o proxy via `–proxy-cert <path-t-cert-file>` .

## <a name="february-2021"></a>Fevereiro de 2021

### <a name="new-capabilities-and-features"></a>Novas capacidades e funcionalidades

Número da versão Azure Data CLI `azdata` ( 20.3.1. Pode instalar `azdata` a partir do [Azure Data CLI `azdata` ()](/sql/azdata/install/deploy-install-azdata).

Atualizações adicionais incluem:

- SQL Managed Instance preparado para o Azure Arc
   - Alta disponibilidade com grupos de disponibilidade sempre

- Azure Arc habilitado postgreSQL Hyperscale Azure Data Studio: 
   - A página geral mostra agora o estado do grupo de servidor itemizado por nó
   - Uma nova página de propriedades está agora disponível para mostrar mais detalhes sobre o grupo de servidor
   - Configurar parâmetros do motor postgres da página **de parâmetros do nó**

Para questões associadas a esta versão, consulte [questões conhecidas - Azure Arc habilitado serviços de dados (Pré-visualização)](known-issues.md)

## <a name="january-2021"></a>Janeiro de 2021

### <a name="new-capabilities-and-features"></a>Novas capacidades e funcionalidades

Número da versão Azure Data CLI `azdata` ( 20.3.0. Pode instalar `azdata` a partir do [Azure Data CLI `azdata` ()](/sql/azdata/install/deploy-install-azdata).

Atualizações adicionais incluem:
- Portal localizado disponível para 17 novos idiomas
- Pequenas alterações nos ficheiros Kube-native .yaml
- Novas versões de Grafana e Kibana
- Problemas com ambientes Python ao usar azdata em cadernos no Azure Data Studio resolvidos
- A extensão pg_audit já está disponível para a Escala de Hiperescala PostgreSQL
- Um ID de backup já não é necessário quando se faz uma restauração completa de uma base de dados de hiperescala PostgreSQL
- O estado (estado de saúde) é reportado para cada um dos casos postgreSQL que constituem um grupo de corte

   Em lançamentos anteriores, o estado foi agregado ao nível do grupo do servidor e não itemado ao nível do nó PostgreSQL.

- As implementações pós-SQL agora honram os parâmetros de tamanho de volume indicados na criação de comandos
- Os parâmetros da versão do motor são agora honrados ao editar um grupo de servidores
- A convenção de nomeação das cápsulas para Azure Arc habilitada a Hiperescala PósgreSQL mudou

    Está agora na forma: `ServergroupName{c, w}-n` . Por exemplo, um grupo de servidores com três nóns, um nó coordenador e dois nóiros operários é representado como:
   - `Postgres01c-0` (nó coordenador)
   - `Postgres01w-0` (nó do trabalhador)
   - `Postgres01w-1` (nó do trabalhador)

## <a name="december-2020"></a>Dezembro de 2020

### <a name="new-capabilities--features"></a>Novas funcionalidades & de capacidades

Número da versão Azure Data CLI `azdata` ( 20.2.5. Pode instalar `azdata` a partir do [Azure Data CLI `azdata` ()](/sql/azdata/install/deploy-install-azdata).

Ver pontos finais para sql Gestd Instance e PostgreSQL Hyperscale usando o Azure Data CLI `azdata` () com `azdata arc sql endpoint list` `azdata arc postgres endpoint list` comandos.

Editar os pedidos e limites do recurso SQL Managed Instance (NÚCLEO e memória da CPU) utilizando o Azure Data Studio.

Azure Arc ativado PostgreSQL Hyperscale agora suporta o ponto de restauração no tempo, além de restaurar totalmente a cópia de segurança para ambas as versões 11 e 12 de PostgreSQL. O ponto no tempo restaurar a capacidade permite-lhe indicar uma data e hora específicas para restaurar.

A convenção de nomeação das cápsulas para Azure Arc ativada postgreSQL Hyperscale mudou. Está agora no formulário: ServergroupName{r, s}-_n_. Por exemplo, um grupo de servidores com três nóns, um nó coordenador e dois nóiros operários é representado como:
- `postgres02r-0` (nó coordenador)
- `postgres02s-0` (nó do trabalhador)
- `postgres02s-1` (nó do trabalhador)

### <a name="breaking-change"></a>Mudança de rutura

#### <a name="new-resource-provider"></a>Novo fornecedor de recursos

Esta versão introduz um fornecedor de [recursos](../../azure-resource-manager/management/azure-services-resource-providers.md) atualizado chamado `Microsoft.AzureArcData` . Antes de poder utilizar esta funcionalidade, tem de registar este fornecedor de recursos. 

Para registar este fornecedor de recursos: 

1. No portal Azure, selecione **Subscrições** 
2. Escolher a sua subscrição
3. Em **Definições**, selecione **fornecedores de recursos** 
4. Procurar `Microsoft.AzureArcData` e selecionar **Registar** 

Pode rever etapas detalhadas nos [fornecedores e tipos de recursos da Azure.](../../azure-resource-manager/management/resource-providers-and-types.md) Esta alteração também remove todos os recursos Azure existentes que você carregou para o portal Azure. Para utilizar o fornecedor de recursos, é necessário atualizar o controlador de dados e utilizar o mais recente `azdata` CLI.  

### <a name="platform-release-notes"></a>Notas de versão da plataforma

#### <a name="direct-connectivity-mode"></a>Modo de conectividade direta

Esta versão introduz o modo de conectividade direta. O modo de conectividade direta permite ao controlador de dados enviar automaticamente as informações de utilização para o Azure. Como parte do upload de utilização, o recurso do controlador de dados Arc é automaticamente criado no portal, se ainda não for criado através do `azdata` upload.  

Pode especificar conectividade direta quando criar o controlador de dados. O exemplo a seguir cria um controlador de dados com `azdata arc dc create` o nome de modo de conectividade direta `arc` `connectivity-mode direct` (). Antes de executar o exemplo, `<subscription id>` substitua-o pelo seu ID de subscrição.

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>Problemas conhecidos

- No Serviço Azure Kubernetes (AKS), a versão 1.19.x da Kubernetes não é suportada.
- Em Kubernetes 1.19 `containerd` não é suportado.
- O recurso de controlador de dados em Azure é atualmente um recurso Azure. Quaisquer atualizações como a eliminação não são propagadas de volta ao cluster kubernetes.
- Os nomes de exemplos não podem ser superiores a 13 caracteres
- Não há atualização no local para o controlador de dados do Arco Azure ou casos de base de dados.
- As imagens do contentor de serviços de dados preparados para o Arc não são assinadas.  Pode ter de configurar os nós do Kubernetes para permitir a solicitação de imagens de contentor não assinadas.  Por exemplo, se estiver a utilizar o Docker como tempo de funcionaamento do contentor, pode definir a variável ambiente DOCKER_CONTENT_TRUST=0 e reiniciar.  Outros runtimes de contentor têm opções semelhantes, como em [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration).
- Não é possível criar instâncias geridas pelo SQL ou grupos de servidores de hiperescala PostgreSQL a partir do portal Azure.
- Por enquanto, se estiver a utilizar o NFS, tem de definir `allowRunAsRoot` no seu ficheiro de perfil de `true` implementação antes de criar o controlador de dados Azure Arc.
- Apenas a autenticação de login SQL e PostgreSQL.  Sem suporte para Azure Ative Directory ou Ative Directory.
- A criação de um controlador de dados no OpenShift requer restrições de segurança descontraídas.  Para obter mais detalhes, veja a documentação.
- Se estiver a utilizar o motor do Serviço Azure Kubernetes (AKS) no Azure Stack Hub com o controlador de dados Azure Arc e as instâncias de base de dados, o upgrade para uma versão mais recente de Kubernetes não é suportado. Desinstale o controlador de dados do Azure Arc e todas as instâncias da base de dados antes de atualizar o cluster Kubernetes.
- Os clusters AKS que abrangem [várias zonas de disponibilidade](../../aks/availability-zones.md) não são atualmente suportados para serviços de dados habilitados a Azure Arc. Para evitar este problema, quando criar o cluster AKS no portal Azure, se selecionar uma região onde as zonas estão disponíveis, limpe todas as zonas do controlo de seleção. Veja a imagem seguinte:

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="Limpe as caixas de verificação para cada zona especificar nenhuma.":::

## <a name="october-2020"></a>Outubro de 2020 

Número da versão Azure Data CLI `azdata` ( 20.2.3. Pode instalar `azdata` a partir do [Azure Data CLI `azdata` ()](/sql/azdata/install/deploy-install-azdata).

### <a name="breaking-changes"></a>Alterações interruptivas

Esta versão introduz as seguintes alterações de rutura: 

* Na definição de recurso personalizado PostgreSQL (CRD), o termo `shards` é renomeado para `workers` . Este termo corresponde `workers` ao nome do parâmetro da linha de comando.

* `azdata arc postgres server delete` solicita a confirmação antes de apagar uma instância postgres.  Use `--force` para saltar o pedido.

### <a name="additional-changes"></a>Alterações adicionais

* Um novo parâmetro opcional foi adicionado ao `azdata arc postgres server create` chamado `--volume-claim mounts` . O valor é uma lista separada por vírgulas de suportes de reivindicação de volume. Um suporte de reivindicação de volume é um par de tipo de volume e nome de PVC. O único tipo de volume atualmente suportado é `backup` .  No PostgreSQL, quando o tipo de volume `backup` é, o PVC é montado para `/mnt/db-backups` .  Isto permite a partilha de backups entre instâncias PostgresSQL para que a cópia de segurança de uma instância PostgresSQL possa ser restaurada em outro caso.

* Novos nomes curtos para definições de recursos personalizados PostgresSQL: 
  * `pg11` 
  * `pg12`
* O upload de telemetria fornece ao utilizador:
   * Número de pontos enviados para Azure ou 
   * Se nenhum dado tiver sido carregado para Azure, um pedido para tentar novamente.
* `azdata arc dc debug copy-logs` agora também lê a partir de `/var/opt/controller/log` pasta e recolhe registos de motores PostgreSQL no Linux.
*   Apresente um indicador de trabalho durante a criação e restauro da cópia de segurança com a Hiperescala PostgreSQL.
* `azdata arc postrgres backup list` agora inclui informações sobre o tamanho da cópia de segurança.
* A propriedade do nome de administração sql Managed Instance foi adicionada à coluna direita da lâmina de visão geral no portal Azure.
* O Azure Data Studio suporta o número de nós de trabalho, vCore e definições de memória para a Escala de Hiperescala PostgreSQL. 
* A pré-visualização suporta o backup/restauro para as versões 11 e 12 do Postgres.

## <a name="september-2020"></a>Setembro de 2020

Os serviços de dados habilitados Azure Arc são lançados para pré-visualização pública. Os serviços de dados habilitados pelo Arc permitem-lhe gerir serviços de dados em qualquer lugar.

- Instância Gerida do SQL
- Hiperescala pós-alta

Para obter [instruções, o que são os serviços de dados habilitados a Azure Arc?](overview.md)

## <a name="next-steps"></a>Passos seguintes

> **Só quer experimentar as coisas?**  
> Inicie-se rapidamente com [o Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) em AKS, AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) ou num Azure VM.

- [Instale as ferramentas do cliente](install-client-tools.md)
- [Criar o controlador de dados Azure Arc](create-data-controller.md) (requer a instalação das ferramentas do cliente primeiro)
- [Criar um exemplo gerido pelo Azure SQL em Azure Arc](create-sql-managed-instance.md) (requer a criação de um controlador de dados Azure Arc primeiro)
- [Criar uma base de dados Azure para o grupo de servidores de hiperescala PostgreSQL em Azure Arc](create-postgresql-hyperscale-server-group.md) (requer a criação de um controlador de dados Azure Arc primeiro)
- [Resource providers for Azure services](../../azure-resource-manager/management/azure-services-resource-providers.md) (Fornecedor de recursos para serviços do Azure)
