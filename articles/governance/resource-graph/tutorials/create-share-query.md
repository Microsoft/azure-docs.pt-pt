---
title: Criar e partilhar uma consulta no portal do Azure
description: Neste tutorial, aprenda a criar uma consulta de grafo de recursos e a compartilhá-la com outras pessoas na portal do Azure.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: resource-graph
ms.openlocfilehash: e49cff5a9b9b2c8841b6cc685d4778688c214d26
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622487"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Tutorial: criar e compartilhar uma consulta do grafo de recursos do Azure no portal do Azure

O Gerenciador de gráficos de recursos do Azure permite salvar suas consultas de grafo de recursos diretamente no portal do Azure. Há dois tipos de consultas: _privado_ e _compartilhado_. Uma consulta privada é salva em suas configurações de portal do Azure. Enquanto uma consulta compartilhada é um recurso do Resource Manager que pode ser gerenciado com controles de acesso baseado em função (RBAC) e protegido com bloqueios de recursos.

Ao salvar consultas no portal do Azure, você economiza o tempo que, de outra forma, você pode gastar procurando suas consultas favoritas ou comumente usadas. Ao compartilhar consultas, você ajuda sua equipe a perceber as metas de consistência e eficiência por meio de repetição.

Neste tutorial, você concluirá as seguintes tarefas:

> [!div class="checklist"]
> - Criar e excluir uma consulta privada
> - Criar uma consulta compartilhada
> - Descobrir consultas compartilhadas
> - Excluir uma consulta compartilhada

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-and-delete-a-private-query"></a>Criar e excluir uma consulta privada

As consultas particulares são acessíveis e visíveis somente para a conta que as cria. À medida que são salvas nas configurações de portal do Azure de uma conta, elas podem ser criadas, usadas e excluídas somente de dentro do portal do Azure. Uma consulta privada não é um recurso do Resource Manager. Para criar uma nova consulta privada, siga estas etapas:

1. No menu do portal, selecione **todos os serviços** ou use a caixa Azure Search na parte superior de todas as páginas. Pesquise e selecione explorador de **grafo de recursos**.

