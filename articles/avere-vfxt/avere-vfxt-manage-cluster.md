---
title: Gerenciar o cluster avere vFXT – Azure
description: Como gerenciar o cluster avere – adicionar ou remover nós, reinicializar, parar ou destruir o cluster vFXT
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: rohogue
ms.openlocfilehash: bcdba7f14147714c5e29c13bfe9e20fa44a27ef9
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72256200"
---
# <a name="manage-the-avere-vfxt-cluster"></a>Gerir o cluster do Avere vFXT

Depois de criar o cluster, talvez seja necessário adicionar nós de cluster ou parar ou reinicializar o cluster. E quando seu projeto for concluído, você precisará saber como parar e remover o cluster permanentemente. 

Dependendo da tarefa de gerenciamento de cluster, talvez seja necessário usar o painel de controle avere, o script de criação do cluster de linha de comando do vfxt.py ou o portal do Azure para fazer isso. 

Esta tabela fornece uma visão geral de quais ferramentas podem ser usadas para cada tarefa. 

| Ação | Painel de controle do avere | vfxt.py  | Portal do Azure |
| --- | --- | --- | --- |
| Adicionar nós de cluster | não | sim | não |
| Remover nós de cluster | sim | não | não |
| Parar um nó de cluster | Sim (também é possível reiniciar os serviços ou reinicializar) | não | a ativação de uma VM de nó do portal é interpretada como uma falha de nó |
| Iniciar um nó parado | não | não | sim |
| Destruir um único nó de cluster | não | não | sim |
| Reinicializar o cluster |  |  |  |
| Desligar ou parar o cluster com segurança | sim | sim | não |
| Destruir o cluster  | não | sim | Sim, mas a integridade dos dados não é garantida |

As instruções detalhadas para cada ferramenta estão incluídas abaixo.

## <a name="about-stopped-instances-in-azure"></a>Sobre as instâncias interrompidas no Azure

Quando você desliga ou interrompe qualquer VM do Azure, ele pára de incorrer em encargos de computação, mas você ainda deve pagar pelo armazenamento. Se você desligar um nó vFXT ou todo o cluster vFXT e não pretender reiniciá-lo, deverá usar o portal do Azure para excluir as VMs relacionadas. 

No portal do Azure, um nó *parado* (que pode ser reiniciado) mostra o status **parado** no portal do Azure; um nó *excluído* mostra o status **parado (desalocado)** e não gera mais cobranças de armazenamento ou de computação.

Antes de excluir a VM, verifique se todos os dados alterados foram gravados do cache para o armazenamento de back-end usando as opções painel de controle avere ou vfxt.py para parar ou desligar o cluster.

## <a name="manage-the-cluster-with-avere-control-panel"></a>Gerenciar o cluster com o painel de controle do avere 

O painel de controle avere pode ser usado para essas tarefas: 

* Parar ou reinicializar nós individuais
* Remover um nó do cluster
* Parar ou reinicializar o cluster inteiro

O painel de controle avere prioriza a integridade dos dados, portanto, ele tenta gravar quaisquer dados alterados no armazenamento de back-end antes de uma operação possivelmente destrutiva. Isso o torna uma opção mais segura do que o portal do avere. 

Acesse o painel de controle do avere em um navegador da Web. Siga as instruções em [acessar o cluster vFXT](avere-vfxt-cluster-gui.md) se precisar de ajuda.

### <a name="manage-nodes-with-avere-control-panel"></a>Gerenciar nós com o painel de controle do avere

A página de configurações de **nós do FXT** tem controles para gerenciar nós individuais.

Para desligar, reinicializar ou remover um nó, localize o nó na lista na página **nós do FXT** e clique no botão apropriado na coluna **ações** .

> [!NOTE] 
> Os endereços IP podem ser movidos entre nós de cluster quando o número de nós ativos é alterado.

