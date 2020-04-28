---
title: Resolver problemas de latência com os registos da Análise de Armazenamento
description: Identifique e problemas de latência utilizando registos analíticos de armazenamento Azure e otimize a aplicação do cliente.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 2197a149235c0dca98a24a57549538b2a4cbb1c8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "74196513"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Resolver problemas de latência com os registos da Análise de Armazenamento

Diagnosticar e resolver problemas é uma habilidade fundamental para construir e apoiar aplicações de clientes com o Armazenamento Azure.

Devido à natureza distribuída de uma aplicação Azure, diagnosticar e resolver problemas tanto de erros como de problemas de desempenho pode ser mais complexo do que em ambientes tradicionais.

Os seguintes passos demonstram como identificar e resolver problemas de latência usando registos analíticos de armazenamento azure, e otimizar a aplicação do cliente.

## <a name="recommended-steps"></a>Passos recomendados

1. Descarregue os [registos do Storage Analytics.](https://docs.microsoft.com/azure/storage/common/storage-analytics-logging#download-storage-logging-log-data)

2. Utilize o seguinte script PowerShell para converter os registos de formato bruto em formato tabular:

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. O script lançará uma janela GUI onde pode filtrar a informação por colunas, como mostrado abaixo.

   ![Janela de parser de log analítico de armazenamento](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Reduza as entradas de registo com base no "tipo de funcionamento", e procure a entrada de registo criada durante o período de tempo da emissão.

   ![Entradas de registo do tipo de funcionamento](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Durante o momento em que a questão ocorreu, os seguintes valores são importantes:

   * Tipo de funcionamento = GetBlob
   * estado de pedido = SASNetworkError
   * Fim-a-fim-latência-in-Ms = 8453
   * Server-Latência-In-Ms = 391

   A latência de ponta a ponta é calculada utilizando a seguinte equação:

   * Latência de ponta a ponta = Latência do Servidor + Latência do Cliente

   Calcular a Latência do Cliente utilizando a entrada de registo:

   * Latência do Cliente = Latência De ponta a ponta – Latência do Servidor

          * Example: 8453 – 391 = 8062ms

   O quadro seguinte fornece informações sobre os resultados do OperationType e do RequestStatus de alta latência:

   |   |RequestStatus=<br>Êxito|RequestStatus=<br>(SAS) Error de rede|Recomendação|
   |---|---|---|---|
   |GetBlob|Sim|Não|[**Operação GetBlob:** RequestStatus = Sucesso](#getblob-operation-requeststatus--success)|
   |GetBlob|Não|Sim|[**Operação GetBlob:** RequestStatus = (SAS)Error networkerror](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Sim|Não|[**Colocação Operação:** RequestStatus = Sucesso](#put-operation-requeststatus--success)|
   |PutBlob|Não|Sim|[**Colocação Operação:** RequestStatus = (SAS)Error networkerror](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Resultados do estado

### <a name="getblob-operation-requeststatus--success"></a>Operação GetBlob: RequestStatus = Sucesso

Verifique os seguintes valores referidos na etapa 5 da secção "Passos Recomendados":

* Latência de ponta a ponta
* Latência do Servidor
* Latência de Cliente

Numa **Operação GetBlob** com Estatuto de **Pedido = Sucesso**, se o Tempo **Max** for gasto em **Cliente-Latência,** isto indica que o Armazenamento Azure está a gastar um grande volume de tempo a escrever dados ao cliente. Este atraso indica um problema do lado do cliente.

**Recomendação:**

* Investigue o código no seu cliente.
* Utilize wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operação GetBlob: RequestStatus = (SAS)Error de rede

Verifique os seguintes valores referidos na etapa 5 da secção "Passos Recomendados":

* Latência de ponta a ponta
* Latência do Servidor
* Latência de Cliente

Numa **Operação GetBlob** com **RequestStatus = (SAS)NetworkError**, se o **Tempo Max** for gasto em **Cliente-Latência**, a questão mais comum é que o cliente está a desligar-se antes de expirar um prazo no serviço de armazenamento.

**Recomendação:**

* Investigue o código no seu cliente para perceber porquê e quando o cliente se desliga do serviço de armazenamento.
* Utilize wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--success"></a>Colocação de Funcionamento: RequestStatus = Sucesso

Verifique os seguintes valores referidos na etapa 5 da secção "Passos Recomendados":

* Latência de ponta a ponta
* Latência do Servidor
* Latência de Cliente

Numa **Operação De Put** com Estatuto de **Pedido = Sucesso**, se o Tempo **Max** for gasto em **Cliente-Latência**, isto indica que o Cliente está a demorar mais tempo a enviar dados para o Armazenamento Do Azure. Este atraso indica um problema do lado do cliente.

**Recomendação:**

* Investigue o código no seu cliente.
* Utilize wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Colocação de Funcionamento: RequestStatus = (SAS)Error de rede

Verifique os seguintes valores referidos na etapa 5 da secção "Passos Recomendados":

* Latência de ponta a ponta
* Latência do Servidor
* Latência de Cliente

Numa **Operação PutBlob** com **RequestStatus = (SAS)NetworkError**, se o **Tempo Max** for gasto em **Iatência de Clientes**, a questão mais comum é que o cliente está a desligar-se antes de expirar um prazo no serviço de armazenamento.

**Recomendação:**

* Investigue o código no seu cliente para perceber porquê e quando o cliente se desliga do serviço de armazenamento.
* Utilize wireshark, Microsoft Message Analyzer ou Tcping para investigar problemas de conectividade de rede do cliente.

