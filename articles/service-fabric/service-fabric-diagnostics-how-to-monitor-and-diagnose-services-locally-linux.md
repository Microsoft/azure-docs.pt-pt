---
title: Aplicativos de tecido de serviço Debug Azure em Linux
description: Aprenda a monitorizar e diagnosticar os seus serviços de Tecido de Serviço numa máquina de desenvolvimento linux local.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526299"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitor e diagnóstico de serviços numa configuração local de desenvolvimento de máquinas Linux


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

A monitorização, deteção, diagnóstico e resolução de problemas permitem que os serviços continuem com a mínima perturbação na experiência do utilizador. A monitorização e o diagnóstico são cruciais num ambiente de produção implementado. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o gasoduto de diagnóstico funciona quando se muda para um ambiente de produção. O Service Fabric facilita que os desenvolvedores de serviços implementem diagnósticos que possam funcionar perfeitamente em conjuntos de desenvolvimento local de máquina única e configurações de clusters de produção no mundo real.


## <a name="debugging-service-fabric-java-applications"></a>Aplicações de Debugging Fabric Java

Para aplicações java, [vários quadros de exploração madeireira](https://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Uma `java.util.logging` vez que é a opção padrão com o JRE, também é usada para os exemplos de [código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). A discussão que se segue `java.util.logging` explica como configurar o quadro.

Utilizando java.util.logging pode redirecionar os registos da sua aplicação para memória, fluxos de saída, ficheiros de consola ou tomadas. Para cada uma destas opções, existem manipuladores de padrão já fornecidos no quadro. Pode criar `app.properties` um ficheiro para configurar o manipulador de ficheiros para a sua aplicação para redirecionar todos os registos para um ficheiro local.

O seguinte código de corte contém uma configuração de exemplo:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A pasta apontada `app.properties` pelo ficheiro deve existir. Depois `app.properties` de criado o ficheiro, também é necessário `entrypoint.sh` modificar `<applicationfolder>/<servicePkg>/Code/` o script `java.util.logging.config.file` do `app.properties` seu ponto de entrada, na pasta para definir a propriedade para arquivar. A entrada deve parecer o seguinte corte:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Esta configuração resulta na recolha de `/tmp/servicefabric/logs/`registos de forma rotativa em . O ficheiro de registo neste caso é nomeado mysfapp%u.%g.log onde:
* **%u** é um número único para resolver conflitos entre processos simultâneos de Java.
* **%g** é o número de geração a distinguir entre troncos rotativos.

Por predefinição, se nenhum manipulador estiver explicitamente configurado, o manipulador de consolas está registado. Pode-se ver os registos em syslog em /var/log/syslog.

Para mais informações, consulte os exemplos de [código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Aplicações de Debugging Service Fabric C#


Vários quadros estão disponíveis para rastrear aplicações CoreCLR no Linux. Para mais informações, consulte [GitHub: logging](http:/github.com/aspnet/logging).  Uma vez que o EventSource é familiar aos desenvolvedores de C#'este artigo usa EventSource para rastreio em amostras CoreCLR em Linux.

O primeiro passo é incluir system.Diagnostics.Tracing para que possa escrever os seus registos para memória, fluxos de saída ou ficheiros de consola.  Para iniciar sessão utilizando o EventSource, adicione o seguinte projeto ao seu projeto.json:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Pode utilizar um EventListener personalizado para ouvir o evento de serviço e, em seguida, redirecioná-los adequadamente para rastrear ficheiros. O seguinte código snippet mostra uma implementação de amostra de registo utilizando eventSource e um EventListener personalizado:


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


O corte anterior produz os registos para `/tmp/MyServiceLog.txt`um ficheiro em . Este nome de ficheiro tem de ser devidamente atualizado. Caso pretenda redirecionar os registos para consolar, utilize o seguinte corte na sua classe EventListener personalizada:

```csharp
public static TextWriter Out = Console.Out;
```

As amostras em [C# Samples](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) usam EventSource e um EventListener personalizado para registar eventos num ficheiro.



## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreio adicionado à sua aplicação também funciona com o diagnóstico da sua aplicação num cluster Azure. Confira estes artigos que discutem as diferentes opções para as ferramentas e descreva como configurar as mesmas.
* [Como recolher registos com diagnósticos Azure](service-fabric-diagnostics-how-to-setup-lad.md)
