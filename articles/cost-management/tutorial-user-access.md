---
title: Tutorial – Atribuir acesso através da Cloudyn no Azure | Microsoft Docs
description: Neste tutorial, ficará a saber como atribuir acesso a dados da Cloudyn com contas de utilizador que definem os níveis de acesso para as entidades.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/18/2019
ms.topic: tutorial
ms.service: cost-management
ms.custom: seodec18
manager: benshy
ms.openlocfilehash: e9bd7fb147f9789120776bc603768b4ace59e693
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58181083"
---
# <a name="tutorial-assign-access-to-cloudyn-data"></a>Tutorial: Atribuir acesso a dados do Cloudyn

O acesso a dados da Cloudyn é fornecido pela gestão de utilizadores ou de entidades. As contas de utilizador do Cloudyn determinam o acesso às *entidades* e às funções administrativas. Existem dois tipos de acesso: administrador e utilizador. A menos que seja modificado pelo utilizador, o acesso de administrador permite a um utilizador utilizar sem restrições todas as funções no portal do Cloudyn, incluindo: gestão de utilizadores, gestão de listas de destinatários e acesso da entidade de raiz a todos os dados de entidade. O acesso de utilizador destina-se aos utilizadores finais para verem e criarem relatórios através do acesso que têm aos dados de entidade.

As entidades são utilizadas para refletir a estrutura hierárquica da sua organização empresarial. Identificam departamentos, divisões e equipas na sua organização no Cloudyn. A hierarquia de entidades ajuda a monitorizar eficazmente os gastos feitos pelas entidades.

Quando registou o seu contrato ou conta do Azure, foi criada uma conta com permissões de administrador no Cloudyn, pelo que pode executar todos os passos neste tutorial. Este tutorial abrange o acesso a dados da Cloudyn, incluindo gestão de utilizadores e de entidades. Saiba como:

> [!div class="checklist"]
> * Criar um utilizador com acesso de administrador
> * Criar um utilizador com acesso de utilizador
> * Eliminar um utilizador
> * Eliminar ou exportar dados pessoais
> * Criar e gerir entidades


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter uma conta do Azure.
- Tem de ter um registo de avaliação ou uma subscrição paga da Cloudyn.

## <a name="create-a-user-with-admin-access"></a>Criar um utilizador com acesso de administrador

Embora já tenha acesso de administrador, os seus colegas na organização podem também precisar de ter acesso de administrador. No portal do Cloudyn, clique no símbolo de engrenagem na parte superior direita e selecione **Gestão de Utilizadores**. Clique em **Adicionar Novo Utilizador** para adicionar um novo utilizador.

Introduza as informações necessárias sobre o utilizador. O **ID de início de sessão** tem de ser um endereço de e-mail válido. Escolha as permissões para Permitir Gestão de Utilizadores, para que o utilizador possa criar e modificar outros utilizadores. Gestão de Listas de Destinatários para permitir ao utilizador editar listas de destinatários. É enviada uma ligação do Cloudyn com informações de início de sessão ao utilizador por e-mail quando seleciona **Notificar utilizador por e-mail**. No primeiro início de sessão, o utilizador define uma palavra-passe.

