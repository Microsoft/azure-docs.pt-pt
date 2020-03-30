---
title: Compor configurações de DSC na Configuração do Estado da Automação Azure (DSC) utilizando recursos compósitos
description: Saiba como compor configurações utilizando recursos compósitos na Configuração do Estado da Automação Azure (DSC)
keywords: powershell dsc, configuração de estado desejada, powershell dsc azure, recursos compósitos
services: automation
ms.subservice: dsc
ms.date: 08/21/2018
ms.topic: conceptual
ms.openlocfilehash: e5083ec55ee0a57cd7defd466f5baf1704336320
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77370664"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Compor configurações de DSC na Configuração do Estado da Automação Azure (DSC) utilizando recursos compósitos

Quando um recurso precisa de ser gerido com mais do que uma configuração de estado desejada (DSC), o melhor caminho é utilizar [recursos compósitos](/powershell/scripting/dsc/resources/authoringresourcecomposite). Um recurso composto é uma configuração aninhada e parametrizada sendo usada como um recurso DSC dentro de outra configuração. Isto permite a criação de configurações complexas, permitindo que os recursos compósitos subjacentes (configurações parametrizadas) sejam geridos e construídos individualmente.

A Automação Azure permite a [importação e compilação de recursos compósitos.](automation-dsc-compile.md)
Uma vez que os recursos compósitos tenham sido importados para a sua conta de Automação, você pode usar a experiência de **configuração compor** na página **de Configuração do Estado (DSC).**

## <a name="composing-a-configuration-from-composite-resources"></a>Compor uma configuração a partir de recursos compósitos

Antes de poder atribuir uma configuração feita a partir de recursos compósitos no portal Azure, deve compor-a. Isto pode ser feito utilizando a **configuração compor** na página **de Configuração do Estado (DSC)** enquanto nas **configurações configurações** ou nos separadores **de configurações compiladas.**

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **Todos os recursos** e, em seguida, o nome da sua conta Automation.
1. Na página da **conta Automation,** selecione **a configuração do Estado (DSC)** em Gestão de **Configuração**.
1. Na página de **configuração do Estado (DSC),** clique no separador **Configurações** ou **Configurações Compiladas** e, em seguida, clique na **configuração compor** no menu na parte superior da página.
1. Na etapa **Basics,** forneça o novo nome de configuração (necessário) e clique em qualquer lugar na linha de cada recurso composto que deseja incluir na sua nova configuração, em seguida, clique em **Seguinte** ou clique na etapa de **código Fonte.** Para os seguintes passos, selecionamos **PSExecutionPolicy** e **RenameAndDomainJoin** recursos compósitos.
   ![Screenshot do passo básico da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O passo do **código Fonte** mostra como é a configuração composta dos recursos compósitos selecionados. Pode ver a fusão de todos os parâmetros e como são passados para o recurso composto. Quando terminar de rever o novo código fonte, clique em **Seguinte** ou clique na etapa **parâmetros.**
   ![Screenshot do passo do código fonte da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **parâmetros,** o parâmetro que cada recurso composto tem é exposto para que possam ser fornecidos. Se um parâmetro tiver uma descrição, é apresentado junto ao campo do parâmetro. Se um campo for um parâmetro do tipo **PSCredential,** a queda para configurar fornece uma lista de objetos **credenciais** na conta de Automação corrente. A + Adicionar uma opção **credencial** também está disponível. Uma vez fornecidos todos os parâmetros necessários, clique em **Guardar e compilar**.
   ![Screenshot do passo dos parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Uma vez que a nova configuração é guardada, é submetida para compilação. O estado do trabalho de compilação pode ser visto como qualquer configuração importada. Para mais informações, consulte [visualizar um trabalho](automation-dsc-getting-started.md#viewing-a-compilation-job)de compilação .

Quando a compilação tiver terminado com sucesso, a nova configuração aparece no separador de **configurações compilada.** Uma vez visível neste separador, pode ser atribuído a um nó gerido utilizando os passos em [Reatribuir um nó para uma configuração diferente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration)do nó .

## <a name="next-steps"></a>Passos seguintes

- Para começar, veja Começar com a Configuração do Estado da [Automação Azure](automation-dsc-getting-started.md)
- Para aprender a bordo de nós, consulte [máquinas de embarque para gestão pela Configuração do Estado da Automação Azure](automation-dsc-onboarding.md)
- Para aprender sobre a compilação de configurações de DSC para que possa atribuí-las a nós-alvo, consulte [Configurações de Compilação na Configuração do Estado da Automação Azure](automation-dsc-compile.md)
- Para referência de cmdlet PowerShell, consulte [Os cmdlets](/powershell/module/azurerm.automation/#automation) de configuração do Estado da Automatização Do Azure
- Para obter informações sobre preços, consulte os preços de configuração do Estado da [Automação Do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de utilização da Configuração do Estado da Automação Azure num pipeline de implantação contínua, consulte a implantação contínua utilizando a configuração do Estado da [Automação Azure e](automation-dsc-cd-chocolatey.md) o Chocolatey
