---
title: 'Tutorial: Amostra de plantas para novo ambiente'
description: Neste tutorial, você usa uma amostra de planta para criar uma definição de planta que configura dois grupos de recursos e configura uma atribuição de funções para cada um.
ms.date: 03/25/2020
ms.topic: tutorial
ms.openlocfilehash: 79928822b41dfe0583afbeaf2f5f1a4a87dd3202
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80677081"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>Tutorial: Criar um ambiente a partir de uma amostra de plantas

As plantas da amostra fornecem exemplos do que pode ser feito usando plantas azure. Cada um é uma amostra com uma intenção ou propósito específico, mas não cria um ambiente completo por si só. Cada um deles é destinado como um ponto de partida para explorar usando plantas azure com várias combinações de artefactos incluídos, desenhos e parâmetros.

O seguinte tutorial utiliza os **Grupos de Recursos com** amostra de plantas RBAC para mostrar diferentes aspetos do serviço Azure Blueprints. Estão abrangidos os seguintes passos:

> [!div class="checklist"]
> - Criar uma nova definição de planta a partir da amostra
> - Marque a sua cópia da amostra como **Publicado**
> - Atribuir a sua cópia da planta a uma subscrição existente
> - Inspecionar os recursos implantados para a atribuição
> - Desatribua a planta para remover as fechaduras

## <a name="prerequisites"></a>Pré-requisitos

Para completar este tutorial, é necessária uma subscrição Azure. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="create-blueprint-definition-from-sample"></a>Criar definição de planta a partir de amostra

Primeiro, implemente a amostra da planta. Importar cria uma nova planta no seu ambiente com base na amostra.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. A partir da página **iniciar** à esquerda, selecione o botão **Criar** _uma planta_.

1. Encontre os **Grupos de Recursos com** amostra de planta RBAC em _Outras Amostras_ e selecione-os.

1. Introduza os _Fundamentos_ da amostra de plantas:

   - **Nome**da planta : Forneça um nome para a sua cópia da amostra de plantas. Para este tutorial, usaremos o nome _dois-rgs-com-role-assignments_.
   - Localização da **definição**: Utilize a elipse e selecione o grupo de gestão ou subscrição para guardar a sua cópia da amostra para.

1. Selecione o separador _Artefactos_ na parte superior da página ou **Seguinte: Artefactos** na parte inferior da página.

1. Reveja a lista de artefactos que compõem a amostra da planta. Esta amostra define dois grupos de recursos, com nomes de exibição de _ProdRG_ e _PreProdRG_. O nome final e a localização de cada grupo de recursos são definidos durante a atribuição do projeto. O grupo de recursos _ProdRG_ é atribuído ao papel de _Contribuinte_ e o grupo de recursos _PreProdRG_ é atribuído às funções _de Proprietário_ e _Leitores._ As funções atribuídas na definição são estáticas, mas utilizador, app ou grupo que é atribuído a função é definida durante a atribuição do projeto.

1. Selecione **Guardar Rascunho** quando terminar de rever a amostra de plantas.

Este passo cria uma cópia da definição de projeto de amostra no grupo de gestão selecionado ou subscrição. A definição de planta guardada é gerida como qualquer planta criada a partir do zero. Pode guardar a amostra para o seu grupo de gestão ou subscrição quantas vezes for necessário. No entanto, cada cópia deve ser fornecida com um nome único.

Uma vez que a **definição** de planta de poupança tenha sido bem sucedida a notificação do portal, passe para o passo seguinte.

## <a name="publish-the-sample-copy"></a>Publicar a cópia da amostra

A sua cópia da amostra de plantas foi agora criada no seu ambiente. É criado em modo **Draft** e deve ser **publicado** antes de ser atribuído e implantado. A cópia da amostra de plantas pode ser personalizada ao seu ambiente e necessidades. Para este tutorial, não faremos nenhuma alteração.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a definição de planta _de dois rgs-com-role-assignments_ e, em seguida, selecione-a.

1. Selecione **Publicar a planta** no topo da página. No novo painel à direita, forneça **a Versão** como _1.0_ para a sua cópia da amostra de plantas. Esta propriedade é útil para se fizer uma modificação mais tarde. Forneça notas de **alteração** como "Primeira versão publicada dos grupos de recursos com amostra de plantas RBAC." Em seguida, **selecione Publicar** na parte inferior da página.

Este passo permite atribuir a planta a uma subscrição. Uma vez publicadas, ainda podem ser feitas alterações. Alterações adicionais requerem a publicação com um novo valor **versão** para rastrear diferenças entre diferentes versões da mesma definição de blueprint.

Uma vez que a **definição** de projeto de publicação tenha sido bem sucedida a notificação do portal, passe para o passo seguinte.

## <a name="assign-the-sample-copy"></a>Atribuir a cópia da amostra

Uma vez que a cópia da amostra de plantas tenha sido **publicada**com sucesso, pode ser atribuída a uma subscrição dentro do grupo de gestão a que foi guardada. Este passo é onde são fornecidos parâmetros para tornar cada implantação da cópia da amostra de plantas única.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **definições** de Blueprint à esquerda. Utilize os filtros para encontrar a definição de planta _de dois rgs-com-role-assignments_ e, em seguida, selecione-a.

1. Selecione **a planta de atribuição** na parte superior da página de definição de planta.

1. Fornecer os valores do parâmetro para a atribuição do projeto:

   - Noções básicas

     - **Assinaturas**: Selecione uma ou mais das subscrições que estão no grupo de gestão para a qual guardou a sua cópia da amostra de projeto. Se selecionar mais do que uma subscrição, será criada uma atribuição para cada utilização dos parâmetros introduzidos.
     - **Nome de atribuição**: O nome é pré-povoado para si com base no nome da definição de projeto.
     - **Localização**: Selecione uma região para a identidade gerida a criar. O Azure Blueprint utiliza esta identidade gerida para implementar todos os artefactos no esquema atribuído. Para saber mais, consulte [identidades geridas para os recursos do Azure.](../../../active-directory/managed-identities-azure-resources/overview.md)
       Para este tutorial, selecione _East US 2_.
     - Versão de **definição**de planta : Escolha a versão _1.0_ **publicada** da sua cópia da definição de planta da amostra.

   - Atribuição de bloqueio

     Selecione o modo de bloqueio de planta _"Ler Apenas"._ Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

   - Identidade Gerida

     Deixe a opção de predefinição _do Sistema._ Para mais informações, consulte [identidades geridas.](../../../active-directory/managed-identities-azure-resources/overview.md)

   - Parâmetros de artefacto

     Os parâmetros definidos nesta secção aplicam-se ao artefacto sob o qual é definido. Estes parâmetros são [parâmetros dinâmicos](../concepts/parameters.md#dynamic-parameters) uma vez que são definidos durante a atribuição da planta. Para cada artefacto, defina o valor do parâmetro para o que está definido na coluna **Valor.** Para `{Your ID}`selecionar a sua conta de utilizador Azure.

     |Nome do artefacto|Tipo de artefacto|Nome do parâmetro|Valor|Descrição|
     |-|-|-|-|-|
     |Grupo de recursos ProdRG|Grupo de recursos|Nome|ProduçãoRG|Define o nome do primeiro grupo de recursos.|
     |Grupo de recursos ProdRG|Grupo de recursos|Localização|E.U.A.Oeste 2|Define a localização do primeiro grupo de recursos.|
     |Contribuinte|Atribuição de função|Utilizador ou Grupo|{O seu ID}|Define qual utilizador ou grupo conceder a atribuição da função _Contributiva_ dentro do primeiro grupo de recursos.|
     |Grupo de recursos PreProdRG|Grupo de recursos|Nome|Pré-ProduçãoRG|Define o nome do segundo grupo de recursos.|
     |Grupo de recursos PreProdRG|Grupo de recursos|Localização|E.U.A. Oeste|Define a localização do segundo grupo de recursos.|
     |Proprietário|Atribuição de função|Utilizador ou Grupo|{O seu ID}|Define qual utilizador ou grupo conceder a atribuição da função _proprietário_ dentro do segundo grupo de recursos.|
     |Leitores|Atribuição de função|Utilizador ou Grupo|{O seu ID}|Define qual utilizador ou grupo para conceder a atribuição de funções aos _Leitores_ dentro do segundo grupo de recursos.|

1. Uma vez introduzidos todos os parâmetros, **selecione Atribuir** na parte inferior da página.

Este passo implanta os recursos definidos e configura a atribuição de **bloqueio**selecionada . As fechaduras da planta podem demorar até 30 minutos a aplicar.

Uma vez que a **definição** de planta de atribuição tenha sido bem sucedida a notificação do portal, passe para o passo seguinte.

## <a name="inspect-resources-deployed-by-the-assignment"></a>Inspecionar os recursos utilizados pela atribuição

A atribuição da planta cria e rastreia os artefactos definidos na definição de planta. Podemos ver o estado dos recursos a partir da página de atribuição de plantas e olhando diretamente para os recursos.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **plantas atribuída** à esquerda. Utilize os filtros para encontrar a atribuição da blueprint _assignment-two-rgs-com-role-assignments_ e, em seguida, selecione-a.

   A partir desta página, podemos ver a atribuição bem sucedida e a lista de recursos criados juntamente com o seu estado de bloqueio de plantas. Se a atribuição for atualizada, a **operação de atribuição** mostra detalhes sobre a implementação de cada versão de definição. Cada recurso listado que foi criado pode ser clicado e abre essa página de propriedade de recursos.

1. Selecione o grupo de recursos **ProductionRG.**

   Vemos que o nome do grupo de recursos é **ProductionRG** e não o nome de exibição de _artefactoS ProdRG_. Este nome corresponde ao valor definido durante a atribuição do projeto.

1. Selecione a página de controlo de **acesso (IAM)** à esquerda e, em seguida, o separador de **atribuições de funções.**

   Aqui vemos que a sua conta foi concedida a função _de Contribuinte_ no âmbito _deste recurso._ A atribuição de _atribuição-dois-rgs-com-atribuição de tarefas_ tem o papel _de Proprietário,_ uma vez que foi usada para criar o grupo de recursos. Estas permissões também são usadas para gerir recursos com fechaduras de plantas configuradas.

1. A partir do portal Azure breadcrumb, selecione **Assignment-two-rgs-com-role-assignments** para voltar uma página e, em seguida, selecione o grupo de recursos **Pré-ProductionRG.**

1. Selecione a página de controlo de **acesso (IAM)** à esquerda e, em seguida, o separador de **atribuições de funções.**

   Aqui vemos que a sua conta foi concedida tanto as funções de _Proprietário_ como _de Leitor,_ tanto no âmbito _deste recurso._ A atribuição do projeto também tem o papel _de Proprietário_ como o primeiro grupo de recursos.

1. Selecione o separador **de atribuições Negar.**

   A atribuição da planta criou uma [atribuição de negação](../../../role-based-access-control/deny-assignments.md) no grupo de recursos implantados para impor o modo de bloqueio de plantas _Read Only._ A atribuição de negação impede que alguém com direitos adequados sobre o separador de atribuições role atomar ações _específicas._ A atribuição de negação afeta _todos os princípios._

1. Selecione a atribuição de negação e, em seguida, selecione a página **Permissões Negadas** à esquerda.

   A atribuição de negação está **\*** a impedir todas as operações com a configuração e **a configuração Action,** mas permite ler o acesso excluindo/ler ** \*** via **NotActions**.

1. A partir do portal Azure breadcrumb, selecione **PreProductionRG - Control e Acesso (IAM)**. Em seguida, selecione a página **'Visão Geral'** à esquerda e, em seguida, o botão do **grupo de recursos Delete.** Introduza o nome _PreProductionRG_ para confirmar a eliminação e selecione **Apagar** na parte inferior do painel.

   Notificação do portal **Apagar o grupo de recursos PreProductionRG falhou.** O erro indica que, embora a sua conta tenha permissão para eliminar o grupo de recursos, o acesso é negado pela atribuição do projeto. Lembre-se que selecionamos o modo de bloqueio de plantas _Read Only_ durante a atribuição da planta. O bloqueio da planta impede uma conta com permissão, mesmo _proprietário,_ de apagar o recurso. Para obter mais informações, veja [bloqueio de recurso em esquemas](../concepts/resource-locking.md).

Estes passos mostram que os nossos recursos foram criados conforme definido e as fechaduras do projeto impediram a eliminação indesejada, mesmo de uma conta com permissão.

## <a name="unassign-the-blueprint"></a>Desatribua a planta

O último passo é remover a atribuição da planta e os recursos que implementou.
Remover a atribuição não remove os artefactos implantados.

1. Selecione **Todos os serviços** no painel esquerdo. Procure e selecione **Plantas**.

1. Selecione a página de **plantas atribuída** à esquerda. Utilize os filtros para encontrar a atribuição da blueprint _assignment-two-rgs-com-role-assignments_ e, em seguida, selecione-a.

1. Selecione o botão de **planta Unassign** na parte superior da página. Leia o aviso no diálogo de confirmação e, em seguida, selecione **OK**.

   Com a atribuição da planta removida, as fechaduras da planta também são removidas. Os recursos criados podem ser novamente eliminados por uma conta com permissões.

1. Selecione **grupos de recursos** do menu Azure e, em seguida, selecione **ProductionRG**.

1. Selecione a página de controlo de **acesso (IAM)** à esquerda e, em seguida, o separador de **atribuições de funções.**

A segurança de cada grupo de recursos ainda tem as atribuições de funções implementadas, mas a atribuição de plantas já não tem acesso ao _Proprietário._

Assim que a atribuição do **projeto de remoção ter sido bem sucedida,** a notificação do portal aparece, passe para o passo seguinte.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com este tutorial, elimine os seguintes recursos:

- Grupo de recursos _ProductionRG_
- Grupo de recursos _PreProductionRG_
- Definição de projeto _dois-rgs-com-atribuição de papéis_

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, aprendeu a criar uma nova planta a partir de uma definição de amostra. Para saber mais sobre as Plantas Azure, continue o artigo blueprint lifecycle.

> [!div class="nextstepaction"]
> [Conheça o ciclo de vida da planta](../concepts/lifecycle.md)