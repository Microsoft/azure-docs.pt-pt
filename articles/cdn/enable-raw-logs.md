---
title: Logs crus Azure CDN HTTP
description: Este artigo descreve os registos crus Azure CDN HTTP.
services: cdn
author: sohamnchatterjee
manager: danielgi
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/23/2020
ms.author: sohamnc
ms.openlocfilehash: a2522eba17574246ab99a0d47a42f128d5f61ace
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/17/2020
ms.locfileid: "84888644"
---
# <a name="azure-cdn-http-raw-logs"></a>Logs crus Azure CDN HTTP
Os registos brutos fornecem informações ricas sobre operações e erros que são importantes para a auditoria e resolução de problemas. Os registos brutos diferem dos registos de atividade. Os registos de atividade dão visibilidade às operações es feitas nos recursos da Azure. Os registos brutos fornecem um registo das operações do seu recurso.

> [!IMPORTANT]
> A funcionalidade de registos em bruto HTTP está disponível para Azure CDN da Microsoft.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration"></a>Configuração

Para configurar registos Raw para o seu Azure CDN a partir do perfil da Microsoft: 

1. A partir do menu do portal Azure, selecione **Todos os**  >>  **\<your-CDN-profile>** Recursos.

2. Em **Monitorização**, selecione **Definições de Diagnóstico**.

3. **Selecione + Adicione a definição de diagnóstico**.

    ![Definição de diagnóstico CDN](./media/cdn-raw-logs/raw-logs-01.png)

    > [!IMPORTANT]
    > Os registos brutos só estão disponíveis no nível de perfil, enquanto os registos de código de estado http agregados estão disponíveis no nível do ponto final.

4. Nas **definições de Diagnóstico,** introduza um nome para a definição de diagnóstico no **nome de definições de Diagnóstico**.

5. Selecione o **registo** e detenda a retenção em dias.

6. Selecione os **detalhes do Destino.** As opções de destino são:
    * **Enviar para o Log Analytics**
        * Selecione o espaço de trabalho **subscrição** e **Log Analytics**.
    * **Arquivar para uma conta de armazenamento**
        * Selecione a **Subscrição** e a **Conta de Armazenamento.**
    * **Transmita para um centro de eventos**
        * Selecione o espaço de nome do centro de **eventos,** **nome do hub do evento (opcional)** e nome de política do centro de **eventos**. **Subscription**

    ![Definição de diagnóstico CDN](./media/cdn-raw-logs/raw-logs-02.png)

7. Selecione **Guardar**.

## <a name="raw-logs-properties"></a>Propriedades de troncos crus

A Azure CDN do Microsoft Service fornece atualmente registos Raw. Os registos brutos fornecem pedidos individuais de API com cada entrada com o seguinte esquema: 

| Propriedade              | Descrição                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | A cadeia de referência única que identifica um pedido servido pela Porta da Frente, também enviada como cabeçalho X-Azure-Ref para o cliente. Necessário para pesquisar detalhes nos registos de acesso para um pedido específico. |
| HttpMethod            | Método HTTP utilizado pelo pedido.                                                                                                                                                                     |
| HttpVersion           | Tipo de pedido ou ligação.                                                                                                                                                                   |
| RequestUri            | URI do pedido recebido.                                                                                                                                                                         |
| PedidoBytes          | O tamanho da mensagem de pedido HTTP em bytes, incluindo os cabeçalhos de pedido e o corpo de pedido.                                                                                                   |
| RespostaBytes         | Bytes enviados pelo servidor backend como resposta.                                                                                                                                                    |
| UserAgent             | O tipo de navegador que o cliente usou.                                                                                                                                                               |
| ClientIp              | O endereço IP do cliente que fez o pedido.                                                                                                                                                  |
| TimeTaken             | O tempo que a ação demorou, em milissegundos.                                                                                                                                            |
| SecurityProtocol      | A versão do protocolo TLS/SSL utilizada pelo pedido ou nula se não houver encriptação.                                                                                                                           |
| Ponto Final              | O anfitrião do ponto final cdn configurado no perfil principal da CDN.                                                                                                                                   |
| Nome do anfitrião backend     | O nome do hospedeiro de backend ou origem para onde os pedidos estão a ser enviados.                                                                                                                                |
| Enviado para o escudo de origem | Se for verdade, significa que o pedido foi respondido a partir da cache do escudo de origem em vez do pop de borda. O escudo de origem é uma cache dos pais usada para melhorar a relação de cache.                                       |
| HttpStatusCode        | O código de estado HTTP devolvido do representante.                                                                                                                                                        |
| HttpStatusDetails     | Estado resultante do pedido. O significado deste valor de cadeia pode ser encontrado numa tabela de referência de Estado.                                                                                              |
| Pop                   | O pop de borda, que respondeu ao pedido do utilizador. As abreviaturas dos POPs são códigos aeroportuários dos respetivos metros.                                                                                   |
| Estado da Cache          | Significa se o objeto foi devolvido da cache ou veio da origem.                                                                                                             |
> [!IMPORTANT]
> A funcionalidade de registos HTTP Raw está disponível automaticamente para quaisquer perfis criados ou atualizados após **25 de fevereiro de 2020**. Para os perfis cdN criados anteriormente, deve-se atualizar o ponto final do CDN após a configuração do registo. Por exemplo, pode-se navegar para a geo filtragem sob os pontos finais da CDN e bloquear qualquer país/região que não seja relevante para a sua carga de trabalho e salvar. 

> [!NOTE]
> Os registos podem ser visualizados no perfil de Log Analytics executando uma consulta. Uma consulta de amostra pareceria AzureDiagnostics onde categoria == "AzureCdnAccessLog"

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, ativou registos em bruto HTTP para o serviço Microsoft CDN.

Para obter mais informações sobre a Azure CDN e os outros serviços Azure mencionados neste artigo, consulte:

* [Analisar](cdn-log-analysis.md) Padrões de utilização de Azure CDN.

* Saiba mais sobre [o Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/overview)

* Configurar [o Log Analytics no Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
