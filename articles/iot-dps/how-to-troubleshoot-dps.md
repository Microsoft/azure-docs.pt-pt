---
title: Diagnosticar e resolver problemas desliga-se com DPS do Hub Azure IoT
description: Aprenda a diagnosticar e resolver erros comuns com conectividade do dispositivo para o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75646477"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Resolução de problemas com o Serviço de Provisionamento de Dispositivos Hub Azure IoT

Problemas de conectividade para dispositivos IoT podem ser difíceis de resolver porque existem muitos pontos possíveis de falhas como falhas de atestado, falhas de registo, etc. Este artigo fornece orientações sobre como detetar e resolver problemas de conectividade do dispositivo através do [Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Utilização do Monitor Azure para visualizar métricas e criar alertas

O procedimento seguinte descreve como visualizar e configurar alerta na métrica do Serviço de Provisionamento de Dispositivos IoT Hub. 

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue no seu Serviço de Provisionamento de Dispositivos IoT Hub.

3. Selecione **Métricas**.

4. Selecione a métrica desejada. 
   <br />Atualmente existem três métricas para DPS:

    | Nome métrico | Descrição |
    |-------|------------|
    | Tentativas de atestação | Número de dispositivos que tentaram autenticar com o Serviço de Fornecimento de Dispositivos|
    | Tentativas de registo | Número de dispositivos que tentaram registar-se no IoT Hub após autenticação bem sucedida|
    | Dispositivo atribuído | Número de dispositivos que com sucesso atribuídos ao IoT Hub|

5. Selecione o método de agregação desejado para criar uma visão visual da métrica. 

6. Para configurar um alerta de uma métrica, selecione **Novas regras** de alerta a partir da parte superior direita da lâmina métrica, da mesma forma que pode ir para a lâmina **alerta** e selecionar Novas regras de **alerta**.

7. **Selecione Adicionar condição**e, em seguida, selecione a métrica e o limiar desejados seguindo as instruções.

Para saber mais, veja [quais são os alertas clássicos no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Usar log analítico para ver e resolver erros

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Navegue pelo seu hub ioT.

3. Selecione **definições de Diagnóstico**.

4. Selecione **Ligar os diagnósticos**.

5. Permitir a recolha dos registos desejados.

    | Nome do registo | Descrição |
    |-------|------------|
    | Operações de Dispositivos | Registos relacionados com eventos de ligação ao dispositivo |
    | Operações de Serviço | Registos de eventos relacionados com a utilização do serviço SDK (por exemplo, criação ou atualização de grupos de inscrição)|

6. Ligue **Enviar para Registar Analytics** [(ver preços).](https://azure.microsoft.com/pricing/details/log-analytics/) 

7. Aceda ao separador **Logs** no portal Azure sob recurso do Serviço de Provisionamento de Dispositivos.

8. Clique em **Correr** para ver eventos recentes.

9. Se houver resultados, `OperationName` `ResultType`procure, `ResultSignature` `ResultDescription` e (mensagem de erro) para obter mais detalhes sobre o erro.


## <a name="common-error-codes"></a>Códigos de erro comuns
Use esta tabela para compreender e resolver erros comuns.

| Código de Erro| Descrição | Código de Estado HTTP |
|-------|------------|------------|
| 400 | O corpo do pedido não é válido; por exemplo, não pode ser analisado, ou o objeto não pode ser validado.| 400 Mau formato |
| 401 | O símbolo de autorização não pode ser validado; por exemplo, está expirado ou não se aplica ao URI do pedido. Este código de erro também é devolvido aos dispositivos como parte do fluxo de atestado TPM. | 401 Não autorizado|
| 404 | A instância do Serviço de Provisionamento de Dispositivos, ou um recurso (por exemplo, uma inscrição) não existe. |404 Não Encontrado |
| 412 | O ETag no pedido não corresponde ao ETag do recurso existente, de acordo com o RFC7232. | 412 Pré-condição falhou |
| 429 | As operações estão a ser aceleradas pelo serviço. Para limites de serviço específicos, consulte os limites do Serviço de [Provisionamento de Dispositivos IoT Hub](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 Pedidos a mais |
| 500 | Ocorreu um erro interno. | 500 Erro de Servidor Interno|
