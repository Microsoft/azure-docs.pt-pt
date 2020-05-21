---
title: 'Tutorial: Gerir consultas no portal Azure'
description: Neste tutorial, você cria uma Consulta de Gráfico de Recursos e partilha a nova consulta com outros no portal Azure.
ms.date: 05/20/2020
ms.topic: tutorial
ms.openlocfilehash: e63a92a4570a0713ed65e1090e4d77d25fb10f95
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83637755"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>Tutorial: Crie e partilhe uma consulta de gráfico de recursos azure no portal Azure

O Azure Resource Graph Explorer permite-lhe guardar as suas consultas de Gráfico de Recursos diretamente no portal Azure. Existem dois tipos de consultas: _Privada_ e _Partilhada._ Uma consulta privada é guardada nas definições do portal Azure. Enquanto uma consulta partilhada é um recurso do Gestor de Recursos que pode ser gerido com controlos de acesso baseados em funções (RBAC) e protegido com fechaduras de recursos. Ambos os tipos de consultas são encriptados em repouso.

Ao guardar consultas no portal Azure, economiza o tempo que de outra forma poderia passar à procura das suas consultas favoritas ou comumente usadas. Quando partilhas consultas, ajudas a tua equipa a concretizar objetivos de consistência e eficiência através da repetição.

Neste tutorial, completará as seguintes tarefas:

> [!div class="checklist"]
> - Criar e eliminar uma consulta privada
> - Criar uma consulta partilhada
> - Descubra consultas partilhadas
> - Eliminar uma consulta partilhada

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, precisa de uma subscrição do Azure. Se não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-and-delete-a-private-query"></a>Criar e eliminar uma consulta privada

As consultas privadas são acessíveis e visíveis apenas para a conta que as cria. Como são guardados nas definições do portal Azure de uma conta, podem ser criados, utilizados e apagados apenas a partir do interior do portal Azure. Uma consulta privada não é um recurso do Gestor de Recursos. Para criar uma nova consulta privada, siga estes passos:

1. No menu do portal, selecione **Todos os serviços** ou utilize a caixa de pesquisa Azure no topo de todas as páginas. Procure e, em seguida, selecione **Resource Graph Explorer**.

