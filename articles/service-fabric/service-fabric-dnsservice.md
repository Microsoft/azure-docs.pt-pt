---
title: Serviço Azure Fabric DNS
description: Utilize o serviço dns da Service Fabric para descobrir microserviços de dentro do cluster.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75458023"
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O Serviço DNS é um serviço de sistema opcional que pode permitir no seu cluster descobrir outros serviços utilizando o protocolo DNS. 

Muitos serviços, especialmente serviços contentorizados, são endereçados através de um URL pré-existente. É desejável a resolução destes serviços utilizando o protocolo DNS padrão, em vez do protocolo service Fabric Naming Service. O serviço DNS permite-lhe mapear os nomes dNS para um nome de serviço e, portanto, resolver endereços IP do ponto final. Esta funcionalidade mantém a portabilidade dos serviços contentorizados em diferentes plataformas e pode facilitar cenários de "levantar e mudar", permitindo-lhe utilizar URLs de serviço existentes em vez de ter de reescrever código para alavancar o Serviço de Naming. 

O serviço DNS mapeia os nomes do DNS para nomes de serviço, que por sua vez são resolvidos pelo Serviço de Nomeação para devolver o ponto final do serviço. O nome DNS para o serviço é fornecido no momento da criação. O diagrama seguinte mostra como o serviço DNS funciona para serviços apátridas.

![pontos finais de serviço](./media/service-fabric-dnsservice/stateless-dns.png)

Começando com a versão 6.3 do Service Fabric, o protocolo DNS de Tecido de Serviço foi alargado para incluir um esquema de endereçamento de serviços estatais divididos. Estas extensões tornam possível a resolução de endereços IP específicos de partição utilizando uma combinação de nome dNS de serviço e o nome da partição. Os três regimes de partilha são apoiados:

- Partição nomeada
- Divisória variada
- Partição singleton

O diagrama seguinte mostra como o serviço DNS funciona para serviços estatais divididos.

![pontos finais de serviço apátridas](./media/service-fabric-dnsservice/stateful-dns.png)