Em **O utilizador tem acesso de administrador**, está selecionada a entidade de raiz da sua organização. Mantenha a raiz selecionada e, em seguida, guarde as informações de utilizador. A seleção da entidade de raiz permite ao utilizador ter permissão de administrador não só para a entidade de raiz na árvore, mas também para todas as entidades que residem abaixo da mesma.  
  ![Exemplo que mostra o acesso de administrador na caixa de adicionar novo utilizador](./media/tutorial-user-access/new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Criar um utilizador com acesso de utilizador
Os utilizadores típicos que precisem de acesso à Cloudyn, como os dashboards e relatórios, devem ter acesso de utilizador para os visualizar. Crie um novo utilizador com acesso de utilizador semelhante ao que criou com acesso de administrador, com as seguintes diferenças:

- Desmarque **Permitir Gestão de Utilizadores**, **Permitir Gestão de Listas de Destinatários** e desmarque tudo na lista **O utilizador tem acesso de administrador**.
- Selecione as entidades que o utilizador precisa de aceder na lista **O utilizador tem acesso de utilizador**.
- Também pode permitir o acesso do administrador a entidades específicas, conforme necessário.

![Exemplo que mostra o acesso de utilizador na caixa de adicionar novo utilizador](./media/tutorial-user-access/new-user-access.png)

Para ver um tutorial em vídeo sobre como adicionar utilizadores, veja [Adding Users to Cloudyn](https://youtu.be/Nzn7GLahx30) (Adicionar Utilizadores à Cloudyn).

## <a name="delete-a-user"></a>Eliminar um utilizador

Quando elimina um utilizador, quaisquer entidades a que o utilizador tenha acesso permanecem intactas. Os relatórios *pessoais* guardados são removidos quando o utilizador é eliminado. Os relatórios *públicos* guardados criados pelo utilizador não são eliminados.

Não pode remover-se como utilizador.

> [!WARNING]
> Quando elimina um utilizador, não é possível restaurá-lo.

1.  No portal do Cloudyn, clique no símbolo de engrenagem na parte superior direita e, em seguida, selecione **Gestão de Utilizadores**.
2.  Na lista de utilizadores, selecione o utilizador que quer eliminar e, em seguida, clique em **Eliminar Utilizador** (símbolo de caixote do lixo).
3.  Na caixa Eliminar Utilizador, clique em **Sim** e, em seguida, clique em **OK**.


## <a name="delete-or-export-personal-data"></a>Eliminar ou exportar dados pessoais

Se quiser eliminar ou exportar dados pessoais do Cloudyn, tem de criar um pedido de suporte. Quando o pedido de suporte é criado, atua como pedido formal, um Pedido do Requerente de Dados. Em seguida, a Microsoft efetua uma ação de linha de comandos para remover a conta e eliminar quaisquer dados de cliente ou pessoais. Para saber mais sobre como pode solicitar a eliminação ou exportação dos seus dados, veja [Pedidos de Requerentes de Dados do Cloudyn](https://www.cloudyn.com/cloudyn-gdpr-requests).

## <a name="create-and-manage-entities"></a>Criar e gerir entidades

Quando definir a hierarquia de entidades de custo, é recomendado identificar a estrutura da sua organização. As entidades permitem-lhe segmentar gastos por contas ou subscrições individuais. O utilizador cria entidades de custos para criar grupos lógicos para gerir e controlar gastos. À medida que cria a árvore, considere a forma como quer ou precisa de ver os seus custos segregados por unidades de negócio, centros de custos, ambientes e departamentos de vendas. A árvore de entidades no Cloudyn é flexível devido à herança de entidades.

As subscrições individuais para as suas contas da cloud estão associadas a entidades específicas. Pode associar uma entidade a uma subscrição ou conta de fornecedor de serviços em cloud. Assim, as entidades são multi-inquilino. Pode atribuir o acesso de utilizadores específicos apenas ao respetivo segmento da sua empresa através de entidades. Se o fizer, mantém os dados isolados, mesmo em grandes partes de uma empresa, como subsidiárias. Além disso, o isolamento de dados ajuda na governação.  

Quando registou o seu contrato ou conta do Azure no Cloudyn, os dados dos recursos do Azure, incluindo utilização, desempenho, faturação e dados de etiqueta das suas subscrições, foram copiados para a sua conta do Cloudyn. No entanto, tem de criar manualmente a árvore de entidades. Caso tenha ignorado o registo do Azure Resource Manager, apenas os dados de faturação e alguns relatórios de imobilizado estão disponíveis no portal do Cloudyn.

No portal do Cloudyn, clique no símbolo de engrenagem na parte superior direita e selecione **Contas da Cloud**. Começa por uma única entidade (raiz) e cria a árvore de entidades na raiz. Segue-se um exemplo de uma hierarquia de entidades que poderá assemelhar-se a muitas organizações de TI após a conclusão da árvore:

![Exemplo de uma árvore de entidades, mostrado na página de gestão de contas](./media/tutorial-user-access/entity-tree.png)

Junto a **Entidades**, clique em **Adicionar Entidade**. Introduza as informações sobre a pessoa ou o departamento que quer adicionar. Os campos **Nome Completo** e **E-mail** não têm de corresponder ao utilizadores existentes. Se quiser ver uma lista de níveis de acesso, procure *Adicionar uma entidade* na ajuda.

![Exemplo mostrando os níveis de acesso e o nome na caixa de entidade de adicionar a entidade](./media/tutorial-user-access/add-entity.png)

Quando tiver terminado, **guarde** a entidade.

### <a name="entity-access-levels"></a>Níveis de acesso de entidade

Os níveis de acesso de entidade em conjunto com acesso de um utilizador permite-lhe definir o tipo de ações disponíveis no portal do Cloudyn.

- **Enterprise** - fornece a capacidade de criar e gerir entidades de custo subordinado.
- **Enterprise + alocação de custos** - fornece a capacidade de criar e gerir as entidades de custos subordinados, incluindo a alocação de custos para contas consolidadas.
- **Enterprise, custo baseado na alocação de custos principais** - fornece a capacidade de criar e gerir entidades de custos subordinados. Os custos da conta baseiam-se no modelo de alocação de custo principais.
- **Apenas Dashboards Personalizados** - fornece o utilizador para ver apenas os dashboards personalizados predefinidos.
- **Apenas Dashboards** - fornece ao utilizador a capacidade de verem apenas os dashboards.

### <a name="create-a-cost-entity-hierarchy"></a>Criar uma hierarquia de entidades de custo

Para criar uma hierarquia de entidades de custo, deve ter uma conta com acesso enterprise ou enterprise + alocação de custos.

No portal do Cloudyn, clique no símbolo de engrenagem na parte superior direita e selecione **Contas da Cloud**. A árvore **Entidades** é apresentada no painel esquerdo. Se necessário, expanda a árvore de entidade para que possa visualizar a entidade que pretende associar a uma conta.  As contas de fornecedor de serviço de cloud são apresentadas no separadores no painel direito. Selecione um separador e, em seguida, clique e arraste uma conta/subscrição de conta para a entidade, em seguida, largue-a. A caixa **Mover** informa-o que a conta foi movida com êxito. Clique em **OK**.

Também pode associar várias contas a uma entidade. Selecione as contas e, em seguida, clique em **Mover**. Na caixa Mover contas, selecione a entidade em que pretende mover a conta e, em seguida, clique em **Guardar**. A caixa Mover contas pede-lhe para confirmar que pretende mover as contas. Clique em **Sim** e, em seguida, em **OK**.

Para ver um tutorial em vídeo sobre como criar uma hierarquia de entidades de custo, veja [Creating a Cost Entity Hierarchy in Cloudyn](https://youtu.be/dAd9G7u0FmU) (Criar uma Hierarquia de Entidades de Custo na Cloudyn).

Se for um utilizador do Contrato Enterprise do Azure, veja o tutorial em vídeo [Connecting to Azure Resource Manager with Cloudyn](https://youtu.be/oCIwvfBB6kk) (Ligar ao Azure Resource Manager com a Cloudyn) sobre a associação de contas e subscrições a entidades.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar um utilizador com acesso de administrador
> * Criar um utilizador com acesso de utilizador
> * Eliminar um utilizador
> * Eliminar ou exportar dados pessoais
> * Criar e gerir entidades


Se ainda não ativou a acesso à API do Azure Resource Manager para as suas contas, avance para o seguinte artigo.

> [!div class="nextstepaction"]
> [Ativar as subscrições e contas do Azure](activate-subs-accounts.md)
