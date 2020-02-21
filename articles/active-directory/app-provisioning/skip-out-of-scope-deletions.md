---
title: Sem a eliminação de utilizadores fora do âmbito de aplicação  Microsoft Docs
description: Aprenda a anular o comportamento padrão de desprovisionamento fora do âmbito dos utilizadores.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: app-provisioning
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1668c022a0f067a381ba09b397c7d38c99ad074
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77522454"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Sem a eliminação das contas de utilizador que ficam fora de âmbito

Por predefinição, o motor de fornecimento de AD Azure elimina ou desativa utilizadores que saem do âmbito. No entanto, para certos cenários como o Workday to AD User Inbound Provisioning este comportamento pode não ser o esperado e você pode querer anular este comportamento padrão.  

Este guia descreve como utilizar o Microsoft Graph API e o explorador da Microsoft Graph API para definir a bandeira ***SkipOutOfScopeDeletions*** que controla o processamento de contas que ficam fora de âmbito. 
* Se ***skipOutOfScopeDeletions*** for definido para 0 (falso), então as contas que desafundam do âmbito serão desativadas no alvo
* Se as ***SkipOutOfScopeDeletions*** estiverem definidas para 1 (verdadeiramente), então as contas que desniveem o seu alcance não serão desativadas no alvo Esta bandeira é definida ao nível da App de *Provisionamento* e pode ser configurada utilizando a API do gráfico. 

Como esta configuração é amplamente utilizada com a aplicação de provisionamento de utilizadores do *Workday para Ative Directory,* os passos abaixo incluem imagens da aplicação Workday. No entanto, isto também pode ser usado com **todas as outras aplicações** tais (ServiceNow, Salesforce, Dropbox, etc.).

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar o seu ID principal do serviço de aplicações de provisionamento (ID do objeto)

1. Lance o [portal Azure](https://portal.azure.com)e navegue para a secção Propriedades da sua aplicação de provisionamento. Para, por exemplo, se pretender exportar o seu Dia de Trabalho para o Mapeamento de *aplicações de fornecimento* de utilizadores ad para a secção Propriedades dessa aplicação. 
1. Na secção Propriedades da sua aplicação de provisionamento, copie o valor GUID associado ao campo ID do *Objeto.* Este valor também é chamado de **ServicePrincipalId** da sua App e será usado em operações do Graph Explorer.

   ![Id principal do serviço de aplicações do dia de trabalho](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Assine no Microsoft Graph Explorer

1. Lançar [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "Iniciar sessão com a Microsoft" e iniciar sessão utilizando credenciais de Administração Global Azure AD Ou App Admin.

    ![Sign-in de gráfico](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Após o sucesso do início de sessão, verá os detalhes da conta do utilizador no painel da mão esquerda.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Passo 3: Obtenha credenciais de aplicativos existentes e detalhes de conectividade

No Microsoft Graph Explorer, faça a seguinte consulta GET substituindo [servicePrincipalId] pelo **ServiçoPrincipalId** extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Obter consulta de emprego](./media/skip-out-of-scope-deletions/skip-03.png)

Copie a Resposta num ficheiro de texto. Será parecido com o texto JSON mostrado abaixo, com valores realçados em amarelo específico para a sua implementação. Adicione as linhas realçadas em verde até ao fim e atualize a senha de ligação workday realçada em azul. 

   ![Obter resposta de emprego](./media/skip-out-of-scope-deletions/skip-04.png)

Aqui está o bloco JSON para adicionar ao mapeamento. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Passo 4: Atualizar os segredos finalpoint com a bandeira SkipOutOfScopeDeletions

No Graph Explorer, execute o comando abaixo para atualizar os segredos finalpoint com a bandeira ***SkipOutOfScopeDeletions.*** 

No URL abaixo substitua [servicePrincipalId] pelo **Serviço PrincipalId** extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Copie o texto atualizado do passo 3 para o "Request Body" e coloque o cabeçalho "Content-Type" para "application/json" em "'Request Headers'. 

   ![Pedido de COLOCAÇÃO](./media/skip-out-of-scope-deletions/skip-05.png)

Clique em "Run Query". 

Deve obter a saída como "Sucesso – Código de Estado 204". 

   ![Resposta PUT](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Passo 5: Verifique se os utilizadores fora do âmbito não são desativados

Pode testar os resultados desta bandeira no comportamento esperado, atualizando as suas regras de deteção para saltar um utilizador específico. No exemplo abaixo, estamos excluindo o empregado com ID 21173 (que estava no âmbito mais cedo) adicionando uma nova regra de digitalização: 

   ![Exemplo de deteção](./media/skip-out-of-scope-deletions/skip-07.png)

No próximo ciclo de provisionamento, o serviço de provisionamento da AD Azure identificará que o utilizador 21173 se desentendeu e se a propriedade SkipOutOfScopeDeletions estiver ativada, então a regra de sincronização para esse utilizador apresentará uma mensagem como mostrado abaixo: 

   ![Exemplo de deteção](./media/skip-out-of-scope-deletions/skip-08.png)


