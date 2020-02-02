---
title: Criar zonas DNS e conjuntos de registros usando o SDK do .NET
titleSuffix: Azure DNS
description: Neste roteiro de aprendizagem, comece a criar zonas DNS e conjuntos de registros no DNS do Azure usando o SDK do .NET.
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
ms.openlocfilehash: c497209e456ff838786edaa19e46ebc5c1858d5f
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938865"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas DNS e conjuntos de registros usando o SDK do .NET

Você pode automatizar operações para criar, excluir ou atualizar zonas DNS, conjuntos de registros e registros usando o SDK do DNS com a biblioteca de gerenciamento do DNS do .NET. Um projeto completo do Visual Studio está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Criar uma conta de entidade de serviço

Normalmente, o acesso programático aos recursos do Azure é concedido por meio de uma conta dedicada em vez de suas próprias credenciais de usuário. Essas contas dedicadas são chamadas de contas de "entidade de serviço". Para usar o projeto de exemplo do SDK do DNS do Azure, primeiro você precisa criar uma conta de entidade de serviço e atribuir a ela as permissões corretas.

1. Siga [estas instruções](../active-directory/develop/howto-authenticate-service-principal-powershell.md) para criar uma conta de entidade de serviço (o projeto de exemplo do SDK do DNS do Azure pressupõe autenticação baseada em senha).
2. Crie um grupo de recursos ([Veja como](../azure-resource-manager/templates/deploy-portal.md)).
3. Use o RBAC do Azure para conceder as permissões ' colaborador da zona DNS ' da conta da entidade de serviço ao grupo de recursos ([Veja como](../role-based-access-control/role-assignments-portal.md).)
4. Se você estiver usando o projeto de exemplo do SDK do DNS do Azure, edite o arquivo ' Program. cs ' da seguinte maneira:

   * Insira os valores corretos para o `tenantId`, `clientId` (também conhecido como ID da conta), `secret` (senha da conta da entidade de serviço) e `subscriptionId` conforme usado na etapa 1.
   * Insira o nome do grupo de recursos escolhido na etapa 2.
   * Insira um nome de zona DNS de sua escolha.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes NuGet e declarações de namespace

Para usar o SDK do .NET do Azure DNS, você precisa instalar o pacote NuGet da **biblioteca de gerenciamento do DNS do Azure** e outros pacotes do Azure necessários.

1. No **Visual Studio**, abra um projeto ou um novo projeto.
2. Vá para **ferramentas** **>** **Gerenciador de pacotes NuGet** **>** **gerenciar pacotes NuGet para solução...** .
3. Clique em **procurar**, habilite a caixa de seleção **incluir pré-lançamento** e digite **Microsoft. Azure. Management. DNS** na caixa de pesquisa.
4. Selecione o pacote e clique em **instalar** para adicioná-lo ao seu projeto do Visual Studio.
5. Repita o processo acima para instalar também os seguintes pacotes: **Microsoft. REST. ClientRuntime. Azure. Authentication** e **Microsoft. Azure. Management. ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicionar as seguintes declarações de namespace

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializar o cliente de gerenciamento DNS

O `DnsManagementClient` contém os métodos e as propriedades necessários para gerenciar zonas DNS e conjuntos de registros.  O código a seguir faz logon na conta da entidade de serviço e cria um objeto `DnsManagementClient`.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Criar ou atualizar uma zona DNS

Para criar uma zona DNS, primeiro um objeto "zona" é criado para conter os parâmetros de zona DNS. Como as zonas DNS não estão vinculadas a uma região específica, o local é definido como ' global '. Neste exemplo, um [Azure Resource Manager ' tag '](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) também é adicionado à zona.

Para realmente criar ou atualizar a zona no DNS do Azure, o objeto de zona que contém os parâmetros de zona é passado para o método `DnsManagementClient.Zones.CreateOrUpdateAsyc`.

