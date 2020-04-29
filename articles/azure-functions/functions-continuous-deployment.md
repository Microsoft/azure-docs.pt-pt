---
title: Implementação contínua para Funções do Azure
description: Utilize as funcionalidades de implantação contínua do Serviço de Aplicações Azure para publicar as suas funções.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: cc1e100a0c2e652ab081869409fd24dbf88017a3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79277027"
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

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>Configurar a implementação contínua

Para configurar a implementação contínua para uma aplicação de função existente, complete estes passos. Os passos demonstram a integração com um repositório GitHub, mas passos semelhantes aplicam-se para O Repos Azure ou outros repositórios de código fonte.

1. Na sua aplicação de funções no [portal Azure,](https://portal.azure.com)selecione **Plataforma possui** > Centro de**Implantação**.

    ![Centro de Implantação Aberto](./media/functions-continuous-deployment/platform-features.png)

2. No **Centro de Implantação,** selecione **GitHub**e, em seguida, selecione **Autorizar**. Se já autorizou o GitHub, selecione **Continuar**. 

    ![Centro de Implantação de Serviços de Aplicações Azure](./media/functions-continuous-deployment/github.png)

3. No GitHub, selecione o botão Autorizar o **AzureAppService.** 

    ![Autorizar o Serviço de Aplicações Azure](./media/functions-continuous-deployment/authorize.png)
    
    No **Centro de Implantação** do portal Azure, selecione **Continuar**.

4. Selecione um dos seguintes fornecedores de construção:

    * **Serviço de Aplicações Serviço de construção serviço**: Melhor quando não precisa de uma construção ou se precisa de uma construção genérica.
    * **Gasodutos Azure (Pré-visualização)**: Melhor quando precisa de mais controlo sobre a construção. Este fornecedor encontra-se atualmente em pré-visualização.

    ![Selecione um fornecedor de construção](./media/functions-continuous-deployment/build.png)

5. Configure informações específicas da opção de controlo de fonte que especificou. Para o GitHub, deve introduzir ou selecionar valores para **Organização,** **Repositório**e **Ramo**. Os valores baseiam-se na localização do seu código. Em seguida, selecione **Continuar**.

    ![Configure GitHub](./media/functions-continuous-deployment/github-specifics.png)

6. Reveja todos os detalhes e, em seguida, selecione **Finish** para completar a sua configuração de implementação.

    ![Resumo](./media/functions-continuous-deployment/summary.png)

Quando o processo estiver concluído, todo o código da fonte especificada é implantado na sua aplicação. Nessa altura, as alterações na fonte de implantação desencadeiam uma implementação dessas alterações na sua aplicação de funções no Azure.

## <a name="deployment-scenarios"></a>Cenários de implementação

<a name="existing"></a>

### <a name="move-existing-functions-to-continuous-deployment"></a>Mover as funções existentes para uma implantação contínua

Se já escreveu funções no [portal Azure](https://portal.azure.com) e pretende descarregar o conteúdo da sua aplicação antes de mudar para uma implementação contínua, vá ao separador **Overview** da sua aplicação de funções. Selecione o botão de conteúdo da **aplicação Descarregamento.**

![Descarregue o conteúdo das aplicações](./media/functions-continuous-deployment/download.png)

> [!NOTE]
> Depois de configurar a integração contínua, já não pode editar os seus ficheiros de origem no portal Funções.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Best Practices for Azure Functions (Melhores Práticas para as Funções do Azure)](functions-best-practices.md)
