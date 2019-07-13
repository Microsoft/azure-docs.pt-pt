---
title: Referência de domínio pré-concebida
titleSuffix: Azure
description: Referência para os domínios criados previamente, que são coleções pré-criados de objetivos e entidades do Language Understanding Intelligent Service (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: b615185472ede25fd6ceef9cc3e7325375e16ac7
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846671"
---
# <a name="prebuilt-domain-reference-for-your-luis-app"></a>Referência de domínio pré-criado para a sua aplicação LUIS
Esta referência disponibiliza informações sobre o [domínios pré-concebidos](luis-how-to-use-prebuilt-domains.md), que são coleções pré-criados de objetivos e entidades que oferece o LUIS.

[Domínios personalizados](luis-how-to-start-new-app.md), por outro lado, começar com nenhum intenções e modelos. Pode adicionar qualquer intenções de domínio pré-criado e entidades para um modelo personalizado.

# <a name="supported-domains-across-cultures"></a>Domínios suportados entre culturas

A cultura de suporte única é o inglês. 

<!--

The table below summarizes the currently supported domains. Support for English is usually more complete than others.


| Entity Type       | EN-US      | ZH-CN   | DE    | FR     | ES    | IT      | PT-BR |  JP  |      KO |        NL |    TR |
|:-----------------:|:-------:|:-------:|:-----:|:------:|:-----:|:-------:| :-------:| :-------:| :-------:| :-------:|  :-------:| 
| Calendar    | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -      | -    | -    | -     | -  |
| Communication   | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Email           | ✓    | ✓       | ✓   | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| HomeAutomation           | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Notes      | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| Places    | ✓    | -       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| RestaurantReservation   | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo     | ✓    | ✓       | ✓    | ✓     | ✓     | ✓  | -  | -      | -    | -    | -     | -  |
| ToDo_IPA        | ✓    | ✓       | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Utilities          | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Weather        | ✓    | ✓        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
| Web    | ✓    | -        | ✓    | ✓      | ✓     | ✓       | -  | -      | -    | -    | -     | -  |
||||||||||||| 

-->

<br><br>

|Tipo de entidade|description|
|--|--|
|Calendário|Calendário é qualquer coisa sobre reuniões pessoais e de compromissos, _não_ eventos públicos (por exemplo, o mundo cup agendas, calendários de eventos de Seattle) ou calendários genéricos (por exemplo, que dia é hoje em dia, o que Outono começar, quando é dia de trabalho).|
|Comunicação|Pedidos para fazer chamadas, envie textos ou mensagens instantâneas, localize e adicione a contactos e vários outros pedidos relacionados com a comunicação (geralmente de saída). Consultas de nome de contato único não pertencem ao domínio de comunicação.|
|Email|O e-mail é um subdomínio do domínio de comunicação. Ela contém principalmente pedidos para enviar e receber mensagens através de e-mails.|
|HomeAutomation|O domínio de HomeAutomation fornece intenções e entidades relacionadas para controlar os dispositivos de casa inteligentes. Principalmente suporta o comando de controle relacionados com luzes e ar-condicionado, mas tem algumas capacidades de generalização para outras aplicações electric.|
|Notas|Domínio de nota fornece intenções e entidades para criar notas e escrever itens para os utilizadores.|
|Locais|Locais incluem as empresas, instituições, restaurantes, espaços públicos e endereços. O domínio suporta local localizar e perguntar sobre as informações de local público, tais como localidade, horas e a distância a funcionar.|
|RestaurantReservation|Domínio de reserva do restaurante suporta objetivos para a manipulação de reservas de restaurantes.|
|ToDo|ToDo domínio fornece tipos de listas de tarefas para os utilizadores adicionar, marcar e eliminar os seus itens de afazeres.|
|Serviços Públicos|Domínio de utilitários é um domínio geral entre todos os LUIS modelos pré-construídos que contém expressões em cenários de diferença e objetivos comuns.|
|Meteorologia|Domínio de Meteorologia enfoca a verificação da condição meteorológica e consultorias com localização e a hora ou a verificação de tempo por condições meteorológicas.|
|Web|O domínio da Web fornece a intenção e as entidades de pesquisa para um Web site.|
