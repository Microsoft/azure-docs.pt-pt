---
title: Compor configurações DSC
description: Este artigo diz como compor configurações usando recursos compósitos na Configuração do Estado da Automação Azure.
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure, recursos compósitos
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: 04e32a88dcfb9f069e51b2f18207155b95da37d6
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83744354"
---
# <a name="compose-dsc-configurations"></a>Compor configurações DSC

Quando precisa de gerir o recurso com mais do que uma configuração de estado desejada (DSC), o melhor caminho é utilizar [recursos compósitos](/powershell/scripting/dsc/resources/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC dentro de outra configuração. A utilização de recursos compósitos permite-lhe criar configurações complexas, permitindo que os recursos compósitos subjacentes sejam individualmente geridos e construídos.

A Automação Azure permite a [importação e compilação de recursos compósitos.](automation-dsc-compile.md) Depois de importar recursos compósitos para a sua conta Automation, pode utilizar a Configuração do Estado da Automação Azure através da **Configuração do Estado (funcionalidade DSC** no portal Azure.

## <a name="compose-a-configuration"></a>Componha uma configuração

Antes de poder atribuir uma configuração feita a partir de recursos compósitos no portal Azure, deve compor a configuração. A composição utiliza a **configuração compor** na página de Configuração do Estado (DSC) enquanto na **Configuração** ou no separador de **configurações compiladas.**

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da conta Automation, selecione **a configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de configuração do Estado (DSC), clique nas **Configurações** ou no separador de **configurações compiladas** e, em seguida, clique na **configuração compor** no menu na parte superior da página.
1. Na etapa **Basics,** forneça o novo nome de configuração (necessário) e clique em qualquer lugar na linha de cada recurso composto que deseja incluir na sua nova configuração, em seguida, clique em **Seguinte** ou clique na etapa de **código Fonte.** Para os seguintes passos, selecionamos `PSExecutionPolicy` e `RenameAndDomainJoin` compósitos recursos.
   ![Screenshot do passo básico da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O passo do **código Fonte** mostra como é a configuração composta dos recursos compósitos selecionados. Pode ver a fusão de todos os parâmetros e como são passados para o recurso composto. Quando terminar de rever o novo código fonte, clique em **Seguinte** ou clique na etapa **parâmetros.**
   ![Screenshot do passo do código fonte da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **parâmetros,** o parâmetro para cada recurso composto é exposto para que os valores possam ser fornecidos. Se um parâmetro tiver uma descrição, é apresentado junto ao campo do parâmetro. Se um parâmetro for de `PSCredential` tipo, o dropdown fornece uma lista de objetos **credenciais** na conta de Automação corrente. A + Adicionar uma opção **credencial** também está disponível. Uma vez fornecidos todos os parâmetros necessários, clique em **Guardar e compilar**.
   ![Screenshot do passo dos parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

## <a name="submit-the-configuration-for-compilation"></a>Submeter a configuração para compilação

Uma vez que a nova configuração é guardada, é submetida para compilação. Pode ver o estado do trabalho de compilação, como faz com qualquer configuração importada. Para mais informações, consulte ver um trabalho de [compilação](automation-dsc-getting-started.md#view-a-compilation-job).

Quando a compilação tiver terminado com sucesso, a nova configuração aparece no separador de **configurações compilada.** Em seguida, pode atribuir a configuração a um nó gerido, utilizando os passos para [reatribuir um nó a uma configuração diferente](automation-dsc-getting-started.md#reassign-a-node-to-a-different-node-configuration)do nó .

## <a name="next-steps"></a>Passos seguintes

- [Começar com a Configuração do Estado da Automação Azure](automation-dsc-getting-started.md)
- [Ativar a configuração do Estado da Automação Azure](automation-dsc-onboarding.md)
- [Compilar configurações dSC na configuração do Estado da Automação Azure](automation-dsc-compile.md)
- [Configurar a implantação contínua com chocolatey](automation-dsc-cd-chocolatey.md)
- [Preços de configuração do Estado da Automação Azure](https://azure.microsoft.com/pricing/details/automation/)
