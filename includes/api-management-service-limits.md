---
title: incluir ficheiro
description: incluir ficheiro
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238143"
---
| Resource | Limite |
| --- | --- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho da cache | 5 GB por unidade<sup>2</sup> |
| Ligações simultâneas de back-end<sup>3</sup> por uma autoridade HTTP | 2.048 por unidade<sup>4</sup> |
| Tamanho máximo da resposta em cache | 2 MB |
| Tamanho do documento de política máximo | 256 KB<sup>5</sup> | 
| Domínios de gateway personalizada máximo por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de AC por instância de serviço | 10 | 
| Número máximo de instâncias de serviço por assinatura<sup>7</sup> | 20 | 
| Número máximo de subscrições por instância de serviço<sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>7</sup> | 50 | 
| Número máximo de APIs, por instância de serviço<sup>7</sup> | 50 | 
| Número máximo de operações de API por instância de serviço<sup>7</sup> | 1,000 | 
| Duração de pedidos total máxima<sup>7</sup> | 30 segundos | 
| Tamanho da carga de colocação em memória intermédia máximo<sup>7</sup> | 2 MB | 


<sup>1</sup>limites de dimensionamento dependem do escalão de preço. Para ver os escalões de preço e os respetivos limites de dimensionamento, veja [preços da API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>por cache unidade tamanho depende do escalão de preço. Para ver os escalões de preço e os respetivos limites de dimensionamento, veja [preços da API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>ligações são agrupadas e reutilizadas, a menos que explicitamente fechados pelo back-end.<br/>
<sup>4</sup>este limite é por unidade dos escalões básico, Standard e Premium. O escalão de programador está limitado a 1024. Este limite não se aplica para o escalão de consumo.<br/> 
<sup>5</sup>este limite aplica-se para os escalões Basic, Standard e Premium. Na camada de consumo, o tamanho do documento de política está limitado a 4 KB.<br/>
<sup>6</sup>este recurso está disponível no escalão Premium apenas.<br/>
<sup>7</sup>este recurso aplica-se apenas na camada de consumo.<br/>



