---
title: Manipular modelos
description: Manipular modelos remotamente renderizados movendo-se, escalando rotativa e muito mais
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 003203ef1a25102f9fd3c50001603dbd5d33ce5a
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2020
ms.locfileid: "94565975"
---
# <a name="tutorial-manipulating-models"></a>Tutorial: Manipular modelos

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
>
> * Adicione limites visuais e de manipulação em torno de modelos remotamente renderizados
> * Mova-se, rode e escalone
> * Raycast com consultas espaciais
> * Adicione animações simples para objetos renderizados remotamente

## <a name="prerequisites"></a>Pré-requisitos

* Este tutorial baseia-se em [Tutorial: Interfaces e modelos personalizados.](../custom-models/custom-models.md)

## <a name="query-remote-object-bounds-and-apply-to-local-bounds"></a>Consultar limites de objeto remoto e aplicar-se aos limites locais

Para interagir com objetos remotos, precisamos de uma representação local para interagir primeiro. Os [limites dos objetos](../../../concepts/object-bounds.md) são úteis para uma manipulação rápida de um objeto remoto. Os limites remotos podem ser consultados a partir de ARR, utilizando a Entidade local como referência. Os limites são questionados depois de o modelo ter sido carregado para a sessão remota.

Os limites de um modelo são definidos pela caixa que contém todo o modelo - tal como o [**BoxCollider**](https://docs.unity3d.com/Manual/class-BoxCollider.html)da Unity , que tem um centro e tamanho definidos para os eixos x, y, z. Na verdade, usaremos o **BoxCollider** da Unidade para representar os limites do modelo remoto.

1. Crie um novo script no mesmo diretório que **o RemoteRenderedModel** e nomeie-o **RemoteBounds**.
1. Substitua o conteúdo do script pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System;
    using UnityEngine;

    [RequireComponent(typeof(BaseRemoteRenderedModel))]
    public class RemoteBounds : BaseRemoteBounds
    {
        //Remote bounds works with a specific remotely rendered model
        private BaseRemoteRenderedModel targetModel = null;

        private BoundsQueryAsync remoteBoundsQuery = null;

        private RemoteBoundsState currentBoundsState = RemoteBoundsState.NotReady;

        public override RemoteBoundsState CurrentBoundsState
        {
            get => currentBoundsState;
            protected set
            {
                if (currentBoundsState != value)
                {
                    currentBoundsState = value;
                    BoundsStateChange?.Invoke(value);
                }
            }
        }

        public override event Action<RemoteBoundsState> BoundsStateChange;

        public void Awake()
        {
            BoundsStateChange += HandleUnityEvents;
            targetModel = GetComponent<BaseRemoteRenderedModel>();

            targetModel.ModelStateChange += TargetModel_OnModelStateChange;
            TargetModel_OnModelStateChange(targetModel.CurrentModelState);
        }

        private void TargetModel_OnModelStateChange(ModelState state)
        {
            switch (state)
            {
                case ModelState.Loaded:
                    QueryBounds();
                    break;
                default:
                    BoundsBoxCollider.enabled = false;
                    CurrentBoundsState = RemoteBoundsState.NotReady;
                    break;
            }
        }

        // Create a query using the model entity
        private void QueryBounds()
        {
            //Implement me
        }

        // Check the result and apply it to the local Unity bounding box if it was successful
        private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
        {
            //Implement me
        }
    }
    ```

    > [!NOTE]
    > Se vir um erro no Estúdio Visual alegando que o *Recurso 'X' não está disponível em C# 6. Por favor, utilize a versão linguística 7.0 ou superior,* estes erros podem ser ignorados com segurança. Isto está relacionado com a Solução e geração de projetos da Unidade.

    Este script deve ser adicionado ao mesmo GameObject que o script que implementa  **o Modelo De BaseRemoteRendered**. Neste caso, isto significa **RemoteRenderedModel**. Semelhante aos scripts anteriores, este código inicial irá lidar com todas as alterações de estado, eventos e dados relacionados com limites remotos.

    Existem dois métodos para implementar: **QueryBounds** e **ProcessQueryResult**. **A QueryBounds** apanha os limites, e o **ProcessQueryResult** toma o resultado da consulta e aplica-o ao **BoxCollider** local.

    O método **DeryBounds** é simples: envie uma consulta para a sessão de renderização remota e ouça o `Completed` evento.

1. Substitua o método **QueryBounds** pelo seguinte método completo:

    ```csharp
    // Create a query using the model entity
    private void QueryBounds()
    {
        remoteBoundsQuery = targetModel.ModelEntity.QueryLocalBoundsAsync();
        CurrentBoundsState = RemoteBoundsState.Updating;
        remoteBoundsQuery.Completed += ProcessQueryResult;
    }
    ```

    **ProcessQueryResult** também é simples. Vamos verificar o resultado para ver se foi bem sucedido. Se sim, converta e aplique os limites devolvidos num formato que o **BoxCollider** possa aceitar.    

1. Substitua o método **ProcessQueryResult** pelo seguinte método concluído:

    ```csharp
    // Check the result and apply it to the local Unity bounding box if it was successful
    private void ProcessQueryResult(BoundsQueryAsync remoteBounds)
    {
        if (remoteBounds.IsRanToCompletion)
        {
            var newBounds = remoteBounds.Result.toUnity();
            BoundsBoxCollider.center = newBounds.center;
            BoundsBoxCollider.size = newBounds.size;
            BoundsBoxCollider.enabled = true;
            CurrentBoundsState = RemoteBoundsState.Ready;
        }
        else
        {
            CurrentBoundsState = RemoteBoundsState.Error;
        }
    }
    ```

Agora, quando o script **RemoteBounds** for adicionado ao mesmo objeto de jogo que o **RemoteRenderedModel** , um **BoxCollider** será adicionado se necessário e quando o modelo atingir o seu `Loaded` estado, os limites serão automaticamente questionados e aplicados ao **BoxCollider.**

1. Utilizando o **TestModel** GameObject criado anteriormente, adicione o componente **RemoteBounds.**
1. Confirme que o guião é adicionado.

     ![Adicionar componente RemoteBounds](./media/remote-bounds-script.png)

1. Execute novamente a aplicação. Pouco depois de o modelo carregar, verá os limites do objeto remoto. Verá algo como os valores abaixo:

     ![Screenshot que mostra o exemplo dos limites do objeto remoto.](./media/updated-bounds.png)

Agora temos um **BoxCollider** local configurado com limites precisos no objeto unidade. Os limites permitem visualização e interação usando as mesmas estratégias que usaríamos para um objeto renderizado localmente. Por exemplo, scripts que alteram a Transformação, física, e muito mais.

## <a name="move-rotate-and-scale"></a>Mova-se, rode e escalone  

Mover, rodar e escalar objetos remotamente renderizados funciona como qualquer outro objeto de Unidade. O **RemoteRenderingCoordinator,** no seu `LateUpdate` método, está a convocar `Update` a sessão atualmente ativa. Parte do que `Update` faz é sincronizar a entidade modelo local transforma-se com os seus homólogos remotos. Para mover, rodar ou escalar um modelo renderizado remotamente, basta mover,rodar ou escalar a transformação do GameObject representando o modelo remoto. Aqui, vamos modificar a transformação do GameObject que tem o script **RemoteRenderedModel** ligado a ele.

Este tutorial está a usar o MRTK para interação com os objetos. A maior parte da implementação específica do MRTK para mover, rodar e escalar um objeto está fora do âmbito deste tutorial. Existe um controlador de visualização de modelos que vem pré-configurado dentro do **AppMenu,** no menu **Model Tools.**

1. Certifique-se de que o **TestModel** GameObject criado anteriormente está em cena.
1. Certifique-se de que o **prefácio appMenu** está no local.
1. Prima o botão De reprodução da Unidade para reproduzir a cena e abra o menu **Model Tools** dentro do **AppMenu**.
![Ver controlador](./media/model-with-view-controller.png)

O **AppMenu** tem um sub menu **Model Tools** que implementa um controlador de vista para encadernação com o modelo. Quando o GameObject contiver um componente **RemoteBounds,** o controlador de visualização adicionará um componente [**BoundingBox,**](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_BoundingBox.html) que é um componente MRTK que torna uma caixa de delimitação em torno de um objeto com um **BoxCollider**. Um [**ObjectManipulator,**](https://microsoft.github.io/MixedRealityToolkit-Unity/version/releases/2.3.0/api/Microsoft.MixedReality.Toolkit.Experimental.UI.ObjectManipulator.html?q=ObjectManipulator)responsável pelas interações manuais. Estes scripts combinados permitir-nos-ão mover, rodar e escalar o modelo renderizado remotamente.

1. Mova o rato para o painel de jogo e clique no seu interior para lhe dar o foco.
1. Utilizando a [simulação manual do MRTK,](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/InputSimulation/InputSimulationService.html#hand-simulation)prima e segure a tecla Shift esquerda.
1. Guie a mão simulada para que o raio de mão esteja apontando para o modelo de teste.

    ![Raio de mão pontiagudo](./media/handray-engine.png)

1. Segure o clique esquerdo e arraste o modelo para movê-lo.

Deve ver o conteúdo prestado remotamente mover-se junto com a caixa de delimitação. Pode notar algum atraso ou desfasamento entre a caixa de delimitação e o conteúdo remoto. Este atraso dependerá da sua latência na internet e largura de banda.

## <a name="ray-cast-and-spatial-queries-of-remote-models"></a>Ray elenco e consultas espaciais de modelos remotos

Um colisor de caixa em torno de modelos é adequado para interagir com todo o modelo, mas não detalhado o suficiente para interagir com partes individuais de um modelo. Para resolver isto, podemos utilizar [o fundição de raios remotos.](../../../overview/features/spatial-queries.md#ray-casts) O fundição de raios remotos é uma API fornecida pela Azure Remote Rendering para lançar raios na cena remota e retorno resultados de sucesso localmente. Esta técnica pode ser usada para selecionar entidades infantis de um grande modelo ou obter informações de resultados de sucesso como posição, normal superfície e distância.

O modelo de teste tem uma série de sub-entidades que podem ser consultadas e selecionadas. Por enquanto, a seleção irá desausar o nome da Entidade selecionada para a Consola de Unidade. Verifique o capítulo [materiais, iluminação e efeitos](../materials-lighting-effects/materials-lighting-effects.md#highlighting-and-outlining) para realçar a Entidade selecionada.

Primeiro, vamos criar um invólucro estático em torno das consultas de raios remotos. Este script aceitará uma posição e direção no espaço Unidade, convertê-lo-á para os tipos de dados aceites pelo elenco de raios remotos e devolverá os resultados. O script fará uso da `RayCastQueryAsync` API.

1. Crie um novo script chamado **RemoteRayCaster** e substitua o seu conteúdo pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    /// <summary>
    /// Wraps the Azure Remote Rendering RayCast queries to easily send requests using Unity data types
    /// </summary>
    public class RemoteRayCaster
    {
        public static double maxDistance = 30.0;

        public static async Task<RayCastHit[]> RemoteRayCast(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            if(RemoteRenderingCoordinator.instance.CurrentCoordinatorState == RemoteRenderingCoordinator.RemoteRenderingState.RuntimeConnected)
            {
                var rayCast = new RayCast(origin.toRemotePos(), dir.toRemoteDir(), maxDistance, hitPolicy);
                return await RemoteRenderingCoordinator.CurrentSession.Actions.RayCastQueryAsync(rayCast).AsTask();
            }
            else
            {
                return new RayCastHit[0];
            }
        }

        public static async Task<Entity[]> RemoteRayCastEntities(Vector3 origin, Vector3 dir, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            var hits = await RemoteRayCast(origin, dir, hitPolicy);
            return hits.Select(hit => hit.HitEntity).Where(entity => entity != null).ToArray();
        }
    }
    ```

    > [!NOTE]
    > A Unidade tem uma classe chamada [**RaycastHit,**](https://docs.unity3d.com/ScriptReference/RaycastHit.html)e a Azure Remote Rendering tem uma classe chamada [**RayCastHit**](/dotnet/api/microsoft.azure.remoterendering.raycasthit). O maiúscula **C** é uma diferença importante para evitar erros de compilação.

    **O RemoteRayCaster** fornece um ponto de acesso comum para lançar raios remotos na sessão atual. Para ser mais específico, implementaremos um ponteiro MRTK em seguida. O script implementará a `IMixedRealityPointerHandler` interface, que dirá ao MRTK que queremos que este script ouça eventos [de Ponto de Realidade Mista.](https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/Input/Pointers.html)

1. Crie um novo script chamado **RemoteRayCastPointerHandler** e substitua o código pelo seguinte código:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.

    using Microsoft.Azure.RemoteRendering;
    using Microsoft.MixedReality.Toolkit.Input;
    using System;
    using System.Linq;
    using System.Threading.Tasks;
    using UnityEngine;

    public class RemoteRayCastPointerHandler : BaseRemoteRayCastPointerHandler, IMixedRealityPointerHandler
    {
        public UnityRemoteEntityEvent OnRemoteEntityClicked = new UnityRemoteEntityEvent();

        public override event Action<Entity> RemoteEntityClicked;

        public void Awake()
        {
            // Forward events to Unity events
            RemoteEntityClicked += (entity) => OnRemoteEntityClicked?.Invoke(entity);
        }

        public async void OnPointerClicked(MixedRealityPointerEventData eventData)
        {
            if (RemoteEntityClicked != null) //Ensure someone is listening before we do the work
            {
                var firstHit = await PointerDataToRemoteRayCast(eventData.Pointer);
                if (firstHit.success)
                    RemoteEntityClicked.Invoke(firstHit.hit.HitEntity);
            }
        }

        public void OnPointerDown(MixedRealityPointerEventData eventData) { }

        public void OnPointerDragged(MixedRealityPointerEventData eventData) { }

        public void OnPointerUp(MixedRealityPointerEventData eventData) { }

        private async Task<(bool success, RayCastHit hit)> PointerDataToRemoteRayCast(IMixedRealityPointer pointer, HitCollectionPolicy hitPolicy = HitCollectionPolicy.ClosestHit)
        {
            RayCastHit hit;
            var result = pointer.Result;
            if (result != null)
            {
                var endPoint = result.Details.Point;
                var direction = pointer.Rays[pointer.Result.RayStepIndex].Direction;
                Debug.DrawRay(endPoint, direction, Color.green, 0);
                hit = (await RemoteRayCaster.RemoteRayCast(endPoint, direction, hitPolicy)).FirstOrDefault();
            }
            else
            {
                hit = new RayCastHit();
            }
            return (hit.HitEntity != null, hit);
        }
    }
    ```

O método **do RemoteRayCastPointerHandler** `OnPointerClicked` é chamado por MRTK quando um ponteiro 'clica' num colisor, como o nosso colisor de caixas. Depois disso, `PointerDataToRemoteRayCast` é chamado a converter o resultado do ponteiro em um ponto e direção. Este ponto e direção são então usados para lançar um raio remoto na sessão remota.

![Limites atualizados](./media/raycast-local-remote.png)

Enviar pedidos de ray casting em clique é uma estratégia eficiente para consultar objetos remotos. No entanto, não é uma experiência de utilizador ideal porque o cursor colide com o colisor da caixa, não com o próprio modelo.

Também pode criar um novo ponteiro MRTK que lança os seus raios na sessão remota com mais frequência. Embora esta seja uma abordagem mais complexa, a experiência do utilizador seria melhor. Esta estratégia está fora do âmbito deste tutorial, mas um exemplo desta abordagem pode ser visto na App Showcase, encontrada no [repositório de amostras ARR.](https://github.com/Azure/azure-remote-rendering/tree/master/Unity/Showcase)

Quando um elenco de raios é concluído com sucesso no **RemoteRayCastPointerHandler,** o hit `Entity` é emitido a partir do evento `OnRemoteEntityClicked` Unidade. Para responder a esse evento, criaremos um script de ajuda que aceite o `Entity` e execute uma ação nele. Vamos começar por fazer com que o guião imprima o nome do `Entity` registo de depurrão.

1. Crie um novo script chamado **RemoteEntityHelper** e substitua os seus conteúdos pelo seguinte:

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Azure.RemoteRendering.Unity;
    using UnityEngine;
    
    public class RemoteEntityHelper : MonoBehaviour
    {
        public void EntityToDebugLog(Entity entity)
        {
            Debug.Log(entity.Name);
        }
    }
    ```

2. No **TestModel** GameObject criado anteriormente, adicione tanto o componente **RemoteRayCastPointerHandler** como o componente **RemoteEntityHelper.**
1. Atribua o `EntityToDebugLog` método ao `OnRemoteEntityClicked` evento. Quando o tipo de saída do evento e o tipo de entrada do método coincidirem, podemos usar a ligação dinâmica do evento da Unidade, que passará automaticamente o valor do evento para o método.
    1. Criar um novo campo de retorno ![ Adicionar callback](./media/add-callback-remote-entity-clicked.png)
    1. Arraste o componente **do Helper de Entidade Remota** no campo Object, para fazer referência ao objeto de atribuição de gameObject ![ pai](./media/assign-object.png)
    1. Atribua o `EntityToDebugLog` como retorno de retorno Atribuir ![ retorno](./media/remote-entity-event.png)
1. Pressione o editor de unidade para iniciar a cena, ligue-se a uma sessão remota e carregue o modelo de teste.
1. Utilizando a simulação manual do MRTK, pressione e segure a tecla Shift esquerda.
1. Guie a mão simulada para que o raio de mão esteja apontando para o modelo de teste.
1. Clique longo para simular um toque de ar, executando o `OnPointerClicked` evento.
1. Observe a Consola de Unidade para obter uma mensagem de registo com o nome da entidade infantil selecionada. Por exemplo: ![ Exemplo de entidade infantil](./media/child-entity-example.png)

## <a name="synchronizing-the-remote-object-graph-into-the-unity-hierarchy"></a>Sincronizar o gráfico de objeto remoto na hierarquia da Unidade

Até agora, só vimos um único GameObject local representando todo o modelo. Isto funciona bem para a renderização e manipulação de todo o modelo. No entanto, se quisermos aplicar efeitos ou manipular sub-entidades específicas, teremos de criar GameObjects locais para representar essas entidades. Primeiro, podemos explorar manualmente no modelo de teste.

1. Inicie a cena e carregue o modelo de teste.
1. Expanda as crianças do **TestModel** GameObject na hierarquia da Unidade e selecione o **TestModel_Entity** GameObject.
1. No Inspetor, clique no botão *Mostrar Crianças.*
![Mostrar às crianças](./media/show-remote-children.png)
1. Continue a expandir as crianças na hierarquia e a clicar no *Show Children* até que seja mostrada uma grande lista de crianças.
![Todas as crianças](./media/test-model-children.png)

Uma lista de dezenas de entidades irá agora povoar a hierarquia. A seleção de um deles mostrará os `Transform` componentes e componentes no `RemoteEntitySyncObject` Inspetor. Por predefinição, cada entidade não é sincronizada automaticamente em cada frame, pelo que as alterações locais ao `Transform` servidor não são sincronizadas. Podes verificar *o Sync Every Frame* e depois mover, escalar ou rodar a transformação na vista Cena, não verás o modelo renderizado na vista de cena, vês a vista do Jogo para ver a posição e a atualização visual do modelo.

O mesmo processo pode ser feito programáticamente e é o primeiro passo para modificar entidades remotas específicas.

1. Modificar o script **RemoteEntityHelper** para conter também o seguinte método:

    ```csharp
    public void MakeSyncedGameObject(Entity entity)
    {
        var entityGameObject = entity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);
        var sync = entityGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;
    }
    ```

1. Adicione uma chamada adicional ao evento **RemoteRayCastPointerHandler,** `OnRemoteEntityClicked` definindo-o para `MakeSyncedGameObject` .
![Retorno adicional](./media/additional-callback.png)
1. Utilizando a simulação manual do MRTK, pressione e segure a tecla Shift esquerda.
1. Guie a mão simulada para que o raio de mão esteja apontando para o modelo de teste.
1. Clique longo para simular um toque de ar, executando o `OnPointerClicked` evento.
1. Verifique e expanda a Hierarquia para ver um novo objeto infantil, representando a entidade clicada.
![Representação do GameObject](./media/gameobject-representing-entity.png)
1. Após o teste, remova a chamada para `MakeSyncedGameObject` , uma vez que vamos incorporar isto como parte de outros efeitos mais tarde.

> [!NOTE]
> Sincronizar cada quadro só é necessário quando é necessário sincronizar os dados de transformação. Há algumas despesas gerais para a sincronização de transformações, por isso deve ser usada com moderação.

Criar um caso local e torná-lo automaticamente sincronizado é o primeiro passo na manipulação de subinsícopas. As mesmas técnicas que usamos para manipular o modelo como um todo também podem ser usadas nas sub-entidades. Por exemplo, depois de criar uma instância local sincronizada de uma entidade, você poderia consultar os seus limites e adicionar manipuladores de manipulação para permitir que ele fosse movido pelos raios de mão do utilizador.

## <a name="next-steps"></a>Passos seguintes

Agora pode manipular e interagir com os seus modelos remotamente renderizados! No próximo tutorial, cobriremos materiais modificados, alterando a iluminação e aplicando efeitos em modelos renderizados remotamente.

> [!div class="nextstepaction"]
> [Seguinte: Refinação de materiais, iluminação e efeitos](../materials-lighting-effects/materials-lighting-effects.md)