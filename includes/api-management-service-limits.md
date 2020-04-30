---
title: incluir ficheiro
description: incluir ficheiro
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 072f13f5a0884cf95fe760e17ff0d770111f4da0
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204498"
---
| Recurso | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho da cache | 5 GiB por unidade<sup>2</sup> |
| Conexões recorrentes de back-end<sup>3</sup> por autoridade HTTP | 2.048 por unidade<sup>4</sup> |
| Tamanho máximo da resposta em cache | 2 MiB |
| Tamanho máximo do documento de política | 256 KiB<sup>5</sup> |
| Domínios de gateway personalizados máximos por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados ca por instância de serviço<sup>7</sup> | 10 |
| Número máximo de casos de serviço por subscrição<sup>8</sup> | 20 |
| Número máximo de assinaturas por exemplo de serviço<sup>8</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>8</sup> | 50 |
| Número máximo de APIs por instância de serviço<sup>8</sup> | 50 |
| Número máximo de operações da API por instância de serviço<sup>8</sup> | 1,000 |
| Duração total total do pedido<sup>8</sup> | 30 segundos |
| Tamanho máximo da carga útil tamponada<sup>8</sup> | 2 MiB |
| Tamanho<sup>9</sup> de URL de pedido máximo | 4096 bytes |
| Número máximo de gateways auto-hospedados<sup>10</sup> | 25 |

<sup>1</sup> Os limites de escala dependem do nível de preços. Para obter mais informações sobre os níveis de preços e os seus limites de escala, consulte os preços da [Gestão DaAPi](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> O tamanho do cache por unidade depende do nível de preços. Para ver os níveis de preços e os seus limites de escala, consulte os preços da [Gestão Da API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> As ligações são reunidas e reutilizadas a menos que explicitamente fechadas na parte de trás.<br/>
<sup>4</sup> Este limite é por unidade dos níveis Básico, Standard e Premium. O nível de Desenvolvimento está limitado a 1.024. Este limite não se aplica ao nível de consumo.<br/>
<sup>5</sup> Este limite aplica-se aos níveis Básico, Standard e Premium. No nível de Consumo, o tamanho do documento político é limitado a 4 KiB.<br/>
<sup>6</sup> Vários domínios personalizados são suportados apenas nos níveis Developer e Premium.<br/>
<sup>7</sup> Os certificados CA não são suportados no nível de consumo.<br/>
<sup>8</sup> Este recurso aplica-se apenas ao nível de Consumo.<br/>
<sup>9</sup> Aplica-se apenas ao nível de Consumo. Inclui uma corda de consulta longa até 2048 bytes.<br/>
<sup>10</sup> Os gateways auto-hospedados são suportados apenas nos níveis Developer e Premium. O limite aplica-se ao número de recursos de [gateway auto-alojados.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway) Para aumentar este limite, contacte o [suporte](https://azure.microsoft.com/support/options/). Note-se que o número de nós (ou réplicas) associados a um recurso de gateway auto-hospedado é ilimitado no nível Premium e limitado a um único nó no nível de Desenvolvimento.
