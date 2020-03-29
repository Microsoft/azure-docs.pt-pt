---
title: Alargar os Laboratórios Azure DevTest utilizando funções azure [ Microsoft Docs
description: Aprenda a estender os Laboratórios Azure DevTest utilizando funções Azure.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2019
ms.author: spelluru
ms.openlocfilehash: dd1fc4c1076d89c12b25837db9fa6a0ac3e1f3a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70014363"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Utilizar as Funções do Azure para expandir o DevTest Labs
Pode utilizar funções Azure para suportar cenários adicionais para além dos que já são suportados pela DevTest Labs. As Funções Azure podem ser usadas para alargar a funcionalidade incorporada do serviço para atender às suas necessidades específicas do negócio. A lista que se segue fornece alguns dos cenários possíveis. Este artigo mostra-lhe como implementar um destes cenários de amostra.

- Forneça um resumo de alto nível de máquinas virtuais (VMs) no Laboratório
- [Configurar um laboratório para utilizar um gateway de ambiente de trabalho remoto](configure-lab-remote-desktop-gateway.md)
- Relatório de conformidade na página de suporte interno
- Ativar os utilizadores para completar operações que requerem permissões acrescidas na subscrição
- [Iniciando fluxos de trabalho baseados em eventos de DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Descrição geral
[A Azure Functions](../azure-functions/functions-overview.md) é uma plataforma de computação sem servidores no Azure. A utilização de Funções Azure numa solução com a DevTest Labs permite-nos aumentar as funcionalidades existentes com o nosso próprio código personalizado. Para obter mais informações sobre as Funções Azure, consulte a [documentação das Funções Azure.](../azure-functions/functions-overview.md) Para ilustrar como as Funções Azure podem ajudar a cumprir os seus requisitos ou cenários completos em DevTest Labs, este artigo usa um exemplo de fornecer um resumo de alto nível de VMs no Laboratório da seguinte forma:

**Requisito/cenário**de exemplo : Os utilizadores podem ver detalhes sobre todos os VMs num laboratório, incluindo o sistema operativo, o proprietário e quaisquer artefactos aplicados.  Além disso, se o artefacto **Apply Windows Updates** não foi recentemente aplicado, existe uma maneira fácil de o aplicar.

Para completar o cenário, utilizará duas funções descritas no seguinte diagrama:  

![Fluxo global](./media/extend-devtest-labs-azure-functions/flow.png)

O código fonte para estas funções de amostra está localizado no [repositório DevTest Labs GitHub](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (tanto as implementações de C# como powerShell estão disponíveis).

- **UpdateInternalSupportPage**: Esta função consulta DevTest Labs e atualiza a página de suporte interno diretamente com detalhes sobre as máquinas virtuais.
- **AplicaoWindowsUpdateArtifact**: Para um VM num laboratório, esta função aplica o artefacto da **atualização do Windows.**

## <a name="how-it-works"></a>Como funciona
Quando os utilizadores selecionam a página de **suporte interno** em DevTest Labs, têm uma página pré-povoada com informações sobre VMs, proprietários de laboratórios e contactos de suporte.  

Quando selecionar o **Select aqui para atualizar** o botão, a página chama a primeira função Azure: **UpdateInternalSupportPage**. A função consulta de informação a DevTest Labs e, em seguida, reescreve a página de **suporte interno** com as novas informações.

Há uma ação adicional que pode ser tomada, para quaisquer VMs em que os artefactos do Windows Update não foram aplicados recentemente, haverá um botão para aplicar atualizações do windows para o VM. Quando selecionar o botão de**atualização** do Windows * Executar para um VM, a página chama a segunda Função Azure: **ApplyWindowsUpdateArtifact**. Esta função verifica se a máquina virtual está em funcionamento e, em caso afirmativo, aplica o artefacto [do Windows Update](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates) diretamente.

## <a name="step-by-step-walkthrough"></a>Passo a passo
Esta secção fornece instruções passo a passo para a criação de Recursos Azure necessários para atualizar a página de **suporte interno.** Este walkthrough fornece um exemplo de extensão de DevTest Labs. Pode usar este padrão para outros cenários.

### <a name="step-1-create-a-service-principal"></a>Passo 1: Criar um diretor de serviço 
O primeiro passo é conseguir um diretor de serviço com permissão para a subscrição que contém o laboratório. O diretor de serviço deve utilizar a autenticação baseada em palavra-passe. Pode ser feito com [o Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest) [Azure PowerShell,](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)ou com o [portal Azure.](../active-directory/develop/howto-create-service-principal-portal.md) Se já tem um diretor de serviço para usar, pode saltar este passo.

Anote o ID da **aplicação,** **a chave**e o ID do **inquilino** para o diretor de serviço. Vai precisar deles mais tarde nesta passagem. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Passo 2: Descarregue a amostra e abra no Visual Studio 2019
Descarregue uma cópia da [amostra C# Azure Functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) localmente (quer clonando o repositório ou baixando o repositório a partir [daqui).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Abra a solução de amostra com o Visual Studio 2019.  
1. Instale a carga de trabalho de **desenvolvimento do Azure** para o Estúdio Visual se ainda não a tiver instalada. Pode ser instalado através do menu **Tools** -> **Get Tools and Features).**

    ![Carga de trabalho de desenvolvimento azure](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compilar a solução. Selecione **construir** e, em seguida, construir o item do menu **Build.**

### <a name="step-3-deploy-the-sample-to-azure"></a>Passo 3: Desdobrar a amostra para O Azure
No Estúdio Visual, na janela Do Explorador de **Soluções,** clique no projeto **AzureFunctions** e, em seguida, selecione **Publicar**. Siga o assistente para completar a publicação para uma nova ou uma aplicação de função Azure existente. Para obter informações detalhadas sobre o desenvolvimento e implementação de funções Azure utilizando o Visual Studio, consulte [Funções De Desenvolvimento Azure utilizando o Visual Studio](../azure-functions/functions-develop-vs.md).

![Publicar diálogo](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Passo 4: Recolher as definições de aplicação
Uma vez publicadas as funções, é necessário obter URLs para estas funções a partir do portal Azure. 

1. Navegue para o [portal Azure.](https://portal.azure.com) 
1. Encontre a aplicação de funções.
1. Na página **'Apps' função,** selecione a função. 
1. Selecione Obter URL de **função** como mostrado na imagem seguinte. 

    ![Funções Azure URLs](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copie e guarde o URL. Repita estes passos para a outra função Azure. 

Você também precisará de informações adicionais sobre o diretor de serviço, tais como ID de aplicação, chave e ID do inquilino.


### <a name="step-5--update-application-settings"></a>Passo 5: Definições de aplicação de atualização
No Estúdio Visual, depois de publicar a Função Azure, selecione as definições de serviço de **aplicação Edit Azure** em **ações**. Atualizar as seguintes definições de aplicação (remota):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (padrão para 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Definições da aplicação](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Passo 6: Testar a função Azure
O último passo nesta passagem é testar a função Azure.  

1. Navegue para a função **UpdateInternalSupportPage** na aplicação de função criada no passo 3. 
1. Selecione **Teste** no lado direito da página. 
1. Insira nas propriedades da rota (LABNAME, RESOURCEGROUPNAME e SUBSCRIPTIONID).
1. Selecione **Executar** para executar a função.  

    Esta função atualizará a página de suporte interno do laboratório especificado. Também inclui um botão para os utilizadores ligarem diretamente para a função da próxima vez

    ![Função de teste](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Passos seguintes
As Funções Azure podem ajudar a alargar a funcionalidade da DevTest Labs para além do que já está incorporado e ajudar os clientes a cumprir os seus requisitos únicos para as suas equipas. Este padrão pode ser alargado & expandido ainda mais para cobrir ainda mais.  Para saber mais sobre devTest Labs, consulte os seguintes artigos: 

- [DevTest Labs Enterprise Reference Architecture](devtest-lab-reference-architecture.md)
- [Perguntas frequentes](devtest-lab-faq.md)
- [Escalonando os Laboratórios DevTest](devtest-lab-guidance-scale.md)
- [Automatizar laboratórios DevTest com PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








