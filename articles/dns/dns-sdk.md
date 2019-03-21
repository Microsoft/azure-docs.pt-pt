---
title: Criar zonas DNS e conjuntos de registos no DNS do Azure com o SDK .NET | Documentos da Microsoft
description: Como criar zonas DNS e conjuntos de registos no DNS do Azure com o SDK de .NET.
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.assetid: eed99b87-f4d4-4fbf-a926-263f7e30b884
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2016
ms.author: victorh
ms.openlocfilehash: a06d629087e853c2578e6d35a2ea90c5a8eff840
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57860606"
---
# <a name="create-dns-zones-and-record-sets-using-the-net-sdk"></a>Criar zonas DNS e conjuntos de registos com o SDK .NET

É possível automatizar as operações criar, eliminar ou atualizar as zonas, conjuntos de registos e registos DNS com o SDK de DNS com a biblioteca de gestão de DNS do .NET. Um projeto do Visual Studio completo está disponível [aqui.](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True)

## <a name="create-a-service-principal-account"></a>Criar uma conta do principal de serviço

Normalmente, o acesso programático a recursos do Azure é concedido através de uma conta dedicada, em vez de suas próprias credenciais de utilizador. Estas contas dedicadas são chamadas de contas de "principal de serviço". Para utilizar o projeto de exemplo do SDK do DNS do Azure, tem primeiro de criar uma conta do principal de serviço e atribua-lhe as permissões corretas.

1. Siga [estas instruções](../active-directory/develop/howto-authenticate-service-principal-powershell.md) para criar uma conta de principal de serviço (o projeto de exemplo do SDK do DNS do Azure assume que a autenticação baseada em palavra-passe.)
2. Criar um grupo de recursos ([aqui está como](../azure-resource-manager/resource-group-template-deploy-portal.md)).
3. Utilizar o Azure RBAC para conceder permissões de "Contribuinte de zona DNS" para o grupo de recursos da conta do principal de serviço ([aqui está como](../role-based-access-control/role-assignments-portal.md).)
4. Se utilizar o projeto de exemplo do SDK do DNS do Azure, edite o ficheiro de "program.cs" da seguinte forma:

   * Inserir os valores corretos para o `tenantId`, `clientId` (também conhecido como ID de conta), `secret` (senha da conta do principal de serviço) e `subscriptionId` conforme descrito no passo 1.
   * Introduza o nome do grupo de recursos selecionado na etapa 2.
   * Introduza um nome de zona DNS da sua preferência.

## <a name="nuget-packages-and-namespace-declarations"></a>Pacotes NuGet e declarações de espaço de nomes

Para utilizar o SDK de .NET de DNS do Azure, tem de instalar o **biblioteca de gestão de DNS do Azure** pacote NuGet e outras necessário pacotes do Azure.

1. Na **Visual Studio**, abra um projeto ou um novo projeto.
2. Aceda a **ferramentas** **>** **Gestor de pacotes NuGet** **>** **gerir pacotes NuGet para Solução...** .
3. Clique em **navegue**, ativar a **incluir pré-lançamento** caixa de verificação e o tipo **Microsoft.Azure.Management.Dns** na caixa de pesquisa.
4. Selecione o pacote e clique em **instalar** para adicioná-lo ao seu projeto do Visual Studio.
5. Repita o processo acima para também instalar os seguintes pacotes: **Microsoft.Rest.ClientRuntime.Azure.Authentication** e **Microsoft.Azure.Management.ResourceManager**.

## <a name="add-namespace-declarations"></a>Adicionar declarações de espaço de nomes

Adicione as seguintes declarações de espaço de nomes

```cs
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.Dns;
using Microsoft.Azure.Management.Dns.Models;
```

## <a name="initialize-the-dns-management-client"></a>Inicializar o cliente de gestão de DNS

O `DnsManagementClient` contém os métodos e propriedades necessárias para gerir zonas DNS e conjuntos de registos.  O código a seguir inicia sessão na conta de principal de serviço e cria um `DnsManagementClient` objeto.

```cs
// Build the service credentials and DNS management client
var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, secret);
var dnsClient = new DnsManagementClient(serviceCreds);
dnsClient.SubscriptionId = subscriptionId;
```

## <a name="create-or-update-a-dns-zone"></a>Criar ou atualizar uma zona DNS

Para criar uma zona DNS, primeiro um "Zona" objeto é criado para conter os parâmetros de zona DNS. Uma vez que as zonas de DNS não estão ligadas a uma região específica, a localização está definida como 'global'. Neste exemplo, um [do Azure Resource Manager "tag"](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) também é adicionado à zona.

Para realmente criar ou atualizar a zona no DNS do Azure, o objeto de zona que contém os parâmetros de zona é passado para o `DnsManagementClient.Zones.CreateOrUpdateAsyc` método.

