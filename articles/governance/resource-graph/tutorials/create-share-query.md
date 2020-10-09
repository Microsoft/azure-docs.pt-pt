---
title: 'Tutorial: Gerir consultas no portal Azure'
description: Neste tutorial, cria-se uma Consulta de Gráfico de Recurso e partilha a nova consulta com outros no portal Azure.
ms.date: 06/29/2020
ms.topic: tutorial
ms.openlocfilehash: f602a6ed26f467f2d3395bdf5e3346c83684b108
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88005183"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Tutorial: Criar e partilhar uma consulta de Gráfico de Recursos Azure no portal Azure

O Azure Resource Graph Explorer permite-lhe guardar as suas consultas de Gráfico de Recursos diretamente no portal Azure. Existem dois tipos de consultas: _Privado_ e _Partilhado._ Uma consulta privada é guardada nas definições do seu portal Azure. Enquanto uma consulta partilhada é um recurso Azure Resource Manager que pode ser gerido com o controlo de acesso baseado em funções Azure (Azure RBAC) e protegido com bloqueios de recursos. Ambos os tipos de consultas são encriptados em repouso.

Ao guardar consultas no portal Azure, poupa o tempo que poderia gastar à procura das suas consultas favoritas ou comumente usadas. Quando partilhas consultas, ajudas a tua equipa a perceber objetivos de consistência e eficiência através da repetição.

Neste tutorial, completará as seguintes tarefas:

> [!div class="checklist"]
> - Criar e eliminar uma consulta privada
> - Criar uma consulta partilhada
> - Descubra consultas partilhadas
> - Excluir uma consulta partilhada

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-and-delete-a-private-query"></a>Criar e eliminar uma consulta privada

As consultas privadas são acessíveis e visíveis apenas à conta que as cria. Como são guardados nas definições do portal Azure de uma conta, podem ser criados, usados e eliminados apenas a partir do interior do portal Azure. Uma consulta privada não é um recurso do Gestor de Recursos. Para criar uma nova consulta privada, siga estes passos:

1. A partir do menu do portal, selecione **Todos os serviços** ou utilize a caixa de pesquisa Azure no topo de todas as páginas. Procure e, em seguida, selecione **Resource Graph Explorer**.

