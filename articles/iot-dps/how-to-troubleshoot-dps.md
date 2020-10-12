---
title: Desconexões de resolução de problemas e de resolução de problemas com DPS do Hub IoT do Azure
description: Aprenda a diagnosticar e resolver erros comuns com a conectividade do dispositivo para o Serviço de Provisionamento de Dispositivos Azure IoT Hub (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75646477"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Resolução de problemas com serviço de provisionamento de dispositivos de hub Azure IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de resolver problemas porque existem muitos pontos possíveis de falhas, tais como falhas no atestado, falhas de registo, etc. Este artigo fornece orientações sobre como detetar e resolver problemas de conectividade do dispositivo através do [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Usando o Monitor Azure para ver métricas e configurar alertas

O procedimento a seguir descreve como visualizar e configurar alerta na métrica do Serviço de Provisionamento de Dispositivos IoT Hub. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu serviço de provisionamento de dispositivos IoT Hub.

3. Selecione **Métricas**.

4. Selecione a métrica desejada. 
   <br />Atualmente existem três métricas para DPS:

    | Nome da Métrica | Descrição |
    |-------|------------|
    | Tentativas de atestado | Número de dispositivos que tentaram autenticar com o Serviço de Provisionamento de Dispositivos|
    | Tentativas de registo | Número de dispositivos que tentaram registar-se no IoT Hub após autenticação bem sucedida|
    | Dispositivo atribuído | Número de dispositivos que foram atribuídos com sucesso ao IoT Hub|

5. Selecione o método de agregação desejado para criar uma visão visual da métrica. 

6. Para configurar um alerta de métrica, selecione **Novas regras** de alerta a partir do topo direito da lâmina métrica, da mesma forma pode ir à lâmina **de alerta** e selecionar **novas regras de alerta**.

7. **Selecione Adicionar a condição,** em seguida, selecione a métrica e o limiar desejados seguindo as indicações.

Para saber mais, veja [quais são os alertas clássicos no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Utilizar Log Analytic para ver e resolver erros

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu hub IoT.

3. Selecione **definições de Diagnóstico**.

4. **Selecione Ligue os diagnósticos**.

5. Ativar os registos desejados para serem recolhidos.

    | Nome do registo | Descrição |
    |-------|------------|
    | Operações de Dispositivos | Registos relacionados com eventos de ligação do dispositivo |
    | Operações de Serviço | Registos de eventos relacionados com a utilização do serviço SDK (por exemplo, criar ou atualizar grupos de inscrição)|

6. Ligue **enviar para registar análise** [(ver preços).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Aceda ao **separador Registos** no portal Azure sob recurso de Serviço de Provisionamento de Dispositivos.

8. Clique em **Executar** para ver eventos recentes.

9. Se houver resultados, procure `OperationName` , `ResultType` e `ResultSignature` `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.


## <a name="common-error-codes"></a>Códigos de erro comuns
Use esta tabela para compreender e resolver erros comuns.

| Código de Erro| Descrição | Código de Estado HTTP |
|-------|------------|------------|
| 400 | O corpo do pedido não é válido; por exemplo, não pode ser analisado, ou o objeto não pode ser validado.| 400 Mau formato |
| 401 | O símbolo de autorização não pode ser validado; por exemplo, expira ou não se aplica ao URI do pedido. Este código de erro também é devolvido aos dispositivos como parte do fluxo de atestado TPM. | 401 Não Autorizado|
| 404 | A instância do Serviço de Provisionamento de Dispositivos, ou um recurso (por exemplo, uma inscrição) não existe. |404 Não Encontrado |
| 412 | O ETag no pedido não corresponde ao ETag do recurso existente, de acordo com o RFC7232. | 412 Pré-condição falhou |
| 429 | As operações estão a ser estranguladas pelo serviço. Para limites de serviço específicos, consulte [os limites do Serviço de Provisionamento de Dispositivos IoT Hub](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Pedidos a mais |
| 500 | Ocorreu um erro interno. | Erro Interno do Servidor 500|
