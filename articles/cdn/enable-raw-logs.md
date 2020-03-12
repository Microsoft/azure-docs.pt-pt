---
title: Troncos crus Azure CDN HTTP
description: Este artigo descreve os registos brutos Azure CDN HTTP.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2020
ms.author: allensu
ms.openlocfilehash: 79ced4df8df12bf2ef1fbe0075b53d02fafb2aff
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79129829"
---
# <a name="azure-cdn-http-raw-logs"></a>Troncos crus Azure CDN HTTP
Os registos brutos fornecem informações ricas sobre operações e erros que são importantes para a auditoria e resolução de problemas. Os troncos brutos diferem dos registos de atividade. Os registos de atividade proporcionam visibilidade às operações feitas nos recursos do Azure. Os registos brutos fornecem um registo das operações do seu recurso.

> [!IMPORTANT]
> A funcionalidade de registos crus HTTP está disponível para O CDN Azure da Microsoft.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configuração

Para configurar registos em bruto para o seu CDN Azure a partir do perfil da Microsoft: 

1. No menu do portal Azure, selecione **Todos os Recursos** >> \<seu perfil **CDN>** .

2. Em **monitorização,** selecione **definições de diagnóstico**.

3. Selecione **+ Adicione a definição de diagnóstico**.

    ![Definição de diagnóstico do CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Os registos brutos só estão disponíveis no nível de perfil, enquanto os registos de código de estado de http agregados estão disponíveis no nível final.

4. Em **definições de diagnóstico,** introduza um nome para a definição de diagnóstico com o nome de **definições**de diagnóstico .

5. Selecione o **registo** e detete tere a retenção em dias.

6. Selecione os detalhes do **Destino**. As opções de destino são:
    * **Enviar para Log Analytics**
        * Selecione o espaço de trabalho **de Subscrição** e **Log Analytics**.
    * **Arquivar para uma conta de armazenamento**
        * Selecione a **Subscrição** e a **Conta de Armazenamento.**
    * **Stream para um centro de eventos**
        * Selecione o espaço de nome do centro de **subscrição,** **evento,** nome do **hub do evento (opcional)** , e nome de **política do hub do evento**.

    ![Definição de diagnóstico do CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecione **Guardar**.

## <a name="raw-logs-properties"></a>Propriedades de troncos crus

O Azure CDN do Microsoft Service fornece atualmente registos em bruto (loteado de hora a hora). Os registos brutos fornecem pedidos individuais de API com cada entrada com o seguinte esquema: 

| Propriedade              | Descrição                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| HttpMethod            | Método HTTP utilizado pelo pedido.                                                                                                                                                                     |
| HttpVersion           | Tipo do pedido ou ligação.                                                                                                                                                                   |
| RequestUri            | URI do pedido recebido.                                                                                                                                                                         |
| RequestBytes          | O tamanho da mensagem de pedido http em bytes, incluindo os cabeçalhos de pedido e o corpo de pedido.                                                                                                   |
| RespostaBytes         | Bytes enviados pelo servidor backend como resposta.                                                                                                                                                    |
| UserAgent             | O tipo de navegador que o cliente usou.                                                                                                                                                               |
| ClientIp              | O endereço IP do cliente que fez o pedido.                                                                                                                                                  |
| Tempo tomado             | O tempo que a ação demorou, em milissegundos.                                                                                                                                            |
| Protocolo de Segurança      | A versão protocolar TLS/SSL utilizada pelo pedido ou nula se não houver encriptação.                                                                                                                           |
| Ponto Final              | O hospedeiro final da CDN configurado sob o perfil CDN do progenitor.                                                                                                                                   |
| Backend Nome anfitrião     | O nome do hospedeiro ou origem do backend onde os pedidos são enviados.                                                                                                                                |
| Enviado para o escudo de origem | Se for verdade, significa que o pedido foi respondido da cache do escudo de origem em vez do pop de borda. O escudo de origem é uma cache dos pais usada para melhorar a relação de impacto da cache.                                       |
| HttpStatusCode        | O código de estado HTTP devolvido do representante.                                                                                                                                                        |
| HttpStatusDetails     | Consequente situação no pedido. O significado deste valor de cadeia pode ser encontrado numa tabela de referência do Estado.                                                                                              |
| Pop                   | O edge pop, que respondeu ao pedido do utilizador. As abreviaturas dos POPs são códigos aeroportuários dos respetivos metros.                                                                                   |
| Estado da Cache          | Significa se o objeto foi devolvido da cache ou veio da origem.                                                                                                             |

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, ativou os registos em bruto http para o serviço CDN da Microsoft.

Para obter mais informações sobre o Azure CDN e os outros serviços Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) Padrões de utilização do CDN azure.

* Saiba mais sobre [o Monitor Azure.](https://docs.microsoft.com/azure/azure-monitor/overview)

* Configure [o Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).