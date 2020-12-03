---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: ac91ef5a56fe234cba47b430b78e74ce81c9d504
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96537081"
---
O Azure Storage fornece as seguintes métricas de transação no Azure Monitor.

| Métrica | Descrição |
| ------------------- | ----------------- |
| Transações | O número de pedidos feitos a um serviço de armazenamento ou a uma operação de API especificada. Este número inclui pedidos com e sem êxito, bem como pedidos que produziram erros. <br/><br/> Unidade: Contagem <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: ResponseType, GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions)<br/> Exemplo de valor: 1024 |
| Entrada | A quantidade de dados de entrada. Este número inclui a entrada de um cliente externo no Armazenamento do Azure, assim como a entrada no Azure. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| Saída | A quantidade de dados de saída. Este número inclui a saída de um cliente externo no Armazenamento do Azure, assim como a saída no Azure. Como resultado, este número não reflete a saída faturável. <br/><br/> Unidade: Bytes <br/> Tipo de agregação: Total <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| SucessoServerLatency | O tempo médio utilizado para processar um pedido com êxito pelo Armazenamento do Azure. Este valor não inclui a latência de rede especificada em SuccessE2ELatency. <br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| SucessoE2ELatency | A latência de ponto a ponto média de pedidos com êxito feitos a um serviço de armazenamento ou a uma operação de API especificada. Este valor inclui o tempo de processamento necessário no Armazenamento do Azure para ler o pedido, enviar a resposta e receber confirmação da resposta. A diferença entre os valores successE2ELatency e SuccessServerLatency é a latência provavelmente causada pela rede e pelo cliente.<br/><br/> Unidade: Milissegundos <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 1024 |
| Disponibilidade | A percentagem de disponibilidade para o serviço de armazenamento ou a operação API especificada. A disponibilidade é calculada ao dividir o valor do total dos pedidos faturáveis pelo número de pedidos aplicáveis, incluindo os que produziram erros inesperados. Todos os erros inesperados resultam numa disponibilidade reduzida para o serviço de armazenamento ou para a operação de API especificada. <br/><br/> Unidade: Por cento <br/> Tipo de agregação: Média <br/> Dimensões aplicáveis: GeoType, ApiName e Autenticação[(Definição)](#metrics-dimensions) <br/> Exemplo de valor: 99.99 |
