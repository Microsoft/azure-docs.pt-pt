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
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67184662"
---
| Resource | Limite |
| --- | --- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho da cache | 5 GB por unidade<sup>2</sup> |
| Conexões de back-end simultâneas<sup>3</sup> por autoridade http | 2\.048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MB |
| Tamanho máximo do documento de política | 256 KB<sup>5</sup> | 
| Máximo de domínios de gateway personalizados por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de autoridade de certificação por instância de serviço | 10 | 
| Número máximo de instâncias de serviço por assinatura<sup>7</sup> | 20 | 
| Número máximo de assinaturas por instância de serviço<sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>7</sup> | 50 | 
| Número máximo de APIs por instância de serviço<sup>7</sup> | 50 | 
| Número máximo de operações de API por instância de serviço<sup>7</sup> | 1,000 | 
| Duração de solicitação total máxima<sup>7</sup> | 30 segundos | 
| Tamanho máximo de carga em buffer<sup>7</sup> | 2 MB | 


<sup>1</sup> Os limites de dimensionamento dependem do tipo de preço. Para ver os tipos de preço e seus limites de dimensionamento, consulte [preços de gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> O tamanho do cache por unidade depende do tipo de preço. Para ver os tipos de preço e seus limites de dimensionamento, consulte [preços de gerenciamento de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> As conexões são agrupadas e reutilizadas, a menos que sejam fechadas explicitamente pelo back-end.<br/>
<sup>4</sup> Esse limite é por unidade das camadas básica, Standard e Premium. A camada de desenvolvedor é limitada a 1.024. Esse limite não se aplica à camada de consumo.<br/> 
<sup>5</sup> Esse limite se aplica às camadas Basic, Standard e Premium. No tipo de consumo, o tamanho do documento de política é limitado a 4 KB.<br/>
<sup>6</sup> Esse recurso está disponível somente na camada Premium.<br/>
<sup>7</sup> Este recurso se aplica somente à camada de consumo.<br/>



