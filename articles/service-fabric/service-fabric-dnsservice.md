---
title: Serviço DNS de Tecido de Serviço Azure
description: Utilize o serviço dns da Service Fabric para descobrir microserviços a partir do interior do cluster.
ms.topic: conceptual
ms.date: 7/20/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: f7f06920820cdc73f8d3101ab24ee46625931ee4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91268048"
---
# <a name="dns-service-in-azure-service-fabric"></a>Serviço DNS no Azure Service Fabric
O Serviço DNS é um serviço de sistema opcional que pode permitir no seu cluster descobrir outros serviços utilizando o protocolo DNS. 

Muitos serviços, especialmente serviços contentorizados, são endereçados através de um URL pré-existente. Ser capaz de resolver estes serviços usando o protocolo padrão DNS, em vez do protocolo serviço de nomeação de tecido de serviço, é desejável. O serviço DNS permite-lhe mapear os nomes DNS para um nome de serviço e, portanto, resolver endereços IP de ponto final. Esta funcionalidade mantém a portabilidade dos serviços contentorizados em diferentes plataformas e pode facilitar os cenários de "elevação e mudança", permitindo-lhe utilizar URLs de serviço existentes em vez de ter de reescrever código para alavancar o Serviço de Nomeação. 

O serviço DNS mapeia os nomes dns para nomes de serviço, que por sua vez são resolvidos pelo Serviço de Nomeação para devolver o ponto final do serviço. O nome DNS para o serviço é fornecido no momento da criação. O diagrama seguinte mostra como o serviço DNS funciona para serviços apátridas.

![Diagrama mostrando como os nomes dns são mapeados para nomes de serviço pelo serviço DNS para serviços apátridas.](./media/service-fabric-dnsservice/stateless-dns.png)

Começando pela versão 6.3 do Service Fabric, o protocolo DNS do Tecido de Serviço foi alargado para incluir um esquema de endereçamento de serviços estatais divididos. Estas extensões possibilitam a resolução de endereços IP de partição específicas utilizando uma combinação de nome DNS de serviço e o nome de partição. Todos os três sistemas de partição são apoiados:

- Partição nomeada
- Partição de gama
- Partição singleton

O diagrama seguinte mostra como o serviço DNS funciona para serviços estatais divididos.

![Diagrama mostrando como os nomes dns são mapeados para nomes de serviço pelo serviço DNS para serviços atrilhoados divididos.](./media/service-fabric-dnsservice/stateful-dns.png)

