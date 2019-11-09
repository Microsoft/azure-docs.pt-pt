---
title: Exibir logs de contêineres no Azure Service Fabric | Microsoft Docs
description: Descreve como exibir logs de contêiner para um Service Fabric de serviços de contêiner em execução usando Service Fabric Explorer.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/15/2018
ms.author: atsenthi
ms.openlocfilehash: fd1787318e8573183293ddd832a11cf8cfe09cf2
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73832611"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Exibir logs para um serviço de contêiner de Service Fabric
O Azure Service Fabric é um orquestrador de contêiner e dá suporte a [contêineres do Linux e do Windows](service-fabric-containers-overview.md).  Este artigo descreve como exibir logs de contêiner de um serviço de contêiner em execução ou um contêiner inativo para que você possa diagnosticar e solucionar problemas.

## <a name="access-the-logs-of-a-running-container"></a>Acessar os logs de um contêiner em execução
Os logs de contêiner podem ser acessados usando [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Em um navegador da Web, abra Service Fabric Explorer do ponto de extremidade de gerenciamento do cluster navegando até `http://mycluster.region.cloudapp.azure.com:19080/Explorer`.  

Os logs de contêiner estão localizados no nó de cluster em que a instância do serviço de contêiner está sendo executada. Por exemplo, obtenha os logs do contêiner de front-end da Web do [aplicativo de exemplo de votação do Linux](service-fabric-quickstart-containers-linux.md). No modo de exibição de árvore, expanda **Cluster**>**aplicativos**>**vototype**>**Fabric:/votação/azurevotefront**.  Em seguida, expanda a partição (d1aa737e-f22a-e347-be16-eec90be24bc1, neste exemplo) e veja que o contêiner está sendo executado no nó do cluster *_lnxvm_0*.

No modo de exibição de árvore, encontre o pacote de códigos no nó de *_lnxvm_0* expandindo **nós**> **_Lnxvm_0**>**Fabric:/votação**>**azurevotfrontPkg**>**código de** **pacotes**>código.  Em seguida, selecione a opção **logs de contêiner** para exibir os logs de contêiner.

![Plataforma do Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Acessar os logs de um contêiner inoperante ou falhado
A partir do v 6.2, você também pode buscar os logs para um contêiner inativo ou que falhou usando [APIs REST](/rest/api/servicefabric/sfclient-index) ou comandos [SFCTL (CLI do Service Fabric)](service-fabric-cli.md) .

### <a name="set-container-retention-policy"></a>Definir a política de retenção de contentores
Para ajudar a diagnosticar falhas no arranque de contentores, o Service Fabric (versão 6.1 ou superior) suporta a retenção de contentores que terminaram ou cujo arranque falhou. Esta política pode ser definida no ficheiro **ApplicationManifest.xml**, conforme mostrado no fragmento seguinte:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A definição **ContainersRetentionCount** especifica o número de contentores que vão ser retidos quando falham. Se não for especificado um valor negativo, todos os contentores com falhas serão mantidos. Quando o atributo **ContainersRetentionCount** não for especificado, nenhum contêiner será retido. O atributo **ContainersRetentionCount** também suporta Parâmetros de Aplicação, para que os utilizadores possam especificar valores diferentes para clusters de teste e produção. Utilize restrições de posicionamento para segmentar o serviço de contentores para um nó particular ao utilizar esta funcionalidade para impedir a passagem do serviço de contentores para outros nós. Quaisquer contentores retidos que utilizem esta funcionalidade têm de ser removidos manualmente.

A configuração **RunInteractive** corresponde aos [sinalizadores](https://docs.docker.com/engine/reference/commandline/run/#options)de `--interactive` e `tty` do Docker. Quando essa configuração é definida como true no arquivo de manifesto, esses sinalizadores são usados para iniciar o contêiner.  

### <a name="rest"></a>REST
Use a operação [obter logs de contêiner implantados no nó](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) para obter os logs de um contêiner com falha. Especifique o nome do nó no qual o contêiner estava em execução, o nome do aplicativo, o nome do manifesto do serviço e o nome do pacote de códigos.  Especifique `&Previous=true`. A resposta conterá os logs de contêiner para o contêiner inativo da instância do pacote de códigos.

O URI de solicitação tem o seguinte formato:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Exemplo de solicitação:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

corpo da resposta 200:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Service Fabric (SFCTL)
Use o comando [sfctl do serviço Get-container-logs](service-fabric-sfctl-service.md) para buscar os logs de um contêiner que falhou.  Especifique o nome do nó no qual o contêiner estava em execução, o nome do aplicativo, o nome do manifesto do serviço e o nome do pacote de códigos. Especifique o sinalizador de `--previous`.  A resposta conterá os logs de contêiner para o contêiner inativo da instância do pacote de códigos.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Resposta:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Passos seguintes
- Trabalhe no [tutorial criar um aplicativo de contêiner do Linux](service-fabric-tutorial-create-container-images.md).
- Saiba mais sobre [Service Fabric e contêineres](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
