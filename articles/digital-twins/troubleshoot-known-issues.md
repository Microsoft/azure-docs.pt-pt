---
title: Questões conhecidas - Azure Digital Twins
description: Obtenha ajuda para reconhecer e mitigar problemas conhecidos com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.custom: contperf-fy21q2
ms.openlocfilehash: 641b44a5e21e6646c07e6e1511e1c4ff01707f79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102434105"
---
# <a name="known-issues-in-azure-digital-twins"></a>Temas conhecidos em Azure Digital Twins

Este artigo fornece informações sobre questões conhecidas associadas à Azure Digital Twins.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 Erro do Cliente: Mau Pedido" na Cloud Shell

**Descrição da emissão:** Os comandos em Cloud Shell em execução *https://shell.azure.com* podem falhar intermitentemente com o erro "400 Erro do Cliente: Mau Pedido para url: http://localhost:50342/oauth2/token ", seguido de traço de pilha completa.

| Isto afeta-me? | Causa | Resolução |
| --- | --- | --- |
| Em &nbsp; Azure &nbsp; Digital &nbsp; Twins, isto afeta os seguintes grupos de comando:<br><br>`az dt route`<br><br>`az dt model`<br><br>`az dt twin` | Este é o resultado de um problema conhecido na Cloud Shell: [*Obter ficha da Cloud Shell falha intermitentemente com 400 Erro do Cliente: Mau Pedido*](https://github.com/Azure/azure-cli/issues/11749).<br><br>Isto apresenta um problema com os tokens auth auth instance da Azure Digital Twins e a autenticação baseada em [identidade gerida](../active-directory/managed-identities-azure-resources/overview.md) pela Cloud Shell. <br><br>Isto não afeta os comandos Azure Digital Twins dos `az dt` grupos ou grupos de `az dt endpoint` comando, porque utilizam um tipo diferente de token de autenticação (baseado no Azure Resource Manager), que não tem qualquer problema com a autenticação de identidade gerida da Cloud Shell. | Uma maneira de resolver isto é refazer o `az login` comando em Cloud Shell e completar os passos de login subsequentes. Isto irá mudar a sua sessão para fora da autenticação de identidade gerida, o que evita o problema da raiz. Depois disto, deve ser capaz de refazer o comando.<br><br>Em alternativa, pode abrir o painel Cloud Shell no portal Azure e completar o seu trabalho cloud Shell a partir daí.<br>:::image type="content" source="media/troubleshoot-known-issues/portal-launch-icon.png" alt-text="Imagem do ícone Cloud Shell na barra de ícone do portal Azure" lightbox="media/troubleshoot-known-issues/portal-launch-icon.png":::<br><br>Finalmente, outra solução é [instalar o CLI Azure](/cli/azure/install-azure-cli) na sua máquina para que possa executar os comandos Azure CLI localmente. O CLI local não vive esta questão. |


## <a name="missing-role-assignment-after-scripted-setup"></a>Atribuição de função em falta após configuração escrita

**Descrição da emissão:** Alguns utilizadores podem experimentar problemas com a parte de atribuição de funções de [*Como-a- Configurar um caso e autenticação (scripted)*](how-to-set-up-instance-scripted.md). O script não indica falha, mas a função *Azure Digital Twins Data Owner* não é atribuída com sucesso ao utilizador, e este problema terá impacto na capacidade de criar outros recursos ao longo da estrada.

| Isto afeta-me? | Causa | Resolução |
| --- | --- | --- |
| Para determinar se a sua atribuição de funções foi configurada com sucesso após a execução do script, siga as instruções na secção de atribuição de [*funções*](how-to-set-up-instance-scripted.md#verify-user-role-assignment) de utilizador do artigo de configuração. Se o seu utilizador não for mostrado com esta função, este problema afeta-o. | Para os utilizadores que iniciam sessão com uma conta pessoal da [Microsoft (MSA),](https://account.microsoft.com/account)o ID principal do utilizador que o identifica em comandos como este pode ser diferente do e-mail de login do utilizador, dificultando a descoberta e utilização do script para atribuir a função corretamente. | Para resolver, pode configurar manualmente a sua atribuição de funções utilizando as instruções do [CLI](how-to-set-up-instance-cli.md#set-up-user-access-permissions) ou [do portal Azure](how-to-set-up-instance-portal.md#set-up-user-access-permissions). |

## <a name="issue-with-interactive-browser-authentication-on-azureidentity-120"></a>Problema com autenticação de navegador interativo em Azure.Identidade 1.2.0

**Descrição da emissão:** Ao escrever código de autenticação nas suas aplicações Azure Digital Twins utilizando a versão **1.2.0** da biblioteca **[Azure.Identity,](/dotnet/api/azure.identity)** poderá experimentar problemas com o método [InteractiveBrowserCredential.](/dotnet/api/azure.identity.interactivebrowsercredential) Isto apresenta-se como uma resposta de erro de "Azure.Identity.AuthenticationFailedException" ao tentar autenticar numa janela do navegador. A janela do navegador pode não conseguir arrancar completamente ou parecer autenticar o utilizador com sucesso, enquanto a aplicação do cliente ainda falha com o erro.

| Isto afeta-me? | Causa | Resolução |
| --- | --- | --- |
| O &nbsp; método afetado é utilizado nos &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; seguintes artigos:<br><br>[*Tutorial: Código de uma aplicação de cliente*](tutorial-code.md)<br><br>[*Como fazer: Escrever código de autenticação de aplicativos*](how-to-authenticate-client.md)<br><br>[*Como fazer: Use as APIs e SDKs de gémeos digitais Azure*](how-to-use-apis-sdks.md) | Alguns utilizadores já tiveram este problema com a versão **1.2.0** da `Azure.Identity` biblioteca. | Para resolver, atualize as suas aplicações para utilizar uma [versão posterior](https://www.nuget.org/packages/Azure.Identity) de `Azure.Identity` . Depois de atualizar a versão da biblioteca, o navegador deve carregar e autenticar conforme esperado. |

## <a name="issue-with-default-azure-credential-authentication-on-azureidentity-130"></a>Problema com a autenticação credencial Azure padrão em Azure.Identidade 1.3.0

**Descrição da emissão:** Ao escrever o código de autenticação utilizando a versão **1.3.0** da biblioteca **[Azure.Identity](/dotnet/api/azure.identity)**, alguns utilizadores experimentaram problemas com o método [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) utilizado em muitas amostras ao longo destes docs Azure Digital Twins. Isto apresenta como uma resposta de erro de "Azure.Identity.AuthenticationFailedException: SharedTokenCacheCredential authentication failed" quando o código tenta autenticar.

| Isto afeta-me? | Causa | Resolução |
| --- | --- | --- |
| `DefaultAzureCredential` é usado na maioria dos exemplos de documentação para este serviço que incluem a autenticação. Se estiver a escrever código de autenticação utilizando `DefaultAzureCredential` a versão 1.3.0 da `Azure.Identity` biblioteca e a ver esta mensagem de erro, isso afeta-o. | Isto é provavelmente o resultado de algum problema de configuração com `Azure.Identity` . | Uma estratégia para resolver isto é excluir `SharedTokenCacheCredential` da sua credencial, como descrito nesta [questão defaultAzureCredential](https://github.com/Azure/azure-sdk/issues/1970) que está atualmente aberta contra `Azure.Identity` .<br>Outra opção é alterar a sua aplicação para utilizar uma versão anterior de `Azure.Identity` , como a versão [1.2.3](https://www.nuget.org/packages/Azure.Identity/1.2.3). Isto não tem impacto funcional para a Azure Digital Twins e, portanto, é também uma solução aceite. |

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)