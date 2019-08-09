---
title: Compor configurações de DSC na configuração de estado de automação do Azure (DSC) usando recursos de composição
description: Saiba como compor configurações usando recursos de composição na configuração de estado da automação do Azure (DSC)
keywords: DSC do PowerShell, configuração de estado desejado, Azure DSC do PowerShell, recursos de composição
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 08/21/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: e153186a3917be3aa94cb663dec58bc3db46aae9
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68850402"
---
# <a name="composing-dsc-configurations-in-azure-automation-state-configuration-dsc-using-composite-resources"></a>Compor configurações de DSC na configuração de estado de automação do Azure (DSC) usando recursos de composição

Quando um recurso precisa ser gerenciado com mais de uma configuração de DSC (configuração de estado desejado), o melhor caminho é usar [recursos de composição](/powershell/dsc/authoringresourcecomposite). Um recurso de composição é uma configuração aninhada e parametrizada sendo usada como um recurso de DSC em outra configuração. Isso permite a criação de configurações complexas, permitindo que os recursos compostos subjacentes (configurações com parâmetros) sejam gerenciados e compilados individualmente.

A automação do Azure permite a [importação e a compilação de recursos de composição](automation-dsc-compile.md#compiling-configurations-in-azure-automation-that-contain-composite-resources).
Depois que os recursos de composição forem importados para sua conta de automação, você poderá usar a experiência de **configuração de composição** na página de **configuração de estado (DSC)** .

## <a name="composing-a-configuration-from-composite-resources"></a>Compor uma configuração de recursos de composição

Antes de atribuir uma configuração feita dos recursos de composição no portal do Azure, você deve redigi-la. Isso pode ser feito usando **a configuração de composição** na página de configuração de **estado (DSC)** enquanto estiver nas guias **configurações** ou **configurações compiladas** .

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
1. À esquerda, clique em **todos os recursos** e, em seguida, no nome da sua conta de automação.
1. Na página **conta de automação** , selecione **configuração de estado (DSC)** em **Gerenciamento de configuração**.
1. Na página **configuração de estado (DSC)** , clique na guia **configurações** ou **configurações compiladas** e, em seguida, clique em **compor configuração** no menu na parte superior da página.
1. Na etapa **básico** , forneça o novo nome de configuração (obrigatório) e clique em qualquer lugar na linha de cada recurso composto que você deseja incluir em sua nova configuração e clique em **Avançar** ou clique na etapa **código-fonte** . Para as etapas a seguir, selecionamos os recursos de composição **PSExecutionPolicy** e **RenameAndDomainJoin** .
   ![Captura de tela da etapa básico da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-basics.png)
1. A etapa de **código-fonte** mostra a aparência da configuração composta dos recursos compostos selecionados. Você pode ver a mesclagem de todos os parâmetros e como eles são passados para o recurso de composição. Quando você terminar de revisar o novo código-fonte, clique em **Avançar** ou clique na etapa **parâmetros** .
   ![Captura de tela da etapa do código-fonte da página de configuração do Compose](./media/compose-configurationwithcompositeresources/compose-configuration-sourcecode.png)
1. Na etapa **parâmetros** , o parâmetro que cada recurso de composição tem é exposto para que eles possam ser fornecidos. Se um parâmetro tiver uma descrição, ele será exibido ao lado do campo de parâmetro. Se um campo for um parâmetro de tipo **PSCredential** , a lista suspensa a configurar fornecerá uma lista de objetos de **credencial** na conta de automação atual. Uma opção **+ Adicionar uma credencial** também está disponível. Depois que todos os parâmetros necessários tiverem sido fornecidos, clique em **salvar e compilar**.
   ![Captura de tela da etapa de parâmetros da página de configuração de composição](./media/compose-configurationwithcompositeresources/compose-configuration-parameters.png)

Depois que a nova configuração for salva, ela será enviada para compilação. O status do trabalho de compilação pode ser exibido como qualquer configuração importada. Para obter mais informações, consulte [exibindo um trabalho de compilação](automation-dsc-getting-started.md#viewing-a-compilation-job).

Quando a compilação for concluída com êxito, a nova configuração aparecerá na guia **configurações compiladas** . Quando estiver visível nessa guia, ela poderá ser atribuída a um nó gerenciado usando as etapas em reatribuir [um nó a uma configuração de nó diferente](automation-dsc-getting-started.md#reassigning-a-node-to-a-different-node-configuration).

## <a name="next-steps"></a>Passos Seguintes

- Para começar, consulte [introdução à configuração de estado de automação do Azure](automation-dsc-getting-started.md)
- Para saber como integrar nós, confira [máquinas de integração para gerenciamento pela configuração de estado da automação do Azure](automation-dsc-onboarding.md)
- Para saber mais sobre como compilar configurações DSC para que você possa atribuí-las aos nós de destino, consulte [compilando configurações na configuração de estado da automação do Azure](automation-dsc-compile.md)
- Para referência de cmdlet do PowerShell, consulte cmdlets de [configuração do estado de automação do Azure](/powershell/module/azurerm.automation/#automation)
- Para obter informações sobre preços, consulte [preços de configuração do estado de automação do Azure](https://azure.microsoft.com/pricing/details/automation/)
- Para ver um exemplo de como usar a configuração de estado de automação do Azure em um pipeline de implantação contínua, consulte [implantação contínua usando configuração de estado de automação do Azure e Chocolatey](automation-dsc-cd-chocolatey.md)
