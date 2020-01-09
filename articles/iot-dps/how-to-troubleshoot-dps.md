---
title: Diagnosticar e solucionar problemas de desconexões com o DPS do Hub IoT do Azure
description: Aprenda a diagnosticar e solucionar erros comuns com conectividade de dispositivo para o serviço de provisionamento de dispositivos no Hub IoT do Azure (DPS)
author: xujing-ms
manager: nberdy
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: xujing
ms.openlocfilehash: 3cbab09c6b50abb590cfe9f2720713a8fa547aa7
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646477"
---
# <a name="troubleshooting-with-azure-iot-hub-device-provisioning-service"></a>Solução de problemas com o serviço de provisionamento de dispositivos no Hub IoT do Azure

Problemas de conectividade para dispositivos IoT podem ser difíceis de solucionar porque há muitos pontos possíveis de falhas, como falhas de atestado, falhas de registro etc. Este artigo fornece orientação sobre como detectar e solucionar problemas de conectividade do dispositivo por meio de [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

## <a name="using-azure-monitor-to-view-metrics-and-set-up-alerts"></a>Usando Azure Monitor para exibir métricas e configurar alertas

O procedimento a seguir descreve como exibir e configurar o alerta na métrica do serviço de provisionamento de dispositivos no Hub IoT. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Navegue até o serviço de provisionamento de dispositivos do Hub IoT.

3. Selecione **Métricas**.

4. Selecione a métrica desejada. 
   <br />Atualmente, há três métricas para o DPS:

    | Nome da Métrica | Descrição |
    |-------|------------|
    | Tentativas de atestado | Número de dispositivos que tentaram autenticar com o serviço de provisionamento de dispositivos|
    | Tentativas de registro | Número de dispositivos que tentaram registrar no Hub IoT após a autenticação bem-sucedida|
    | Dispositivo atribuído | Número de dispositivos que foram atribuídos com êxito ao Hub IoT|

5. Selecione o método de agregação desejado para criar uma exibição Visual da métrica. 

6. Para configurar um alerta de uma métrica, selecione **novas regras de alerta** na parte superior direita da folha métrica, da mesma forma você pode ir para a folha **alerta** e selecionar **novas regras de alerta**.

7. Selecione **Adicionar condição**e, em seguida, selecione a métrica e o limite desejados seguindo os prompts.

Para saber mais, confira [o que são alertas clássicos no Microsoft Azure?](../azure-monitor/platform/alerts-overview.md)

## <a name="using-log-analytic-to-view-and-resolve-errors"></a>Usando o log analítico para exibir e resolver erros

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).

2. Navegue até o Hub IoT.

3. Selecione **configurações de diagnóstico**.

4. Selecione **ativar os diagnósticos**.

5. Habilite os logs desejados a serem coletados.

    | Nome do registo | Descrição |
    |-------|------------|
    | DeviceOperations | Logs relacionados aos eventos de conexão do dispositivo |
    | ServiceOperations | Logs de eventos relacionados ao uso do SDK do serviço (por exemplo, criação ou atualização de grupos de registro)|

6. Ative **Enviar para log Analytics** ([consulte preços](https://azure.microsoft.com/pricing/details/log-analytics/)). 

7. Vá para a guia **logs** na portal do Azure em recurso de serviço de provisionamento de dispositivos.

8. Clique em **executar** para exibir eventos recentes.

9. Se houver resultados, procure `OperationName`, `ResultType`, `ResultSignature`e `ResultDescription` (mensagem de erro) para obter mais detalhes sobre o erro.


## <a name="common-error-codes"></a>Códigos de erro comuns
Use esta tabela para entender e resolver erros comuns.

| Código de Erro| Descrição | Código de Estado de HTTP |
|-------|------------|------------|
| 400 | O corpo da solicitação não é válido; por exemplo, ele não pode ser analisado ou o objeto não pode ser validado.| 400 formato inadequado |
| 401 | O token de autorização não pode ser validado; por exemplo, ele está expirado ou não se aplica ao URI da solicitação. Esse código de erro também é retornado para dispositivos como parte do fluxo de atestado do TPM. | 401 não autorizado|
| 404 | A instância do serviço de provisionamento de dispositivos ou um recurso (por exemplo, um registro) não existe. |404 Não Encontrado |
| 412 | A ETag na solicitação não corresponde à ETag do recurso existente, de acordo com o RFC7232. | 412 falha na pré-condição |
| 429 | As operações estão sendo limitadas pelo serviço. Para limites de serviço específicos, consulte [limites do serviço de provisionamento de dispositivos no Hub IOT](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#iot-hub-device-provisioning-service-limits). | 429 número excessivo de solicitações |
| 500 | Ocorreu um erro interno. | 500 Erro de Servidor Interno|
