---
title: Criar e compartilhar uma consulta no portal do Azure
description: Neste tutorial, aprenda a criar uma consulta de grafo de recursos e a compartilhá-la com outras pessoas na portal do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: 10f93f34923fb2399a4b2053167576ba004ae467
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821684"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-azure-portal"></a>Tutorial: criar e compartilhar uma consulta do grafo de recursos do Azure no portal do Azure

O Gerenciador de gráficos de recursos do Azure permite salvar suas consultas de grafo de recursos diretamente em portal do Azure. Há dois tipos de consultas, _privadas_ e _compartilhadas_. Uma consulta _privada_ é salva em suas configurações de portal do Azure, mas uma consulta _compartilhada_ é um recurso do Resource Manager que pode ser gerenciado com controles de acesso baseado em função (RBAC) e protegido com bloqueios de recursos.

Salvar consultas no portal do Azure poupa o tempo gasto procurando suas consultas favoritas ou comumente usadas. Ao compartilhar consultas, você permite que sua equipe seja consistente e repetível. Neste tutorial, você concluirá estas etapas:

> [!div class="checklist"]
> - Criar e excluir uma consulta _privada_
> - Criar uma consulta _compartilhada_
> - Descobrir consultas _compartilhadas_
> - Excluir uma consulta _compartilhada_

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-and-delete-a-private-query"></a>Criar e excluir uma consulta privada

As consultas _particulares_ só são acessíveis ou visíveis para a conta que as cria. À medida que são salvas nas configurações de portal do Azure de uma conta, elas só podem ser criadas, usadas e excluídas de dentro portal do Azure. Uma consulta _privada_ não é um recurso do Resource Manager. Crie uma nova consulta _privada_ seguindo estas etapas:

1. No menu do portal, selecione "todos os serviços" ou use a caixa de pesquisa do Azure na parte superior de todas as páginas.
   Pesquise e selecione ' Gerenciador de grafo de recursos '.

