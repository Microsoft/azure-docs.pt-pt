---
title: Questões conhecidas - Azure Digital Twins
description: Obtenha ajuda para reconhecer e mitigar problemas conhecidos com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8f56538470b8a52697e2d5c4154a6a6807a0cfde
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489018"
---
# <a name="known-issues-in-azure-digital-twins"></a>Temas conhecidos em Azure Digital Twins

Este artigo fornece informações sobre questões conhecidas associadas à Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 Erro do Cliente: Mau Pedido" na Cloud Shell

Os comandos em Cloud Shell em execução *https://shell.azure.com* podem falhar intermitentemente com o erro "400 Erro do Cliente: Mau Pedido para url: http://localhost:50342/oauth2/token ", seguido de traço de pilha completa.

Especificamente para a Azure Digital Twins, isto afeta os seguintes grupos de comando:
* `az dt route`
* `az dt model`
* `az dt twin`

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Isto pode ser resolvido repetindo o `az login` comando em Cloud Shell e completando os passos de login subsequentes. Depois disto, deve ser capaz de refazer o comando.

Em alternativa, pode abrir o painel Cloud Shell no portal Azure e completar o seu trabalho cloud Shell a partir daí:

:::image type="content" source="media/includes/portal-cloud-shell.png" alt-text="Vista do portal Azure com o ícone 'Cloud Shell' em destaque, e a Cloud Shell aparecendo na parte inferior da janela do portal":::

Finalmente, outra solução é [instalar o CLI Azure](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) na sua máquina para que possa executar os comandos Azure CLI localmente. O CLI local não vive esta questão.

### <a name="possible-causes"></a>Possíveis causas

Este é o resultado de um problema conhecido na Cloud Shell: [*Obter ficha da Cloud Shell falha intermitentemente com 400 Erro do Cliente: Mau Pedido*](https://github.com/Azure/azure-cli/issues/11749).

Isto apresenta um problema com os tokens auth auth instance da Azure Digital Twins e a autenticação baseada em [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) pela Cloud Shell. O passo de resolução de problemas de funcionamento `az login` tira-o da autenticação de identidade gerida, ultrapassando assim este problema.

Isto não afeta os comandos Azure Digital Twins dos `az dt` grupos ou `az dt endpoint` grupos de comando, porque utilizam um tipo diferente de token de autenticação (baseado em ARM), que não tem qualquer problema com a autenticação de identidade gerida da Cloud Shell.

## <a name="missing-role-assignment-after-scripted-setup"></a>Atribuição de função em falta após configuração escrita

Alguns utilizadores podem experimentar problemas com a parte de atribuição de funções de [*Como-a- Configurar um caso e autenticação (scripted)*](how-to-set-up-instance-scripted.md). O script não indica falha, mas a função *Azure Digital Twins Data Owner* não é atribuída com sucesso ao utilizador, e este problema terá impacto na capacidade de criar outros recursos ao longo da estrada.

[!INCLUDE [digital-twins-role-rename-note.md](../../includes/digital-twins-role-rename-note.md)]

Para determinar se a sua atribuição de funções foi configurada com sucesso após a execução do script, siga as instruções na secção de atribuição de [*funções*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) de utilizador do artigo de configuração. Se o seu utilizador não for mostrado com esta função, este problema afeta-o.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver, pode configurar manualmente a sua atribuição de funções utilizando o portal CLI ou Azure. 

Siga estas instruções:
* [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions)
* [portal](how-to-set-up-instance-portal.md#set-up-user-access-permissions)

### <a name="possible-causes"></a>Possíveis causas

Para os utilizadores que iniciam sessão com uma conta pessoal da [Microsoft (MSA),](https://account.microsoft.com/account)o ID principal do utilizador que o identifica em comandos como este pode ser diferente do e-mail de login do utilizador, dificultando a descoberta e utilização do script para atribuir a função corretamente.

## <a name="issue-with-interactive-browser-authentication"></a>Problema com a autenticação interativa do navegador

Ao escrever código de autenticação nas suas aplicações Azure Digital Twins utilizando a versão **1.2.0** da biblioteca ** [Azure.Identity,](/dotnet/api/azure.identity?view=azure-dotnet&preserve-view=true) **poderá experimentar problemas com o método [InteractiveBrowserCredential.](/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet&preserve-view=true)

Esta não é a última versão da biblioteca. A versão mais recente é **1.2.2**.

O método afetado é utilizado nos seguintes artigos: 
* [*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)
* [*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)
* [*Como fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md)

O problema inclui uma resposta de erro de "Azure.Identity.AuthenticationFailedException" ao tentar autenticar numa janela do navegador. A janela do navegador pode não conseguir arrancar completamente ou parecer autenticar o utilizador com sucesso, enquanto a aplicação do cliente ainda falha com o erro.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Para resolver, atualize as suas aplicações para utilizar a `Azure.Identity` versão **1.2.2**. Com esta versão da biblioteca, o navegador deve carregar e autenticar como esperado.

### <a name="possible-causes"></a>Possíveis causas

Isto está relacionado com um problema aberto com a versão mais recente da `Azure.Identity` biblioteca (versão **1.2.0):** [*Não autentica quando utilizar o InteractiveBrowserCredential*](https://github.com/Azure/azure-sdk-for-net/issues/13940).

Verá este problema se utilizar a versão **1.2.0** na sua aplicação Azure Digital Twins, ou se adicionar a biblioteca ao seu projeto sem especificar uma versão (como também por defeito nesta versão mais recente).

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)