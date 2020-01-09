---
title: Depurar aplicativos Service Fabric do Azure no Linux
description: Saiba como monitorar e diagnosticar seus serviços de Service Fabric em uma máquina de desenvolvimento local do Linux.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526299"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitorar e diagnosticar serviços em uma configuração de desenvolvimento de computador Linux local


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

O monitoramento, a detecção, o diagnóstico e a solução de problemas permitem que os serviços continuem com o mínimo de interrupção para a experiência do usuário. O monitoramento e o diagnóstico são essenciais em um ambiente de produção implantado real. A adoção de um modelo semelhante durante o desenvolvimento de serviços garante que o pipeline de diagnóstico funcione quando você passa para um ambiente de produção. Service Fabric facilita para os desenvolvedores de serviço a implementação de diagnósticos que podem funcionar diretamente entre as configurações de desenvolvimento local de um único computador e as configurações de cluster de produção reais.


## <a name="debugging-service-fabric-java-applications"></a>Depurando Service Fabric aplicativos Java

Para aplicativos Java, [várias estruturas de log](https://en.wikipedia.org/wiki/Java_logging_framework) estão disponíveis. Como `java.util.logging` é a opção padrão com o JRE, ele também é usado para os [exemplos de código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). A discussão a seguir explica como configurar o `java.util.logging` Framework.

Usando Java. util. Logging, você pode redirecionar os logs do aplicativo para memória, fluxos de saída, arquivos de console ou soquetes. Para cada uma dessas opções, há manipuladores padrão já fornecidos na estrutura. Você pode criar um arquivo de `app.properties` para configurar o manipulador de arquivo para seu aplicativo para redirecionar todos os logs para um arquivo local.

O trecho de código a seguir contém uma configuração de exemplo:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

A pasta apontada pelo arquivo de `app.properties` deve existir. Depois que o arquivo de `app.properties` for criado, você também precisará modificar o script de ponto de entrada, `entrypoint.sh` na pasta `<applicationfolder>/<servicePkg>/Code/` para definir a propriedade `java.util.logging.config.file` para `app.properties` arquivo. A entrada deve ser semelhante ao trecho a seguir:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Essa configuração resulta em logs sendo coletados de modo rotativo em `/tmp/servicefabric/logs/`. Nesse caso, o arquivo de log é denominado mysfapp% u .% g. log, em que:
* **% u** é um número exclusivo para resolver conflitos entre processos Java simultâneos.
* **% g** é o número de geração para distinguir entre logs de rotação.

Por padrão, se nenhum manipulador for configurado explicitamente, o manipulador de console será registrado. É possível exibir os logs no syslog em/var/log/syslog.

Para obter mais informações, consulte os [exemplos de código no GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Depurando aplicativos Service Fabric C#


Várias estruturas estão disponíveis para rastrear aplicativos de CoreCLR no Linux. Para obter mais informações, consulte [github: Logging](http:/github.com/aspnet/logging).  Como o EventSource é familiar C# para os desenvolvedores, este artigo usa o EventSource para rastreamento em exemplos de CoreCLR no Linux.

A primeira etapa é incluir System. Diagnostics. Tracing para que você possa gravar seus logs em memória, fluxos de saída ou arquivos de console.  Para registro em log usando EventSource, adicione o projeto a seguir ao Project. JSON:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Você pode usar um EventListener personalizado para escutar o evento de serviço e, em seguida, redirecioná-los adequadamente para arquivos de rastreamento. O trecho de código a seguir mostra uma implementação de exemplo de registro em log usando EventSource e um EventListener personalizado:


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


O trecho de código anterior gera os logs para um arquivo em `/tmp/MyServiceLog.txt`. Esse nome de arquivo precisa ser atualizado adequadamente. Caso você queira redirecionar os logs para o console, use o trecho a seguir em sua classe EventListener personalizada:

```csharp
public static TextWriter Out = Console.Out;
```

Os exemplos em [ C# amostras](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) usam EventSource e um EventListener personalizado para registrar eventos em um arquivo.



## <a name="next-steps"></a>Passos seguintes
O mesmo código de rastreamento adicionado ao seu aplicativo também funciona com o diagnóstico do seu aplicativo em um cluster do Azure. Confira estes artigos que discutem as diferentes opções para as ferramentas e descrevem como configurá-las.
* [Como coletar logs com Diagnóstico do Azure](service-fabric-diagnostics-how-to-setup-lad.md)
