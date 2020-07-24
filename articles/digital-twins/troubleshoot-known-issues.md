---
title: Questões conhecidas - Azure Digital Twins
description: Obtenha ajuda para reconhecer e mitigar problemas conhecidos com a Azure Digital Twins.
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.service: digital-twins
ms.date: 07/14/2020
ms.openlocfilehash: bdde2076039a6f7687e06edef6dfd6f6f5148ce4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87044137"
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

## <a name="next-steps"></a>Passos seguintes

Leia mais sobre segurança e permissões em Azure Digital Twins:
* [*Conceitos: Segurança para soluções Azure Digital Twins*](concepts-security.md)