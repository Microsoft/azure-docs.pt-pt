---
title: Assistentes de voz no Windows - Acima das diretrizes de implementação de bloqueio
titleSuffix: Azure Cognitive Services
description: As instruções para implementar a ativação de voz e as capacidades acima do bloqueio para uma aplicação de agente de voz.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 7a142060a29561526c378ce04b23aa2b286cd6c1
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997405"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementação de assistentes de voz no Windows

Este guia percorre importantes detalhes de implementação para criar um assistente de voz no Windows.

## <a name="implementing-voice-activation"></a>Implementação da ativação de voz

Depois de [configurar o seu ambiente](how-to-windows-voice-assistants-get-started.md) e aprender como funciona a [ativação de voz,](windows-voice-assistants-overview.md#how-does-voice-activation-work)pode começar a implementar a ativação de voz para a sua própria aplicação de assistente de voz.

### <a name="registration"></a>Registo

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Certifique-se de que o microfone está disponível e acessível e, em seguida, monitorize o seu estado

O MVA precisa de um microfone para estar presente e acessível para ser capaz de detetar uma ativação de voz. Utilize as classes [AppCapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362), [DeviceWatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)e [MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362) para verificar se há acesso à privacidade do microfone, presença do dispositivo e estado do dispositivo (como volume e mudo) respectivamente.

### <a name="register-the-application-with-the-background-service"></a>Registe a aplicação com o serviço de fundo

Para que o MVA lance a aplicação em segundo plano, a aplicação tem de ser registada no Serviço de Fundo. Consulte [aqui](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)um guia completo para a inscrição do Serviço de Fundo .

### <a name="unlock-the-limited-access-feature"></a>Desbloquear a funcionalidade de acesso limitado

Utilize a sua chave de acesso limitado fornecida pela Microsoft para desbloquear a funcionalidade de assistente de voz. Utilize a classe [LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362) a partir do Windows SDK para o fazer.

### <a name="register-the-keyword-for-the-application"></a>Registar a palavra-chave para o pedido

A aplicação precisa de se registar, do seu modelo de palavra-chave e da sua linguagem com o Windows.

Comece por recuperar o detetor de palavras-chave. Neste código de amostra, recuperamos o primeiro detetor, mas pode `configurableDetectors`selecionar um determinado detetor selecionando-o a partir de .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

Depois de recuperar o objeto ActivationSignalDetector, ligue para o seu `ActivationSignalDetector.CreateConfigurationAsync` método com o ID de sinal, identificação do modelo e nome de exibição para registar a sua palavra-chave e recuperar a da sua aplicação . `ActivationSignalDetectionConfiguration` Os iDs de sinal e modelo devem ser orientados pelo desenvolvedor e permanecer consistentes para a mesma palavra-chave.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Verifique se a definição de ativação de voz está ativada

Para utilizar a ativação de voz, o utilizador precisa de ativar a ativação de voz para o seu sistema e ativar a ativação de voz para a sua aplicação. Pode encontrar a definição em "Definições de privacidade de ativação de voz" nas definições do Windows. Para verificar o estado da definição de ativação `ActivationSignalDetectionConfiguration` de voz na sua aplicação, utilize a instância do registo da palavra-chave. O campo [DisponibilidadeInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) `ActivationSignalDetectionConfiguration` no campo contém um valor enum que descreve o estado da definição de ativação de voz.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Recupere uma ConversationalAgentSession para registar a aplicação com o sistema MVA

Trata-se `ConversationalAgentSession` de uma classe no Windows SDK que permite à sua aplicação atualizar o Windows com o estado da aplicação (Idle, Detecting, Listening, Working, Speaking) e receber eventos, como a deteção de ativação e alterações de estado do sistema, como o bloqueio do ecrã. Recuperar uma instância do AgentSession também serve para registar a aplicação com o Windows como activatável por voz. É uma boa prática manter `ConversationalAgentSession`uma referência ao . Para recuperar a sessão, utilize a `ConversationalAgentSession.GetCurrentSessionAsync` API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Ouça os dois sinais de ativação: o OnBackgroundActivated e o OnSignalDetected

O Windows irá sinalizar a sua aplicação quando detetar uma palavra-chave de uma de duas maneiras. Se a aplicação não estiver ativa (isto é, não tem uma `ConversationalAgentSession`referência a uma instância não eliminada), então lançará a sua aplicação e chamará o método OnBackgroundActivated no ficheiro App.xaml.cs da sua aplicação. Se o campo `BackgroundActivatedEventArgs.TaskInstance.Task.Name` dos argumentos do evento corresponder à corda "AgentBackgroundTrigger", então a startup de aplicação foi desencadeada pela ativação de voz. A aplicação precisa de anular este método e recuperar uma instância de ConversationalAgentSession para sinalizar para o Windows que está agora ativa. Quando a aplicação estiver ativa, o Windows sinalizará a ocorrência de uma ativação de voz utilizando o `ConversationalAgentSession.OnSignalDetected` evento. Adicione um manipulador de eventos a `ConversationalAgentSession`este evento assim que recuperar o .

## <a name="keyword-verification"></a>Verificação de palavras-chave

Uma vez ativada uma aplicação de agente de voz por voz, o próximo passo é verificar se a deteção da palavra-chave era válida. O Windows não fornece uma solução para a verificação de palavras-chave, mas permite que os assistentes de voz acedam ao áudio a partir da ativação hipotética e verificação completa por si só.

### <a name="retrieve-activation-audio"></a>Recuperar áudio de ativação

Crie um [AudioGraph](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) e `CreateAudioDeviceInputNodeAsync` passe-o para o `ConversationalAgentSession`. Isto carregará o tampão de áudio do gráfico com o áudio *a começar aproximadamente 3 segundos antes da palavra-chave ser detetada*. Este áudio adicional de ligação está incluído para acomodar uma ampla gama de comprimentos de palavra-chave e velocidades de altifalante. Em seguida, manuseie o evento [QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362) a partir do gráfico de áudio para recuperar os dados de áudio.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Nota: O áudio principal incluído no tampão de áudio pode fazer com que a verificação da palavra-chave falhe. Para corrigir este problema, corte o início do tampão de áudio antes de enviar o áudio para verificação de palavras-chave. Esta guarnição inicial deve ser adaptada a cada assistente.

### <a name="launch-in-the-foreground"></a>Lançamento em primeiro plano

Quando a verificação das palavras-chave for bem sucedida, a aplicação tem de passar para o primeiro plano para exibir ui. Ligue `ConversationalAgentSession.RequestForegroundActivationAsync` para a API para mover a sua aplicação para o primeiro plano.

### <a name="transition-from-compact-view-to-full-view"></a>Transição da vista compacta para a vista completa

Quando a sua aplicação é ativada pela primeira vez por voz, é iniciada numa vista compacta. Leia a [orientação do Design para visualização](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) de ativação de voz para orientação sobre as diferentes visões e transições entre elas para assistentes de voz no Windows.

Para fazer a transição da vista compacta para `TryEnterViewModeAsync`a visão completa da aplicação, utilize a API ApplicationView:

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementação acima da ativação do bloqueio

Os seguintes passos cobrem os requisitos para permitir que um assistente de voz no Windows corra acima do bloqueio, incluindo referências ao código de exemplo e orientações para a gestão do ciclo de vida da aplicação. Para obter orientações sobre a conceção de experiências de bloqueio acima, visite o guia de [boas práticas.](windows-voice-assistants-best-practices.md)

### <a name="detecting-lock-screen-transitions"></a>Deteção de transições de ecrã de bloqueio

A biblioteca ConversationalAgent no Windows SDK fornece uma API para tornar o estado do ecrã de bloqueio e alterações no estado do ecrã de bloqueio facilmente acessível. Para detetar o estado atual `ConversationalAgentSession.IsUserAuthenticated` do ecrã de bloqueio, verifique o campo. Para detetar alterações no estado de `ConversationalAgentSession` bloqueio, `SystemStateChanged` adicione um manipulador de eventos ao evento do objeto. Dispara sempre que o ecrã muda de desbloqueado para bloqueado ou vice-versa. Se o valor dos argumentos `ConversationalAgentSystemStateChangeType.UserAuthentication`do evento for , então o estado do ecrã de bloqueio mudou e a aplicação deve fechar.

```csharp
// When the app changes lock state, close the application to prevent duplicates running at once
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        WindowService.CloseWindow();
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Deteção acima da preferência do utilizador de ativação de bloqueio

A entrada na aplicação na página de definições de privacidade de ativação de voz tem um alternância para a funcionalidade de bloqueio acima. Para que a sua aplicação possa ser lançada acima do bloqueio, o utilizador terá de ativar esta definição. O estado das permissões de bloqueio acima também é armazenado no objeto ActivationSignalDetectionDetectionConfiguration. O estado De DisponibilidadeInfo.HasLockScreenPermission reflete se o utilizador deu uma permissão de bloqueio acima. Se esta definição estiver desativada, uma aplicação de voz pode levar o utilizador a navegar para a página de definições apropriada sintetizada no link "definições ms:ativação de voz de privacidade" com instruções sobre como ativar a ativação acima do bloqueio para a aplicação.

## <a name="closing-the-application"></a>Encerramento do pedido

Para fechar corretamente a aplicação programáticamente enquanto acima ou abaixo do bloqueio, utilize a `WindowService.CloseWindow()` API. Isto despoleta todos os métodos de ciclo de vida da UWP, incluindo o OnSuspend, permitindo que a aplicação se elimine da sua `ConversationalAgentSession` instância antes de fechar.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visite a aplicação UWP Voice Assistant Sample por exemplos e code walk-throughs](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
