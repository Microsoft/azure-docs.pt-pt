---
title: Depurar seu aplicativo no Eclipse
description: Melhore a confiabilidade e o desempenho de seus serviços desenvolvendo e Depurando-os no Eclipse em um cluster de desenvolvimento local.
author: suhuruli
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 15448a9bd8998a99e8fce578b05130694ecd5fd0
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614490"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Depurar seu aplicativo Java Service Fabric usando o eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Inicie um cluster de desenvolvimento local seguindo as etapas em [configurando seu ambiente de desenvolvimento de Service Fabric](service-fabric-get-started-linux.md).

2. Atualize entryPoint.sh do serviço que você deseja depurar, para que ele inicie o processo Java com os parâmetros de depuração remota. Esse arquivo pode ser encontrado no seguinte local: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. A porta 8001 está definida para depuração neste exemplo.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Atualize o manifesto do aplicativo definindo a contagem de instâncias ou a contagem de réplicas para o serviço que está sendo depurado como 1. Esta definição evita conflitos na porta utilizada para depuração. Por exemplo, para serviços sem estado, defina `InstanceCount="1"` e para serviços com estado, defina os tamanhos de destino e de réplica mínimos como 1 da seguinte forma: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Implementar a aplicação.

5. No IDE do eclipse, selecione **executar-> configurações de depuração-> aplicativo Java remoto e propriedades de conexão de entrada** e defina as propriedades da seguinte maneira:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Defina os pontos de interrupção nas pontas desejadas e depure o aplicativo.

Se o aplicativo estiver falhando, talvez você também queira habilitar o os despejos. Execute `ulimit -c` em um shell e, se ele retornar 0, os despejos não estará habilitado. Para habilitar os despejos ilimitados, execute o seguinte comando: `ulimit -c unlimited`. Você também pode verificar o status usando o comando `ulimit -a`.  Se você quisesse atualizar o caminho de geração de despejo, execute `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`. 

### <a name="next-steps"></a>Passos seguintes

* [Coletar logs usando o diagnóstico do Azure do Linux](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
