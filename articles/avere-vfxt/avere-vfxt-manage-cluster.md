---
title: Gerir o cluster de vFXT Avere - Azure
description: Como gerir o cluster de Avere - adicionar ou remover nós, reiniciar, parar ou destruir o cluster vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: v-erkell
ms.openlocfilehash: be9205fdf7fec0661d7382ed0d1bedf47487b15e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60409775"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Gerir o cluster do Avere vFXT

Depois de criar o cluster, poderá ter de adicionar nós de cluster ou parar ou reiniciar o cluster. E quando o projeto estiver concluído precisa saber como parar e remova o cluster permanentemente. 

Consoante a tarefa de gestão do cluster, poderá ter de utilizar o painel de controle de Avere, o script de criação do cluster de linha de comandos vfxt.py ou o portal do Azure para fazê-lo. 

Esta tabela fornece uma descrição geral dos quais ferramentas podem ser usadas para cada tarefa. 

| Ação | Painel de controlo de Avere | vfxt.py  | Portal do Azure |
| --- | --- | --- | --- |
| Adicionar nós de cluster | não | sim | não |
| Remover nós de cluster | sim | não | não |
| Parar um nó de cluster | Sim (pode também reinicie os serviços ou reinício) | não | desativação de um nó VM no portal é interpretado como uma falha de nó |
| Iniciar um nó parado | não | não | sim |
| Destrua um único nó de cluster | não | não | sim |
| Reinicie o cluster |  |  |  |
| Encerrar ou parar o cluster com segurança | sim | sim | não |
| Destrua o cluster  | não | sim | Sim, mas não é garantida a integridade dos dados |

Instruções detalhadas para cada ferramenta estão incluídas abaixo.

## <a name="about-stopped-instances-in-azure"></a>Sobre as instâncias paradas no Azure

Quando encerra ou parar qualquer VM do Azure, ele deixa de incorrer em custos de computação, mas ainda terá de pagar por seu armazenamento. Se encerrar um nó de vFXT ou o cluster inteiro vFXT e não pretender reiniciá-lo, deve utilizar o portal do Azure para eliminar as VMs relacionadas. 

No portal do Azure, um *parado* nó (que pode ser reiniciada) mostra o estado **parado** no portal do Azure; um *eliminado* nó mostra o estado **parado (desalocada)**  e ele já não incorre em encargos de computação ou de armazenamento.

Antes de eliminar a VM, certifique-se de que todos os dados alterados foi escrito da cache para o armazenamento de back-end, utilizando as opções de painel de controlo de Avere ou vfxt.py pare ou encerre o cluster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Gerir o cluster com o painel de controlo de Avere 

O painel de controle de Avere podem ser utilizado para as tarefas seguintes: 

* Parar ou reiniciar os nós individuais
* Remover um nó do cluster
* Parar ou reiniciar todo o cluster

Painel de controlo de Avere dá prioridade à integridade de dados, para que ele tenta gravar todos os dados alterados para o armazenamento de back-end antes de uma operação possivelmente destrutiva. Desta forma, uma opção mais segura do que o portal de Avere. 

Painel de controlo de Avere de acesso de um navegador da web. Siga as instruções em [aceder ao cluster vFXT](avere-vfxt-cluster-gui.md) se precisar de ajuda.

### <a name="manage-nodes-with-avere-control-panel"></a>Gira nós com painel de controlo de Avere

O **FXT nós** página de definições tem controlos para o gerenciamento de nós individuais.

Para encerrar, reiniciar ou remover um nó, localizar o nó na lista no **FXT nós** página e clique no botão apropriado no seu **ações** coluna.

> [!NOTE] 
> Endereços IP pode ser movida entre os nós do cluster quando altera o número de nós ativos.

Leia [Cluster > nós FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) no guia de configurações de cluster de Avere para obter mais informações.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Parar ou reiniciar o cluster com o painel de controlo de Avere

