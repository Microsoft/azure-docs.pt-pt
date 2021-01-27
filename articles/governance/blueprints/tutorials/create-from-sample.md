---
title: 'Tutorial: Amostra de planta para novo ambiente'
description: Neste tutorial, você usa uma amostra de planta para criar uma definição de planta que configura dois grupos de recursos e configura uma atribuição de papel para cada um.
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/27/2021
ms.locfileid: "98915561"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Tutorial: Criar um ambiente a partir de uma amostra de planta

As plantas da amostra fornecem exemplos do que pode ser feito usando plantas Azure. Cada um é uma amostra com uma intenção ou propósito específico, mas não cria um ambiente completo por si só. Cada um deles é concebido como um ponto de partida para explorar usando plantas Azure com várias combinações de artefactos, desenhos e parâmetros incluídos.

O seguinte tutorial utiliza os **Grupos de Recursos com** amostra de planta RBAC para mostrar diferentes aspetos do serviço Azure Blueprints. São abrangidas as seguintes etapas:

> [!div class="checklist"]
> - Criar uma nova definição de planta a partir da amostra
> - Marcar a cópia do exemplo como **Publicada**
> - Atribuir a cópia do esquema a uma subscrição já existente
> - Inspecione os recursos destacados para a atribuição
> - Desatribua a planta para remover as fechaduras

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, é necessária uma subscrição do Azure. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-blueprint-definition-from-sample"></a>Criar definição de planta a partir de amostra

Primeiro, implementar a amostra de planta. A importação cria uma nova planta no seu ambiente com base na amostra.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Na página **Começar** à esquerda, selecione o botão **Criar** em _Criar um esquema_.

1. Encontre os **Grupos de Recursos com** amostra de planta RBAC em _Outras Amostras_ e selecione-o.

1. Introduza as _Informações Básicas_ do esquema de exemplo:

   - **Nome da planta**: Forneça um nome para a sua cópia da amostra de planta. Para este tutorial, usaremos o nome _de duas tarefas._
   - **Localização de definição**: Utilize a elipse e selecione o grupo de gestão ou a subscrição para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_, na parte superior da página, ou **Seguinte: Artefactos**, na parte inferior.

1. Reveja a lista de artefactos que compõem o esquema de exemplo. Esta amostra define dois grupos de recursos, com nomes de exibição de _ProdRG_ e _PreProdRG._ O nome final e a localização de cada grupo de recursos são definidos durante a atribuição do projeto. O grupo de recursos _ProdRG_ é atribuído ao papel _de Contribuinte_ e o grupo de recursos _PreProdRG_ é atribuído aos papéis _de Proprietário_ e _Leitores._ As funções atribuídas na definição são estáticas, mas o utilizador, app ou grupo que é atribuído a função é definido durante a atribuição do projeto.

1. Quando terminar de rever o esquema de exemplo, selecione **Guardar Rascunho**.

Este passo cria uma cópia da definição de planta de amostra no grupo de gestão ou subscrição selecionados. A definição de planta guardada é gerida como qualquer planta criada a partir do zero. Pode guardar a amostra para o seu grupo de gestão ou subscrição quantas vezes for necessário. No entanto, cada cópia deve ser fornecida com um nome único.

Uma vez que a **definição de projeto de poupança sedisse** a notificação do portal bem sucedida, passe para o passo seguinte.

## <a name="publish-the-sample-copy"></a>Publicar a cópia do exemplo

A cópia do esquema de exemplo está agora criada no seu ambiente. Está criada no modo **Rascunho** e tem de ser **Publicada** antes de poder ser atribuída e implementada. A cópia da amostra de plantas pode ser personalizada ao seu ambiente e às suas necessidades. Para este tutorial, não faremos nenhuma alteração.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para encontrar a definição de planta _de dois rgs-com-role-assignments_ e, em seguida, selecione-os.

1. Selecione **Publicar esquema**, na parte superior da página. No novo painel à direita, forneça a **Versão** como _1.0_ para a sua cópia da amostra de planta. Esta propriedade é útil se fizer modificações mais tarde. Fornecer **notas de alteração** tais como "Primeira versão publicada dos grupos de recursos com amostra de planta RBAC." Em seguida, selecione **Publicar**, na parte inferior da página.

