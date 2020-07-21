---
title: Questões conhecidas - Azure Digital Twins
description: Obtenha ajuda para reconhecer e mitigar problemas conhecidos com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: 8fce451d9b806d2fa9a4f3d9e1c117de0aaa9fc0
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86532291"
---
# <a name="known-issues-in-azure-digital-twins"></a>Temas conhecidos em Azure Digital Twins

Este artigo fornece informações sobre questões conhecidas associadas à Azure Digital Twins.

## <a name="managing-event-routes-in-the-azure-portal"></a>Gerir rotas de eventos no portal Azure

Se você for assinado no portal com uma conta pessoal da [**Microsoft (MSA),**](https://account.microsoft.com/account/Account)como uma *@outlook.com* conta, você verá um ecrã dizendo *que você precisa de permissão para ver as rotas do evento* quando tenta gerir rotas de eventos no portal, independentemente do seu nível de permissão.

:::image type="content" source="media/troubleshoot-known-issues/event-route-need-permission.png" alt-text="Screenshot do portal Azure do erro de permissão ao tentar criar rotas de eventos numa instância Azure Digital Twins":::

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Os utilizadores que não conseguem gerir as rotas de eventos no portal ainda podem gerir rotas de eventos utilizando as APIs ou CLI das Gémeas Digitais Azure. Mudar para uma destas ferramentas para a gestão de rotas de eventos é a estratégia recomendada para mitigar este problema.

As instruções para tal podem ser encontradas em [*Como-a-: Gerir pontos finais e rotas*](how-to-manage-routes.md).

### <a name="possible-causes"></a>Possíveis causas

É assinado no portal com uma conta pessoal da [Microsoft (MSA),](https://account.microsoft.com/account/Account)como uma *@outlook.com* conta. A gestão das rotas de eventos no portal Azure está atualmente disponível apenas para utilizadores do Azure em contas de domínio corporativo.

## <a name="400-client-error-bad-request-in-cloud-shell"></a>"400 Erro do Cliente: Mau Pedido" na Cloud Shell

Os comandos em Cloud Shell podem falhar intermitentemente com o erro "400 Erro do Cliente: Mau Pedido para url: http://localhost:50342/oauth2/token " seguido de traço de pilha completa.

### <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Isto pode ser resolvido recorrindo o `az login` comando e completando os passos de login subsequentes.

Depois disto, deve ser capaz de re-executar o comando.

### <a name="possible-causes"></a>Possíveis causas

Este é o resultado de um problema conhecido na Cloud Shell: [*Obter ficha da Cloud Shell falha intermitentemente com 400 Erro do Cliente: Mau Pedido*](https://github.com/Azure/azure-cli/issues/11749).

## <a name="next-steps"></a>Próximos passos

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)