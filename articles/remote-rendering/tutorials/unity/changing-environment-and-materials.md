---
title: Mudança do ambiente e dos materiais
description: Tutorial que mostra como modificar o mapa do céu e materiais de objeto numa cena de Unidade.
author: jakrams
ms.author: jakras
ms.date: 02/03/2020
ms.topic: tutorial
ms.openlocfilehash: 1354ac3cf2c6fc716ac72ae339928fa49171893e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80679619"
---
# <a name="tutorial-changing-the-environment-and-materials"></a>Tutorial: Mudar o ambiente e os materiais

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Mude o mapa ambiental de uma cena.
> * Modificar os parâmetros do material.
> * Carregue texturas personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial assume que está familiarizado com [tutorial: Trabalhar com entidades remotas em Unidade.](working-with-remote-entities.md) No entanto, basta um projeto de Unidade com o qual pode ligar-se a sessões e carregar um modelo, como mostra o [Tutorial: Criar um projeto de Unidade do zero](project-setup.md).

> [!TIP]
> O [repositório](https://github.com/Azure/azure-remote-rendering) de amostras ARR contém projetos de unidade preparados para todos os tutoriais na pasta *Unidade,* que você pode usar como referência.

## <a name="change-the-environment-map"></a>Alterar o mapa do ambiente

A Renderização Remota Azure suporta o uso de caixas de céu (por [vezes](../../overview/features/sky.md) também chamadas de "mapas ambientais") para simular iluminação ambiente. Isto é especialmente útil quando os seus objetos usam *[renderização fisicamente baseada,](../../overview/features/pbr-materials.md)* como os nossos modelos de amostra. Arr também vem com uma variedade de texturas de céu embutidos, que vamos usar neste tutorial.

Crie um novo script chamado **RemoteSky** e adicione-o a um novo GameObject. Abra o ficheiro script e substitua-o pelo seguinte código:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

public class RemoteSky : MonoBehaviour
{
    private int skyIndex = 0;

    private LoadTextureFromSASParams[] builtIns =
    {
        new LoadTextureFromSASParams("builtin://Autoshop", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://BoilerRoom", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://ColorfulStudio", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Hangar", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://IndustrialPipeAndValve", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Lebombo", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SataraNight", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://SunnyVondelpark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://Syferfontein", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://TearsOfSteelBridge", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://VeniceSunset", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WhippleCreekRegionalPark", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://WinterRiver", TextureType.CubeMap),
        new LoadTextureFromSASParams("builtin://DefaultSky", TextureType.CubeMap)
    };

    public async void ToggleSky()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        skyIndex = (skyIndex + 1) % builtIns.Length;

        var texture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(builtIns[skyIndex]).AsTask();

        Debug.Log($"Switching sky to: {builtIns[skyIndex].TextureId}");

        var settings = RemoteManagerUnity.CurrentSession.Actions.SkyReflectionSettings;

        settings.SkyReflectionTexture = texture;
    }

    private void OnGUI()
    {
        if (RemoteManagerUnity.IsConnected)
        {
            if (GUI.Button(new Rect(10, Screen.height - 50, 175, 30), "Toggle Sky"))
            {
                ToggleSky();
            }
        }
    }
}
```

Note que `LoadTextureFromSASAsync` a variante é usada acima porque as texturas incorporadas são carregadas. Em caso de carregamento a partir de `LoadTextureAsync` [armazenamentos de blob ligados,](../../how-tos/create-an-account.md#link-storage-accounts)utilize a variante. Um exemplo de como isto funciona para modelos pode ser encontrado na secção de carregamento de [modelos](../../concepts/models.md#loading-models).

Quando executar o código e alternar através dos mapas do céu, irá notar uma iluminação drasticamente diferente no seu modelo. No entanto, o fundo permanecerá preto e você não pode ver a textura real do céu. Isto é intencional, já que renderizar um fundo seria distrair com um dispositivo de Realidade Aumentada. Numa aplicação adequada, você deve usar texturas do céu que são semelhantes ao seu ambiente real, pois isso ajudará a fazer os objetos parecerem mais reais.

## <a name="modify-materials"></a>Modificar materiais

No tutorial anterior, usamos componentes de [sobreposição](../../overview/features/override-hierarchical-state.md) de estado para alterar a cor da tonalidade dos objetos selecionados. Agora queremos obter um efeito semelhante, mas fazê-lo modificando o [material](../../concepts/materials.md) de um objeto.

Primeiro precisamos de um guião para escolher objetos, como fizemos [no segundo tutorial.](working-with-remote-entities.md) Se ainda não tens um guião **do RemoteRaycaster,** cria-o agora. Substitua o seu conteúdo pelo seguinte código:

```csharp
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;
using System.Collections;
using System.Collections.Generic;
using System.Threading.Tasks;
using UnityEngine;

[RequireComponent(typeof(ARRServiceUnity))]
public class RemoteRaycaster : MonoBehaviour
{
    public double MaxDistance = 30.0;

    private ARRServiceUnity arrService = null;

    private void Awake()
    {
        arrService = GetComponent<ARRServiceUnity>();
    }

    private async Task<Entity> RemoteRayCast(Vector3 origin, Vector3 dir)
    {
        Entity entity = null;

        var raycast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), MaxDistance, HitCollectionPolicy.ClosestHit);

        var hits = await arrService.CurrentActiveSession.Actions.RayCastQueryAsync(raycast).AsTask();

        if (hits != null)
        {
            foreach (var hit in hits)
            {
                var hitEntity = hit.HitEntity;
                if (hitEntity == null)
                {
                    continue;
                }

                entity = hitEntity;
                break;
            }
        }

        return entity;
    }

    private RemoteModelEntity focusedModel = null;

    public async void Raycast(Vector3 origin, Vector3 direction)
    {
        var remoteEntity = await RemoteRayCast(origin, direction);

        if (focusedModel != null)
        {
            if (focusedModel.Entity == remoteEntity)
            {
                // picked the same object as before
                return;
            }

            ClearFocus();
        }

        if (remoteEntity == null)
        {
            // picked no object at all
            return;
        }

        // get the instance of a Unity GameObject for the ARR entity
        var entityGO = remoteEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        // ensure the game object has the RemoteModelEntity component
        focusedModel = entityGO.GetComponent<RemoteModelEntity>();
        if (focusedModel == null)
        {
            focusedModel = entityGO.AddComponent<RemoteModelEntity>();
        }

        focusedModel.SetFocus(true);
    }

    private void ClearFocus()
    {
        focusedModel.SetFocus(false);
        CleanHierarchy(focusedModel.gameObject);
        focusedModel = null;
    }

    private void CleanHierarchy(GameObject focusedGO)
    {
        var sync = focusedGO?.GetComponent<RemoteEntitySyncObject>();
        if (sync == null || !sync.IsEntityValid)
        {
            return;
        }

        sync.Entity.DestroyGameObject(EntityExtensions.DestroyGameObjectFlags.DestroyEmptyParents | EntityExtensions.DestroyGameObjectFlags.KeepRemoteRoot);
    }

    private void Update()
    {
        if (!RemoteManagerUnity.IsConnected)
        {
            return;
        }

        Vector3 position = Input.mousePresent ? Input.mousePosition : new Vector3(Screen.width / 2, Screen.height / 2, 0);
        var ray = Camera.main.ScreenPointToRay(position);

        Raycast(ray.origin, ray.direction);
    }
}
```

Adicione o componente ao seu objeto de jogo *RemoteRendering.* É responsável por recolher objetos sob o rato e adicionar componentes *RemoteModelEntity* aos objetos colhidos. Esta classe de componentes é onde implementamos a funcionalidade real de mudança de material.

Se ainda não tiver um script **RemoteModelEntity,** crie-o e substitua o seu conteúdo por este código:

```csharp
using UnityEngine;
using Microsoft.Azure.RemoteRendering;
using Microsoft.Azure.RemoteRendering.Unity;

