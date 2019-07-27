---
title: Resolução de problemas - QnAMaker
titleSuffix: Azure Cognitive Services
description: QnAMaker é composto por componentes alojados na conta de utilizador do Azure. Depuração pode exigir que os utilizadores manipular seus recursos do Azure do QnAMaker ou fornecer a equipa de suporte do QnAMaker com informações adicionais sobre sua configuração.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 94a3dfd9a3560321d7e2753ccd385fb1a5323107
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559926"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Dicas de solução de problemas para suportar o serviço QnA Maker e o tempo de execução
QnAMaker é composto por componentes alojados na conta de utilizador do Azure. Depuração pode exigir que os utilizadores manipular seus recursos do Azure do QnAMaker ou fornecer a equipa de suporte do QnAMaker com informações adicionais sobre sua configuração.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Como obter as mais recentes atualizações de tempo de execução do QnAMaker
Tempo de execução do QnAMaker faz parte do serviço de aplicações do Azure implementadas quando [criar um serviço de QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. As atualizações são feitas periodicamente o tempo de execução. QnA Maker serviço de aplicativo estiver no modo de atualização automática, poste nossa versão de extensão de site de abril 2019 (versão 5 +). Isso já foi projetado para cuidar do tempo de inatividade ZERO durante as atualizações. Você pode verificar a versão atual em https://www.qnamaker.ai/UserSettings. Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes a serem aplicadas à configuração do QnAMaker.

1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique na aplicação do serviço e abra a secção de descrição geral

     ![Serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. Reinicie o serviço de aplicações. Ele deve ser concluído dentro de alguns segundos. Tenha em atenção que seus aplicativos/bots downstream têm por base este serviço QnAMaker estará disponível para os utilizadores finais durante este período de reinício.

    ![Reiniciar o serviço de aplicações QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Como obter o nome de anfitrião do serviço do QnAMaker
O nome de anfitrião do QnAMaker serviço é útil para fins de depuração quando contactar o suporte de QnAMaker ou UserVoice. O nome do anfitrião é um URL neste formato: https:// *{nome do anfitrião}* . azurewebsites.net.
    
1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

2. Clique no serviço de aplicações

     ![Selecione o serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

3. O URL de nome de anfitrião está disponível na seção de visão geral

    ![Nome de anfitrião do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Melhorar as perguntas da base de dados de conhecimento com o aprendizado ativo](./improve-knowledge-base.md)
