---
title: Criar & localizar âncoras em Swift
description: Explicação aprofundada de como criar e localizar âncoras usando âncoras espaciais Azure em Swift.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: f72e648d8f7cba0af01e7f87827d38368dba698d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74277325"
---
# <a name="how-to-create-and-locate-anchors-using-azure-spatial-anchors-in-swift"></a>Como criar e localizar âncoras usando âncoras espaciais Azure em Swift

> [!div  class="op_single_selector"]
> * [Unidade](create-locate-anchors-unity.md)
> * [Objective-C](create-locate-anchors-objc.md)
> * [Swift](create-locate-anchors-swift.md)
> * [Android Java](create-locate-anchors-java.md)
> * [C++/NDK](create-locate-anchors-cpp-ndk.md)
> * [C++/WinRT](create-locate-anchors-cpp-winrt.md)

As Âncoras Espaciais Azure permitem-lhe partilhar âncoras no mundo entre diferentes dispositivos. Apoia vários ambientes de desenvolvimento diferentes. Neste artigo, vamos mergulhar em como usar o Azure Spatial Anchors SDK, em Swift, para:

- Configurar e gerir corretamente uma sessão de Âncoras Espaciais Azure.
- Crie e coloque propriedades em âncoras locais.
- Mande-os para a nuvem.
- Localize e elimine as âncoras espaciais da nuvem.

## <a name="prerequisites"></a>Pré-requisitos

Para completar este guia, certifique-se de que tem:

- Leia através da visão geral das [Âncoras Espaciais Azure](../overview.md).
- Completou um dos [Quickstarts de 5 minutos.](../index.yml)
- Conhecimento básico sobre Swift.
- Conhecimentos básicos sobre <a href="https://developer.apple.com/arkit/" target="_blank">ARKit.</a>

[!INCLUDE [Start](../../../includes/spatial-anchors-create-locate-anchors-start.md)]

Saiba mais sobre a aula [ASACloudSpatialAnchorSession.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession)

```swift
    var _cloudSession : ASACloudSpatialAnchorSession? = nil
    // In your view handler
    _cloudSession = ASACloudSpatialAnchorSession()
```

[!INCLUDE [Account Keys](../../../includes/spatial-anchors-create-locate-anchors-account-keys.md)]

Saiba mais sobre a aula [DE Configuração ASASession.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asasessionconfiguration)

```swift
    _cloudSession!.configuration.accountKey = "MyAccountKey"
```

[!INCLUDE [Access Tokens](../../../includes/spatial-anchors-create-locate-anchors-access-tokens.md)]

```swift
    _cloudSession!.configuration.accessToken = "MyAccessToken"
```

[!INCLUDE [Access Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-access-tokens-event.md)]

Saiba mais sobre o método do protocolo [tokenRequired.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#tokenrequired)

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        args.accessToken = "MyAccessToken"
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        let deferral = args.getDeferral()
        myGetTokenAsync( withCompletionHandler: { (myToken: String?) in
            if (myToken != nil) {
                args.accessToken = myToken
            }
            deferral?.complete()
        })
    }

```

[!INCLUDE [Azure AD Tokens](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens.md)]

```swift
    _cloudSession!.configuration.authenticationToken = "MyAuthenticationToken"
```

[!INCLUDE [Azure AD Tokens Event](../../../includes/spatial-anchors-create-locate-anchors-aad-tokens-event.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        args.authenticationToken = "MyAuthenticationToken"
    }
```

[!INCLUDE [Asynchronous Tokens](../../../includes/spatial-anchors-create-locate-anchors-asynchronous-tokens.md)]

```swift
    internal func tokenRequired(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASATokenRequiredEventArgs!) {
        let deferral = args.getDeferral()
        myGetTokenAsync( withCompletionHandler: { (myToken: String?) in
            if (myToken != nil) {
                args.authenticationToken = myToken
            }
            deferral?.complete()
        })
    }

```

[!INCLUDE [Setup](../../../includes/spatial-anchors-create-locate-anchors-setup-ios.md)]

