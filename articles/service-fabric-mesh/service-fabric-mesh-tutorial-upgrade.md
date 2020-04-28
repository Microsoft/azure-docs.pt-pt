---
title: Tutorial- Atualizar uma aplicação de malha de tecido de serviço Azure
description: Este tutorial é parte quatro de uma série e mostra-lhe como atualizar uma aplicação de Malha de Tecido de Serviço Azure diretamente do Estúdio Visual.
author: dkkapur
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 7cdb8868f760ef0f35ab90c06b411110f871738c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75351724"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: Aprenda a atualizar uma aplicação de Tecido de Serviço usando o Estúdio Visual

Este tutorial é parte quatro de uma série e mostra-lhe como atualizar uma aplicação de Malha de Tecido de Serviço Azure diretamente do Estúdio Visual. A atualização incluirá uma atualização de código e uma atualização de config. Verá que os passos para a modernização e publicação de dentro do Estúdio Visual são os mesmos.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Atualize um serviço de malha de tecido de serviço utilizando o Estúdio Visual

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

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Atualize um serviço de malha de tecido de serviço utilizando o Estúdio Visual

Este artigo mostra como atualizar um microserviço dentro de uma aplicação. Neste exemplo, vamos modificar `WebFrontEnd` o serviço para exibir uma categoria de tarefa e aumentar a quantidade de CPU que é dada. Depois atualizaremos o serviço.

## <a name="modify-the-config"></a>Modificar o config

Quando cria uma aplicação de malha de tecido de serviço, o estúdio Visual adiciona um ficheiro **parâmetros.yaml** para cada ambiente de implantação (cloud e local). Nestes ficheiros, pode definir parâmetros e seus valores que podem ser referenciados a partir dos seus ficheiros Mesh *.yaml, tais como service.yaml ou network.yaml.  O Visual Studio fornece algumas variáveis para si, como o cpU que o serviço pode usar.

Atualizaremos o `WebFrontEnd_cpu` parâmetro para atualizar os `1.5` recursos do CPU para antecipar que o serviço **WebFrontEnd** será mais fortemente utilizado.

1. **Cloud**No projeto **todolistapp,** no âmbito da Nuvem de **Ambientes,** > abra o ficheiro **parâmetros.yaml.** Modificar `WebFrontEnd_cpu`o valor `1.5`para . O nome do parâmetro é prefaciado com o nome `WebFrontEnd_` do serviço como uma melhor prática para distingui-lo de parâmetros com o mesmo nome que se aplicam a diferentes serviços.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Abra o ficheiro **service.yaml** do projeto **WebFrontEnd** no âmbito dos**Recursos de Serviço** **WebFrontEnd** > .

    Note que `resources:` a `cpu:` secção in, está definida para `"[parameters('WebFrontEnd_cpu')]"`. Se o projeto estiver a ser construído `'WebFrontEnd_cpu` para a nuvem, o valor para será retirado do `1.5`arquivo de **parâmetros** > Da**Nuvem** > **ambiente.yaml,** e será . Se o projeto estiver a ser construído para funcionar localmente, > o valor será retirado do arquivo de **parâmetros locais** > **ambientes.yaml,** e será '0,5'.**Local**

> [!Tip]
> Por predefinição, o ficheiro de parâmetro sintetizador que é um par do ficheiro perfil.yaml será utilizado para fornecer os valores para esse ficheiro perfil.yaml.
> Por exemplo, ambientes > cloud > parâmetros.yaml fornece os valores dos parâmetros para Ambientes > Cloud > perfil.yaml.
>
> Pode anular isto adicionando o seguinte ao ficheiro profile.yaml:`parametersFilePath=”relative or full path to the parameters file”` Por exemplo, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` ou`parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modificar o modelo

Para introduzir uma alteração `Category` de código, adicione uma propriedade à `ToDoItem` classe no `ToDoItem.cs` ficheiro.

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

O `WebFrontEnd` projeto é uma aplicação ASP.NET Core com uma página web que mostra itens de lista de afazeres. No `WebFrontEnd` projeto, `Index.cshtml` abra e adicione as seguintes duas linhas, indicadas abaixo, para exibir a categoria da tarefa:

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

## <a name="upgrade-the-app-from-visual-studio"></a>Atualize a aplicação do Estúdio Visual

Quer esteja a fazer uma atualização de código, ou uma atualização de config (neste caso estamos a fazer as duas coisas), atualize a sua aplicação de malha de tecido de serviço no Azure clicando no **todolistapp** no Visual Studio e, em seguida, selecione **Publish...**

Em seguida, verá a caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric).

Utilize a queda do **perfil Target** para selecionar o ficheiro profile.yaml para utilizar para esta implementação. Estamos a atualizar a aplicação na nuvem para selecionarmos a **nuvem.yaml** `WebFrontEnd_cpu` no dropdown, que utilizará o valor de 1.0 definido nesse ficheiro.

![Caixa de diálogo de publicação do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecione a conta e a subscrição do Azure. Detete a **Localização** para o local que usou quando publicou originalmente a app de afazer para o Azure. Este artigo usou **os EUA Orientais.**

Desloque o **grupo de recursos** para o grupo de recursos que usou quando publicou originalmente a app de fazer para o Azure.

Dete tese de registo de **contentores Azure** para o nome de registo de contentores azul que criou quando publicou originalmente a app de fazer para o Azure.

No diálogo de publicação, prima o botão **Publicar** para atualizar a aplicação a fazer no Azure.

Monitorize o progresso da atualização selecionando o painel de **ferramentas** de tecido de serviço na janela **De saída** do Estúdio Visual. 

Depois de a imagem ser construída e empurrada para **For status** o Registo de Contentores Azure, aparecerá na saída a saída que pode clicar para monitorizar a implantação no portal Azure.

Uma vez concluída a atualização, a saída de **Ferramentas** de Tecido de Serviço apresentará o endereço IP e a porta da sua aplicação sob a forma de um URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um browser e navegue para o URL para ver o site em execução no Azure. Deve agora ver uma página web que contenha uma coluna de categoria.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Como atualizar uma aplicação de malha de tecido de serviço utilizando o Visual Studio

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)