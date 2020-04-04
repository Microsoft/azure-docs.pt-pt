---
title: incluir ficheiro
description: incluir ficheiro
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 5608683578a796ae0fffff7544a749043e8698fd
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656189"
---
| Recurso | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho da cache | 5 GiB por unidade<sup>2</sup> |
| Conexões recorrentes de back-end<sup>3</sup> por autoridade HTTP | 2.048 por unidade<sup>4</sup> |
| Tamanho máximo da resposta em cache | 2 MiB |
| Tamanho máximo do documento de política | 256 KiB<sup>5</sup> |
| Domínios de gateway personalizados máximos por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados ca por instância de serviço | 10 |
| Número máximo de casos de serviço por subscrição<sup>7</sup> | 20 |
| Número máximo de assinaturas por exemplo de serviço<sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>7</sup> | 50 |
| Número máximo de APIs por instância de serviço<sup>7</sup> | 50 |
| Número máximo de operações da API por instância de serviço<sup>7</sup> | 1,000 |
| Duração total máxima do pedido<sup>7</sup> | 30 segundos |
| Tamanho máximo da carga útil tamponada<sup>7</sup> | 2 MiB |
| Tamanho<sup>8</sup> do URL de pedido máximo | 4096 bytes |

<sup>1</sup> Os limites de escala dependem do nível de preços. Para ver os níveis de preços e os seus limites de escala, consulte os preços da [Gestão Da API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> O tamanho do cache por unidade depende do nível de preços. Para ver os níveis de preços e os seus limites de escala, consulte os preços da [Gestão Da API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> As ligações são reunidas e reutilizadas a menos que explicitamente fechadas na parte de trás.<br/>
<sup>4</sup> Este limite é por unidade dos níveis Básico, Standard e Premium. O nível de Desenvolvimento está limitado a 1.024. Este limite não se aplica ao nível de consumo.<br/>
<sup>5</sup> Este limite aplica-se aos níveis Básico, Standard e Premium. No nível de Consumo, o tamanho do documento político é limitado a 16 KiB.<br/>
<sup>6</sup> Este recurso está disponível apenas no nível Premium.<br/>
<sup>7</sup> Este recurso aplica-se apenas ao nível de Consumo.<br/>
<sup>8</sup> Aplica-se apenas ao nível de Consumo. Inclui uma corda de consulta longa até 2048 bytes.<br/>
