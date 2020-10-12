---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 04/14/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 9ed918c841989cb50efa362251d09c0b655b7fc4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89570231"
---
| Recurso | Limite |
| ---------------------------------------------------------------------- | -------------------------- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho da cache | 5 GiB por unidade<sup>2</sup> |
| Ligações de back-end simultâneas<sup>3</sup> por autoridade HTTP | 2.048 por unidade<sup>4</sup> |
| Tamanho máximo de resposta em cache | 2 MiB |
| Tamanho máximo do documento de política | 256 KiB<sup>5</sup> |
| Domínios de gateway personalizados máximos por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de CA por instância de serviço<sup>7</sup> | 10 |
| Número máximo de casos de serviço por subscrição<sup>8</sup> | 20 |
| Número máximo de subscrições por instância de serviço<sup>8</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>8</sup> | 50 |
| Número máximo de APIs por instância de serviço<sup>8</sup> | 50 |
| Número máximo de operações de API por instância de serviço<sup>8</sup> | 1,000 |
| Duração máxima do pedido<sup>8</sup> | 30 segundos |
| Tamanho máximo de carga tamponada<sup>8</sup> | 2 MiB |
| Pedido máximo URL tamanho<sup>9</sup> | 4096 bytes |
| Número máximo de gateways auto-hospedados<sup>10</sup> | 25 |

<sup>1</sup> Os limites de escala dependem do nível de preços. Para obter mais informações sobre os níveis de preços e os seus limites de escala, consulte [os preços da API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Por unidade, o tamanho da cache depende do nível de preços. Para ver os níveis de preços e os seus limites de escala, consulte [os preços da API Management](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> As ligações são agrizadas e reutilizadas, a menos que esteja explicitamente fechada na parte de trás.<br/>
<sup>4</sup> Este limite é por unidade dos escalões Básico, Standard e Premium. O nível de Desenvolvedor está limitado a 1.024. Este limite não se aplica ao nível de Consumo.<br/>
<sup>5</sup> Este limite aplica-se aos níveis Básico, Standard e Premium. No nível de consumo, o tamanho do documento de política é limitado a 4 KiB.<br/>
<sup>6</sup> Vários domínios personalizados são suportados apenas nos níveis Developer e Premium.<br/>
<sup>7</sup> Os certificados de CA não são suportados no nível de Consumo.<br/>
<sup>8</sup> Este limite aplica-se apenas ao nível de Consumo. Não há limites nestas categorias para outros níveis.<br/>
<sup>9</sup> Aplica-se apenas ao nível de consumo. Inclui uma cadeia de consultas até 2048.<br/>
<sup>10</sup> Gateways auto-hospedados são suportados apenas nos níveis Developer e Premium. O limite aplica-se ao número de [recursos de gateway auto-hospedados.](https://docs.microsoft.com/rest/api/apimanagement/2019-12-01/gateway) Para elevar este limite contacte [o suporte.](https://azure.microsoft.com/support/options/) Note-se que o número de nós (ou réplicas) associados a um recurso de gateway auto-hospedado é ilimitado no nível Premium e limitado a um único nó no nível de Desenvolvedor.
