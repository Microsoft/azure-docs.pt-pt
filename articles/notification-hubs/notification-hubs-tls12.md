---
title: Notificações Hubs TLS atualizações
description: Saiba mais sobre o suporte para TLS em Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87a3627d7820f9f456ac08e2f20b70af961f817e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87084250"
---
# <a name="transport-layer-security-tls"></a>Transport Layer Security (TLS)

Para garantir um nível mais elevado de segurança, os Centros de Notificação desativarão o suporte para as versões TLS 1.0 e 1.1 em **31 de dezembro de 2020** (estendida a partir de 30 de abril de 2020). Estes protocolos mais antigos fornecem criptografia fraca, e são vulneráveis aos ataques de BEAST e POODLE. As aplicações implementadas para dispositivos com a versão 5 ou superior do Android, ou versão iOS 5 ou superior, não são impactadas por esta mudança, uma vez que os sistemas operativos suportam o TLS 1.2 e o cliente e servidor negociarão a versão mais alta do protocolo mutuamente suportada em ligação.

Recomendamos que reveja todas as suas aplicações que utilizam Hubs de Notificação Azure, para garantir que utilizam as bibliotecas mais aplicáveis e pilhas TLS que suportam TLS 1.2.

## <a name="update-apps"></a>Atualizar aplicações

Pode garantir que as suas aplicações iOS estão a utilizar o TLS 1.2 utilizando a funcionalidade de segurança de rede da Apple chamada App Transport Security (ATS). O ATS não pode ser utilizado para SDKs mais antigos do que o iOS 9.0 ou o macOS 10.11, e pode ler mais sobre isso a partir da [documentação da Apple.](https://developer.apple.com/documentation/security/preventing_insecure_network_connections)

Para aplicações Android usando instâncias SSLSocket, deteta protocolos habilitados em cada instância SSLSocket como indicado no [setEnabledProtocols](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D)).

A tabela na página de suporte à compatibilidade do [protocolo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ajuda a mapear sistemas operativos com versões TLS compatíveis.

Para mais informações, consulte a visão geral do [suporte aos protocolos TLS no Windows](/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral dos Hubs de Notificação](notification-hubs-push-notification-overview.md)
