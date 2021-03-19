---
title: incluir ficheiro
description: incluir ficheiro
ms.topic: include
ms.custom: include file
ms.date: 03/11/2020
ms.openlocfilehash: b34d3ed2481600cde7ffb3275d635b8a57926bd6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "83665951"
---
As seguintes funções estão previstas para a colaboração:

|Função|Funcionalidades|Acesso API|Permissões de API|
|--|--|--|--|
|Proprietário|Todos|Chave de Autenticação|Todos|
|Contribuinte|Tudo exceto a capacidade de adicionar novos membros a papéis|Chave de Autenticação|Tudo exceto a capacidade de adicionar novos membros a papéis|
|Leitura do Criador de QnA<br>(ler)|Exportação/Download<br>Teste|Ficha de portador|1. Baixar KB API<br>2. Lista de KBs para a API do utilizador<br>3. Obter detalhes da base do conhecimento<br>4. Baixar Alterações<br>Gerar Resposta |
|QnA Maker Editor<br>(ler/escrever)|Exportação/Download<br>Teste<br>Atualizar KB<br>KB de exportação<br>Importação KB<br>Substituir KB<br>Criar KB|Ficha de portador|1. Criar KB API<br>2. Atualizar KB API<br>3. Substituir a KB API<br>4. Substituir Alterações<br>5. "Train API" [no novo modelo de serviço v5]|
|Utilizador de Serviço Cognitivo<br>(ler/escrever/publicar)|Todos|Ficha de portador|Todos|