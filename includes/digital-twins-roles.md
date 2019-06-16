---
title: incluir ficheiro
description: incluir ficheiro
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 12/20/2018
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: 7e4760990229433b2ea40fadd0d17de0b52fcb36
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66162104"
---
A tabela seguinte descreve as funções que estão disponíveis no duplos Digital do Azure:

| **Função** | **Descrição** | **Identificador** |
| --- | --- | --- |
| Administrador de espaço | *CRIE*, *ler*, *ATUALIZAÇÃO*, e *eliminar* permissão para o espaço especificado e todos os nós por baixo. Permissão global. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Administrador de utilizadores| *CRIE*, *ler*, *ATUALIZAÇÃO*, e *eliminar* permissão para os utilizadores e os objetos relacionadas ao usuário. *LER* permissão para espaços. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Administrador de dispositivos | *CRIE*, *ler*, *ATUALIZAÇÃO*, e *eliminar* permissão para dispositivos e os objetos relacionados com o dispositivo. *LER* permissão para espaços. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Chave de administrador | *CRIE*, *ler*, *ATUALIZAÇÃO*, e *eliminar* permissão para chaves de acesso. *LER* permissão para espaços. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Administrador de token |  *Leia* e *ATUALIZAÇÃO* permissão para chaves de acesso. *LER* permissão para espaços. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Utilizador |  *LER* objetos relacionados com permissão para espaços, sensores e utilizadores, que inclui suas correspondente. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Especialista de suporte |  *LER* permissão para tudo, exceto as chaves de acesso. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalador do dispositivo | *Leia* e *ATUALIZAÇÃO* objetos relacionados com permissão para dispositivos e sensores, que inclui suas correspondente. *LER* permissão para espaços. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Dispositivo de gateway | *CRIAR* permissão para sensores. *LER* objetos relacionados com permissão para dispositivos e sensores, que inclui suas correspondente. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |