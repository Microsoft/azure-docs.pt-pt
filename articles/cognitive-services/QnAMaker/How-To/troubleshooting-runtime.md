---
title: Resolução de problemas - QnAMaker
titlesuffix: Azure Cognitive Services
description: QnAMaker é composto por componentes alojados na conta de utilizador do Azure. Depuração pode exigir que os utilizadores manipular seus recursos do Azure do QnAMaker ou fornecer a equipa de suporte do QnAMaker com informações adicionais sobre sua configuração.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 6a84ecff04381b6dcc9706105df92ecc96dc8b1c
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55882102"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Dicas de solução de problemas para suportar o serviço QnA Maker e o tempo de execução
QnAMaker é composto por componentes alojados na conta de utilizador do Azure. Depuração pode exigir que os utilizadores manipular seus recursos do Azure do QnAMaker ou fornecer a equipa de suporte do QnAMaker com informações adicionais sobre sua configuração.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Como obter as mais recentes atualizações de tempo de execução do QnAMaker
Tempo de execução do QnAMaker faz parte do serviço de aplicações do Azure implementadas quando [criar um serviço de QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. As atualizações são feitas periodicamente o tempo de execução. Para aplicar as atualizações mais recentes para aplicar a sua configuração de QnAMaker, tem de reiniciar o serviço de aplicações.
1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique na aplicação do serviço e abra a secção de descrição geral

     ![Serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Reinicie o serviço de aplicações. Ele deve ser concluído dentro de alguns segundos. Tenha em atenção que seus aplicativos/bots downstream têm por base este serviço QnAMaker estará disponível para os utilizadores finais durante este período de reinício.

    ![Reiniciar o serviço de aplicações QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Como obter o nome de anfitrião do serviço do QnAMaker
O nome de anfitrião do QnAMaker serviço é útil para fins de depuração quando contactar o suporte de QnAMaker ou UserVoice. O nome do anfitrião é um URL neste formato: https://*{nome do anfitrião}*. azurewebsites.net.
    
1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique no serviço de aplicações

     ![Selecione o serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. O URL de nome de anfitrião está disponível na seção de visão geral

    ![Nome de anfitrião do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Melhorar as perguntas de base de dados de conhecimento com aprendizagem ativa](./improve-knowledge-base.md)
