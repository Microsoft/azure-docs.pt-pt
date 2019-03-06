---
title: Criar um ambiente a partir de um exemplo de esquema
description: Utilize um plano gráfico de exemplo para criar uma definição de esquema que define dois grupos de recursos e configura uma atribuição de função para cada um.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/05/2019
ms.topic: tutorial
ms.service: blueprints
ms.custom: mvc
manager: carmonm
ms.openlocfilehash: 68bd6e3942d2b2e2c8bcf39bfcff10540e564315
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411458"
---
# <a name="create-an-environment-from-a-blueprint-sample"></a>Criar um ambiente a partir de um exemplo de esquema

Planos gráficos de exemplo fornecem exemplos do que pode ser feito usando os esquemas do Azure. Cada um é um exemplo com um objetivo específico ou para fins, mas não cria um ambiente completo por conta própria. Cada concebido como um ponto de partida para explorar, utilizando o esquema do Azure com várias combinações de artefactos incluídos, designs e parâmetros.

O tutorial seguinte utiliza a **grupos de recursos com RBAC** exemplo de plano gráfico para mostrar os diferentes aspectos do serviço de esquemas. São abordados os seguintes passos:

> [!div class="checklist"]
> - Criar uma nova definição de esquema do exemplo
> - Marcar a sua cópia do exemplo de como **publicado**
> - Atribuir a sua cópia do esquema a uma subscrição existente
> - Inspecionar os recursos implementados para a atribuição
> - Anular atribuição do esquema para remover os bloqueios

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessária uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-blueprint-definition-from-sample"></a>Criar definição do esquema de exemplo

Em primeiro lugar, implemente o exemplo de esquema. Importar cria uma novo esquema no seu ambiente com base no exemplo.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Do **introdução** página à esquerda, selecione a **Create** botão sob _criar um plano gráfico_.

1. Encontrar o **grupos de recursos com RBAC** exemplo de esquema sob _outros exemplos_ e selecione **utilizar este exemplo**.

1. Introduza o _Noções básicas_ do exemplo de esquema:

   - **Nome do blueprint**: Forneça um nome para a sua cópia do exemplo de esquema. Para este tutorial, vamos utilizar o nome _rgs de dois com atribuições função_.
   - **Localização da definição**: Utilize o botão de reticências e selecione a subscrição ou grupo de gestão para guardar a sua cópia do exemplo para.

1. Selecione o _artefactos_ separador na parte superior da página ou **seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que constituem o exemplo de esquema. Este exemplo define dois grupos de recursos, com os nomes a apresentar da _ProdRG_ e _PreProdRG_. O nome final e a localização de cada grupo de recursos são definidos durante a atribuição do esquema. O _ProdRG_ grupo de recursos é atribuído a _contribuinte_ função e o _PreProdRG_ grupo de recursos é atribuído o _proprietário_ e  _Os leitores_ funções. As funções atribuídas na definição são estáticas, mas o utilizador, aplicação ou grupo que é atribuído a função é definido durante a atribuição do esquema.

1. Selecione **Guardar rascunho** quando terminar a rever o exemplo de esquema.

Este passo cria uma cópia da definição de esquema de exemplo, no grupo de gestão selecionado ou subscrição. A definição do esquema guardado é gerida como qualquer esquema criada do zero. Pode salvar o exemplo ao seu grupo de gestão ou subscrição, o número de vezes conforme necessário. No entanto, cada cópia tem de ser fornecida um nome exclusivo.

Uma vez a **gravação de definição do esquema foi efetuada com êxito** aparece a notificação do portal, mover para o passo seguinte.

## <a name="publish-the-sample-copy"></a>Publicar a cópia de exemplo

Sua cópia do exemplo de plano gráfico agora foi criada no seu ambiente. Ele é criado na **rascunho** modo e têm de ser **publicado** antes de pode ser atribuída e implantada. A cópia do exemplo de esquema pode ser personalizada para suas necessidades e ambiente. Para este tutorial, iremos não efetue quaisquer alterações.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar os _rgs de dois com atribuições função_ definição de esquema e, em seguida, selecioná-lo.

1. Selecione **publicar esquema** na parte superior da página. No novo painel à direita, fornecer **versão** como _1.0_ para obter uma cópia do exemplo de esquema. Esta propriedade é útil para se tornar uma modificação mais tarde. Fornecer **alterar notas** como "primeira versão publicados a partir de grupos de recursos com o exemplo de plano gráfico RBAC." Em seguida, selecione **publicar** na parte inferior da página.

