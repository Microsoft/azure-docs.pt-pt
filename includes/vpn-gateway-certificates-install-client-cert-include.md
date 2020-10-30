---
title: incluir ficheiro
description: incluir ficheiro
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 393b29245141b2970e7c1a227d6e8b1b131c445c
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061647"
---
Se quiser criar uma ligação P2S a partir de um computador cliente sem ser o utilizado para gerar os certificados de cliente, tem de instalar um certificado de cliente. Ao instalar um certificado de cliente, vai precisar da palavra-passe que foi criada quando o certificado de cliente foi exportado.

1. Localize e copie o ficheiro *.pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *.pfx* para o instalar. Deixe a **Localização da Loja** como **Utilizador Atual** e, em seguida, selecione **Seguinte** .
1. Na página **Ficheiro a importar** , não efetue nenhuma alteração. Selecione **Seguinte** .
1. Na página **de proteção da chave privada,** insira a palavra-passe para o certificado ou verifique se o principal de segurança está correto e, em seguida, selecione **Seguinte** .
1. Na página **Certificate Store,** deixe a localização predefinitiva e, em seguida, selecione **Seguinte** .
1. Selecione **Concluir** . No **Aviso de Segurança** para a instalação do certificado, selecione **Sim** . Pode selecionar confortavelmente 'Sim' para este aviso de segurança porque gerou o certificado.
1. O certificado foi agora importado com êxito.
