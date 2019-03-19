---
title: Depurar o Azure Service Fabric aplicações no Linux | Documentos da Microsoft
description: Saiba como monitorizar e diagnosticar os serviços do Service Fabric numa máquina de desenvolvimento local do Linux.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 144b3f8ae210d515695c2184c2c3193084755746
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57841026"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorizar e diagnosticar serviços numa configuração de desenvolvimento do computador local


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitoramento, detetar e diagnosticar e resolução de problemas permitem para os serviços continuar com a mínima interrupção para a experiência do usuário. Monitorização e diagnóstico é essencial num ambiente de produção implementado real. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o pipeline de diagnóstico funciona ao mover para um ambiente de produção. Service Fabric torna mais fácil para os desenvolvedores de serviço implementar o diagnóstico de forma totalmente integrada pode trabalhar em configurações de desenvolvimento local da única máquina e configurações de cluster de produção do mundo real.


## <a name="debugging-service-fabric-java-applications"></a>Depuração de aplicações Java do Service Fabric

Para aplicações de Java [várias arquiteturas de registo](https://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Uma vez que `java.util.logging` é a opção predefinida com o JRE, também é utilizado para o [exemplos no GitHub de código](https://github.com/Azure-Samples/service-fabric-java-getting-started). A discussão seguinte explica como configurar o `java.util.logging` framework.

Usando Util pode redirecionar os registos da aplicação para a memória, fluxos de saída, arquivos de console ou sockets. Para cada uma destas opções, existem manipuladores padrão já é fornecidos no framework. Pode criar um `app.properties` arquivo para configurar o manipulador de arquivo para a sua aplicação redirecionar todos os registos para um ficheiro local.

O fragmento de código seguinte contém um exemplo de configuração:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A pasta apontada pelo `app.properties` ficheiro tem de existir. Depois do `app.properties` ficheiro é criado, terá de também modificar o script de ponto de entrada `entrypoint.sh` no `<applicationfolder>/<servicePkg>/Code/` pasta para definir a propriedade `java.util.logging.config.file` para `app.properties` ficheiro. A entrada deve ter um aspeto como o seguinte fragmento:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuração resulta em registos a ser recolhidos de forma rotação em `/tmp/servicefabric/logs/`. O ficheiro de registo neste caso é denominado mysfapp%u.%g.log onde:
* **%u** é um número exclusivo para resolver conflitos entre processos simultâneos de Java.
* **%g** é o número de geração de distinguir entre girando registos.

Por predefinição se nenhum manipulador explicitamente estiver configurado, o processador de consola está registado. Um pode ver os registos de syslog em /var/log/syslog.

Para obter mais informações, consulte a [exemplos no GitHub de código](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Depurando aplicativos do Service Fabric em C#


Várias estruturas estão disponíveis para rastreio CoreCLR aplicações no Linux. Para obter mais informações, consulte [GitHub: registo](http:/github.com/aspnet/logging).  Uma vez que EventSource é familiar para os desenvolvedores de c#,' Este artigo usa EventSource para rastreamento nos exemplos de CoreCLR no Linux.

A primeira etapa é incluir System.Diagnostics.Tracing para que possa escrever os registos de memória, fluxos de saída ou ficheiros da consola.  Para o Registro em log usando EventSource, adicione ao projeto seguinte ao seu Project:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Pode usar um EventListener personalizado para ouvir o evento de serviço e, em seguida, redirecioná-los adequadamente para ficheiros de rastreio. O fragmento de código seguinte mostra uma implementação de exemplo do início de sessão usando EventSource e um EventListener personalizado:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


O trecho anterior produz os registos para um ficheiro no `/tmp/MyServiceLog.txt`. Este nome de ficheiro tem de ser atualizados adequadamente. Caso deseje redirecionar os registos de consola, utilize o fragmento seguinte na sua classe EventListener personalizado:

```csharp
public static TextWriter Out = Console.Out;
```

Os exemplos em [amostras de c#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) utilizar EventSource e um EventListener personalizado para registar eventos para um ficheiro.



## <a name="next-steps"></a>Passos Seguintes
O mesmo código de rastreamento adicionado ao seu aplicativo também funciona com o diagnóstico da sua aplicação num cluster do Azure. Veja estes artigos que discutem as diferentes opções para as ferramentas e descrevem como configurá-los.
* [Como recolher registos com o diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)
