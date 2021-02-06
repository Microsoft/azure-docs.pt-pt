---
title: Questões comuns para a malha de tecido de serviço Azure
description: Saiba mais sobre perguntas e respostas comumente feitas para a malha de tecido de serviço Azure.
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.openlocfilehash: 8e53ab0ae4cc463bea8a6a8cb6d339f94fdcac6d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/06/2021
ms.locfileid: "99626041"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Perguntas de malha de tecido de serviço comumente

> [!IMPORTANT]
> A pré-estreia da Malha de Tecido de Serviço Azure foi reformada. As novas implementações deixarão de ser permitidas através da API de malha de malha de tecido de serviço. O apoio às implementações existentes continuará até 28 de abril de 2021.
> 
> Para mais detalhes, consulte [a pré-reforma da malha de malha de malha de tecido de serviço Azure.](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo tem respostas para perguntas comumente feitas.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Como reporto um problema ou faço uma pergunta?

Faça perguntas, obtenha respostas dos engenheiros da Microsoft e reporte problemas no [repo GitHub de rede de tecido de serviço.](https://aka.ms/sfmeshissues)

## <a name="quota-and-cost"></a>Quota e Custo

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Qual é o custo de participar na pré-estreia?

Atualmente, não existem encargos para a colocação de aplicações ou contentores na pré-visualização da Malha. Por favor, atenção para atualizações em maio para ativar a faturação. No entanto, encorajamo-lo a apagar os recursos que implementa e a não os deixar a funcionar a menos que esteja a testá-los ativamente.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Existe um limite de quota do número de núcleos e RAM?

Sim. As quotas para cada subscrição são:

- Número de candidaturas: 5
- Núcleos por aplicação: 12
- RAM total por aplicação: 48 GB
- Pontos finais da Rede e ingress: 5
- Volumes Azure que pode anexar: 10
- Número de réplicas de serviço: 3
- O maior contentor que pode implantar está limitado a 4 núcleos e 16GB de RAM.
- Pode alocar núcleos parciais aos seus contentores em incrementos de 0,5 núcleos, até um máximo de 6 núcleos.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Quanto tempo posso deixar a minha candidatura implantada?

Atualmente, limitámos a vida útil de uma candidatura a dois dias. Isto é para maximizar a utilização dos núcleos gratuitos atribuídos à pré-visualização. Como resultado, só é permitido executar uma determinada implantação continuamente durante 48 horas, após o qual será desligado.

Se vir isto acontecer, pode validar que o sistema o desliga, executando o `az mesh app show` comando no Azure CLI. Verifique se devolve `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

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

Para eliminar o grupo de recursos, utilize o `az group delete <nameOfResourceGroup>` comando.

## <a name="deployments"></a>Implementações

### <a name="what-container-images-are-supported"></a>Que imagens de contentores são suportadas?

Se estiver a desenvolver uma máquina de Atualização de Criadores de outono do Windows (versão 1709), só pode utilizar imagens de estivadores da versão 1709 do Windows.

Se estiver a desenvolver uma atualização do Windows 10 abril 2018 (versão 1803), pode utilizar imagens de estivadores do Windows 1709 ou da versão 1803 do Windows.

As seguintes imagens de sistema operativo podem ser utilizadas para a implantação de serviços:
- Windows - windowsservercore e nanoserver
    - Servidor Windows 1709
    - Servidor Windows 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Sem limitações conhecidas

> [!NOTE]
> A ferramenta visual Studio para malha ainda não suporta a implantação em recipientes Windows Server 2019 e 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Que tipo de aplicações posso implementar? 

Pode implantar qualquer coisa que seja executado em contentores que se encaixem dentro das restrições colocadas num recurso de aplicação (ver acima para obter mais informações sobre quotas). Se detetarmos que está a usar a Malha para executar cargas de trabalho ilegais ou abusar do sistema (isto é, mineração), então reservamo-nos o direito de terminar as suas implementações e bloquear a sua subscrição de funcionar no serviço. Por favor, contacte-nos se tiver alguma dúvida sobre a execução de uma carga de trabalho específica. 

## <a name="developer-experience-issues"></a>Problemas de experiência do desenvolvedor

### <a name="dns-resolution-from-a-container-doesnt-work"></a>Resolução de DNS de um contentor não funciona

As consultas de DNS de saída de um contentor para o serviço DNS de Tecido de Serviço podem falhar em determinadas circunstâncias. Isto está a ser investigado. Para mitigar:

- Utilize a atualização do Windows Fall Creators (versão 1709) ou superior à imagem do contentor base.
- Se o nome de serviço por si só não funcionar, experimente o nome totalmente qualificado: Nome de serviço.Nome de aplicação.
- No ficheiro Docker para o seu serviço, adicione `EXPOSE <port>` onde é a porta onde está a porta onde está a expor o seu serviço. Por exemplo:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>O DNS não funciona da mesma forma que funciona para os clusters de desenvolvimento de Tecidos de Serviço e na Malha

Poderá necessitar de serviços de referência de forma diferente no seu cluster de desenvolvimento local do que na Malha Azure.

No seu uso de cluster de desenvolvimento `{serviceName}.{applicationName}` local. Na malha de tecido de serviço Azure, utilize `{servicename}` . 

A Azure Mesh não suporta atualmente a resolução de DNS em todas as aplicações.

Para outros problemas conhecidos do DNS com a execução de um cluster de desenvolvimento de tecido de serviço no Windows 10, consulte: [Recipientes Debug Windows](../service-fabric/service-fabric-how-to-debug-windows-containers.md) e [problemas conhecidos de DNS](../service-fabric/service-fabric-dnsservice.md#known-issues).

### <a name="networking"></a>Rede

O ServiceFabric network NAT pode desaparecer durante a utilização da sua aplicação na sua máquina local. Para diagnosticar se isto aconteceu, executar o seguinte a partir de um pedido de comando:

`docker network ls` e note se `servicefabric_nat` está listado.  Caso contrário, executará o seguinte comando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Isto abordará o problema mesmo que a app já esteja implementada localmente e em estado pouco saudável.

### <a name="issues-running-multiple-apps"></a>Problemas com várias aplicações

Poderá encontrar disponibilidade de CPU e limites a serem fixados em todas as aplicações. Para mitigar:
- Criar um aglomerado de cinco nós.
- Reduza o uso do CPU em serviços em toda a aplicação que é implementada. Por exemplo, no ficheiro serviço.yaml do seu serviço, mude `cpu: 1.0` para `cpu: 0.5`

Várias aplicações não podem ser implantadas num aglomerado de um nó. Para mitigar:
- Use um cluster de cinco nós ao implementar várias aplicações para um cluster local.
- Remova as aplicações que não está a testar atualmente.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>A VS Tooling tem suporte limitado para contentores Windows

A ferramenta Visual Studio suporta apenas a implementação de Contentores Windows com uma versão base de SISTEMA do Windows Server 1709 e 1803 hoje. 

## <a name="feature-gaps-and-other-known-issues"></a>Lacunas de recursos e outras questões conhecidas

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Depois de implementar a minha aplicação, o recurso de rede associado a ele não tem um endereço IP

Há uma questão conhecida em que o endereço IP não fica disponível imediatamente. Verifique o estado do recurso de rede em poucos minutos para ver o endereço IP associado.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>A minha aplicação não consegue aceder ao recurso rede/volume certo

No seu modelo de aplicação, utilize o ID de recursos completo para redes e volumes para poder aceder ao recurso associado. Aqui está um exemplo da amostra de arranque rápido:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Quando eu escalono para fora, todos os meus contentores são afetados, incluindo os de execução

Isto é um bug e está a ser implementada uma correção.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Malha de Tecido de Serviço, leia a [visão geral.](service-fabric-mesh-overview.md)
