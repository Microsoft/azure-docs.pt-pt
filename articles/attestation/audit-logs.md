---
title: Registos de auditoria da Azure Attestation
description: Descreve os registos de auditoria completos que são recolhidos para a Azure Attestation
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: reference
ms.date: 11/23/2020
ms.author: mbaldwin
ms.openlocfilehash: 1fa4a458a4e3e1df1d84c343a32e3a41a4a25e75
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "95759028"
---
# <a name="audit-logs-for-azure-attestation"></a>Registos de auditoria para a Azure Attestation

Os registos de auditoria são registos seguros, imutáveis e com prazos de eventos discretos que ocorreram ao longo do tempo. Estes registos capturam eventos importantes que podem afetar a funcionalidade da sua instância de atestação.

A Azure Attestation gere instâncias de atestação e as políticas associadas a eles. As ações associadas à gestão de casos e às alterações de políticas são auditadas e registadas.

Este artigo contém informações sobre os eventos que são registados, as informações recolhidas e a localização destes registos.

## <a name="about-audit-logs"></a>Sobre registos de auditoria

O Azure Attestation utiliza código para produzir registos de auditoria para eventos que afetam a forma como o atestado é realizado. Isto normalmente resume-se a como ou quando as mudanças de política são feitas para o seu caso de atestado, bem como algumas ações de administração.

### <a name="auditable-events"></a>Eventos auditáveis
Aqui estão alguns dos registos de auditoria que recolhemos:

|     Evento/API                              |     Descrição do Evento                                                                         |
|--------------------------------------------|-----------------------------------------------------------------------------------------------|
|     Criar Instância                        |     Cria uma nova instância de um serviço de atestado. |
|     Destruir exemplo                       |     Destrói um caso de serviço de atestado. |
|     Adicionar Certificado de Política                 |     Adição de um certificado ao atual conjunto de certificados de gestão de políticas. |
|     Remover Certificado de Política              |     Remova um certificado do conjunto atual de certificados de gestão de políticas. |
|     Definir Política Atual                     |     Define a política de atestado para um determinado tipo TEE. |
|     Política de Atestation reset               |     Reinicia a política de atestado para um determinado tipo TEE. |
|     Preparar para atualizar a política               |     Prepare-se para atualizar a política de atestado para um determinado tipo TEE. |
|     Inquilinos rehidratas após desastre       |     Re-selam todos os inquilinos do atestado neste caso do serviço de atestado. Isto só pode ser realizado pelos administradores do Attestation Service. |

### <a name="collected--information"></a>Informação recolhida
Para cada um destes eventos, a Azure Attestation recolhe as seguintes informações:

- Nome da Operação
- Operação Sucesso
- Operação Caller, que pode ser qualquer um dos seguintes:
    - Azure AD UPN
    - ID do Objeto
    - Certificado
    - ID de Inquilino do Azure AD
- Operação Target, que pode ser qualquer um dos seguintes:
    - Ambiente
    - Região de Serviços
    - Função de Serviço
    - Instância de função de serviço
    - ID do Recurso
    - Região de Recursos

### <a name="sample-audit-log"></a>Registo de auditoria de amostra

Os registos de auditoria são fornecidos no formato JSON. Aqui está um exemplo de como um registo de auditoria pode parecer.

```json
{"operationName":"SetCurrentPolicy","resultType":"Success","resultDescription":null,"auditEventCategory":["ApplicationManagement"],"nCloud":null,"requestId":null,"callerIpAddress":null,"callerDisplayName":null,"callerIdentities":[{"callerIdentityType":"ObjectID","callerIdentity":"<some object ID>"},{"callerIdentityType":"TenantId","callerIdentity":"<some tenant ID>"}],"targetResources":[{"targetResourceType":"Environment","targetResourceName":"PublicCloud"},{"targetResourceType":"ServiceRegion","targetResourceName":"EastUS2"},{"targetResourceType":"ServiceRole","targetResourceName":"AttestationRpType"},{"targetResourceType":"ServiceRoleInstance","targetResourceName":"<some service role instance>"},{"targetResourceType":"ResourceId","targetResourceName":"/subscriptions/<some subscription ID>/resourceGroups/<some resource group name>/providers/Microsoft.Attestation/attestationProviders/<some instance name>"},{"targetResourceType":"ResourceRegion","targetResourceName":"EastUS2"}],"ifxAuditFormat":"Json","env_ver":"2.1","env_name":"#Ifx.AuditSchema","env_time":"2020-11-23T18:23:29.9427158Z","env_epoch":"MKZ6G","env_seqNum":1277,"env_popSample":0.0,"env_iKey":null,"env_flags":257,"env_cv":"##00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000_00000000-0000-0000-0000-000000000000","env_os":null,"env_osVer":null,"env_appId":null,"env_appVer":null,"env_cloud_ver":"1.0","env_cloud_name":null,"env_cloud_role":null,"env_cloud_roleVer":null,"env_cloud_roleInstance":null,"env_cloud_environment":null,"env_cloud_location":null,"env_cloud_deploymentUnit":null}
```

## <a name="access-audit-logs"></a>Aceder a Registos de Auditoria

Estes registos são armazenados em Azure e não podem ser acedidos diretamente. Se precisar de aceder a estes registos, preencha um bilhete de apoio. Para obter mais informações, consulte [contacte o Microsoft Support](https://azure.microsoft.com/support/options/). 

Assim que o bilhete de suporte for arquivado, a Microsoft irá descarregar e fornecer-lhe acesso a estes registos.
