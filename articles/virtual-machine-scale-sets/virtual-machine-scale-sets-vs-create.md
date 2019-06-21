---
title: Implementar o conjunto de dimensionamento de Máquina Virtual com o Visual Studio | Documentos da Microsoft
description: Implementar conjuntos de dimensionamento de Máquina Virtual com o Visual Studio e um modelo do Resource Manager
services: virtual-machine-scale-sets
ms.custom: H1Hack27Feb2017
ms.workload: na
documentationcenter: ''
author: mayanknayar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ed0786b8-34b2-49a8-85b5-2a628128ead6
ms.service: virtual-machine-scale-sets
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.author: manayar
ms.openlocfilehash: 3d472aeaae7e7f02eba58aadea1df042d6c0f27b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204724"
---
# <a name="how-to-create-a-virtual-machine-scale-set-with-visual-studio"></a>Como criar um conjunto de dimensionamento de Máquina Virtual com o Visual Studio
Este artigo mostra-lhe como implementar uma Azure Virtual Machine conjunto de dimensionamento com uma implementação de grupo de recursos do Studio Visual.

[Conjuntos de dimensionamento de máquinas virtuais do Azure](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview/) é um recurso de computação do Azure para implementar e gerir uma coleção de máquinas virtuais semelhantes com dimensionamento automático e balanceamento de carga. Pode aprovisionar e implementar conjuntos de dimensionamento de Máquina Virtual usando [modelos do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates). Modelos Azure Resource Manager podem ser implementados com a CLI do Azure, PowerShell, REST e também diretamente a partir do Visual Studio. O Visual Studio fornece um conjunto de modelos de exemplo, o que pode ser implementado como parte de um projeto de implementação de grupo de recursos do Azure.

As implementações de grupo de recursos do Azure são uma forma de agrupar e publicar um conjunto de recursos relacionados do Azure numa operação de implementação única. Pode saber mais sobre as mesmas aqui: [Criar e implementar grupos de recursos do Azure através do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## <a name="pre-requisites"></a>Pré-requisitos
Para começar a implementar conjuntos de dimensionamento de Máquina Virtual no Visual Studio, precisa do seguinte:

* Visual Studio 2013 ou posterior
* Azure SDK 2.7, 2.8 ou 2.9

>[!NOTE]
>Estas instruções partem do princípio de que está a utilizar o Visual Studio com [do Azure SDK 2.8](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

## <a name="creating-a-project"></a>Criar um projeto
1. Criar um novo projeto no Visual Studio escolhendo **arquivo | Novo | Projeto**.
   
    ![Ficheiro novo][file_new]

2. Em **em Visual C# | Cloud**, escolha **do Azure Resource Manager** para criar um projeto para implementar um modelo do Azure Resource Manager.
   
    ![Criar projeto][create_project]

3. Na lista de modelos, selecione o Linux ou o modelo de conjunto de dimensionamento Windows Máquina Virtual.
   
   ![Selecione o modelo][select_Template]

4. Depois de criado o projeto verá scripts de implementação do PowerShell, um modelo do Azure Resource Manager e um ficheiro de parâmetros para o conjunto de dimensionamento de Máquina Virtual.
   
    ![Explorador de Soluções][solution_explorer]

## <a name="customize-your-project"></a>Personalizar o seu projeto
Agora pode editar o modelo de personalizá-lo para as necessidades da sua aplicação, como propriedades de extensão VM a adicionar ou editar regras de balanceamento de carga. Por predefinição, a modelos de conjunto de dimensionamento de Máquina Virtual são configurados para implementar a extensão de AzureDiagnostics, o que torna mais fácil adicionar regras de dimensionamento automático. Também implementa um balanceador de carga com um endereço IP público, configurado com regras NAT de entrada. 

O Balanceador de carga permite-lhe ligar às instâncias de VM com SSH (Linux) ou RDP (Windows). O intervalo de portas front-end é US 50000. Para linux, isso significa que, se SSH para a porta 50000, é encaminhados para a porta 22 da primeira VM num conjunto de dimensionamento. Ligar à porta 50001 é encaminhado para a porta 22 a segunda VM e assim por diante.

 Uma boa forma de editar os seus modelos com o Visual Studio é usar o contorno de JSON para organizar os parâmetros, variáveis e recursos. Com uma compreensão do esquema Visual Studio pode apontar erros no seu modelo antes de o implementar.

![JSON Explorer][json_explorer]

## <a name="deploy-the-project"></a>Implementar o projeto
1. Implemente o modelo de Gestor de recursos do Azure para criar o recurso de conjunto de dimensionamento de Máquina Virtual. Com o botão direito no nó do projeto e escolha **implementar | Nova implementação**.
   
    ![Implementar Modelo][5deploy_Template]
    
2. Selecione a sua subscrição na caixa de diálogo "Implementar ao grupo de recursos".
   
    ![Implementar Modelo][6deploy_Template]

3. A partir daqui, pode criar um grupo de recursos do Azure para implementar o modelo para.
   
    ![Novo grupo de recursos][new_resource]

4. Em seguida, clique em **Editar parâmetros** para introduzir parâmetros que são transmitidos ao seu modelo. Forneça o nome de utilizador e palavra-passe para o sistema operacional, que é necessária para criar a implementação. Se não tiver o PowerShell Tools for Visual Studio instaladas, é recomendável verificar **guardar palavras-passe** para evitar uma linha de comandos da linha de comandos oculta do PowerShell ou utilize [suporte de keyvault](https://azure.microsoft.com/blog/keyvault-support-for-arm-templates/).
   
    ![Editar parâmetros][edit_parameters]

5. Agora, clique em **Deploy**. O **saída** janela mostra o progresso da implementação. Tenha em atenção que a ação está a executar o **Deploy AzureResourceGroup.ps1** script.
   
   ![Janela de saída][output_window]

## <a name="exploring-your-virtual-machine-scale-set"></a>Explorar o conjunto de dimensionamento de Máquina Virtual
Depois de concluída a implementação, pode ver o novo conjunto de dimensionamento de Máquina Virtual no Visual Studio **Cloud Explorer** (atualizar a lista). Cloud Explorer permite-lhe gerir recursos do Azure no Visual Studio ao mesmo tempo de desenvolvimento de aplicativos. Também pode ver seu conjunto de dimensionamento de máquinas virtuais no [portal do Azure](https://portal.azure.com) e [Explorador de recursos do Azure](https://resources.azure.com/).

![Cloud Explorer][cloud_explorer]

 O portal fornece a melhor forma de gerir visualmente a sua infraestrutura do Azure com um navegador da web, enquanto o Azure Resource Explorer fornece uma forma fácil de explorar e depurar os recursos do Azure, fornecendo uma janela para a vista"instância" e também que mostra comandos do PowerShell para os recursos está a visualizar.

## <a name="next-steps"></a>Passos Seguintes
Depois de ter implantado com êxito os conjuntos de dimensionamento de Máquina Virtual através do Visual Studio, pode personalizar ainda mais o seu projeto para atender às suas necessidades de aplicação. Por exemplo, configurar o dimensionamento automático, adicionando um **Insights** recurso, infraestrutura a adicionar ao seu modelo (como VMs autónomas) ou a implementação de aplicações com a extensão de script personalizado. Modelos de bom exemplo podem ser encontrados na [modelos de início rápido do Azure](https://github.com/Azure/azure-quickstart-templates) repositório do GitHub (procure "vmss").

[file_new]: ./media/virtual-machine-scale-sets-vs-create/1-FileNew.png
[create_project]: ./media/virtual-machine-scale-sets-vs-create/2-CreateProject.png
[select_Template]: ./media/virtual-machine-scale-sets-vs-create/3b-SelectTemplateLin.png
[solution_explorer]: ./media/virtual-machine-scale-sets-vs-create/4-SolutionExplorer.png
[json_explorer]: ./media/virtual-machine-scale-sets-vs-create/10-JsonExplorer.png
[5deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/5-DeployTemplate.png
[6deploy_Template]: ./media/virtual-machine-scale-sets-vs-create/6-DeployTemplate.png
[new_resource]: ./media/virtual-machine-scale-sets-vs-create/7-NewResourceGroup.png
[edit_parameters]: ./media/virtual-machine-scale-sets-vs-create/8-EditParameter.png
[output_window]: ./media/virtual-machine-scale-sets-vs-create/9-Output.png
[cloud_explorer]: ./media/virtual-machine-scale-sets-vs-create/12-CloudExplorer.png
