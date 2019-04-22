---
title: Depurar a sua aplicação do Azure Service Fabric no Eclipse | Documentos da Microsoft
description: Melhore a fiabilidade e desempenho dos seus serviços ao desenvolver e depurá-los no Eclipse num cluster de desenvolvimento local.
services: service-fabric
documentationcenter: .net
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 2f00636da2b29e7815569a683fdf51c6a4e3b0e0
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58880304"
---
# <a name="debug-your-java-service-fabric-application-using-eclipse"></a>Depurar a sua aplicação Java do Service Fabric com o Eclipse
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
> 

1. Iniciar um cluster de desenvolvimento local ao seguir os passos em [como configurar o ambiente de desenvolvimento do Service Fabric](service-fabric-get-started-linux.md).

2. Atualize entryPoint.sh do serviço que pretende depurar, para que ele inicia o processo de java com parâmetros de depuração remota. Este ficheiro pode ser encontrado na seguinte localização: `ApplicationName\ServiceNamePkg\Code\entrypoint.sh`. A porta 8001 está definida para depuração neste exemplo.

    ```sh
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar myapp.jar
    ```
3. Atualize o manifesto da aplicação ao definir a contagem de instâncias ou de réplicas para o serviço que está a ser depurado como 1. Esta definição evita conflitos na porta utilizada para depuração. Por exemplo, para serviços sem estado, defina `InstanceCount="1"` e para serviços com estado, defina os tamanhos de destino e de réplica mínimos como 1 da seguinte forma: `TargetReplicaSetSize="1" MinReplicaSetSize="1"`.

4. Implemente a aplicação.

5. No Eclipse IDE, selecione **executar -> configurações de depuração -> aplicação Java remota e propriedades de ligação de entrada** e defina as propriedades da seguinte forma:

   ```
   Host: ipaddress
   Port: 8001
   ```
6.  Defina pontos de interrupção em pontos pretendidos e depurar a aplicação.

Se o aplicativo está falhando, pode também querer ativar coredumps. Executar `ulimit -c` numa shell e se ele retorna 0, então coredumps não estão ativadas. Para ativar coredumps ilimitado, execute o seguinte comando: `ulimit -c unlimited`. Também pode verificar o estado com o comando `ulimit -a`.  Se quiser atualizar o caminho de geração de coredump, executar `echo '/tmp/core_%e.%p' | sudo tee /proc/sys/kernel/core_pattern`. 

### <a name="next-steps"></a>Passos Seguintes

* [Recolher registos com o diagnóstico do Linux do Azure](service-fabric-diagnostics-how-to-setup-lad.md).
* [Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).
