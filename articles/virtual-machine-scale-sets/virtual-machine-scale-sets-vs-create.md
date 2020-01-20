---
title: Implantar conjunto de dimensionamento de máquinas virtuais usando o Visual Studio
description: Implantar conjuntos de dimensionamento de máquinas virtuais usando o Visual Studio e um modelo do Resource Manager
ms.custom: H1Hack27Feb2017
ms.workload: azure-vs
author: mayanknayar
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: manayar
ms.openlocfilehash: 0d0dc3fbb7e48b1f7e6936cfb65473dba882b776
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274224"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um conjunto de dimensionamento de máquinas virtuais com o Visual Studio

Este artigo mostra como implantar um conjunto de dimensionamento de máquinas virtuais do Azure usando uma implantação de grupo de recursos do Visual Studio.

Os [conjuntos de dimensionamento de máquinas virtuais do Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) são um recurso de computação do Azure para implantar e gerenciar uma coleção de máquinas virtuais semelhantes com dimensionamento automático e balanceamento de carga. Você pode provisionar e implantar conjuntos de dimensionamento de máquinas virtuais usando [modelos de Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Azure Resource Manager modelos podem ser implantados usando CLI do Azure, PowerShell, REST e também diretamente do Visual Studio. O Visual Studio fornece um conjunto de modelos de exemplo, que você pode implantar como parte de um projeto de implantação do grupo de recursos do Azure.

As implantações do grupo de recursos do Azure são uma maneira de agrupar e publicar um conjunto de recursos relacionados do Azure em uma única operação de implantação. Para obter mais informações, consulte [criando e implantando grupos de recursos do Azure por meio do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="prerequisites"></a>Pré-requisitos

Para começar a implantar conjuntos de dimensionamento de máquinas virtuais no Visual Studio, você precisa dos seguintes pré-requisitos:

* Visual Studio 2013 ou posterior
* SDK do Azure 2,7, 2,8 ou 2,9

>[!NOTE]
>Este artigo usa o Visual Studio 2019 com o [SDK do Azure 2,8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## Criar um projeto<a name="creating-a-project"></a> 

1. Abra o Visual Studio e selecione **criar um novo projeto**.

1. Em **criar um novo projeto**, escolha **grupo de recursos do Azure** para C# e, em seguida, selecione **Avançar**.

1. Em **configurar seu novo projeto**, insira um nome e selecione **criar**.

    ![Nomeie e crie seu projeto](media/virtual-machine-scale-sets-vs-create/configure-azure-resource-group.png)

1. Na lista de modelos, escolha o modelo conjunto de dimensionamento de **máquinas virtuais do Windows** ou **conjunto de dimensionamento de máquinas virtuais do Linux** . Selecione **OK**.

   ![Selecionar um modelo de máquina virtual](media/virtual-machine-scale-sets-vs-create/select-vm-template.png)

Depois de criar seu projeto, **Gerenciador de soluções** contém um script de implantação do PowerShell, um modelo de Azure Resource Manager e um arquivo de parâmetro para o conjunto de dimensionamento de máquinas virtuais.

## <a name="customize-your-project"></a>Personalizar seu projeto

Agora você pode editar o modelo para personalizá-lo para as necessidades do seu aplicativo. Você pode adicionar propriedades de extensão da máquina virtual ou editar regras de balanceamento de carga. Por padrão, os modelos de conjunto de dimensionamento de máquinas virtuais são configurados para implantar a extensão **AzureDiagnostics** , o que torna mais fácil adicionar regras de dimensionamento automático. Os modelos também implantam um balanceador de carga com um endereço IP público, configurado com regras de NAT de entrada.

O balanceador de carga permite que você se conecte às instâncias de máquina virtual com SSH (Linux) ou RDP (Windows). O intervalo de portas de front-end começa em 50000. Para o Linux, se você tiver o SSH na porta 50000, o balanceamento de carga o roteará para a porta 22 da primeira máquina virtual no conjunto de dimensionamento. Conectar-se à porta 50001 é roteado para a porta 22 da segunda máquina virtual e assim por diante.

 Uma boa maneira de editar seus modelos com o Visual Studio é usar a **estrutura de tópicos JSON**. Você pode organizar os parâmetros, as variáveis e os recursos. Com uma compreensão do esquema, o Visual Studio pode indicar erros em seu modelo antes de implantá-lo.

![Explorador do JSON](media/virtual-machine-scale-sets-vs-create/json-explorer.png)

## <a name="deploy-the-project"></a>Implantar o projeto

Implante o modelo de Azure Resource Manager para criar o recurso de conjunto de dimensionamento de máquinas virtuais:

1. Em **Gerenciador de soluções**, clique com o botão direito do mouse no projeto e escolha **implantar** > **novo**.

    ![Implantar seu projeto](media/virtual-machine-scale-sets-vs-create/deploy-new-project.png)

1. Em **implantar no grupo de recursos**, escolha qual assinatura deve ser usada e selecione um grupo de recursos. Você pode criar um grupo de recursos, se necessário.

1. Em seguida, selecione **Editar parâmetros** para inserir parâmetros que são passados para o modelo.

   ![Inserir assinatura e grupo de recursos](media/virtual-machine-scale-sets-vs-create/deploy-to-resource-group.png)

1. Forneça o nome de usuário e a senha para o sistema operacional. Esses valores são necessários para criar a implantação. Se você não tiver PowerShell Tools for Visual Studio instalado, selecione **salvar senhas** para evitar um prompt de comando do PowerShell oculto ou use o [suporte a Key Vault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/). Selecione **salvar** para continuar.

    ![Editar parâmetros de implantação](media/virtual-machine-scale-sets-vs-create/edit-deployment-parameters.png)

1. Em **implantar no grupo de recursos**, selecione **implantar**. A ação executa o script **Deploy-azureresourcegroup. ps1** . A janela **saída** mostra o progresso da implantação.

   ![A saída mostra os resultados](media/virtual-machine-scale-sets-vs-create/deployment-output.png)

## Explore o conjunto de dimensionamento de máquinas virtuais<a name="exploring-your-virtual-machine-scale-set"></a>

Selecione **exibir** > **Cloud Explorer** para exibir o novo conjunto de dimensionamento de máquinas virtuais. Use **atualizar tudo**, se necessário.

![Cloud Explorer](media/virtual-machine-scale-sets-vs-create/cloud-explorer.png)

O **Cloud Explorer** permite que você gerencie recursos do Azure no Visual Studio ao desenvolver aplicativos. Você também pode exibir o conjunto de dimensionamento de máquinas virtuais na [portal do Azure](https://portal.azure.com) e [Azure Resource Explorer](https://resources.azure.com/).

 O portal fornece a melhor maneira de gerenciar sua infraestrutura do Azure com um navegador da Web. Azure Resource Explorer fornece uma maneira fácil de explorar e depurar recursos do Azure. O Azure Resource Explorer oferece a exibição de instância e também mostra os comandos do PowerShell para os recursos que você está vendo.

## <a name="next-steps"></a>Passos seguintes

Depois de implantar com êxito os conjuntos de dimensionamento de máquinas virtuais por meio do Visual Studio, você pode personalizar ainda mais seu projeto de acordo com os requisitos do aplicativo. Por exemplo, configure o dimensionamento automático adicionando um recurso de **informações** . Você pode adicionar a infraestrutura ao modelo, como máquinas virtuais autônomas, ou implantar aplicativos usando a extensão de script personalizado. Bons modelos de exemplo podem ser encontrados no repositório GitHub de [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) . Procurar `vmss`.
