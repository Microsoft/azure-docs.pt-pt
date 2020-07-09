---
title: Verifique a instância do recipiente de saúde
titleSuffix: Azure Cognitive Services
description: Saiba como verificar a instância do recipiente de saúde.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 5c598807f36000a18211e32eba53220bfbeea2f7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108720"
---
### <a name="verify-that-a-container-is-running"></a>Verifique se um contentor está a funcionar

1. Selecione o **separador Visão Geral** e copie o endereço IP.
1. Abra um novo separador de navegador e insira o endereço IP. Por exemplo, `http://<IP-address>:5000 (http://55.55.55.55:5000` insira). A página inicial do recipiente é apresentada, o que lhe permite saber se o recipiente está em funcionamento.

    ![Veja a página inicial do contentor para verificar se está em funcionamento](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Selecione o link de descrição da **API de serviço** para ir à página swagger do recipiente.

1. Escolha qualquer uma das APIs **post** e selecione **Experimentá-lo**. Os parâmetros são apresentados, que inclui a entrada de exemplo.

Existem vários URLs que também pode usar para verificar se o recipiente está em funcionamento.

|Pedir|Objetivo|
|--|--|
|`http://localhost:5000/`|O recipiente fornece uma página inicial.|
|`http://localhost:5000/ready`|Solicitado com GET, isto fornece uma verificação de que o recipiente está pronto para aceitar uma consulta contra o modelo. Este pedido pode ser utilizado para sondas kubernetes [de prontidão e prontidão.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)|
|`http://localhost:5000/status`|Solicitado com GET, tal como o ponto final /pronto, isto valida que o recipiente está a funcionar sem incorrer numa consulta contra o modelo. Este pedido pode ser utilizado para sondas kubernetes [de prontidão e prontidão.](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-probes/)|
|`http://localhost:5000/swagger`|Através deste URL, o recipiente fornece um conjunto completo de documentação para os pontos finais e uma `Try it now` característica. Com esta funcionalidade, pode introduzir as suas definições num formulário HTML baseado na Web e fazer a consulta sem ter de escrever qualquer código. Após o retorno da consulta, é fornecido um comando CURL de exemplo para demonstrar os cabeçalhos HTTP e o formato corporal que é necessário. |
|`http://localhost:5000/demo`| Solicitada através de um browser, esta funcionalidade fornece uma visualização interativa dos resultados a partir de consultas de amostras de texto de entrada ou de uma que você fornece.  |

Utilize este URL de pedido `http://localhost:5000/text/analytics/v3.0-preview.1/domains/health` para submeter uma consulta ao recipiente.