Este passo torna possível atribuir o esquema para uma subscrição. Depois de publicado, ainda podem ser feitas alterações. Publicação com um novo necessitam de alterações adicionais **versão** valor para controlar as diferenças entre versões diferentes da mesma definição de esquema.

Uma vez a **publicação foi efetuada com êxito de definição de esquema** aparece a notificação do portal, mover para o passo seguinte.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Depois da cópia do exemplo de plano gráfico de ter sido com êxito **publicado**, podem ser atribuído a uma subscrição dentro do grupo de gestão que foram salvos. Este passo é onde os parâmetros são fornecidos para que cada implementação da cópia do exemplo de plano gráfico exclusivo.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **definições de esquema** página à esquerda. Utilize os filtros para encontrar os _rgs de dois com atribuições função_ definição de esquema e, em seguida, selecioná-lo.

1. Selecione **esquema de atribuir** na parte superior da página de definição de esquema.

1. Forneça os valores de parâmetro para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione um ou mais das subscrições que estão no grupo de gestão é guardado sua cópia do exemplo de plano gráfico para. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada um usando os parâmetros introduzidos.
     - **Nome da atribuição**: O nome é preenchido previamente com base no nome da definição de esquema.
     - **Localização**: Selecione uma região para a identidade gerida a ser criado em. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, veja [identidades geridas dos recursos do Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
       Neste tutorial, selecione _E.U.A. Leste 2_.
     - **Versão da definição de esquema**: Escolha o **publicada** versão _1.0_ da sua cópia da definição de esquema de exemplo.

   - Atribuição de Bloqueio

     Selecione o _só de leitura_ modo de bloqueio de esquema. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a predefinição _sistema atribuído_ opção. Para obter mais informações, consulte [geridos identidades](../../../active-directory/managed-identities-azure-resources/overview.md).

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se para o artefacto sob a qual está definido. Esses parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) , uma vez que estão definidos durante a atribuição do esquema. Para cada artefato, defina o valor de parâmetro para o que é definido no **valor** coluna. Para `{Your ID}`, selecione a sua conta de utilizador do Azure.

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Value|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos de ProdRG|Grupo de recursos|Name|ProductionRG|Define o nome do primeiro grupo de recursos.|
     |Grupo de recursos de ProdRG|Grupo de recursos|Localização|EUA Oeste 2|Define a localização do primeiro grupo de recursos.|
     |Contribuinte|Atribuição de função|Utilizador ou grupo|{Sua ID}|Define o que utilizador ou grupo para conceder a _contribuinte_ atribuição de função no primeiro grupo de recursos.|
     |Grupo de recursos de PreProdRG|Grupo de recursos|Name|PreProductionRG|Define o nome do segundo grupo de recursos.|
     |Grupo de recursos de PreProdRG|Grupo de recursos|Localização|EUA Oeste|Define a localização do segundo grupo de recursos.|
     |Proprietário|Atribuição de função|Utilizador ou grupo|{Sua ID}|Define o que utilizador ou grupo para conceder a _proprietário_ atribuição de função dentro do segundo grupo de recursos.|
     |Leitores|Atribuição de função|Utilizador ou grupo|{Sua ID}|Define o que utilizador ou grupo para conceder a _leitores_ atribuição de função dentro do segundo grupo de recursos.|

1. Depois de tem sido introduzidos todos os parâmetros, selecione **atribuir** na parte inferior da página.

Este passo implementa os recursos definidos e configura o selecionado **atribuição de bloqueio**. Bloqueios de plano gráfico podem demorar até 30 minutos a aplicar.

Uma vez a **atribuir da definição do esquema foi efetuada com êxito** aparece a notificação do portal, mover para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos implementados pela atribuição

A atribuição do esquema cria e controla os artefactos definidos na definição de esquema. Podemos ver o estado dos recursos da página de atribuição de esquema e ao examinar os recursos diretamente.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **atribuído a planos gráficos** página à esquerda. Utilize os filtros para encontrar os _Assignment-two-rgs-with-role-assignments_ atribuição de esquema e, em seguida, selecioná-lo.

   Nesta página, podemos ver a atribuição concluída com êxito e a lista de recursos criados, juntamente com o respetivo estado de bloqueio de esquema. Se a atribuição é atualizada, o **operação de atribuição** pendente mostra detalhes sobre a implementação de cada versão de definição. Cada recurso listado que foi criado pode ser clicado e abre-se essa página de propriedades de recursos.

1. Selecione o **ProductionRG** grupo de recursos.

   Vemos que é o nome do grupo de recursos **ProductionRG** e não o nome de exibição de artefacto _ProdRG_. Este nome corresponde ao valor definido durante a atribuição do esquema.

