---
title: Ver registos de contentores no Azure Service Fabric | Documentos da Microsoft
description: Descreve como ver os registos de contentor para um serviços de contentor do Service Fabric em execução com o Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: aljo
ms.openlocfilehash: 801d96dc154c1d7144217fe0f70c179c086690f0
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/25/2019
ms.locfileid: "56804506"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Ver registos para um serviço de contentor do Service Fabric
O Azure Service Fabric é um orquestrador de contentores e oferece suporte a ambos [contentores do Linux e Windows](service-fabric-containers-overview.md).  Este artigo descreve como visualizar registos de contentor de um serviço de contentor em execução ou um contentor inativo para que possa diagnosticar e resolver problemas.

## <a name="access-the-logs-of-a-running-container"></a>Aceder aos registos de um contentor em execução
Registos de contentor podem ser acessados usando [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Num navegador da web, abra Service Fabric Explorer a partir do ponto final de gestão do cluster ao navegar para [ http://mycluster.region.cloudapp.azure.com:19080/Explorer ](http://mycluster.region.cloudapp.azure.com:19080/Explorer).  

Registos de contentor estão localizados no nó de cluster que a instância do serviço de contentor é executado. Por exemplo, obter os registos do contentor web front-end do [aplicação de voto do Linux do exemplo](service-fabric-quickstart-containers-linux.md). Na vista de árvore, expanda **Cluster**>**aplicativos**>**VotingType**>**fabric: / Voting / azurevotefront**.  Em seguida, expanda a partição (d1aa737e-f22a-e347-be16-eec90be24bc1, neste exemplo) e ver que o contentor está em execução no nó de cluster *_lnxvm_0*.

Na vista de árvore, encontrar o pacote do código sobre o *_lnxvm_0* nó ao expandir **nós**>**_lnxvm_0**>**fabric: / Voting**  > **azurevotfrontPkg**>**pacotes do código**>**código**.  Em seguida, selecione o **registos de contentor** opção para apresentar os registos de contentor.

![Plataforma do Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Aceder aos registos de um contentor inativo ou falhado
A partir de v6.2, pode também obter os registos para um contentor inativo ou falhado através de [REST APIs](/rest/api/servicefabric/sfclient-index) ou [Service Fabric CLI (SFCTL)](service-fabric-cli.md) comandos.

### <a name="set-container-retention-policy"></a>Definir a política de retenção de contentores
Para ajudar a diagnosticar falhas no arranque de contentores, o Service Fabric (versão 6.1 ou superior) suporta a retenção de contentores que terminaram ou cujo arranque falhou. Esta política pode ser definida no ficheiro **ApplicationManifest.xml**, conforme mostrado no fragmento seguinte:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A definição **ContainersRetentionCount** especifica o número de contentores que vão ser retidos quando falham. Se não for especificado um valor negativo, todos os contentores com falhas serão mantidos. Quando o **ContainersRetentionCount** atributo não for especificado, não existem contentores serão retidos. O atributo **ContainersRetentionCount** também suporta Parâmetros de Aplicação, para que os utilizadores possam especificar valores diferentes para clusters de teste e produção. Utilize restrições de posicionamento para segmentar o serviço de contentores para um nó particular ao utilizar esta funcionalidade para impedir a passagem do serviço de contentores para outros nós. Quaisquer contentores retidos que utilizem esta funcionalidade têm de ser removidos manualmente.

A definição **RunInteractive** corresponde do Docker `--interactive` e `tty` [sinalizadores](https://docs.docker.com/engine/reference/commandline/run/#options). Quando esta definição está definida como true no arquivo de manifesto, esses sinalizadores são usados para iniciar o contentor.  

### <a name="rest"></a>REST
Utilize o [obter contentores implementados no nó Logs](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operação para obter os registos para um contentor falhado. Especifique o nome do nó que o contentor foi a ser executado, o nome da aplicação, nome do manifesto do serviço e o nome do pacote de código.  Especificar `&Previous=true`. A resposta irá conter os registos de contentor para o contentor de mensagens não da instância de pacote do código.

O URI do pedido tem o seguinte formato:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Pedido de exemplo:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

Corpo de resposta 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Utilize o [sfctl get-container-registos do serviço](service-fabric-sfctl-service.md) comando para obter os registos para um contentor falhado.  Especifique o nome do nó que o contentor foi a ser executado, o nome da aplicação, nome do manifesto do serviço e o nome do pacote de código. Especifique o `--previous` sinalizador.  A resposta irá conter os registos de contentor para o contentor de mensagens não da instância de pacote do código.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Resposta:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Passos Seguintes
- Trabalhar com o [criar um tutorial de aplicação de contentor do Linux](service-fabric-tutorial-create-container-images.md).
- Saiba mais sobre [Service Fabric e contentores](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
