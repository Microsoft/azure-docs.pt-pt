---
title: Depurar a sua aplicação no Eclipse
description: Melhore a fiabilidade e o desempenho dos seus serviços desenvolvendo-os e depurando-os em Eclipse num cluster de desenvolvimento local.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-java
ms.openlocfilehash: d321e0c10b66a15e6cb309cefe711602fa12957c
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91534115"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Depurar a sua aplicação Java Service Fabric usando o Eclipse
> [!div class="op_single_selector"]
> * [Estúdio Visual/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Inicie um cluster de desenvolvimento local seguindo os passos na Configuração do [seu ambiente de desenvolvimento do tecido de serviço.](service-fabric-get-started-linux.md)

2. Atualize entryPoint.sh do serviço que deseja depurar, para que inicie o processo java com parâmetros de depuração remoto. Este ficheiro pode ser encontrado no seguinte local: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh` . A porta 8001 está definida para depuração neste exemplo.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Atualize o Manifesto de Aplicação definindo a contagem de casos ou a contagem de réplicas para o serviço que está a ser depurado para 1. Esta definição evita conflitos na porta utilizada para depuração. Por exemplo, para serviços sem estado, defina `InstanceCount="1"` e para serviços com estado, defina os tamanhos de destino e de réplica mínimos como 1 da seguinte forma: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Desdobre a aplicação.

5. No Eclipse IDE, selecione **Configurações de Depur de depurar > -> Debug -> propriedades de ligação de entrada e de ligação de entrada** e desconte as propriedades da seguinte forma:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Desative os pontos de rutura nos pontos desejados e depure a aplicação.

Se a aplicação estiver em queda, também poderá querer ativar as coredumps. Execute `ulimit -c` numa concha e se voltar a 0, então as coredumps não estão ativadas. Para ativar as coredumps ilimitadas, execute o seguinte comando: `ulimit -c unlimited` . Também pode verificar o estado utilizando o comando `ulimit -a` .  Se quiser atualizar o caminho da geração coredump, execute `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern` . 

### <a name="next-steps"></a>Próximos passos

* [Recolher registos utilizando diagnósticos Linux Azure](./service-fabric-diagnostics-event-aggregation-lad.md).
* [Monitorizar e diagnosticar serviços localmente.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
