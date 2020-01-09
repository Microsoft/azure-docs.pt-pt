---
title: Manipular eventos de ciclo de vida do serviço de nuvem | Microsoft Docs
description: Saiba como os métodos de ciclo de vida de uma função de serviço de nuvem podem ser usados no .NET
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 0a9c32affc50a6d357d4160e00486c896d762e3f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75385818"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o Ciclo de Vida de uma função Web ou de Trabalho em .NET
Ao criar uma função de trabalho, você estende a classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) , que fornece métodos para substituição que permitem responder a eventos de ciclo de vida. Para funções Web, essa classe é opcional, portanto, você deve usá-la para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Estender a classe RoleEntryPoint
A classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) inclui métodos que são chamados pelo Azure quando ele **inicia**, **executa**ou **para** uma função Web ou de trabalho. Opcionalmente, você pode substituir esses métodos para gerenciar a inicialização de função, as sequências de desligamento de função ou o thread de execução da função. 

Ao estender **RoleEntryPoint**, você deve estar ciente dos seguintes comportamentos dos métodos:

* Os métodos [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) e [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) retornam um valor booliano, portanto, é possível retornar **false** desses métodos.
  
   Se o seu código retornar **false**, o processo de função será encerrado abruptamente, sem executar nenhuma sequência de desligamento que você possa ter em vigor. Em geral, você deve evitar retornar **false** do método **OnStart** .
* Qualquer exceção não percebida dentro de uma sobrecarga de um método **RoleEntryPoint** é tratada como uma exceção sem tratamento.
  
   Se ocorrer uma exceção dentro de um dos métodos do ciclo de vida, o Azure gerará o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) e, em seguida, o processo será encerrado. Depois que sua função tiver sido colocado offline, ela será reiniciada pelo Azure. Quando ocorre uma exceção sem tratamento, o evento de [parada](/previous-versions/azure/reference/ee758136(v=azure.100)) não é gerado e o método **OnStop** não é chamado.

Se sua função não for iniciada ou estiver reciclando entre os Estados inicializando, ocupado e parando, seu código poderá estar lançando uma exceção sem tratamento dentro de um dos eventos do ciclo de vida sempre que a função for reiniciada. Nesse caso, use o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) para determinar a causa da exceção e tratá-la adequadamente. Sua função também pode estar retornando do método [Run](/previous-versions/azure/reference/ee772746(v=azure.100)) , que faz com que a função seja reiniciada. Para obter mais informações sobre Estados de implantação, consulte [problemas comuns que causam a reciclagem de funções](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se você estiver usando as **Ferramentas do Azure para Microsoft Visual Studio** para desenvolver seu aplicativo, os modelos de projeto de função estenderão automaticamente a classe **RoleEntryPoint** para você, nos arquivos *WebRole.cs* e *WorkerRole.cs* .
> 
> 

## <a name="onstart-method"></a>Método OnStart
O método **OnStart** é chamado quando sua instância de função é colocada online pelo Azure. Enquanto o código OnStart está em execução, a instância de função é marcada como **ocupada** e nenhum tráfego externo será direcionado a ele pelo balanceador de carga. Você pode substituir esse método para executar o trabalho de inicialização, como a implementação de manipuladores de eventos e a inicialização de [diagnóstico do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** retornar **true**, a instância será inicializada com êxito e o Azure chamará o método **RoleEntryPoint. Run** . Se **OnStart** retornar **false**, a função será encerrada imediatamente, sem executar nenhuma sequência de desligamento planejada.

O exemplo de código a seguir mostra como substituir o método **OnStart** . Esse método configura e inicia um monitor de diagnóstico quando a instância de função inicia e configura uma transferência de dados de log para uma conta de armazenamento:

```csharp
public override bool OnStart()
{
    var config = DiagnosticMonitor.GetDefaultInitialConfiguration();

    config.DiagnosticInfrastructureLogs.ScheduledTransferLogLevelFilter = LogLevel.Error;
    config.DiagnosticInfrastructureLogs.ScheduledTransferPeriod = TimeSpan.FromMinutes(5);

    DiagnosticMonitor.Start("DiagnosticsConnectionString", config);

    return true;
}
```

## <a name="onstop-method"></a>Método OnStop
O método **OnStop** é chamado depois que uma instância de função é obtida offline pelo Azure e antes da saída do processo. Você pode substituir esse método para chamar o código necessário para que sua instância de função seja desligada corretamente.

> [!IMPORTANT]
> O código em execução no método **OnStop** tem um tempo limitado para ser concluído quando é chamado por razões diferentes de um desligamento iniciado pelo usuário. Depois que esse tempo decorrer, o processo é encerrado, portanto, você deve garantir que o código no método **OnStop** possa ser executado rapidamente ou tolerar a não execução até a conclusão. O método **OnStop** é chamado depois que o evento de **parada** é gerado.
> 
> 

## <a name="run-method"></a>Método Run
Você pode substituir o método **Run** para implementar um thread de execução longa para sua instância de função.

Não é necessário substituir o método **Run** ; a implementação padrão inicia um thread que é suspenso para sempre. Se você substituir o método **Run** , seu código deverá ser bloqueado indefinidamente. Se o método **Run** retornar, a função será reciclada automaticamente normalmente; em outras palavras, o Azure gera o evento de **parada** e chama o método **OnStop** para que suas sequências de desligamento possam ser executadas antes que a função seja colocado offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementando os métodos de ciclo de vida de ASP.NET para uma função Web
Você pode usar os métodos de ciclo de vida ASP.NET, além daqueles fornecidos pela classe **RoleEntryPoint** , para gerenciar sequências de inicialização e desligamento para uma função Web. Isso pode ser útil para fins de compatibilidade se você estiver portando um aplicativo ASP.NET existente para o Azure. Os métodos de ciclo de vida de ASP.NET são chamados de dentro dos métodos **RoleEntryPoint** . O método **Start do aplicativo\_** é chamado depois que o método **RoleEntryPoint. OnStart** é concluído. O método **end do aplicativo\_** é chamado antes do método **RoleEntryPoint. OnStop** ser chamado.

## <a name="next-steps"></a>Passos seguintes
Saiba como [criar um pacote de serviço de nuvem](cloud-services-model-and-package.md).




