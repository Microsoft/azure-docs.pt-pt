---
title: Ver resultados e implementação de testes do sistema - Tradutor Personalizado
titleSuffix: Azure Cognitive Services
description: Quando o seu treino for bem sucedido, reveja os testes do sistema para analisar os resultados do seu treino. Se estiver satisfeito com os resultados do treino, faça um pedido de implantação para o modelo treinado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 04e21e4bcd56506ba27730ebc1f9b0db8dc39615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88509690"
---
# <a name="view-system-test-results"></a>Viver resultados do teste do sistema

Quando o seu treino for bem sucedido, reveja os testes do sistema para analisar os resultados do seu treino. Se estiver satisfeito com os resultados do treino, faça um pedido de implantação para o modelo treinado.

## <a name="system-test-results-page"></a>Página de resultados de testes do sistema

Selecione um projeto e, em seguida, selecione o separador modelo desse projeto, localize o modelo que pretende utilizar e, finalmente, selecione o separador de teste.

O separador de teste mostra-lhe:

1.  **Resultados dos testes do sistema:** O resultado do processo de teste nos treinos. O processo de teste produz a pontuação BLEU.

    **Contagem da sentença:** Quantas frases paralelas foram usadas no conjunto de testes.

     **Pontuação BLEU:** Pontuação BLEU gerada para um modelo após a conclusão do treino.

    **Estado:** Indica se o processo de teste está completo ou em curso.

    ![Resultados dos testes do sistema](media/how-to/how-to-system-test-results.png)

2.  Clique nos resultados dos testes do Sistema e isso irá levá-lo para testar a página de detalhes do resultado. Esta página mostra a tradução automática de frases que faziam parte do conjunto de dados do teste.

3.  A tabela na página de detalhes do resultado do teste tem duas colunas - uma para cada idioma do par. A coluna para a língua de origem mostra que a frase deve ser traduzida. A coluna para a língua-alvo contém duas frases em cada linha.

    **Ref:** Esta frase é a tradução de referência da frase de origem, tal como dada no conjunto de dados do teste.

    **MT:** Esta frase é a tradução automática da frase de origem feita pelo modelo construído após a formação.

    ![Os resultados dos testes do sistema comparam](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Teste de descarregamento

Clique no link Traduções de Descarregamento para descarregar um ficheiro zip. O fecho contém as traduções automáticas de frases de origem no conjunto de dados de teste.

![Teste de descarregamento](media/how-to/how-to-system-test-download.png)

Este arquivo zip descarregado contém três ficheiros.

1.  **custom.mt.txt:** Este ficheiro contém traduções automáticas de frases linguísticas de origem na linguagem-alvo feita pelo modelo treinado com os dados do utilizador.

2.  **ref.txt:** Este ficheiro contém traduções fornecidas pelo utilizador de frases linguísticas de origem no idioma-alvo.

3.  **source.txt:** Este ficheiro contém frases na língua de origem.

    ![Resultados dos testes do sistema descarregados](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Implementar um modelo

Para solicitar uma implantação:

1.  Selecione um projeto, vá ao separador Modelos.

2. Para um modelo treinado com sucesso, mostra o botão "Implementar", se não for implantado.

    ![Implementar modelo](media/how-to/how-to-deploy-model.png)

3.  Clique em Implementar.
4.  Selecione **Implementar** para a região(s) onde pretende que o seu modelo seja implantado e clique em Guardar. Pode selecionar **Implementar** para várias regiões.

    ![Implementar modelo](media/how-to/how-to-deploy-model-regions.png)

5.  Pode ver o estado do seu modelo na coluna "Status".

>[!Note]
>O Custom Tradutor suporta 10 modelos implantados num espaço de trabalho a qualquer momento.

## <a name="update-deployment-settings"></a>Atualizar definições de implementação

Para atualizar as definições de implementação:

1.  Selecione um projeto e vá para o **separador Modelos.**

2. Para um modelo implantado com sucesso, mostra um botão **De atualização.**

    ![Implementar modelo](media/how-to/how-to-update-undeploy-model.png)

3.  Selecione **Atualizar**.
4.  Selecione **Implementar** ou **Não ser utilizado** para a região(s) onde pretende que o seu modelo seja implantado ou não utilizado, depois clique em **Guardar**.

    ![Implementar modelo](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Se selecionar **o "Undeployed"** para todas as regiões, o modelo não é despreocupado de todas as regiões e colocado num estado não desocupado. Está agora indisponível para uso.

## <a name="next-steps"></a>Passos seguintes

- Comece a utilizar o seu modelo de tradução personalizado implementado através do [Microsoft Tradutor Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Aprenda [a gerir as definições](how-to-manage-settings.md) para partilhar o seu espaço de trabalho, gerir a chave de subscrição.
- Saiba [como migrar o seu espaço de trabalho e projeto](how-to-migrate.md) a partir do Microsoft Tradutor [Hub](https://hub.microsofttranslator.com)
