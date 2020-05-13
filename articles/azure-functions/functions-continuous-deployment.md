---
title: Implementação contínua para Funções do Azure
description: Utilize as funcionalidades de implantação contínua do Serviço de Aplicações Azure para publicar as suas funções.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123693"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure

Pode utilizar funções Azure para implementar o seu código continuamente utilizando a [integração](functions-deployment-technologies.md#source-control)do controlo de fonte . A integração do controlo de fontes permite um fluxo de trabalho no qual uma atualização de código desencadeia a implantação para o Azure. Se é novo nas Funções Azure, inicie-se por rever a visão geral das [Funções Azure](functions-overview.md).

A implantação contínua é uma boa opção para projetos onde integra contribuições múltiplas e frequentes. Quando utiliza uma implementação contínua, mantém uma única fonte de verdade para o seu código, o que permite às equipas colaborarem facilmente. Pode configurar a implantação contínua nas Funções Azure a partir dos seguintes locais do código fonte:

* [Repositórios do Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

A unidade de implantação para funções no Azure é a aplicação de funções. Todas as funções numa aplicação de função são implementadas ao mesmo tempo. Depois de ativar a implementação contínua, o acesso ao código de funcionamento no portal Azure é configurado *apenas* como leitura porque a fonte da verdade está definida para ser em outro lugar.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para a implantação contínua

Para que a implementação contínua seja bem sucedida, a sua estrutura de diretório deve ser compatível com a estrutura básica da pasta que as Funções Azure esperam.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> A implementação contínua ainda não é suportada para aplicações Linux em execução de um plano de consumo. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurar a implantação contínua

Para configurar a implementação contínua para uma aplicação de função existente, complete estes passos. Os passos demonstram a integração com um repositório GitHub, mas passos semelhantes aplicam-se para O Repos Azure ou outros repositórios de código fonte.

1. Na sua aplicação de funções no [portal Azure,](https://portal.azure.com)selecione **Centro de Implantação,** selecione **GitHub,** e, em seguida, selecione **Autorizar**. Se já autorizou o GitHub, selecione **Continuar** e saltar o próximo passo. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Centro de Implantação de Serviços de Aplicações Azure":::

3. No GitHub, **selecione Autorizar o AzureAppService**.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Autorizar o Serviço de Aplicações Azure":::

    Introduza a sua palavra-passe GitHub e, em seguida, selecione **Continuar**.

4. Selecione um dos seguintes fornecedores de construção:

    * **Serviço de Aplicações Serviço de construção serviço**: Melhor quando não precisa de uma construção ou se precisa de uma construção genérica.
    * **Gasodutos Azure (Pré-visualização)**: Melhor quando precisa de mais controlo sobre a construção. Este fornecedor encontra-se atualmente em pré-visualização.

    Selecione **Continuar**.

5. Configure informações específicas da opção de controlo de fonte que especificou. Para o GitHub, deve introduzir ou selecionar valores para **Organização,** **Repositório**e **Ramo**. Os valores baseiam-se na localização do seu código. Em seguida, selecione **Continuar**.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="Configure GitHub":::

6. Reveja todos os detalhes e, em seguida, selecione **Finish** para completar a sua configuração de implementação.

Quando o processo estiver concluído, todo o código da fonte especificada é implantado na sua aplicação. Nessa altura, as alterações na fonte de implantação desencadeiam uma implementação dessas alterações na sua aplicação de funções no Azure.

> [!NOTE]
> Depois de configurar a integração contínua, já não pode editar os seus ficheiros de origem no portal Funções.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
