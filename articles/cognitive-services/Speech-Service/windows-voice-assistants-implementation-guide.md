---
title: Assistentes de voz no Windows - Acima das Diretrizes de Implementação do Bloqueio
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
ms.custom: devx-track-csharp
ms.openlocfilehash: 92ab043d4fccbe0764e361eac6f71ef69a5963cb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939854"
---
# <a name="implementing-voice-assistants-on-windows"></a>Implementação de assistentes de voz no Windows

Este guia percorre importantes detalhes de implementação para criar um assistente de voz no Windows.

## <a name="implementing-voice-activation"></a>Implementação da ativação de voz

Depois de [configurar o seu ambiente](how-to-windows-voice-assistants-get-started.md) e aprender [como funciona a ativação de voz,](windows-voice-assistants-overview.md#how-does-voice-activation-work)pode começar a implementar ativação de voz para a sua própria aplicação de assistente de voz.

### <a name="registration"></a>Registo

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>Certifique-se de que o microfone está disponível e acessível e, em seguida, monitorize o seu estado

O MVA precisa de um microfone para estar presente e acessível para ser capaz de detetar uma ativação de voz. Utilize as classes [AppCapability](/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability), [DeviceWatcher](/uwp/api/windows.devices.enumeration.devicewatcher)e [MediaCapture](/uwp/api/windows.media.capture.mediacapture) para verificar o acesso à privacidade do microfone, a presença do dispositivo e o estado do dispositivo (como volume e mudo) respectivamente.

### <a name="register-the-application-with-the-background-service"></a>Registe a aplicação com o serviço de fundo

Para que a MVA lance a aplicação em segundo plano, a aplicação precisa de ser registada no Serviço de Fundo. Consulte aqui um guia completo para o registo do Serviço de [Antecedentes.](/windows/uwp/launch-resume/register-a-background-task)

### <a name="unlock-the-limited-access-feature"></a>Desbloqueie a funcionalidade de acesso limitado

Utilize a sua chave de funcionalidade de acesso limitado fornecida pela Microsoft para desbloquear a função de assistente de voz. Utilize a classe [LimitedAccessFeature](/uwp/api/windows.applicationmodel.limitedaccessfeatures) do Windows SDK para o fazer.

### <a name="register-the-keyword-for-the-application"></a>Registar a palavra-chave para a aplicação

A aplicação precisa de se registar, o seu modelo de palavra-chave e o seu idioma com o Windows.

Comece por recuperar o detetor de palavras-chave. Neste código de amostra, recuperamos o primeiro detetor, mas pode selecionar um determinado detetor selecionando-o a partir de `configurableDetectors` .

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

Depois de recuperar o objeto ActivationSignalDetector, ligue para o seu `ActivationSignalDetector.CreateConfigurationAsync` método com o ID de sinal, iD do modelo e nome de exibição para registar a sua palavra-chave e recuperar a da sua aplicação `ActivationSignalDetectionConfiguration` . Os IDs de sinal e modelo devem ser orientados pelo desenvolvedor e manter-se consistentes para a mesma palavra-chave.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>Verifique se a definição de ativação de voz está ativada

Para utilizar a ativação por voz, o utilizador necessita de ativar a ativação de voz para o seu sistema e ativar a ativação de voz para a sua aplicação. Pode encontrar a definição em "Definições de privacidade de ativação por voz" nas definições do Windows. Para verificar o estado da definição de ativação por voz na sua aplicação, utilize a instância `ActivationSignalDetectionConfiguration` do registo da palavra-chave. O campo [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) contém `ActivationSignalDetectionConfiguration` um valor enum que descreve o estado da definição de ativação de voz.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>Recupere uma Sessão de Conversas para registar a app com o sistema MVA

É `ConversationalAgentSession` uma classe no Windows SDK que permite que a sua aplicação atualize o Windows com o estado da aplicação (Idle, Detecting, Listening, Working, Speaking) e receba eventos, como deteção de ativação e alterações do estado do sistema, como o bloqueio do ecrã. Recuperar um caso da AgenteSessão também serve para registar a aplicação com o Windows como ativado por voz. É uma boa prática manter uma referência ao `ConversationalAgentSession` . Para recuperar a sessão, use a `ConversationalAgentSession.GetCurrentSessionAsync` API.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>Ouça os dois sinais de ativação: o OnBackgroundActivated e OnSignalDetected

O Windows sinalizará a sua aplicação quando detetar uma palavra-chave de duas formas. Se a aplicação não estiver ativa (ou seja, não tem referência a uma instância não eliminada `ConversationalAgentSession` de), então lançará a sua aplicação e chamará o método OnBackgroundActivated na App.xaml.cs ficheiro da sua aplicação. Se o campo dos argumentos do evento `BackgroundActivatedEventArgs.TaskInstance.Task.Name` corresponder à cadeia "AgentBackgroundTrigger", então a startup da aplicação foi desencadeada por ativação de voz. A aplicação precisa de anular este método e recuperar um exemplo de ConversationalAgentSession para sinalizar para o Windows que está agora ativo. Quando a aplicação estiver ativa, o Windows sinalizará a ocorrência de uma ativação por voz utilizando o `ConversationalAgentSession.OnSignalDetected` evento. Adicione um manipulador de eventos a este evento assim que recuperar o `ConversationalAgentSession` .

## <a name="keyword-verification"></a>Verificação de palavras-chave

Uma vez que uma aplicação de agente de voz é ativada por voz, o próximo passo é verificar se a deteção de palavras-chave era válida. O Windows não fornece uma solução para a verificação de palavras-chave, mas permite que os assistentes de voz acedam ao áudio a partir da ativação hipotética e verificação completa por si só.

### <a name="retrieve-activation-audio"></a>Recuperar áudio de ativação

Crie um [AudioGraph](/uwp/api/windows.media.audio.audiograph) e passe-o para o `CreateAudioDeviceInputNodeAsync` do `ConversationalAgentSession` . Isto carregará o tampão áudio do gráfico com o áudio *a iniciar aproximadamente 3 segundos antes de a palavra-chave ter sido detetada*. Este áudio adicional principal está incluído para acomodar uma ampla gama de comprimentos de palavras-chave e velocidades de altifalante. Em seguida, manuseie o evento [QuantumStarted](/uwp/api/windows.media.audio.audiograph.quantumstarted) a partir do gráfico de áudio para recuperar os dados áudio.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

Nota: O áudio principal incluído no tampão áudio pode fazer com que a verificação das palavras-chave falhe. Para corrigir este problema, corte o início do tampão de áudio antes de enviar o áudio para verificação da palavra-chave. Esta guarnição inicial deve ser adaptada a cada assistente.

### <a name="launch-in-the-foreground"></a>Lançamento em primeiro plano

Quando a verificação de palavras-chave for bem sucedida, a aplicação precisa de passar para o primeiro plano para exibir UI. Ligue para a `ConversationalAgentSession.RequestForegroundActivationAsync` API para mover a sua candidatura para primeiro plano.

### <a name="transition-from-compact-view-to-full-view"></a>Transição de vista compacta para vista completa

Quando a sua aplicação é ativada pela primeira vez por voz, é iniciada numa vista compacta. Leia a [orientação do Design para pré-visualização de ativação de voz](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) para obter orientações sobre as diferentes visões e transições entre eles para assistentes de voz no Windows.

Para fazer a transição de vista compacta para vista completa da aplicação, utilize a API ApplicationView `TryEnterViewModeAsync` :

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>Implementação acima da ativação do bloqueio

Os seguintes passos abrangem os requisitos para permitir que um assistente de voz no Windows seja executado acima do bloqueio, incluindo referências ao código de exemplo e diretrizes para a gestão do ciclo de vida da aplicação.

Para obter orientações sobre a conceção de experiências de bloqueio acima, visite o guia de [boas práticas.](windows-voice-assistants-best-practices.md)

Quando uma aplicação mostra uma vista acima do cadeado, considera-se que está em "Modo Quiosque". Para obter mais informações sobre a implementação de uma aplicação que utiliza o Modo Quiosque, consulte a [documentação](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)do modo quiosque .

### <a name="transitioning-above-lock"></a>Transição acima da fechadura

Uma ativação acima do cadeado é semelhante a uma ativação abaixo do bloqueio. Se não houver casos ativos da aplicação, será iniciada uma nova instância em segundo plano e `OnBackgroundActivated` em App.xaml.cs será chamada. Se houver uma instância do pedido, esse caso receberá uma notificação através do `ConversationalAgentSession.SignalDetected` evento.

Se a aplicação ainda não estiver a aparecer acima do cadeado, deve ligar `ConversationalAgentSession.RequestForegroundActivationAsync` . Isto desencadeia o `OnLaunched` método em App.xaml.cs que deve navegar para a vista que será mostrada acima do cadeado.

### <a name="detecting-lock-screen-transitions"></a>Deteção de transições de ecrã de bloqueio

A biblioteca ConversationalAgent no Windows SDK fornece uma API para tornar o estado do ecrã de bloqueio e alterações no estado do ecrã de bloqueio facilmente acessíveis. Para detetar o estado atual do ecrã de bloqueio, verifique o `ConversationalAgentSession.IsUserAuthenticated` campo. Para detetar alterações no estado de bloqueio, adicione um manipulador de eventos ao `ConversationalAgentSession` evento do `SystemStateChanged` objeto. Dispara sempre que o ecrã muda de desbloqueado para o fecho ou vice-versa. Se o valor dos argumentos do evento for `ConversationalAgentSystemStateChangeType.UserAuthentication` , então o estado do ecrã de bloqueio mudou.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>Deteção acima da preferência do utilizador de ativação de bloqueio

A entrada da aplicação na página de definições de Privacidade de Ativação por Voz tem um toggle para a funcionalidade de bloqueio acima. Para que a sua aplicação possa ser lançada acima do bloqueio, o utilizador terá de ligar esta definição. O estado das permissões de bloqueio acima também é armazenado no objeto ActivationSignalDetectionConfiguration. O estado de disponibilidadeInfo.HasLockScreenPermission reflete se o utilizador deu a permissão de bloqueio acima. Se esta definição for desativada, uma aplicação de voz pode levar o utilizador a navegar para a página de definições apropriadas no link "ms-settings:privacy-voiceactivation" com instruções sobre como ativar a ativação acima do bloqueio para a aplicação.

## <a name="closing-the-application"></a>Encerramento da aplicação

Para fechar corretamente a aplicação programáticamente enquanto está acima ou abaixo do bloqueio, utilize a `WindowService.CloseWindow()` API. Isto desencadeia todos os métodos de ciclo de vida UWP, incluindo OnSuspend, permitindo que a aplicação elimine o seu caso antes de `ConversationalAgentSession` fechar.

> [!NOTE]
> A aplicação pode fechar sem fechar a [instância de bloqueio abaixo](/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-). Neste caso, a vista de bloqueio acima precisa de "limpar", certificando-se de que, uma vez desbloqueado o ecrã, não existem manipuladores ou tarefas de eventos que tentem manipular a vista de bloqueio acima.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Visite a aplicação UWP Voice Assistant Sample para exemplos e códigos de passagem](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
