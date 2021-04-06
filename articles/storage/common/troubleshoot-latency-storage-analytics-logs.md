---
title: Resolver problemas de latência com os registos da Análise de Armazenamento
description: Identifique e resolva problemas de latência utilizando registos analíticos do Azure Storage e otimize a aplicação do cliente.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221113"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Resolver problemas de latência com os registos da Análise de Armazenamento

O diagnóstico e a resolução de problemas é uma habilidade fundamental para construir e apoiar aplicações de clientes com armazenamento Azure.

Devido à natureza distribuída de uma aplicação Azure, diagnosticar e resolver problemas tanto os erros como as questões de desempenho podem ser mais complexos do que nos ambientes tradicionais.

Os seguintes passos demonstram como identificar e resolver problemas de latência usando registos analíticos de armazenamento Azure e otimizar a aplicação do cliente.

## <a name="recommended-steps"></a>Passos recomendados

1. Descarregue os [registos Storage Analytics](./manage-storage-analytics-logs.md#download-storage-logging-log-data).

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

3. O script lançará uma janela GUI onde pode filtrar as informações por colunas, como mostrado abaixo.

   ![Janela de parser de log analítico de armazenamento](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. Reduza as entradas de registo com base em "tipo de funcionamento", e procure a entrada de registo criada durante o período de tempo do problema.

   ![Entradas de registo tipo de operação](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. Durante o período em que ocorreu a emissão, os seguintes valores são importantes:

   * Tipo de operação = GetBlob
   * estado de pedido = SASNetworkError
   * Fim-a-fim-Latência-In-Ms = 8453
   * Servidor-Latência-In-Ms = 391

   A latência de ponta a ponta é calculada utilizando a seguinte equação:

   * Latência de ponta a ponta = Server-Latency + Latência do Cliente

   Calcular a Latência do Cliente utilizando a entrada de registo:

   * Latência do Cliente = Latência de ponta a ponta – Server-Latency

        Exemplo: 8453 - 391 = 8062ms

   A tabela a seguir fornece informações sobre os resultados da Operação De Alta LatênciaType e RequestStatus:

   | Tipo Blob |PedidoStatus=<br>Com êxito|PedidoStatus=<br>(SAS) NetworkError|Recomendação|
   |---|---|---|---|
   |GetBlob|Yes|No|[**Operação GetBlob:** RequestStatus = Sucesso](#getblob-operation-requeststatus--success)|
   |GetBlob|No|Yes|[**Operação GetBlob:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|Yes|No|[**Colocar operação:** RequestStatus = Sucesso](#put-operation-requeststatus--success)|
   |PutBlob|No|Yes|[**Colocar operação:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>Resultados do estado

### <a name="getblob-operation-requeststatus--success"></a>Operação GetBlob: RequestStatus = Sucesso

Verifique os seguintes valores mencionados no passo 5 da secção "Etapas Recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Numa **Operação GetBlob** com **RequestStatus = Sucesso**, se **o tempo max** for gasto em **Cliente-Latência**, isto indica que o Azure Storage está a gastar um grande volume de tempo a escrever dados ao cliente. Este atraso indica uma questão de Client-Side.

**Recomendação:**

* Investigue o código no seu cliente.
* Utilize o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>Operação GetBlob: RequestStatus = (SAS)NetworkError

Verifique os seguintes valores mencionados no passo 5 da secção "Etapas Recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Numa **Operação GetBlob** com **RequestStatus = (SAS)NetworkError**, se o **tempo máximo** for gasto em **Cliente-Latência**, a questão mais comum é que o cliente está a desligar-se antes de expirar um intervalo no serviço de armazenamento.

**Recomendação:**

* Investigue o código no seu cliente para perceber porquê e quando o cliente se desliga do serviço de armazenamento.
* Utilize o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--success"></a>Operação Put: RequestStatus = Sucesso

Verifique os seguintes valores mencionados no passo 5 da secção "Etapas Recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Numa **Operação Put** with **RequestStatus = Sucesso**, se o tempo **máximo** for gasto em **Cliente-Latência,** isto indica que o Cliente está a demorar mais tempo a enviar dados para o Azure Storage. Este atraso indica uma questão de Client-Side.

**Recomendação:**

* Investigue o código no seu cliente.
* Utilize o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente. 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Operação: RequestStatus = (SAS)NetworkError

Verifique os seguintes valores mencionados no passo 5 da secção "Etapas Recomendadas":

* Latência de ponta a ponta
* Server-Latency
* Client-Latency

Numa **Operação PutBlob** com **RequestStatus = (SAS)NetworkError**, se o **tempo máximo** for gasto em **Cliente-Latência**, a questão mais comum é que o cliente está a desligar-se antes de expirar um intervalo no serviço de armazenamento.

**Recomendação:**

* Investigue o código no seu cliente para perceber porquê e quando o cliente se desliga do serviço de armazenamento.
* Utilize o Wireshark, o Microsoft Message Analyzer ou o Tcping para investigar problemas de conectividade de rede do cliente.