As portas dinâmicas não são suportadas pelo serviço DNS. Para resolver os serviços expostos em portas dinâmicas, utilize o [serviço de procuração inversa](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>Ativar o serviço DNS
> [!NOTE]
> O serviço DNS para serviços de Service Fabric ainda não está suportado no Linux.

Quando cria um cluster utilizando o portal, o serviço DNS é ativado por predefinição na caixa de verificação de **serviço incluir DNS** no menu **de configuração cluster:**

![Ativar o serviço DNS através do portal](./media/service-fabric-dnsservice/enable-dns-service.png)

Se não estiver a utilizar o portal para criar o seu cluster ou se estiver a atualizar um cluster existente, terá de ativar o serviço DNS num modelo:

- Para implementar um novo cluster, pode utilizar os [modelos](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) de amostra ou criar o seu próprio modelo de Gestor de Recursos. 
- Para atualizar um cluster existente, pode navegar para o grupo de recursos do cluster no portal e clicar em **Script de Automação** para trabalhar com um modelo que reflita o estado atual do cluster e outros recursos no grupo. Para saber mais, consulte [Export o modelo do grupo de recursos.](../azure-resource-manager/templates/export-template-portal.md)

Depois de ter um modelo, pode ativar o serviço DNS com os seguintes passos:

1. Verifique se o `apiversion` recurso é definido para ou mais tarde para o recurso `2017-07-01-preview` `Microsoft.ServiceFabric/clusters` e, se não, atualizá-lo como mostrado no exemplo seguinte:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Agora ative o serviço DNS de uma das seguintes formas:

   - Para ativar o serviço DNS com definições predefinidos, adicione-o à `addonFeatures` secção no interior da `properties` secção, como mostra o seguinte exemplo:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Para ativar o serviço com outras definições que não o predefinido, adicione uma `DnsService` secção à secção no interior da `fabricSettings` `properties` secção. Neste caso, não precisa de adicionar o DnsService a `addonFeatures` . Para saber mais sobre as propriedades que podem ser configurados para o Serviço DNS, consulte as [definições do Serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).

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
3. Uma vez atualizado o modelo de cluster com as suas alterações, aplique-as e deixe a atualização completa. Quando a atualização estiver concluída, o serviço de sistema DNS começa a funcionar no seu cluster. O nome de serviço é `fabric:/System/DnsService` , e você pode encontrá-lo sob a secção de serviço **sistema** no explorador de tecido de serviço. 

> [!NOTE]
> Ao atualizar o DNS de desativado para ativar, o Service Fabric Explorer pode não refletir o novo estado. Para resolver, reinicie os nós modificando a Política de Upgrade no seu modelo de Gestor de Recursos Azure. Consulte a [referência do modelo de tecido de](/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) serviço para mais informações.

> [!NOTE]
> Ativar o serviço DNS ao desenvolver-se numa máquina local irá sobrepor-se a algumas definições de DNS. Se tiver problemas de ligação à internet, verifique as definições de DNS.

## <a name="setting-the-dns-name-for-your-service"></a>Definição do nome DNS para o seu serviço
Pode definir um nome DNS para os seus serviços declarativamente para serviços predefinidos no ficheiro ApplicationManifest.xml ou através dos comandos PowerShell.

O nome DNS para o seu serviço é resolúvel em todo o cluster, pelo que é importante garantir a singularidade do nome DNS em todo o cluster. 

É altamente recomendável que utilize um esquema de nomeação `<ServiceDnsName>.<AppInstanceName>` de; por exemplo, `service1.application1` . Se uma aplicação for implementada usando a composição do Docker, os serviços são automaticamente atribuídos nomes DNS usando este esquema de nomeação.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Definição do nome DNS para um serviço predefinido no ApplicationManifest.xml
Abra o seu projeto no Visual Studio, ou no seu editor favorito, e abra o ficheiro ApplicationManifest.xml. Aceda à secção de serviços predefinidos e para cada serviço adicione o `ServiceDnsName` atributo. O exemplo a seguir mostra como definir o nome DNS do serviço para `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Uma vez implementada a aplicação, a instância de serviço no explorador de Tecido de Serviço mostra o nome DNS para este caso, como mostrado na seguinte figura: 

![pontos finais de serviço](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

O exemplo a seguir define o nome DNS para um serviço imponente para `statefulsvc.app` . O serviço usa um esquema de partição nomeado. Note que os nomes de partição são inferiores. Este é um requisito para divisórias que serão direcionadas em consultas de DNS; para obter mais informações, consulte [fazer consultas dns numa partição de serviço imponente](#preview-making-dns-queries-on-a-stateful-service-partition).

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

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Definição do nome DNS para um serviço utilizando Powershell
Pode definir o nome DNS para um serviço ao criá-lo utilizando o `New-ServiceFabricService` comando Powershell. O exemplo a seguir cria um novo serviço apátrida com o nome DNS `service1.application1`

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

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>[Pré-visualização] Fazer consultas de DNS numa divisória de serviço imponente
Começando pela versão 6.3 do Service Fabric DNS, o serviço DNS de Tecido de Serviço suporta consultas para divisórias de serviço.

Para as divisórias que serão utilizadas em consultas DNS, aplicam-se as seguintes restrições de nomeação:

   - Os nomes de partição devem ser compatíveis com DNS.
   - Não devem ser utilizados nomes de partição multi-etiquetas (que incluem ponto, '.', no nome).
   - Os nomes das divisórias devem ser mais baixos.

As consultas de DNS que visam uma partição são formatadas da seguinte forma:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Em que:

- *First-Label-Of-Partitioned-Service-DNSName* é a primeira parte do nome DNS do seu serviço.
- *PartitionPrefix* é um valor que pode ser definido na secção DnsService do manifesto do cluster ou através do modelo de Gestor de Recursos do cluster. O valor predefinido é "--". Para saber mais, consulte  [as definições do Serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Nome-alvo-Partição* é o nome da partição. 
- *O partitionS sufixo* é um valor que pode ser definido na secção DnsService do manifesto do cluster ou através do modelo de Gestor de Recursos do cluster. O valor predefinido é a corda vazia. Para saber mais, consulte  [as definições do Serviço DNS](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Restante-Partilhationed-Service-DNSName* é a parte restante do nome DNS do seu serviço.

Os exemplos a seguir mostram consultas de DNS para serviços divididos em execução num cluster que tem definições padrão para `PartitionPrefix` e `PartitionSuffix` : 

- Para resolver a partição "0" de um serviço com nome DNS `backendrangedschemesvc.application` que utiliza um esquema de partição variado, utilize `backendrangedschemesvc-0.application` .
- Para resolver a partição "primeiro" de um serviço com nome DNS `backendnamedschemesvc.application` que utiliza um esquema de partição nomeado, utilize `backendnamedschemesvc-first.application` .

O serviço DNS devolve o endereço IP da réplica primária da partição. Se não for especificada nenhuma partição, o serviço devolve o endereço IP da réplica primária de uma divisória selecionada aleatoriamente.

## <a name="using-dns-in-your-services"></a>Utilização de DNS nos seus serviços
Se implementar mais de um serviço, pode encontrar os pontos finais de outros serviços com os quais comunicar utilizando um nome DNS. O serviço DNS funciona para serviços apátridas, e, na versão 6.3 do Service Fabric e posteriormente, para serviços estatais. Para serviços estatais em execução em versões de Tecido de Serviço antes do 6.3, pode utilizar o serviço de [procuração inversa](./service-fabric-reverseproxy.md) incorporado para chamadas http para chamar uma partição de serviço particular. 

As portas dinâmicas não são suportadas pelo serviço DNS. Pode utilizar o serviço de procuração inversa para resolver serviços que utilizam portas dinâmicas.

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

O seguinte código mostra uma chamada numa partição específica de um serviço estatal. Neste caso, o nome DNS contém o nome de partição (partição1). A chamada pressupõe um cluster com valores predefinidos para `PartitionPrefix` e `PartitionSuffix` .

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
* Para as versões de Tecido de Serviço 6.3 ou superior, existe um problema com pesquisas de DNS para nomes de serviço que contenham um hífen no nome DNS. Para mais informações sobre este assunto, por favor siga a seguinte [edição do GitHub .](https://github.com/Azure/service-fabric-issues/issues/1197) Uma correção para isto está a chegar na próxima atualização 6.3. 

* O serviço DNS para serviços de Service Fabric ainda não está suportado no Linux. O serviço DNS é suportado para contentores em Linux. A resolução manual utilizando o Fabric Client/ServicePartitionResolver é a alternativa disponível.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a comunicação de serviços dentro do cluster com  [a ligação e comunicação com os serviços](service-fabric-connect-and-communicate-with-services.md)
