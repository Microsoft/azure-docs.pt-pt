---
title: Excluir os utilizadores fora do âmbito
description: Saiba como anular o comportamento padrão de desavisionamento fora do âmbito dos utilizadores.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: f459a804b4c375eea17cbc22ded2f41f808c1b82
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93041172"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>Ignorar a eliminação das contas de utilizador que ficam fora de alcance

Por predefinição, o motor de provisionamento Azure AD elimina ou desativa os utilizadores que ficam fora de alcance. No entanto, para certos cenários como o Workday to AD User Inbound Provisioning, este comportamento pode não ser o esperado e poderá querer anular este comportamento padrão.  

Este artigo descreve como utilizar a API do Microsoft Graph e o explorador API do Microsoft Graph para definir a bandeira * **SkipOutOfScopeDeletions** _ que controla o processamento de contas que ficam fora de alcance. _ Se * **SkipOutOfScopeDeletions** _ estiver definido para 0 (falso), as contas que ficam fora de alcance serão desativadas no alvo.
_ Se * **SkipOutOfScopeDeletions** _ estiver definido para 1 (verdadeiro), as contas que ficam fora de alcance não serão desativadas no alvo. Esta bandeira é definida ao nível _Provisioning App* e pode ser configurada usando a API do gráfico. 

Uma vez que esta configuração é amplamente utilizada com a *aplicação de provisionamento do utilizador do Workday to Ative Directory,* os seguintes passos incluem imagens da aplicação Workday. No entanto, a configuração também pode ser utilizada com *todas as outras aplicações* – como o ServiceNow, Salesforce e Dropbox.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Passo 1: Recuperar o ID principal do serviço de aplicações de provisionamento (ID do objeto)

1. Lance o [portal Azure](https://portal.azure.com)e navegue para a secção Propriedades da sua aplicação de provisionamento. Por exemplo, se pretende exportar o seu *Workday para a aplicação de provisionamento de utilizadores AD,* navegue para a secção Propriedades dessa aplicação. 
1. Na secção Propriedades da sua aplicação de provisionamento, copie o valor GUID associado ao campo *de ID* do objeto. Este valor também é chamado de **ServicePrincipalId** da sua App e será utilizado em operações do Graph Explorer.

   ![ID principal do serviço de aplicações workday](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>Passo 2: Inscreva-se no Microsoft Graph Explorer

1. Lançar [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Clique no botão "Iniciar com a Microsoft" e inscreva-se utilizando credenciais de administração global do Azure AD Ou app.

    ![Sindução de gráfico](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. Após o sinse-in com sucesso, verá os detalhes da conta do utilizador no painel esquerdo.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>Passo 3: Obtenha credenciais de aplicações existentes e detalhes de conectividade

No Microsoft Graph Explorer, executar a seguinte consulta GET substituindo [servicePrincipalId] pelo **ServicePrincipalId** extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Consulta de emprego GET](./media/skip-out-of-scope-deletions/skip-03.png)

Copie a Resposta num ficheiro de texto. Será semelhante ao texto JSON mostrado abaixo, com valores destacados em amarelo específico para a sua implantação. Adicione as linhas realçadas em verde até ao fim e atualize a palavra-passe de ligação Workday realçada em azul. 

   ![Resposta ao emprego GET](./media/skip-out-of-scope-deletions/skip-04.png)

Aqui está o bloco JSON para adicionar ao mapeamento. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>Passo 4: Atualizar o ponto final dos segredos com a bandeira skipOutOfScopeDeletions

No Graph Explorer, executar o comando abaixo para atualizar o ponto final dos segredos com a bandeira * *_SkipOutOfScopeDeletions_* _. 

No URL abaixo substitua [servicePrincipalId] pelo _ *ServicePrincipalId* * extraído do [Passo 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id). 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Copie o texto atualizado do passo 3 para o "Órgão de Pedido" e desemote o cabeçalho "Content-Type" para "application/json" em "Pedir cabeçalhos". 

   ![Pedido de COLOCAÇÃO](./media/skip-out-of-scope-deletions/skip-05.png)

Clique em "Run Consulta". 

Deve obter a saída como "Sucesso – Código de Estado 204". 

   ![Resposta PUT](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>Passo 5: Verificar se os utilizadores fora do âmbito não são desativados

Pode testar esta bandeira resulta em comportamento esperado atualizando as suas regras de deteção para saltar um utilizador específico. No exemplo abaixo, estamos excluindo o empregado com iD 21173 (que estava no âmbito anterior) adicionando uma nova regra de digitalização: 

   ![Screenshot que mostra a secção "Adicionar filtro de escotagem" com um utilizador de exemplo realçado.](./media/skip-out-of-scope-deletions/skip-07.png)

No próximo ciclo de provisionamento, o serviço de prestação de Ad Azure identificará que o utilizador 21173 ficou fora de alcance e se a propriedade SkipOutOfScopeDeletions estiver ativada, então a regra de sincronização para esse utilizador apresentará uma mensagem como mostrado abaixo: 

   ![Exemplo de escoar](./media/skip-out-of-scope-deletions/skip-08.png)


