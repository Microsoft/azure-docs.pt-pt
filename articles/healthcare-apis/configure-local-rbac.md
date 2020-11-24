---
title: Configure o controlo de acesso local baseado em funções (RBAC local) para a Azure API para fHIR
description: Este artigo descreve como configurar a API Azure para a FHIR usar um inquilino externo da AD Azure para o plano de dados
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 03/15/2020
ms.author: matjazl
ms.openlocfilehash: 096e4e3ecbcedaec674e074a2baccbb336e03c94
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95524203"
---
# <a name="configure-local-rbac-for-fhir"></a>Configurar o RBAC local para fHIR 

Este artigo explica como configurar a API Azure para a FHIR utilizar um inquilino externo e secundário do Azure Ative Directory para gerir o acesso a um avião de dados. Utilize este modo apenas se não for possível utilizar o inquilino do Azure Ative Directory associado à sua subscrição.

> [!NOTE]
> Se o seu plano de dados de serviço FHIR estiver configurado para utilizar o seu inquilino principal do Azure Ative Directory associado à sua subscrição, [utilize o Azure RBAC para atribuir funções de plano de dados](configure-azure-rbac.md).

## <a name="add-service-principal"></a>Adicionar principal de serviço

O RBAC local permite-lhe utilizar um inquilino externo do Azure Ative Directory com o seu servidor FHIR. A fim de permitir que o sistema local de RBAC verifique os membros do grupo neste inquilino, a Azure API para fHIR deve ter um diretor de serviço no arrendatário. Este diretor de serviço será criado automaticamente em inquilinos ligados a subscrições que tenham implantado a AZure API para fHIR, mas caso o seu inquilino não tenha nenhuma subscrição ligada a ele, um administrador de inquilino terá de criar este diretor de serviço com um dos seguintes comandos:

Utilizando o `Az` módulo PowerShell:

```azurepowershell-interactive
New-AzADServicePrincipal -ApplicationId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

ou pode utilizar o `AzureAd` módulo PowerShell:

```azurepowershell-interactive
New-AzureADServicePrincipal -AppId 3274406e-4e0a-4852-ba4f-d7226630abb7
```

ou pode utilizar o Azure CLI:

```azurecli-interactive
az ad sp create --id 3274406e-4e0a-4852-ba4f-d7226630abb7
```

## <a name="configure-local-rbac"></a>Configurar o RBAC local

Pode configurar a AZure API para fHIR para utilizar um inquilino externo ou secundário do Azure Ative Directory na lâmina **de autenticação:**

![Atribuições locais do RBAC](media/rbac/local-rbac-guids.png).

Na caixa de autoridade, insira um inquilino do Azure Ative Directory válido. Uma vez validado o inquilino, a caixa de identificação de **objetos permitidos** deve ser ativada e pode introduzir uma lista de identificações de objetos de identidade. Estes IDs podem ser as identidades dos objetos de identidade de:

* Um utilizador do Azure Ative Directory.
* Um diretor de serviço do Azure Ative Directory.
* Um grupo de segurança de diretório azure ative.

Pode ler o artigo sobre como [encontrar iDs de objeto de identidade](find-identity-object-ids.md) para mais detalhes.

Depois de introduzir os IDs de objetos necessários, clique em **Guardar** e aguarde que as alterações sejam guardadas antes de tentar aceder ao plano de dados utilizando os utilizadores, principais ou grupos designados.

## <a name="caching-behavior"></a>Comportamento caching

A Azure API para fHIR irá cache decisões por até 5 minutos. Se conceder a um utilizador acesso ao servidor FHIR adicionando-os à lista de IDs de objetos permitidos, ou removê-los da lista, deverá esperar que leve até cinco minutos para que as alterações nas permissões se propaguem.

## <a name="next-steps"></a>Passos seguintes

Neste artigo, você aprendeu a atribuir o acesso de avião de dados FHIR usando um inquilino externo (secundário) Azure Ative Directory. Em seguida, saiba mais sobre as definições adicionais para a AZure API para FHIR:
 
>[!div class="nextstepaction"]
>[Definições adicionais Azure API para FHIR](azure-api-for-fhir-additional-settings.md)
