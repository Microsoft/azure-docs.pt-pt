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
ms.openlocfilehash: 609eaa25640e74ffe3b39606051edd7048f72497
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251948"
---
| Recurso | Limite |
| --- | --- |
| Número máximo de unidades de escala | 10 por região<sup>1</sup> |
| Tamanho da cache | 5 GB por unidade<sup>2</sup> |
| Ligações de back-end em simultâneo<sup>3</sup> por uma autoridade HTTP | 2048 por unidade<sup>4</sup> |
| Tamanho máximo da resposta em cache | 2MB |
| Tamanho do documento de política máximo | 256KB<sup>5</sup> | 
| Domínios de gateway personalizada máximo por instância de serviço<sup>6</sup> | 20 |
| Número máximo de certificados de AC por instância de serviço | 10 | 
| Número máximo de instâncias de serviço por assinatura<sup>7</sup> | 20 | 
| Número máximo de subscrições por instância de serviço<sup>7</sup> | 500 |
| Número máximo de certificados de cliente por instância de serviço<sup>7</sup> | 50 | 
| Número máximo de APIs, por instância de serviço<sup>7</sup> | 50 | 
| Número máximo de operações de API por instância de serviço<sup>7</sup> | 1000 | 
| Duração de pedidos total máxima<sup>7</sup> | 30 segundos | 
| Tamanho da carga de colocação em memória intermédia máximo<sup>7</sup> | 2MB | 


<sup>1</sup> limites de dimensionamento dependem do escalão de preço. Para ver os preços camadas e os respetivos limites de dimensionamento aceda a [preços da gestão de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> por cache unidade tamanho depende do escalão de preço. Para ver os preços camadas e os respetivos limites de dimensionamento aceda a [preços da gestão de API](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> ligações são agrupadas e utilizadas voltar, a menos que explicitamente fechada pelo back-end.<br/>
<sup>4</sup> por unidade dos escalões básico, Standard e Premium. O escalão de programador está limitado a 1024. Não se aplica para o escalão de consumo.<br/> 
<sup>5</sup> escalões no básico, Standard e Premium. O consumo de tamanho de escalão de documento de política está limitado a 4KB.<br/>
<sup>6</sup> disponível no escalão Premium apenas.<br/>
<sup>7</sup> aplica-se apenas na camada de consumo.<br/>



