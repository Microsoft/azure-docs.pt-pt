---
title: Atualizações de TLS de Centros de Notificação
description: Saiba mais sobre o suporte para TLS em Centros de Notificação Azure.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: ''
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/30/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/28/2020
ms.openlocfilehash: 87309e20efd9d6f8bd1a659451e5a603e6b95bc8
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76908530"
---
# <a name="transport-layer-security-tls"></a>Segurança da camada de transporte (TLS)

Para garantir um nível mais elevado de segurança, os Centros de Notificação desativarão o suporte para as versões TLS 1.0 e 1.1 no dia 30 de abril de 2020. Estes protocolos antigos fornecem criptografia fraca, e são vulneráveis aos ataques beast e POODLE. As aplicações implementadas para dispositivos que executam a versão 5 ou superior do Android, ou a versão 5 ou superior do iOS, não são afetadas por esta alteração, uma vez que esses sistemas operativos suportam o TLS 1.2 e o cliente e o servidor negociarão a versão mais elevada suportada mutuamente protocolo após ligação.

Recomendamos que reveja todas as suas aplicações que utilizam hubs de notificação Do Azure, para garantir que utilizam as bibliotecas mais aplicáveis e pilhas TLS que suportam TLS 1.2.

## <a name="update-apps"></a>Atualizar aplicativos

Pode garantir que as suas aplicações iOS estão a utilizar o TLS 1.2 utilizando a funcionalidade de segurança de rede da Apple chamada App Transport Security (ATS). O ATS não pode ser utilizado para SDKs mais antigos do que o iOS 9.0 ou macOS 10.11, podeler mais sobre o mesmo a partir [da documentação da Apple](https://developer.apple.com/documentation/security/preventing_insecure_network_connections).

Para aplicações Android utilizando instâncias SSLSocket, detete imar protocolos ativados em cada instância SSLSocket, conforme notado em [protocolos definidos .](https://developer.android.com/reference/javax/net/ssl/SSLSocket#setEnabledProtocols(java.lang.String%5B%5D))

A tabela na página de suporte à compatibilidade do [protocolo TLS](https://support.globalsign.com/customer/portal/articles/2934392-tls-protocol-compatibility) ajuda a mapear sistemas operativos com versões TLS compatíveis.

Para mais informações, consulte a visão geral do [suporte para protocolos TLS no Windows](https://docs.microsoft.com/archive/blogs/kaushal/support-for-ssltls-protocols-on-windows).

## <a name="next-steps"></a>Passos seguintes

- [Visão geral dos hubs de notificação](notification-hubs-push-notification-overview.md)