Saiba mais sobre o método de [início.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#start)

```swift
    _cloudSession!.session = self.sceneView.session;
    _cloudSession!.delegate = self;
    _cloudSession!.start()
```

[!INCLUDE [Frames](../../../includes/spatial-anchors-create-locate-anchors-frames.md)]

Saiba mais sobre o método [processFrame.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#processframe)

```swift
    _cloudSession?.processFrame(self.sceneView.session.currentFrame)
```

[!INCLUDE [Feedback](../../../includes/spatial-anchors-create-locate-anchors-feedback.md)]

Saiba mais sobre o método do protocolo atualizado da [sessão.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#sessionupdated)

```swift
    internal func sessionUpdated(_ cloudSession:ASACloudSpatialAnchorSession!, _ args:ASASessionUpdatedEventArgs!) {
        let status = args.status!
        if (status.userFeedback.isEmpty) {
            return
        }
        _feedback = "Feedback: \(FeedbackToString(userFeedback:status.userFeedback)) - Recommend Create=\(status.recommendedForCreateProgress * 100)"
    }
```

[!INCLUDE [Creating](../../../includes/spatial-anchors-create-locate-anchors-creating.md)]

Saiba mais sobre a classe [ASACloudSpatialAnchor.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor)

```swift
    // Create a local anchor, perhaps by hit-testing and creating an ARAnchor
    var localAnchor : ARAnchor? = nil
    let hits = self.sceneView.session.currentFrame?.hitTest(CGPoint(x:0.5, y:0.5), types: ARHitTestResult.ResultType.estimatedHorizontalPlane)
    if (hits!.count == 0) return
    // The hitTest method sorts the resulting list by increasing distance from the camera
    // The first hit result will usually be the most relevant when responding to user input
    localAnchor = ARAnchor(transform:hits![0].worldTransform)
    self.sceneView.session.add(anchor: _localAnchor!)

    // If the user is placing some application content in their environment,
    // you might show content at this anchor for a while, then save when
    // the user confirms placement.
    var cloudAnchor : ASACloudSpatialAnchor? = nil
    cloudAnchor = ASACloudSpatialAnchor()
    cloudAnchor!.localAnchor = localAnchor
    _cloudSession?.createAnchor(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        if (error != nil) {
            _feedback = "Save Failed:\(error!.localizedDescription)"
            return
        }
        _feedback = "Created a cloud anchor with ID=\(cloudAnchor!.identifier!)"
    })
```

[!INCLUDE [Session Status](../../../includes/spatial-anchors-create-locate-anchors-session-status.md)]

Saiba mais sobre o método [getStatusWithCompletionHandler.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getsessionstatus)

```swift
    _cloudSession?.getStatusWithCompletionHandler( { (value:ASASessionStatus, error:Error?) in
        if (error != nil) {
            _feedback = "Session status error:\(error!.localizedDescription)"
            return
        }
        if (value!.recommendedForCreateProgress <> 1.0) {
            return
        }
        // Issue the creation request ...
    })
```

[!INCLUDE [Setting Properties](../../../includes/spatial-anchors-create-locate-anchors-setting-properties.md)]

Saiba mais sobre a propriedade [appProperties.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#appproperties)

```swift
    var cloudAnchor : ASACloudSpatialAnchor? = nil
    cloudAnchor = ASACloudSpatialAnchor()
    cloudAnchor!.localAnchor = localAnchor
    cloudAnchor!.appProperties = [ "model-type" : "frame", "label" : "my latest picture" ]
    _cloudSession?.createAnchor(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        // ...
    })
```

[!INCLUDE [Update Anchor Properties](../../../includes/spatial-anchors-create-locate-anchors-updating-properties.md)]

Saiba mais sobre o método [de actualizaçãoAnchorProperties.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#updateanchorproperties)

```swift
    var anchor : ASACloudSpatialAnchor? = /* locate your anchor */;
    anchor!.appProperties["last-user-access"] = "just now"
    _cloudSession?.updateAnchorProperties(anchor!, withCompletionHandler: { (error:Error?) in
        if (error != nil) {
            _feedback = "Updating Properties Failed:\(error!.localizedDescription)"
        }
    })
```

[!INCLUDE [Getting Properties](../../../includes/spatial-anchors-create-locate-anchors-getting-properties.md)]

Saiba mais sobre o método [getAnchorProperties.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#getanchorproperties)

```swift
    _cloudSession?.getAnchorProperties("anchorId", withCompletionHandler: { (anchor:SCCCloudSpatialAnchor?, error:Error?) in
        if (error != nil) {
            _feedback = "Getting Properties Failed:\(error!.localizedDescription)"
        }
        if (anchor != nil) {
            anchor!.appProperties["last-user-access"] = "just now"
            _cloudSession?.updateAnchorProperties(anchor!, withCompletionHandler: { (error:Error?) in
                // ...
            })
        }
    })

```

[!INCLUDE [Expiration](../../../includes/spatial-anchors-create-locate-anchors-expiration.md)]

Saiba mais sobre a propriedade de [validade.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchor#expiration)

```swift
    let secondsInAWeek = 60.0 * 60.0 * 24.0 * 7.0
    let oneWeekFromNow = Date(timeIntervalSinceNow: secondsInAWeek)
    cloudAnchor!.expiration = oneWeekFromNow
```

[!INCLUDE [Locate](../../../includes/spatial-anchors-create-locate-anchors-locating.md)]

Saiba mais sobre o método [createWatcher.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#createwatcher)

```swift
    let criteria = ASAAnchorLocateCriteria()!
    criteria.identifiers = [ "id1", "id2", "id3" ]
    _cloudSession!.createWatcher(criteria)
```

[!INCLUDE [Locate Events](../../../includes/spatial-anchors-create-locate-anchors-locating-events.md)]

Saiba mais sobre o método do protocolo [anchorLocated.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsessiondelegate#anchorlocated)

```swift
    internal func anchorLocated(_ cloudSession: ASACloudSpatialAnchorSession!, _ args: ASAAnchorLocatedEventArgs!) {
        let status = args.status
        switch (status) {
        case ASALocateAnchorStatus.located:
            let foundAnchor = args.anchor
            // Go add your anchor to the scene...
            break
        case ASALocateAnchorStatus.alreadyTracked:
            // This anchor has already been reported and is being tracked
            break
        case ASALocateAnchorStatus.notLocatedAnchorDoesNotExist:
            // The anchor was deleted or never existed in the first place
            // Drop it, or show UI to ask user to anchor the content anew
            break
        case ASALocateAnchorStatus.notLocated:
            // The anchor hasn't been found given the location data
            // The user might in the wrong location, or maybe more data will help
            // Show UI to tell user to keep looking around
            break
        }
    }
```

[!INCLUDE [Deleting](../../../includes/spatial-anchors-create-locate-anchors-deleting.md)]

Saiba mais sobre o método [de eliminação.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#deleteanchor)

```swift
    _cloudSession?.delete(cloudAnchor!, withCompletionHandler: { (error: Error?) in
        // Perform any processing you may want when delete finishes
    })
```

[!INCLUDE [Stopping](../../../includes/spatial-anchors-create-locate-anchors-stopping.md)]

Saiba mais sobre o método de [paragem.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#stop)

```swift
    _cloudSession!.stop()
```

[!INCLUDE [Resetting](../../../includes/spatial-anchors-create-locate-anchors-resetting.md)]

Saiba mais sobre o método de [reset.](https://docs.microsoft.com/objectivec/api/spatial-anchors/asacloudspatialanchorsession#reset)

```swift
    _cloudSession!.reset()
```

[!INCLUDE [Cleanup](../../../includes/spatial-anchors-create-locate-anchors-cleanup-others.md)]

```swift
    _cloudSession = nil
```

[!INCLUDE [Next Steps](../../../includes/spatial-anchors-create-locate-anchors-next-steps.md)]
