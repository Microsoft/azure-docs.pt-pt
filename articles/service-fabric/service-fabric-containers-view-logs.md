---
title: Ver registos de contentores no Tecido de Serviço Azure
description: Descreve como visualizar registos de contentores para um serviço de contentores de tecido de serviço em funcionamento utilizando o Service Fabric Explorer.
ms.topic: conceptual
ms.date: 05/15/2018
ms.openlocfilehash: c47a408b272f95dbfcf3d791c644bfeb52254a72
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "75458178"
---
# <a name="view-logs-for-a-service-fabric-container-service"></a>Ver registos para um serviço de contentores de tecido de serviço
A Azure Service Fabric é uma orquestradora de contentores e suporta [recipientes Linux e Windows.](service-fabric-containers-overview.md)  Este artigo descreve como visualizar os registos de contentores de um serviço de contentores em funcionamento ou de um recipiente morto para que possa diagnosticar e resolver problemas.

## <a name="access-the-logs-of-a-running-container"></a>Aceda aos registos de um recipiente em execução
Os registos de contentores podem ser acedidos através [do Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).  Num navegador web, abra o Service Fabric Explorer a partir do ponto final de gestão do cluster navegando para `http://mycluster.region.cloudapp.azure.com:19080/Explorer` .  

Os troncos de contentores estão localizados no nó de aglomerado em que a instância de serviço do contentor está em funcionamento. Como exemplo, obtenha os registos do recipiente frontal web da [aplicação linux voting.](service-fabric-quickstart-containers-linux.md) Na vista da árvore, expandir **o cluster** > **applications** > **voteType** > **tecido:/Votação/azurevotefront**.  Em seguida, expanda a partição (d1aaa737e-f22a-e347-be16-eec90be24bc1, neste exemplo) e certifique-se de que o recipiente está a funcionar no nó de cluster *_lnxvm_0*.

Na vista da árvore, encontre o pacote de código no nó *_lnxvm_0* expandindo **os nós** > **_lnxvm_0** > **tecido:/Código** > de pacotes **azurevotfrontPkg** >  > .  Em seguida, selecione a opção **Registos de Contentores** para visualizar os registos do contentor.

![Plataforma do Service Fabric][Image1]

## <a name="access-the-logs-of-a-dead-or-crashed-container"></a>Aceda aos registos de um contentor morto ou acidentado
A partir de v6.2, também pode recolher os registos de um recipiente morto ou acidentado utilizando comandos [REST APIs](/rest/api/servicefabric/sfclient-index) ou [Service Fabric CLI (SFCTL).](service-fabric-cli.md)

### <a name="set-container-retention-policy"></a>Definir a política de retenção de contentores
Para ajudar a diagnosticar falhas no arranque de contentores, o Service Fabric (versão 6.1 ou superior) suporta a retenção de contentores que terminaram ou cujo arranque falhou. Esta política pode ser definida no ficheiro **ApplicationManifest.xml**, conforme mostrado no fragmento seguinte:
```xml
 <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" ContainersRetentionCount="2"  RunInteractive="true"> 
 ```

A definição **ContainersRetentionCount** especifica o número de contentores que vão ser retidos quando falham. Se não for especificado um valor negativo, todos os contentores com falhas serão mantidos. Quando o atributo **ContainersRetentionCount** não for especificado, não serão retidos contentores. O atributo **ContainersRetentionCount** também suporta Parâmetros de Aplicação, para que os utilizadores possam especificar valores diferentes para clusters de teste e produção. Utilize restrições de posicionamento para segmentar o serviço de contentores para um nó particular ao utilizar esta funcionalidade para impedir a passagem do serviço de contentores para outros nós. Quaisquer contentores retidos que utilizem esta funcionalidade têm de ser removidos manualmente.

A definição **RunInteractive** corresponde à do Docker e às `--interactive` `tty` [bandeiras.](https://docs.docker.com/engine/reference/commandline/run/#options) Quando esta definição é definida como verdadeira no ficheiro manifesto, estas bandeiras são utilizadas para iniciar o recipiente.  

### <a name="rest"></a>REST
Utilize os [registos de contentores implantados na](/rest/api/servicefabric/sfclient-api-getcontainerlogsdeployedonnode) operação nól para obter os registos de um recipiente acidentado. Especificar o nome do nó em que o contentor estava a funcionar, o nome da aplicação, o nome manifesto de serviço e o nome do pacote de código.  Especifique `&Previous=true`. A resposta conterá os registos dos contentores para o recipiente morto da instância do pacote de código.

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
Utilize o comando [de registos de contentores de serviço sfctl](service-fabric-sfctl-service.md) para recolher os registos de um recipiente acidentado.  Especificar o nome do nó em que o contentor estava a funcionar, o nome da aplicação, o nome manifesto de serviço e o nome do pacote de código. Especifique a `--previous` bandeira.  A resposta conterá os registos dos contentores para o recipiente morto da instância do pacote de código.

```
sfctl service get-container-logs --node-name _Node_0 --application-id SimpleHttpServerApp --service-manifest-name SimpleHttpServerSvcPkg --code-package-name Code –-previous
```
Resposta:
```json
{   "content": "Exception encountered: System.Net.Http.HttpRequestException: Response status code does not indicate success: 500 (Internal Server Error).\r\n\tat System.Net.Http.HttpResponseMessage.EnsureSuccessStatusCode()\r\n" }
```

## <a name="next-steps"></a>Passos seguintes
- Trabalhe através do tutorial de aplicação de [recipientes Create a Linux](service-fabric-tutorial-create-container-images.md).
- Saiba mais sobre [o Tecido de Serviço e os contentores](service-fabric-containers-overview.md)

[Image1]: media/service-fabric-containers-view-logs/view-container-logs-sfx.png
