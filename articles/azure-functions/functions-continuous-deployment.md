---
title: Implementação contínua para Funções do Azure
description: Use os recursos de implantação contínua do serviço de Azure App para publicar suas funções.
author: ggailey777
manager: gwallace
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/25/2019
ms.author: glenga
ms.openlocfilehash: dae75153cffbf2f0e836e1a28b78a9f05f54e6e0
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74091185"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure

Você pode usar Azure Functions para implantar o código continuamente usando a [integração de controle do código-fonte](functions-deployment-technologies.md#source-control). A integração de controle do código-fonte habilita um fluxo de trabalho no qual uma atualização de código dispara a implantação no Azure. Se você for novo no Azure Functions, comece analisando o [Azure Functions visão geral](functions-overview.md).

A implantação contínua é uma boa opção para projetos em que você integra contribuições múltiplas e frequentes. Ao usar a implantação contínua, você mantém uma única fonte de verdade para seu código, o que permite que as equipes colaborem facilmente. Você pode configurar a implantação contínua no Azure Functions dos seguintes locais de código-fonte:

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

A unidade de implantação para funções no Azure é o aplicativo de funções. Todas as funções em um aplicativo de funções são implantadas ao mesmo tempo. Depois de habilitar a implantação contínua, o acesso ao código de função no portal do Azure é configurado como *somente leitura* porque a fonte de verdade está definida como em outro lugar.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para implantação contínua

Para que a implantação contínua seja realizada com sucesso, sua estrutura de diretório deve ser compatível com a estrutura de pasta básica que Azure Functions espera.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> A implantação contínua ainda não tem suporte para aplicativos do Linux em execução em um plano de consumo. 

## <a name="credentials"></a>Configurar a implantação contínua

Para configurar a implantação contínua para um aplicativo de funções existente, conclua estas etapas. As etapas demonstram a integração com um repositório GitHub, mas etapas semelhantes se aplicam para Azure Repos ou outros repositórios de código-fonte.

1. Em seu aplicativo de funções no [portal do Azure](https://portal.azure.com), selecione **recursos de plataforma** > **centro de implantação**.

    ![Abrir o centro de implantação](./media/functions-continuous-deployment/platform-features.png)

2. Na **central de implantação**, selecione **GitHub**e, em seguida, selecione **autorizar**. Se você já tiver autorizado o GitHub, selecione **continuar**. 

    ![Centro de implantação de serviço Azure App](./media/functions-continuous-deployment/github.png)

3. No GitHub, selecione o botão **autorizar AzureAppService** . 

    ![Autorizar serviço Azure App](./media/functions-continuous-deployment/authorize.png)
    
    Na **central de implantação** no portal do Azure, selecione **continuar**.

4. Selecione um dos seguintes provedores de compilação:

    * **Serviço de compilação do serviço de aplicativo**: melhor quando você não precisa de uma compilação ou se precisa de uma compilação genérica.
    * **Azure pipelines (visualização)** : melhor quando você precisa de mais controle sobre a compilação. Este provedor está em visualização no momento.

    ![Selecionar um provedor de compilação](./media/functions-continuous-deployment/build.png)

5. Configure informações específicas para a opção de controle do código-fonte que você especificou. Para o GitHub, você deve inserir ou selecionar valores para **organização**, **repositório**e **ramificação**. Os valores se baseiam no local do seu código. Em seguida, selecione **continuar**.

    ![Configurar o GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Examine todos os detalhes e, em seguida, selecione **concluir** para concluir a configuração da implantação.

    ![Resumo](./media/functions-continuous-deployment/summary.png)

Quando o processo for concluído, todo o código da origem especificada será implantado em seu aplicativo. Nesse ponto, as alterações na origem da implantação disparam uma implantação dessas alterações em seu aplicativo de funções no Azure.

## <a name="deployment-scenarios"></a>Cenários de implementação

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Mover funções existentes para implantação contínua

Se você já escreveu funções no [portal do Azure](https://portal.azure.com) e deseja baixar o conteúdo do seu aplicativo antes de alternar para a implantação contínua, vá para a guia **visão geral** do seu aplicativo de funções. Selecione o botão **baixar conteúdo do aplicativo** .

![Baixar o conteúdo do aplicativo](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Depois de configurar a integração contínua, você não poderá mais editar seus arquivos de origem no portal do functions.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
