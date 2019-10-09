---
title: Perguntas comuns sobre a malha de Service Fabric do Azure | Microsoft Docs
description: Saiba mais sobre perguntas e respostas frequentes para a malha de Service Fabric do Azure.
services: service-fabric-mesh
keywords: ''
author: chackdan
ms.author: pepogors
ms.date: 4/23/2019
ms.topic: troubleshooting
ms.service: service-fabric-mesh
manager: jeanpaul.connock
ms.openlocfilehash: edd30dc8799ae9e5410ebc862574d632d09b9483
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168688"
---
# <a name="commonly-asked-service-fabric-mesh-questions"></a>Perguntas frequentes sobre a malha Service Fabric

O Azure Service Fabric Mesh é um serviço totalmente gerido que permite aos programadores implementar aplicações de microsserviços sem gerir máquinas virtuais, armazenamento ou redes. Este artigo tem respostas para as perguntas mais frequentes.

## <a name="how-do-i-report-an-issue-or-ask-a-question"></a>Como fazer relatar um problema ou fazer uma pergunta?

Faça perguntas, obtenha respostas de engenheiros da Microsoft e relate problemas no [repositório GitHub Service-Fabric-malha-Preview](https://aka.ms/sfmeshissues).

## <a name="quota-and-cost"></a>Cota e custo

### <a name="what-is-the-cost-of-participating-in-the-preview"></a>Qual é o custo de participação na versão prévia?

No momento, não há encargos para implantar aplicativos ou contêineres na visualização de malha. Verifique se há atualizações no pode ser habilitado para a cobrança. No entanto, incentivamos você a excluir os recursos implantados e não deixá-los em execução, a menos que esteja testando-os ativamente.

### <a name="is-there-a-quota-limit-of-the-number-of-cores-and-ram"></a>Há um limite de cota do número de núcleos e RAM?

Sim. As cotas para cada assinatura são:

- Número de aplicativos: 5
- Núcleos por aplicativo: 12
- Total de RAM por aplicativo: 48 GB
- Pontos de extremidade de rede e entrada: 5
- Volumes do Azure que você pode anexar: 10
- Número de réplicas de serviço: 3
- O maior contêiner que você pode implantar é limitado a 4 núcleos e 16 GB de RAM.
- Você pode alocar núcleos parciais para seus contêineres em incrementos de 0,5 núcleos, até um máximo de 6 núcleos.

### <a name="how-long-can-i-leave-my-application-deployed"></a>Por quanto tempo posso deixar meu aplicativo implantado?

No momento, limitamos o tempo de vida de um aplicativo a dois dias. Isso é para maximizar o uso dos núcleos gratuitos alocados para a versão prévia. Como resultado, você só tem permissão para executar uma determinada implantação continuamente por 48 horas, após o momento em que ela será desligada.

Se você vir isso acontecer, poderá validar que o sistema o desliga executando o comando `az mesh app show` na CLI do Azure. Verifique se ele retorna `"status": "Failed", "statusDetails": "Stopped resource due to max lifetime policies for an application during preview. Delete the resource to continue."` 

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

Para excluir o grupo de recursos, use o comando `az group delete <nameOfResourceGroup>`.

## <a name="deployments"></a>Implementações

### <a name="what-container-images-are-supported"></a>Quais imagens de contêiner têm suporte?

Se você estiver desenvolvendo em uma máquina de atualização do Windows Outono Creators (versão 1709), só poderá usar imagens do Docker da versão 1709 do Windows.

Se você estiver desenvolvendo em uma máquina de atualização do Windows 10 de abril de 2018 (versão 1803), poderá usar as imagens do Windows versão 1709 ou do Windows versão 1803.

As imagens do sistema operacional do contêiner a seguir podem ser usadas para implantar serviços:

- Windows-windowsservercore e coserver
    - Windows Server 1709
    - Windows Server versão 1803
    - Windows Server 1809
    - Windows Server 2019 LTSC
- Linux
    - Sem limitações conhecidas

> [!NOTE]
> As ferramentas do Visual Studio para malha ainda não dão suporte à implantação em contêineres do Windows Server 2019 e 1809.

### <a name="what-types-of-applications-can-i-deploy"></a>Que tipos de aplicativos posso implantar? 

Você pode implantar qualquer coisa que seja executada em contêineres que caibam dentro das restrições colocadas em um recurso de aplicativo (veja acima para obter mais informações sobre cotas). Se detectarmos que você está usando a malha para executar cargas de trabalho ilegais ou abusando o sistema (ou seja, mineração), reservamos o direito de encerrar suas implantações e impedir que sua assinatura seja executada no serviço. Entre em contato conosco se tiver alguma dúvida sobre como executar uma carga de trabalho específica. 

## <a name="developer-experience-issues"></a>Problemas de experiência do desenvolvedor

### <a name="dns-resolution-from-a-container-doesnt-work"></a>A resolução DNS de um contêiner não funciona

As consultas de DNS de saída de um contêiner para o serviço DNS Service Fabric podem falhar em determinadas circunstâncias. Isso está sendo investigado. Para atenuar:

- Use a atualização do Windows Outono Creators (versão 1709) ou superior como sua imagem de contêiner base.
- Se o nome do serviço sozinho não funcionar, tente o nome totalmente qualificado: ServiceName.ApplicationName.
- No arquivo do Docker para seu serviço, adicione `EXPOSE <port>`, em que porta é a porta na qual você está expondo seu serviço. Por exemplo:

```Dockerfile
EXPOSE 80
```

### <a name="dns-does-not-work-the-same-as-it-does-for-service-fabric-development-clusters-and-in-mesh"></a>O DNS não funciona da mesma forma que para clusters de desenvolvimento Service Fabric e em malha

Talvez seja necessário referenciar serviços de forma diferente em seu cluster de desenvolvimento local do que na malha do Azure.

No seu cluster de desenvolvimento local, use `{serviceName}.{applicationName}`. Na malha de Service Fabric do Azure, use `{servicename}`. 

Atualmente, a malha do Azure não oferece suporte à resolução de DNS entre aplicativos.

Para outros problemas de DNS conhecidos com a execução de um cluster de desenvolvimento Service Fabric no Windows 10, consulte: [Depurar contêineres do Windows](/azure/service-fabric/service-fabric-how-to-debug-windows-containers) e [problemas de DNS conhecidos](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#known-issues).

### <a name="networking"></a>Redes

O NAT de rede do infabric pode desaparecer ao usar o aplicativo em execução no computador local. Para diagnosticar se isso ocorreu, execute o seguinte em um prompt de comando:

`docker network ls` e observe se `servicefabric_nat` está listado.  Caso contrário, execute o seguinte comando: `docker network create -d=nat --subnet 10.128.0.0/24 --gateway 10.128.0.1 servicefabric_nat`

Isso resolverá o problema mesmo que o aplicativo já esteja implantado localmente e em um estado não íntegro.

### <a name="issues-running-multiple-apps"></a>Problemas ao executar vários aplicativos

Você pode encontrar a disponibilidade da CPU e os limites sendo corrigidos em todos os aplicativos. Para atenuar:
- Crie um cluster de cinco nós.
- Reduza o uso da CPU em serviços em todo o aplicativo implantado. Por exemplo, no arquivo Service. YAML do serviço, altere `cpu: 1.0` para `cpu: 0.5`

Vários aplicativos não podem ser implantados em um cluster de um nó. Para atenuar:
- Use um cluster de cinco nós ao implantar vários aplicativos em um cluster local.
- Remova os aplicativos que você não está testando no momento.

### <a name="vs-tooling-has-limited-support-for-windows-containers"></a>As ferramentas do VS têm suporte limitado para contêineres do Windows

As ferramentas do Visual Studio dão suporte apenas à implantação de contêineres do Windows com uma versão base do sistema operacional do Windows Server 1709 e 1803 hoje. 

## <a name="feature-gaps-and-other-known-issues"></a>Lacunas de recursos e outros problemas conhecidos

### <a name="after-deploying-my-application-the-network-resource-associated-with-it-does-not-have-an-ip-address"></a>Depois de implantar meu aplicativo, o recurso de rede associado a ele não tem um endereço IP

Há um problema conhecido em que o endereço IP não fica disponível imediatamente. Verifique o status do recurso de rede em alguns minutos para ver o endereço IP associado.

### <a name="my-application-fails-to-access-the-right-networkvolume-resource"></a>Meu aplicativo não consegue acessar o recurso de rede/volume certo

No modelo de aplicativo, use a ID de recurso completo para redes e volumes para poder acessar o recurso associado. Aqui está um exemplo do exemplo de início rápido:

```json
"networkRefs": [
    {
    "name":  "[resourceId('Microsoft.ServiceFabric/networks', 'SbzVotingNetwork')]" 
    }
]
```

### <a name="when-i-scale-out-all-of-my-containers-are-affected-including-running-ones"></a>Quando expandi, todos os meus contêineres são afetados, incluindo a execução de

Esse é um bug e uma correção está sendo implementada.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a malha de Service Fabric, leia a [visão geral](service-fabric-mesh-overview.md).
