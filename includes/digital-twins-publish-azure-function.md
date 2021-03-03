---
author: baanders
description: incluir o ficheiro para o processo de publicação de uma função Azure do Visual Studio
ms.service: digital-twins
ms.topic: include
ms.date: 1/21/2021
ms.author: baanders
ms.openlocfilehash: 59506b1d1d3fbbc5a532c597d46dc92ee3c2e98e
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101750093"
---
Para publicar o projeto numa aplicação de função em Azure, selecione o projeto à direita no *Solution Explorer* e escolha **a Publish.**

> [!IMPORTANT] 
> A publicação de uma aplicação de função no Azure incorre em encargos adicionais na sua subscrição, independentemente da Azure Digital Twins.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-1.png" alt-text="Screenshot do Estúdio Visual mostrando o menu de solução selecionado à direita. A publicação está em destaque no menu.":::

Na página *Publicar* que se segue, deixe a seleção de destino padrão do **Azure** e selecione *Next*. 

Para um alvo específico, escolha **Azure Function App (Windows)** e selecione *Next*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-2.png" alt-text="Screenshot do Estúdio Visual no diálogo de função Publish Azure. A Aplicação de Função Azure (Windows) é selecionada na página-alvo específica.":::

Na página *de instância funções,* escolha a sua subscrição. Em seguida, selecione o *+* ícone para criar uma nova Função Azure.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-3.png" alt-text="Screenshot do Estúdio Visual no diálogo de função Publish Azure. O botão + para criar uma nova função é realçado na página de instância de Funções.":::

Na *App de Função (Windows) - Crie uma nova* janela, preencha os campos da seguinte forma:
* **O nome** é o nome do plano de consumo que a Azure utilizará para hospedar a sua aplicação Azure Functions. Este será também o nome da aplicação de função que detém a sua função real. Pode escolher o seu próprio valor único ou deixar a sugestão padrão.
* Certifique-se de que a **Subscrição** corresponde à subscrição que pretende utilizar 
* Certifique-se de que o **grupo de Recursos** ao grupo de recursos que pretende utilizar
* Deixe o **tipo de Plano** como *Consumo*
* Selecione a **localização** que corresponde à localização do seu grupo de recursos
* Crie um novo recurso **de armazenamento Azure** utilizando o *novo...* link. Desaprote o local para combinar com o seu grupo de recursos, use os outros valores predefinidos e selecione "Ok".

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-4.png" alt-text="Screenshot do Estúdio Visual no diálogo de função Publish Azure. Os detalhes de uma nova aplicação de função estão a ser preenchidos, incluindo Nome, Subscrição, Grupo de Recursos, Tipo de Plano, Localização e Armazenamento Azure.":::

Em seguida, selecione **Criar**.

Após uma breve espera enquanto o serviço de aplicações é criado, o diálogo deve voltar à página *de instância de Funções,* com a sua nova aplicação de função a aparecer na área **de Apps de Função** aninhada por baixo do seu grupo de recursos. Selecione *Concluir*.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-5.png" alt-text="Publicar função Azure em Visual Studio: Instância de funções (após aplicação de função)":::

No painel *publicar* que se abre de volta na janela principal do Estúdio Visual, verifique se todas as informações parecem corretas e selecione **Publicar**.

:::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-6.png" alt-text="Screenshot do Estúdio Visual no diálogo de função Publish Azure. A nova aplicação de funções aparece na lista de aplicações de funções e há um botão Finish.":::

> [!NOTE]
> Se vires um popup como este: :::image type="content" source="../articles/digital-twins/media/tutorial-end-to-end/publish-azure-function-7.png" alt-text="Screenshot da janela pop-up do Estúdio Visual chamada Credenciais de Publicação. Contém campos para um nome de utilizador e palavra-passe, e um botão para tentar obter credenciais do Azure." border="false":::
> Selecione **Tente obter credenciais de Azure** e **Save**.
>
> Se vir um aviso para a *versão 'Atualizar funções' no Azure* ou se *a sua versão do tempo de execução das funções não corresponder à versão em execução em Azure:*
>
> Siga as instruções para atualizar para a versão mais recente do tempo de execução do Azure Functions. Este problema pode ocorrer se estiver a utilizar uma versão mais antiga do Visual Studio.

A sua aplicação de função foi agora publicada no Azure. 

Para que a sua aplicação de funções possa aceder ao Azure Digital Twins, terá de ter uma identidade gerida pelo sistema com permissões para aceder à sua instância Azure Digital Twins. Vais marcar isso a seguir.
