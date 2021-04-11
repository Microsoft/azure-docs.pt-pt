---
title: Como prevenir erros de configuração com o Centro de Segurança Azure
description: Saiba como utilizar as opções de "Enforce" e 'Deny' do Security Center nas páginas de detalhes das recomendações
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/17/2021
ms.author: memildin
ms.openlocfilehash: fabbd255f64e5614ae052c9d7c8d65fc35d45856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104600501"
---
# <a name="prevent-misconfigurations-with-enforcedeny-recommendations"></a>Evitar configurações incorretas com recomendações Impor/Negar

As configurações erradas de segurança são a principal causa de incidentes de segurança. O Centro de Segurança tem agora a capacidade de ajudar a *prevenir* a má configuração de novos recursos no que diz respeito a recomendações específicas. 

Esta função pode ajudar a manter as suas cargas de trabalho seguras e estabilizar a sua pontuação segura.

A aplicação de uma configuração segura, baseada numa recomendação específica, é oferecida em dois modos:

- Usando o efeito **Deny** da Política Azure, você pode impedir que recursos insalubres sejam criados
- Utilizando a opção **Enforce,** pode tirar partido do efeito **DeployIfNotExist** da política da Azure e remediar automaticamente recursos não conformes após a criação

Isto pode ser encontrado no topo da página de detalhes de recursos para recomendações de segurança selecionadas (ver [Recomendações com opções de negação/execução).](#recommendations-with-denyenforce-options)

## <a name="prevent-resource-creation"></a>Prevenir a criação de recursos

1. Abra a recomendação de que os seus novos recursos devem satisfazer e selecione o botão **Deny** no topo da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-deny-button.png" alt-text="Página de recomendação com botão Deny realçado":::

    O painel de configuração abre a listagem das opções de âmbito. 

1. Desacione o âmbito selecionando o grupo de subscrição ou gestão relevante.

    > [!TIP]
    > Pode utilizar os três pontos no final da linha para alterar uma única subscrição, ou utilizar as caixas de verificação para selecionar várias subscrições ou grupos e, em seguida, selecionar **Alterar para Negar**.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-prevent-resource-creation.png" alt-text="Definir a margem de manobra para a Política Azure negar":::


## <a name="enforce-a-secure-configuration"></a>Impor uma configuração segura

1. Abra a recomendação de que irá implementar uma implementação de modelo para se novos recursos não satisfazê-lo, e selecione o botão **Enforce** no topo da página.

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-button.png" alt-text="Página de recomendação com botão De aplicação realçado":::

    O painel de configuração abre-se com todas as opções de configuração de política. 

    :::image type="content" source="./media/security-center-remediate-recommendations/recommendation-enforce-config.png" alt-text="Impor opções de configuração":::

1. Desa esta vista, nome de atribuição e outras opções relevantes.

1. Selecione **Rever + criar**.

## <a name="recommendations-with-denyenforce-options"></a>Recomendações com opções de negação/aplicação

Estas recomendações podem ser utilizadas com a opção **de negação:**

[!INCLUDE [azure-security-center-recommendations-deny](../../includes/asc/recommendations-with-deny.md)]

Estas recomendações podem ser utilizadas com a opção **de aplicação:**

- A auditoria no servidor SQL deve ser ativada
- A azure Backup deve ser ativado para máquinas virtuais
- O Azure Defender para SQL deve ser ativado nos seus servidores SQL
- O Add-on de Política Azure para Kubernetes deve ser instalado e ativado nos seus clusters
- Os registos de diagnóstico no Azure Stream Analytics devem ser ativados
- Os registos de diagnóstico nas contas do Lote devem ser ativados
- Os registos de diagnóstico em Data Lake Analytics devem ser ativados
- Os registos de diagnóstico no Centro de Eventos devem ser ativados
- Os registos de diagnóstico no Cofre de Chaves devem ser ativados
- Os registos de diagnóstico em Aplicações Lógicas devem ser ativados
- Os registos de diagnóstico nos serviços de pesquisa devem ser ativados
- Os registos de diagnóstico no Service Bus devem ser ativados
