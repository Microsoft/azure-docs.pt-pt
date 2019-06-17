---
title: Implementação contínua para funções do Azure | Documentos da Microsoft
description: Use os recursos de implementação contínua do serviço de aplicações do Azure para publicar as suas funções.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 09/25/2016
ms.author: glenga
ms.openlocfilehash: d4d2f24a0a7b1f01627ed2cea4a5732ca0e001c9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068413"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure

As funções do Azure permite-lhe implementar seu código através do continuamente [integração do controlo de origem](functions-deployment-technologies.md#source-control). Isto permite que um fluxo de trabalho onde o código atualiza acionador de implementação para o Azure. Se estiver familiarizado com as funções do Azure, comece com o [descrição geral das funções do Azure](functions-overview.md).

Implementação contínua é uma excelente opção para projetos em que estiver a integrar várias e frequentes contribuições. Ele também permite manter uma única fonte de verdade para o seu código de função. Pode configurar a implementação contínua nas funções do Azure, das seguintes localizações de código de origem:

* [Repositórios do Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

A unidade de implementação para as funções do Azure é a aplicação de funções. Isso significa que todas as funções na aplicação de função são implementadas ao mesmo tempo. Depois da implementação contínua é ativada, o acesso ao código de função no portal do Azure está configurado como *só de leitura*, uma vez que a fonte verdadeira é definida como noutro local.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para a implementação contínua

Para a implementação contínua com êxito, a estrutura de diretórios tem de ser compatível com a seguinte estrutura de pasta básico que espera as funções do Azure:

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Configurar a implementação contínua

Utilize este procedimento para configurar a implementação contínua para uma aplicação de função existente. Estes passos demonstram a integração com um repositório do GitHub, mas os passos semelhantes são aplicáveis para repositórios do Azure ou outros repositórios de código de origem.

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **central de implantação**.

    ![Abrir o deployment center](./media/functions-continuous-deployment/platform-features.png)

2. Sobre o **Deployment Center**, selecione **GitHub** para **controlo de origem** > **autorizar**.

    ![Central de implantação](./media/functions-continuous-deployment/github.png)

3. Selecione **autorizar AzureAppService** > **continuar**.

    ![Autorizar](./media/functions-continuous-deployment/authorize.png)

4. Selecione um dos seguintes fornecedores de compilação:

    * **Serviço de compilação do serviço de aplicações** – melhor quando não precisar de uma compilação, ou se precisar de uma compilação genérica.
    * **Pipelines do Azure (pré-visualização)** – melhor quando precisar de mais controlo sobre a compilação. Este fornecedor está atualmente em pré-visualização.

    ![Selecionar um fornecedor de compilação](./media/functions-continuous-deployment/build.png)

5. Configure as informações específicas para a opção de controlo de origem que especificou. Do GitHub, tem de fornecer a **organização**, **repositório**, e **ramo** onde reside o seu código. Em seguida, selecione **continuar**.

    ![Configurar o GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Por fim, reveja todos os detalhes e selecione **concluir** para concluir a configuração da implementação.

    ![Resumo](./media/functions-continuous-deployment/summary.png)

Quando o processo for concluído, todo o código da origem especificada é implementado para a sua aplicação. Nesse ponto, as alterações na origem de implementação acionam uma implementação dessas alterações à sua aplicação de função no Azure.

## <a name="deployment-scenarios"></a>Cenários de implementação

<a name="existing"></a>
### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para a implementação contínua

Se já escreveu as funções na [portal do Azure](https://portal.azure.com) e pretende transferir os conteúdos da sua aplicação antes de mudar para a implementação contínua, deve navegar para o **descrição geral** separador da sua função aplicação e clique nas **transferir conteúdo da aplicação** botão.

![Transferir o conteúdo da aplicação](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Depois de configurar a integração contínua, já não é possível editar os arquivos de origem no portal de funções.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
