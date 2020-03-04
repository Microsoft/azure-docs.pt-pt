---
title: Perguntas comuns para malha de tecido de serviço Azure
description: Saiba mais sobre perguntas e respostas comumente feitas para a Malha de Tecido de Serviço Azure.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 2a5c2ea63d162eb6fb78ab702e0519f8ac25dcc7
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2020
ms.locfileid: "78252497"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Perguntas de malha de tecido de serviço comumente perguntas

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo tem respostas a perguntas comumente feitas.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Como reporto um assunto ou faço uma pergunta?

Faça perguntas, obtenha respostas dos engenheiros da Microsoft e reporte problemas no [repo gitHub](https://aka.ms/sfmeshissues)de malha de serviço.

## <a name="quota-and-cost"></a>Quota e Custo

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Qual é o custo de participar na pré-visualização?

Atualmente, não existem encargos para a implantação de aplicações ou contentores para a pré-visualização da Malha. Por favor, consulte as atualizações em maio para obter a faturação. No entanto, encorajamo-lo a apagar os recursos que implementa e não deixá-los a funcionar a menos que esteja a testá-los ativamente.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Existe um limite de quota do número de núcleos e RAM?

Sim. As quotas para cada subscrição são:

- Número de candidaturas: 5
- Núcleos por aplicação: 12
- RAM total por aplicação: 48 GB
- Pontos finais de rede e Ingress: 5
- Volumes Azure que pode anexar: 10
- Número de réplicas de serviço: 3
- O maior contentor que pode implantar está limitado a 4 núcleos e 16GB de RAM.
- Pode alocar núcleos parciais aos seus recipientes em incrementos de 0,5 núcleos, até um máximo de 6 núcleos.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Quanto tempo posso deixar a minha candidatura implantada?

Atualmente, limitamos a vida útil de uma candidatura a dois dias. Isto para maximizar a utilização dos núcleos gratuitos atribuídos à pré-visualização. Como resultado, só é permitido executar uma determinada implantação continuamente durante 48 horas, após o qual será desligado.

Se vir isto acontecer, pode validar que o sistema o desligou executando o comando `az mesh app show` no Azure CLI. Verifique se devolve `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

Por exemplo: 

```azurecli
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

```output
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

Para eliminar o grupo de recursos, utilize o comando `az group delete <nameOfResourceGroup>`.

## <a name="deployments"></a>Implementações

### <a name="what-container-images-are-supported"></a>Que imagens de contentores são suportadas?

Se estiver a desenvolver uma máquina de Atualização de Criadores de outono do Windows (versão 1709), só pode utilizar imagens de docker da versão 1709 do Windows.

Se estiver a desenvolver uma máquina de atualização do Windows 10 de abril de 2018 (versão 1803), pode utilizar imagens de docker da versão Windows 1709 ou do Windows 1803.

As seguintes imagens de osso do recipiente podem ser utilizadas para a implantação de serviços:
- Windows - windowsservercore e nanoserver
    - Windows Server 1709
    - Windows Server versão 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Sem limitações conhecidas

> [!NOTE]
> A ferramenta visual studio para a Malha ainda não suporta a implementação em recipientes Windows Server 2019 e 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Que tipo de aplicações posso implementar? 

Pode implantar qualquer coisa que corra em contentores que se encaixem dentro das restrições colocadas num recurso de aplicação (ver acima para obter mais informações sobre quotas). Se detetarmos que está a usar a Malha para executar cargas de trabalho ilegais ou abusar do sistema (ou seja, mineração), reservamo-nos o direito de terminar as suas implementações e bloquear a sua subscrição de executar o serviço. Por favor contacte-nos se tiver alguma dúvida sobre a execução de uma carga de trabalho específica. 

## <a name="developer-experience-issues"></a>Problemas de experiência de desenvolvedor

### <a name="dns-resolution-from-a-container-doesnt-work"></a>A resolução do DNS de um contentor não funciona

As consultas de DNS de saída de um contentor para o serviço DNS de tecido de serviço podem falhar em determinadas circunstâncias. Isto está a ser investigado. Para mitigar:

- Utilize a atualização dos Criadores de outono do Windows (versão 1709) ou superior à imagem do recipiente base.
- Se o nome do serviço por si só não funcionar, experimente o nome totalmente qualificado: ServiceName.ApplicationName.
- No ficheiro Docker para o seu serviço, adicione `EXPOSE <port>` onde a porta é a porta onde está a expor o seu serviço. Por exemplo:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>O DNS não funciona da mesma forma que faz para os clusters de desenvolvimento do Tecido de Serviço e na Malha

Você pode precisar de referência de serviços de forma diferente no seu cluster de desenvolvimento local do que em Azure Mesh.

No seu cluster de desenvolvimento local, utilize `{serviceName}.{applicationName}`. Na malha de tecido de serviço Azure, utilize `{servicename}`. 

A Malha Azure não suporta atualmente a resolução do DNS através de aplicações.

Para outros problemas conhecidos do DNS com a execução de um cluster de desenvolvimento de Tecido de Serviço no Windows 10, consulte: [Debug Windows contentores](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) e [problemas dNS conhecidos](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Redes

A rede ServiceFabric NAT pode desaparecer enquanto utiliza a sua aplicação na sua máquina local. Para diagnosticar se isto aconteceu, corra o seguinte a partir de um pedido de comando:

`docker network ls` e note se `servicefabric_nat` está listado.  Caso contrário, execute o seguinte comando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Isto abordará o problema mesmo que a aplicação já esteja implantada localmente e em estado pouco saudável.

### <a name="issues-running-multiple-apps"></a>Problemas executando várias aplicações

Pode encontrar disponibilidade de CPU e limites a serem fixados em todas as aplicações. Para mitigar:
- Crie um aglomerado de cinco nós.
- Reduza o uso de CPU em serviços em toda a app que é implementada. Por exemplo, no ficheiro service.yaml do seu serviço, mude de `cpu: 1.0` para `cpu: 0.5`

Várias aplicações não podem ser implantadas para um aglomerado de um nó. Para mitigar:
- Use um cluster de cinco nós ao implementar várias aplicações para um cluster local.
- Remova as aplicações que não está a testar.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>Vs Tooling tem suporte limitado para recipientes Windows

A ferramenta Visual Studio apenas suporta a implementação de Recipientes Windows com uma versão base OS do Windows Server 1709 e 1803 hoje. 

## <a name="feature-gaps-and-other-known-issues"></a>Lacunas de recurso e outras questões conhecidas

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Depois de implementar a minha aplicação, o recurso de rede associado a ele não tem um endereço IP

Há uma questão conhecida em que o endereço IP não fica disponível imediatamente. Verifique o estado do recurso de rede em poucos minutos para ver o endereço IP associado.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>A minha aplicação não consegue aceder ao recurso de rede/volume certo

No seu modelo de aplicação, utilize o ID completo de recursos para redes e volumes para poder aceder ao recurso associado. Aqui está um exemplo da amostra de arranque rápido:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Quando eu escalar, todos os meus recipientes são afetados, incluindo os de corrida

Isto é um inseto e está a ser implementada uma correção.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Malha de Tecido de Serviço, leia a [visão geral.](service-fabric-mesh-overview.md)
