---
author: baanders
description: incluir o ficheiro para o processo de publicação de uma função Azure do Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: ddc56ab05a087c9e86d67a13aebcfb8e65fbd78f
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/14/2021
ms.locfileid: "107480749"
---
Para publicar o projeto numa aplicação de função em Azure, comece no Solution Explorer. Clique com o botão direito no projeto e, em seguida, escolha **Publicar.**

> [!IMPORTANT] 
> A publicação de uma aplicação de função no Azure incorre em encargos adicionais na sua subscrição, independentemente da Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Screenshot do Visual Studio, mostrando o menu de solução de clique direito. No menu, destaque para a Publicação.":::

Na página **publicar** que abre, deixe a seleção de alvo padrão do **Azure**. Em seguida, selecione **Seguinte**. 

Para um alvo específico, escolha **a App de Função Azure (Windows)** e, em seguida, selecione **Next**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Screenshot do Visual Studio, mostrando o diálogo da função Publish Azure. Na página-alvo específica, a seleção é Azure Function App (Windows).":::

No separador **De instância funções,** escolha a sua subscrição. Em seguida, selecione o ícone mais (+) para criar uma nova função.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Screenshot do Visual Studio, mostrando o diálogo da função Publish Azure. O ícone plus é realçado.":::

Na **App de Função (Windows) - Crie uma nova** janela, preencha os seguintes campos:
* **O nome** é o nome do plano de consumo que a Azure utilizará para hospedar a sua aplicação Azure Functions. Este nome também se aplicará à aplicação de função que detém a sua função real. Pode escolher um valor único ou deixar a sugestão padrão.
* Certifique-se de que a **Subscrição** corresponde à subscrição que pretende utilizar. 
* Certifique-se de que o **grupo de recursos** é o que pretende utilizar.
* Deixe a seleção do **tipo Plano** como **Consumo.**
* Selecione a **localização** do seu grupo de recursos.
* Crie um novo recurso **de Armazenamento Azure** selecionando o **novo** link. Desaprote a localização para corresponder ao seu grupo de recursos, use os outros valores predefinidos e, em seguida, selecione **OK**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Screenshot do Visual Studio, mostrando o diálogo da função Publish Azure. Os detalhes de uma nova aplicação de função estão a ser preenchidos, incluindo Nome, Subscrição, Grupo de Recursos, Tipo de Plano, Localização e Armazenamento Azure.":::

Em seguida, selecione **Criar**.

Após a criação do serviço de aplicações, o separador **de instância de Funções** abre.000.000. A sua nova aplicação de função aparece na área **de Apps de Função** por baixo do seu grupo de recursos. Selecione **Concluir**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Screenshot do Visual Studio, mostrando o diálogo da função Publish Azure. O separador de instância de Funções é selecionado. A nova aplicação de funções aparece no grupo de recursos.":::

No painel **de publicação** que abre na janela principal do Estúdio Visual, verifique se toda a informação parece correta. Em seguida, selecione **Publicar**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Screenshot do Visual Studio, mostrando o painel de publicação. O botão Publicar é realçado.":::

> [!NOTE]
> Se vir uma janela pop-up como o exemplo a seguir, selecione **Tente obter credenciais do Azure** e, em seguida, selecione **Save**.
> :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Screenshot do Visual Studio, mostrando uma janela pop-up chamada Publicar credenciais. Contém campos para um nome de utilizador e palavra-passe. Também contém um botão para tentar obter credenciais de Azure." border="false":::
>
> Se vir uma das seguintes advertências, siga as instruções para atualizar para a versão mais recente do tempo de execução do Azure Functions:
> * "Versão de Funções de Atualização no Azure."
> * "A sua versão do tempo de execução das funções não corresponde à versão em execução em Azure."
>
> Estes avisos podem aparecer se estiver a usar uma versão antiga do Visual Studio.

A sua aplicação de função foi agora publicada no Azure.
