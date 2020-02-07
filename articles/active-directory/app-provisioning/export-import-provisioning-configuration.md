---
title: Exportar ou importar a sua configuração de provisionamento utilizando o Gráfico API  Microsoft Docs
description: Aprenda a exportar e importar a configuração de provisionamento utilizando a API do gráfico.
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
ms.openlocfilehash: af699fa2201bce5627426dcdefc1b98c1d885ae5
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77066619"
---
# <a name="export-or-import-your-provisioning-configuration-by-using-graph-api"></a>Exportar ou importar a sua configuração de provisionamento utilizando a API do gráfico

Pode utilizar o Microsoft Graph API e o Graph Explorer para exportar os mapeamentos de atributos e esquemas para um ficheiro JSON e importá-lo de volta para AD Azure. Também pode utilizar os passos capturados aqui para criar uma cópia de segurança da sua configuração de provisionamento. 

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar o seu ID principal do serviço de aplicações de provisionamento (ID do objeto)

1. Lance o [portal Azure](https://portal.azure.com)e navegue para a secção Propriedades da sua aplicação de provisionamento. Para, por exemplo, se pretender exportar o seu Dia de Trabalho para o Mapeamento de *aplicações de fornecimento* de utilizadores ad para a secção Propriedades dessa aplicação. 
1. Na secção Propriedades da sua aplicação de provisionamento, copie o valor GUID associado ao campo ID do *Objeto.* Este valor também é chamado de **ServicePrincipalId** da sua App e será usado em operações do Graph Explorer.

   ![Id principal do serviço de aplicações do dia de trabalho](./media/export-import-provisioning-configuration/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Assine no Microsoft Graph Explorer

1. Lançar [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "Iniciar sessão com a Microsoft" e iniciar sessão utilizando credenciais de Administração Global Azure AD Ou App Admin.

    ![Sign-in de gráfico](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Após o sucesso do início de sessão, verá os detalhes da conta do utilizador no painel da mão esquerda.

## <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Passo 3: Recuperar o ID de trabalho de provisionamento da App de Provisionamento

No Microsoft Graph Explorer, faça a seguinte consulta GET substituindo [servicePrincipalId] pelo **ServiçoPrincipalId** extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Receberá uma resposta como mostrado abaixo. Copie o "atributo id" presente na resposta. Este valor é o **ProvisioningJobId** e será utilizado para recuperar os metadados de esquemas subjacentes.

   [![provisioning Job ID](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

## <a name="step-4-download-the-provisioning-schema"></a>Passo 4: Descarregue o Provisioning Schema

No Microsoft Graph Explorer, faça a seguinte consulta GET, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nos passos anteriores.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Copie o objeto JSON a partir da resposta e guarde-o para um ficheiro para criar uma cópia de segurança do esquema.

## <a name="step-5-import-the-provisioning-schema"></a>Passo 5: Importar o Schema de Provisionamento

> [!CAUTION]
> Execute este passo apenas se precisar de modificar o esquema para configuração que não pode ser alterada usando o portal Azure ou se precisar restaurar a configuração a partir de um ficheiro previamente apoiado com esquema válido e de trabalho.

No Microsoft Graph Explorer, configure a seguinte consulta PUT, substituindo [servicePrincipalId] e [ProvisioningJobId] pelo ServicePrincipalId e o ProvisioningJobId recuperados nos passos anteriores.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

No separador "Request Body", copie o conteúdo do ficheiro esquema JSON.

   [corpo de pedido de ![](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

No separador "Cabeçalhos de Pedido", adicione o atributo cabeçalho do Tipo conteúdo com valor "aplicação/json"

   [cabeçalhos de pedido de ![](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Clique no botão "Run Query" para importar o novo esquema.
