---
title: Gerir o cluster Avere vFXT - Azure
description: Como gerir o cluster Avere - adicione ou remova os nódes, reinicie, pare ou destrua o cluster vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: how-to
ms.date: 01/13/2020
ms.author: rohogue
ms.openlocfilehash: 4135bfe528c33a2beaeb21438181deb5b19ad12e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85505499"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Gerir o cluster do Avere vFXT

Em algum momento do ciclo de vida do seu agrupamento Avere vFXT para Azure, poderá ter de adicionar nós de cluster, ou iniciar ou reiniciar o cluster. Quando o seu projeto estiver concluído, terá de saber como parar o cluster e removê-lo permanentemente.

Este artigo explica como adicionar ou remover nós de cluster e outras operações básicas de cluster. Se necessitar de alterar as definições do cluster ou monitorizar o seu trabalho, utilize o [Painel de Controlo Avere](avere-vfxt-cluster-gui.md).

Dependendo da tarefa de gestão, poderá ter de utilizar uma de três ferramentas diferentes: o Avere Control Panel, o script de gestão de vfxt.py de linha de comando e o portal Azure.

Esta tabela fornece uma visão geral das ferramentas que podem ser utilizadas para cada tarefa.

| Ação | Painel de Controlo de Avere | vfxt.py  | Portal do Azure |
| --- | --- | --- | --- |
| Adicionar nós de cluster | não | sim | não |
| Remover os gádos do cluster | sim | não | não |
| Pare um nó de cluster | sim (também pode reiniciar serviços ou reiniciar) | não | desligar um nó VM do portal é interpretado como uma falha de nó |
| Inicie um nó parado | não | não | sim |
| Destrua um único nó de aglomerado | não | não | sim |
| Reinicie o cluster |  |  |  |
| Desligue ou pare o cluster com segurança | sim | sim | não |
| Destrua o aglomerado  | não | sim | Sim, mas a integridade dos dados não é garantida. |

Instruções detalhadas para cada ferramenta estão incluídas abaixo.

## <a name="about-stopped-instances-in-azure"></a>Sobre casos parados em Azure

Quando desliga ou detém qualquer Azure VM, deixa de incorrer em taxas de cálculo, mas ainda assim tem de pagar o seu armazenamento. Se desligar um nó vFXT ou todo o cluster vFXT e não pretende reiniciá-lo, deve utilizar o portal Azure para eliminar os VMs relacionados.

No portal Azure, um nó *parado* (que pode ser reiniciado) mostra o estado **parado** no portal Azure. Um nó *eliminado* mostra o estado **parado (deallocated)** e já não incorre em taxas de cálculo ou armazenamento.

Antes de eliminar o VM, certifique-se de que todos os dados alterados foram escritos da cache para o armazenamento de back-end utilizando o Painel de Controlo Avere ou vfxt.py opções para parar ou desligar o cluster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Gerir o cluster com o Painel de Controlo Avere

O Painel de Controlo avere pode ser utilizado para estas tarefas:

* Parar ou reiniciar os nóns individuais
* Remover um nó do cluster
* Parar ou reiniciar todo o cluster

O Painel de Controlo avere dá prioridade à integridade dos dados, pelo que tenta escrever quaisquer dados alterados para armazenamento de back-end antes de uma operação possivelmente destrutiva. Isto faz com que seja uma opção mais segura do que o portal Azure.

Aceder ao Painel de Controlo avere a partir de um navegador web. Siga as instruções no [Access do cluster vFXT](avere-vfxt-cluster-gui.md) se precisar de ajuda.

### <a name="manage-nodes-with-avere-control-panel"></a>Gerir os acenos com o Painel de Controlo Avere

A página de definições **de Nodes FXT** tem controlos para gerir os nós individuais.

Para desligar, reiniciar ou remover um nó, encontrar o nó na lista na página **FXT Nodes** e clicar no botão apropriado na coluna **Ações.**

> [!NOTE]
> Os endereços IP podem mover-se entre os nós do cluster quando o número de nós ativos for alterativo.

