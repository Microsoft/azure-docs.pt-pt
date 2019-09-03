---
title: Resolver erros comuns
description: Saiba como solucionar problemas de criação, atribuição e remoção de plantas.
author: DCtheGeek
ms.author: dacoulte
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 8cbefcbadc14c1249d2783f1539e40c99c3be66c
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231563"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Solucionar erros usando plantas do Azure

Você pode encontrar erros ao criar ou atribuir plantas. Este artigo descreve vários erros que podem ocorrer e como resolvê-los.

## <a name="finding-error-details"></a>Localizando detalhes do erro

Muitos erros serão o resultado da atribuição de um plano gráfico a um escopo. Quando uma atribuição falha, o plano gráfico fornece detalhes sobre a implantação com falha. Essas informações indicam o problema para que ele possa ser corrigido e a próxima implantação seja realizada com sucesso.

1. Selecione **todos os serviços** no painel esquerdo. Pesquise e selecione **plantas**.

1. Selecione **plantas atribuídas** na página à esquerda e use a caixa de pesquisa para filtrar as atribuições de plano gráfico para localizar a atribuição com falha. Você também pode classificar a tabela de atribuições pela coluna **estado de provisionamento** para ver todas as atribuições com falha agrupadas.

1. Clique no plano gráfico com o status com _falha_ ou clique com o botão direito do mouse e selecione **Exibir detalhes da atribuição**.

1. Uma faixa vermelha avisando que a atribuição falhou está na parte superior da página de atribuição do Blueprint. Clique em qualquer lugar na faixa para obter mais detalhes.

É comum que o erro seja causado por um artefato e não pelo plano gráfico como um todo. Se um artefato criar um Key Vault e Azure Policy impedir a criação de Key Vault, toda a atribuição falhará.

## <a name="general-errors"></a>Erros gerais

### <a name="policy-violation"></a>Cenário Violação de política

#### <a name="issue"></a>Problema

A implantação do modelo falhou devido a uma violação de política.

#### <a name="cause"></a>Causa

Uma política pode entrar em conflito com a implantação por vários motivos:

- O recurso que está sendo criado é restrito pela política (geralmente as restrições de SKU ou local)
- A implantação está definindo campos configurados pela política (comum com marcas)

#### <a name="resolution"></a>Resolução

Altere o plano gráfico para que ele não entre em conflito com as políticas nos detalhes do erro. Se essa alteração não for possível, uma opção alternativa será ter o escopo da atribuição de política alterado para que o plano gráfico não esteja mais em conflito com a política.

### <a name="escape-function-parameter"></a>Cenário O parâmetro Blueprint é uma função

#### <a name="issue"></a>Problema

Os parâmetros de plano gráfico que são funções são processados antes de serem passados para artefatos.

#### <a name="cause"></a>Causa

Passar um parâmetro Blueprint que usa uma função, `[resourceGroup().tags.myTag]`como, para um artefato resulta no resultado processado da função que está sendo definida no artefato em vez da função dinâmica.

#### <a name="resolution"></a>Resolução

Para passar uma função por meio de como um parâmetro, escape toda a `[` cadeia de caracteres de tal forma que `[[resourceGroup().tags.myTag]`o parâmetro Blueprint se parece com. O caractere de escape faz com que plantas tratem o valor como uma cadeia de caracteres ao processar o plano gráfico. Em seguida, os planos gráficos colocam a função no artefato, permitindo que ele seja dinâmico conforme o esperado. Para obter mais informações, consulte [estrutura do arquivo de modelo-sintaxe](../../../azure-resource-manager/resource-group-authoring-templates.md#syntax).

## <a name="next-steps"></a>Passos seguintes

Se você não tiver visto seu problema ou não conseguir resolver o problema, visite um dos seguintes canais para obter mais suporte:

- Obtenha respostas de especialistas do Azure por meio dos [fóruns do Azure](https://azure.microsoft.com/support/forums/).
- Ligue-se a [@AzureSupport](https://twitter.com/azuresupport) – a conta oficial do Microsoft Azure para melhorar a experiência do cliente ao ligar a comunidade do Azure aos recursos certos: respostas, suporte e especialistas.
- Se precisar de mais ajuda, você poderá arquivar um incidente de suporte do Azure. Vá para o [site de suporte do Azure](https://azure.microsoft.com/support/options/) e selecione **obter suporte**.