---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76044924"
---
A tabela seguinte descreve as funções disponíveis nas Gémeas Digitais Azure:

| **Função** | **Descrição** | **Identificador** |
| --- | --- | --- |
| Administrador espacial | *CRIAR,* *LER,* *ATUALIZAR*e *ELIMINAR* a permissão para o espaço especificado e todos os nós por baixo. Permissão global. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrador de Utilizadores| *CRIAR,* *LER,* *ATUALIZAR*e *ELIMINAR* a permissão para utilizadores e objetos relacionados com o utilizador. *LEIA* a permissão para espaços. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrador de Dispositivos | *CRIAR,* *LER,* *ATUALIZAR*e *ELIMINAR* a permissão para dispositivos e objetos relacionados com dispositivos. *LEIA* a permissão para espaços. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Administrador chave | *CRIAR,* *LER,* *ATUALIZAR*e *ELIMINAR* a permissão para as teclas de acesso. *LEIA* a permissão para espaços. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrador de Fichas |  *LEIA* e *ATUALIZE* a permissão para as teclas de acesso. *LEIA* a permissão para espaços. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Utilizador |  *LEIA* a permissão para espaços, sensores e utilizadores, que inclui os seus objetos relacionados correspondentes. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Especialista em Apoio |  *LEIA* a permissão para tudo, menos as chaves de acesso. | 6e46958b-dc62-4e7c-990c-c3da2e30969 |
| Instalador de dispositivos | *LEIA* e *ATUALIZAÇÃO* a permissão para dispositivos e sensores, que inclui os seus objetos relacionados correspondentes. *LEIA* a permissão para espaços. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Dispositivo Gateway | *Criar* permissão para sensores. *LEIA* a permissão para dispositivos e sensores, que inclui os seus objetos relacionados correspondentes. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |