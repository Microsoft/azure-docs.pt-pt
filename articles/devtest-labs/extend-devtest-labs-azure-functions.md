---
title: Estenda os laboratórios Azure DevTest usando funções Azure | Microsoft Docs
description: Aprenda a estender a Azure DevTest Labs usando funções Azure.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 7a51f0935540df18cfb8805902bbe2c4ec365291
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203679"
---
# <a name="use-azure-functions-to-extend-devtest-labs"></a>Utilizar as Funções do Azure para expandir o DevTest Labs
Pode utilizar as Funções Azure para suportar cenários adicionais para além dos que já são suportados pela DevTest Labs. As Funções Azure podem ser usadas para alargar a funcionalidade incorporada do serviço para atender às necessidades específicas do seu negócio. A lista que se segue fornece alguns dos cenários possíveis. Este artigo mostra-lhe como implementar um destes cenários de amostragem.

- Fornecer um resumo de alto nível de máquinas virtuais (VMs) no Laboratório
- [Configurar um laboratório para utilizar um gateway de ambiente de trabalho remoto](configure-lab-remote-desktop-gateway.md)
- Relatório de conformidade na página de suporte interno
- Permitir que os utilizadores completem operações que exijam permissões acrescidas na subscrição
- [Início de fluxos de trabalho com base em eventos da DevTest Labs](https://github.com/RogerBestMsft/DTL-SecureArtifactData)

## <a name="overview"></a>Descrição Geral
[Azure Functions](../azure-functions/functions-overview.md) é uma plataforma de computação sem servidor no Azure. A utilização de Funções Azure numa solução com a DevTest Labs permite-nos aumentar as funcionalidades existentes com o nosso próprio código personalizado. Para obter mais informações sobre as Funções Azure, consulte [a documentação do Azure Functions](../azure-functions/functions-overview.md). Para ilustrar como as Funções Azure podem ajudar a cumprir os seus requisitos ou cenários completos na DevTest Labs, este artigo usa um exemplo de fornecer um resumo de alto nível de VMs no Laboratório da seguinte forma:

**Exemplo de requisito/cenário**: Os utilizadores podem ver detalhes sobre todos os VMs em um laboratório, incluindo o sistema operativo, proprietário, e quaisquer artefactos aplicados.  Além disso, se o artefacto **de Aplicação de Atualizações** do Windows não foi aplicado recentemente, existe uma maneira fácil de aplicá-lo.

Para completar o cenário, utilizará duas funções como descrito no seguinte diagrama:  

![Fluxo global](./media/extend-devtest-labs-azure-functions/flow.png)

O código-fonte destas funções de amostra está localizado no [repositório GitHub da DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions) (estão disponíveis as implementações C# e PowerShell).

- **UpdateInternalSupportPage**: Esta função consulta a DevTest Labs e atualiza a página de suporte interno diretamente com detalhes sobre as máquinas virtuais.
- **ApplyWindowsUpdateArtifact**: Para um VM em laboratório, esta função aplica o artefacto **de atualização** do Windows.

## <a name="how-it-works"></a>Como funciona
Quando os utilizadores selecionam a página **de suporte interno** em DevTest Labs, têm uma página pré-povoada com informações sobre VMs, proprietários de laboratórios e contactos de suporte.  

Quando seleciona o **botão Select aqui para atualizar,** a página chama a primeira função Azure: **UpdateInternalSupportPage**. A função consulta a DevTest Labs para obter informações e, em seguida, reescreve a página **de suporte interno** com as novas informações.

Há uma ação adicional que pode ser tomada, para quaisquer VMs em que os artefactos do Windows Update não foram aplicados recentemente, haverá um botão para aplicar atualizações do Windows para o VM. Quando seleciona o botão **de atualização** * Executar o Windows para um VM, a página chama a segunda Função Azure: **ApplyWindowsUpdateArtifact**. Esta função verifica se a máquina virtual está em funcionamento e, em caso afirmativo, aplica diretamente o artefacto [do Windows Update.](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-install-windows-updates)

## <a name="step-by-step-walkthrough"></a>Passo a passo passo
Esta secção fornece instruções passo a passo para a criação dos Recursos Azure necessários para atualizar a página **de suporte interno.** Este walkthrough fornece um exemplo de extensão de Laboratórios DevTest. Pode usar este padrão para outros cenários.

### <a name="step-1-create-a-service-principal"></a>Passo 1: Criar um diretor de serviço 
O primeiro passo é conseguir um diretor de serviço com permissão para a subscrição que contém o laboratório. O diretor de serviço deve utilizar a autenticação baseada em palavra-passe. Pode ser feito com [Azure CLI,](/cli/azure/create-an-azure-service-principal-azure-cli) [Azure PowerShell,](/powershell/azure/create-azure-service-principal-azureps?view=azps-2.5.0)ou com o [portal Azure](../active-directory/develop/howto-create-service-principal-portal.md). Se já tem um diretor de serviço para usar, pode saltar este passo.

Note a identificação do **pedido,** **chave,** e **identificação do inquilino** para o diretor de serviço. Vai precisar deles mais tarde nesta passagem. 

### <a name="step-2-download-the-sample-and-open-in-visual-studio-2019"></a>Passo 2: Descarregue a amostra e abra no Visual Studio 2019
Faça o download de uma cópia da [amostra C# Azure Functions](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/AzureFunctions/CSharp) localmente (quer clonando o repositório quer descarregando o repositório a partir [daqui).](https://github.com/Azure/azure-devtestlab/archive/master.zip)  

1. Abra a solução de amostra com o Visual Studio 2019.  
1. Instale a carga de trabalho de **desenvolvimento do Azure** para o Visual Studio se ainda não a tiver instalada. Pode ser instalado através de **Ferramentas**  ->  **Obter Ferramentas e Features** item de menu).

    ![Carga de trabalho de desenvolvimento azul](./media/extend-devtest-labs-azure-functions/azure-development-workload-vs.png)
1. Compilar a solução. Selecione **Construir** e, em seguida, Construir Artigo de menu **solução.**

### <a name="step-3-deploy-the-sample-to-azure"></a>Passo 3: Desloque a amostra para Azure
No Estúdio Visual, na janela **Solution Explorer,** clique com o botão direito no projeto **AzureFunctions** e, em seguida, selecione **Publicar**. Siga o assistente para concluir a publicação para uma aplicação de função Azure nova ou existente. Para obter informações detalhadas sobre o desenvolvimento e implementação de funções Azure utilizando o Visual Studio, consulte [Develop Azure Functions usando o Visual Studio](../azure-functions/functions-develop-vs.md).

![Publicar diálogo](./media/extend-devtest-labs-azure-functions/publish-dialog.png)


### <a name="step-4--gather-application-settings"></a>Passo 4: Recolher definições de aplicação
Uma vez publicadas as funções, é necessário obter URLs para estas funções a partir do portal Azure. 

1. Navegue até ao [portal Azure.](https://portal.azure.com) 
1. Encontre o aplicativo de função.
1. Na página **'Aplicações de função',** selecione a função. 
1. **Selecione Obter URL de função** como mostrado na imagem seguinte. 

    ![Azure funções URLs](./media/extend-devtest-labs-azure-functions/function-url.png)
4. Copie e guarde a URL. Repita estes passos para a outra função Azure. 

Você também precisará de informações adicionais sobre o principal do serviço, como ID de aplicação, chave, e ID do inquilino.


### <a name="step-5--update-application-settings"></a>Passo 5: Atualizar definições de aplicação
No Estúdio Visual, depois de publicar a Função Azure, selecione as Definições de Serviço de **Aplicações Editar Azure** em **Ações**. Atualizar as seguintes definições de aplicação (remota):

- AzureFunctionUrl_ApplyUpdates
- AzureFunctionUrl_UpdateSupportPage
- WindowsUpdateAllowedDays (padrão a 7)
- ServicePrincipal_AppId
- ServicePrincipal_Key
- ServicePrincipal_Tenant

    ![Definições da aplicação](./media/extend-devtest-labs-azure-functions/application-settings.png)

### <a name="step-6-test-the-azure-function"></a>Passo 6: Testar a função Azure
O último passo nesta passagem é testar a função Azure.  

1. Navegue para a função **UpdateInternalSupportPage** na aplicação de função criada no passo 3. 
1. Selecione **Teste** no lado direito da página. 
1. Introduza as propriedades da rota (LABNAME, RESOURCEGROUPNAME e SUBSCRIÇID).
1. Selecione **Executar** para executar a função.  

    Esta função atualizará a página de suporte interno do laboratório especificado. Também inclui um botão para os utilizadores ligarem diretamente para a função da próxima vez

    ![Função de teste](./media/extend-devtest-labs-azure-functions/test-function.png)

## <a name="next-steps"></a>Passos seguintes
As Funções Azure podem ajudar a alargar a funcionalidade da DevTest Labs para além do que já está incorporado e ajudar os clientes a cumprir os seus requisitos únicos para as suas equipas. Este padrão pode ser estendido & expandido ainda mais para cobrir ainda mais.  Para saber mais sobre a DevTest Labs, consulte os seguintes artigos: 

- [Arquitetura de referência empresarial de DevTest Labs](devtest-lab-reference-architecture.md)
- [Perguntas Mais Frequentes](devtest-lab-faq.md)
- [Escalonando laboratórios DevTest](devtest-lab-guidance-scale.md)
- [Automatização de Laboratórios DevTest com PowerShell](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Modules/Library/Tests)