1. No **separador Consulta 1** na página do Explorador de Gráficos de Recursos Azure, introduza a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   Selecione **A consulta** de executar para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre esta consulta, consulte [Amostras – Conte máquinas virtuais por tipo OS](../samples/starter.md#count-virtual-machines-by-os-type).


1. Selecione **Guardar** ou **Guardar as ,** introduza os **VMs de Contagem por OS** como nome, deixe o tipo de consulta **privada**e, em seguida, selecione **Guardar** na parte inferior da placa de **consulta Save.** O título do separador muda de **Consulta 1** para **Contagem VMs por OS**.

1. Afaste-se do Azure Resource Graph Explorer no portal Azure e, em seguida, volte ao mesmo. Note que a consulta guardada já não está exposta e o **separador Consulta 1** voltou.

1. **Selecione Abrir uma consulta**. Certifique-se de que o tipo é **consulta privada.** O nome guardado **Count VMs by OS** aparece agora na lista de **Nomes de Consulta.** Quando seleciona o link de título da consulta guardada, é carregado num novo separador com o nome dessa consulta.

   > [!NOTE] 
   > Quando uma consulta guardada está aberta e o separador mostra o seu nome, selecionando o botão **Guardar** atualiza-o com quaisquer alterações que tenham sido feitas. Para criar uma nova consulta guardada a partir desta consulta aberta, selecione **Save as** e proceda como se estivesse a guardar uma nova consulta.

1. Para eliminar a consulta guardada, **selecione abrir novamente uma consulta** e verificar se o campo **Tipo** está definido para **consulta privada**. Na linha da `Count VMs by OS` consulta guardada, selecione **Eliminar** (ícone do recipiente de reciclagem). Na caixa de diálogo de confirmação, selecione **Sim** para terminar de apagar a consulta.
   Em seguida, feche o **painel de consulta aberto.**

## <a name="create-a-shared-query"></a>Criar uma consulta partilhada

Ao contrário de uma consulta privada, uma consulta partilhada é um recurso do Gestor de Recursos. Este facto significa que a consulta é guardada a um grupo de recursos, pode ser gerida e controlada com RBAC, e pode até ser protegida com fechaduras de recursos. Como recurso, qualquer pessoa que tenha as permissões apropriadas pode vê-lo e usá-lo.
Para criar uma nova consulta partilhada, siga estes passos:

1. No menu do portal, selecione **Todos os serviços,** ou utilize a caixa de pesquisa Azure no topo de todas as páginas para procurar e selecionar o **Resource Graph Explorer**.

1. No **separador Consulta 1** na página do Explorador de Gráficos de Recursos Azure, introduza a seguinte consulta:

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   Selecione **A consulta** de executar para ver os resultados da consulta no painel inferior.

   Para obter mais informações sobre esta consulta, consulte [Amostras – Conte máquinas virtuais por tipo OS](../samples/starter.md#count-virtual-machines-by-os-type).

1. Selecione **Guardar** ou **Guardar como**.

   
   ![Guarde a nova consulta utilizando o botão de salvamento](../media/create-share-query/save-shared-query-buttons.png)

1. Na placa de **consulta Save,** introduza **VMs de Conde por OS** para o nome.

1. Altere o tipo para **consulta partilhada,** detete a descrição para **Contar de máquinas virtuais pelo tipo OS**e desloque a **Subscrição** para especificar onde o recurso de consulta é criado.

1. Deixe a **Publicação para** a caixa de verificação de recursos do grupo de recursos de recursos de recursos de recursos selecionado e a localização do Grupo de **Recursos** definida para **(EUA) West Central US**.

1. Selecione **Guardar** na parte inferior do painel de **consulta Save.** O título do separador muda de **Consulta 1** para **Contagem VMs por OS**. A primeira vez que o grupo de recursos de **consultas de gráfico** de recursos é usado, a poupança demora mais tempo do que o esperado à medida que o grupo de recursos é criado.
   
   ![Guarde a nova consulta como uma consulta partilhada](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > Pode limpar a Caixa de Verificação de Recursos do Grupo de Recursos do **Grupo de** Recursos de Recursos de Recursos de Recursos para obter um recurso de recursos se pretender fornecer o nome de um grupo de recursos existente para guardar a consulta partilhada. A utilização do grupo de recursos nomeado por padrão para consultas torna as consultas partilhadas mais fáceis de descobrir. Também torna o objetivo desse grupo de recursos mais evidente. No entanto, pode optar por selecionar um grupo de recursos existente por razões de segurança baseadas em permissões existentes.

1. Afaste-se do Azure Resource Graph Explorer no portal Azure e, em seguida, volte ao mesmo. Note que a consulta guardada já não está exposta e o **separador Consulta 1** voltou.

1. **Selecione Abrir uma consulta**. Verifique se o tipo está definido para **consulta partilhada** e a combinação de partida de grupo **de Subscrição** e **Recurso** onde guardou a consulta. Os **VMs de Conde guardados por produto OS** aparecem agora na lista de **Nomes de Consulta.** Selecione o link de título da consulta guardada para carregá-lo num novo separador com o nome dessa consulta. Como uma consulta partilhada, exibe um ícone no separador ao lado do título, denotando-o como partilhado.

   ![Mostre a Consulta Partilhada com ícone](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > Quando uma consulta guardada está aberta e o separador mostra o seu nome, o botão **Guardar** atualiza-o com quaisquer alterações que tenham sido feitas. Para criar uma nova consulta guardada, selecione **Save as** e proceda como se estivesse a guardar uma nova consulta.

## <a name="discover-shared-queries"></a>Descubra consultas partilhadas

Como uma consulta partilhada é um recurso do Gestor de Recursos, existem várias maneiras de encontrar uma:

- Do Resource Graph Explorer, **selecione Abra uma consulta** e desembare o tipo para consulta **partilhada**.
- A partir da página do portal de consultas do Gráfico de Recursos.
- Do grupo de recursos em que a consulta partilhada foi guardada.
- Através de uma consulta ao Resource Graph.

### <a name="view-resource-graph-queries"></a>Ver consultas de gráfico de recursos

No portal Azure, a página de consultas do Graph de Recursos exibe consultas partilhadas a que a conta de login tem acesso. Esta página permite filtrar pelo nome, subscrição, grupo de recursos e outras propriedades da consulta do Graph de Recursos. Também pode marcar, exportar e eliminar consultas de Gráfico de Recursos utilizando esta interface.

Selecionando uma das consultas abre a página de consulta do Graph de Recursos. Tal como outros recursos do Gestor de Recursos, esta página oferece uma visão geral interativa juntamente com o registo de Atividades, controlo de acesso e tags. Também pode aplicar um bloqueio de recursos diretamente a partir desta página.

Chegar à página de consultas do Graph de Recursos a partir do menu do portal selecionando **Todos os serviços** ou utilizando a caixa de pesquisa Azure no topo de todas as páginas. Procure e selecione **Resource Graph Explorer**.

### <a name="list-resource-groups-resources"></a>Lista de recursos de grupos

A consulta do Graph de Recursos está listada ao lado de outros recursos que fazem parte de um grupo de recursos.
A seleção da consulta do Graph de Recursos abre a página para essa consulta. As opções de elipse e menu de atalho (desencadeadas por cliques à direita) funcionam da mesma forma que na página de consulta do Graph de Recursos.

### <a name="query-resource-graph"></a>Gráfico de recursos de consulta

Você pode encontrar consultas de Gráfico de Recursos através de uma consulta ao Graph de Recursos. Os seguintes limites de consulta do Gráfico de Recursos por `Microsoft.ResourceGraph/queries` tipo, e depois usam `project` para listar apenas o nome, o tempo modificado e a própria consulta:

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>Eliminar uma consulta partilhada

Se uma consulta partilhada já não for necessária, apague-a. Ao eliminar uma consulta partilhada, remove o recurso correspondente do Gestor de Recursos. Quaisquer painéis que o gráfico de resultados foi fixado para agora exibir uma mensagem de erro. Quando essa mensagem de erro for visualizada, utilize o **Botão Remover do painel de instrumentos** para limpar o painel de instrumentos.

Pode eliminar uma consulta partilhada através das seguintes interfaces:
- Página de consultas de gráfico de recursos
- Página de consulta de gráfico de recursos
- A **página de abrir uma consulta** no Explorador de Gráficos de Recursos
- Página de grupos de recursos

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar este tutorial, elimine as consultas privadas e partilhadas que criou se já não as quiser.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, criou consultas privadas e partilhadas. Para saber mais sobre a linguagem do gráfico de recursos, continue na página de detalhes da linguagem da consulta.

> [!div class="nextstepaction"]
> [Obtenha mais informações sobre a linguagem de consulta](../concepts/query-language.md)