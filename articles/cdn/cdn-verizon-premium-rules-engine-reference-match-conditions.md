---
title: Azure CDN da Verizon Premium regras condições de jogo do motor
description: Documentação de referência para a Rede de Entrega de Conteúdos Azure da Verizon Premium regras condições de correspondência do motor.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: efd6e6a93cd4ca79e6c4b6de69f8514e2d71b252
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "84323319"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN da Verizon Premium regras condições de jogo do motor

Este artigo enumera descrições detalhadas das condições de correspondência disponíveis para a Rede de Entrega de Conteúdos Azure (CDN) do motor de [regras](cdn-verizon-premium-rules-engine.md)Verizon Premium .

A segunda parte de uma regra é a condição do jogo. Uma condição de correspondência identifica tipos específicos de pedidos para os quais será realizado um conjunto de funcionalidades.

Por exemplo, pode utilizar uma condição de correspondência para:

- Filtrar pedidos de conteúdo num determinado local.
- Filtrar pedidos gerados a partir de um determinado endereço IP ou país/região.
- Filtrar os pedidos por informação do cabeçalho.

## <a name="match-conditions"></a><a name="top"></a>Condições de jogo

* [Always](#always) (Sempre)
* [Dispositivo](#device)
* [Localização](#location)
* [Origem](#origin)
* [Pedir](#request)
* [URL](#url)

### <a name="always"></a><a name="always"></a>Sempre

[A condição de correspondência Always](https://docs.vdms.com/cdn/Content/HRE/M/Always.htm) foi concebida para aplicar um conjunto predefinido de funcionalidades a todos os pedidos.

### <a name="device"></a><a name="device"></a>Dispositivo

Estas condições de correspondência destinam-se a identificar pedidos baseados no agente utilizador do cliente.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| Nome da marca | Identifica os pedidos se a marca do dispositivo corresponde a: <br> **-** Valor específico [(Marca Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Literal.htm) <br> **-** Expressão regular [(Marca Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Regex.htm) <br> **-** Padrão específico [(Marca Marca Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Brand-Name-Wildcard.htm) |
| Dispositivo OS | Identifica os pedidos se o sistema operativo corresponde a: <br> **-** Valor específico [(Dispositivo OS Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Literal.htm) <br> **-** Expressão regular [(Dispositivo OS Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Regex.htm) <br> **-** Padrão específico [(Dispositivo OS Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Wildcard.htm) |
| Versão do SO do dispositivo | Identifica os pedidos se a versão OS do dispositivo corresponde a: <br> **-** Valor específico [(Versão DO Dispositivo OS Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Literal.htm) <br> **-** Expressão regular [(Versão do Dispositivo OS Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Regex.htm) <br> **-** Padrão específico [(Versão do Dispositivo OS Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Device-OS-Version-Wildcard.htm) |
| [Orientação dupla?](https://docs.vdms.com/cdn/Content/HRE/M/D-Dual-Orientation.htm) | Identifica os pedidos se o dispositivo suporta a dupla orientação. |
| HTML DTD preferido | Identifica os pedidos se o DTD preferido do dispositivo corresponde a: <br> **-** Valor específico [(HTML Preferred DTD Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Literal.htm) <br> **-** Expressão regular [(HTML Preferred DTD Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Regex.htm) <br> **-** Padrão específico [(HTML Preferred DTD Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-HTML-Preferred-DTD-Wildcard.htm) |
| [Imagem De Inlining?](https://docs.vdms.com/cdn/Content/HRE/M/D-Image-Inlining.htm) | Identifica os pedidos se o dispositivo suporta imagens codificadas base64. |
| [Is Android?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Android.htm) | Identifica os pedidos se o dispositivo utiliza o SISTEMA Android. |
| [A App é?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-App.htm) | Identifica os pedidos por saber se uma aplicação nativa solicitou conteúdo. |
| [É o Ambiente de Trabalho Completo?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Full-Desktop.htm) | Identifica os pedidos se o dispositivo proporciona uma experiência completa no ambiente de trabalho. |
| [O iOS é?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-iOS.htm) | Identifica os pedidos se o dispositivo utiliza o iOS. |
| [O Robot?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Robot.htm) | Identifica os pedidos por se considerar que o dispositivo é considerado um cliente HTTP automatizado (por exemplo, um robot crawler). |
| [É smart TV?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smart-TV.htm) | Identifica os pedidos se o dispositivo é uma smart TV. |
| [Smartphone é smartphone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Smartphone.htm) | Identifica os pedidos se o dispositivo é um smartphone.
| [Is Tablet?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Tablet.htm) | Identifica os pedidos se o dispositivo é um tablet. |
| [É o touchscreen?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Touchscreen.htm) | Identifica os pedidos se o dispositivo de mira primário do dispositivo é um ecrã tátil. |
| [O Windows é o Windows Phone?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Windows-Phone.htm) | Identifica os pedidos se o dispositivo é um Windows Mobile 6.5/Windows Phone 7 ou superior. |
| [O dispositivo sem fios é um dispositivo sem fios?](https://docs.vdms.com/cdn/Content/HRE/M/D-Is-Wireless-Device.htm) | Identifica os pedidos se o dispositivo é sem fios. 
| Nome de Marketing | Identifica os pedidos se o nome de marketing do dispositivo corresponde a: <br> **-** Valor específico [(Marketing Name Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Literal.htm) <br> **-** Expressão regular [(Nome de Marketing Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Regex.htm) <br> **-** Padrão específico [(Marketing Name Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Marketing-Name-Wildcard.htm) |
| Navegador Móvel | Identifica os pedidos se o navegador do dispositivo corresponde a: <br> **-** Valor específico [(Mobile Browser Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Literal.htm) <br> **-** Expressão regular [(Mobile Browser Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Regex.htm) <br> **-** Padrão específico [(Mobile Browser Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Wildcard.htm) |
| Versão do navegador móvel | Identifica os pedidos se a versão do navegador do dispositivo corresponde a: <br> **-** Valor específico [(Versão Móvel Do navegador literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Literal.htm) <br> **-** Expressão regular [(Versão móvel do navegador Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Regex.htm) <br> **-** Padrão específico [(Versão móvel wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Mobile-Browser-Version-Wildcard.htm) |
| Nome do modelo | Identifica os pedidos se o nome do modelo do dispositivo corresponde a: <br> **-** Valor específico [(Nome modelo literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Literal.htm) <br> **-** Expressão regular [(Nome modelo Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Regex.htm) <br> **-** Padrão específico [(Modelo Nome Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Model-Name-Wildcard.htm) |
| [Download Progressivo?](https://docs.vdms.com/cdn/Content/HRE/M/D-Progressive-Download.htm) | Identifica os pedidos se o dispositivo suporta o download progressivo. |
| Data da versão: | Identifica os pedidos se a data de lançamento do dispositivo corresponde a: <br> **-** Valor específico [(Data de Lançamento Literal)](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Literal.htm) <br> **-** Expressão regular [(Data de Lançamento Regex)](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Regex.htm) <br> **-** Padrão específico [(Data de Lançamento Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/D-Release-Date-Wildcard.htm) |
| [Altura da Resolução](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Height.htm) | Identifica os pedidos pela altura do dispositivo. |
| [Largura de Resolução](https://docs.vdms.com/cdn/Content/HRE/M/D-Resolution-Width.htm) | Identifica os pedidos pela largura do dispositivo. |

**[De volta ao Topo](#top)**

### <a name="location"></a><a name="location"></a>Localização

Estas condições de correspondência destinam-se a identificar pedidos baseados na localização do solicitador.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Número AS](https://docs.vdms.com/cdn/Content/HRE/M/AS-Number.htm) | Identifica pedidos originários de uma determinada rede. |
| Nome da cidade | Identifica os pedidos por se tiverem origem numa cidade cujo nome corresponda a: <br> **-** Valor específico [(City Name Literal)](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Literal.htm) <br> **-** Expressão regular [(City Name Regex)](https://docs.vdms.com/cdn/Content/HRE/M/City-Name-Regex.htm) |
| [Continente](https://docs.vdms.com/cdn/Content/HRE/M/Continent.htm) | Identifica os pedidos originários dos continentes especificados. |
| [País](https://docs.vdms.com/cdn/Content/HRE/M/Country.htm) | Identifica os pedidos originários dos países especificados. |
| [Código DMA](https://docs.vdms.com/cdn/Content/HRE/M/DMA-Code.htm) | Identifica os pedidos originários dos metros especificados (Áreas de Mercado Designadas). |
| [Latitude](https://docs.vdms.com/cdn/Content/HRE/M/Latitude.htm) | Identifica os pedidos originários das latitudes especificadas. |
| [Longitude](https://docs.vdms.com/cdn/Content/HRE/M/Longitude.htm) | Identifica pedidos originários das longitudes especificadas. |
| [Código do Metro](https://docs.vdms.com/cdn/Content/HRE/M/Metro-Code.htm) | Identifica os pedidos originários dos metros especificados (Áreas de Mercado Designadas). |
| [Código Postal](https://docs.vdms.com/cdn/Content/HRE/M/Postal-Code.htm) | Identifica os pedidos originários dos códigos postais especificados. |
| [Código da Região](https://docs.vdms.com/cdn/Content/HRE/M/Region-Code.htm) | Identifica os pedidos originários das regiões especificadas. |

> [!NOTE]
> **Devo usar código de metrô ou código DMA?** <br>
Ambas as condições de jogo fornecem a mesma capacidade. No entanto, recomendamos a utilização da condição de correspondência do Código do Metro para identificar os pedidos por DMA.

**[De volta ao Topo](#top)**

### <a name="origin"></a><a name="origin"></a>Origem

Estas condições de correspondência são projetadas para identificar pedidos que apontam para o armazenamento de CDN ou um servidor de origem do cliente.

| Name       | Objetivo                                                           |
|------------|-------------------------------------------------------------------|
| [Origem CDN](https://docs.vdms.com/cdn/Content/HRE/M/CDN-Origin.htm) | Identifica os pedidos de conteúdo armazenados no armazenamento de CDN. |
| [Origem do Cliente](https://docs.vdms.com/cdn/Content/HRE/M/Customer-Origin.htm) | Identifica os pedidos de conteúdo armazenados num servidor específico de origem do cliente. |

**[De volta ao Topo](#top)**

### <a name="request"></a><a name="request"></a>Pedir

Estas condições de correspondência são projetadas para identificar pedidos com base nas suas propriedades.

| Name              | Objetivo                                                                |
|-------------------|------------------------------------------------------------------------|
| [Endereço IP do Cliente](https://docs.vdms.com/cdn/Content/HRE/M/Client-IP-Address.htm) | Identifica pedidos originários de um determinado endereço IP. |
| Parâmetro de cookie  | Identifica um pedido através do facto de conter um cookie que corresponda a: <br> **-** Valor Específico [(Parâmetro de Cookie Literal)](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Literal.htm) <br> **-** Expressão regular [(Cookie Parameter Regex](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Regex.htm) <br> **-** Padrão específico [(Cookie Parameter Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/Cookie-Parameter-Wildcard.htm) |
| [Edge CNAME](https://docs.vdms.com/cdn/Content/HRE/M/Edge-CNAME.htm) | Identifica os pedidos que apontam para uma borda específica CNAME. |
| Domínio de Encaminhamento | Identifica um pedido por se ter sido referido por um nome de anfitrião que corresponda a: <br> **-** Valor específico [(Referir Domínio Literal)](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Literal.htm) <br> **-** Padrão específico [(Cartão de Domínio de Encaminhamento)](https://docs.vdms.com/cdn/Content/HRE/M/Referring-Domain-Wildcard.htm) |
| Cabeçalho do Pedido | Identifica um pedido se contém um cabeçalho que corresponde a: <br> **-** Valor específico [(Cabeçalho de Pedido Literal)](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Literal.htm) <br> **-** Expressão regular [(Pedido Cabeçalho Regex)](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Regex.htm) <br> **-** Padrão específico [(Pedido Cabeçalho Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/Request-Header-Wildcard.htm) |
| [Método de Pedido](https://docs.vdms.com/cdn/Content/HRE/M/Request-Method.htm) | Identifica os pedidos pelo seu método HTTP. |
| [Regime de Pedidos](https://docs.vdms.com/cdn/Content/HRE/M/Request-Scheme.htm) | Identifica os pedidos através do protocolo HTTP. |

**[De volta ao Topo](#top)**

### <a name="url"></a><a name="url"></a>URL

| Name              | Objetivo                                                                |
|-------------------|------------------------------------------------------------------------|
| Caminho do URL | Identifica os pedidos se o seu percurso relativo, incluindo o nome de ficheiro, corresponde a: <br> **-** Valor específico [(URL Path Literal)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Literal.htm) <br> **-** Expressão regular [(URL Path Regex)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Regex.htm) <br> **-** Padrão específico [(URL Path Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Wildcard.htm) |
| Diretório de Caminhos URL | Identifica os pedidos se o seu percurso relativo corresponde a: <br> **-** Valor específico [(URL Path Directory Literal)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Literal.htm) <br> **-** Padrão específico [(URL Path Directory Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Directory-Wildcard.htm) |
| Extensão do caminho url | Identifica os pedidos se a extensão do ficheiro corresponde a: <br> **-** Valor específico [(URL Path Extension Literal)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Literal.htm) <br> **-** Padrão específico [(URL Path Extension Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Extension-Wildcard.htm) |
| Nome de arquivo de caminho URL | Identifica os pedidos se o seu nome de ficheiro corresponde a: <br> **-** Valor específico [(URL Path Filename Literal)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Literal.htm) <br> **-** Padrão específico [(URL Path Filename Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Path-Filename-Wildcard.htm) |
| Consulta de URL | Identifica os pedidos se a sua cadeia de consulta corresponde a: <br> **-** Valor específico [(URL Consulta Literal)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Literal.htm) <br> **-** Expressão regular [(URL Query Regex)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Regex.htm) <br> **-** Padrão específico [(URL Query Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Wildcard.htm) |
| Parâmetro de consulta de URL | Identifica os pedidos se contêm um parâmetro de cadeia de consulta definido para um valor que corresponde a: <br> **-** Valor específico [(URL Consulta Parâmetro Literal)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Literal.htm) <br> **-** Padrão específico [(URL Query Parameter Wildcard)](https://docs.vdms.com/cdn/Content/HRE/M/URL-Query-Parameter-Wildcard.htm) |

**[De volta ao Topo](#top)**

Para as condições de jogo mais recentes, consulte a [documentação](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Conditio)do Verizon Rules Engine .

## <a name="next-steps"></a>Passos seguintes

- [Visão geral da Rede de Entrega de Conteúdos Azure](cdn-overview.md)
- [Referência do motor de regras](cdn-verizon-premium-rules-engine-reference.md)
- [Expressões condicionais do motor de regras](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Funcionalidades do motor de regras](cdn-verizon-premium-rules-engine-reference-features.md)
- [Comportamento HTTP predefinido que predefinindo usando o motor de regras](cdn-verizon-premium-rules-engine.md)
