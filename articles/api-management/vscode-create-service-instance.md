---
title: Criar um exemplo de Gestão API Azure usando o Código do Estúdio Visual Microsoft Docs
description: Código de Estúdio Visual para criar uma instância de Gestão API Azure.
ms.service: api-management
ms.workload: integration
author: vladvino
ms.author: apimpm
ms.topic: quickstart
ms.date: 09/14/2020
ms.openlocfilehash: fdf7e842959e09a8ec35017031f745cb16d13c6d
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400408"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-using-visual-studio-code"></a>Quickstart: Criar uma nova instância de serviço de gestão API da Azure usando o Código do Estúdio Visual

A Gestão de API (APIM) do Azure ajuda as organizações a publicar APIs para programadores externos, parceiros e internos, de modo a que estes possam explorar ao máximo o potencial dos seus dados e serviços. A Gestão de API fornece as competências essenciais para garantir um programa de API bem-sucedido através do envolvimento dos programadores, das informações de negócio, da análise, da segurança e da proteção. A APIM permite-lhe criar e gerir gateways de API modernos para serviços de back-end existentes alojados em qualquer lado. Para obter mais informações, consulte o tópico [Descrição Geral](api-management-key-concepts.md).

Este quickstart descreve os passos para criar um novo exemplo de Gestão de API usando a *pré-visualização da extensão de gestão da API Azure* para o Código do Estúdio Visual. Também pode utilizar a extensão para realizar operações de gestão comuns na sua instância de Gestão de API.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Além disso, certifique-se de que instalou o seguinte:

- [Visual Studio Code](https://code.visualstudio.com/)

- [Extensão de gestão da API Azure para código de estúdio visual (pré-visualização)](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-apimanagement&ssr=false#overview)

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Lançar Código de Estúdio Visual e abrir a extensão Azure. (Se não vir o ícone Azure na Barra de Atividades, certifique-se de que a extensão de *Gestão API AZure* está ativada.)

Selecione **Iniciar sação no Azure...** para lançar uma janela do navegador e iniciar sação na sua conta Microsoft.

![Inscreva-se no Azure a partir da extensão de Gestão da API para o Código VS](./media/vscode-create-service-instance/vscode-apim-login.png)

## <a name="create-an-api-management-service"></a>Criar um serviço de Gestão de API

Assim que fizer o seu sº de subscrição na sua conta Microsoft, o painel de exploradores *AZure: API Management* irá listar as suas subscrições Azure.

Clique com o botão direito na subscrição que gostaria de utilizar e selecione **Create API Management in Azure**.

![Criar assistente de gestão API em código VS](./media/vscode-create-service-instance/vscode-apim-create.png)

No painel que abre, forneça um nome para o novo caso de Gestão da API. Deve ser globalmente único dentro de Azure e consistir em 1-50 caracteres alfanuméricos e/ou hífenes, e começar com uma letra e terminar com um alfanumérico.

Será criado um novo caso de Gestão da API (e grupo de recursos-mãe) com o nome especificado. Por padrão, o caso é criado na região *oeste dos EUA* com O *SKU de consumo.*

> [!TIP]
> Se ativar a **Criação Avançada** nas Definições de *Extensão de Gestão da API Azure,* também pode especificar um [SKU de Gestão de API,](https://azure.microsoft.com/pricing/details/api-management/) [região Azure](https://status.azure.com/en-us/status)e um [grupo de recursos](../azure-resource-manager/management/overview.md) para implementar a sua instância de Gestão de API.
>
> Enquanto o *SKU de Consumo* demora menos de um minuto a providenciar, outros SKUs normalmente demoram 30-40 minutos a criar.

Neste momento, está pronto para importar e publicar a sua primeira API. Pode fazê-lo e também executar operações comuns de Gestão de API dentro da extensão do Código do Estúdio Visual. Veja [o tutorial](visual-studio-code-tutorial.md) para mais informações.

![Recém-criado exemplo de Gestão de API no painel de extensão da API Management do código VS](./media/vscode-create-service-instance/vscode-apim-instance.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não for necessário, remova a instância de Gestão da API clicando à direita e selecionando **Open in Portal** para eliminar o serviço de [Gestão API](get-started-create-service-instance.md#clean-up-resources) e o seu grupo de recursos.

Em alternativa, pode selecionar **eliminar a Gestão da API** apenas para eliminar a instância de Gestão da API (esta operação não elimina o seu grupo de recursos).

![Eliminar instância de Gestão de API do Código VS](./media/vscode-create-service-instance/vscode-apim-delete.png)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Importar e gerir APIs utilizando a Extensão de Gestão da API](visual-studio-code-tutorial.md)
