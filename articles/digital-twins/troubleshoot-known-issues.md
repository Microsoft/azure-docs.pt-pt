---
title: Questões conhecidas - Azure Digital Twins
description: Obtenha ajuda para reconhecer e mitigar problemas conhecidos com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 0c008061d2d4fafa96eda934d5026c92839a0bdb
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661491"
---
# <a name="known-issues-in-azure-digital-twins"></a>Temas conhecidos em Azure Digital Twins

Este artigo fornece informações sobre questões conhecidas associadas à Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 Erro do Cliente: Mau Pedido" na Cloud Shell

Os comandos em Cloud Shell podem falhar intermitentemente com o erro "400 Erro do Cliente: Mau Pedido para url: http://localhost:50342/oauth2/token " seguido de traço de pilha completa.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Isto pode ser resolvido recorrindo o `az login` comando e completando os passos de login subsequentes.

Depois disto, deve ser capaz de re-executar o comando.

### <a name="possible-causes"></a>Possíveis causas

Este é o resultado de um problema conhecido na Cloud Shell: [*Obter ficha da Cloud Shell falha intermitentemente com 400 Erro do Cliente: Mau Pedido*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="missing-role-assignment-after-scripted-setup"></a>Atribuição de função em falta após configuração escrita

Alguns utilizadores podem experimentar problemas com a parte de atribuição de funções de [*Como-a- Configurar um caso e autenticação (scripted)*](how-to-set-up-instance-scripted.md). O script não indica falha, mas a *função Azure Digital Twins Owner (Preview)* não está atribuída com sucesso ao utilizador, o que terá impacto na capacidade de criar outros recursos ao longo da estrada.

Para determinar se a sua atribuição de funções foi configurada com sucesso após a execução do script, siga as instruções na secção de atribuição de [*funções*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) de utilizador do artigo de configuração. Se o seu utilizador não for mostrado com esta função, este problema afeta-o.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver, pode configurar manualmente a sua atribuição de funções utilizando o portal CLI ou Azure. 

Siga estas instruções:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Possíveis causas

Para os utilizadores que iniciam sessão com uma conta pessoal da [Microsoft (MSA),](https://account.microsoft.com/account)o ID principal do utilizador que o identifica em comandos como este pode ser diferente do e-mail de login do seu utilizador, dificultando a descoberta e utilização do script para atribuir a função corretamente.

## <a name="issue-with-interactive-browser-authentication"></a>Problema com a autenticação interativa do navegador

Ao escrever código de autenticação nas suas aplicações Azure Digital Twins utilizando a versão mais recente (versão **1.2.0**) da biblioteca ** [Azure.Identity,](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) **poderá experimentar problemas com o método [InteractiveBrowserCredential.](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet)

O método afetado é utilizado nos seguintes artigos: 
* [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)
* [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md)

O problema inclui uma resposta de erro de "Azure.Identity.AuthenticationFailedException" ao tentar autenticar numa janela do navegador. A janela do navegador pode não conseguir arrancar completamente ou parecer autenticar o utilizador com sucesso, enquanto a aplicação do cliente ainda falha com o erro.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver, as suas aplicações utilizem explicitamente a versão Azure.Identity **1.1.1**. Com esta versão da biblioteca, o navegador deve carregar e autenticar como esperado.

>[!NOTE]
> Não basta adicionar a biblioteca sem especificar qualquer versão, uma vez que ainda irá por defeito ao mais recente **1.2.0**. Terá de especificar explicitamente a versão **1.1.1.**

### <a name="possible-causes"></a>Possíveis causas

Isto está relacionado com um problema aberto com a versão mais recente da biblioteca Azure.Identity (versão **1.2.0):** [*Não autentica quando utilizar o InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Verá este problema se utilizar a versão **1.2.0** na sua aplicação Azure Digital Twins, ou se adicionar a biblioteca ao seu projeto sem especificar uma versão (como também por defeito nesta versão mais recente).

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)