// to prevent namespace conflicts
using ARRTexture = Microsoft.Azure.RemoteRendering.Texture;

public class RemoteModelEntity : MonoBehaviour
{
    private Color HighlightColor = new Color(1.0f, 0.4f, 0.1f, 1.0f);

    public Entity Entity => localSyncObject != null ? localSyncObject?.Entity : null;
    private RemoteEntitySyncObject localSyncObject = null;
    private ARRMeshComponent meshComponent = null;
    private Color4 originalColor;

    public void OnEnable()
    {
        localSyncObject = GetComponent<RemoteEntitySyncObject>();

        meshComponent = GetComponent<ARRMeshComponent>();
        if (meshComponent == null)
        {
            var mesh = localSyncObject.Entity.FindComponentOfType<MeshComponent>();
            if (mesh != null)
            {
                gameObject.BindArrComponent<ARRMeshComponent>(mesh);
                meshComponent = gameObject.GetComponent<ARRMeshComponent>();
            }
        }
        meshComponent.enabled = true;
    }

    public void SetFocus(bool on)
    {
        SetMaterialColor(on);
    }

    private void SetMaterialColor(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        // get the list of materials from the picked mesh
        // we only modify the first material; depending on how the model is authored, this may
        // work exactly as desired, or not ;-)
        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        // there are different types of materials, cast as necessary
        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            if (on)
            {
                // modify the color of the material
                originalColor = materialColor.AlbedoColor;
                materialColor.AlbedoColor = HighlightColor.toRemoteColor4();
            }
            else
            {
                materialColor.AlbedoColor = originalColor;
            }
        }
    }
}
```

Quando executa este código, os objetos de que paira com o rato ficam em destaque. O efeito é semelhante ao que fizemos no tutorial 2, mas a forma como é alcançado é diferente. Aqui temos a lista de materiais no objeto escolhido e depois modificamos o primeiro para ter uma cor albedo diferente.

> [!IMPORTANT]
> Tenha em atenção que se este método realça as partes corretas de um modelo, depende da forma como um modelo é autor. Funcionará perfeitamente, se cada objeto usar exatamente um material. No entanto, se o modelo não tiver uma relação 1:1 entre peças e materiais, o código ingénuo acima não fará a coisa certa.

## <a name="use-a-different-texture"></a>Use uma textura diferente

[Texturas](../../concepts/textures.md) são tipicamente parte de um modelo de origem. Durante a conversão do [modelo,](../../quickstarts/convert-model.md)todas as texturas são convertidas para o formato de tempo de execução necessário e embaladas no ficheiro final do modelo. Para substituir uma textura no tempo de funcionação, é necessário guardá-la em [formato de ficheiro DDS](https://en.wikipedia.org/wiki/DirectDraw_Surface) e carregá-la para o armazenamento de blob Azure. Consulte [este guia de arranque rápido](../../quickstarts/convert-model.md) para como criar um recipiente de blob Azure. Uma vez que tenha um recipiente de bolha, pode abri-lo no Azure Storage Explorer e carregar o seu ficheiro através de arrasto e gota.

No lado do tempo de execução, você pode abordar um ativo de textura no armazenamento de blob de duas maneiras distintas:

* Textura de endereço pelo seu SAS URI. Para isso, clique à direita no ficheiro carregado e selecione " Obter Assinatura de**Acesso Partilhado...**" a partir do menu de contexto. Utilize este SAS `LoadTextureFromSASAsync` URI com a variante de função (ver código de amostra abaixo).
* Abordar a textura por parâmetros de armazenamento blob diretamente, no caso de o [armazenamento blob estar ligado à conta](../../how-tos/create-an-account.md#link-storage-accounts). A função de `LoadTextureAsync`carregamento relevante neste caso é .

Agora abra o script **RemoteModelEntity,** adicione o seguinte código e remova funções duplicadas:

```csharp
    private string textureFile = ""; //<SAS URI for your texture>
    private ARRTexture originalTexture = null;

    private async void SetMaterialTexture(bool on)
    {
        if (!meshComponent.IsComponentValid)
        {
            return;
        }

        var materials = meshComponent.RemoteComponent.Mesh.Materials;
        if (materials == null || materials.Count == 0)
        {
            return;
        }

        ARRTexture newTexture = originalTexture;

        if (on)
        {
            var textureParams = new LoadTextureFromSASParams(textureFile, TextureType.Texture2D);

            newTexture = await RemoteManagerUnity.CurrentSession.Actions.LoadTextureFromSASAsync(textureParams).AsTask();
        }

        if (materials[0].MaterialSubType == MaterialType.Color)
        {
            var materialColor = materials[0] as ColorMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
        else if (materials[0].MaterialSubType == MaterialType.Pbr)
        {
            var materialColor = materials[0] as PbrMaterial;

            originalTexture = materialColor.AlbedoTexture;
            materialColor.AlbedoTexture = newTexture;
        }
    }

    public void SetFocus(bool on)
    {
        if (string.IsNullOrEmpty(textureFile))
        {
            SetMaterialColor(on);
        }
        else
        {
            SetMaterialTexture(on);
        }
    }
```

Executa este código e paira sobre o seu modelo. Se o seu modelo tiver coordenadas UV adequadas, deve ver a sua textura aparecer. Caso contrário, só poderá notar uma mudança de cor.

## <a name="next-steps"></a>Passos seguintes

Isto conclui a nossa série de introdução sobre como usar a Renderização Remota Azure com unidade. Como próximo passo deve familiarizar-se com alguns conceitos fundamentais de ARR, tais como [sessões,](../../concepts/sessions.md) [entidades](../../concepts/entities.md)e [modelos](../../concepts/models.md) para construir um entendimento mais profundo. Existem também várias funcionalidades como [luzes,](../../overview/features/lights.md) [renderização](../../overview/features/outlines.md)de contornos, [sobreposições hierárquicas](../../overview/features/override-hierarchical-state.md)do estado, e [materiais](../../concepts/materials.md) que deve explorar mais detalhadamente.

> [!div class="nextstepaction"]
> [Objetos e componentes do jogo de unidade](../../how-tos/unity/objects-components.md)