Leia os [nós do cluster > FXT](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_fxt_nodes.html#gui-fxt-nodes>) no guia de configurações do cluster avere para obter mais informações.

### <a name="stop-or-reboot-the-cluster-with-avere-control-panel"></a>Parar ou reinicializar o cluster com o painel de controle do avere

A página Configurações de **manutenção do sistema** tem comandos para reiniciar os serviços de cluster, reinicializar o cluster ou desligar o cluster com segurança. Leia [administração > manutenção do sistema](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_system_maintenance.html#gui-system-maintenance>) (no guia de configurações do cluster avere) para obter detalhes.

Quando um cluster está sendo desligado, ele posta mensagens de estado na guia **painel** primeiro. Após alguns instantes, a sessão do painel de controle avere deixará de responder, o que significa que o cluster foi desligado.

## <a name="manage-the-cluster-with-vfxtpy"></a>Gerenciar o cluster com o vfxt.py

vfxt.py é uma ferramenta de linha de comando para a criação e o gerenciamento de cluster. 

o vfxt.py é pré-instalado na VM do controlador de cluster. Se você quiser instalá-lo em outro sistema, consulte a documentação em <https://github.com/Azure/AvereSDK>.

O script vfxt.py pode ser usado para essas tarefas de gerenciamento de cluster:

* Adicionar novos nós a um cluster
* Parar ou iniciar um cluster  
* Destruir um cluster

Como o painel de controle do avere, as operações do vfxt.py tentam garantir que os dados alterados sejam armazenados permanentemente no armazenamento de back-end antes de desligar ou destruir o cluster ou nó. Isso o torna uma opção mais segura do que o portal do avere.

Um guia de uso completo do vfxt.py está disponível no GitHub: [Gerenciamento de cluster de nuvem com vfxt.py](https://github.com/azure/averesdk/blob/master/docs/README.md)

### <a name="add-cluster-nodes-with-vfxtpy"></a>Adicionar nós de cluster com vfxt.py

Um exemplo de script de comando para adicionar nós de cluster está incluído no controlador de cluster. Localize ``./add-nodes`` no controlador e abra-o em um editor para personalizá-lo com as informações do cluster. 

O cluster deve estar em execução para usar este comando. 

Forneça os seguintes valores: 

* Nome do grupo de recursos para o cluster e também para recursos de rede e armazenamento se eles não forem iguais aos do cluster
* Local do cluster
* Rede e sub-rede do cluster 
* Função de acesso de nó de cluster (use a função interna [operador avere](../role-based-access-control/built-in-roles.md#avere-operator))
* Endereço IP de gerenciamento de cluster e senha administrativa 
* Número de nós a serem adicionados (1, 2 ou 3)
* Tipo de instância de nó e valores de tamanho de cache 

Se você não estiver usando o protótipo, deverá construir um comando semelhante ao seguinte, incluindo todas as informações descritas acima. 

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

Para obter mais informações, leia [adicionar nós a um cluster](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#add-nodes-to-a-cluster) no guia de uso do vfxt.py.

### <a name="stop-a-cluster-with-vfxtpy"></a>Parar um cluster com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --stop --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET
```

### <a name="start-a-stopped-cluster-with-vfxtpy"></a>Iniciar um cluster parado com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --start --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --instances INSTANCE1_ID INSTANCE2_ID INSTANCE3_ID ...
```    

Como o cluster é interrompido, você deve passar identificadores de instância para especificar os nós de cluster. Leia [especificando qual cluster deve ser modificado](https://github.com/Azure/AvereSDK/blob/master/docs/using_vfxt_py.md#specifying-which-cluster-to-modify) no guia de uso do vfxt.py para saber mais.

### <a name="destroy-a-cluster-with-vfxtpy"></a>Destruir um cluster com vfxt.py

```bash
vfxt.py --cloud-type azure --from-environment --destroy --resource-group GROUPNAME --admin-password PASSWORD --management-address ADMIN_IP --location LOCATION --azure-network NETWORK --azure-subnet SUBNET --management-address ADMIN_IP
```

A opção ``--quick-destroy`` poderá ser usada se você não quiser gravar dados alterados do cache de cluster.

Leia o [Guia de uso do vfxt.py](<https://github.com/Azure/AvereSDK/blob/master/docs/README.md>) para obter informações adicionais.  

## <a name="manage-cluster-vms-from-the-azure-portal"></a>Gerenciar VMs de cluster do portal do Azure 

O portal do Azure pode ser usado para destruir VMs de cluster individualmente, mas a integridade dos dados não será garantida se o cluster não for completamente desligado primeiro. 

O portal do Azure pode ser usado para essas tarefas de gerenciamento de cluster: 

* Iniciar um nó vFXT interrompido
* Parar um nó vFXT individual (o cluster interpreta isso como uma falha de nó)
* Destrua um cluster vFXT *se* você não precisar garantir que os dados alterados no cache do cluster sejam gravados no Filer principal
* Remova permanentemente os nós vFXT e outros recursos de cluster depois que eles forem desligados com segurança

### <a name="restart-vfxt-instances-from-the-azure-portal"></a>Reinicie as instâncias de vFXT do portal do Azure

Se precisar reiniciar um nó parado, você deverá usar o portal do Azure. Selecione **máquinas virtuais** no menu à esquerda e, em seguida, clique no nome da VM na lista para abrir sua página de visão geral.

Clique no botão **Iniciar** na parte superior da página Visão geral para reativar a VM.

![Tela de portal do Azure mostrando a opção para iniciar uma VM parada](media/avere-vfxt-start-stopped-incurring-annot.png)

### <a name="delete-cluster-nodes"></a>Excluir nós de cluster

Se você quiser excluir um nó do cluster vFXT, mas manter o restante do cluster, primeiro [remova o nó do cluster](#manage-nodes-with-avere-control-panel) com o painel de controle avere.

> [!CAUTION]
> Se você excluir um nó sem primeiro removê-lo do cluster vFXT, os dados poderão ser perdidos.

Para destruir permanentemente uma ou mais instâncias usadas como nó vFXT, use o portal do Azure.
Selecione **máquinas virtuais** no menu à esquerda e, em seguida, clique no nome da VM na lista para abrir sua página de visão geral.

Clique no botão **excluir** na parte superior da página Visão geral para destruir permanentemente a VM.

Você pode usar esse método para remover permanentemente nós de cluster depois que eles tiverem sido desligados com segurança. 

### <a name="destroy-the-cluster-from-the-azure-portal"></a>Destruir o cluster do portal do Azure

> [!NOTE] 
> Se você quiser que as alterações de cliente restantes no cache sejam gravadas no armazenamento de back-end, use a opção vfxt.py `--destroy` ou use o painel de controle avere para desligar o cluster corretamente antes de remover as instâncias de nó no portal do Azure.

Você pode destruir instâncias de nó permanentemente excluindo-as no portal do Azure. Você pode excluí-los um de cada vez, conforme descrito acima, ou pode usar a página **máquinas virtuais** para localizar todas as VMs do cluster, selecioná-las com as caixas de seleção e clicar no botão **excluir** para removê-las todas em uma ação.

![Lista de VMs no portal, filtradas pelo termo "cluster", com três das quatro selecionadas e realçadas](media/avere-vfxt-multi-vm-delete.png)

### <a name="delete-additional-cluster-resources-from-the-azure-portal"></a>Excluir recursos de cluster adicionais do portal do Azure

Se você criou recursos adicionais especificamente para o cluster vFXT, talvez queira removê-los como parte da subdivisão do cluster. Não destrua elementos que contêm dados necessários ou quaisquer itens compartilhados com outros projetos.

Além de excluir os nós de cluster, considere remover estes componentes: 

* A VM do controlador de cluster
* Discos de dados associados a nós de cluster
* Interfaces de rede e IPs públicos associados a componentes de cluster
* Redes virtuais
* Contas de armazenamento (**somente** se não contiverem dados importantes)
* Conjunto de disponibilidade 

![Portal do Azure lista "todos os recursos" mostrando os recursos criados para um cluster de teste](media/avere-vfxt-all-resources-list.png)

### <a name="delete-a-clusters-resource-group-from-the-azure-portal"></a>Excluir o grupo de recursos de um cluster do portal do Azure

Se você criou um grupo de recursos especificamente para hospedar o cluster, poderá destruir todos os recursos relacionados ao cluster destruindo o grupo de recursos. 

> [!Caution] 
> Destrua apenas o grupo de recursos se você tiver certeza de que nada do valor reside no grupo. Por exemplo, verifique se você moveu todos os dados necessários de quaisquer contêineres de armazenamento dentro do grupo de recursos.  

Para excluir um grupo de recursos, clique em **grupos de recursos** no menu à esquerda do portal e filtre a lista de grupos de recursos para localizar aquele que você criou para o cluster vFXT. Selecione o grupo de recursos e clique nos três pontos à direita do painel. Escolha **Eliminar grupo de recursos**. O portal pedirá que você confirme a exclusão, o que é irreversível.  

![Grupo de recursos mostrando a ação "excluir grupo de recursos"](media/avere-vfxt-delete-resource-group.png)
