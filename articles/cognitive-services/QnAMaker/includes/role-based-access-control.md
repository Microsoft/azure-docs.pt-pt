---
title: incluir ficheiro
description: incluir ficheiro
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665951"
---
São fornecidas as seguintes funções para a colaboração:

|Função|Funcionalidades|Acesso API|Permissões de API|
|--|--|--|--|
|Proprietário|Todos|Chave de Autenticação|Todos|
|Contribuinte|Tudo menos a capacidade de adicionar novos membros aos papéis|Chave de Autenticação|Tudo menos a capacidade de adicionar novos membros aos papéis|
|QnA Maker Read<br>(ler)|Exportação/Download<br>Testar|Símbolo do portador|1. Baixar KB API<br>2. Lista KBs para a API do utilizador<br>3. Obter detalhes da base de conhecimento<br>4. Alterações de descarregamento<br>Gerar Resposta |
|Editor de Criador da QnA<br>(ler/escrever)|Exportação/Download<br>Testar<br>Atualização KB<br>Exportação KB<br>Importação KB<br>Substituir kB<br>Criar KB|Símbolo do portador|1. Criar KB API<br>2. Atualização KB API<br>3. Substituir a KB API<br>4. Substituir alterações<br>5. "Train API" [no novo modelo de serviço v5]|
|Utilizador de Serviço Cognitivo<br>(ler/escrever/publicar)|Todos|Símbolo do portador|Todos|