---
title: Perguntas comuns para o modo de malha do Azure Service Fabric | Documentos da Microsoft
description: Saiba mais sobre as perguntas mais freqüentes e respostas para o modo de malha do Azure Service Fabric.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: chackdan
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: 950f9ac89b9d3224db29b32fe2d1e403ccc98116
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65143280"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Malha de recursos de infraestrutura do serviço perguntas mais frequentes

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo tem respostas para perguntas freqüentes.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Como posso comunicar um problema ou faça uma pergunta?

Faça perguntas, receba respostas dos engenheiros da Microsoft e reportar problemas na [repositório do GitHub service-fabric-malha-pré-visualização](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Quota e custo

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>O que é o custo de participar na pré-visualização?

Atualmente não existem custos para a implementação de aplicações ou contentores para a pré-visualização da malha. Preste atenção para as atualizações em Maio à ativação para faturação. No entanto, Encorajamo-lo para eliminar os recursos a implementar e não deixá-los em execução, a menos que está testando intensamente o-los.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Existe um limite de quota do número de núcleos e RAM?

Sim. As quotas para cada subscrição são:

- Número de aplicativos: 5
- Núcleos por aplicação: 12
- Total de RAM por aplicação: 48 GB
- Pontos finais de rede e de entrada: 5
- Volumes do Azure que pode anexar: 10
- Número de réplicas do serviço: 3
- O contentor maior, que pode implementar está limitado a 4 núcleos e 16GB de RAM.
- Pode alocar núcleos parciais para os contentores em incrementos de 0,5 núcleos, até um máximo de 6 núcleos.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Quanto posso deixar meu aplicativo implantado?

Atualmente, limitada a vida útil de um aplicativo em dois dias. Trata-se para maximizar a utilização dos núcleos livres alocado para a pré-visualização. Como resultado, só são permitidas para execução de uma determinada continuamente para 48 horas, após o qual ele será encerrado.

Se vir que isso aconteça, pode validar que o sistema encerrá-lo ao executar o `az mesh app show` comando na CLI do Azure. Verifique se ela retorna `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Por exemplo: 

```cli
~$ az mesh app show --resource-group myResourceGroup --name helloWorldApp
{
  "debugParams": null,
  "description": "Service Fabric Mesh HelloWorld Application!",
  "diagnostics": null,
  "healthState": "Ok",
  "id": "/subscriptions/1134234-b756-4979-84re-09d671c0c345/resourcegroups/myResourceGroup/providers/Microsoft.ServiceFabricMesh/applications/helloWorldApp",
  "location": "eastus",
  "name": "helloWorldApp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "serviceNames": [
    "helloWorldService"
  ],
  "services": null,
  "status": "Failed",
  "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue.",
  "tags": {},
  "type": "Microsoft.ServiceFabricMesh/applications",
  "unhealthyEvaluation": null
}
```

Para eliminar o grupo de recursos, utilize o `az group delete <nameOfResourceGroup>` comando.

## <a name="deployments"></a>Implementações

### <a name="what-container-images-are-supported"></a>As imagens de contentor são suportadas?

Se estiver a desenvolver numa máquina Windows Fall Creators Update (versão 1709), só pode utilizar imagens do docker Windows versão 1709.

Se estiver a desenvolver num Windows 10 de Abril de 2018 atualização máquina (versão 1803), pode utilizar a versão 1709 do Windows ou imagens do Windows versão 1803 docker.

As seguintes imagens de sistema operacional de contentor podem ser utilizadas para implementar serviços:

- Windows - windowsservercore e nanoserver
    - Windows Server 1709
    - Windows Server versão 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Não existem limitações conhecidas

> [!NOTE]
> Ferramentas para a malha do Visual Studio não suporta ainda a implantação no Windows Server 2019 e 1809 contentores.

### <a name="what-types-of-applications-can-i-deploy"></a>Os tipos de aplicações posso implementar? 

Pode implementar tudo o que é executado em contentores que cabem dentro as restrições colocados num recurso de aplicação (veja acima para obter mais informações sobre as quotas). Se detectarmos que estiver a utilizar malha para executar cargas de trabalho ilegais ou abuso do sistema (ou seja, de extração), em seguida, reservamo-no direito de terminar a suas implementações e a lista de bloqueios sua subscrição a execução no serviço. Contacte-nos se tiver dúvidas sobre como executar uma carga de trabalho específica. 

## <a name="developer-experience-issues"></a>Problemas de experiência do desenvolvedor

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Resolução DNS de um contentor não funciona

Consultas DNS de um contentor para o serviço de DNS de recursos de infraestrutura do serviço de saída pode falhar em determinadas circunstâncias. Isso é que está sendo investigado. Para mitigar:

- Utilizar o Windows Fall Creators update (versão 1709) ou superior, como a imagem base do contentor.
- Se o nome do serviço autónomo não funcionar, experimente o nome totalmente qualificado: ServiceName.ApplicationName.
- No ficheiro do Docker para o seu serviço, adicione `EXPOSE <port>` em que a porta é a porta estão a expor seu serviço de mensagens em fila no. Por exemplo:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>DNS não funcionam da mesma forma que funciona para clusters de desenvolvimento do Service Fabric e na malha

Terá de fazer referência a serviços de forma diferente no seu cluster de desenvolvimento local do que na malha do Azure.

Utilizar o seu cluster de desenvolvimento local `{serviceName}.{applicationName}`. Na malha de recursos de infraestrutura do serviço de Azure, utilize `{servicename}`. 

Malha do Azure não suporta atualmente a resolução de DNS em todas as aplicações.

Para outros problemas conhecidos do DNS com a execução de um cluster de desenvolvimento do Service Fabric no Windows 10, consulte: [Depurar os contentores do Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) e [DNS problemas conhecidos](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Redes

A rede do ServiceFabric NAT poderá desaparecer ao utilizar a executar a sua aplicação no seu computador local. Para diagnosticar se isso acontecer, execute o seguinte numa linha de comandos:

`docker network ls` e tenha em atenção se `servicefabric_nat` está listado.  Caso contrário, em seguida, execute o seguinte comando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Isto irá resolver o problema, mesmo que a aplicação já tenha sido implementada localmente e, em mau estado de funcionamento.

### <a name="issues-running-multiple-apps"></a>Problemas de execução de várias aplicações

Pode encontrar a disponibilidade de CPU e os limites que está a ser corrigidos em todas as aplicações. Para mitigar:
- Crie um cluster de cinco nós.
- Reduza a utilização da CPU nos serviços em toda a aplicação que é implementada. Por exemplo, no arquivo de service.yaml do seu serviço, alterar `cpu: 1.0` para `cpu: 0.5`

Não não possível implementar várias aplicações para um cluster de um nó. Para mitigar:
- Utilize um cluster de cinco nós, ao implementar várias aplicações num cluster local.
- Remova aplicações que não está atualmente testando.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>As ferramentas do VS tem suporte limitado para contentores do Windows

As ferramentas do Visual Studio só suportam a implementação de contentores do Windows com uma versão de SO base do Windows Server 1709 e versão 1803 hoje mesmo. 

## <a name="feature-gaps-and-other-known-issues"></a>Intervalos de funcionalidades e outros problemas conhecidos

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Depois de implantar meu aplicativo, o recurso de rede associado, pelo que não tem um endereço IP

Existe um problema conhecido no qual o endereço IP não ficam disponível imediatamente. Verificar o estado do recurso de rede dentro de alguns minutos para ver o endereço IP associado.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Meu aplicativo não consegue aceder ao recurso de rede/volume correta

No seu modelo de aplicação, utilize o ID de recurso completo para redes e volumes para poder aceder ao recurso associado. Eis um exemplo do exemplo de início rápido:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Quando eu aumentar horizontalmente, todos os meus contentores afetados, incluindo a execução aqueles

Este é um bug e uma correção que está sendo implementada.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a malha de recursos de infraestrutura do serviço, leia os [descrição geral](service-fabric-mesh-overview.md).