Este passo permite atribuir a planta a uma subscrição. Uma vez publicadas, as alterações ainda podem ser feitas. Alterações adicionais requerem a publicação com um novo valor **de Versão** para rastrear diferenças entre diferentes versões da mesma definição de planta.

Assim que **a definição de projeto de publicação tiver sido publicada,** a notificação do portal foi publicada, passe para o passo seguinte.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia de exemplo

Uma vez publicada com **sucesso** a cópia da amostra de plantas, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. É neste passo que são fornecidos os parâmetros que fazem com que cada implementação da cópia do esquema de exemplo seja única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **Definições de esquema** à esquerda. Utilize os filtros para encontrar a definição de planta _de dois rgs-com-role-assignments_ e, em seguida, selecione-os.

1. Selecione **Atribuir esquema**, na parte superior da página de definição do esquema.

1. Indique os valores dos parâmetros para a atribuição do esquema:

   - Noções básicas

     - **Subscrições**: Selecione uma ou mais das subscrições que estão no grupo de gestão a que guardou a sua cópia da amostra de planta. Se selecionar mais de uma subscrição, é criada uma atribuição para cada uma mediante a utilização dos parâmetros introduzidos.
     - **Nome da atribuição**: O nome é pré-povoado para si com base no nome da definição de planta.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para recursos Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
       Para este tutorial, selecione _East US 2_.
     - **Versão de definição de planta**: Escolha a versão _1.0_ **da** sua cópia da definição de planta de amostra.

   - Atribuição de bloqueio

     Selecione o modo de bloqueio de planta _Apenas de leitura._ Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção _predefinida do Sistema._ Para mais informações, consulte [identidades geridas.](../../../active-directory/managed-identities-azure-resources/overview.md)

   - Parâmetros dos artefactos

     Os parâmetros definidos nesta secção aplicam-se ao artefacto no qual são definidos. Estes parâmetros são [parâmetros dinâmicos,](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para cada artefacto, desa um valor de parâmetro para o que está definido na coluna **Valor.** Para `{Your ID}` , selecione a sua conta de utilizador Azure.

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos ProdRG|Grupo de recursos|Name|ProductionRG|Define o nome do primeiro grupo de recursos.|
     |Grupo de recursos ProdRG|O grupo de recursos|A localização|E.U.A. Oeste 2|Define a localização do primeiro grupo de recursos.|
     |Contribuinte|Atribuição de função|Utilizador ou Grupo|{O seu ID}|Define qual utilizador ou grupo para conceder a atribuição de função _de contribuinte_ dentro do primeiro grupo de recursos.|
     |Grupo de recursos PréProdRG|Grupo de recursos|Name|Pré-Produção|Define o nome do segundo grupo de recursos.|
     |Grupo de recursos PréProdRG|O grupo de recursos|A localização|E.U.A. Oeste|Define a localização do segundo grupo de recursos.|
     |Proprietário|Atribuição de função|Utilizador ou Grupo|{O seu ID}|Define qual utilizador ou grupo para conceder a atribuição de função _do Proprietário_ dentro do segundo grupo de recursos.|
     |Leitores|Atribuição de função|Utilizador ou Grupo|{O seu ID}|Define qual utilizador ou grupo para conceder a atribuição de função aos _Leitores_ dentro do segundo grupo de recursos.|

1. Depois de introduzidos todos os parâmetros, selecione **Atribuir**, na parte inferior da página.

Este passo implementa os recursos definidos e configura a atribuição de **bloqueio** selecionada. As fechaduras da planta podem demorar até 30 minutos a ser aplicadas.

Assim que a **definição de projeto de atribuição tiver sido a** notificação do portal, passe para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecione os recursos utilizados pela atribuição

A atribuição da planta cria e rastreia os artefactos definidos na definição de planta. Podemos ver o estado dos recursos a partir da página de atribuição de projetos e olhando diretamente para os recursos.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **de plantas atribuídas** à esquerda. Utilize os filtros para encontrar a atribuição de projeto _de design de atribuição de duas rgs-com-função_ e, em seguida, selecione-o.

   A partir desta página, podemos ver a atribuição bem sucedida e a lista de recursos criados juntamente com o seu estado de bloqueio de planta. Se a atribuição for atualizada, a **operação** de atribuição mostra detalhes sobre a implementação de cada versão de definição. Cada recurso listado que foi criado pode ser selecionado e abre essa página de propriedade de recursos.

1. Selecione o grupo de recursos **ProductionRG.**

   Vemos que o nome do grupo de recursos é **ProductionRG** e não o nome de exposição _de artefactos ProdRG_. Este nome corresponde ao valor definido durante a atribuição da planta.

1. Selecione a página **de controlo de acesso (IAM)** à esquerda e, em seguida, o **separador atribuições de Função.**

   Aqui vemos que a sua conta foi concedida a função _de Contribuinte_ no âmbito _deste recurso._ A atribuição de projeto _de design de atribuição de dois rgs-com-funções_ tem a função de _Proprietário,_ uma vez que foi usada para criar o grupo de recursos. Estas permissões também são usadas para gerir recursos com fechaduras de planta configuradas.

1. A partir do portal Azure breadcrumb, selecione **Assignment-two-rgs-with-role-assignments** para voltar uma página e, em seguida, selecione o grupo de recursos **Pré-ProduçãoRG.**

1. Selecione a página **de controlo de acesso (IAM)** à esquerda e, em seguida, o **separador atribuições de Função.**

   Aqui vemos que a sua conta foi concedida tanto as funções _de Proprietário_ como _de Leitor,_ tanto no âmbito _deste recurso._ A atribuição do projeto também tem o papel _de Proprietário_ como o primeiro grupo de recursos.

1. Selecione o **separador Descoduções Desemacorar.**

   A atribuição da planta criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantado para impor o modo de bloqueio de planta _Read Only._ A atribuição de negação impede que alguém com direitos adequados no _separador de atribuições_ de Função tome ações específicas. A atribuição de negação afeta _todos os principais._

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está a impedir todas as operações com a configuração **\* *_ e _* Action,** mas permite ler o acesso excluindo **\* /ler** via **NotActions**.

1. A partir do portal Azure breadcrumb, selecione **Pré-ProduçãoRG - Controlo de acesso (IAM)**. Em seguida, selecione a página **'Vista Geral'** à esquerda e, em seguida, o botão **de grupo de recursos Delete.** Introduza o nome _Pré-ProduçãoRG_ para confirmar a eliminação e **selecione Eliminar** na parte inferior do painel.

   É apresentada a notificação do **portal Eliminar o grupo de recursos PreProductionRG.** O erro afirma que, embora a sua conta tenha permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do projeto. Lembre-se que selecionámos o modo de bloqueio de planta _Read Only_ durante a atribuição da planta. O bloqueio da planta impede que uma conta com permissão, mesmo _proprietário,_ a supressão do recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estes passos mostram que os nossos recursos foram criados como definidos e as fechaduras de planta impediram a eliminação indesejada, mesmo a partir de uma conta com permissão.

## <a name="unassign-the-blueprint"></a>Unassign the blueprint

O último passo é remover a atribuição da planta e os recursos que implementou.
Remover a missão não remove os artefactos implantados.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Esquemas**.

1. Selecione a página **de plantas atribuídas** à esquerda. Utilize os filtros para encontrar a atribuição de projeto _de design de atribuição de duas rgs-com-função_ e, em seguida, selecione-o.

1. Selecione o botão **de planta de adign** no topo da página. Leia o aviso no diálogo de confirmação e, em seguida, selecione **OK**.

   Com a atribuição da planta removida, as fechaduras da planta também são removidas. Os recursos criados podem ser novamente eliminados por uma conta com permissões.

1. Selecione **grupos** de recursos do menu Azure e, em seguida, selecione **ProductionRG**.

1. Selecione a página **de controlo de acesso (IAM)** à esquerda e, em seguida, o **separador atribuições de Função.**

A segurança de cada grupo de recursos ainda tem as atribuições de funções implantadas, mas a atribuição de projeto já não tem acesso _ao Proprietário._

Assim que a **atribuição do projeto de remoção tiver sido bem sucedida,** passe para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar com este tutorial, elimine os seguintes recursos:

- Grupo de recursos _ProductionRG_
- Grupo de recursos _Pré-ProduçãoRG_
- Definição de _planta dois rgs-com-função-atribuições_

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu a criar uma nova planta a partir de uma definição de amostra. Para saber mais sobre a Azure Blueprints, continue para o artigo do ciclo de vida do projeto.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](../concepts/lifecycle.md)