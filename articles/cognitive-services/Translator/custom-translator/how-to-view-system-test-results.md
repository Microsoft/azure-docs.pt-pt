---
title: Ver resultados e implementação de testes do sistema - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Quando o seu treino for bem sucedido, reveja os testes do sistema para analisar os resultados do seu treino. Se estiver satisfeito com os resultados do treino, faça um pedido de implantação para o modelo treinado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "68595612"
---
# <a name="view-system-test-results"></a>Viver resultados do teste do sistema

Quando o seu treino for bem sucedido, reveja os testes do sistema para analisar os resultados do seu treino. Se estiver satisfeito com os resultados do treino, faça um pedido de implantação para o modelo treinado.

## <a name="system-test-results-page"></a>Página de resultados de testes do sistema

Selecione um projeto, depois selecione o separador de modelos desse projeto, localize o modelo que pretende utilizar e, finalmente, selecione o separador de teste.

O separador de teste mostra-lhe:

1.  **Resultados dos testes do sistema:** O resultado do processo de teste nos treinos. O processo de teste produz a pontuação BLEU.

    **Contagem de frases:** Quantas frases paralelas foram usadas no conjunto de testes.

     **Bleu Score:** Pontuação BLEU gerada para um modelo após a conclusão do treino.

    **Estado:** Indica se o processo de teste está completo ou em curso.

    ![Resultados dos testes do sistema](media/how-to/how-to-system-test-results.png)

2.  Clique nos resultados dos testes do Sistema e isso irá levá-lo a testar a página de detalhes dos resultados. Esta página mostra a tradução automática de frases que faziam parte do conjunto de dados de teste.

3.  A tabela na página de detalhes do resultado do teste tem duas colunas - uma para cada idioma do par. A coluna para a língua de origem mostra que a frase deve ser traduzida. A coluna para a linguagem alvo contém duas frases em cada linha.

    **Ref:** Esta frase é a tradução de referência da frase de origem, tal como é dada no conjunto de dados do teste.

    **MT:** Esta frase é a tradução automática da frase de origem feita pelo modelo construído após a realização da formação.

    ![Resultados dos testes do sistema comparam](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Teste de descarregamento

Clique no link Download Translations para descarregar um ficheiro zip. O fecho contém as traduções automáticas das frases de origem no conjunto de dados de teste.

![Teste de descarregamento](media/how-to/how-to-system-test-download.png)

Este arquivo zip descarregado contém três ficheiros.

1.  **custom.mt.txt:** Este ficheiro contém traduções automáticas de frases linguísticas de origem na linguagem-alvo feita pelo modelo treinado com os dados do utilizador.

2.  **ref.txt:** Este ficheiro contém traduções fornecidas pelo utilizador de frases linguísticas de origem na língua-alvo.

3.  **source.txt:** Este ficheiro contém frases na linguagem fonte.

    ![Resultados dos testes do sistema descarregados](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Implementar um modelo

Para solicitar uma implantação:

1.  Selecione um projeto, vá ao separador Models.

2. Para um modelo treinado com sucesso, mostra o botão "Deploy", se não for implantado.

    ![Modelo de implantação](media/how-to/how-to-deploy-model.png)

3.  Clique no Deploy.
4.  Selecione **Deployed** para a ou região onde pretende que o seu modelo seja implementado e clique em Guardar. Pode selecionar **Implantado** para várias regiões.

    ![Modelo de implantação](media/how-to/how-to-deploy-model-regions.png)

5.  Pode ver o estado do seu modelo na coluna "Status".

>[!Note]
>O Custom Tradutor suporta 10 modelos implantados dentro de um espaço de trabalho a qualquer momento.

## <a name="update-deployment-settings"></a>Atualizar definições de implementação

Para atualizar as definições de implementação:

1.  Selecione um projeto e vá ao separador **Models.**

2. Para um modelo implementado com sucesso, mostra um botão **Update.**

    ![Modelo de implantação](media/how-to/how-to-update-undeploy-model.png)

3.  Selecione **Atualizar**.
4.  Selecione **Implantado** ou **Não implantado** para a ou região onde pretende que o seu modelo seja implantado ou não implantado, em seguida, clique em **Guardar**.

    ![Modelo de implantação](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Se selecionar **Não implantado** para todas as regiões, o modelo não é implantado de todas as regiões e colocado num estado não implantado. Está agora indisponível para uso.

## <a name="next-steps"></a>Passos seguintes

- Comece a utilizar o seu modelo de tradução personalizada implementado através do [Microsoft Tradutor Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Saiba [como gerir as definições](how-to-manage-settings.md) para partilhar o seu espaço de trabalho, gerir a chave de subscrição.
- Saiba [como migrar o seu espaço](how-to-migrate.md) de trabalho e projeto a partir do Microsoft Tradutor [Hub](https://hub.microsofttranslator.com)