Leia [cluster > nos nós FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) no guia de definições do cluster Avere para obter mais informações.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Parar ou reiniciar o cluster com o Painel de Controlo Avere

A página de definições **de Manutenção do Sistema** tem comandos para reiniciar os serviços de cluster, reiniciar o cluster ou ligar o cluster de forma segura. Leia [a Administração > Manutenção do Sistema](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (no guia de configurações do cluster Avere) para mais detalhes.

Quando um cluster começa a desligar-se, publica mensagens de estado no **separador Dashboard.** Após alguns momentos, as mensagens param e, eventualmente, a sessão do Painel de Controlo de Avere deixa de responder, o que significa que o cluster foi desligado.

## <a name="manage-the-cluster-with-vfxtpy"></a>Gerir o cluster com vfxt.py

vfxt.py é uma ferramenta de linha de comando para a criação e gestão de clusters.

vfxt.py está pré-instalado no controlador de cluster VM. Se pretender instalá-lo noutro sistema, consulte a documentação em <https://github.com/Azure/AvereSDK> .

O vfxt.py script pode ser usado para estas tarefas de gestão de clusters:

* Adicione novos nosdes a um cluster
* Parar ou iniciar um cluster  
* Destrua um aglomerado

Tal como o Avere Control Panel, vfxt.py operações tentam garantir que os dados alterados são armazenados permanentemente no armazenamento de back-end antes de desligar ou destruir o cluster ou o nó. Isto faz com que seja uma opção mais segura do que o portal Azure.

Um guia completo de utilização vfxt.py está disponível no GitHub: [Cloud cluster management com vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Adicione os nóns de cluster com vfxt.py

Um script de comando de amostra para adicionar nós de cluster está incluído no controlador de cluster. Localize ``./add-nodes`` no controlador e abra-o num editor para personalizá-lo com as informações do seu cluster.

O aglomerado deve estar a funcionar para usar este comando.

Fornecer os seguintes valores:

* Nome do grupo de recursos para o cluster, e também para recursos de rede e armazenamento se não estiverem no mesmo grupo de recursos que o cluster
* Localização do cluster
* Rede de cluster e sub-rede
* Função de acesso ao nó de cluster (utilizar a função incorporada [Avere Operator)](../role-based-access-control/built-in-roles.md#avere-operator)
* Endereço IP de gestão de cluster e senha administrativa
* Número de nós a adicionar (1, 2 ou 3)
* Tipo de instância de nó e valores de tamanho de cache

Se não estiver a utilizar o protótipo, deve construir um comando como o seguinte, incluindo todas as informações descritas acima.

```bash
   vfxt.py --cloud-type azure --from-environment \
   --resource-group GROUP_NAME \
   [--network-resource-group GROUP_NAME --storage-resource-group GROUP_NAME]  \
   --location LOCATION --azure-network NETWORK_NAME --azure-subnet SUBNET_NAME \
   --add-nodes --nodes NODE_COUNT \
   --management-address CLUSTER_IP --admin-password ADMIN_PASSWORD \
   --instance-type TYPE --node-cache-size SIZE \
   --azure-role "Avere Operator" \
   --log ~/vfxt.log
```

Para mais informações, leia [Adicione nós a um cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) no guia de utilização vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Pare um aglomerado com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Inicie um aglomerado parado com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```

Como o cluster está parado, deve passar os identificadores de instância para especificar os nós do cluster. Leia [Especificando qual o cluster a modificar](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) no guia de utilização vfxt.py para saber mais.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Destrua um aglomerado com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A opção ``--quick-destroy`` pode ser utilizada se não quiser guardar dados alterados da cache do cluster.

Leia o [guia de utilização vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) para obter informações adicionais.

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Gerir VMs de cluster a partir do portal Azure

O portal Azure pode ser usado para destruir o cluster VMs individualmente, mas a integridade dos dados não é garantida se o cluster não for desligado de forma limpa primeiro.

O portal Azure pode ser utilizado para estas tarefas de gestão de clusters:

* Inicie um nó vFXT parado
* Pare um nó vFXT individual (o cluster interpreta isto como uma falha no nó)
* Destrua um cluster vFXT *se* não precisar de garantir que os dados alterados na cache do cluster são escritos para o ficheiro principal
* Remova permanentemente os nosdes vFXT e outros recursos de cluster depois de terem sido desligados com segurança

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Reinicie as instâncias vFXT do portal Azure

Se precisar de reiniciar um nó parado, tem de utilizar o portal Azure. Selecione **máquinas virtuais** no menu esquerdo e, em seguida, clique no nome VM na lista para abrir a sua página geral.

Clique no botão **Iniciar** na parte superior da página geral para reativar o VM.

![Ecrã do portal Azure mostrando a opção de iniciar um vm parado](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Eliminar os nosdes de cluster

Se pretender eliminar um nó do cluster vFXT, mas manter o restante do cluster, deve primeiro [retirar o nó do cluster](#manage-nodes-with-avere-control-panel) com o Painel de Controlo Avere.

> [!CAUTION]
> Se eliminar um nó sem primeiro removê-lo do cluster vFXT, os dados podem ser perdidos.

Para destruir permanentemente uma ou mais instâncias usadas como nó vFXT, utilize o portal Azure.
Selecione **máquinas virtuais** no menu esquerdo e, em seguida, clique no nome VM na lista para abrir a sua página geral.

Clique no botão **Eliminar** na parte superior da página geral para destruir permanentemente o VM.

Pode utilizar este método para remover permanentemente os nós do cluster depois de terem sido desligados com segurança.

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Destrua o aglomerado do portal Azure

> [!NOTE]
> Se desejar que as alterações restantes do cliente na cache sejam escritas para armazenamento back-end, utilize a opção vfxt.py `--destroy` ou utilize o Painel de Controlo Avere para desligar o cluster de forma limpa antes de remover as instâncias do nó no portal Azure.

Pode destruir instâncias de nó permanentemente eliminando-as no portal Azure. Pode eliminá-los um de cada vez, conforme descrito acima, ou pode utilizar a página **'Máquinas Virtuais'** para encontrar todos os VMs do cluster, selecioná-los com as caixas de verificação e clicar no botão **Eliminar** para removê-los todos numa só ação.

![Lista de VMs no portal, filtrado pelo termo "cluster", com três dos quatro verificados e destacados](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Eliminar recursos adicionais de cluster do portal Azure

Se criou recursos adicionais especificamente para o cluster vFXT, talvez queira removê-los como parte da demolição do cluster. Não destrua elementos que contenham dados de que necessita, nem quaisquer itens partilhados com outros projetos.

Além de eliminar os nóns de cluster, considere remover estes componentes:

* O controlador de cluster VM
* Discos de dados associados aos nosdes de cluster
* Interfaces de rede e IPs públicos associados a componentes de cluster
* Redes virtuais
* Contentores de armazenamento e contas de armazenamento **(apenas** se não contiverem dados importantes)
* Conjunto de disponibilidade

![Lista de "todos os recursos" do portal Azure mostrando recursos criados para um cluster de testes](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Eliminar o grupo de recursos de um cluster do portal Azure

Se criou um grupo de recursos especificamente para alojar o cluster, pode destruir todos os recursos relacionados para o cluster destruindo o grupo de recursos.

> [!Caution]
> Só destrua o grupo de recursos se tiver a certeza de que nada de valor reside no grupo. Por exemplo, certifique-se de que moveu os dados necessários de quaisquer recipientes de armazenamento dentro do grupo de recursos.  

Para eliminar um grupo de recursos, clique em **grupos** de recursos no menu esquerdo do portal e filtre a lista de grupos de recursos para encontrar o que criou para o cluster vFXT. Selecione o grupo de recursos e clique nos três pontos à direita do painel. Escolha **Eliminar grupo de recursos**. O portal vai pedir-lhe para confirmar a supressão, que é irreversível.

![Grupo de recursos que mostra a ação "Eliminar grupo de recursos"](media/avere-vfxt-delete-resource-group.png)
