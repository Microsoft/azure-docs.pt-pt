---
author: baanders
description: incluir o ficheiro para o processo de publicação de uma função Azure do Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 069a29f49172cf3bad6b7f7b6aca28f32f5d63b3
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683909"
---
Para publicar o projeto numa aplicação de função em Azure, selecione o projeto à direita no *Solution Explorer* e escolha **a Publish.**

> [!IMPORTANT] 
> A publicação de uma aplicação de função no Azure incorre em encargos adicionais na sua subscrição, independentemente da Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Visual Studio: projeto de publicação":::

Na página *Publicar* que se segue, deixe a seleção de alvo padrão de **Azure** e atinja *Next*. 

Para um alvo específico, escolha **Azure Function App (Windows)** e acerte *em Next*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Publicar função Azure em Visual Studio: alvo específico":::

Na página *de instância funções,* escolha a sua subscrição. Isto deve povoar uma caixa com os *grupos de recursos* na sua subscrição.

Selecione o grupo de recursos do seu caso e bata *+* para criar uma nova Função Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Publicar função Azure em Visual Studio: Instância de funções (antes da aplicação de função)":::

Na *App de Função (Windows) - Crie uma nova* janela, preencha os campos da seguinte forma:
* **O nome** é o nome do plano de consumo que a Azure utilizará para hospedar a sua aplicação Azure Functions. Este será também o nome da aplicação de função que detém a sua função real. Pode escolher o seu próprio valor único ou deixar a sugestão padrão.
* Certifique-se de que a **Subscrição** corresponde à subscrição que pretende utilizar 
* Certifique-se de que o **grupo de Recursos** ao grupo de recursos que pretende utilizar
* Deixe o **tipo de Plano** como *Consumo*
* Selecione a **localização** que corresponde à localização do seu grupo de recursos
* Crie um novo recurso **de armazenamento Azure** utilizando o *novo...* link. Desaprote o local para combinar com o seu grupo de recursos, use os outros valores predefinidos e acerte "Ok".

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Publicar função Azure em Visual Studio: Function App (Windows) - Criar novos":::

Em seguida, selecione **Criar**.

Isto deve levá-lo de volta à página *de instância funções,* onde a sua nova aplicação de função está agora visível por baixo do seu grupo de recursos. Hit *Finish*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publicar função Azure em Visual Studio: Instância de funções (após aplicação de função)":::

No painel *publicar* que se abre de volta na janela principal do Estúdio Visual, verifique se todas as informações parecem corretas e selecione **Publicar**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Publicar função Azure em Visual Studio: publicar":::

> [!NOTE]
> Se vir um popup como este: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Publicar a função Azure em Visual Studio: publicar credenciais" border="false":::
> Selecione **Tente obter credenciais de Azure** e **Save**.
>
> Se vir um aviso para a *versão 'Atualizar funções' no Azure* ou se *a sua versão do tempo de execução das funções não corresponder à versão em execução em Azure:*
>
> Siga as instruções para atualizar para a versão mais recente do tempo de execução do Azure Functions. Este problema pode ocorrer se estiver a utilizar uma versão mais antiga do Visual Studio.

Para que a sua aplicação de funções possa aceder ao Azure Digital Twins, precisa de ter uma identidade gerida pelo sistema e ter permissões para aceder à sua instância Azure Digital Twins. Vais marcar isso a seguir.
