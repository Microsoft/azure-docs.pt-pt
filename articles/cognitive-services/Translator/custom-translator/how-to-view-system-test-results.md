---
title: Exibir resultados de teste do sistema e implantação-Tradutor personalizado
titleSuffix: Azure Cognitive Services
description: Quando seu treinamento for bem-sucedido, examine os testes do sistema para analisar os resultados de treinamento. Se você estiver satisfeito com os resultados de treinamento, coloque uma solicitação de implantação para o modelo treinado.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: f5e1a21f24fdd2458d4803d6ed1675455fa5037d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595612"
---
# <a name="view-system-test-results"></a>Viver resultados do teste do sistema

Quando seu treinamento for bem-sucedido, examine os testes do sistema para analisar os resultados de treinamento. Se você estiver satisfeito com os resultados de treinamento, coloque uma solicitação de implantação para o modelo treinado.

## <a name="system-test-results-page"></a>Página de resultados de teste do sistema

Selecione um projeto e, em seguida, selecione a guia modelos desse projeto, localize o modelo que você deseja usar e, por fim, selecione a guia teste.

A guia teste mostra:

1.  **Resultados de Teste do sistema:** O resultado do processo de teste nos treinamentos. O processo de teste produz a pontuação BLEU.

    **Número de sentenças:** Quantas sentenças paralelas foram usadas no conjunto de teste.

     **Pontuação de BLEU:** Pontuação BLEU gerada para um modelo após a conclusão do treinamento.

    **Estado** Indica se o processo de teste está concluído ou em andamento.

    ![Resultados do teste do sistema](media/how-to/how-to-system-test-results.png)

2.  Clique nos resultados do teste do sistema e isso o levará para a página de detalhes do resultado do teste. Esta página mostra a tradução automática de frases que faziam parte do conjunto de testes.

3.  A tabela na página de detalhes do resultado de teste tem duas colunas: uma para cada idioma no par. A coluna do idioma de origem mostra a frase a ser traduzida. A coluna para o idioma de destino contém duas frases em cada linha.

    **Referência** Essa sentença é a conversão de referência da sentença de origem, conforme fornecido no conjunto de dados de teste.

    **MT:** Essa frase é a tradução automática da sentença de origem feita pelo modelo criado após o treinamento ter sido realizado.

    ![Comparação dos resultados do teste do sistema](media/how-to/how-to-system-test-results-2.png)

## <a name="download-test"></a>Baixar teste

Clique no link baixar traduções para baixar um arquivo zip. O zip contém as traduções da máquina das sentenças de origem no conjunto de dados de teste.

![Baixar teste](media/how-to/how-to-system-test-download.png)

Esse arquivo zip baixado contém três arquivos.

1.  **custom.mt.txt:** Esse arquivo contém traduções de computador de sentenças de idioma de origem no idioma de destino feito pelo modelo treinado com os dados do usuário.

2.  **ref.txt:** Esse arquivo contém traduções fornecidas pelo usuário de frases de idioma de origem no idioma de destino.

3.  **Source. txt:** Esse arquivo contém sentenças no idioma de origem.

    ![Resultados do teste do sistema baixados](media/how-to/how-to-download-system-test.png)

## <a name="deploy-a-model"></a>Implementar um modelo

Para solicitar uma implantação:

1.  Selecione um projeto, vá para a guia modelos.

2. Para um modelo treinado com êxito, ele mostra o botão "implantar", se não for implantado.

    ![Implementar modelo](media/how-to/how-to-deploy-model.png)

3.  Clique em implantar.
4.  Selecione  implantado para as regiões em que você deseja que seu modelo seja implantado e clique em salvar. Você pode selecionar  implantado para várias regiões.

    ![Implementar modelo](media/how-to/how-to-deploy-model-regions.png)

5.  Você pode exibir o status do seu modelo na coluna "status".

>[!Note]
>O tradutor personalizado dá suporte a 10 modelos implantados em um espaço de trabalho a qualquer momento.

## <a name="update-deployment-settings"></a>Atualizar configurações de implantação

Para atualizar as configurações de implantação:

1.  Selecione um projeto e vá para a guia **modelos** .

2. Para um modelo implantado com êxito, ele mostra um botão de **atualização** .

    ![Implementar modelo](media/how-to/how-to-update-undeploy-model.png)

3.  Selecione **Atualizar**.
4.  Selecione  implantado ou não implantado para as regiões onde você deseja que seu modelo seja implantado ou não implantado e, em seguida, clique em **salvar**.

    ![Implementar modelo](media/how-to/how-to-undeploy-model.png)

>[!Note]
>Se você selecionar  não implantado para todas as regiões, o modelo será Desimplantado de todas as regiões e colocado em um estado não implantado. Agora ele não está disponível para uso.

## <a name="next-steps"></a>Passos seguintes

- Começar a utilizar o seu modelo de tradução personalizadas implementado via [API de texto do Microsoft Translator V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).
- Saiba [como gerenciar configurações](how-to-manage-settings.md) para compartilhar seu espaço de trabalho, gerenciar a chave de assinatura.
- Saiba [como migrar seu espaço de trabalho e projeto](how-to-migrate.md) do [Hub do Microsoft Translator](https://hub.microsofttranslator.com)
