---
title: Exportar ou importar sua configuração de provisionamento usando o API do Graph | Microsoft Docs
description: Saiba como exportar e importar a configuração de provisionamento usando o API do Graph.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c3e92ee5ffd97174331703b703e811bd1ce5f43
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/09/2019
ms.locfileid: "70815909"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Exportar ou importar sua configuração de provisionamento usando API do Graph

Você pode usar Microsoft Graph API e o explorador do Graph para exportar os mapeamentos e o esquema de atributo de provisionamento de usuário para um arquivo JSON e importá-los de volta para o Azure AD. Você também pode usar as etapas capturadas aqui para criar um backup de sua configuração de provisionamento. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar a ID da entidade de serviço do aplicativo de provisionamento (ID do objeto)

1. Inicie o [portal do Azure](https://portal.azure.com)e navegue até a seção Propriedades do seu aplicativo de provisionamento. Por exemplo, se você quiser exportar seu *WORKDAY para* o mapeamento de aplicativo de provisionamento de usuário do AD, navegue até a seção de propriedades desse aplicativo. 
1. Na seção Propriedades do aplicativo de provisionamento, copie o valor de GUID associado ao campo ID de *objeto* . Esse valor também é chamado de **servicePrincipalName** do seu aplicativo e ele será usado em operações do Gerenciador de gráficos.

   ![ID da entidade de serviço do aplicativo workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Entrar no Microsoft Graph Explorer

1. Iniciar o [Gerenciador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "entrar com a Microsoft" e entre usando as credenciais de administrador global ou de administrador de aplicativo do Azure AD.

    ![Entrada no grafo](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Após a entrada bem-sucedida, você verá os detalhes da conta de usuário no painel esquerdo.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passo 3: Recuperar a ID do trabalho de provisionamento do aplicativo de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET substituindo [servicePrincipalName] pelo **servicePrincipalName** extraído da [etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Você receberá uma resposta conforme mostrado abaixo. Copie o "atributo de ID" presente na resposta. Esse valor é o **ProvisioningJobId** e será usado para recuperar os metadados de esquema subjacentes.

   [![ID do trabalho de provisionamento](./media/export-import-provisioning-mappings/wd_export_03.png)](./media/export-import-provisioning-mappings/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Passo 4: Baixar o esquema de provisionamento

No Microsoft Graph Explorer, execute a seguinte consulta GET, substituindo [servicePrincipalName] e [ProvisioningJobId] com o servicePrincipalName e o ProvisioningJobId recuperado nas etapas anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON da resposta e salve-o em um arquivo para criar um backup do esquema.

## <a name="step-5-import-the-provisioning-schema"></a>Passo 5: Importar o esquema de provisionamento

> [!CAUTION]
> Execute esta etapa somente se precisar modificar o esquema para configuração que não pode ser alterada usando o portal do Azure ou se você precisar restaurar a configuração de um arquivo de backup anterior com um esquema válido e funcional.

No Microsoft Graph Explorer, configure a seguinte consulta PUT, substituindo [servicePrincipalName] e [ProvisioningJobId] com o servicePrincipalName e o ProvisioningJobId recuperado nas etapas anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Na guia "corpo da solicitação", copie o conteúdo do arquivo de esquema JSON.

   [![Corpo da solicitação](./media/export-import-provisioning-mappings/wd_export_04.png)](./media/export-import-provisioning-mappings/wd_export_04.png#lightbox)

Na guia "cabeçalhos de solicitação", adicione o atributo de cabeçalho Content-Type com o valor "Application/JSON"

   [![Cabeçalhos de solicitação](./media/export-import-provisioning-mappings/wd_export_05.png)](./media/export-import-provisioning-mappings/wd_export_05.png#lightbox)

Clique no botão "executar consulta" para importar o novo esquema.
