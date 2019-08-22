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
ms.date: 08/20/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b58a08c4cfa97356be4064456b2e9ec5cceccfbc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876329"
---
# <a name="troubleshooting-tips-to-support-the-qna-maker-service-and-runtime"></a>Dicas de solução de problemas para suportar o serviço QnA Maker e o tempo de execução

QnAMaker inclui recursos hospedados na assinatura do Azure do usuário. A depuração pode exigir que os usuários manipulem seus recursos de QnAMaker do Azure ou forneçam à equipe de suporte do QnAMaker informações adicionais sobre a configuração.

## <a name="how-to-get-latest-qnamaker-runtime-updates"></a>Como obter as mais recentes atualizações de tempo de execução do QnAMaker

O tempo de execução do QnAMaker faz parte do serviço Azure App implantado quando você [cria um serviço QnAMaker](./set-up-qnamaker-service-azure.md) no portal do Azure. As atualizações são feitas periodicamente o tempo de execução. QnA Maker serviço de aplicativo está no modo de atualização automática após a versão da extensão de site de abril de 2019 (versão 5 +). Isso já foi projetado para cuidar do tempo de inatividade ZERO durante as atualizações. 

Você pode verificar a versão atual em https://www.qnamaker.ai/UserSettings. Se sua versão for anterior à versão 5. x, você deverá reiniciar o serviço de aplicativo para aplicar as atualizações mais recentes.

1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Clique na aplicação do serviço e abra a secção de descrição geral

     ![Serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. Reinicie o serviço de aplicações. Ele deve ser concluído dentro de alguns segundos. Observe que quaisquer aplicativos dependentes ou bots que usam esse serviço QnAMaker não estarão disponíveis para os usuários finais durante esse período de reinicialização.

    ![Reiniciar o serviço de aplicações QnAMaker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="how-to-get-the-qnamaker-service-hostname"></a>Como obter o nome de anfitrião do serviço do QnAMaker
O nome de anfitrião do QnAMaker serviço é útil para fins de depuração quando contactar o suporte de QnAMaker ou UserVoice. O nome do host é uma URL neste formato: https:// *{hostname}* . azurewebsites.net.
    
1. Aceda ao seu serviço de QnAMaker (grupo de recursos) [portal do Azure](https://portal.azure.com)

    ![Grupo de recursos do Azure do QnAMaker no portal do Azure](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. Selecione o serviço de aplicativo associado ao recurso de QnA Maker. Normalmente, os nomes são os mesmos.

     ![Selecione o serviço de aplicações do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)

1. O URL de nome de anfitrião está disponível na seção de visão geral

    ![Nome de anfitrião do QnAMaker](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-gethostname.png)
    

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Melhorar as perguntas da base de dados de conhecimento com o aprendizado ativo](./improve-knowledge-base.md)