O **manutenção do sistema** página de definições tem de comandos para reiniciar os serviços de cluster, reiniciando o cluster ou em segurança desativando o cluster. Leia [administração > manutenção do sistema](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (no Avere cluster guia configurações) para obter detalhes.

Quando um cluster está a ser encerrado, publica mensagens de estado para o **Dashboard** separador em primeiro lugar. Após alguns instantes, a sessão de painel de controlo de Avere deixa de responder, o que significa que o cluster foi encerrado.

## <a name="manage-the-cluster-with-vfxtpy"></a>Gerir o cluster com vfxt.py

vfxt.PY é uma ferramenta da linha de comandos para a gestão e de criação do cluster. 

vfxt.PY é pré-instalado no controlador de cluster VM. Se pretender instalá-la em outro sistema, consulte a documentação em <https://github.com/Azure/AvereSDK>.

O script de vfxt.py pode ser utilizado para estas tarefas de gestão do cluster:

* Adicionar novos nós a um cluster
* Parar ou iniciar um cluster  
* Destrua um cluster

Como o painel de controlo de Avere, operações de vfxt.py tentam para se certificar de que os dados alterados são permanentemente armazenados no armazenamento de back-end antes de encerrar ou destruir o cluster ou nó. Desta forma, uma opção mais segura do que o portal de Avere.

Um guia de utilização vfxt.py completo está disponível no GitHub: [Gestão de cluster na cloud com vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Adicionar nós de cluster com vfxt.py

Um exemplo de script de comando para adicionar nós de cluster está incluído no controlador de cluster. Localize ``./add-nodes`` no controlador e abra-o num editor de personalizá-lo com as suas informações de cluster. 

Tem de executar o cluster utilizar este comando. 

Fornece os seguintes valores: 

* Nome do grupo de recursos para o cluster e também para recursos de rede e armazenamento, se não forem o mesmo que o cluster
* Localização do cluster
* Rede de cluster e a sub-rede 
* Função de acesso de nó de cluster (utilizar a função incorporada [Avere operador](../role-based-access-control/built-in-roles.md#avere-operator))
* Endereço IP de gestão do cluster e a palavra-passe administrativa 
* Número de nós a adicionar (1, 2 ou 3)
* Instância cache e o tipo de tamanho valores de nó 

Se não estiver usando o protótipo, deve construir um comando como o seguinte, incluindo todas as informações descritas acima. 

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

Para obter mais informações, leia [adicionar nós a um cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) no guia de utilização de vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Parar um cluster com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Iniciar um cluster de parado com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Uma vez que o cluster estiver parado, tem de passar os identificadores de instância para especificar os nós do cluster. Leia [especificando qual cluster para modificar](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) no guia de utilização de vfxt.py para saber mais.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Destrua um cluster com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A opção ``--quick-destroy`` pode ser utilizado se não desejar gravar dados alterados a partir da cache de cluster.

Leitura a [guia de utilização de vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) para obter informações adicionais.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Gerir VMs do cluster a partir do portal do Azure 

O portal do Azure pode ser utilizado para destruir o cluster de VMs individualmente, mas a integridade dos dados não é garantida, se o cluster não é encerrado primeiro corretamente. 

O portal do Azure pode ser utilizado para essas tarefas de gestão do cluster: 

* Iniciar um nó de vFXT parado
* Parar um nó de vFXT individuais (o cluster interpreta isso como uma falha de nó)
* Destrua um cluster de vFXT *se* não é necessário garantir que os dados alterados na cache de cluster foi escritos para o filtro de núcleo
* Remover permanentemente o vFXT nós e outros recursos de cluster depois de eles foram encerrados com segurança

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Reiniciar instâncias de vFXT do portal do Azure

Se precisar de reiniciar um nó de parada, tem de utilizar o portal do Azure. Selecione **máquinas virtuais** no menu à esquerda e, em seguida, clique no nome da VM na lista para abrir a página de descrição geral.

Clique nas **iniciar** botão na parte superior da página Descrição geral para reativar a VM.

![Ecrã do portal do Azure que mostra a opção para iniciar uma vm parada](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Eliminar nós de cluster

Se quiser eliminar um nó do vFXT cluster mas manter o restante do cluster, deve primeiro [remove o nó do cluster](#manage-nodes-with-avere-control-panel) com o painel de controle de Avere.

> [!CAUTION]
> Se eliminar um nó sem remover primeiro do vFXT cluster, os dados poderão perder-se.

Para destruir permanentemente uma ou mais instâncias utilizadas como nó vFXT, utilize o portal do Azure.
Selecione **máquinas virtuais** no menu à esquerda e, em seguida, clique no nome da VM na lista para abrir a página de descrição geral.

Clique nas **eliminar** botão na parte superior da página Descrição geral para permanentemente destruir a VM.

Pode utilizar este método para remover permanentemente nós do cluster depois de eles foram encerrados com segurança. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Destrua o cluster no portal do Azure

> [!NOTE] 
> Se pretender que as restantes alterações de cliente na cache de escrita para armazenamento de back-end, utilize o vfxt.py `--destroy` opção ou utilize o painel de controle de Avere para encerrar corretamente a cluster antes de remover as instâncias de nó no portal do Azure.

Pode destruir instâncias de nó permanentemente ao eliminá-los no portal do Azure. Pode eliminá-los um de cada vez conforme descrito acima, ou pode utilizar o **máquinas virtuais** página para localizar todas as VMs do cluster, selecioná-los com as caixas de verificação e clique nas **eliminar** botão para removê-los em todos os uma ação.

![Lista de VMs no portal, filtrado pelo termo "cluster", com três dos quatro verificada e realçado](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Eliminar recursos de cluster adicional no portal do Azure

Se tiver criado especificamente para o cluster de vFXT obter recursos adicionais, convém removê-los como parte da subdividir o cluster. Não destrua elementos que contêm dados que necessários, ou todos os itens que são partilhados com outros projetos.

Para além de eliminar os nós do cluster, considere remover estes componentes: 

* A VM do controlador de cluster
* Discos de dados associados a nós de cluster
* Interfaces de rede e IPs públicos associados aos componentes de cluster
* Redes virtuais
* Contas de armazenamento (**apenas** se contiverem dados não importantes)
* Conjunto de disponibilidade 

![Lista de "todos os recursos", que mostra os recursos de portal do Azure criado para um cluster de teste](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Eliminar grupo de recursos de um cluster a partir do portal do Azure

Se tiver criado um grupo de recursos especificamente para hospedar o cluster, pode destruir todos os recursos relacionados para o cluster com a destruição do grupo de recursos. 

> [!Caution] 
> Apenas destrua o grupo de recursos se tiver a certeza de que nada de valor reside no grupo. Por exemplo, certifique-se de que passaram a todos os dados necessários de quaisquer contentores de armazenamento no grupo de recursos.  

Para eliminar um grupo de recursos, clique em **grupos de recursos** no menu à esquerda do portal e filtro a lista de grupos de recursos para encontrar aquele que criou para o cluster vFXT. Selecione o grupo de recursos e clique nas reticências à direita do painel. Escolha **Eliminar grupo de recursos**. O portal irá pedir-lhe para confirmar a eliminação, que é irreversível.  

![Grupo de recursos que mostra a ação "Eliminar grupo de recursos"](media/avere-vfxt-delete-resource-group.png)
