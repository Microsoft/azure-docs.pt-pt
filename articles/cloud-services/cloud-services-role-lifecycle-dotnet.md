---
title: Lidar com eventos de ciclo de vida do Serviço cloud [ Microsoft Docs
description: Saiba como os métodos de ciclo de vida de uma função de Serviço em Nuvem podem ser usados em .NET
services: cloud-services
documentationcenter: .net
author: tgore03
ms.service: cloud-services
ms.topic: article
ms.date: 07/18/2017
ms.author: tagore
ms.openlocfilehash: 0a9c32affc50a6d357d4160e00486c896d762e3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75385818"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalize o ciclo de vida de uma função Web ou Trabalhador em .NET
Ao criar uma função de trabalhador, alarga a classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) que fornece métodos para que possa anular que lhe permitam responder a eventos de ciclo de vida. Para papéis web esta aula é opcional, por isso deve usá-la para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Alargar a classe RoleEntryPoint
A classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) inclui métodos que são chamados pelo Azure quando **começa,** **executa,** ou **para** uma função web ou trabalhadora. Você pode opcionalmente substituir estes métodos para gerir a inicialização de papéis, sequências de encerramento de papéis, ou o fio de execução do papel. 

Ao alargar o **RoleEntryPoint,** deve estar atento aos seguintes comportamentos dos métodos:

* Os métodos [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) e [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) devolvem um valor booleano, pelo que é possível devolver **falsos** a estes métodos.
  
   Se o seu código devolver **falsos,** o processo de função é encerrado abruptamente, sem executar qualquer sequência de encerramento que possa ter no lugar. Em geral, deve evitar a devolução **de falsos** do método **OnStart.**
* Qualquer exceção não apanhada dentro de uma sobrecarga de um método **RoleEntryPoint** é tratada como uma exceção não tratada.
  
   Se ocorrer uma exceção dentro de um dos métodos de ciclo de vida, o Azure levantará o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) e, em seguida, o processo é encerrado. Depois do seu papel ter sido desligado, será reiniciado pelo Azure. Quando ocorre uma exceção não tratada, o evento [stop](/previous-versions/azure/reference/ee758136(v=azure.100)) não é levantado e o método **OnStop** não é chamado.

Se o seu papel não começar, ou estiver a reciclar entre os estados inicializadores, ocupados e de paragem, o seu código pode estar a lançar uma exceção não tratada dentro de um dos eventos de ciclo de vida cada vez que o papel recomeça. Neste caso, utilize o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) para determinar a causa da exceção e manuseá-la adequadamente. O seu papel também pode estar a regressar do método [Run,](/previous-versions/azure/reference/ee772746(v=azure.100)) o que faz com que o papel seja reiniciado. Para obter mais informações sobre os estados de implantação, consulte [questões comuns que causam funções para reciclar](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se estiver a utilizar as **Ferramentas Azure para** o Microsoft Visual Studio para desenvolver a sua aplicação, os modelos de projeto de função estendem automaticamente a classe **RoleEntryPoint** para si, nos *ficheiros WebRole.cs* e *WorkerRole.cs.*
> 
> 

## <a name="onstart-method"></a>Método OnStart
O método **OnStart** é chamado quando a sua instância de papel é trazida online pelo Azure. Enquanto o código OnStart está a ser executado, a instância de funções é marcada como **Busy** e nenhum tráfego externo será direcionado para ele pelo equilibrista de carga. Pode substituir este método para realizar trabalhos de inicialização, tais como implementar manipuladores de eventos e iniciar o [Azure Diagnostics](cloud-services-how-to-monitor.md).

Se o **OnStart** regressar **verdadeiro,** a instância é inicializada com sucesso e o Azure chama o método **RoleEntryPoint.Run.** Se o **OnStart** devolver **falsos,** a função termina imediatamente, sem executar quaisquer sequências de encerramento planeadas.

O exemplo de código que se segue mostra como anular o método **OnStart.** Este método configura e inicia um monitor de diagnóstico quando a instância de funções começa e configura uma transferência de dados de registo para uma conta de armazenamento:

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
O método **OnStop** é chamado depois de uma instância de funções ter sido desligada pelo Azure e antes da saída do processo. Pode anular este método de chamada de código necessário para que a sua função seja desligada de forma limpa.

> [!IMPORTANT]
> O código que funciona no método **OnStop** tem um tempo limitado para terminar quando é chamado por razões que não seja uma paragem iniciada pelo utilizador. Após este tempo decorrido, o processo é encerrado, por isso deve certificar-se de que o código no método **OnStop** pode ser executado rapidamente ou tolera não correr até à conclusão. O método **OnStop** é chamado após o evento **stop** ser levantado.
> 
> 

## <a name="run-method"></a>Método de execução
Pode substituir o método **Run** para implementar um fio de longa duração para a sua instância de papel.

Não é necessário ultrapassar o método **Run;** a implementação padrão inicia um fio que dorme para sempre. Se anular o método **Executar,** o seu código deve bloquear indefinidamente. Se o método **Executar** voltar, a função é automaticamente reciclada graciosamente; por outras palavras, o Azure eleva o evento **Stop** e chama o método **OnStop** para que as suas sequências de encerramento possam ser executadas antes de o papel ser retirado offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementação dos métodos ASP.NET ciclo de vida para um papel web
Pode utilizar os métodos ASP.NET ciclo de vida, para além dos fornecidos pela classe **RoleEntryPoint,** para gerir as sequências de inicialização e de paragem para uma função web. Isto pode ser útil para fins de compatibilidade se estiver a analisar uma aplicação ASP.NET existente para o Azure. Os métodos ASP.NET ciclo de vida são chamados a partir dos métodos **RoleEntryPoint.** O método De início de **\_aplicação** é chamado após o acabamento do método **RoleEntryPoint.OnStart.** O método De fim de **aplicação\_** é chamado antes do método **RoleEntryPoint.OnStop** ser chamado.

## <a name="next-steps"></a>Passos seguintes
Saiba como criar um pacote de serviço na [nuvem.](cloud-services-model-and-package.md)




