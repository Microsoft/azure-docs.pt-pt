---
title: O serviço de DNS de recursos de infraestrutura do serviço do Azure | Documentos da Microsoft
description: Utilize o serviço de dns do Service Fabric para detetar os microsserviços de dentro do cluster.
services: service-fabric
documentationcenter: .net
author: msfussell
manager: timlt
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: msfussell
ms.openlocfilehash: b49df76bc5b9c4d5f469e702b65ffd5762e49a39
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57455841"
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O serviço DNS é um serviço de sistema opcional que pode ativar no seu cluster para detetar a outros serviços que utilizam o protocolo DNS. 

Vários serviços, especialmente em contentores serviços, são endereçáveis através de um URL já existente. Ser capaz de resolver esses serviços a utilizar o protocolo DNS padrão, em vez do protocolo de serviço de nomenclatura do Service Fabric, é desejável. O serviço DNS permite-lhe mapear nomes DNS para um nome de serviço e, por conseguinte, resolver endereços IP do ponto final. Essa funcionalidade mantém a portabilidade de serviços em contentores em diferentes plataformas e pode tornar "lift- and -shift" cenários mais fácil, permitindo-lhe utilizar existente do serviço URLs em vez de o ter reescrever o código para tirar partido do serviço de nomenclatura. 

O serviço DNS mapeia nomes DNS para nomes de serviço, que por sua vez, são resolvidos pelo serviço de nomenclatura para retornar o ponto final de serviço. O nome DNS para o serviço está disponível no momento da criação. O diagrama seguinte mostra o funcionamento do serviço DNS para serviços sem estado.

![pontos finais de serviço](./media/service-fabric-dnsservice/stateless-dns.png)

A partir do Service Fabric versão 6.3, o protocolo de DNS de recursos de infraestrutura do serviço tiver sido expandido para incluir um esquema de endereçamento particionados serviços com estado. Estas extensões permitem resolver endereços IP de partição específica usando uma combinação de nome DNS de serviço com estado e o nome da partição. Todos os três esquemas de criação de partições são suportados:

- Com o nome de criação de partições
- Ranged a criação de partições
- A criação de partições de singleton

O diagrama seguinte mostra o funcionamento do serviço DNS para serviços com estado particionados.

![pontos finais de serviço com estado](./media/service-fabric-dnsservice/stateful-dns.png)

