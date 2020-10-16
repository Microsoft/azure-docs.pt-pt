---
title: Implementar conjunto de balança de máquina virtual usando estúdio visual
description: Implementar conjuntos de escala de máquina virtual usando estúdio visual e um modelo de gestor de recursos
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: visual-studio
ms.date: 09/09/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 2db281f1b3278e37deca8486971a7f7a83f85ccf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86503086"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um conjunto de escala de máquina virtual com estúdio visual

Este artigo mostra-lhe como implementar um Conjunto de Escala de Máquina Virtual Azure utilizando uma implementação do Grupo de Recursos do Estúdio Visual.

[Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) é um recurso Azure Compute para implementar e gerir uma coleção de máquinas virtuais semelhantes com autoescala e equilíbrio de carga. Pode providenciar e implementar conjuntos de escala de máquinas virtuais utilizando [modelos de gestor de recursos Azure.](https://github.com/Azure/azure-quickstart-templates) Os modelos Azure Resource Manager podem ser implementados usando Azure CLI, PowerShell, REST e também diretamente do Visual Studio. O Visual Studio fornece um conjunto de modelos de exemplo, que pode implementar como parte de um projeto de implementação do Grupo de Recursos Azure.

As implementações do Azure Resource Group são uma forma de agrupar e publicar um conjunto de recursos Azure relacionados numa única operação de implantação. Para obter mais informações, consulte [criar e implementar grupos de recursos Azure através do Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a implantar conjuntos de escala de máquina virtual no Estúdio Visual, precisa dos seguintes pré-requisitos:

* Estúdio Visual 2013 ou mais tarde
* Azure SDK 2.7, 2.8 ou 2.9

>[!NOTE]
>Este artigo utiliza o Visual Studio 2019 com [a Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Criar um Projeto <a name="creating-a-project"></a> 

1. Abra o Estúdio Visual e selecione **Criar um novo projeto.**

1. In **Create a new project**, escolha O Grupo de Recursos **Azure** para C# e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** insira um nome e selecione **Criar**.

    ![Nomeie e crie o seu projeto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. A partir da lista de modelos, escolha o **conjunto de escala de máquina virtual do Windows** ou o modelo de conjunto de escala de máquina virtual **Linux.** Selecione **OK**.

   ![Selecione um modelo de máquina virtual](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Depois de criar o seu projeto, **o Solution Explorer** contém um script de implementação PowerShell, um modelo de Gestor de Recursos Azure e um ficheiro de parâmetro para o Conjunto de Escala de Máquina Virtual.

## <a name="customize-your-project"></a>Personalize o seu projeto

Agora pode editar o modelo para personalizá-lo para as necessidades da sua aplicação. Pode adicionar propriedades de extensão de máquina virtual ou editar regras de equilíbrio de carga. Por predefinição, os modelos de conjunto de escala de máquina virtual são configurados para implementar a extensão **AzureDiagnostics,** o que facilita a adição de regras de autoescalação. Os modelos também implantam um equilibrador de carga com um endereço IP público, configurado com regras NAT de entrada.

O equilibrador de carga permite ligar-se às instâncias da máquina virtual com SSH (Linux) ou RDP (Windows). A gama de portas front-end começa às 50.000. Para o Linux, se você SSH para a porta 50000, carregue as rotas de equilíbrio para a porta 22 da primeira máquina virtual no Conjunto de Escala. A ligação à porta 50001 é encaminhada para a porta 22 da segunda máquina virtual, e assim por diante.

 Uma boa forma de editar os seus modelos com o Visual Studio é usar o **Contorno JSON.** Pode organizar os parâmetros, variáveis e recursos. Com uma compreensão do esquema, o Visual Studio pode apontar erros no seu modelo antes de o implementar.

![JSON Explorer](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Implementar o projeto

Implemente o modelo do Gestor de Recursos Azure para criar o recurso Conjunto de Escala de Máquina Virtual:

1. No **Solution Explorer,** clique com o botão direito no projeto e escolha **Implementar**  >  **Novo**.

    ![Implemente o seu projeto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. No **Grupo de Recursos,** escolha qual a subscrição a utilizar e selecione um grupo de recursos. Pode criar um grupo de recursos, se necessário.

1. Em seguida, **selecione Editar Parâmetros** para introduzir parâmetros que são passados para o seu modelo.

   ![Insira o grupo de subscrição e recursos](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Forneça o nome de utilizador e a palavra-passe para o sistema operativo. Estes valores são necessários para criar a implantação. Se não tiver ferramentas PowerShell para Estúdio Visual **instaladas, selecione Guarde as palavras-passe** para evitar uma solicitação de comando oculta do PowerShell ou utilize [o suporte do Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). **Selecione Guardar** para continuar.

    ![Editar parâmetros de implantação](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. In **Deploy to Resource Group**, selecione **Deploy**. A ação executa o **guiãoDeploy-AzureResourceGroup.ps1.** A janela **de saída** mostra o progresso da implantação.

   ![A saída mostra resultados](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Explore o seu conjunto de escala de máquina virtual <a name="exploring-your-virtual-machine-scale-set"></a>

Selecione **Ver**  >  **Cloud Explorer** para ver o novo Conjunto de Escala de Máquina Virtual. Utilize **Refresh All**, se necessário.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**O Cloud Explorer** permite-lhe gerir os recursos do Azure no Visual Studio enquanto desenvolve aplicações. Também pode ver o seu Conjunto de Escala de Máquina Virtual no [portal Azure](https://portal.azure.com) e no [Azure Resource Explorer](https://resources.azure.com/).

 O portal fornece a melhor maneira de gerir a sua infraestrutura Azure com um navegador web. O Azure Resource Explorer fornece uma maneira fácil de explorar e depurar recursos Azure. O Azure Resource Explorer oferece a visualização de exemplo e também mostra comandos PowerShell para os recursos que está a ver.

## <a name="next-steps"></a>Passos seguintes

Uma vez implementado com sucesso os Conjuntos de Escala de Máquina Virtual através do Visual Studio, poderá personalizar ainda mais o seu projeto de acordo com os seus requisitos de aplicação. Por exemplo, configurar a autoescala adicionando um recurso **Insights.** Pode adicionar infraestruturas ao seu modelo, como máquinas virtuais autónomas, ou implementar aplicações usando a extensão de script personalizada. Bons modelos de exemplo podem ser encontrados no [repositório GitHub dos Modelos De Quickstart Azure.](https://github.com/Azure/azure-quickstart-templates) Procurar `vmss`.
