---
title: Melhorar a base de dados de conhecimento - QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/05/2019
ms.author: diberry
ms.openlocfilehash: 7281fb15e91195e1dd20095d9fdf80d3d9894a26
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433064"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>Utilizar a aprendizagem ativa para melhorar a base de dados de conhecimento

Aprendizagem ativa permite-lhe melhorar a qualidade da sua base de dados de conhecimento ao sugerir perguntas alternativas, com base no utilizador envios, para o par de perguntas e respostas. Examinar essas sugestões, a adicioná-las à existente perguntas ou rejeitá-los. 

A base de dados de conhecimento não é alterado automaticamente. Tem de aceitar as sugestões para que todas as alterações entrem em vigor. Estas sugestões adicionar perguntas, mas não alterar ou remover perguntas existentes.

## <a name="active-learning"></a>Aprendizagem ativa

A ferramenta QnA Maker aprende novas variações de pergunta com comentários implícita e explícita.
 
* Comentários implícito – o classificador compreende quando uma pergunta de utilizador tem várias respostas com classificações que são muito parecidas e considera-o como comentários. 
* Comentários explícitos – quando várias respostas com pouca variação na pontuações são devolvidas a partir da base de dados de conhecimento, o aplicativo cliente pede ao usuário que pergunta é a pergunta correta. Comentários de explícita do utilizador é enviado para a ferramenta QnA Maker com a API de comboio. 

Qualquer um dos métodos fornece o classificador com consultas similares estão agrupados em cluster.

## <a name="how-active-learning-works"></a>Como Active Directory funciona de aprendizagem

Aprendizagem ativa é acionada com base em classificações de melhores respostas alguns devolvidas pelo QnA Maker para qualquer determinada consulta. Se as diferenças de pontuação se situam dentro de um pequeno intervalo, em seguida, a consulta é considerada uma possível _sugestão_ para cada uma das possíveis respostas. 

Todas as sugestões sejam agrupadas por semelhança e sugestões principais para perguntas alternativas são apresentadas com base na frequência das consultas determinadas pelos usuários finais. Aprendizagem ativa fornece as sugestões de possíveis melhor em casos em que os pontos finais estão a obter uma quantidade razoável e a variedade de consultas de utilização.

Quando as consultas de 5 ou mais semelhantes são colocados num cluster, cada 30 minutos, a ferramenta QnA Maker sugere perguntas baseada no utilizador para o estruturador de base de dados de conhecimento para aceitar ou rejeitar.

Assim que as perguntas são sugeridas no portal do QnA Maker, terá de rever e aceitar ou rejeitar essas sugestões. 

## <a name="upgrade-version-to-use-active-learning"></a>Atualizar a versão a utilizar a aprendizagem ativa

Aprendizagem ativa é suportada na versão de runtime 4.4.0 e acima. Se a sua base de dados de conhecimento foi criado numa versão anterior, [atualizar o runtime](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates) para utilizar esta funcionalidade. 

## <a name="best-practices"></a>Melhores práticas

