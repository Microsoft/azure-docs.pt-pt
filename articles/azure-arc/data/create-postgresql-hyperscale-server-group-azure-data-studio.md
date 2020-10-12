---
title: Criar Azure Arc ativado hiperescala pósgresQL usando Azure Data Studio
description: Criar Azure Arc ativado hiperescala pósgresQL usando Azure Data Studio
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 1b1cf90efa14345fa8395f5f62fd80934b922352
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91273012"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>Criar Azure Arc ativado hiperescala pósgresQL usando Azure Data Studio

Este documento percorre os passos para a utilização do Azure Data Studio para providenciar grupos de servidores pós-escala pós-SQL.

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>Ligue-se ao controlador de dados Azure Arc

Antes de criar um caso, inicie sessão no controlador de dados Azure Arc se ainda não tiver iniciado sessão.

```console
azdata login
```

Em seguida, será solicitado o espaço de nome onde o controlador de dados é criado, o nome de utilizador e a palavra-passe para iniciar sessão no controlador.

> Se precisar validar o espaço de nomes, pode correr ```kubectl get pods -A``` para obter uma lista de todos os espaços de nomes do cluster.

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>Passo preliminar e temporário apenas para utilizadores openshift

Implemente este passo antes de passar para o próximo passo. Para implementar o grupo de servidores PostgreSQL Hyperscale no Red Hat OpenShift num projeto que não seja o padrão, é necessário executar os seguintes comandos contra o seu cluster para atualizar as restrições de segurança. Este comando concede os privilégios necessários às contas de serviço que irão executar o seu grupo de servidores De Hiperescala PostgreSQL. A restrição de contexto de segurança (SCC) **_arc-data-scc_** é a que adicionou quando implementou o controlador de dados Azure Arc.

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

_**O nome do grupo do servidor** é o nome do grupo de servidor que irá implementar durante o próximo passo._
   
Para mais detalhes sobre SCCs em OpenShift, consulte a [documentação OpenShift](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html).
Pode agora implementar o próximo passo.

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Criar um grupo de servidores do PostgreSQL Hyperscale preparado para o Azure Arc

1. Lançar Azure Data Studio
1. No separador Ligações, clique nos três pontos no topo esquerdo e escolha "Nova Implementação"
1. A partir das opções de implementação, selecione **o grupo de servidores de hiperescala PostgreSQL - Azure Arc**
    >[!NOTE]
    > Pode ser solicitado que instale o `azdata` CLI aqui se não estiver instalado atualmente.
1. Aceite os termos de Privacidade e licença e clique em **Selecionar** na parte inferior
1. No grupo de servidores de hiperescala PostgreSQL - Lâmina de Arco Azure, introduza as seguintes informações:
   - Insira um nome para o grupo de servidor
   - Introduza e confirme uma palavra-passe para o utilizador administrador de _postgres_ do grupo de servidor
   - Selecione a classe de armazenamento conforme apropriado para os dados
   - Selecione a classe de armazenamento conforme apropriado para os registos
   - Selecione a classe de armazenamento conforme apropriado para cópias de segurança
   - Selecione o número de nós de trabalhadores para a provisão
1. Clique no **botão Implementar**

Isto inicia a criação do grupo de servidores pós-escala pós-escala do Arco Azure no controlador de dados.

Em poucos minutos, a sua criação deve ser concluída com sucesso.

## <a name="next-steps"></a>Passos seguintes
- [Gerencie o seu grupo de servidor usando o Azure Data Studio](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [Monitorize o seu grupo de servidor](monitor-grafana-kibana.md)
- Leia os conceitos e guias de como fazer a Base de Dados Azure para a Hiperescala Pós-SQL para distribuir os seus dados através de vários nós de hiperescala postgreSQL e para beneficiar de toda a potência da Base de Dados Azure para a Escala de Hiperescala PostgresQL. :
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
- [Modelo de recurso Kubernetes](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

