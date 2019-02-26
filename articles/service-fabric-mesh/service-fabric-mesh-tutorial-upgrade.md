---
title: Tutorial - atualizar um aplicativo de malha do Azure Service Fabric | Documentos da Microsoft
description: Saiba como atualizar uma aplicação do Service Fabric com o Visual Studio
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 23809abd06d626eb87e5d5d15d265f1769b97b66
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806743"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: Saiba como atualizar uma aplicação do Service Fabric com o Visual Studio

Este tutorial é a parte quatro de uma série e mostra-lhe como atualizar uma aplicação do Azure Service Fabric em malha diretamente a partir do Visual Studio. A atualização irá incluir uma atualização de código e uma configuração de atualização. Verá que os passos para atualizar e publicar a partir de dentro do Visual Studio são os mesmos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Atualizar um serviço de malha do Service Fabric com o Visual Studio

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar uma aplicação do Service Fabric Mesh em execução no seu cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Atualizar uma malha de recursos de infraestrutura do serviço aplicação
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se ainda não implementou a aplicação de tarefas pendentes, siga as instruções em [Publicar uma aplicação Web do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Atualizar um serviço de malha do Service Fabric com o Visual Studio

Este artigo mostra como atualizar um microsserviço dentro de um aplicativo. Neste exemplo, vamos modificar o `WebFrontEnd` serviço para apresentar uma categoria de tarefa e aumentar a quantidade de CPU é fornecido. Em seguida, podemos irá atualizar o serviço implementado.

## <a name="modify-the-config"></a>Modificar a configuração

Quando cria uma aplicação de malha do Service Fabric, o Visual studio adiciona uma **parameters.yaml** ficheiro para cada ambiente de implementação (na cloud e locais). Esses arquivos, pode definir parâmetros e os respetivos valores que, em seguida, podem ser referenciados a partir dos seus ficheiros de *.yaml malha como service.yaml ou network.yaml.  O Visual Studio fornece algumas variáveis para, como a quantidade da CPU pode utilizar o serviço.

Iremos atualizar o `WebFrontEnd_cpu` parâmetro para atualizar os recursos de cpu para `1.5` antes que o **WebFrontEnd** serviço irá ser utilizado mais pesada.

1. Na **todolistapp** projeto, em **ambientes** > **Cloud**, abra o **parameters.yaml** ficheiro. Modificar a `WebFrontEnd_cpu`, valor a `1.5`. O nome do parâmetro vêm acompanhado com o nome do serviço `WebFrontEnd_` como melhor prática para distingui-la a partir de parâmetros do mesmo nome que se aplicam aos diferentes serviços.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Abra o **WebFrontEnd** do projeto **service.yaml** de ficheiros em **WebFrontEnd** > **recursos de serviço**.

    Tenha em atenção que na `resources:` secção, `cpu:` está definida como `"[parameters('WebFrontEnd_cpu')]"`. Se o projeto está sendo criado para a nuvem, o valor para `'WebFrontEnd_cpu` será retirada dos **ambientes** > **Cloud** > **parameters.yaml** de ficheiros e será `1.5`. Se o projeto está sendo criado para ser executado localmente, o valor será retirado dos **ambientes** > **Local** > **parameters.yaml** arquivo, e será o "0,5".

> [!Tip]
> Por predefinição, o ficheiro de parâmetro que é um elemento de rede do ficheiro profile.yaml será utilizado para indicar os valores para esse ficheiro profile.yaml.
> Por exemplo, ambientes > nuvem > parameters.yaml fornece os valores de parâmetro para ambientes > nuvem > profile.yaml.
>
> Pode substituí-lo ao adicionar o seguinte ao ficheiro profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` Por exemplo, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` ou `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modificar o modelo

Para introduzir uma alteração de código, adicione uma `Category` propriedade para o `ToDoItem` classe no `ToDoItem.cs` ficheiro.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Em seguida, atualize o `Load()` método, no mesmo ficheiro, para definir a categoria para uma cadeia de caracteres padrão:

```csharp
public static ToDoItem Load(string description, int index, bool completed)
{
    ToDoItem newItem = new ToDoItem(description)
    {
        Index = index,
        Completed = completed, 
        Category = "none"    // <-- add this line
    };

    return newItem;
}
```

## <a name="modify-the-service"></a>Modificar o serviço

O `WebFrontEnd` projeto é uma aplicação ASP.NET Core com uma página da web que mostra itens de lista de tarefas pendentes. Na `WebFrontEnd` projeto aberto `Index.cshtml` e adicione as seguintes duas linhas, indicadas abaixo, para apresentar a categoria da tarefa:

```HTML
<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Category</th>           @*add this line*@
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Category</td>           @*add this line*@
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Crie e execute a aplicação para verificar que vê uma nova coluna de categoria na página da web que lista as tarefas.

## <a name="upgrade-the-app-from-visual-studio"></a>Atualizar a aplicação a partir do Visual Studio

Se estiver fazendo uma atualização de código, ou uma atualização de configuração (neste caso, estamos ambos), atualize a sua aplicação de malha do Service Fabric no Azure, clicando na **todolistapp** no Visual Studio e, em seguida, selecione **publicar...**

Em seguida, verá a caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric).

Utilizar o **perfil de destino** lista pendente para selecionar o ficheiro de profile.yaml a utilizar para esta implementação. Vamos atualizar a aplicação na cloud pelo que vamos selecionar o **cloud.yaml** na lista pendente, que irá utilizar o `WebFrontEnd_cpu` o valor 1,0 definidas nesse ficheiro.

![Caixa de diálogo de publicação do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecione a conta e a subscrição do Azure. Definir o **localização** para a localização que utilizou quando publicado originalmente a aplicação de tarefas pendentes para o Azure. Este artigo utilizado **E.U.A. Leste**.

Definir **grupo de recursos** ao grupo de recursos que utilizou quando publicado originalmente a aplicação de tarefas pendentes para o Azure.

Definir **Azure Container Registry** para o nome do registo de contentor do azure que criou quando publicado originalmente a aplicação de tarefas pendentes para o Azure.

Na caixa de diálogo Publicar, prima a **publicar** botão para atualizar a aplicação de tarefas no Azure.

Monitorizar o progresso da atualização, selecionando o **ferramentas do Service Fabric** painel no Visual Studio **saída** janela. 

Depois da imagem é criada e enviada para o Azure Container Registry, um **para obter o estado** ligação será apresentado na saída que pode clicar para monitorizar a implementação no portal do Azure.

Assim que a atualização estiver concluída, o **ferramentas do Service Fabric** saída irá apresentar o endereço IP e porta do seu aplicativo na forma de um URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um browser e navegue para o URL para ver o site em execução no Azure. Agora, deverá ver uma página da web que contém uma coluna de categoria.

## <a name="next-steps"></a>Passos Seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Como atualizar uma aplicação de malha do Service Fabric com o Visual Studio

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)