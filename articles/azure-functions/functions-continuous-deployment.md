---
title: Implementação contínua para funções do Azure | Documentos da Microsoft
description: Utilize as funcionalidades de implementação contínua do serviço de aplicações do Azure para publicar as suas funções.
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
ms.openlocfilehash: dd1605aa2f5fc9e0b4bc790bae2a1c20cb3ea048
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594539"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure

Pode usar as funções do Azure para implementar continuamente o seu código usando [integração do controlo de origem](functions-deployment-technologies.md#source-control). Integração do controlo de origem ativa um fluxo de trabalho no qual uma atualização de código aciona a implementação para o Azure. Se estiver familiarizado com as funções do Azure, comece por rever as [descrição geral das funções do Azure](functions-overview.md).

Implementação contínua é uma boa opção para projetos em que integra várias e frequentes contribuições. Quando utiliza a implementação contínua, mantém uma única fonte de verdade para o seu código, que permite que as equipes colaborar facilmente. Pode configurar a implementação contínua nas funções do Azure, das seguintes localizações de código de origem:

* [Repositórios do Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

A unidade de implementação para funções no Azure é a aplicação de funções. Todas as funções na aplicação de função são implementadas ao mesmo tempo. Depois de ativar a implementação contínua, o acesso ao código de função no portal do Azure está configurado como *só de leitura* porque a fonte verdadeira é definida como noutro local.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para a implementação contínua

Para a implementação contínua com êxito, a estrutura de diretórios têm de ser compatível com a estrutura de pastas básico que espera as funções do Azure.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

## <a name="credentials"></a>Configurar a implementação contínua

Para configurar a implementação contínua para uma aplicação de função existente, conclua estes passos. Os passos demonstram a integração com um repositório do GitHub, mas os passos semelhantes são aplicáveis para repositórios do Azure ou outros repositórios de código de origem.

1. Na sua aplicação de função no [portal do Azure](https://portal.azure.com), selecione **recursos da plataforma** > **Deployment Center**.

    ![Central de implantação aberto](./media/functions-continuous-deployment/platform-features.png)

2. Na **Deployment Center**, selecione **GitHub**e, em seguida, selecione **autorizar**. Se já autorizado GitHub, selecione **continuar**. 

    ![Centro de implementação de serviço de aplicações do Azure](./media/functions-continuous-deployment/github.png)

3. No GitHub, selecione o **autorizar AzureAppService** botão. 

    ![Autorizar o serviço de aplicações do Azure](./media/functions-continuous-deployment/authorize.png)
    
    Na **Deployment Center** no portal do Azure, selecione **continuar**.

4. Selecione um dos seguintes fornecedores de compilação:

    * **Serviço de compilação do serviço de aplicações**: Melhor quando não precisar de uma compilação, ou se precisar de uma compilação genérica.
    * **Pipelines do Azure (pré-visualização)** : Melhor quando precisa de mais controle sobre a compilação. Este fornecedor está atualmente em pré-visualização.

    ![Selecione um fornecedor de compilação](./media/functions-continuous-deployment/build.png)

5. Configure as informações específicas para a opção de controlo de origem que especificou. No GitHub, tem de introduzir ou selecione os valores para **organização**, **repositório**, e **ramo**. Os valores baseiam-se na localização do seu código. Em seguida, selecione **continuar**.

    ![Configurar o GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Reveja todos os detalhes e, em seguida, selecione **concluir** para concluir a configuração da implementação.

    ![Resumo](./media/functions-continuous-deployment/summary.png)

Quando o processo estiver concluído, todo o código da origem especificada é implementado para a sua aplicação. Nesse ponto, as alterações na origem de implementação acionam uma implementação dessas alterações à sua aplicação de função no Azure.

## <a name="deployment-scenarios"></a>Cenários de implementação

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para a implementação contínua

Se já escreveu as funções na [portal do Azure](https://portal.azure.com) e pretender transferir o conteúdo da sua aplicação antes de mudar para a implementação contínua, vá para o **descrição geral** separador da sua aplicação de função. Selecione o **transferir conteúdo da aplicação** botão.

![Transferir conteúdo da aplicação](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Depois de configurar a integração contínua, já não é possível editar os arquivos de origem no portal de funções.

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
