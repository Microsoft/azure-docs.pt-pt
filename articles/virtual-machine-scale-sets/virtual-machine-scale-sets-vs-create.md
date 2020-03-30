---
title: Implementar conjunto de escala de máquina virtual usando o estúdio visual
description: Implementar conjuntos de escala de máquina virtual usando o estúdio visual e um modelo de gestor de recursos
ms.custom: vs-azure, H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: c49b4f42bc726c68880bdd4d6f58956936e83177
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066967"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um conjunto de escala de máquina virtual com estúdio visual

Este artigo mostra-lhe como implementar um Conjunto de Escala de Máquinas Virtuais Azure utilizando uma implementação do Grupo de Recursos de Estúdio Visual.

[O Azure Virtual Machine Scale Sets](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) é um recurso da Azure Compute para implantar e gerir uma coleção de máquinas virtuais semelhantes com autoescala e equilíbrio de carga. Pode fornecer e implementar conjuntos de escala de máquina virtual utilizando modelos de [gestor de recursos azure](https://github.com/Azure/azure-quickstart-templates). Os modelos do Gestor de Recursos Azure podem ser implementados utilizando o Azure CLI, PowerShell, REST e também diretamente do Estúdio Visual. O Visual Studio fornece um conjunto de modelos de exemplo, que pode implementar como parte de um projeto de implantação do Grupo de Recursos Azure.

As implantações do Grupo De recursos Azure são uma forma de agrupar e publicar um conjunto de recursos Azure relacionados numa única operação de implantação. Para mais informações, consulte Criar e implementar grupos de [recursos Azure através do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a implementar conjuntos de escala de máquina virtual em estúdio visual, precisa dos seguintes pré-requisitos:

* Estúdio Visual 2013 ou mais tarde
* Azure SDK 2.7, 2.8 ou 2.9

>[!NOTE]
>Este artigo utiliza o Visual Studio 2019 com [o Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="create-a-project"></a>Criar um Projeto<a name="creating-a-project"></a> 

1. Open Visual Studio e selecione **Criar um novo projeto.**

1. Em **Criar um novo projeto,** escolha o Grupo de Recursos **Azure** para C# e, em seguida, selecione **Next**.

1. Em **Configurar o seu novo projeto,** insira um nome e selecione **Criar**.

    ![Nomeie e crie o seu projeto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. A partir da lista de modelos, escolha o conjunto de escala de **máquina virtual do Windows** ou o modelo de conjunto de escala de máquina virtual **Linux.** Selecione **OK**.

   ![Selecione um modelo de máquina virtual](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Depois de criar o seu projeto, o **Solution Explorer** contém um script de implementação PowerShell, um modelo de Gestor de Recursos Azure e um ficheiro de parâmetropara o Conjunto de Escala de Máquina virtual.

## <a name="customize-your-project"></a>Personalize o seu projeto

Agora pode editar o modelo para personalizá-lo para as necessidades da sua aplicação. Pode adicionar propriedades de extensão de máquina virtual ou editar regras de equilíbrio de carga. Por predefinição, os modelos de conjunto de escala de máquina virtual estão configurados para implementar a extensão **AzureDiagnostics,** o que facilita a adição de regras de escala automática. Os modelos também implantam um equilibrador de carga com um endereço IP público, configurado com regras nat de entrada.

O equilibrador de carga permite-lhe ligar-se às instâncias da máquina virtual com SSH (Linux) ou RDP (Windows). A gama frontal da porta começa em 50000. Para o Linux, se o SSH chegar à porta 50000, carregue as rotas de equilíbrio para a porta 22 da primeira máquina virtual do Conjunto de Escalas. A ligação à porta 50001 é encaminhada para a porta 22 da segunda máquina virtual, e assim por diante.

 Uma boa maneira de editar os seus modelos com o Visual Studio é usar o **Contorno JSON**. Pode organizar os parâmetros, variáveis e recursos. Com uma compreensão do esquema, o Visual Studio pode apontar erros no seu modelo antes de o implementar.

![Explorador JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Implementar o projeto

Implemente o modelo de Gestor de Recursos Azure para criar o recurso conjunto de escala de máquina virtual:

1. No **Solution Explorer,** clique no projeto e escolha **implementar** > **Novo**.

    ![Implementar o seu projeto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. No **Deploy to Resource Group,** escolha qual subscrição usar e selecione um grupo de recursos. Pode criar um grupo de recursos, se necessário.

1. Em seguida, selecione **Parâmetros de edição** para introduzir parâmetros que são passados para o seu modelo.

   ![Insira o grupo de subscrição e recursos](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Forneça o nome de utilizador e a palavra-passe para o sistema operativo. Estes valores são necessários para criar a implantação. Se não tiver ferramentas PowerShell para estúdio visual instaladas, selecione **Guardar palavras-passe** para evitar um pedido de comando PowerShell oculto ou utilizar o [suporte do Cofre chave](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Selecione **Guardar** para continuar.

    ![Editar parâmetros de implantação](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. No **Deploy to Resource Group,** selecione **Deploy**. A ação executa o script **Deploy-AzureResourceGroup.ps1.** A janela **de saída** mostra o progresso da implantação.

   ![A saída mostra resultados](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## <a name="explore-your-virtual-machine-scale-set"></a>Explore o seu conjunto de escala de máquina virtual<a name="exploring-your-virtual-machine-scale-set"></a>

Selecione **Ver** > **Cloud Explorer** para ver o novo conjunto de escala de máquina virtual. Utilize **a tualização tudo,** se necessário.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

**O Cloud Explorer** permite-lhe gerir os recursos azure no Visual Studio enquanto desenvolve aplicações. Também pode ver o seu conjunto de escala de máquina virtual no [portal Azure](https://portal.azure.com) e [no Azure Resource Explorer.](https://resources.azure.com/)

 O portal fornece a melhor forma de gerir a sua infraestrutura Azure com um navegador web. O Azure Resource Explorer fornece uma maneira fácil de explorar e depurar recursos Azure. O Azure Resource Explorer oferece a visão do exemplo e também mostra comandos PowerShell pelos recursos que está a ver.

## <a name="next-steps"></a>Passos seguintes

Depois de ter implementado com sucesso conjuntos de escala de máquinavirtual através do Estúdio Visual, pode personalizar ainda mais o seu projeto de acordo com os seus requisitos de aplicação. Por exemplo, configure a escala automática adicionando um recurso **Insights.** Pode adicionar infraestruturas ao seu modelo, como máquinas virtuais autónomas, ou implementar aplicações utilizando a extensão personalizada do script. Modelos de exemplo bons podem ser encontrados no repositório [De Modelos De Arranque Rápido Azure](https://github.com/Azure/azure-quickstart-templates) GitHub. Procurar `vmss`.
