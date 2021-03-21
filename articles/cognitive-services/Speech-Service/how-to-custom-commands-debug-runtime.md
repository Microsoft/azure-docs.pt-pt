---
title: Depurar erros ao executar uma aplicação de Comandos Personalizados
titleSuffix: Azure Cognitive Services
description: Neste artigo, aprende-se a depurar erros de tempo de execução numa aplicação de Comandos Personalizados.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: b41bcda7381e5811ef1e1f91c102d8c85f50b3b5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "103196921"
---
# <a name="debug-errors-when-running-a-custom-commands-application"></a>Depurar erros ao executar uma aplicação de Comandos Personalizados

Este artigo descreve como depurar quando vê erros durante a execução da aplicação Comandos Personalizados. 

## <a name="connection-failed"></a>A ligação falhou

Se a sua aplicação de Comandos Personalizados executar a partir da aplicação do [cliente (com O SDK de voz de fala)](./how-to-custom-commands-setup-speech-sdk.md) ou [do Cliente Assistente de Voz do Windows,](./how-to-custom-commands-developer-flow-test.md)poderá experimentar erros de ligação conforme listados abaixo:

| Código de erro | Detalhes |
| ------- | -------- |
| [401](#error-401) | AutenticaçãoFailure: WebSocket Upgrade falhou com um erro de autenticação |
| [1002](#error-1002) | O servidor devolveu o código de estado '404' quando o código de estado '101' era esperado. |

### <a name="error-401"></a>Erro 401
- A região especificada na aplicação do cliente não corresponde à região da aplicação de comando personalizado

- A chave de recursos de fala é inválida
    
    Certifique-se de que a chave de recursos da fala está correta.

### <a name="error-1002"></a>Erro 1002 
- A sua aplicação de comando personalizado não foi publicada
    
    Publique a sua aplicação no portal.

- A sua aplicação de comando personalizadaId não é válida

    Certifique-se de que o seu ID de aplicação de comando personalizado está correto.
 aplicação de comando personalizado fora do seu recurso de fala

    Certifique-se de que a aplicação de comando personalizada é criada sob o seu recurso de fala.

Para obter mais informações sobre a resolução de problemas de problemas de ligação, [referenciar a resolução de problemas do cliente assistente de voz do Windows](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/clients/csharp-wpf#troubleshooting)


## <a name="dialog-is-canceled"></a>O diálogo está cancelado.

Ao executar a sua aplicação Comandos Personalizados, o diálogo será cancelado quando ocorrerem alguns erros.

- Se estiver a testar a aplicação no portal, apresentará diretamente a descrição do cancelamento e reproduzirá um auricular de erro. 

- Se estiver a executar a aplicação com o [Cliente Assistente de Voz do Windows,](./how-to-custom-commands-developer-flow-test.md)reproduziria um auricular de erro. Pode encontrar o **Evento: Diálogo Cancelado** nos **Registos de Atividade.**

- Se estiver a seguir a aplicação do cliente exemplo de aplicação do nosso cliente [(com a Speech SDK),](./how-to-custom-commands-setup-speech-sdk.md)ele reproduziria um erro de auricular. Pode encontrar o **Evento: Diálogo Cancelado** sob o **Estado.**

- Se estiver a construir a sua própria aplicação ao cliente, pode sempre conceber as suas lógicas desejadas para lidar com os eventos CanceladoDialog.

O evento CanceladoDialog consiste em código de cancelamento e descrição, conforme listado abaixo:

| Código de Cancelamento | Descrição do cancelamento |
| ------- | --------------- | ----------- |
| [MaxTurnThresholdReached](#no-progress-was-made-after-the-max-number-of-turns-allowed) | Nenhum progresso foi feito após o número máximo de voltas permitidas |
| [ReconhecerQuotaExceed](#recognizer-usage-quota-exceeded) | Quota de utilização do Reconhecimento ultrapassado |
| [ReconhecerConnectionFailed](#connection-to-the-recognizer-failed) | A ligação ao reconhecimento falhou |
| [Reconhecidosuautorizados](#this-application-cannot-be-accessed-with-the-current-subscription) | Esta aplicação não pode ser acedida com a subscrição atual |
| [ReconhecerInputExceededAllowedLength](#input-exceeds-the-maximum-supported-length) | A entrada excede o comprimento máximo suportado para o reconhecedor |
| [ReconhecerNotFound](#recognizer-not-found) | Reconhecedor não encontrado |
| [ReconhecíverInvalidQuery](#invalid-query-for-the-recognizer) | Consulta inválida para o reconhecedor |
| [ReconhecerError](#recognizer-return-an-error) | Reconhecedor devolve um erro |

### <a name="no-progress-was-made-after-the-max-number-of-turns-allowed"></a>Nenhum progresso foi feito após o número máximo de voltas permitidas
O diálogo é cancelado quando uma ranhura necessária não é atualizada com sucesso após determinado número de voltas. O número máximo de construção é 3.

### <a name="recognizer-usage-quota-exceeded"></a>Quota de utilização do Reconhecimento ultrapassado
A Compreensão linguística (LUIS) tem limites na utilização de recursos. Normalmente, "O erro de utilização do reconhecimento excedeu o erro" pode ser causado por: 
- A sua autoria LUIS excede o limite

    Adicione um recurso de previsão à sua aplicação de Comandos Personalizados: 
    1. ir a **Definições**, recurso LUIS
    1. Escolha um recurso de previsão a partir do **recurso Prediction,** ou clique em **Criar novo recurso** 

- O seu recurso de previsão LUIS excede o limite

    Se estiver com um recurso de previsão de F0, tem um limite de 10 mil/mês, 5 consultas/segundo.

Para obter mais detalhes sobre os limites de recursos da LUIS, consulte a [utilização do recurso de compreensão linguística e limite](../luis/luis-limits.md#resource-usage-and-limits)

### <a name="connection-to-the-recognizer-failed"></a>A ligação ao reconhecimento falhou
Geralmente significa falha de ligação transitória para o reconhecimento da compreensão da linguagem (LUIS). Tente de novo e a questão deve ser resolvida.

### <a name="this-application-cannot-be-accessed-with-the-current-subscription"></a>Esta aplicação não pode ser acedida com a subscrição atual
A sua subscrição não está autorizada a aceder à aplicação LUIS. 

### <a name="input-exceeds-the-maximum-supported-length"></a>A entrada excede o comprimento máximo suportado
A sua entrada ultrapassou os 500 caracteres. Só permitimos, no máximo, 500 caracteres para a expressão de entrada.

### <a name="invalid-query-for-the-recognizer"></a>Consulta inválida para o reconhecedor
A sua entrada ultrapassou os 500 caracteres. Só permitimos, no máximo, 500 caracteres para a expressão de entrada.

### <a name="recognizer-return-an-error"></a>Reconhecedor devolva um erro
O reconhecimento LUIS devolveu um erro ao tentar reconhecer a sua entrada.

### <a name="recognizer-not-found"></a>Reconhecedor não encontrado
Não é possível encontrar o tipo de reconhecimento especificado no seu modelo de diálogo de comandos personalizados. Atualmente, apenas apoiamos [o Reconhecimento de Compreensão linguística (LUIS).](https://www.luis.ai/)

## <a name="other-common-errors"></a>Outros erros comuns
### <a name="unexpected-response"></a>Resposta inesperada
Respostas inesperadas podem ser causadas por várias coisas. Alguns cheques para começar com:
- Sim/Sem Intenções em frases por exemplo

    Como atualmente não suportamos Sim/Nenhuma Intenção, exceto quando utilizamos com funcionalidade de confirmação. Todas as intenções sim/não definidas em frases por exemplo não seriam detetadas.

- Intenções semelhantes e exemplos de frases entre comandos

    A precisão do reconhecimento LUIS pode ser afetada quando dois comandos partilham intenções e frases de exemplo semelhantes. Pode tentar tornar a funcionalidade de comandos e exemplos de frases o mais distintas possível.

    Para melhores práticas de melhorar a precisão do reconhecimento, consulte [as melhores práticas do LUIS](../luis/luis-concept-best-practices.md).

- O diálogo está cancelado.
    
    Verifique as razões do cancelamento na secção acima.

### <a name="error-while-rendering-the-template"></a>Erro ao renderizar o modelo
Um parâmetro indefinido é usado na resposta da fala. 

### <a name="object-reference-not-set-to-an-instance-of-an-object"></a>Referência do objeto não definida para uma instância de um objeto
Tem um parâmetro vazio na carga útil JSON definida na **ação enviar atividade para o cliente.**

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ver amostras no GitHub](https://aka.ms/speech/cc-samples)