1. Na guia ' consulta 1 ' na página do Gerenciador de gráficos de recursos do Azure, insira a consulta a seguir. Para obter informações sobre essa consulta, consulte [amostras – contagem de máquinas virtuais por tipo de so](../samples/starter.md#count-virtual-machines-by-os-type).
   Selecione **Executar consulta** para ver os resultados da consulta no painel inferior.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecione **salvar** ou **salvar como**, digite o _nome_ como ' contar VMs por sistema operacional ', deixe o _tipo_ como ' consulta privada ' e, em seguida, selecione **salvar** na parte inferior do painel _Salvar consulta_ . O título da guia muda de ' consulta 1 ' para ' contar VMs por sistema operacional '.

1. Navegue para fora do Gerenciador de grafo de recursos do Azure no portal do Azure e, em seguida, retorne a ele. A consulta salva não é mais exibida e a guia ' consulta 1 ' foi retornada.

1. Selecione **abrir uma consulta**. Verifique se o _tipo_ é ' particular Query '. O "contagem de VMs por so" salvo agora aparece na lista _nome da consulta_ . Selecione o link título da consulta salva e ela será carregada em uma nova guia com o nome das consultas.

   > [!NOTE]
   > Quando uma consulta salva é aberta e a guia mostra o _nome_, o botão **salvar** o atualiza com as alterações feitas. Para criar uma nova consulta salva, use **salvar como** e siga as etapas como se fosse uma consulta salva totalmente nova.

1. Para excluir a consulta salva, selecione **abrir uma consulta** novamente e verifique se o _tipo_ é ' particular Query '. Na linha da consulta "contagem de VMs por sistema operacional" salva, selecione o ícone de lixeira. Na caixa de diálogo de confirmação, selecione **Sim** para concluir a exclusão da consulta. Em seguida, feche o painel _abrir uma consulta_ .

## <a name="create-a-shared-query"></a>Criar uma consulta compartilhada

Ao contrário de uma consulta _privada_ , uma consulta _compartilhada_ é um recurso do Resource Manager. Esse fato significa que a consulta é salva em um grupo de recursos, pode ser gerenciada e controlada com o RBAC e até mesmo protegida com bloqueios de recursos. Como um recurso, qualquer pessoa com as permissões apropriadas pode vê-la e usá-la. Crie uma nova consulta _compartilhada_ seguindo estas etapas:

1. No menu do portal, selecione "todos os serviços" ou use a caixa de pesquisa do Azure na parte superior de todas as páginas.
   Pesquise e selecione ' Gerenciador de grafo de recursos '.

1. Na guia ' consulta 1 ' na página do Gerenciador de gráficos de recursos do Azure, insira a consulta a seguir. Para obter informações sobre essa consulta, consulte [amostras – contagem de máquinas virtuais por tipo de so](../samples/starter.md#count-virtual-machines-by-os-type).
   Selecione **Executar consulta** para ver os resultados da consulta no painel inferior.

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

1. Selecione **salvar** ou **salvar como**.

   ![Salvar a nova consulta usando o botão salvar](../media/create-share-query/save-shared-query-buttons.png)

1. No painel _Salvar consulta_ , insira o _nome_ como ' contar VMs por sistema operacional ', altere o _tipo_ para ' consulta compartilhada ', defina _Descrição_ como ' contagem de máquinas virtuais por tipo de sistema operacional ' e selecione a _assinatura_ na qual o recurso de consulta é criado. Deixe a caixa de seleção "publicar no recurso-grafo-consultas do grupo de recursos" marcada e o _local do grupo de recursos_ definido como "(EUA) Oeste EUA Central". Em seguida, selecione **salvar** na parte inferior do painel _Salvar consulta_ . O título da guia muda de ' consulta 1 ' para ' contar VMs por sistema operacional '. A primeira vez que o grupo de recursos ' Resource-Graph-queries ' é usado, o salvamento leva mais tempo quando o grupo de recursos é criado.

   ![Salvar a nova consulta como uma consulta compartilhada](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE]
   > Se desejar, remova a marca de seleção para fornecer o nome de um grupo de recursos existente no qual salvar a consulta compartilhada. Usar o grupo de recursos nomeado padrão para consultas torna as consultas _compartilhadas_ mais fáceis de descobrir. Isso também torna mais aparente a finalidade desse grupo de recursos. No entanto, a seleção de um grupo de recursos existente pode ser feita por motivos de segurança com base em permissões existentes.

1. Navegue para fora do Gerenciador de grafo de recursos do Azure no portal do Azure e, em seguida, retorne a ele. A consulta salva não é mais exibida e a guia ' consulta 1 ' foi retornada.

1. Selecione **abrir uma consulta**. Verifique se o _tipo_ é "consulta compartilhada" e se a combinação de _assinatura_ e _grupo de recursos_ corresponde ao local em que você salvou a consulta. O "contagem de VMs por so" salvo agora aparece na lista _nome da consulta_ . Selecione o link título da consulta salva e ela será carregada em uma nova guia com o nome das consultas. Como uma consulta _compartilhada_ , ele exibe um ícone na guia ao lado do título que o indica como compartilhado.

   ![Mostrar a consulta compartilhada com o ícone](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE]
   > Quando uma consulta salva é aberta e a guia mostra o _nome_, o botão **salvar** o atualiza com as alterações feitas. Para criar uma nova consulta salva, use **salvar como** e siga as etapas como se fosse uma consulta salva totalmente nova.

## <a name="discover-shared-queries"></a>Descobrir consultas compartilhadas

Como uma consulta _compartilhada_ é um recurso do Resource Manager, há várias maneiras de encontrá-las:

- No Gerenciador de gráficos de recursos, selecione **abrir uma consulta** e defina _tipo_ como ' consulta compartilhada '
- A página do portal de consultas do grafo de recursos
- O grupo de recursos no qual ele foi salvo
- Com uma consulta ao grafo de recursos

### <a name="view-resource-graph-queries"></a>Exibir consultas de grafo de recursos

No portal do Azure, a página consultas do grafo de recursos exibe consultas _compartilhadas_ às quais a conta conectada tem acesso. Esta página permite filtrar por nome, assinatura, grupo de recursos e outras propriedades da consulta do grafo de recursos. As consultas de grafo de recursos também podem ser marcadas, exportadas e excluídas usando essa interface.

A seleção de uma das consultas abre a página de consulta do gráfico de recursos. Assim como outros recursos do Resource Manager, esta página oferece uma visão geral interativa juntamente com o log de atividades, o controle de acesso e as marcas. Um bloqueio de recurso também pode ser aplicado diretamente desta página.

Acesse a página consultas de grafo de recursos no menu do portal selecionando "todos os serviços" ou usando a caixa de pesquisa do Azure na parte superior de todas as páginas. Pesquise e selecione ' Gerenciador de grafo de recursos '.

### <a name="list-resource-groups-resources"></a>Listar recursos de grupos de recursos

A consulta do grafo de recursos é listada junto com outros recursos que fazem parte de um grupo de recursos.
Selecionar a consulta de grafo de recursos abre a página para essa consulta. As opções de reticências ou de clique com o botão direito do mouse funcionam da mesma forma que a página consulta do gráfico de recursos.

### <a name="query-resource-graph"></a>Grafo de recursos de consulta

Como um recurso do Resource Manager, as consultas de grafo de recursos podem ser encontradas com uma consulta ao grafo de recursos.
Os seguintes limites de consulta do grafo de recursos por tipo `Microsoft.ResourceGraph/queries`e, em seguida, usa `project` para listar apenas o nome, o tempo modificado e a própria consulta:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Excluir uma consulta compartilhada

Se uma consulta _compartilhada_ não for mais necessária, exclua-a. A exclusão de uma consulta _compartilhada_ remove o recurso real do Resource Manager. Todos os painéis nos quais o gráfico de resultados foi fixado agora exibem uma mensagem de erro. Quando essa mensagem de erro for exibida, use o botão **remover do painel** para limpar seu painel.

Uma consulta _compartilhada_ pode ser excluída das seguintes interfaces:
- Página consultas do grafo de recursos
- Página de consulta do grafo de recursos
- O Gerenciador de gráficos de recursos abre uma página de consulta
- Página grupos de recursos

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este tutorial, exclua as consultas _particulares_ e _compartilhadas_ que você criou se não quiser mais.

## <a name="next-steps"></a>Passos seguintes

- Executar sua primeira consulta com [portal do Azure](../first-query-portal.md)
- Obter mais informações sobre a [linguagem de consulta](../concepts/query-language.md)
- Aprender a [explorar recursos](../concepts/explore-resources.md)
- Ver exemplos de [Consultas de introdução](../samples/starter.md)
- Ver exemplos de [Consultas avançadas](../samples/advanced.md)
- Enviar comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance)