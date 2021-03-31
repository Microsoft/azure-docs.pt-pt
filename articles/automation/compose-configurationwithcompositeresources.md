---
title: Compor configurações DSC
description: Este artigo diz como compor configurações usando recursos compósitos na Configuração do Estado da Automação Azure.
keywords: powershell dsc, configuração de estado desejada, powershell dsc azul, recursos compósitos
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 1b1bbb12412deec6ecac8cf1ffd47a00f778862e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98894733"
---
# <a name="compose-dsc-configurations"></a>Compor configurações DSC

Quando é necessário gerir o recurso com mais do que uma única configuração de estado desejada (DSC), o melhor caminho é utilizar [recursos compósitos](/powershell/scripting/dsc/resources/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC dentro de outra configuração. A utilização de recursos compósitos permite criar configurações complexas, permitindo que os recursos compósitos subjacentes sejam geridos e construídos individualmente.

A Azure Automation permite a [importação e compilação de recursos compósitos.](automation-dsc-compile.md) Uma vez importada recursos compósitos para a sua conta Demôm automação, pode utilizar a Configuração do Estado da Automação através da funcionalidade **Configuração Estatal (DSC)** no portal Azure.

## <a name="compose-a-configuration"></a>Compor uma configuração

Antes de poder atribuir uma configuração feita a partir de recursos compósitos no portal Azure, tem de compor a configuração. A composição utiliza **a configuração composição** na página de Configuração do Estado (DSC) enquanto está no **separador Configurações** ou no separador **configurações compiladas.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Automation, selecione **a configuração do Estado (DSC)** em **Gestão de Configuração**.
1. Na página de configuração do Estado (DSC), clique no **separador Configurações** ou no separador **configurações compiladas** e, em seguida, clique na **configuração Compose** no menu no topo da página.
1. Na etapa **Basics,** forneça o novo nome de configuração (necessário) e clique em qualquer lugar na linha de cada recurso composto que pretende incluir na sua nova configuração, em seguida, clique em **Seguinte** ou clique no passo **de código Fonte.** Para os seguintes passos, selecionamos `PSExecutionPolicy` e `RenameAndDomainJoin` compósitamos recursos.
   ![Screenshot do passo básico da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O passo **de código Source** mostra como é a configuração composta dos recursos compósitos selecionados. Pode ver a fusão de todos os parâmetros e como são passados para o recurso composto. Quando terminar de rever o novo código fonte, clique em **Seguinte** ou clique no passo **parâmetros.**
   ![Screenshot do passo de código fonte da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **de Parâmetros,** o parâmetro de cada recurso composto é exposto para que os valores possam ser fornecidos. Se um parâmetro tiver uma descrição, é apresentado ao lado do campo de parâmetros. Se um parâmetro for do `PSCredential` tipo, o dropdown fornece uma lista de objetos **credenciais** na conta de Automação corrente. A + Adicionar uma opção **de credencial** também está disponível. Uma vez fornecidos todos os parâmetros necessários, clique em **Guardar e compilar**.
   ![Screenshot do passo dos parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>Submeta a configuração para compilação

Uma vez guardada a nova configuração, é submetida para compilação. Pode ver o estado do trabalho de compilação como faz com qualquer configuração importada. Para mais informações, consulte [Ver um trabalho de compilação.](automation-dsc-getting-started.md#view-a-compilation-job)

Quando a compilação tiver sido concluída com sucesso, a nova configuração aparece no separador **configurações compiladas.** Em seguida, pode atribuir a configuração a um nó gerido, utilizando os passos na [reatribuição de um nó para uma configuração diferente do nó](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Passos seguintes

- Para aprender a ativar os nós, consulte [Ativar a Configuração do Estado da Automação Azure](automation-dsc-onboarding.md).
- Para saber sobre a compilação de configurações de DSC para que possa atribuí-las aos nós-alvo, consulte [as configurações do Compile DSC na Configuração do Estado da Automação Azure](automation-dsc-compile.md).
- Para ver um exemplo da utilização da Configuração do Estado da Automação Azure num gasoduto de implantação contínua, consulte [Configurar uma implementação contínua com chocolateria](automation-dsc-cd-chocolatey.md).
- Para obter informações sobre preços, consulte [os preços de configuração do Estado da Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Para obter uma referência de cmdlet PowerShell, consulte [Az.Automation](/powershell/module/az.automation).
