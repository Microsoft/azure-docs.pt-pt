---
title: Perguntas frequentes sobre a funcionalidade de restauro ponto-a-tempo de Azure Cosmos.
description: Este artigo lista frequentemente perguntas sobre a funcionalidade de restauro ponto-a-tempo Azure Cosmos que é conseguida utilizando o modo de backup contínuo.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 7a41eb8bdd573ac08b0c76eb9a6c2b0724637c39
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/03/2021
ms.locfileid: "99527961"
---
# <a name="frequently-asked-questions-on-the-azure-cosmos-db-point-in-time-restore-feature"></a>Perguntas frequentes sobre a funcionalidade de restauro ponto-a-tempo do Azure Cosmos
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Este artigo lista frequentemente perguntas sobre a funcionalidade de restauro pontual Azure Cosmos DB que é conseguida utilizando o modo de backup contínuo.

## <a name="how-much-time-does-it-takes-to-restore"></a>Quanto tempo leva para restaurar?
A duração do restauro depende do tamanho dos seus dados.

### <a name="can-i-submit-the-restore-time-in-local-time"></a>Posso enviar o tempo de restauro na hora local?
A restauração pode não acontecer dependendo se os recursos-chave, como bases de dados ou contentores, existiam nessa altura. Pode verificar inserindo a hora e olhando para a base de dados ou recipiente selecionados por um determinado tempo. Se não virem recursos para restaurar, então o processo de restauro não funciona.

### <a name="how-can-i-track-if-an-account-is-being-restored"></a>Como posso rastrear se uma conta está sendo restaurada?
Depois de submeter o comando de restauro e aguardar na mesma página, depois de concluída a operação, a barra de estado mostra a mensagem de conta restaurada com sucesso. Também pode pesquisar a conta restaurada e [acompanhar o estado da conta que está a ser restaurada.](continuous-backup-restore-portal.md#track-restore-status) Enquanto a restauração estiver em curso, o estado da conta será "Criar", após a operação de restauro concluída, o estado da conta mudará para "Online".

Da mesma forma para PowerShell e CLI, você pode acompanhar o progresso da operação de restauro executando `az cosmosdb show` o comando da seguinte forma:

```azurecli-interactive
az cosmosdb show --name "accountName" --resource-group "resourceGroup"
```

O ProvisioningState mostra "Bem sucedido" quando a conta está online.

```json
{
"virtualNetworkRules": [],
"writeLocations" : [
{
    "documentEndpoint": "https://<accountname>.documents.azure.com:443/", 
    "failoverpriority": 0,
    "id": "accountName" ,
    "isZoneRedundant" : false, 
    "locationName": "West US 2", 
    "provisioningState": "Succeeded"
}
]
}
```

### <a name="how-can-i-find-out-whether-an-account-was-restored-from-another-account"></a>Como posso descobrir se uma conta foi restaurada de outra conta?
Executar o `az cosmosdb show` comando, na saída, você pode ver que o valor da `createMode` propriedade. Se o valor estiver definido para **Restaurar**. indica que a conta foi restaurada a partir de outra conta. O `restoreParameters` imóvel tem mais detalhes, `restoreSource` como, que tem o ID da conta de origem. O último GUID no `restoreSource` parâmetro é o casoId da conta de origem.

Por exemplo, na seguinte saída, o ID da conta de origem é "7b4bb-f6a0-430e-ade1-638d781830cc"

```json
"restoreParameters": {
   "databasesToRestore" : [],
   "restoreMode": "PointInTime",
   "restoreSource": "/subscriptions/2a5b-f6a0-430e-ade1-638d781830dd/providers/Microsoft.DocumentDB/locations/westus/restorableDatabaseAccounts/7b4bb-f6a0-430e-ade1-638d781830cc",
   "restoreTimestampInUtc": "2020-06-11T22:05:09Z"
}
```

### <a name="what-happens-when-i-restore-a-shared-throughput-database-or-a-container-within-a-shared-throughput-database"></a>O que acontece quando restauro uma base de dados de produção partilhada ou um contentor dentro de uma base de dados de produção partilhada?
Toda a base de dados de produção partilhada foi restaurada. Não é possível escolher um subconjunto de contentores numa base de dados de produção partilhada para ser restaurado.

### <a name="what-is-the-use-of-instanceid-in-the-account-definition"></a>Qual é a utilização do InstanceID na definição de conta?
Em qualquer momento, a propriedade "accountName" da conta da Azure Cosmos é globalmente única enquanto está viva. No entanto, depois de a conta ser eliminada, é possível criar outra conta com o mesmo nome e, portanto, o "nome da conta" já não é suficiente para identificar um caso de conta. 

ID ou "instanceId" é uma propriedade de um caso de uma conta e é usado para desambiguar em várias contas (ao vivo e apagado) se tiverem o mesmo nome para restauro. Pode obter a identificação da instância executando os `Get-AzCosmosDBRestorableDatabaseAccount` comandos ou  `az cosmosdb restorable-database-account` comandos. O valor do atributo nome denota o "InstanceID".

## <a name="next-steps"></a>Passos seguintes

* O que é o modo [de backup contínuo?](continuous-backup-restore-introduction.md)
* Configure e gere a cópia de segurança contínua utilizando [o portal Azure](continuous-backup-restore-portal.md), [PowerShell,](continuous-backup-restore-powershell.md) [CLI](continuous-backup-restore-command-line.md)ou [Azure Resource Manager](continuous-backup-restore-template.md).
* [Gerir as permissões necessárias](continuous-backup-restore-permissions.md) para restaurar os dados com o modo de backup contínuo.
* [Modelo de recurso do modo de backup contínuo](continuous-backup-restore-resource-model.md)