---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 03/21/2018
ms.date: 12/24/2018
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: 0068bd151c3d7d243b05c326ec73a201f4131296
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679981"
---
Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Localize e copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Deixe a **Localização do Arquivo** como **Utilizador Atual** e, em seguida, clique em **Seguinte**.
2. Na página **Ficheiro a importar**, não efetue nenhuma alteração. Clique em **Seguinte**.
3. Sobre o **proteção por chave privada** página, introduza a palavra-passe para o certificado, ou certifique-se de que a entidade de segurança está correta e clique em **próxima**.
4. Na página **Arquivo de Certificados**, mantenha a localização predefinida e, em seguida, clique em **Seguinte**.
5. Clique em **Concluir**. Em **Aviso de Segurança** para a instalação do certificado, clique em **Sim**. Pode sentir-se à vontade para clicar em ''Sim'', uma vez que gerou o certificado. O certificado foi agora importado com êxito.