Para melhores práticas quando utilizar a aprendizagem ativa, consulte [melhores práticas](../Concepts/best-practices.md#active-learning).

## <a name="score-proximity-between-knowledge-base-questions"></a>Pontuação de proximidade entre perguntas de base de dados de conhecimento

Quando a pontuação de uma pergunta é altamente seguro, por exemplo, 80%, o intervalo de classificações que são considerados para a aprendizagem ativa são ampla, aproximadamente dentro de 10%. À medida que diminui a pontuação de confiança, como a 40%, o intervalo das pontuações diminui, aproximadamente dentro de 4%. 

O algoritmo para determinar a proximidade não é um cálculo simple. Os intervalos nos exemplos anteriores não devem ser corrigidos, mas devem ser utilizados como um guia para compreender o impacto do algoritmo apenas.

## <a name="turn-on-active-learning"></a>Ativar o aprendizagem ativa

Aprendizagem ativa está desativada por predefinição. A ativá-lo para ver perguntas sugeridas. 

1. Selecione **publicar** para publicar a base de dados de conhecimento. Consultas de aprendizagem ativa são recolhidas a partir do GenerateAnswer predição ponto final de API apenas. As consultas para o painel de teste no portal do Qna Maker não afetam aprendizagem ativa.

1. Para ativar o Active Directory de aprendizado no, clique no seu **Name**, aceda a [ **definições de serviço** ](https://www.qnamaker.ai/UserSettings) no portal do QnA Maker, no canto superior direito.  

    ![Na página de definições de serviço, ative aprendizagem ativa](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Encontrar o serviço QnA Maker, em seguida, alternar **aprendizagem ativa**. 

    [![Na página de definições de serviço, ative aprendizagem ativa](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    Uma vez **aprendizagem ativa** é ativado, o conhecimento sugere novas questões em intervalos regulares, com base nas perguntas enviadas por utilizador. Pode desabilitar **aprendizagem ativa** , Ativando a definição novamente.

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>Adicionar sugestão de aprendizagem ativa a base de dados de conhecimento

1. Para ver perguntas sugeridas, sobre o **editar** página base de dados de conhecimento, selecione **Mostrar sugestões**. 

    [![Na página de definições de serviço, o botão de Mostrar sugestões de alternar.](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. Filtrar a base de dados de conhecimento com pares de perguntas e respostas para mostrar apenas sugestões selecionando **filtrar por sugestões**.

    [![Na página de definições de serviço, filtrar por sugestões para ver apenas as pergunta/respostam pares](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  Cada secção de pergunta com sugestões mostra as novas questões com uma marca de verificação `✔` , para aceitar a pergunta ou um `x` para rejeitar as sugestões. Selecione a marca de verificação para adicionar a pergunta. 

    [![Na página de definições de serviço, ative aprendizagem ativa](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    Pode adicionar ou eliminar _todas as sugestões_ ao selecionar **adicione todos** ou **rejeitar tudo**.

1. Selecione **guardar e treinar** para guardar as alterações para a base de dados de conhecimento.

1. Selecione **publicar** para permitir que as alterações estar disponível a partir da API de GenerateAnswer.

    Quando as consultas de 5 ou mais semelhantes são colocados num cluster, cada 30 minutos, a ferramenta QnA Maker sugere perguntas baseada no utilizador para o estruturador de base de dados de conhecimento para aceitar ou rejeitar.

## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>Determinar a melhor escolha quando várias perguntas tem pontuações semelhantes

Quando uma pergunta é demasiado Fechar na pontuação para outras perguntas, o programador da aplicação cliente pode optar por pedir esclarecimentos.

### <a name="use-the-top-property-in-the-generateanswer-request"></a>Utilize a propriedade superior no pedido GenerateAnswer

Quando submeter uma pergunta para a ferramenta QnA Maker para uma resposta, permite que parte do corpo do JSON para devolver mais do que uma resposta principal:

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

Quando a aplicação de cliente (como um chatbot) recebe a resposta, são devolvidas melhores 3 perguntas:

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>Acompanhamento de aplicação de cliente quando perguntas têm pontuações semelhantes

A aplicação cliente apresenta todas as dúvidas com uma opção para o utilizador selecionar a pergunta que mais representa a sua intenção. 

Assim que o usuário seleciona uma das perguntas existentes. Os comentários do utilizador são enviados para o QnA Maker [Train](http://www.aka.ms/activelearningsamplebot) loop de API para continuar o comentários de aprendizagem ativa. 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

Saiba mais sobre como utilizar a aprendizagem ativa com um [Bot do Azure C# exemplo](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)

## <a name="active-learning-is-saved-in-the-exported-apps-tsv-file"></a>Aprendizagem ativa é guardada no ficheiro de tsv exportado da aplicação

Quando a aplicação tem a aprendizagem ativa ativada e exportar a aplicação, o `SuggestedQuestions` coluna no ficheiro tsv retém os dados de aprendizagem ativa. 

O `SuggestedQuestions` coluna é um objeto JSON de informações de implícito (`autosuggested`) e explícito (`usersuggested`) [comentários](#active-learning). Um exemplo deste objeto JSON para uma utilizador submeteu pergunta de `help` é:

```JSON
[
    {
        "clusterHead": "help",
        "totalAutoSuggestedCount": 1,
        "totalUserSuggestedCount": 0,
        "alternateQuestionList": [
            {
                "question": "help",
                "autoSuggestedCount": 1,
                "userSuggestedCount": 0
            }
        ]
    }
]
```

Quando importe novamente esta aplicação, a aprendizagem ativa continua a recolher informações e recomendar sugestões para a sua base de dados de conhecimento. 

## <a name="next-steps"></a>Passos Seguintes
 
> [!div class="nextstepaction"]
> [Utilizar a API do QnA Maker](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
