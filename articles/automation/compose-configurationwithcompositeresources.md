---
title: Composição de configurações de DSC no Azure Automation Estado Configuration (DSC) usando recursos compostos
description: Saiba como compor configurações com recursos compostos na configuração de estado de automatização do Azure (DSC)
keywords: PowerShell dsc, configuração de estado pretendido, azure dsc de powershell, recursos compostos
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 64588829cec964e52dcb44465869e0090f36f9f1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61303989"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Composição de configurações de DSC no Azure Automation Estado Configuration (DSC) usando recursos compostos

Quando um recurso tem de ser geridos com mais de uma configuração de configuração (DSC) do estado pretendido único, o melhor caminho é usar [recursos compostos](/powershell/dsc/authoringresourcecomposite). Um recurso composto é uma configuração de aninhada e parametrizada a ser utilizada como um recurso de DSC dentro de outra configuração. Isso permite que a criação de configurações complexas, permitindo que os recursos compostos subjacentes (parametrizados configurações) a ser geridos e criados individualmente.

A automatização do Azure permite que o [importação e compilação dos recursos compostos](automation-dsc-compile.md#composite-resources). Depois de recursos compostos tiverem sido importados para a sua conta de automatização, é possível utilizar o **Compose configuration** experiência no **Estado Configuration (DSC)** página.

## <a name="composing-a-configuration-from-composite-resources"></a>Compor uma configuração de recursos compostos

Antes de poder atribuir uma configuração feita a partir de recursos compostos no portal do Azure, tem de compor-lo. Isso pode ser feito utilizando **Compose configuration** no **Estado Configuration (DSC)** página enquanto em qualquer uma o **configurações** ou **Compiled configurações** separadores.

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. No lado esquerdo, clique em **todos os recursos** e, em seguida, o nome da sua conta de automatização.
1. Sobre o **conta de automatização** página, selecione **State configuration (DSC)** sob **o gerenciamento de configuração**.
1. Na **State configuration (DSC)** página, clique no **configurações** ou **compilado configurações** separador, em seguida, clique em **compor configuração**  no menu na parte superior da página.
1. Sobre o **Noções básicas** passo, forneça o novo nome de configuração (obrigatório) e clique em qualquer parte na linha de cada recurso composta que pretende incluir na sua nova configuração, em seguida, clique em **próxima** ou clique no **Código-fonte** passo. Para obter os passos seguintes, selecionamos **PSExecutionPolicy** e **RenameAndDomainJoin** recursos compostos.
   ![Captura de ecrã do passo Noções básicas da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. O **código-fonte** passo mostra a aparência a configuração composta dos recursos compostos selecionados. Pode ver a mesclagem de todos os parâmetros e como eles são transmitidos para o recurso composto. Quando tiver concluído a rever o novo código de origem, clique em **próxima** ou clique no **parâmetros** passo.
   ![Captura de ecrã do passo de código fonte da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Sobre o **parâmetros** passo, o parâmetro que tenha de cada recurso composto é exposto para que eles podem ser fornecidos. Se um parâmetro tem uma descrição, é apresentado junto ao campo de parâmetro. Se um campo é uma **PSCredential** parâmetro de tipo, na lista pendente para configurar fornece uma lista de **credencial** objetos na conta de automatização atual. R **+ adicionar uma credencial** opção é também estará disponível. Depois de todos os parâmetros necessários foram fornecidos, clique em **salvar e compilar**.
   ![Captura de ecrã do passo de parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Depois da nova configuração é guardada, ele é enviado para a compilação. Estado da tarefa de compilação pode ser visualizado como qualquer configuração importados. Para obter mais informações, consulte [visualizar uma tarefa de compilação](automation-dsc-getting-started.md#viewing-a-compilation-job).

Quando a compilação foi concluída com êxito, a nova configuração é apresentado na **compilado configurações** separador. Assim que estiver visível neste separador, pode ser atribuído a um nó gerido utilizando os passos em [reatribuição de um nó para uma configuração de nó diferente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Passos Seguintes

- Para começar a utilizar, consulte o artigo [introdução à configuração de estado de automatização do Azure](automation-dsc-getting-started.md)
- Para saber como a carregar nós, consulte [integrar computadores para gestão de configuração de estado de automatização do Azure](automation-dsc-onboarding.md)
- Para obter informações sobre como compilar configurações de DSC para que pode atribuí-las a nós de destino, consulte [compilar configurações na configuração de estado de automatização do Azure](automation-dsc-compile.md)
- Para referência de cmdlets do PowerShell, consulte [cmdlets de configuração de estado de automatização do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração de estado de automatização do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo do uso de configuração de estado de automatização do Azure num pipeline de implementação contínua, consulte [contínua através do Azure Automation estado de configuração da implementação e Chocolatey](automation-dsc-cd-chocolatey.md)