1. No **separador Consulta 1** na página Azure Resource Graph Explorer, insira a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   **Selecione Consultar** a consulta para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre esta consulta, consulte [Samples – Count máquinas virtuais por tipo OS.](../samples/starter.md#count-os)

1. **Selecione Guardar** ou **Guardar como**, **insira os VMs do Conde por OS** como o nome, deixe o tipo como consulta **Privada**, e, em seguida, selecione **Guardar** na parte inferior do painel de **consulta Save.** O título do separador muda de **Consulta 1** para **VMs de Contagem por OS**.

1. Afaste-se do Azure Resource Graph Explorer no portal Azure e, em seguida, volte para ele. Note que a consulta guardada já não é apresentada e que o **separador Consulta 1** regressou.

1. Selecione **Abrir uma consulta**. Certifique-se de que o tipo é **consulta privada.** O nome guardado **Conde VMs por OS** aparece agora na lista de **Nomes de Consulta.** Quando seleciona a ligação de título da consulta guardada, é carregada num novo separador com o nome dessa consulta.

   > [!NOTE] 
   > Quando uma consulta guardada estiver aberta e o separador mostrar o seu nome, selecionar o botão **Guardar** atualiza-o com quaisquer alterações que tenham sido efetuadas. Para criar uma nova consulta guardada a partir desta consulta aberta, **selecione Save as** e proceda como se estivesse a guardar uma nova consulta.

1. Para eliminar a consulta guardada, selecione **Abra novamente uma consulta** e verifique se o campo **Tipo** está definido para **consulta Privada**. Na linha da `Count VMs by OS` consulta guardada, **selecione Delete** (Ícone do caixote do lixo de reciclagem). Na caixa de diálogo de confirmação, selecione **Sim** para terminar a eliminação da consulta.
   Em seguida, feche o **painel de consulta.**

## <a name="create-a-shared-query"></a>Criar uma consulta partilhada

Ao contrário de uma consulta privada, uma consulta partilhada é um recurso de Gestor de Recursos. Este facto significa que a consulta é guardada para um grupo de recursos, pode ser gerida e controlada com o Azure RBAC, e pode até ser protegida com bloqueios de recursos. Como recurso, qualquer pessoa que tenha as permissões apropriadas pode vê-lo e usá-lo.
Para criar uma nova consulta partilhada, siga estes passos:

1. A partir do menu do portal, selecione **Todos os serviços**ou utilize a caixa de pesquisa Azure no topo de todas as páginas para procurar e selecionar **o Explorador de Gráficos de Recursos**.

1. No **separador Consulta 1** na página Azure Resource Graph Explorer, insira a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   **Selecione Consultar** a consulta para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre esta consulta, consulte [Samples – Count máquinas virtuais por tipo OS.](../samples/starter.md#count-os)

1. **Selecione Guardar** ou **Guardar como**.

   :::image type="content" source="../media/create-share-query/save-shared-query-buttons.png" alt-text="Guarde a nova consulta utilizando o botão de guardar" border="false":::

1. No **painel de consulta Save,** **insira o Conde VMs por OS** para obter o nome.

1. Altere o tipo para **consulta partilhada,** desenrânea a descrição **para Contagem de máquinas virtuais por tipo OS**, e desempenhe a **Subscrição** para especificar onde o recurso de consulta é criado.

1. Deixe a Publicação para a caixa de verificação **de grupo de recursos de recursos de recursos de recursos de recursos** selecionados e a **localização do Grupo de Recursos** definida para **(EUA) West Central US**.

1. **Selecione Guarde** na parte inferior do painel de **consultas Save.** O título do separador muda de **Consulta 1** para **VMs de Contagem por OS**. A primeira vez que o grupo **de recursos-consultas de gráficos** é usado, o save demora mais do que o esperado à medida que o grupo de recursos é criado.
   
   :::image type="content" source="../media/create-share-query/save-shared-query-window.png" alt-text="Guarde a nova consulta utilizando o botão de guardar" border="false":::

   > [!NOTE] 
   > Pode limpar a caixa de verificação **de recursos de recursos-gráficos de recursos** se quiser fornecer o nome de um grupo de recursos existente para guardar a consulta partilhada. A utilização do grupo de recursos nomeado padrão para consultas torna as consultas partilhadas mais fáceis de descobrir. Torna também o objetivo desse grupo de recursos mais evidente. No entanto, poderá optar por selecionar um grupo de recursos existente por razões de segurança baseadas em permissões existentes.

1. Afaste-se do Azure Resource Graph Explorer no portal Azure e, em seguida, volte para ele. Note que a consulta guardada já não é apresentada e que o **separador Consulta 1** regressou.

1. Selecione **Abrir uma consulta**. Verifique se o tipo está definido para **consulta partilhada** e a combinação de **subscrição** e jogo de **grupo de recursos** onde guardou a consulta. O **número de VMs do Conde** Guardado por OS aparece agora na lista **de Nomes de Consulta.** Selecione a ligação de título da consulta guardada para carregá-la em um novo separador com o nome dessa consulta. Como uma consulta partilhada, exibe um ícone no separador ao lado do título, denotando-o como partilhado.

   :::image type="content" source="../media/create-share-query/show-saved-shared-query.png" alt-text="Guarde a nova consulta utilizando o botão de guardar" border="false":::

   > [!NOTE] 
   > Quando uma consulta guardada estiver aberta e o separador mostrar o seu nome, o botão **Guardar** atualiza-o com quaisquer alterações que tenham sido efetuadas. Para criar uma nova consulta guardada, **selecione Save as** e proceda como se estivesse a guardar uma nova consulta.

## <a name="discover-shared-queries"></a>Descubra consultas partilhadas

Como uma consulta partilhada é um recurso do Gestor de Recursos, existem várias formas de encontrar uma:

- A partir do Explorador de Gráficos de Recurso, selecione **Abrir uma consulta** e definir o tipo para consulta **partilhada**.
- A partir da página do portal de consultas de gráfico de recurso.
- Do grupo de recursos em que a consulta partilhada foi salva.
- Através de uma consulta ao Gráfico de Recursos.

### <a name="view-resource-graph-queries"></a>Ver consultas de gráfico de recurso

No portal Azure, a página de consultas de Gráfico de Recurso exibe consultas partilhadas às que a conta de acesso iniciado tem acesso. Esta página permite filtrar pelo nome, subscrição, grupo de recursos e outras propriedades da consulta de Gráfico de Recurso. Também pode marcar, exportar e eliminar consultas de Gráfico de Recursos utilizando esta interface.

A seleção de uma das consultas abre a página de consulta de Gráfico de Recurso. Tal como outros recursos do Gestor de Recursos, esta página oferece uma visão geral interativa, juntamente com o registo de Atividade, controlo de acesso e tags. Também pode aplicar um bloqueio de recursos diretamente a partir desta página.

Chegar à página de consultas de Gráfico de Recurso a partir do menu do portal selecionando **Todos os serviços** ou utilizando a caixa de pesquisa Azure no topo de todas as páginas. Procure e selecione **Resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Recursos de grupos de recursos de lista

A consulta de Gráfico de Recurso é listada juntamente com outros recursos que fazem parte de um grupo de recursos.
A seleção da consulta 'Gráfico de Recursos' abre a página para essa consulta. As opções de menu de elipses e atalhos (desencadeados por cliques à direita) funcionam da mesma forma que na página de consulta de Gráfico de Recurso.

### <a name="query-resource-graph"></a>Gráfico de recurso de consulta

Pode encontrar consultas de Gráfico de Recurso através de uma consulta ao Gráfico de Recursos. Os seguintes limites de consulta de gráfico de recurso por tipo `Microsoft.ResourceGraph/queries` , e, em seguida, usa `project` para listar apenas o nome, o tempo modificado e a própria consulta:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="run-a-shared-query"></a>Executar uma consulta partilhada

Uma consulta partilhada de gráficos de recurso pode ser executada com a `{{shared-query-uri}}` sintaxe (pré-visualização). Para obter mais informações, consulte [a sintaxe de consulta partilhada.](../concepts/query-language.md#shared-query-syntax)

## <a name="delete-a-shared-query"></a>Excluir uma consulta partilhada

Se uma consulta partilhada já não for necessária, elimine-a. Ao eliminar uma consulta partilhada, remove o recurso correspondente ao Gestor de Recursos. Quaisquer dashboards que o gráfico de resultados foi fixado para agora exibir uma mensagem de erro. Quando a mensagem de erro for apresentada, utilize o botão **Remover do painel de instrumentos** para limpar o painel de instrumentos.

Pode eliminar uma consulta partilhada através das seguintes interfaces:
- Página de consultas de gráfico de recurso
- Página de consulta de gráfico de recurso
- Abra uma página **de consulta** no Explorador de Gráficos de Recursos
- Página de grupos de recursos

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar este tutorial, elimine as consultas privadas e partilhadas que criou se já não as quiser.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, criou consultas privadas e partilhadas. Para saber mais sobre a linguagem de gráfico de recurso, continue na página de detalhes da linguagem de consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre o idioma de consulta](../concepts/query-language.md)