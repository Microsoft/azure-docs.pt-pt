---
title: 'Quickstart: Criar um modelo de âncoras de objeto para ser usado numa aplicação'
description: Neste arranque rápido, aprende-se a criar um modelo de Âncoras de Objetos a partir de um modelo 3D.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/22/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 69d23b9d02eb176a2e42985ef5c3673e83d9bb7e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102607905"
---
# <a name="quickstart-create-an-object-anchors-model-from-a-3d-model"></a>Quickstart: Criar um modelo de âncora de objetos a partir de um modelo 3D

Azure Object Anchors é um serviço de nuvem gerido que converte modelos 3D em modelos de IA que permitem experiências de realidade mista conscientes de objetos para os HoloLens. Este quickstart cobre como criar um modelo de Âncora de Objetos a partir de um modelo 3D utilizando o C#/.NET Core SDK.

Vai aprender a:

> [!div class="checklist"]
> * Criar uma conta de Âncoras de Objetos
> * Converter um modelo 3D para criar um modelo de Âncoras de Objetos

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

* Uma máquina Windows com <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>.
* <a href="https://git-scm.com" target="_blank">Git para Windows</a>.
* O <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1">.NET Core 3.1 SDK</a>.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-object-anchors-account"></a>Criar uma conta de Âncoras de Objetos

Primeiro, tem de criar uma conta com o serviço 'Âncoras de Objectos'.

1. Vá ao [portal Azure](https://portal.azure.com/) e selecione **Criar um recurso**.

   :::image type="content" source="./media/create-aoa-resource-1.png" alt-text="Criar um novo recurso":::

2. Procure o recurso **'Âncoras de** Objectos'.

   Procure por "Âncoras de Objetos".

   :::image type="content" source="./media/create-aoa-resource-2.png" alt-text="Selecione o recurso 'Âncoras de Objecto'":::

   No recurso **'Âncoras de Objecto'** nos resultados da pesquisa, selecione **Criar âncoras de objetos de >**.

   :::image type="content" source="./media/create-aoa-resource-3.png" alt-text="Criar um recurso de âncoras de objeto":::

3. Na caixa de diálogo de **conta 'Âncoras de Objecto':**
    * Insira um nome de recurso único.
    * Selecione a subscrição a que pretende anexar o recurso.
    * Criar ou utilizar um grupo de recursos existente.
    * Selecione a região em que gostaria que o seu recurso existisse.

    :::image type="content" source="./media/create-aoa-resource-4.png" alt-text="Insira detalhes da conta de recursos de Âncoras de Objeto":::

    Selecione **Criar** para começar a criar o recurso.

4. Depois de criar o recurso, selecione **Aceder ao recurso**.

   :::image type="content" source="./media/create-aoa-resource-5.png" alt-text="Ir para recurso":::

5. Na página geral:

   Tome nota do Domínio da **Conta.** Precisará dela mais tarde.

   :::image type="content" source="./media/create-aoa-resource-6.1.png" alt-text="Copie o domínio da conta para o seu recurso 'Âncoras de Objecto'":::

   Tome nota do **ID da Conta.** Precisará dela mais tarde.

   :::image type="content" source="./media/create-aoa-resource-6.2.png" alt-text="Copie o ID de conta para o seu recurso De âncora de objeto":::

   Vá à página **'Chaves de Acesso'** e tome nota da **chave principal**. Precisará dela mais tarde.

   :::image type="content" source="./media/create-aoa-resource-7.png" alt-text="Copie a chave de conta para o seu recurso 'Âncoras de Objecto'":::

## <a name="get-the-sample-project"></a>Obtenha o projeto de amostra

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

## <a name="convert-a-3d-model"></a>Converter um modelo 3D

Agora, podes ir em frente e converter o teu modelo 3D.

1. Aberto `quickstarts/conversion/Conversion.sln` no Estúdio Visual. Esta solução contém um projeto de consola C#.

2. Abra o `Configuration.cs` ficheiro localizado na raiz do projeto e substitua os `set-me` valores nos seguintes campos:

   | Campo         | Descrição                                                         |
   |---------------|---------------------------------------------------------------------|
   | ContaDomínio | O domínio da **conta** da conta 'Âncoras de Objecto' criada acima. |
   | AccountId     | O **ID** de conta da conta 'Âncoras de Objecto' criada acima.     |
   | ContaKey    | A **chave primária** da conta 'Âncoras de Objectos' criada acima     |

   Há quatro campos adicionais que precisam de ser verificados:

    | Campo                    | Descrição                       |
    | ---                      | ---                               |
    | InputAssetPath           | O caminho absoluto para um modelo 3D na sua máquina local. Os formatos de ficheiros suportados `fbx` `ply` `obj` são, `glb` , , e `gltf` . |
    | AssetDimensionunit       | A unidade de medição do seu modelo 3D. Todas as unidades de medição suportadas podem ser acedidas através da `Azure.MixedReality.ObjectAnchors.Conversion.AssetLengthUnit` enumeração. |
    | Gravidade                  | A direção do vetor de gravidade do modelo 3D. Este vetor 3D dá a direção descendente no sistema de coordenadas do seu modelo. Por exemplo, se o negativo `y` representar a direção descendente no espaço 3D do modelo, este valor seria `Vector3(0.0f, -1.0f, 0.0f)` . |

3. Construa e execute o projeto para carregar o seu modelo 3D, registar um novo trabalho de conversão com o serviço, e esperar que seja concluído. Uma vez concluído o trabalho, o modelo 'Âncoras de Objectos' será descarregado ao lado do ficheiro especificado no `InputAssetPath` . Deve ver algo semelhante à seguinte saída da consola:

   ```shell
    Asset   : ***********
    Gravity : ***********
    Unit    : ***********
    Attempting to upload asset...
    Attempting to create asset conversion job...
    Successfully created asset conversion job. Job ID: ***********
    Waiting for job completion...

    Asset conversion job completed successfully.
    Attempting to download result as '***********'...
    Success!
   ```

   Tome nota da **identificação** do trabalho para referência futura. Pode ser útil quando se depura ou resolva problemas.

4. Uma vez concluído o trabalho com sucesso, deverá consultar um ficheiro com o formato `<Model-Filename-Without-Extension>_<JobID>.ou` no local de saída especificado. Por exemplo, se o seu nome de ficheiro modelo 3D for `chair.ply` e o seu ID de trabalho for `00000000-0000-0000-0000-000000000000` o nome de ficheiro que as saídas de serviço serão `chair_00000000-0000-0000-0000-000000000000.ou` .

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, criou uma conta De Âncoras de Objetos e converteu um modelo 3D para criar um modelo de Âncoras de Objetos. Para aprender a integrar este modelo com o Object Anchors SDK na sua aplicação de realidade mista, continue com qualquer um dos seguintes artigos:

> [!div class="nextstepaction"]
> [Unity HoloLens](get-started-unity-hololens.md)

> [!div class="nextstepaction"]
> [HoloLens de unidade com MRTK](get-started-unity-hololens-mrtk.md)

> [!div class="nextstepaction"]
> [HoloLens DirectX](get-started-hololens-directx.md)
