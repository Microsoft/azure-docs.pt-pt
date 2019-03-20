---
title: Ver resultados de teste de sistema e de implementação - Translator personalizado
titleSuffix: Azure Cognitive Services
description: Quando seu treinamento é efetuada com êxito, reveja os testes de sistema para analisar os resultados de treinamento. Se estiver satisfeito com os resultados de treinamento, coloca um pedido de implementação para o modelo preparado.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: v-rada
ms.topic: conceptual
ms.openlocfilehash: 67ddc7a8e9d79d0eef9c017b315385df62c18e33
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57997710"
---
# <a name="view-system-test-results"></a>Viver resultados do teste do sistema

Quando seu treinamento é efetuada com êxito, reveja os testes de sistema para analisar os resultados de treinamento. Se estiver satisfeito com os resultados de treinamento, coloca um pedido de implementação para o modelo preparado.

## <a name="system-test-results-page"></a>Página de resultados do teste de sistema

Selecione um projeto, em seguida, selecione o separador de modelos desse projeto, localize o modelo que pretende utilizar e, finalmente, selecione o separador de teste.

O separador de teste mostra-lhe:

1.  **Resultados do teste de sistema:** O resultado do processo de teste nos treinamentos. O processo de teste produz a pontuação de BLEU.

    **Contagem de sentença:** O número de frases paralelas foram utilizadas no conjunto de teste.

     **Pontuação de BLEU:** Pontuação de BLEU gerada para um modelo após a conclusão do treinamento.

    **Status:** Indica se o processo de teste for concluído ou em curso.

    ![Resultados do teste de sistema](media/how-to/how-to-system-test-results.png)

2.  Clique nos resultados de teste de sistema e que será apresentada a página de detalhes do resultado de teste. Esta página mostra a conversão de máquina de frases que faziam parte do conjunto de dados de teste.

3.  A tabela na página de detalhes do resultado de teste tem duas colunas – uma para cada idioma no par. A coluna para a linguagem de código mostra a frase ser traduzido. A coluna para o idioma de destino contém duas frases em cada linha.

    **Ref:** Esta frase é a conversão de referência da frase de origem fornecido no conjunto de dados de teste.

    **MT:** Esta frase é a tradução automática da sentença origem feita com o modelo criado após a formação foi realizada.

    ![Comparação de resultados do teste de sistema](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Transferir teste

Clique na ligação de traduções de transferir para transferir um ficheiro zip. O zip contém as traduções de máquina de frases de origem no conjunto de dados de teste.

![Transferir teste](media/how-to/how-to-system-test-download.png)

Este arquivo zip transferido contém três arquivos.

1.  **custom.mt.txt:** Este ficheiro contém traduções de máquina de frases de idioma de origem no idioma de destino efetuada pelo modelo preparado com dados do utilizador.

2.  **ref.txt:** Este ficheiro contém utilizador fornecido traduções de frases de idioma de origem no idioma de destino.

3.  **Source.txt:** Este ficheiro contém as frases no idioma de origem.

    ![Resultados do teste de sistema transferido](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Implementar um modelo

Para pedir uma implementação:

1.  Selecione um projeto, aceda ao separador de modelos.

2. Um modelo preparado com êxito, ele mostra o botão "Deploy", se não for implementada.

    ![Implementar modelo](media/how-to/how-to-deploy-model.png)

3.  Clique em implementar.
4.  Selecione **implementado** para as regiões onde pretende que o seu modelo deve ser implementado e clique em Guardar. Pode selecionar **implementado** para várias regiões.

    ![Implementar modelo](media/how-to/how-to-deploy-model-regions.png)

5.  Pode ver o estado do seu modelo na coluna "Estado".

## <a name="update-deployment-settings"></a>Definições de implementação de atualização

Para atualizar as definições de implementação:

1.  Selecione um projeto e vá para o **modelos** separador.

2. Para um modelo implementado com êxito, ela mostra uma **atualização** botão.

    ![Implementar modelo](media/how-to/how-to-update-undeploy-model.png)

3.  Selecione **atualização**.
4.  Selecione **implementado** ou **Undeployed** para as regiões onde pretende que o seu modelo implementado ou não implementadas, em seguida, clique em **guardar**.

    ![Implementar modelo](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Se selecionou **Undeployed** para todas as regiões, o modelo é não implementado a partir de todas as regiões e colocar num Estado de funcionamento não implementado. Está agora disponível para utilização.

## <a name="next-steps"></a>Passos Seguintes

- Começar a utilizar o seu modelo de tradução personalizadas implementado via [API de texto do Microsoft Translator V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Saiba mais [como gerenciar as configurações](how-to-manage-settings.md) para partilhar a sua área de trabalho, gerir a chave de subscrição.
- Saiba mais [como migrar a sua área de trabalho e o projeto](how-to-migrate.md) partir [Hub do Microsoft Translator](https://hub.microsofttranslator.com)