As portas dinâmicas não são suportadas pelo serviço DNS. Para resolver os serviços expostos em portas dinâmicas, utilize o serviço de [procuração inversa](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Habilitar o serviço DNS
> [!NOTE]
> O serviço DNS para serviços de Tecido de Serviço ainda não é suportado no Linux.

Quando cria um cluster utilizando o portal, o serviço DNS é ativado por padrão na caixa de verificação de **serviço SNS incluída** no menu de **configuração do Cluster:**

![Habilitar o serviço DNS através do portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Se não estiver a usar o portal para criar o seu cluster ou se estiver a atualizar um cluster existente, terá de ativar o serviço DNS num modelo:

- Para implementar um novo cluster, pode utilizar os [modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) de amostra ou criar o seu próprio modelo de Gestor de Recursos. 
- Para atualizar um cluster existente, pode navegar para o grupo de recursos do cluster no portal e clicar no **Script de Automação** para trabalhar com um modelo que reflete o estado atual do cluster e outros recursos no grupo. Para saber mais, consulte [Exportar o modelo do grupo de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

Depois de ter um modelo, pode ativar o serviço DNS com os seguintes passos:

1. Verifique se `apiversion` o `2017-07-01-preview` recurso está `Microsoft.ServiceFabric/clusters` definido para ou mais tarde e, se não, atualizá-lo como mostrado no seguinte exemplo:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora, ative o serviço DNS de uma das seguintes formas:

   - Para ativar o serviço DNS com `addonFeatures` definições `properties` predefinidas, adicione-o à secção dentro da secção, como mostra o seguinte exemplo:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Para ativar o serviço com outras definições que não o padrão, adicione uma `DnsService` secção à `fabricSettings` secção dentro da `properties` secção. Neste caso, não precisa de adicionar o DnsService a `addonFeatures`. Para saber mais sobre as propriedades que podem ser definidas para o Serviço DNS, consulte [as definições do Serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. Depois de atualizar o modelo de cluster com as suas alterações, aplique-as e deixe a atualização completa. Quando a atualização estiver concluída, o sistema DNS começa a funcionar no seu cluster. O nome `fabric:/System/DnsService`do serviço é , e você pode encontrá-lo sob a secção de serviço **sistema** no explorador de tecido de serviço. 

> [!NOTE]
> Ao atualizar o DNS de deficientes para ativados, o Service Fabric Explorer pode não refletir o novo estado. Para resolver, reinicie os nós modificando a Política de Upgrade no seu modelo de Gestor de Recursos Azure. Consulte a referência do modelo de [tecido de serviço](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) para mais informações.

> [!NOTE]
> Ativar o serviço DNS ao desenvolver-se numa máquina local irá sobrepor-se a algumas definições de DNS. Se tiver problemas de ligação à internet, verifique as definições de DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Definição do nome DNS para o seu serviço
Pode definir um nome DNS para os seus serviços, quer declarativamente para serviços predefinidos no ficheiro ApplicationManifest.xml, quer através dos comandos PowerShell.

O nome DNS para o seu serviço é resolúvel em todo o cluster, pelo que é importante garantir a singularidade do nome DNS em todo o cluster. 

É altamente recomendável que utilize `<ServiceDnsName>.<AppInstanceName>`um esquema de nomeação de; por exemplo, `service1.application1`. Se uma aplicação for implementada utilizando a composição do Docker, os serviços são automaticamente atribuídos aos nomes dNS utilizando este esquema de nomeação.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definição do nome DNS para um serviço predefinido no ApplicationManifest.xml
Abra o seu projeto no Visual Studio, ou no seu editor favorito, e abra o ficheiro ApplicationManifest.xml. Vá à secção de serviços predefinidos e para cada serviço adicione o `ServiceDnsName` atributo. O exemplo seguinte mostra como definir o nome DNS do serviço para`service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Uma vez implementada a aplicação, a instância de serviço no explorador de Tecido de Serviço mostra o nome DNS para esta instância, como mostra a seguinte figura: 

![pontos finais de serviço](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

O exemplo que se segue define o nome `statefulsvc.app`DNS para um serviço imponente para . O serviço usa um esquema de partição nomeado. Note que os nomes da partição são minúsculos. Esta é uma exigência para divisórias que serão direcionadas em consultas de DNS; para mais informações, consulte [fazer consultas de DNS numa partição de serviço sinuoso.](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition)

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Definição do nome DNS para um serviço utilizando powershell
Pode definir o nome DNS para um `New-ServiceFabricService` serviço ao criá-lo utilizando o comando Powershell. O exemplo seguinte cria um novo serviço apátrida com o nome DNS`service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Pré-visualização] Fazer consultas de DNS numa partição de serviço sinuoso
Começando com a versão 6.3 do Tecido de Serviço, o serviço De serviço Fabric DNS suporta consultas para partições de serviço.

Para divisórias que serão utilizadas em consultas de DNS, aplicam-se as seguintes restrições de nomeação:

   - Os nomes da partição devem ser compatíveis com o DNS.
   - Não devem ser utilizados nomes de divisórias multi-etiquetas (que incluam ponto, '.', no nome).
   - Os nomes da partição devem ser minúsculos.

As consultas dNS que visam uma partição são formatadas da seguinte forma:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Em que:

- O nome DNSName de Serviço De Serviço De primeira *etiqueta-divisória* é a primeira parte do seu nome dNS de serviço.
- *PartitionPrefix* é um valor que pode ser definido na secção DnsService do manifesto do cluster ou através do modelo de Gestor de Recursos do cluster. O valor padrão é "--". Para saber mais, consulte [as definições do Serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Nome de partição-alvo* é o nome da partição. 
- *PartitionSSuffix* é um valor que pode ser definido na secção DnsService do manifesto do cluster ou através do modelo de Gestor de Recursos do cluster. O valor padrão é a corda vazia. Para saber mais, consulte [as definições do Serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *O DNSName de Serviço-DNS* restante é a parte restante do seu nome dNS de serviço.

Os seguintes exemplos mostram consultas de DNS para serviços `PartitionPrefix` divididos em funcionamento num cluster que tem definições padrão para e: `PartitionSuffix` 

- Para resolver a partilha "0" de `backendrangedschemesvc.application` um serviço com o `backendrangedschemesvc-0.application`nome DNS que utiliza um esquema de partilha variado, utilize .
- Para resolver a partilha "primeiro" de `backendnamedschemesvc.application` um serviço com o `backendnamedschemesvc-first.application`nome DNS que utiliza um esquema de partição nomeado, utilize .

O serviço DNS devolve o endereço IP da réplica primária da divisória. Se não for especificada qualquer partição, o serviço devolve o endereço IP da réplica primária de uma divisória selecionada aleatoriamente.

## <a name="using-dns-in-your-services"></a>Utilização de DNS nos seus serviços
Se implementar mais do que um serviço, poderá encontrar os pontos finais de outros serviços com os quais comunicar utilizando um nome DNS. O serviço DNS funciona para serviços apátridas, e, na versão 6.3 e posterior, do Service Fabric para serviços audais. Para serviços estatais que executam em versões de Tecido de Serviço antes de 6.3, você pode usar o serviço de [procuração inversa](./service-fabric-reverseproxy.md) incorporado para chamadas http para chamar uma partição de serviço particular. 

As portas dinâmicas não são suportadas pelo serviço DNS. Pode utilizar o serviço de procuração inversa para resolver serviços que utilizem portas dinâmicas.

O código que se segue mostra como chamar um serviço apátrida através do DNS. É simplesmente uma chamada regular em http onde fornece o nome DNS, a porta e qualquer caminho opcional como parte do URL.

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

O seguinte código mostra uma chamada numa partição específica de um serviço imponente. Neste caso, o nome DNS contém o nome da partição (partição1). A chamada assume um cluster `PartitionPrefix` com `PartitionSuffix`valores padrão para e .

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
* Para as versões De Tecido de Serviço 6.3 ou superior, existe um problema com as procurações de DNS para nomes de serviço que contenham um hífen no nome DNS. Para mais informações sobre este assunto, por favor, acompanhe o seguinte [Problema gitHub](https://github.com/Azure/service-fabric-issues/issues/1197). Uma correção para isto está a chegar na próxima atualização 6.3. 

* O serviço DNS para serviços de Tecido de Serviço ainda não é suportado no Linux. O serviço DNS é suportado para contentores em Linux. Resolução manual utilizando o Cliente de Tecido/ServiçoPartitionResolver é a alternativa disponível.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a comunicação de serviço dentro do cluster com [a ligação e comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md)

