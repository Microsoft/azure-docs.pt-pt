---
title: Criar zonas dns e conjuntos de recordes usando o .NET SDK
titleSuffix: Azure DNS
description: Neste caminho de aprendizagem, começa a criar zonas DNS e recordes em Azure DNS utilizando o .NET SDK.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: rohink
ms.custom: devx-track-csharp
ms.openlocfilehash: 8e116096afbd01af4914be49d5675881724d5069
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96015065"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas dns e conjuntos de recordes usando o .NET SDK

Pode automatizar operações para criar, excluir ou atualizar zonas de DNS, conjuntos de registos e registos utilizando o DNS SDK com a biblioteca .NET DNS Management. Um projeto completo do Estúdio Visual está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Criar uma conta principal de serviço

Normalmente, o acesso programático aos recursos Azure é concedido através de uma conta dedicada e não das suas credenciais de utilizador. Estas contas dedicadas são chamadas de contas "principal de serviço". Para utilizar o projeto de amostra Azure DNS SDK, primeiro precisa de criar uma conta principal de serviço e atribuir-lhe as permissões corretas.

1. Siga [estas instruções](../active-directory/develop/howto-authenticate-service-principal-powershell.md) para criar uma conta principal de serviço (o projeto de amostra Azure DNS SDK assume a autenticação baseada em palavra-passe.)
2. Criar um grupo de recursos[(eis como).](../azure-resource-manager/templates/deploy-portal.md)
3. Use o Azure RBAC para conceder ao grupo de recursos as permissões da conta principal do serviço 'DNS Zone Contributor'[(eis como](../role-based-access-control/role-assignments-portal.md).)
4. Se utilizar o projeto de amostra Azure DNS SDK, edite o ficheiro 'programa.cs' da seguinte forma:

   * Insira os valores corretos para o `tenantId` , `clientId` (também conhecido como ID de conta), `secret` (senha de conta principal de serviço) e `subscriptionId` como usado no passo 1.
   * Introduza o nome do grupo de recursos escolhido no passo 2.
   * Insira o nome da zona DNS à sua escolha.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes NuGet e declarações de espaço de nome

Para utilizar o Azure DNS .NET SDK, é necessário instalar o pacote NuGet da Biblioteca de **Gestão Azure DNS** e outros pacotes Azure necessários.

1. No **Visual Studio,** abra um projeto ou novo projeto.
2. Vá ao Gestor de **Pacotes** **>** **NuGet** **>** **ferramentas Gerir pacotes nuget para solução...**.
3. Clique **em Procurar,** ative a caixa de verificação **"Incluir pré-relançação"** e digite **Microsoft.Azure.Management.Dns** na caixa de pesquisa.
4. Selecione o pacote e clique **em Instalar** para adicioná-lo ao seu projeto Visual Studio.
5. Repita o processo acima para instalar também os seguintes pacotes: **Microsoft.Rest.ClientRuntime.Azure.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicione as seguintes declarações de espaço de nome

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializar o cliente de gestão DNS

`DnsManagementClient`Contém os métodos e propriedades necessários para a gestão de zonas de DNS e conjuntos de registos.  O código seguinte entra na conta principal do serviço e cria um `DnsManagementClient` objeto.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Criar ou atualizar uma zona DNS

Para criar uma zona DE DNS, primeiro é criado um objeto "Zone" para conter os parâmetros da zona DNS. Como as zonas de DNS não estão ligadas a uma região específica, a localização está definida como "global". Neste exemplo, é também adicionada uma ['tag' Azure Resource Manager](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) à zona.

Para realmente criar ou atualizar a zona em Azure DNS, o objeto de zona que contém os parâmetros da zona é passado para o `DnsManagementClient.Zones.CreateOrUpdateAsyc` método.

> [!NOTE]
> DnsManagementClient suporta três modos de funcionamento: sincronizado ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrona com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Pode escolher qualquer um destes modos, dependendo das necessidades da sua aplicação.

O Azure DNS apoia a concordância otimista, chamada [Etags.](./dns-getstarted-powershell.md) Neste exemplo, especificar "*" para o cabeçalho 'Se-Nenhum-Match' diz ao Azure DNS para criar uma zona DE DNS se já não existir.  A chamada falha se uma zona com o nome próprio já existir no grupo de recursos dado.

