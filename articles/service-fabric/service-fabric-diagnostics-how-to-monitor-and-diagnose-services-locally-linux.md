---
title: Aplicativos de tecido de serviço Debug Azure em Linux
description: Saiba como monitorizar e diagnosticar os seus serviços de Service Fabric numa máquina de desenvolvimento Linux local.
ms.topic: conceptual
ms.date: 2/23/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 523cb0d1a8e8f322c1936f1fe52a954399b2acc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88999772"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitorizar e diagnosticar serviços numa configuração local de desenvolvimento de máquinas Linux


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

A monitorização, deteção, diagnóstico e resolução de problemas permitem que os serviços continuem com o mínimo de perturbação da experiência do utilizador. A monitorização e os diagnósticos são fundamentais num ambiente de produção implantado. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o gasoduto de diagnóstico funciona quando se muda para um ambiente de produção. O Service Fabric facilita a implementação de diagnósticos que podem funcionar perfeitamente tanto em configurações de desenvolvimento local de máquina única como em configurações de clusters de produção no mundo real.


## <a name="debugging-service-fabric-java-applications"></a>Aplicações de Depurgging Fabric Java

Para aplicações Java, estão disponíveis [várias estruturas de registo.](https://en.wikipedia.org/wiki/Java_logging_framework) Uma `java.util.logging` vez que é a opção padrão com o JRE, também é usada para os [exemplos de código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). A seguinte discussão explica como configurar o `java.util.logging` quadro.

Utilizando java.util.logging pode redirecionar os registos da aplicação para memória, fluxos de saída, ficheiros de consola ou tomadas. Para cada uma destas opções, existem manipuladores predefinidos já fornecidos no quadro. Pode criar um `app.properties` ficheiro para configurar o manipulador de ficheiros para a sua aplicação para redirecionar todos os registos para um ficheiro local.

O seguinte corte de código contém uma configuração de exemplo:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A pasta apontada pelo `app.properties` ficheiro deve existir. Após a criação do `app.properties` ficheiro, é necessário também modificar o script do ponto de entrada, `entrypoint.sh` na pasta para definir a propriedade para `<applicationfolder>/<servicePkg>/Code/` `java.util.logging.config.file` `app.properties` arquivar. A entrada deve parecer o seguinte corte:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuração resulta em registos a serem recolhidos de forma rotativa em `/tmp/servicefabric/logs/` . O ficheiro de registo neste caso é nomeado mysfapp%u.%g.log onde:
* **%u** é um número único para resolver conflitos entre processos simultâneos de Java.
* **%g** é o número de geração para distinguir entre troncos rotativos.

Por predefinição, se nenhum manipulador estiver explicitamente configurado, o manipulador de consolas está registado. Pode-se ver os registos em syslog em /var/log/syslog.

Para mais informações, consulte os [exemplos de código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Aplicações de depurar tecido c# de serviço


Estão disponíveis várias estruturas para rastrear aplicações CoreCLR no Linux. Para obter mais informações, consulte [extensões .NET para registar registos.](https://github.com/dotnet/extensions/tree/master/src/Logging)  Uma vez que o EventSource é familiar aos desenvolvedores de C#, este artigo utiliza o EventSource para rastrear amostras coreCLR em Linux.

O primeiro passo é incluir System.Diagnostics.Tracing para que possa escrever os seus registos para memória, streams de saída ou ficheiros de consola.  Para iniciar sessão utilizando o EventSource, adicione o seguinte projeto à sua project.jsem:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Pode utilizar um EventListener personalizado para ouvir o evento de serviço e, em seguida, redirecioná-los adequadamente para rastrear ficheiros. O seguinte corte de código mostra uma implementação de amostra de registo usando EventSource e um EventListener personalizado:


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


Os cortes anteriores desemoçam os registos para um ficheiro em `/tmp/MyServiceLog.txt` . Este nome de ficheiro precisa de ser devidamente atualizado. Caso pretenda redirecionar os registos para consolar, utilize o seguinte corte na sua classe EventListener personalizada:

```csharp
public static TextWriter Out = Console.Out;
```

As amostras em [C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) usam EventSource e um EventListener personalizado para registar eventos num ficheiro.



## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreio adicionado à sua aplicação também funciona com o diagnóstico da sua aplicação num cluster Azure. Confira estes artigos que discutem as diferentes opções para as ferramentas e descreva como as configurar.
* [Como recolher registos com diagnósticos Azure](./service-fabric-diagnostics-event-aggregation-lad.md)
