---
title: Ignorar a exclusão de usuários fora do escopo | Microsoft Docs
description: Saiba como substituir o comportamento padrão de desprovisionamento de usuários de escopo.
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
ms.date: 10/03/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3b4a8005cf308d5cfce02976e3b2eff39d5fe8c0
ms.sourcegitcommit: 7868d1c40f6feb1abcafbffcddca952438a3472d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71958640"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Ignorar a exclusão de contas de usuário que saem do escopo

Por padrão, o mecanismo de provisionamento do Azure AD exclui ou desabilita usuários que saem do escopo. No entanto, para determinados cenários como o WORKDAY para o provisionamento de entrada do usuário do AD, esse comportamento pode não ser o esperado e talvez você queira substituir esse comportamento padrão.  

Este guia descreve como usar a API do Microsoft Graph e o Gerenciador de API do Microsoft Graph para definir o sinalizador ***SkipOutOfScopeDeletions*** que controla o processamento de contas que saem do escopo. 
* Se ***SkipOutOfScopeDeletions*** for definido como 0 (false), as contas que saem do escopo ficarão desabilitadas no destino
* Se ***SkipOutOfScopeDeletions*** for definido como 1 (true), as contas que saem do escopo não serão desabilitadas no destino esse sinalizador será definido no nível do aplicativo de *provisionamento* e poderá ser configurado usando o API do Graph. 

Como essa configuração é amplamente usada com o *WORKDAY para Active Directory* aplicativo de provisionamento de usuário, as etapas a seguir incluem capturas de tela do aplicativo workday. No entanto, isso também pode ser usado com outros aplicativos de provisionamento.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar a ID da entidade de serviço do aplicativo de provisionamento (ID do objeto)

1. Inicie o [portal do Azure](https://portal.azure.com)e navegue até a seção Propriedades do seu aplicativo de provisionamento. Por exemplo, se você quiser exportar seu *WORKDAY para* o mapeamento de aplicativo de provisionamento de usuário do AD, navegue até a seção de propriedades desse aplicativo. 
1. Na seção Propriedades do aplicativo de provisionamento, copie o valor de GUID associado ao campo ID de *objeto* . Esse valor também é chamado de **servicePrincipalName** do seu aplicativo e ele será usado em operações do Gerenciador de gráficos.

   ![ID da entidade de serviço do aplicativo workday](./media/export-import-provisioning-mappings/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Entrar no Microsoft Graph Explorer

1. Iniciar o [Gerenciador de Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "entrar com a Microsoft" e entre usando as credenciais de administrador global ou de administrador de aplicativo do Azure AD.

    ![Entrada no grafo](./media/export-import-provisioning-mappings/wd_export_02.png)

1. Após a entrada bem-sucedida, você verá os detalhes da conta de usuário no painel esquerdo.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Passo 3: Obter credenciais de aplicativo e detalhes de conectividade existentes

No Microsoft Graph Explorer, execute a seguinte consulta GET substituindo [servicePrincipalName] pelo **servicePrincipalName** extraído da [etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![OBTER consulta de trabalho](./media/skip-out-of-scope-deletions/skip-03.png)

Copie a resposta em um arquivo de texto. Ele se parecerá com o texto JSON mostrado abaixo, com valores realçados em amarelo específico para sua implantação. Adicione as linhas realçadas em verde ao final e atualize a senha da conexão workday realçada em azul. 

   ![OBTER resposta do trabalho](./media/skip-out-of-scope-deletions/skip-04.png)

Aqui está o bloco JSON a ser adicionado ao mapeamento. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Passo 4: Atualizar o ponto de extremidade dos segredos com o sinalizador SkipOutOfScopeDeletions

No explorador do Graph, execute o comando a seguir para atualizar o ponto de extremidade dos segredos com o sinalizador ***SkipOutOfScopeDeletions*** . 

Na URL abaixo, substitua [servicePrincipalName] pelo **servicePrincipalName** extraído da [etapa 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Copie o texto atualizado da etapa 3 para o "corpo da solicitação" e defina o cabeçalho "Content-Type" como "Application/JSON" em "cabeçalhos de solicitação". 

   ![Solicitação PUT](./media/skip-out-of-scope-deletions/skip-05.png)

Clique em "executar consulta". 

Você deve obter a saída como "êxito – código de status 204". 

   ![COLOCAR resposta](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Passo 5: Verificar se os usuários fora do escopo não são desabilitados

Você pode testar esse sinalizador resulta no comportamento esperado atualizando suas regras de escopo para ignorar um usuário específico. No exemplo a seguir, estamos excluindo o funcionário com a ID 21173 (que estava anteriormente no escopo) adicionando uma nova regra de escopo: 

   ![Exemplo de escopo](./media/skip-out-of-scope-deletions/skip-07.png)

No próximo ciclo de provisionamento, o serviço de provisionamento do Azure AD identificará que o usuário 21173 saiu do escopo e, se a propriedade SkipOutOfScopeDeletions estiver habilitada, a regra de sincronização desse usuário exibirá uma mensagem, conforme mostrado abaixo: 

   ![Exemplo de escopo](./media/skip-out-of-scope-deletions/skip-08.png)


