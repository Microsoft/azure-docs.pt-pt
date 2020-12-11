---
title: Sincronização de entrada para provisão em nuvem usando API de gráfico de MS
description: Este tópico descreve como permitir a sincronização de entrada usando apenas a API do Gráfico
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c6ed106462d7dcac3f31a2ab157e5b634391006
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/10/2020
ms.locfileid: "97093456"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Sincronização de entrada para provisão em nuvem usando API de gráfico de MS

O documento que se segue descreve como replicar um perfil de sincronização do zero utilizando apenas APIs MSGraph.  
A estrutura de como fazê-lo consiste nos seguintes passos.  A saber:

- [Configuração básica](#basic-setup)
- [Criar diretores de serviços](#create-service-principals)
- [Criar Trabalho de Sincronização](#create-sync-job)
- [Atualizar domínio direcionado](#update-targeted-domain)
- [Iniciar trabalho de sincronização](#start-sync-job)
- [Estado de revisão](#review-status)

Utilize estes [comandos Microsoft Azure Ative Directory module para](https://docs.microsoft.com/powershell/module/msonline/) comandos Windows PowerShell para permitir a sincronização para um inquilino de produção, um pré-requisito para poder ligar para o Serviço Web da Administração para esse inquilino.

## <a name="basic-setup"></a>Configuração básica

### <a name="enable-tenant-flags"></a>Ativar bandeiras de inquilinos

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
O primeiro desses dois comandos requer credenciais do Azure Ative Directory. Estes comandos identificam implicitamente o inquilino e permitem a sincronização.

## <a name="create-service-principals"></a>Criar principais de serviço
Em seguida, precisamos criar a [aplicação/diretor de serviço AD2AAD](/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Você precisa usar esta aplicação ID 1a4721b3-e57f-4451-ae87-ef078703ec94. O nome de exibição é o url de domínio AD, se utilizado no portal (por exemplo, contoso.com), mas pode ser nomeado outra coisa.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Criar trabalho de sincronização
A saída do comando acima devolverá o objectId do principal de serviço que foi criado. Para este exemplo, o objectId é 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  Utilize o Microsoft Graph para adicionar um synchronizationJob a esse responsável de serviço.  

A documentação para a criação de um trabalho de sincronização pode ser encontrada [aqui.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)

Se não gravou o ID acima, pode encontrar o principal de serviço executando a seguinte chamada MS Graph. Você vai precisar de Diretório.Read.Todas as permissões para fazer essa chamada:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Em seguida, procure o nome da sua aplicação na saída.

Executar os dois comandos seguintes para criar dois postos de trabalho: um para o fornecimento de utilizador/grupo e outro para sincronização de haxixe de palavra-passe. É o mesmo pedido duas vezes, mas com diferentes IDs de modelo.


Ligue para os seguintes dois pedidos:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Vai precisar de duas chamadas se quiser criar as duas.

Valor de devolução de exemplo (para provisionamento):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Atualizar domínio direcionado
Para este inquilino, o identificador de objetos e identificador de aplicação do principal de serviço são os seguintes:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-c000-000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000

Vamos precisar de atualizar o domínio que esta configuração está a ser alvo, por isso atualize os segredos para este domínio.

Certifique-se de que o nome de domínio que utiliza é o mesmo url que definiu para o seu controlador de domínio on-prem

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

A resposta esperada é... HTTP 204/Sem conteúdo

Aqui, o valor "Domínio" em destaque é o nome do domínio do Diretório Ativo no local a partir do qual as entradas serão adquir ao Azure Ative Directory.

## <a name="start-sync-job"></a>Iniciar trabalho de sincronização
O trabalho pode ser recuperado novamente através do seguinte comando:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

A documentação para a recuperação de empregos pode ser consultada [aqui.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http) 
 
Para iniciar o trabalho, emita este pedido, utilizando o objectId do titular de serviço criado no primeiro passo, e o identificador de emprego devolveu do pedido que criou o trabalho.

A documentação para como iniciar um trabalho pode ser encontrada [aqui.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http) 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

A resposta esperada é... HTTP 204/Sem conteúdo.

Outros comandos para controlar o trabalho estão documentados [aqui.](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)
 
Para reiniciar um trabalho, usar-se-ia...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Estado de revisão
Recupere o seu estatuto de trabalho através de ...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

Veja na secção 'status' do objeto de retorno para obter detalhes relevantes

## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
- [Transformações](how-to-transformation.md)
- [AZURE AD Sincronização API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