1. Na guia **consulta 1** da página do Gerenciador de gráficos de recursos do Azure, insira a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selecione **Executar consulta** para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre essa consulta, consulte [amostras – contagem de máquinas virtuais por tipo de so](../samples/starter.md#count-virtual-machines-by-os-type).


1. Selecione **salvar** ou **salvar como**, digite **contar VMs por sistema operacional** como o nome, deixe o tipo como **consulta privada**e, em seguida, selecione **salvar** na parte inferior do painel **Salvar consulta** . O título da guia muda de **consulta 1** para **contar VMs por sistema operacional**.

1. Afaste-se do Gerenciador de gráficos de recursos do Azure no portal do Azure e, em seguida, retorne a ele. Observe que a consulta salva não é mais exibida e a guia **consulta 1** foi retornada.

1. Selecione **abrir uma consulta**. Verifique se o tipo é **consulta privada**. O nome salvo **VMs de contagem por so** agora aparece na lista **nome da consulta** . Quando você seleciona o link de título da consulta salva, ele é carregado em uma nova guia com o nome da consulta.

   > [!NOTE] 
   >Quando uma consulta salva é aberta e a guia mostra seu nome, selecionar o botão **salvar** atualiza-o com as alterações que foram feitas. Para criar uma nova consulta salva com base nessa consulta aberta, selecione **salvar como** e continue como se você estivesse salvando uma consulta totalmente nova.

1. Para excluir a consulta salva, selecione **abrir uma consulta** novamente e verifique se o campo **tipo** está definido como **consulta privada**. Na linha da consulta de `Count VMs by OS` salva, selecione **excluir** (ícone de lixeira). Na caixa de diálogo de confirmação, selecione **Sim** para concluir a exclusão da consulta.
   Em seguida, feche o painel **abrir uma consulta** .

## <a name="create-a-shared-query"></a>Criar uma consulta compartilhada

Ao contrário de uma consulta privada, uma consulta compartilhada é um recurso do Resource Manager. Esse fato significa que a consulta é salva em um grupo de recursos, pode ser gerenciada e controlada com o RBAC e pode até mesmo ser protegida com bloqueios de recursos. Como um recurso, qualquer pessoa que tenha as permissões apropriadas pode vê-lo e usá-lo.
Para criar uma nova consulta compartilhada, siga estas etapas:

1. No menu do portal, selecione **todos os serviços**ou use a caixa Azure Search na parte superior de todas as páginas para procurar e selecionar **Gerenciador de grafo de recursos**.

1. Na guia **consulta 1** da página do Gerenciador de gráficos de recursos do Azure, insira a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selecione **Executar consulta** para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre essa consulta, consulte [amostras – contagem de máquinas virtuais por tipo de so](../samples/starter.md#count-virtual-machines-by-os-type).

1. Selecione **salvar** ou **salvar como**.

   
   ![Salvar a nova consulta usando o botão salvar](../media/create-share-query/save-shared-query-buttons.png)

1. No painel **Salvar consulta** , insira **contar VMs por sistema operacional** para o nome.

1. Altere o tipo para **consulta compartilhada**, defina a descrição como **contagem de máquinas virtuais por tipo de sistema operacional**e defina **assinatura** para especificar onde o recurso de consulta será criado.

1. Deixe a caixa de seleção **publicar no recurso-grafo-consultas** selecionada e o **local do grupo de recursos** definido como **(US) Oeste EUA Central**.

1. Selecione **salvar** na parte inferior do painel **Salvar consulta** . O título da guia muda de **consulta 1** para **contar VMs por sistema operacional**. Na primeira vez que o grupo de recursos **Resource-Graph-queries** é usado, o salvamento demora mais do que o esperado à medida que o grupo de recursos é criado.
   
   ![Salvar a nova consulta como uma consulta compartilhada](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Você pode desmarcar a caixa de seleção **publicar no recurso-grafo-consultas** se desejar fornecer o nome de um grupo de recursos existente para salvar a consulta compartilhada. Usar o grupo de recursos nomeado padrão para consultas torna as consultas compartilhadas mais fáceis de descobrir. Ele também torna a finalidade desse grupo de recursos mais aparente. No entanto, você pode optar por selecionar um grupo de recursos existente por motivos de segurança com base em permissões existentes.

1. Afaste-se do Gerenciador de gráficos de recursos do Azure no portal do Azure e, em seguida, retorne a ele. Observe que a consulta salva não é mais exibida e a guia **consulta 1** foi retornada.

1. Selecione **abrir uma consulta**. Verifique se o tipo está definido como **consulta compartilhada** e se a combinação de **assinatura** e **grupo de recursos** corresponde ao local em que você salvou a consulta. As VMs de contagem salvas por item do **sistema operacional** agora aparecem na lista **nome da consulta** . Selecione o link título da consulta salva para carregá-la em uma nova guia com o nome dessa consulta. Como uma consulta compartilhada, ela exibe um ícone na guia ao lado do título, denotando-o como compartilhado.

   ![Mostrar a consulta compartilhada com o ícone](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Quando uma consulta salva é aberta e a guia mostra seu nome, o botão **salvar** o atualiza com as alterações feitas. Para criar uma nova consulta salva, selecione **salvar como** e continue como se você estivesse salvando uma consulta totalmente nova.

## <a name="discover-shared-queries"></a>Descobrir consultas compartilhadas

Como uma consulta compartilhada é um recurso do Resource Manager, há várias maneiras de encontrar uma:

- No Gerenciador de gráficos de recursos, selecione **abrir uma consulta** e defina o tipo como **consulta compartilhada**.
- Na página do portal de consultas do grafo de recursos.
- No grupo de recursos em que a consulta compartilhada foi salva.
- Por meio de uma consulta ao grafo de recursos.

### <a name="view-resource-graph-queries"></a>Exibir consultas de grafo de recursos

No portal do Azure, a página consultas do grafo de recursos exibe consultas compartilhadas às quais a conta conectada tem acesso. Esta página habilita a filtragem por nome, assinatura, grupo de recursos e outras propriedades da consulta do grafo de recursos. Você também pode marcar, exportar e excluir consultas de grafo de recursos usando essa interface.

A seleção de uma das consultas abre a página de consulta do gráfico de recursos. Assim como outros recursos do Resource Manager, esta página oferece uma visão geral interativa juntamente com o log de atividades, o controle de acesso e as marcas. Você também pode aplicar um bloqueio de recurso diretamente desta página.

Acesse a página consultas de grafo de recursos no menu do portal selecionando **todos os serviços** ou usando a caixa Azure Search na parte superior de todas as páginas. Pesquise e selecione **Explorador de grafo de recursos**.

### <a name="list-resource-groups-resources"></a>Listar recursos de grupos de recursos

A consulta do grafo de recursos é listada junto com outros recursos que fazem parte de um grupo de recursos.
Selecionar a consulta de grafo de recursos abre a página para essa consulta. As opções de menu de atalho e reticências (disparadas clicando com o botão direito do mouse) funcionam da mesma forma que na página consulta de grafo de recursos.

### <a name="query-resource-graph"></a>Grafo de recursos de consulta

Você pode encontrar consultas de grafo de recursos por meio de uma consulta ao grafo de recursos. Os seguintes limites de consulta do grafo de recursos por tipo `Microsoft.ResourceGraph/queries`e, em seguida, usa `project` para listar apenas o nome, o tempo modificado e a própria consulta:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Excluir uma consulta compartilhada

Se uma consulta compartilhada não for mais necessária, exclua-a. Ao excluir uma consulta compartilhada, você remove o recurso do Resource Manager correspondente. Todos os dashboards aos quais o gráfico de resultados foi fixado agora exibem uma mensagem de erro. Quando essa mensagem de erro for exibida, use o botão **remover do painel** para limpar seu painel.

Você pode excluir uma consulta compartilhada por meio das seguintes interfaces:
- Página consultas do grafo de recursos
- Página de consulta do grafo de recursos
- A página **abrir uma consulta** no Gerenciador de grafo de recursos
- Página grupos de recursos

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este tutorial, exclua as consultas particulares e compartilhadas que você criou se não quiser mais.

## <a name="next-steps"></a>Passos seguintes

- Execute sua primeira consulta usando o [portal do Azure](../first-query-portal.md).
- Obtenha mais informações sobre a [linguagem de consulta](../concepts/query-language.md).
- Saiba mais sobre como [explorar recursos](../concepts/explore-resources.md).
- Consulte exemplos de [consultas iniciais](../samples/starter.md).
- Consulte exemplos de [consultas avançadas](../samples/advanced.md).
- Forneça comentários sobre o [UserVoice](https://feedback.azure.com/forums/915958-azure-governance).