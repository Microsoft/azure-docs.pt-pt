---
title: Processar eventos de ciclo de vida do serviço em nuvem | Documentos da Microsoft
description: Saiba como os métodos de ciclo de vida de uma função de serviço em nuvem podem ser usados no .NET
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: 39b30acd-57b9-48b7-a7c4-40ea3430e451
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeconnoc
ms.openlocfilehash: 13f500b32bb85bdc0f84b812ef4ef9188a257771
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798020"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalizar o Ciclo de Vida de uma função Web ou de Trabalho em .NET
Quando cria uma função de trabalho, estender o [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) classe que fornece métodos para substituição que lhe permitem responder a eventos de ciclo de vida. Para funções da web essa classe é opcional, para que deve usá-lo para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Estender a classe RoleEntryPoint
O [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) classe inclui métodos chamados pelo Azure quando ele **começa**, **executa**, ou **interrompe** uma função web ou de trabalho. Opcionalmente, pode substituir estes métodos para gerir a inicialização de função, sequências de encerramento de função ou o thread de execução da função. 

Ao estender **RoleEntryPoint**, deve estar ciente dos seguintes comportamentos dos métodos:

* O [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) e [OnStop](/previous-versions/azure/reference/ee772844(v=azure.100)) métodos retornam um valor booleano, portanto, é possível retornar **false** entre esses métodos.
  
   Se seu código retornar **false**, o processo de função está a ser encerrado abruptamente, sem executar qualquer sequência de encerramento pode ter no local. Em geral, deve evitar retornando **false** partir a **OnStart** método.
* Qualquer não detetada exceção dentro de uma sobrecarga de um **RoleEntryPoint** método é tratado como uma exceção não processada.
  
   Se ocorrer uma exceção dentro de um dos métodos de ciclo de vida, o Azure irá elevar o [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) eventos e, em seguida, o processo é encerrado. Depois de sua função foi colocada offline, será reiniciada pelo Azure. Quando ocorre uma exceção não processada, o [parar](/previous-versions/azure/reference/ee758136(v=azure.100)) o evento não é gerado e o **OnStop** método não é chamado.

Se a sua função não for iniciado, ou está a reciclar entre a inicializar, ocupado e os Estados de parar, seu código pode ser a gerar uma exceção sem tratamento dentro de um dos eventos de ciclo de vida reinicia a função de cada vez. Neste caso, utilize o [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) evento para determinar a causa da exceção e processá-la adequadamente. Sua função pode também ser retornar a partir do [executar](/previous-versions/azure/reference/ee772746(v=azure.100)) método, o que faz com que a função reiniciar. Para obter mais informações sobre Estados de implementação, consulte [problemas que causa funções comuns para reciclagem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md).

> [!NOTE]
> Se estiver a utilizar o **ferramentas do Azure para Microsoft Visual Studio** para desenvolver seu aplicativo, os modelos de projeto de função estendem automaticamente o **RoleEntryPoint** classe para, no  *WebRole.cs* e *WorkerRole.cs* ficheiros.
> 
> 

## <a name="onstart-method"></a>Método OnStart
O **OnStart** método é chamado quando a sua instância de função é colocada online pelo Azure. Embora o código de OnStart está em execução, a instância de função está marcada como **ocupado** e nenhum tráfego externo será direcionado para o mesmo pelo balanceador de carga. Pode substituir esse método para realizar o trabalho de inicialização, como a implementação de manipuladores de eventos e a partir de [diagnóstico do Azure](cloud-services-how-to-monitor.md).

Se **OnStart** devolve **true**, a instância é inicializada com êxito e o Azure chama o **RoleEntryPoint.Run** método. Se **OnStart** devolve **falso**, a função é encerrada imediatamente, sem executar quaisquer sequências de encerramento planeada.

O exemplo de código seguinte mostra como substituir a **OnStart** método. Esse método configura e inicia um monitor de diagnóstico quando a instância de função é iniciado e configura uma transferência de dados de registo para uma conta de armazenamento:

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
O **OnStop** método é chamado após uma instância de função foi colocada offline pelo Azure e antes do processo é encerrado. Pode substituir esse método para chamar o código necessário para a sua instância de função para encerrada corretamente.

> [!IMPORTANT]
> Código em execução no **OnStop** método tem um período limitado para concluir a quando ele for chamado por motivos que não seja um encerramento iniciado pelo utilizador. Depois de decorrido este tempo, o processo é encerrado, pelo que deve certificar-se de que esse código no **OnStop** método pode executar rapidamente ou tolera não funcionar até à conclusão. O **OnStop** método é chamado após o **parar** o evento é gerado.
> 
> 

## <a name="run-method"></a>Executar o método
Pode substituir a **executar** método para implementar um thread de execução longa para a sua instância de função.

Substituir a **executar** não é necessário um método; a implementação padrão é iniciado um thread que entra em suspensão para sempre. Se substituir o **executar** método, seu código deve estar bloqueada indefinidamente. Se o **executar** método retorna, a função é automaticamente normalmente reciclada; em outras palavras, o Azure gera a **parar** eventos e as chamadas a **OnStop** método para que sua sequências de encerramento podem ser executadas antes da função seja colocada offline.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementando os métodos de ciclo de vida do ASP.NET para uma função da web
Pode usar os métodos de ciclo de vida ASP.NET, para além dos fornecidos pelos **RoleEntryPoint** classe, para gerir sequências de inicialização e desligamento para uma função da web. Isso poderá ser útil para fins de compatibilidade se estiver portando um aplicativo ASP.NET existente para o Azure. Os métodos de ciclo de vida do ASP.NET são chamados de dentro do **RoleEntryPoint** métodos. O **aplicativo\_começar** método é chamado após o **Roleentrypoint** conclusão do método. O **aplicativo\_End** método é chamado antes do **RoleEntryPoint.OnStop** método é chamado.

## <a name="next-steps"></a>Passos Seguintes
Saiba como [criar um pacote de serviço cloud](cloud-services-model-and-package.md).