> [!NOTE]
> O DnsManagementClient dá suporte a três modos de operação: síncrono (' CreateOrUpdate '), assíncrono (' CreateOrUpdateAsync ') ou assíncrono com acesso à resposta HTTP (' CreateOrUpdateWithHttpMessagesAsync ').  Você pode escolher qualquer um desses modos, dependendo das necessidades do seu aplicativo.

O DNS do Azure dá suporte à simultaneidade otimista, chamada [ETags](dns-getstarted-create-dnszone.md). Neste exemplo, a especificação de "*" para o cabeçalho "If-None-Match" informa ao DNS do Azure para criar uma zona DNS, caso ainda não exista uma.  A chamada falhará se uma zona com o nome fornecido já existir no grupo de recursos fornecido.

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

## <a name="create-dns-record-sets-and-records"></a>Criar registros e conjuntos de registros DNS

Os registros DNS são gerenciados como um conjunto de registros. Um conjunto de registros é um conjunto de registros com o mesmo nome e tipo de registro dentro de uma zona.  O nome do conjunto de registros é relativo ao nome da zona, não ao nome DNS totalmente qualificado.

Para criar ou atualizar um conjunto de registros, um objeto de parâmetros "RecordSet" é criado e passado para `DnsManagementClient.RecordSets.CreateOrUpdateAsync`. Assim como acontece com as zonas DNS, há três modos de operação: síncrono (' CreateOrUpdate '), assíncrono (' CreateOrUpdateAsync ') ou assíncrono com acesso à resposta HTTP (' CreateOrUpdateWithHttpMessagesAsync ').

Assim como ocorre com as zonas DNS, as operações em conjuntos de registros incluem suporte para simultaneidade otimista.  Neste exemplo, como nenhuma ' If-Match ' nem ' If-None-Match ' são especificados, o conjunto de registros é sempre criado.  Essa chamada substitui qualquer conjunto de registros existente com o mesmo nome e tipo de registro nesta zona DNS.

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

## <a name="get-zones-and-record-sets"></a>Obter zonas e conjuntos de registros

Os métodos `DnsManagementClient.Zones.Get` e `DnsManagementClient.RecordSets.Get` recuperam zonas e conjuntos de registros individuais, respectivamente. Os conjuntos de registros são identificados por seu tipo, nome e zona e grupo de recursos em que existem. As zonas são identificadas por seu nome e o grupo de recursos em que existem.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Atualizar um conjunto de registros existente

Para atualizar um conjunto de registros DNS existente, primeiro recupere o conjunto de registros e, em seguida, atualize o conteúdo do conjunto de registros e, em seguida, envie a alteração.  Neste exemplo, especificamos ' ETag ' do conjunto de registros recuperado no parâmetro ' If-Match '. A chamada falhará se uma operação simultânea tiver modificado o conjunto de registros enquanto isso ocorre.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Listar zonas e conjuntos de registros

Para listar zonas, use os métodos *DnsManagementClient. Zones. List...* , que dão suporte à listagem de todas as zonas em um determinado grupo de recursos ou todas as zonas em uma determinada assinatura do Azure (entre grupos de recursos.) Para listar conjuntos de registros, use os métodos *DnsManagementClient. Recordsets. List...* , que dão suporte à listagem de todos os conjuntos de registros em uma determinada zona ou apenas aos conjuntos de registros de um tipo específico.

Observação ao listar zonas e conjuntos de registros que os resultados podem ser paginados.  O exemplo a seguir mostra como iterar pelas páginas de resultados. (Um tamanho de página artificialmente pequeno de ' 2 ' é usado para forçar a paginação; na prática, esse parâmetro deve ser omitido e o tamanho de página padrão usado.)

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

Baixe o [projeto de exemplo do SDK do .net do Azure DNS](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que inclui mais exemplos de como usar o SDK do .net do Azure DNS, incluindo exemplos para outros tipos de registro DNS.