Portas dinâmicas não são suportadas pelo serviço de DNS. Para resolver serviços expostos portas dinâmicas, utilize o [inverter o serviço de proxy](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Ativar o serviço DNS
> [!NOTE]
> Serviço DNS para serviços do Service Fabric ainda não é suportado no Linux.

Quando cria um cluster com o portal, o serviço DNS está ativado por predefinição no **serviço DNS incluem** caixa de verificação a **configuração de Cluster** menu:

![Ativar o serviço DNS através do portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Se não estiver a utilizar o portal para criar o cluster, ou se estiver a atualizar um cluster existente, terá de ativar o serviço DNS num modelo:

- Para implementar um novo cluster, pode utilizar o [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou criar seu próprio modelo do Resource Manager. 
- Para atualizar um cluster existente, pode navegar para o grupo de recursos do cluster no portal e clique em **Script de automação** para trabalhar com um modelo que reflita o estado atual do cluster e outros recursos no grupo. Para obter mais informações, consulte [exportar o modelo de grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Depois de ter um modelo, pode ativar o serviço DNS com os seguintes passos:

1. Verifique se o `apiversion` está definido como `2017-07-01-preview` ou posterior para o `Microsoft.ServiceFabric/clusters` recurso e, se não, atualize-a conforme mostrado no exemplo a seguir:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, ative o serviço DNS em uma das seguintes formas:

   - Para ativar o serviço DNS com as predefinições, adicione-o para o `addonFeatures` secção dentro do `properties` secção conforme mostrado no exemplo a seguir:

       ```json
           "properties": {
              ...

              "addonFeatures": [
                "DnsService"
              ],
              ...
           }
       ```
   - Para ativar o serviço com o que não sejam as predefinições, adicione uma `DnsService` secção para o `fabricSettings` secção dentro do `properties` secção. Neste caso, não precisa de adicionar o DnsService para `addonFeatures`. Para saber mais sobre as propriedades que podem ser definidas para o serviço de DNS, consulte [definições do serviço de DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
1. Depois de atualizar o modelo de cluster com as suas alterações, aplicá-las e permitir que a atualização completa. Quando a atualização estiver concluída, é iniciado o serviço de sistema DNS em execução no seu cluster. O nome do serviço é `fabric:/System/DnsService`, e pode encontrá-lo sob o **sistema** secção no Explorador do Service Fabric do serviço. 


## <a name="setting-the-dns-name-for-your-service"></a>Definir o nome DNS para o seu serviço
Pode definir um nome DNS para os serviços de forma declarativa para serviços padrão no ficheiro applicationmanifest. XML ou por meio de comandos do PowerShell.

O nome DNS para o seu serviço é resolvível por todo o cluster, pelo que é importante garantir a exclusividade do nome DNS no cluster. 

É altamente recomendável que utilize um esquema de nomeação de `<ServiceDnsName>.<AppInstanceName>`; por exemplo, `service1.application1`. Se uma aplicação é implementada com o Docker compose, os serviços são atribuídos automaticamente nomes DNS usando este esquema de nomeação.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definir o nome DNS para um serviço predefinido no applicationmanifest. XML
Abra o projeto no Visual Studio ou no seu editor favorito e abra o ficheiro Applicationmanifest XML. Vá para a secção de serviços padrão e para cada serviço, adicione o `ServiceDnsName` atributo. O exemplo seguinte mostra como definir o nome DNS do serviço para `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Assim que a aplicação é implementada, a instância de serviço no Explorador do Service Fabric mostra o nome DNS para esta instância, conforme mostrado na figura a seguir: 

![pontos finais de serviço](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

O exemplo seguinte define o nome DNS para um serviço com monitorização de estado para `statefulsvc.app`. O serviço utiliza um esquema de partições com nome. Tenha em atenção que os nomes de partição são minúsculas. Este é um requisito para as partições que vão ser alvo de consultas de DNS Para obter mais informações, consulte [partição de serviço de consultas de DNS de fazer com um estado](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Definir o nome DNS para um serviço com o Powershell
Pode definir o nome DNS para um serviço ao criar usando o `New-ServiceFabricService` comando do Powershell. O exemplo seguinte cria um novo serviço sem monitoração de estado com o nome DNS `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Pré-visualização] Torne as consultas DNS numa partição de serviço com estado
A partir do Service Fabric versão 6.3, o serviço de DNS do Service Fabric suporta consultas para as partições do serviço.

Para as partições que vão ser utilizadas em consultas DNS, aplicam-se as seguintes restrições de nomenclatura:

   - Nomes de partição devem ser compatíveis com DNS.
   - Nomes de etiqueta multi partição (que incluam dot, '.', no nome) não deve ser utilizado.
   - Nomes de partição devem ser em minúsculas.

Consultas DNS que uma partição de destino são formatadas da seguinte forma:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Em que:

- *First-Label-Of-Partitioned-Service-DNSName* é a primeira parte do seu nome DNS do serviço.
- *PartitionPrefix* é um valor que pode ser definido na secção DnsService do manifesto do cluster ou por meio do modelo do Resource Manager do cluster. O valor predefinido é "-". Para obter mais informações, consulte [definições do serviço de DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Nome da partição de destino* é o nome da partição. 
- *PartitionSuffix* é um valor que pode ser definido na secção DnsService do manifesto do cluster ou por meio do modelo do Resource Manager do cluster. O valor predefinido é a cadeia de caracteres vazia. Para obter mais informações, consulte [definições do serviço de DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Restantes-particionada-Service-DNSName* é a parte restante do seu nome DNS do serviço.

Os exemplos seguintes mostram as consultas DNS para serviços particionados em execução num cluster que tem definições predefinidas para `PartitionPrefix` e `PartitionSuffix`: 

- Para resolver a partição "0" de um serviço com o nome DNS `backendrangedschemesvc.application` que utiliza um esquema de particionamento ranged, utilize `backendrangedschemesvc-0.application`.
- Para resolver a partição "first" de um serviço com o nome DNS `backendnamedschemesvc.application` que utiliza um esquema de partições com nome, utilize `backendnamedschemesvc-first.application`.

O serviço DNS devolve o endereço IP da réplica primária da partição. Se nenhuma partição for especificada, o serviço devolve o endereço IP da réplica primária de uma partição selecionada aleatoriamente.

## <a name="using-dns-in-your-services"></a>Através de DNS nos seus serviços
Se implementar mais de um serviço, pode encontrar os pontos finais de outros serviços para comunicar com o com um nome DNS. O serviço DNS funciona para serviços sem estado e, no Service Fabric versão 6.3 e posterior, para serviços com estado. Para serviços com estado em execução em versões do Service Fabric antes 6.3, pode usar o incorporado [inverter o serviço de proxy](./service-fabric-reverseproxy.md) para chamadas http chamar uma partição de serviço específico. 

Portas dinâmicas não são suportadas pelo serviço de DNS. Pode utilizar o serviço de proxy inverso para resolver os serviços que utilizam portas dinâmicas.

O código seguinte mostra como chamar um serviço sem estado através de DNS. É simplesmente uma chamada regular http em que fornecer o nome DNS, a porta e qualquer caminho opcional como parte do URL.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

O código seguinte mostra uma chamada numa partição específica de um serviço com estado. Neste caso, o nome DNS contém o nome da partição (partition1). A chamada pressupõe um cluster com valores predefinidos para `PartitionPrefix` e `PartitionSuffix`.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Problemas Conhecidos
* Para versões de Service Fabric 6.3 e superiores, existe um problema com pesquisas de DNS para nomes de serviço que contém um hífen, o nome de DNS. Para obter mais informações sobre este problema, controlar as seguintes [problema do GitHub](https://github.com/Azure/service-fabric-issues/issues/1197). A atualização em seguida 6.3 surgem uma correção para isso. 

* Serviço DNS para serviços do Service Fabric ainda não é suportado no Linux. Serviço DNS é suportado para contentores no Linux. Resolução manual usando recursos de infraestrutura. o cliente/ServicePartitionResolver é a alternativa disponível.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre a comunicação de serviço dentro do cluster com [ligar e comunicar com serviços](service-fabric-connect-and-communicate-with-services.md)

