---
title: Tutorial- Atualize uma aplicação de malha de tecido de serviço Azure
description: Este tutorial é parte quatro de uma série e mostra-lhe como atualizar uma aplicação de malha de tecido de serviço Azure diretamente do Visual Studio.
author: georgewallace
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: 1020613eb43177ba159601f253848f8d03f385a8
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625437"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: Saiba como atualizar uma aplicação de Tecido de Serviço usando o Visual Studio

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

Este tutorial é parte quatro de uma série e mostra-lhe como atualizar uma aplicação de malha de tecido de serviço Azure diretamente do Visual Studio. A atualização incluirá tanto uma atualização de código como uma atualização config. Verá que os passos para a atualização e publicação de dentro do Visual Studio são os mesmos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Atualize um serviço de malha de tecido de serviço usando o Visual Studio

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar uma aplicação do Service Fabric Mesh em execução no seu cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Atualizar uma aplicação do Service Fabric Mesh
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se ainda não implementou a aplicação de tarefas pendentes, siga as instruções em [Publicar uma aplicação Web do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Atualize um serviço de malha de tecido de serviço usando o Visual Studio

Este artigo mostra como atualizar um micro serviço dentro de uma aplicação. Neste exemplo, vamos modificar o `WebFrontEnd` serviço para exibir uma categoria de tarefa e aumentar a quantidade de CPU que lhe é dada. Então vamos atualizar o serviço implantado.

## <a name="modify-the-config"></a>Modifique a config

Quando cria uma aplicação de malha de tecido de serviço, o estúdio Visual adiciona um ficheiro **parâmetros.yaml** para cada ambiente de implementação (nuvem e local). Nestes ficheiros, pode definir parâmetros e seus valores que podem ser referenciados a partir dos seus ficheiros Mesh *.yaml, tais como service.yaml ou network.yaml.  O Visual Studio fornece algumas variáveis para si, tais como quanto CPU o serviço pode usar.

Atualizaremos o `WebFrontEnd_cpu` parâmetro para atualizar os recursos do CPU para `1.5` antecipar que o serviço **WebFrontEnd** será mais fortemente utilizado.

1. No projeto **todolistapp,** em **Environments**  >  **Cloud,** abra os **parâmetros.yaml.** Modifique o `WebFrontEnd_cpu` valor para `1.5` . O nome do parâmetro é prefaciado com o nome de serviço `WebFrontEnd_` como uma boa prática para distingui-lo dos parâmetros do mesmo nome que se aplicam a diferentes serviços.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Abra o ficheiro de serviço do projeto **WebFrontEnd.yaml** ao abrigo dos Recursos de  Serviço **WebFrontEnd**  >  .

    Note que a `resources:` secção `cpu:` in.está definida para `"[parameters('WebFrontEnd_cpu')]"` . Se o projeto estiver a ser construído para a nuvem, o valor para `'WebFrontEnd_cpu` será retirado do ficheiro **Environments**  >    >  **Cloud.yaml,** e será `1.5` . Se o projeto estiver a ser construído para funcionar localmente, o valor será retirado do arquivo **Ambientes**  >  **Locais.yaml,**  >   e será '0.5'.

> [!Tip]
> Por predefinição, o ficheiro de parâmetros que é um par do ficheiro profile.yaml será utilizado para fornecer os valores para esse ficheiro profile.yaml.
> Por exemplo, Ambientes > Os parâmetros > cloud.yaml fornecem os valores de parâmetro para Ambientes > perfil de > cloud.yaml.
>
> Pode anular isto adicionando o seguinte ao ficheiro profile.yaml: `parametersFilePath=”relative or full path to the parameters file”` Por exemplo, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` ou `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modificar o modelo

Para introduzir uma alteração de código, adicione uma `Category` propriedade à classe no `ToDoItem` `ToDoItem.cs` ficheiro.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Em seguida, atualize o `Load()` método, no mesmo ficheiro, para definir a categoria para uma cadeia predefinida:

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

O `WebFrontEnd` projeto é uma aplicação core ASP.NET com uma página web que mostra itens de lista de a fazer. No `WebFrontEnd` projeto, abra `Index.cshtml` e adicione as seguintes duas linhas, indicadas abaixo, para exibir a categoria da tarefa:

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

Construa e execute a aplicação para verificar se vê uma nova coluna de categoria na página web que lista as tarefas.

## <a name="upgrade-the-app-from-visual-studio"></a>Atualizar a app do Visual Studio

Quer esteja a fazer uma atualização de código, ou uma atualização config (neste caso estamos a fazer os dois), atualize a sua aplicação de Malha de Tecido de Serviço no Azure clicando à direita no **site em Visual** Studio e, em seguida, selecione **Publicar...**

Em seguida, verá a caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric).

Utilize o **perfil de ponto** de entrega do destino para selecionar o ficheiro profile.yaml para utilizar para esta implementação. Estamos a atualizar a aplicação na nuvem para selecionarmos a **cloud.yaml** no dropdown, que utilizará o `WebFrontEnd_cpu` valor de 1.0 definido nesse ficheiro.

![Caixa de diálogo de publicação do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecione a conta e a subscrição do Azure. Desaprote o **Local** para o local que usou quando publicou originalmente a aplicação para fazer para o Azure. Este artigo usou **o Leste dos EUA.**

Desconfiem **do grupo de recursos** para o grupo de recursos que usou quando publicou originalmente a aplicação para fazer para o Azure.

Deite o **Registo do Contentor Azure** no nome do registo do contentor azul que criou quando publicou originalmente a aplicação de a-fazer para a Azure.

No diálogo de publicação, prima o botão **Publicar** para atualizar a aplicação a fazer no Azure.

Monitorize o progresso da atualização selecionando o painel **de Ferramentas de Tecido** de Serviço na janela de **saída** do Estúdio Visual. 

Depois de a imagem ser construída e empurrada para o Registo do Contentor Azure, aparecerá um link para o **estado** na saída que pode clicar para monitorizar a implementação no portal Azure.

Uma vez terminada a atualização, a saída **ferramentas de tecido** de serviço mostrará o endereço IP e a porta da sua aplicação sob a forma de UM URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um browser e navegue para o URL para ver o site em execução no Azure. Deve agora ver uma página web que contenha uma coluna de categoria.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Como atualizar uma aplicação de malha de tecido de serviço usando o Visual Studio

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)