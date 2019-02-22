---
title: Registe-se de inquilinos para utilização de controlo no Azure Stack | Documentos da Microsoft
description: Detalhes sobre as operações utilizados para gerir registos de inquilino e como a utilização de inquilino está registada no Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 6f2a2eb9902e8567b5fa27ed93dd8be2fe3a01b3
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587077"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Gerir o registo do inquilino no Azure Stack

*Aplica-se a: Sistemas integrados do Azure Stack*

Este artigo contém detalhes sobre operações de registo. Pode usar essas operações para:
- Gerir registos de inquilino
- Gerir o controlo de utilização do inquilino

Pode encontrar detalhes sobre como adicionar, lista, ou remova os mapeamentos de inquilino. Pode utilizar o PowerShell ou os pontos finais de API de faturação para gerir a utilização de controlo. Pode encontrar detalhes sobre como adicionar, lista, ou remova os mapeamentos de inquilino. Pode utilizar o PowerShell ou os pontos finais de API de faturação para gerir a utilização de controlo.

## <a name="add-tenant-to-registration"></a>Adicionar o inquilino para registo

Usar a operação quando deseja adicionar um novo inquilino para o registo. Utilização do inquilino é reportada numa subscrição do Azure ligado ao seu inquilino do Azure Active Directory (Azure AD).

Também pode utilizar a operação se pretender alterar a subscrição associada um inquilino. Chame PUT/New-AzureRMResource para substituir o mapeamento anterior.

Pode associar uma única subscrição do Azure com um inquilino. Se tentar adicionar uma segunda assinatura a um inquilino existente, a primeira assinatura é escrita excessiva.

### <a name="use-api-profiles"></a>Utilizar perfis de API

Os cmdlets de registo exigem que especifique um perfil de API quando a executar o PowerShell. Perfis de API representam um conjunto de fornecedores de recursos do Azure e suas versões de API. Eles ajudá-lo a utilizar a versão correta da API ao interagir com várias clouds do Azure. Por exemplo, trabalha com várias clouds ao trabalhar com global do Azure e o Azure Stack. Perfis de especificar um nome que corresponde à respetiva data de lançamento. Terá de utilizar o **2017-09-03** perfil.

Para obter mais informações sobre o Azure Stack e perfis de API, consulte [perfis de versão de API de gerir no Azure Stack](user/azure-stack-version-profiles.md). Para obter instruções sobre como colocar em funcionamento com o perfil de API com o PowerShell, consulte [perfis de versão de API de utilização para o PowerShell no Azure Stack](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Parâmetros

| Parâmetro                  | Descrição |
|---                         | --- |
| registrationSubscriptionID | A subscrição do Azure que foi utilizada para o registo inicial. |
| customerSubscriptionID     | A subscrição do Azure (não o Azure Stack) pertencentes ao cliente sejam registrados. Tem de ser criada na oferta do fornecedor de serviços Cloud (CSP) através do Centro de parceiros. Se um cliente tiver mais do que um inquilino, criar uma subscrição para o inquilino iniciar sessão no Azure Stack. |
| resourceGroup              | O grupo de recursos no Azure em que o registo é armazenado. |
| registrationName           | O nome do registo do seu Azure Stack. É um objeto armazenado no Azure. O nome, normalmente, está a ser azurestack ponto o formulário-CloudID, onde CloudID é o ID de Cloud da sua implementação do Azure Stack. |

> [!Note]  
> Os inquilinos precisam de estar registado em cada Azure Stack que utilizam. Se um inquilino utiliza mais do que um Azure Stack, terá de atualizar os registros de iniciais de cada implementação com a subscrição do inquilino.

### <a name="powershell"></a>PowerShell

Utilize o cmdlet New-AzureRmResource para adicionar um inquilino. [Ligar ao Azure Stack](azure-stack-powershell-configure-admin.md)e, em seguida, a partir de uma linha de comandos elevada, utilize o seguinte cmdlet:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Chamada à API

**Operação**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Resposta**: 201 criado  
**Corpo da resposta**: Vazio  

## <a name="list-all-registered-tenants"></a>Listar registados todos os inquilinos

Obter uma lista de todos os inquilinos que foram adicionados a um registo.

 > [!Note]  
 > Se tiveram sido registados não inquilinos, não receberá nenhuma resposta.

### <a name="parameters"></a>Parâmetros

| Parâmetro                  | Descrição          |
|---                         | ---                  |
| registrationSubscriptionId | A subscrição do Azure que foi utilizada para o registo inicial.   |
| resourceGroup              | O grupo de recursos no Azure em que o registo é armazenado.    |
| registrationName           | O nome do registo do seu Azure Stack. É um objeto armazenado no Azure. O nome é normalmente na forma de **azurestack**-***CloudID***, em que ***CloudID*** é o ID de Cloud da sua implementação do Azure Stack.   |

### <a name="powershell"></a>PowerShell

Utilize o cmdlet Get-AzureRmResource para listar registados todos os inquilinos. [Ligar ao Azure Stack](azure-stack-powershell-configure-admin.md)e, em seguida, a partir de uma linha de comandos elevada, utilize o seguinte cmdlet:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chamada à API

Pode obter uma lista de todos os mapeamentos de inquilino usando a operação GET.

**Operação**: GET  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Resposta**: 200  
**Corpo da resposta**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Remover um mapeamento do inquilino

Pode remover um inquilino que tenha sido adicionado a um registo. Se esse inquilino ainda está usando os recursos no Azure Stack, é cobrada a utilização das mesmas para a subscrição utilizada no registo do Azure Stack inicial.

### <a name="parameters"></a>Parâmetros

| Parâmetro                  | Descrição          |
|---                         | ---                  |
| registrationSubscriptionId | ID de subscrição para o registo.   |
| resourceGroup              | O grupo de recursos para o registo.   |
| registrationName           | O nome do registo.  |
| customerSubscriptionId     | O ID de subscrição de cliente.  |

### <a name="powershell"></a>PowerShell

Utilize o cmdlet Remove-AzureRmResource para remover um inquilino. [Ligar ao Azure Stack](azure-stack-powershell-configure-admin.md)e, em seguida, a partir de uma linha de comandos elevada, utilize o seguinte cmdlet:

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Chamada à API

Pode remover os mapeamentos de inquilino usando a operação de eliminação.

**Operação**: DELETE  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Resposta**: 204-sem conteúdo  
**Corpo da resposta**: Vazio

## <a name="next-steps"></a>Passos Seguintes

 - Para saber mais sobre como recuperar informações de utilização de recursos do Azure Stack, veja [utilização e faturação no Azure Stack](azure-stack-billing-and-chargeback.md).
