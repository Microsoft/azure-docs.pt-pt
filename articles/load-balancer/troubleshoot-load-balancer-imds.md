---
title: Códigos de erro comuns para o Serviço de Metadados de Instância Azure (IMDS)
titleSuffix: Azure Load Balancer
description: Visão geral dos códigos de erro comuns e dos métodos de mitigação correspondentes para o Serviço de Metadados de Instância Azure (IMDS)
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: troubleshooting
ms.date: 02/12/2021
ms.author: allensu
ms.openlocfilehash: e932e211996a05b2740613381735a7de3492e5bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "100519189"
---
# <a name="error-codes-common-error-codes-when-using-imds-to-retrieve-load-balancer-information"></a>Códigos de erro: Códigos de erro comuns ao utilizar o IMDS para recuperar informações do balançador de carga

Este artigo descreve erros de implementação comuns e como resolver esses erros durante a utilização do Serviço de Metadados de Instância Azure (IMDS).

## <a name="error-codes"></a>Códigos de erro

| Código de erro | Mensagem de erro | Detalhes e mitigação |
| --- | ---------- | ----------------- |
| 400 | Faltando o parâmetro necessário " \<ParameterName> ". Por favor, corrija o pedido e redaça. | O código de erro indica um parâmetro em falta. </br> Para obter mais informações sobre a adição do parâmetro em falta, consulte [como recuperar metadados do balanceador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).
| 400 | O valor do parâmetro não é permitido, ou o valor do parâmetro \<ParameterValue> " " não é permitido para o parâmetro "Nome de parâmetro". Por favor, corrija o pedido e redaça. | O código de erro indica que o formato de pedido não está configurado corretamente. </br> Para obter mais informações, consulte [como recuperar metadados do balanceador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) para corrigir o corpo de pedido e emitir uma nova agem. |
| 400 | Pedido inesperado. Verifique os parâmetros de consulta e volte a tentar. | O código de erro indica que o formato de pedido não está configurado corretamente. </br> Para obter mais informações, consulte [como recuperar metadados do balanceador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) para corrigir o corpo de pedido e emitir uma nova agem. |
| 404 | Não são encontrados metadados do balançador de carga. Verifique se o seu VM está a utilizar algum balanceador de carga SKU não básico e volte a tentar mais tarde. | O código de erro indica que a sua máquina virtual não está associada a um equilibrador de carga ou que o equilibrador de carga é SKU básico em vez de padrão. </br> Para obter mais informações, consulte [Quickstart: Crie um equilibrador de carga público para carregar vMs de equilíbrio utilizando o portal Azure](quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard) para implantar um equilibrador de carga padrão.|
| 404 | API não é encontrada: Caminho = " \<UrlPath> método = \<Method> " | O código de erro indica uma configuração errada do caminho. </br> Para obter mais informações, consulte [como recuperar metadados do balanceador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response) para corrigir o corpo de pedido e emitir uma nova agem.|
| 405 | O método http não é permitido: Caminho = " \<UrlPath> método = \<Method> " | O código de erro indica um verbo HTTP não suportado. </br> Para obter mais informações, consulte [o Serviço de Metadados de Exemplo de Azure (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#http-verbs) para verbos suportados. |
| 429 | Muitos pedidos | O código de erro indica um limite de taxa. </br> Para obter mais informações sobre a limitação da taxa, consulte [o Serviço de Metadados de Instância Azure (IMDS)](../virtual-machines/windows/instance-metadata-service.md?tabs=windows#rate-limiting).|
| 400 | O corpo de pedido é maior que MaxBodyLength: ... | O código de erro indica um pedido maior do que o MaxBodyLength. </br> Para obter mais informações sobre o comprimento do corpo, consulte [como recuperar metadados do balançador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | O comprimento da chave do parâmetro é maior do que maxparameterKeyLength: ... | O código de erro indica um comprimento da chave de parâmetro maior do que o comprimento MaxParameterKeyL. </br> Para obter mais informações sobre o comprimento do corpo, consulte [como recuperar metadados do balançador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response). |
| 400 | O comprimento do valor do parâmetro é maior do que maxparameterValueLength: ... | O código de erro indica um comprimento da chave de parâmetro maior do que o comprimento MaxParameterValueL. </br> Para obter mais informações sobre o comprimento do valor, consulte [como recuperar metadados do balançador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 400 | O comprimento do valor do cabeçalho do parâmetro é maior do que MaxHeaderValueLength: ... | O código de erro indica um comprimento de valor do cabeçalho de parâmetro maior do que o comprimento MaxHeaderValueL. </br> Para obter mais informações sobre o comprimento do valor, consulte [como recuperar metadados do balançador de carga utilizando o Serviço de Metadados de Instância Azure (IMDS)](howto-load-balancer-imds.md#sample-request-and-response).|
| 404 | A Load Balancer Metadas API não está disponível neste momento. Por favor, reda o redando mais tarde. | O código de erro indica que a API pode estar a prever. Tente o seu pedido mais tarde. |
| 404 | /Metadados/loadbalancer não está disponível atualmente | O código de erro indica que a API está em curso de ativação. Tente o seu pedido mais tarde. |
| 503 | Serviço interno indisponível. Por favor, reda o redando mais tarde.  | O código de erro indica que a API está temporariamente indisponível. Tente o seu pedido mais tarde. |
|  |  |

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre [o Serviço de Metadados Azure Instance](../virtual-machines/windows/instance-metadata-service.md)

