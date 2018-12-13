---
title: Resolver erros comuns
description: Saiba como resolver problemas de criação e a atribuição de esquemas
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/25/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 40668fed2fcc2a04e39fa3a4d7e8e8923c75ae05
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53315533"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Resolver problemas de erros usando os esquemas do Azure

Poderão ocorrer erros quando criar ou a atribuição de esquemas. Este artigo descreve vários erros que possam ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Localizando detalhes do erro

Muitos erros será o resultado da atribuição de um plano gráfico para um âmbito. Quando uma tarefa falha, o plano gráfico fornece detalhes sobre a falha na implementação. Estas informações indicam o problema para que pode ser corrigido e a seguinte implementação com êxito.

1. Clique em **Todos os serviços** e procure e selecione **Política**, no painel do lado esquerdo. Na página **Política**, clique em **Esquemas**.

1. Selecione **esquemas atribuídos** partir da página à esquerda e utilize a caixa de pesquisa para filtrar a atribuição de esquema para encontrar a atribuição falhou. Também pode ordenar a tabela de atribuições pela **estado de aprovisionamento** coluna para ver as atribuições de falhas todas agrupados em conjunto.

1. Esquerdo no esquema com o _falhada_ Estado ou o botão direito do mouse e selecione **ver detalhes de atribuição**.

1. É uma faixa vermelha aviso de que a atribuição falhou na parte superior da página de atribuição de esquema. Clique em qualquer lugar na faixa para obter mais detalhes.

É comum para o erro causado por um artefato e não o plano gráfico como um todo. Se um artefato cria um cofre de chave e o Azure Policy impede a criação do Key Vault, a atribuição de inteira falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="policy-violation"></a>Cenário: Violação de política

#### <a name="issue"></a>Problema

A implementação do modelo falhou devido a violação de política.

#### <a name="cause"></a>Causa

Uma política, poderá entrar em conflito com a implantação por uma série de motivos:

- O recurso que está sendo criado é restrita pela diretiva (normalmente restrições SKU ou localização)
- A implementação é definir campos que estão configurados pela política (comuns com etiquetas)

#### <a name="resolution"></a>Resolução

Altere o plano gráfico para que não entra em conflito com as políticas nos detalhes do erro. Se esta alteração não for possível, uma opção alternativa é fazer com o âmbito da atribuição de política foi alterado para que o plano gráfico já não está em conflito com a política.

## <a name="next-steps"></a>Passos Seguintes

Se não vir o seu problema ou não é possível resolver o problema, visite um dos seguintes canais de suporte mais:

- Obtenha respostas de especialistas do Azure através dos [fóruns do Azure](https://azure.microsoft.com/support/forums/)
- Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, pode enviar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.