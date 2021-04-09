---
title: Lidar com eventos de ciclo de vida (clássicos) do Serviço de Nuvem (clássico) | Microsoft Docs
description: Aprenda a usar os métodos de ciclo de vida de um papel de Serviço cloud em .NET, incluindo RoleEntryPoint, que fornece métodos para responder a eventos de ciclo de vida.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: b5aa4bd061647f63ebcc70109f0ba21b39e814cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98741337"
---
# <a name="customize-the-lifecycle-of-a-web-or-worker-role-in-net"></a>Personalize o ciclo de vida de uma função Web ou Trabalhador em .NET

> [!IMPORTANT]
> [Azure Cloud Services (suporte alargado)](../cloud-services-extended-support/overview.md) é um novo modelo de implementação baseado em Recursos Azure para o produto Azure Cloud Services.Com esta alteração, os Serviços Azure Cloud em execução no modelo de implementação baseado no Azure Service Manager foram renomeados como Cloud Services (clássico) e todas as novas implementações devem utilizar [os Serviços Cloud (suporte alargado)](../cloud-services-extended-support/overview.md).

Quando cria um papel de trabalhador, estende-se a classe [RoleEntryPoint,](/previous-versions/azure/reference/ee758619(v=azure.100)) que fornece métodos para que possa anular que lhe permitam responder a eventos de ciclo de vida. Para funções web esta classe é opcional, por isso deve usá-la para responder a eventos de ciclo de vida.

## <a name="extend-the-roleentrypoint-class"></a>Prolongar a classe RoleEntryPoint
A classe [RoleEntryPoint](/previous-versions/azure/reference/ee758619(v=azure.100)) inclui métodos que são chamados por Azure quando **começa,** **executa,** ou **para** um papel web ou trabalhador. Pode substituir opcionalmente estes métodos para gerir a inicialização de funções, as sequências de paragem de funções ou o fio de execução do papel. 

Ao prolongar o **RoleEntryPoint,** deve estar ciente dos seguintes comportamentos dos métodos:

* O método [OnStart](/previous-versions/azure/reference/ee772851(v=azure.100)) devolve um valor boolean, pelo que é possível devolver **falso** deste método.
  
   Se o seu código voltar **a ser falso,** o processo de função termina abruptamente, sem executar qualquer sequência de paragem que possa ter no lugar. Em geral, deve evitar o regresso **falso** do método **OnStart.**
* Qualquer exceção não conseguida dentro de uma sobrecarga de um método **RoleEntryPoint** é tratada como uma exceção não manipulada.
  
   Se ocorrer uma exceção dentro de um dos métodos do ciclo de vida, o Azure levantará o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) e, em seguida, o processo é encerrado. Depois de o seu papel ter sido desligado, será reiniciado pelo Azure. Quando ocorre uma exceção não manipulada, o evento [stop](/previous-versions/azure/reference/ee758136(v=azure.100)) não é levantado, e o método **OnStop** não é chamado.

Se o seu papel não começar, ou estiver a reciclar entre os estados de inicialização, ocupados e de paragem, o seu código pode estar a lançar uma exceção sem manipulação dentro de um dos eventos do ciclo de vida cada vez que o papel reinicia. Neste caso, utilize o evento [UnhandledException](/dotnet/api/system.appdomain.unhandledexception) para determinar a causa da exceção e manuseá-la adequadamente. O seu papel também pode estar a regressar do método [Run,](/previous-versions/azure/reference/ee772746(v=azure.100)) o que faz com que o papel seja reiniciado. Para obter mais informações sobre estados de implantação, consulte [questões comuns que causam papéis para reciclar.](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)

> [!NOTE]
> Se estiver a utilizar as **Ferramentas Azure para** o Microsoft Visual Studio desenvolver a sua aplicação, os modelos de projeto de função estendem automaticamente a classe **RoleEntryPoint** para si, nos ficheiros *WebRole.cs* e *WorkerRole.cs.*
> 
> 

## <a name="onstart-method"></a>Método OnStart
O método **OnStart** é chamado quando a sua instância de função é trazida on-line pela Azure. Enquanto o código OnStart está a ser executado, a instância de função é marcada como **Busy** e nenhum tráfego externo será direcionado para ele pelo equilibrador de carga. Pode sobrepor-se a este método para realizar trabalhos de inicialização, tais como a implementação de manipuladores de eventos e o início do [Azure Diagnostics](cloud-services-how-to-monitor.md).

Se **o OnStart** voltar **a ser verdadeiro,** a instância é inicializada com sucesso e a Azure chama o método **RoleEntryPoint.Run.** Se **o OnStart** voltar **a ser falso,** a função termina imediatamente, sem executar quaisquer sequências de paragem planeadas.

O exemplo de código que se segue mostra como anular o método **OnStart.** Este método configura e inicia um monitor de diagnóstico quando a instância de função começa e configura uma transferência de dados de registo para uma conta de armazenamento:

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
O método **OnStop** é chamado depois de uma instância de papel ter sido desativada pela Azure e antes do processo sair. Pode anular este método de chamada do código necessário para que a sua instância de função seja desligada de forma limpa.

> [!IMPORTANT]
> O código em execução no método **OnStop** tem um tempo limitado para terminar quando é chamado por razões que não uma paragem iniciada pelo utilizador. Após este tempo decorrido, o processo é encerrado, por isso deve certificar-se de que o código do método **OnStop** pode ser executado rapidamente ou tolera não correr até ao final. O método **OnStop** é chamado após o evento **stop** ser levantado.
> 
> 

## <a name="run-method"></a>Método de execução
Pode anular o método **Executar** para implementar um fio de longa duração para a sua instância de função.

Não é necessário ultrapassar o método **Run;** a implementação padrão inicia um fio que dorme para sempre. Se anular o método **'Executar',** o seu código deverá bloquear indefinidamente. Se o método **Executar** voltar, a função é automaticamente reciclada graciosamente; por outras palavras, o Azure eleva o evento **stop** e chama o método **OnStop** para que as suas sequências de paragem possam ser executadas antes de a função ser desligada.

### <a name="implementing-the-aspnet-lifecycle-methods-for-a-web-role"></a>Implementação dos métodos de ciclo de vida ASP.NET para um papel web
Pode utilizar os métodos de ciclo de vida ASP.NET, para além dos fornecidos pela classe **RoleEntryPoint,** para gerir sequências de inicialização e de encerramento para um papel web. Isto pode ser útil para fins de compatibilidade se estiver a apresentar uma aplicação ASP.NET existente ao Azure. Os métodos ASP.NET do ciclo de vida são chamados a partir dos métodos **RoleEntryPoint.** O método **\_ Início de Aplicação** é chamado após o fim do método **RoleEntryPoint.OnStart.** O método **\_ Final de Aplicação** é chamado antes do método **RoleEntryPoint.OnStop** ser chamado.

## <a name="next-steps"></a>Passos seguintes
Saiba como [criar um pacote de serviços em nuvem.](cloud-services-model-and-package.md)




