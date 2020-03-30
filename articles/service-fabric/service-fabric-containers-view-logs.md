---
title: Ver registos de contentores em Tecido de Serviço Azure
description: Descreve como visualizar os registos de contentores para um serviço de transporte de serviços de transporte de serviço saem do Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458178"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Ver registos para um serviço de contentores de tecido de serviço
O Azure Service Fabric é um orquestrador de contentores e suporta [recipientes Linux e Windows.](service-fabric-containers-overview.md)  Este artigo descreve como ver os registos de contentores de um serviço de contentores em funcionamento ou um contentor morto para que possa diagnosticar e resolver problemas.

## <a name="access-the-logs-of-a-running-container"></a>Aceda aos registos de um contentor em funcionamento
Os registos de contentores podem ser acedidos através do [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Num navegador web, abra o Service Fabric Explorer a partir `http://mycluster.region.cloudapp.azure.com:19080/Explorer`do ponto final de gestão do cluster navegando até .  

Os registos de contentores estão localizados no nó do cluster que a instância de serviço do contentor está a funcionar. Como exemplo, obtenha os registos do recipiente frontal web da aplicação da amostra de [voto Linux](service-fabric-quickstart-containers-linux.md). Na vista da árvore, expandir**Aplicações**> **de Cluster**>Tecido Do Tipo>**de Voto:/Votação/frente de voto**.**VotingType**  Em seguida, expanda a partição (d1aa737e-f22a-e347-be16-eec90be24bc1, neste exemplo) e certifique-se de que o recipiente está em funcionamento no nó de aglomerado *_lnxvm_0*.

Na vista da árvore, encontre o pacote de código no nó *_lnxvm_0* expandindo os **nós**>**_lnxvm_0**>**tecido:/Código**>de**código**>**code****azurevotfrontPkg**>de votação.  Em seguida, selecione a opção registos de **contentores** para exibir os registos do recipiente.

![Plataforma do Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Aceda aos troncos de um contentor morto ou acidentado
A partir do v6.2, também pode recolher os registos de um recipiente morto ou acidentado utilizando comandos [REST APIs](/rest/api/servicefabric/sfclient-index) ou [Service Fabric CLI (SFCTL).](service-fabric-cli.md)

### <a name="set-container-retention-policy"></a>Definir a política de retenção de contentores
Para ajudar a diagnosticar falhas no arranque de contentores, o Service Fabric (versão 6.1 ou superior) suporta a retenção de contentores que terminaram ou cujo arranque falhou. Esta política pode ser definida no ficheiro **ApplicationManifest.xml**, conforme mostrado no fragmento seguinte:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A definição **ContainersRetentionCount** especifica o número de contentores que vão ser retidos quando falham. Se não for especificado um valor negativo, todos os contentores com falhas serão mantidos. Quando o atributo Do **Count de retenções** de contentores não for especificado, nenhum recipiente será retido. O atributo **ContainersRetentionCount** também suporta Parâmetros de Aplicação, para que os utilizadores possam especificar valores diferentes para clusters de teste e produção. Utilize restrições de posicionamento para segmentar o serviço de contentores para um nó particular ao utilizar esta funcionalidade para impedir a passagem do serviço de contentores para outros nós. Quaisquer contentores retidos que utilizem esta funcionalidade têm de ser removidos manualmente.

A definição **RunInteractive** corresponde `--interactive` ao `tty` Docker's e [às bandeiras.](https://docs.docker.com/engine/reference/commandline/run/#options) Quando esta definição for definida como verdadeira no ficheiro manifesto, estas bandeiras são utilizadas para iniciar o recipiente.  

### <a name="rest"></a>REST
Utilize os registos de [contentores implantados na](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operação Node para obter os registos para um recipiente acidentado. Especifique o nome do nó em que o recipiente estava em funcionamento, nome da aplicação, nome manifesto de serviço e o nome do pacote de código.  Especifique `&Previous=true`. A resposta conterá os registos do recipiente para o recipiente morto da instância do pacote de código.

O pedido URI tem o seguinte formulário:

```
/Nodes/{nodeName}/$/GetApplications/{applicationId}/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName={ServiceManifestName}&CodePackageName={CodePackageName}&Previous={Previous}
```

Pedido de exemplo:
```
GET http://localhost:19080/Nodes/_Node_0/$/GetApplications/SimpleHttpServerApp/$/GetCodePackages/$/ContainerLogs?api-version=6.2&ServiceManifestName=SimpleHttpServerSvcPkg&CodePackageName=Code&Previous=true  
```

200 Corpo de resposta:
```json
{   "Content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" } 
```

### <a name="service-fabric-sfctl"></a>Tecido de serviço (SFCTL)
Utilize o comando de registos de porta-contentores do [serviço sfctl](service-fabric-sfctl-service.md) para recolher os troncos para um recipiente acidentado.  Especifique o nome do nó em que o recipiente estava em funcionamento, nome da aplicação, nome manifesto de serviço e o nome do pacote de código. Especifique a `--previous` bandeira.  A resposta conterá os registos do recipiente para o recipiente morto da instância do pacote de código.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Resposta:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Passos seguintes
- Trabalhe através do tutorial de aplicação de [contentores Create a Linux](service-fabric-tutorial-create-container-images.md).
- Saiba mais sobre tecido de [serviço e recipientes](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