> [!NOTE]
> DnsManagementClient suporta três modos de operação: síncrona ('CreateOrUpdate"), assíncrona ('CreateOrUpdateAsync"), ou assíncronos com acesso à resposta de HTTP ('CreateOrUpdateWithHttpMessagesAsync').  Pode escolher qualquer um desses modos, consoante as suas necessidades de aplicação.

DNS do Azure suporta a simultaneidade otimista, chamada [Etags](dns-getstarted-create-dnszone.md). Neste exemplo, especificar "*" para o "If-None-Match" cabeçalho, informa ao DNS do Azure para criar uma zona DNS, se ainda não existir.  A chamada falha se uma zona com o nome especificado já existe no grupo de recursos específico.

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

## <a name="create-dns-record-sets-and-records"></a>Criar conjuntos de registos DNS e registos

Registos DNS são geridos como um conjunto de registos. Um conjunto de registos é um conjunto de registos com o mesmo nome e tipo de registo dentro de uma zona.  O nome do conjunto de registos é relativo ao nome da zona, não o nome DNS completamente qualificado.

Para criar ou atualizar um conjunto de registos, é criado e transmitido para um objeto de parâmetros de "Conjunto de registros" `DnsManagementClient.RecordSets.CreateOrUpdateAsync`. Como com zonas DNS, existem três modos de operação: síncrona ('CreateOrUpdate"), assíncrona ('CreateOrUpdateAsync"), ou assíncronos com acesso à resposta de HTTP ('CreateOrUpdateWithHttpMessagesAsync').

Tal como acontece com zonas DNS, operações em conjuntos de registos incluem suporte para a simultaneidade otimista.  Neste exemplo, uma vez que forem especificados nem 'If-Match' nem "If-None-Match", o conjunto de registos é sempre criado.  Esta chamada substitui qualquer conjunto com o mesmo nome e tipo de registo nesta zona DNS de registos existente.

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

## <a name="get-zones-and-record-sets"></a>Obtenha as zonas e conjuntos de registos

O `DnsManagementClient.Zones.Get` e `DnsManagementClient.RecordSets.Get` métodos obter zonas individuais e conjuntos de registos, respectivamente. Conjuntos de registros são identificados pelo respetivo tipo, nome e o grupo de recursos e de zona existam na. As zonas são identificadas pelo respetivo nome e o grupo de recursos que existam na.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);
```

## <a name="update-an-existing-record-set"></a>Atualizar um conjunto de registos existente

Para atualizar um conjunto de registos de DNS existente, primeiro obter o conjunto de registos, em seguida, atualizar o conteúdo do conjunto de registos, em seguida, envie a alteração.  Neste exemplo, podemos especificar 'Etag"do conjunto de registro obtidas no parâmetro If-Match. A chamada falha se uma operação simultânea modificou o registo definido nesse meio tempo.

```cs
var recordSet = dnsClient.RecordSets.Get(resourceGroupName, zoneName, recordSetName, RecordType.A);

// Add a new record to the local object.  Note that records in a record set must be unique/distinct
recordSet.ARecords.Add(new ARecord("5.6.7.8"));

// Update the record set in Azure DNS
// Note: ETAG check specified, update will be rejected if the record set has changed in the meantime
recordSet = await dnsClient.RecordSets.CreateOrUpdateAsync(resourceGroupName, zoneName, recordSetName, RecordType.A, recordSet, recordSet.Etag);
```

## <a name="list-zones-and-record-sets"></a>Lista de zonas e conjuntos de registos

Para listar as zonas, utilize o *DnsManagementClient.Zones.List...*  métodos, que suportam a listagem a todas as zonas de um grupo de recursos específico ou todas as zonas de uma determinada subscrição do Azure (entre grupos de recursos.) Para listar os conjuntos de registos, utilize *DnsManagementClient.RecordSets.List...*  métodos, que suportam a listagem de todos os conjuntos de registos numa determinada zona ou apenas esses conjuntos de registos de um tipo específico.

Tenha em atenção quando lista zonas e conjuntos de registos que os resultados podem ser paginados.  O exemplo seguinte mostra como iterar por meio das páginas de resultados. (Um tamanho de página artificialmente pequeno de '2' é utilizado para forçar a paginação; na prática, este parâmetro deve ser omitido e o tamanho de página predefinido utilizado).

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

## <a name="next-steps"></a>Passos Seguintes

Transfira o [projeto de exemplo do SDK de .NET de DNS do Azure](https://www.microsoft.com/en-us/download/details.aspx?id=47268&WT.mc_id=DX_MVP4025064&e6b34bbe-475b-1abd-2c51-b5034bcdd6d2=True), que inclui mais exemplos sobre como utilizar o SDK de .NET de DNS do Azure, incluindo exemplos para outros tipos de registos de DNS.
