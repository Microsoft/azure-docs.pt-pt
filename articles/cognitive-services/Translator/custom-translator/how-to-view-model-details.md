---
title: Exibir detalhes do modelo-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: A guia modelos em qualquer projeto mostra detalhes de cada modelo, como nome do modelo, status do modelo, Pontuação BLEU, treinamento, ajuste, teste de contagem de sentenças.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 64f446c3b331c1aa6ddaae9081b7f61943f74ab2
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595559"
---
# <a name="view-model-details"></a>Ver detalhes do modelo

A guia modelos em projeto mostra todos os modelos nesse projeto. Todos os modelos treinados para esse projeto estão listados nesta guia.

Para cada modelo no projeto, esses detalhes são exibidos.

1.  Nome do modelo: Mostra o nome do modelo de um determinado modelo.

2.  Estado: Mostra o status de um determinado modelo. Seu novo treinamento terá um status de enviado até que seja aceito. O status será alterado para processamento de dados enquanto o serviço avaliar o conteúdo de seus documentos. Quando a avaliação de seus documentos estiver concluída, o status será alterado para em execução e você poderá ver o número de frases que fazem parte do treinamento, incluindo os conjuntos de ajuste e teste que são criados para você automaticamente. Abaixo está uma lista de status do modelo que descreve o estado dos modelos.

    -  Enviada Especifica que o back-end está processando os documentos para esse modelo.

    -  TrainingQueued: Especifica que o treinamento está sendo enfileirado para o sistema MT para esse modelo.

    -  A executar: Especifica que o treinamento está sendo executado no sistema MT para esse modelo.

    -  Foi Especifica que o treinamento foi bem-sucedido no sistema MT e um modelo está disponível. Nesse status, uma pontuação de BLEU é exibida para esse modelo.

    -  Plant Especifica que o modelo treinado com êxito é enviado para o sistema MT para implantação.

    -  Desimplantando: Especifica que o modelo implantado está sendo Desimplantado.

    -  Cancelada Especifica que o processo de desimplantação de um modelo foi concluído com êxito.

    -  Falha no treinamento: Especifica que o treinamento falhou. Se ocorrer uma falha de treinamento, tente novamente o trabalho de treinamento. Se o erro persistir, entre em contato conosco. Não exclua o modelo com falha.

    - DataProcessingFailed: Especifica que o processamento de dados falhou para um ou mais documentos pertencentes ao modelo.

    - DeploymentFailed Especifica que a implantação do modelo falhou.

    - MigratedDraft: Especifica que o modelo está em estado de rascunho após a migração do hub para o tradutor personalizado.

4.  Pontuação de BLEU: mostra a pontuação BLEU (subestudo de avaliação bilíngüe) do modelo, indicando a qualidade do seu sistema de tradução. Essa Pontuação informa a você como as traduções realizadas pelo sistema de tradução resultantes desse treinamento correspondem às frases de referência no conjunto de dados de teste. A pontuação de BLEU será exibida se o treinamento for concluído com êxito. Se o treinamento não estiver completo/com falha, você não verá nenhuma Pontuação BLEU.

5.  Contagem de sentenças de treinamento: Mostra o número total de frases usadas como conjunto de treinamento.

6.  Contagem de sentenças de ajuste: Mostra o número total de frases usadas como conjunto de ajuste.

7.  Contagem de sentenças de treinamento: Mostra o número total de frases usadas como conjunto de testes.

8.  Número de sentenças mono: Mostra o número total de frases usadas como conjunto mono.

9.  Botão de ação implantar: Para um modelo treinado com êxito, ele mostra o botão "implantar" se não estiver implantado. Se um modelo for implantado, um botão "desimplantar" será mostrado.

10. Apagar Você pode usar esse botão se desejar excluir o modelo. A exclusão de um modelo não excluirá nenhum dos documentos usados para criar esse modelo.

    ![Ver detalhes do modelo](media/how-to/how-to-view-model-details.png)

>[!Note]
>Para comparar os treinamentos consecutivos para os mesmos sistemas, é importante manter o conjunto de ajuste e o conjunto de teste constante.

## <a name="view-model-training-details"></a>Exibir detalhes de treinamento do modelo

Quando o treinamento estiver concluído, você poderá examinar os detalhes sobre o treinamento na página de detalhes. Selecione um projeto, localize e selecione a guia modelos e escolha um modelo.

A página modelo tem duas guias: Detalhes de treinamento e teste.

1.  **Detalhes de treinamento:** Essa guia mostra a lista de documentos usados no treinamento:

    -  Nome dos documentos: Este campo mostra o nome do documento

    -  Tipo de documento: Este campo mostra se este documento é paralelo/mono.

    -  Contagem de sentenças no idioma de origem: Este campo mostra o número de sentenças como parte do idioma de origem.

    -  Contagem de sentenças no idioma de destino: Este campo mostra o número de sentenças como parte do idioma de destino.

    -  Sentenças alinhadas: Este campo mostra o número de frases alinhadas pelo Tradutor personalizado durante o processo de alinhamento.

    -  Sentenças usadas: Este campo mostra o número de frases usadas pelo Tradutor personalizado durante este treinamento.

    ![Detalhes de treinamento do modelo](media/how-to/how-to-model-training-details.png)

2.  **Testar** Esta guia mostra os detalhes do teste para um treinamento bem-sucedido.

## <a name="next-steps"></a>Passos seguintes

- Examine [os resultados do teste](how-to-view-system-test-results.md) e analise os resultados do treinamento.
