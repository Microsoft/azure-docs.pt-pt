---
title: Implementação contínua para Funções do Azure
description: Utilize as funcionalidades de implementação contínua do Azure App Service para publicar as suas funções.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: e49c235e11eea17fdd1a7ff7751cc0493934d725
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83123693"
---
# <a name="continuous-deployment-for-azure-functions"></a>Implementação contínua para Funções do Azure

Pode utilizar as Funções Azure para implementar o seu código continuamente utilizando a [integração do controlo de fontes](functions-deployment-technologies.md#source-control). A integração do controlo de origem permite um fluxo de trabalho no qual uma atualização de código aciona a implementação para o Azure. Se é novo em Funções Azure, inicie a revisão da visão geral das [Funções Azure](functions-overview.md).

A implementação contínua é uma boa opção para projetos onde integra contribuições múltiplas e frequentes. Quando utilizas uma implementação contínua, manténs uma única fonte de verdade para o teu código, que permite às equipas colaborar facilmente. Pode configurar a implementação contínua em Funções Azure a partir das seguintes localizações de código fonte:

* [Repositórios do Azure](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

A unidade de implantação para funções em Azure é a aplicação de função. Todas as funções de uma aplicação de função são implementadas ao mesmo tempo. Depois de ativar a implementação contínua, o acesso ao código de função no portal Azure é configurado como *leitura apenas* porque a fonte da verdade está definida para estar em outro lugar.

## <a name="requirements-for-continuous-deployment"></a>Requisitos para a implantação contínua

Para que a implementação contínua tenha sucesso, a sua estrutura de diretório deve ser compatível com a estrutura básica da pasta que o Azure Functions espera.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> A implementação contínua ainda não é suportada para aplicações Linux que estão a decorrer num plano de Consumo. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurar a implementação contínua

Para configurar a implementação contínua para uma aplicação de função existente, complete estes passos. Os passos demonstram a integração com um repositório GitHub, mas passos semelhantes aplicam-se a Azure Repos ou outros repositórios de código fonte.

1. Na sua aplicação de função no [portal Azure](https://portal.azure.com), selecione **Centro de Implementação,** selecione **GitHub**e, em seguida, selecione **Authorize**. Se já autorizou o GitHub, **selecione Continue** e ignore o próximo passo. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Centro de Implementação de Serviço de Aplicações Azure":::

3. No GitHub, **selecione Authorize AzureAppService**.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Centro de Implementação de Serviço de Aplicações Azure":::

    Introduza a sua palavra-passe GitHub e, em seguida, **selecione Continue**.

4. Selecione um dos seguintes fornecedores de construção:

    * **Serviço de aplicações constrói serviço**: O melhor quando não precisa de uma construção ou se precisa de uma construção genérica.
    * **Gasodutos Azure (Pré-visualização)**: Melhor quando precisar de mais controlo sobre a construção. Este fornecedor está neste momento em pré-visualização.

    Selecione **Continuar**.

5. Configure informações específicas da opção de controlo de origem especificada. Para o GitHub, tem de introduzir ou selecionar valores para **Organização,** **Repositório**e **Ramo.** Os valores baseiam-se na localização do seu código. Em seguida, **selecione Continue**.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="Centro de Implementação de Serviço de Aplicações Azure":::

6. Reveja todos os detalhes e, em seguida, **selecione Terminar** para completar a sua configuração de implementação.

Quando o processo estiver concluído, todo o código da fonte especificada é implantado na sua aplicação. Nessa altura, as alterações na fonte de implementação desencadeiam uma implementação dessas alterações na sua aplicação de função em Azure.

> [!NOTE]
> Depois de configurar a integração contínua, já não pode editar os seus ficheiros de origem no portal Funções.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
