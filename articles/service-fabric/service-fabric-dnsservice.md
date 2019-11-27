---
title: Serviço DNS do Azure Service Fabric | Microsoft Docs
description: Use o serviço DNS do Service Fabric para descobrir os microserviços de dentro do cluster.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/20/2018
ms.author: atsenthi
ms.openlocfilehash: 707fc9f073e37d60c6c6fca8e9a8392b2550da9f
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229297"
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O serviço DNS é um serviço de sistema opcional que você pode habilitar em seu cluster para descobrir outros serviços usando o protocolo DNS. 

Muitos serviços, especialmente serviços em contêineres, são endereçáveis por meio de uma URL pré-existente. A capacidade de resolver esses serviços usando o protocolo DNS padrão, em vez do protocolo Service Fabric Serviço de Nomenclatura, é desejável. O serviço DNS permite mapear nomes DNS para um nome de serviço e, portanto, resolver endereços IP de ponto de extremidade. Essa funcionalidade mantém a portabilidade de serviços em contêineres em diferentes plataformas e pode facilitar os cenários de "deslocamento e mudança", permitindo que você use URLs de serviço existentes em vez de ter que reescrever o código para aproveitar o Serviço de Nomenclatura. 

O serviço DNS mapeia nomes DNS para nomes de serviço que, por sua vez, são resolvidos pelo Serviço de Nomenclatura para retornar o ponto de extremidade de serviço. O nome DNS para o serviço é fornecido no momento da criação. O diagrama a seguir mostra como o serviço DNS funciona para serviços sem estado.

![pontos de extremidade de serviço](./media/service-fabric-dnsservice/stateless-dns.png)

A partir do Service Fabric versão 6,3, o protocolo DNS Service Fabric foi estendido para incluir um esquema para endereçar serviços com estado particionados. Essas extensões possibilitam a resolução de endereços IP de partição específicos usando uma combinação de nome DNS de serviço com estado e o nome da partição. Há suporte para todos os três esquemas de particionamento:

- Particionamento nomeado
- Particionamento de intervalo
- Particionamento singleton

O diagrama a seguir mostra como o serviço DNS funciona para serviços com estado particionados.

![pontos de extremidade de serviço com estado](./media/service-fabric-dnsservice/stateful-dns.png)