1. Selecione o **controlo de acesso (IAM)** página à esquerda e, em seguida, o **atribuições de funções** separador.

   Aqui, Vemos que foram concedidas à sua conta a _contribuinte_ função no âmbito da _este recurso_. O _Assignment-two-rgs-with-role-assignments_ atribuição do esquema tem a _proprietário_ função como ele foi utilizada para criar o grupo de recursos. Estas permissões também são utilizadas para gerir os recursos com bloqueios de esquema configurado.

1. A trilha de portal do Azure, selecione **Assignment-two-rgs-with-role-assignments** voltar uma página, em seguida, selecione a **PreProductionRG** grupo de recursos.

1. Selecione o **controlo de acesso (IAM)** página à esquerda e, em seguida, o **atribuições de funções** separador.

   Aqui, Vemos que sua conta recebeu ambos os _proprietário_ e _leitor_ funções, ambos no âmbito da _este recurso_. A atribuição do esquema também tem o _proprietário_ função, como o primeiro grupo de recursos.

1. Selecione o **negar atribuições** separador.

   A atribuição do esquema de criar um [negar a atribuição](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implementados para impor a _só de leitura_ modo de bloqueio de esquema. A atribuição de negar impede que uma pessoa com direitos adequados no _atribuições de funções_ separador de realizar ações específicas. A atribuição de negar afeta _todos os principais_.

1. Selecione a atribuição de negar, em seguida, selecione o **negado permissões** página à esquerda.

   A atribuição de negação é impedir todas as operações com o **\*** e **ação** configuração, mas permite o acesso de leitura excluindo  **\* /leitura**via **NotActions**.

1. A trilha de portal do Azure, selecione **PreProductionRG - controlo de acesso (IAM)**. Em seguida, selecione o **descrição geral** página à esquerda e, em seguida, o **eliminar grupo de recursos** botão. Introduza o nome _PreProductionRG_ para confirmar a eliminação e selecione **eliminar** na parte inferior do painel.

   A notificação do portal **eliminar grupo de recursos falha PreProductionRG** é apresentado. O erro indica que, enquanto a sua conta tem permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do esquema. Lembre-se de que selecionamos os _só de leitura_ modo de bloqueio de esquema durante a atribuição do esquema. O bloqueio de esquema impede que uma conta com permissões, até mesmo _proprietário_, de eliminar o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estes passos mostram que nossos recursos foram criados, conforme definido e os bloqueios de esquema impediam eliminação indesejada, mesmo a partir de uma conta com permissões.

## <a name="unassign-the-blueprint"></a>Anular a atribuição do esquema

A última etapa é remover a atribuição do esquema e os recursos que ele implementados.
Remover a atribuição não remove os artefactos implementados.

1. Selecione **todos os serviços** no painel esquerdo. Procure e selecione **esquemas**.

1. Selecione o **atribuído a planos gráficos** página à esquerda. Utilize os filtros para encontrar os _Assignment-two-rgs-with-role-assignments_ atribuição de esquema e, em seguida, selecioná-lo.

1. Selecione o **Unassign esquema** botão na parte superior da página. Leia o aviso na caixa de diálogo de confirmação, em seguida, selecione **OK**.

   Com a atribuição do esquema removida, os bloqueios de plano gráfico também são removidos. Mais uma vez podem ser eliminados os recursos criados por uma conta com permissões.

1. Selecione **grupos de recursos** no menu do Azure, em seguida, selecione **ProductionRG**.

1. Selecione o **controlo de acesso (IAM)** página à esquerda e, em seguida, o **atribuições de funções** separador.

A segurança para cada grupo de recursos tem ainda as atribuições de funções implementadas, mas já não tem a atribuição do esquema _proprietário_ acesso.

Uma vez a **remover atribuição do esquema foi efetuada com êxito** aparece a notificação do portal, mover para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com este tutorial, elimine os seguintes recursos:

- Grupo de recursos _ProductionRG_
- Grupo de recursos _PreProductionRG_
- Definição de esquema _rgs de dois com atribuições função_

## <a name="next-steps"></a>Passos Seguintes

- Saber mais sobre o [ciclo de vida do esquema](../concepts/lifecycle.md)
- Compreender como utilizar [parâmetros estáticos e dinâmicos](../concepts/parameters.md)
- Saber como utilizar o [bloqueio de recursos de esquema](../concepts/resource-locking.md)
- Aprender a personalizar a [ordem de sequenciação do esquema](../concepts/sequencing-order.md)
- Saber como [atualizar as atribuições existentes](../how-to/update-existing-assignments.md)
- Resolver problemas durante a atribuição de um esquema com [resolução de problemas gerais](../troubleshoot/general.md)