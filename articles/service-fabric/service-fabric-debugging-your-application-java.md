---
title: Depuraa a sua aplicação no Eclipse
description: Melhore a fiabilidade e desempenho dos seus serviços desenvolvendo-os e depurando-os em Eclipse num cluster de desenvolvimento local.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75614490"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Depurar a sua aplicação Java Service Fabric usando o Eclipse
> [!div class="op_single_selector"]
> * [Estúdio Visual/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Inicie um cluster de desenvolvimento local seguindo os passos na configuração do seu ambiente de desenvolvimento de [Tecido de Serviço.](service-fabric-get-started-linux.md)

2. Atualize entryPoint.sh do serviço que pretende depurar, para que inicie o processo de java com parâmetros de depuração remotos. Este ficheiro pode ser encontrado `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`no seguinte local: . A porta 8001 está definida para depuração neste exemplo.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Atualize o Manifesto de Aplicação definindo a contagem de instâncias ou a contagem de réplicas para o serviço que está a ser depurado para 1. Esta definição evita conflitos na porta utilizada para depuração. Por exemplo, para serviços sem estado, defina `InstanceCount="1"` e para serviços com estado, defina os tamanhos de destino e de réplica mínimos como 1 da seguinte forma: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Implemente a aplicação.

5. No Eclipse IDE, selecione Configurações de **Deteção de Executar -> - > propriedades de java remota e de ligação** à entrada e detetete as propriedades da seguinte forma:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Desloque os pontos de rutura nos pontos desejados e desinveja a aplicação.

Se a aplicação estiver a falhar, também poderá querer ativar os coredumps. Execute `ulimit -c` numa concha e se devolver 0, então as lixeiras não estão ativadas. Para permitir despejos de núcleo ilimitados, execute o seguinte comando: `ulimit -c unlimited`. Também pode verificar o estado `ulimit -a`utilizando o comando .  Se quiser atualizar o caminho da `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`geração coredump, execute . 

### <a name="next-steps"></a>Passos seguintes

* [Colete registos utilizando diagnósticos Linux Azure](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorizar e diagnosticar serviços localmente.](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
