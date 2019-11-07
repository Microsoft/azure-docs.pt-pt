---
title: Tutorial – atualizar um aplicativo de malha de Service Fabric do Azure | Microsoft Docs
description: Este tutorial é a parte quatro de uma série e mostra como atualizar um aplicativo de malha de Service Fabric do Azure diretamente do Visual Studio.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/29/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: 81f155d5708a2fca2fc1145feb20af12d2fd151e
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73686208"
---
# <a name="tutorial-learn-how-to-upgrade-a-service-fabric-application-using-visual-studio"></a>Tutorial: saiba como atualizar um aplicativo Service Fabric usando o Visual Studio

Este tutorial é a parte quatro de uma série e mostra como atualizar um aplicativo de malha de Service Fabric do Azure diretamente do Visual Studio. A atualização incluirá uma atualização de código e uma atualização de configuração. Você verá que as etapas para atualizar e publicar de dentro do Visual Studio são as mesmas.

Neste tutorial, ficará a saber como:
> [!div class="checklist"]
> * Atualizar um serviço de malha Service Fabric usando o Visual Studio

Nesta série de tutoriais, ficará a saber como:
> [!div class="checklist"]
> * [Criar uma aplicação do Service Fabric Mesh no Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Depurar uma aplicação do Azure Service Fabric Mesh em execução no cluster de desenvolvimento local](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Implementar uma aplicação do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * Atualizar um aplicativo de malha Service Fabric
> * [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial:

* Se ainda não implementou a aplicação de tarefas pendentes, siga as instruções em [Publicar uma aplicação Web do Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md).

## <a name="upgrade-a-service-fabric-mesh-service-by-using-visual-studio"></a>Atualizar um serviço de malha Service Fabric usando o Visual Studio

Este artigo mostra como atualizar um microserviço em um aplicativo. Neste exemplo, modificaremos o serviço de `WebFrontEnd` para exibir uma categoria de tarefa e aumentar a quantidade de CPU fornecida. Em seguida, atualizaremos o serviço implantado.

## <a name="modify-the-config"></a>Modificar a configuração

Quando você cria um aplicativo de malha Service Fabric, o Visual Studio adiciona um arquivo **Parameters. YAML** para cada ambiente de implantação (nuvem e local). Nesses arquivos, você pode definir parâmetros e seus valores que podem ser referenciados de seus arquivos de malha *. YAML, como Service. YAML ou Network. YAML.  O Visual Studio fornece algumas variáveis para você, como a quantidade de CPU que o serviço pode usar.

Atualizaremos o parâmetro `WebFrontEnd_cpu` para atualizar os recursos da CPU para `1.5` de previsão de que o serviço de **WebFrontEnd** será muito usado.

1. No projeto **apldelistadetarefas** , em **ambientes** > **nuvem**, abra o arquivo **Parameters. YAML** . Modifique o `WebFrontEnd_cpu`, o valor para `1.5`. O nome do parâmetro é precedido pelo nome do serviço `WebFrontEnd_` como uma prática recomendada para distingui-lo dos parâmetros do mesmo nome que se aplicam a diferentes serviços.

    ```xml
    WebFrontEnd_cpu: 1.5
    ```

2. Abra o arquivo **Service. YAML** do projeto de **WebFrontEnd** em **WebFrontEnd** > **recursos do serviço**.

    Observe que, na seção `resources:`, `cpu:` é definido como `"[parameters('WebFrontEnd_cpu')]"`. Se o projeto estiver sendo compilado para a nuvem, o valor de `'WebFrontEnd_cpu` será obtido dos **ambientes** > arquivo. YAML do **Cloud** > **parameters** e será `1.5`. Se o projeto estiver sendo criado para ser executado localmente, o valor será obtido dos **ambientes** > arquivo **local** > **Parameters. YAML** e será ' 0,5 '.

> [!Tip]
> Por padrão, o arquivo de parâmetro que é um par do arquivo Profile. YAML será usado para fornecer os valores para esse arquivo Profile. YAML.
> Por exemplo, ambientes > parâmetros de > de nuvem. YAML fornece os valores de parâmetro para ambientes > Cloud > Profile. YAML.
>
> Você pode substituir isso adicionando o seguinte ao arquivo Profile. YAML:`parametersFilePath=”relative or full path to the parameters file”` por exemplo, `parametersFilePath=”C:\MeshParms\CustomParameters.yaml”` ou `parametersFilePath=”..\CommonParameters.yaml”`

## <a name="modify-the-model"></a>Modificar o modelo

Para introduzir uma alteração de código, adicione uma propriedade `Category` à classe `ToDoItem` no arquivo `ToDoItem.cs`.

```csharp
public class ToDoItem
{
    public string Category { get; set; }
    ...
}
```

Em seguida, atualize o método `Load()`, no mesmo arquivo, para definir a categoria como uma cadeia de caracteres padrão:

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

O projeto `WebFrontEnd` é um aplicativo ASP.NET Core com uma página da Web que mostra itens de lista de tarefas pendentes. No projeto `WebFrontEnd`, abra `Index.cshtml` e adicione as duas linhas a seguir, indicadas abaixo, para exibir a categoria da tarefa:

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

Crie e execute o aplicativo para verificar se você vê uma nova coluna de categoria na página da Web que lista as tarefas.

## <a name="upgrade-the-app-from-visual-studio"></a>Atualizar o aplicativo do Visual Studio

Se você estiver fazendo uma atualização de código ou uma atualização de configuração (neste caso, estamos fazendo as duas), atualize seu aplicativo de malha Service Fabric no Azure clicando com o botão direito do mouse em **apldelistadetarefas** no Visual Studio e, em seguida, selecione **publicar...**

Em seguida, verá a caixa de diálogo **Publish Service Fabric Application** (Publicar Aplicação do Service Fabric).

Use o menu suspenso **perfil de destino** para selecionar o arquivo Profile. YAML a ser usado para essa implantação. Estamos atualizando o aplicativo na nuvem, portanto, selecionamos o **Cloud. YAML** na lista suspensa, que usará o valor `WebFrontEnd_cpu` de 1,0 definido nesse arquivo.

![Caixa de diálogo de publicação do Service Fabric Mesh do Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Selecione a conta e a subscrição do Azure. Defina o **local** para o local que você usou quando publicou originalmente o aplicativo de tarefas pendentes no Azure. Este artigo usou o **leste dos EUA**.

Defina o **grupo de recursos** para o grupo de recursos que você usou quando publicou originalmente o aplicativo de tarefas pendentes no Azure.

Defina o **registro de contêiner do Azure** para o nome do registro de contêiner do Azure que você criou quando publicou originalmente o aplicativo de tarefas pendentes no Azure.

Na caixa de diálogo publicar, pressione o botão **publicar** para atualizar o aplicativo de tarefas pendentes no Azure.

Monitore o progresso da atualização selecionando o painel de **Ferramentas do Service Fabric** na janela de **saída** do Visual Studio. 

Depois que a imagem for criada e enviada por push ao registro de contêiner do Azure, um link **para status** será exibido na saída na qual você pode clicar para monitorar a implantação no portal do Azure.

Depois que a atualização for concluída, o **Ferramentas do Service Fabric** saída exibirá o endereço IP e a porta do seu aplicativo na forma de uma URL.

```json
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Abra um browser e navegue para o URL para ver o site em execução no Azure. Agora você deve ver uma página da Web que contém uma coluna de categoria.

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Como atualizar um aplicativo de malha Service Fabric usando o Visual Studio

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Limpar os recursos do Service Fabric Mesh](service-fabric-mesh-tutorial-cleanup-resources.md)