```cs
// Create zone parameters
var dnsZoneParams = new Zone("global"); // All DNS zones must have location = "global"

// Create an Azure Resource Manager 'tag'.  This is optional.  You can add multiple tags
dnsZoneParams.Tags = new Dictionary<string, string>();
dnsZoneParams.Tags.Add("dept", "finance");

// Create the actual zone.
// Note: Uses 'If-None-Match *' ETAG check, so will fail if the zone exists already.
// Note: For non-async usage, call dnsClient.Zones.CreateOrUpdate(resourceGroupName, zoneName, dnsZoneParams, null, "*")
// Note: For getting the http response, call dnsClient.Zones.CreateOrUpdateWithHttpMessagesAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*")
var dnsZone = await dnsClient.Zones.CreateOrUpdateAsync(resourceGroupName, zoneName, dnsZoneParams, null, "*");
```

## <a name="create-dns-record-sets-and-records"></a>Crie conjuntos e registos de discos DNS

Os registos do DNS são geridos como um recorde. Um conjunto de recordes é um conjunto de registos com o mesmo nome e tipo de gravação dentro de uma zona.  O nome de conjunto de registos é relativo ao nome da zona, não ao nome DNS totalmente qualificado.

Para criar ou atualizar um conjunto de registos, é criado e passado para `DnsManagementClient.RecordSets.CreateOrUpdateAsync` . Tal como acontece com as zonas DNS, existem três modos de funcionamento: sincronizado ('CreateOrUpdate'), assíncrona ('CreateOrUpdateAsync'), ou assíncrona com acesso à resposta HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Tal como acontece com as zonas de DNS, as operações em conjuntos de recordes incluem suporte para a concordância otimista.  Neste exemplo, uma vez que nem 'If-Match' nem 'If-None-Match' são especificados, o recorde é sempre criado.  Esta chamada substitui qualquer gravador existente com o mesmo nome e tipo de gravação nesta zona de DNS.

```cs
// Create record set parameters
var recordSetParams = new RecordSet();
recordSetParams.TTL = 3600;

// Add records to the record set parameter object.  In this case, we'll add a record of type 'A'
recordSetParams.ARecords = new List<ARecord>();
recordSetParams.ARecords.Add(new ARecord("1.2.3.4"));

// Add metadata to the record set.  Similar to Azure Resource Manager tags, this is optional and you can add multiple metadata name/value pairs
recordSetParams.Metadata = new Dictionary<string, string>();
recordSetParams.Metadata.Add("user", "Mary");

// Create the actual record set in Azure DNS
// Note: no ETAG checks specified, will overwrite existing record set if one exists
var recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSetParams);
```

## <a name="get-zones-and-record-sets"></a>Obtenha zonas e recordes

Os `DnsManagementClient.Zones.Get` `DnsManagementClient.RecordSets.Get` métodos e métodos recuperam zonas individuais e conjuntos de recordes, respectivamente. Os RecordSets são identificados pelo seu tipo, nome e pela zona e grupo de recursos em que existem. As zonas são identificadas pelo seu nome e pelo grupo de recursos em que existem.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Atualizar um conjunto de recordes existente

Para atualizar um conjunto de registos DNS existente, primeiro recupere o recorde e, em seguida, atualize o conteúdo do conjunto de registos e, em seguida, envie a alteração.  Neste exemplo, especificamos o 'Etag' a partir do registo recuperado estabelecido no parâmetro 'If-Match'. A chamada falha se uma operação simultânea modificar o recorde entretanto estabelecido.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Zonas de lista e conjuntos de recordes

Para listar zonas, utilize os *métodos DnsManagementClient.Zones.List...* que suportam a listagem de todas as zonas de um determinado grupo de recursos ou de todas as zonas numa determinada subscrição do Azure (entre grupos de recursos).) Para listar conjuntos de *recordes, utilize os métodos DnsManagementClient.RecordSets.List...* que suportam a listagem de todos os conjuntos de recordes numa determinada zona ou apenas os conjuntos de registos de um tipo específico.

Note quando listar zonas e recordes que os resultados podem ser paginados.  O exemplo a seguir mostra como iterar através das páginas de resultados. (É utilizado um tamanho de página artificialmente pequeno de '2' para forçar a paging; na prática, este parâmetro deve ser omitido e o tamanho da página padrão utilizado.)

```cs
// Note: in this demo, we'll use a very small page size (2 record sets) to demonstrate paging
// In practice, to improve performance you would use a large page size or just use the system default
int recordSets = 0;
var page = await dnsClient.RecordSets.ListAllInResourceGroupAsync(resourceGroupName, zoneName, "2");
recordSets += page.Count();

while (page.NextPageLink != null)
{
    page = await dnsClient.RecordSets.ListAllInResourceGroupNextAsync(page.NextPageLink);
    recordSets += page.Count();
}
```

## <a name="next-steps"></a>Passos seguintes

Descarregue o [projeto de amostra Azure DNS .NET SDK,](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)que inclui outros exemplos sobre como utilizar o Azure DNS .NET SDK, incluindo exemplos para outros tipos de registos DNS.