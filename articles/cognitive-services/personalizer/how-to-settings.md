---
title: Configurar o Personalizador
titleSuffix: Azure Cognitive Services
description: A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço explora, com que frequência o modelo é retreinado e a quantidade de dados armazenados.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: d20f81bf7db2e098f2bca674c5540bc067577f30
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833915"
---
# <a name="configure-personalizer"></a>Configurar o Personalizador

A configuração do serviço inclui como o serviço trata as recompensas, com que frequência o serviço explora, com que frequência o modelo é retreinado e a quantidade de dados armazenados.

## <a name="create-personalizer-resource"></a>Criar recurso personalizado

Crie um recurso personalizado para cada loop de comentários.

1. Inicie sessão no [portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer). O link anterior leva você para a página **criar** para o serviço personalizador.
1. Insira o nome do serviço, selecione uma assinatura, um local, um tipo de preço e um grupo de recursos.
1. Selecione a confirmação e selecione **criar**.

<a name="configure-service-settings-in-the-azure-portal"></a>

## <a name="configure-service-in-the-azure-portal"></a>Configurar o serviço no portal do Azure

1. Inicie sessão no [Portal do Azure](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer).
1. Encontre seu recurso personalizador.
1. Na seção **Gerenciamento de recursos** , selecione **configuração**.

    Antes de deixar o portal do Azure, copie uma das suas chaves de recurso da página **chaves** . Você precisará disso para usar o [SDK do personalizador](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.personalizer).

<a name="configure-reward-settings-for-the-feedback-loop-based-on-use-case"></a>

### <a name="configure-reward-for-the-feedback-loop-based-on-use-case"></a>Configurar recompensa para o loop de comentários com base no caso de uso

Configure o serviço para o uso de recompensas do seu loop de comentários. As alterações nos seguintes valores redefinirão o modelo personalizado atual e o treinarão novamente com os últimos 2 dias de dados:

![Configurar os valores de recompensa para o loop de comentários](media/settings/configure-model-reward-settings.png)

|Valor|Finalidade|
|--|--|
|Tempo de espera de recompensa|Define o período durante o qual o personalizador coletará valores de recompensa para uma chamada de classificação, a partir do momento em que a chamada de classificação ocorrer. Esse valor é definido perguntando: "quanto tempo o personalizador deve esperar por chamadas de recompensas?" Qualquer recompensa chegando após essa janela será registrada, mas não usada para aprendizado.|
|Recompensa padrão|Se nenhuma chamada de recompensa for recebida pelo personalizador durante a janela de tempo de espera da recompensa associada a uma chamada de classificação, o personalizador atribuirá a recompensa padrão. Por padrão, e na maioria dos cenários, a recompensa padrão é zero.|
|Recompensar agregação|Se várias recompensas forem recebidas para a mesma chamada à API de classificação, esse método de agregação será usado: **sum** ou mais **antigo**. O primeiro escolhe a pontuação mais antiga recebida e descarta o restante. Isso será útil se você quiser um recompensa exclusivo entre chamadas possivelmente duplicadas. |

Depois de alterar esses valores, certifique-se de selecionar **salvar**.

### <a name="configure-exploration"></a>Configurar a exploração

A personalização é capaz de descobrir novos padrões e adaptar-se às alterações de comportamento do usuário ao longo do tempo explorando alternativas. O valor de **exploração** determina qual porcentagem de chamadas de classificação são respondidas com a exploração.

As alterações nesse valor redefinirão o modelo personalizado atual e o treinarão novamente com os últimos 2 dias de dados.

![O valor de exploração determina qual porcentagem de chamadas de classificação são respondidas com a exploração](media/settings/configure-exploration-setting.png)

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

### <a name="model-update-frequency"></a>Frequência de atualização do modelo

O modelo mais recente, treinado a partir de recompensar chamadas de API de cada evento ativo, não é usado automaticamente pela chamada de classificação do personalizador. A **frequência de atualização do modelo** define com que frequência o modelo usado pela chamada de classificação é atualizado.

As frequências de atualização de modelo alto são úteis para situações em que você deseja controlar com mais precisão as alterações nos comportamentos do usuário. Os exemplos incluem sites que são executados em notícias ao vivo, conteúdo viral ou oferta de produtos ao vivo. Você pode usar uma frequência de 15 minutos nesses cenários. Para a maioria dos casos de uso, uma frequência de atualização mais baixa é eficaz. As frequências de atualização de um minuto são úteis ao depurar o código de um aplicativo usando o personalizador, fazer demonstrações ou testar interativamente aspectos de aprendizado de máquina.

![Frequência de atualização de modelo define com que frequência um novo modelo personalizado é retreinado.](media/settings/configure-model-update-frequency-settings-15-minutes.png)

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

### <a name="data-retention"></a>Retenção de dados

**Período de retenção de dados** define quantos dias o personalizador mantém logs de dados. Os logs de dados anteriores são necessários para executar [avaliações offline](concepts-offline-evaluation.md), que são usadas para medir a eficácia do personalizador e otimizar a política de aprendizado.

Depois de alterar esse valor, certifique-se de selecionar **salvar**.

## <a name="export-the-personalizer-model"></a>Exportar o modelo personalizador

Na seção do gerenciamento de recursos para **configurações de modelo e aprendizado**, reveja criação de modelo e data da última atualização e exporte o modelo atual. Você pode usar o portal do Azure ou as APIs personalizadas para exportar um arquivo de modelo para fins de arquivamento.

![Exportar modelo personalizado atual](media/settings/export-current-personalizer-model.png)

## <a name="clear-data-for-your-learning-loop"></a>Limpar dados para seu loop de aprendizado

1. Na portal do Azure, para o recurso personalizador, na página **configurações de modelo e aprendizado** , selecione **limpar dados**.
1. Para limpar todos os dados e redefinir o loop de aprendizagem para o estado original, marque todas as 3 caixas de seleção.

    ![Em portal do Azure, desmarque dados do recurso Personalizar.](./media/settings/clear-data-from-personalizer-resource.png)

    |Valor|Finalidade|
    |--|--|
    |Personalização registrada e dados de recompensa.|Esses dados de log são usados em avaliações offline. Limpe os dados se você estiver redefinindo seu recurso.|
    |Redefina o modelo personalizador.|Esse modelo é alterado em cada novo treinamento. Essa frequência de treinamento é especificada na **frequência do modelo de carregamento** na página de **configuração** . |
    |Defina a política de aprendizado como padrão.|Se você alterou a política de aprendizado como parte de uma avaliação offline, isso redefinirá a política de aprendizado original.|

1. Selecione **limpar dados selecionados** para iniciar o processo de limpeza. O status é relatado nas notificações do Azure, na navegação superior direita.

## <a name="next-steps"></a>Passos seguintes

[Saiba como gerenciar uma política de aprendizado](how-to-learning-policy.md)