Não há suporte para portas dinâmicas no serviço DNS. Para resolver os serviços expostos em portas dinâmicas, use o [serviço de proxy reverso](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Habilitando o serviço DNS
> [!NOTE]
> O serviço DNS para serviços de Service Fabric ainda não tem suporte no Linux.

Quando você cria um cluster usando o portal, o serviço DNS é habilitado por padrão na caixa de seleção **incluir serviço DNS** no menu **configuração do cluster** :

![Habilitando o serviço DNS por meio do portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Se você não estiver usando o portal para criar o cluster ou se estiver atualizando um cluster existente, será necessário habilitar o serviço DNS em um modelo:

- Para implantar um novo cluster, você pode usar os [modelos de exemplo](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) ou criar seu próprio modelo do Resource Manager. 
- Para atualizar um cluster existente, você pode navegar até o grupo de recursos do cluster no portal e clicar em **script de automação** para trabalhar com um modelo que reflete o estado atual do cluster e outros recursos no grupo. Para saber mais, consulte [exportar o modelo do grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Depois de ter um modelo, você pode habilitar o serviço DNS com as seguintes etapas:

1. Verifique se o `apiversion` está definido como `2017-07-01-preview` ou posterior para o recurso de `Microsoft.ServiceFabric/clusters` e, se não, atualize-o conforme mostrado no exemplo a seguir:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, habilite o serviço DNS de uma das seguintes maneiras:

   - Para habilitar o serviço DNS com as configurações padrão, adicione-o à seção `addonFeatures` dentro da seção `properties`, conforme mostrado no exemplo a seguir:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Para habilitar o serviço com outras configurações padrão, adicione uma seção `DnsService` à seção `fabricSettings` dentro da seção `properties`. Nesse caso, você não precisa adicionar o DnsService ao `addonFeatures`. Para saber mais sobre as propriedades que podem ser definidas para o serviço DNS, consulte [configurações do serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. Depois de atualizar o modelo de cluster com suas alterações, aplique-as e permita que a atualização seja concluída. Quando a atualização for concluída, o serviço do sistema DNS começará a ser executado no cluster. O nome do serviço é `fabric:/System/DnsService`e você pode encontrá-lo na seção serviço do **sistema** no Service Fabric Explorer. 

> [!NOTE]
> Ao atualizar o DNS de desabilitado para habilitado, Service Fabric Explorer pode não refletir o novo estado. Para resolver, reinicie os nós modificando o UpgradePolicy em seu modelo de Azure Resource Manager. Consulte a [referência do modelo de Service Fabric](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) para obter mais informações.

> [!NOTE]
> Habilitar o serviço DNS ao desenvolver em um computador local substituirá algumas configurações de DNS. Se você tiver problemas para se conectar à Internet, verifique as configurações de DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Configurando o nome DNS para seu serviço
Você pode definir um nome DNS para seus serviços de forma declarativa para serviços padrão no arquivo ApplicationManifest. xml ou por meio de comandos do PowerShell.

O nome DNS do serviço é resolvível em todo o cluster, portanto, é importante garantir a exclusividade do nome DNS no cluster. 

É altamente recomendável que você use um esquema de nomenclatura de `<ServiceDnsName>.<AppInstanceName>`; por exemplo, `service1.application1`. Se um aplicativo for implantado usando o Docker Compose, os serviços serão atribuídos automaticamente a nomes DNS usando esse esquema de nomenclatura.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definindo o nome DNS para um serviço padrão no ApplicationManifest. xml
Abra o projeto no Visual Studio ou seu editor favorito e abra o arquivo ApplicationManifest. xml. Vá para a seção serviços padrão e, para cada serviço, adicione o atributo `ServiceDnsName`. O exemplo a seguir mostra como definir o nome DNS do serviço como `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Depois que o aplicativo for implantado, a instância de serviço no Service Fabric Explorer mostrará o nome DNS dessa instância, conforme mostrado na figura a seguir: 

![pontos de extremidade de serviço](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

O exemplo a seguir define o nome DNS para um serviço com estado como `statefulsvc.app`. O serviço usa um esquema de particionamento nomeado. Observe que os nomes de partição são letras minúsculas. Esse é um requisito para partições que serão destinadas a consultas DNS; para obter mais informações, consulte [fazendo consultas DNS em uma partição de serviço com estado](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Definindo o nome DNS para um serviço usando o PowerShell
Você pode definir o nome DNS para um serviço ao criá-lo usando o comando `New-ServiceFabricService` PowerShell. O exemplo a seguir cria um novo serviço sem estado com o nome DNS `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>Apresentação Fazendo consultas DNS em uma partição de serviço com estado
A partir do Service Fabric versão 6,3, o serviço DNS Service Fabric dá suporte a consultas para partições de serviço.

Para partições que serão usadas em consultas DNS, as seguintes restrições de nomenclatura se aplicam:

   - Os nomes de partição devem estar em conformidade com o DNS.
   - Nomes de partição com vários rótulos (que incluem ponto, '. ', no nome) não devem ser usados.
   - Os nomes de partição devem estar em letras minúsculas.

As consultas DNS direcionadas a uma partição são formatadas da seguinte maneira:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Em que:

- O *primeiro rótulo de-serviço-particionado-Service-DnsName* é a primeira parte do nome DNS do serviço.
- *PartitionPrefix* é um valor que pode ser definido na seção DnsService do manifesto do cluster ou por meio do modelo do Resource Manager do cluster. O valor padrão é "--". Para saber mais, consulte [configurações do serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Target-Partition-Name* é o nome da partição. 
- *PartitionSuffix* é um valor que pode ser definido na seção DnsService do manifesto do cluster ou por meio do modelo do Resource Manager do cluster. O valor padrão é uma cadeia de caracteres vazia. Para saber mais, consulte [configurações do serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Restante-partitiond-Service-DnsName* é a parte restante do seu nome DNS do serviço.

Os exemplos a seguir mostram as consultas DNS para serviços particionados em execução em um cluster com configurações padrão para `PartitionPrefix` e `PartitionSuffix`: 

- Para resolver a partição "0" de um serviço com o nome DNS `backendrangedschemesvc.application` que usa um esquema de particionamento de intervalo, use `backendrangedschemesvc-0.application`.
- Para resolver a partição "primeiro" de um serviço com o nome DNS `backendnamedschemesvc.application` que usa um esquema de particionamento nomeado, use `backendnamedschemesvc-first.application`.

O serviço DNS retorna o endereço IP da réplica primária da partição. Se nenhuma partição for especificada, o serviço retornará o endereço IP da réplica primária de uma partição selecionada aleatoriamente.

## <a name="using-dns-in-your-services"></a>Usando o DNS em seus serviços
Se você implantar mais de um serviço, poderá encontrar os pontos de extremidade de outros serviços com os quais se comunicar usando um nome DNS. O serviço DNS funciona para serviços sem estado e, no Service Fabric versão 6,3 e posterior, para serviços com estado. Para serviços com estado em execução em versões do Service Fabric anteriores a 6,3, você pode usar o [serviço de proxy reverso](./service-fabric-reverseproxy.md) interno para chamadas http para chamar uma partição de serviço específica. 

Não há suporte para portas dinâmicas no serviço DNS. Você pode usar o serviço de proxy reverso para resolver serviços que usam portas dinâmicas.

O código a seguir mostra como chamar um serviço sem estado por meio do DNS. É simplesmente uma chamada http regular em que você fornece o nome DNS, a porta e qualquer caminho opcional como parte da URL.

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

O código a seguir mostra uma chamada em uma partição específica de um serviço com estado. Nesse caso, o nome DNS contém o nome da partição (Partition1). A chamada pressupõe um cluster com valores padrão para `PartitionPrefix` e `PartitionSuffix`.

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
* Para Service Fabric versões 6,3 e superiores, há um problema com as pesquisas de DNS para nomes de serviço que contêm um hífen no nome DNS. Para obter mais informações sobre esse problema, acompanhe o seguinte [problema do GitHub](https://github.com/Azure/service-fabric-issues/issues/1197). Uma correção para isso está disponível na próxima atualização 6,3. 

* O serviço DNS para serviços de Service Fabric ainda não tem suporte no Linux. O serviço DNS tem suporte para contêineres no Linux. A resolução manual usando o Fabric Client/ServicePartitionResolver é a alternativa disponível.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a comunicação de serviço no cluster com [conectar e se comunicar com os serviços](service-fabric-connect-and-communicate-with-services.md)

