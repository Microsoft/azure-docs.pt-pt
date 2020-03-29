---
title: Como configurar uma aplicação de procuração de aplicação / Microsoft Docs
description: Aprenda a criar e configurar uma aplicação proxy de aplicação em alguns passos simples
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aaf2eb282bc3fd0b9f3853ce493c479a3d3c3a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67807861"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Como configurar uma aplicação proxy de aplicação

Este artigo ajuda-o a compreender como configurar uma aplicação de Procuração de Aplicações dentro do Azure AD para expor as suas aplicações no local à nuvem.

## <a name="recommended-documents"></a>Documentos recomendados

Para conhecer as configurações iniciais e a criação de uma aplicação Proxy de aplicação através do Portal da Administração, siga as [aplicações Da Publicação utilizando o Proxy de Aplicação AD Azure](application-proxy-add-on-premises-application.md).

Para mais detalhes sobre a configuração dos Conectores, consulte [ativar o proxy de aplicação no portal Azure](application-proxy-add-on-premises-application.md).

Para obter informações sobre o upload de certificados e utilizando domínios personalizados, consulte [Trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Criar a Aplicação/Definição dos URLs

Se estiver a seguir os passos nas aplicações da Publicação utilizando documentação de Procuração de [Aplicação AD Azure](application-proxy-add-on-premises-application.md) e estiver a obter um erro criando a aplicação, consulte os detalhes do erro para obter informações e sugestões para a forma de corrigir a aplicação. A maioria das mensagens de erro incluem uma correção sugerida. Para evitar erros comuns, verifique:

- É um administrador com permissão para criar um pedido de procuração de aplicação
- O URL interno é único
- O URL externo é único
- Os URLs começam com http ou https, e terminam com um "/"
- O URL deve ser um nome de domínio, não um endereço IP

A mensagem de erro deve ser exibida no canto superior direito quando criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

![Mostra onde encontrar o aviso de notificação no portal Azure](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configure conectores/grupos de conectores

Se tiver dificuldade em configurar a sua aplicação por aviso sobre os conectores e grupos de conectores, consulte instruções para permitir a procuração de aplicações para obter detalhes sobre como descarregar conectores. Se quiser saber mais sobre conectores, consulte a documentação dos [conectores.](application-proxy-connectors.md)

Se os seus conectores estiverem inativos, isto significa que não conseguem chegar ao serviço. Isto acontece muitas vezes porque todas as portas necessárias não estão abertas. Para ver uma lista das portas necessárias, consulte a secção de pré-requisitos da documentação de procuração de aplicação ativa.

## <a name="upload-certificates-for-custom-domains"></a>Enviar certificados para domínios personalizados

Domínios personalizados permitem especificar o domínio dos seus URLs externos. Para utilizar domínios personalizados, precisa de carregar o certificado para esse domínio. Para obter informações sobre a utilização de domínios e certificados personalizados, consulte [Trabalhar com domínios personalizados em Procuração de Aplicação AD Azure](application-proxy-configure-custom-domain.md).

Se encontrar problemas no upload do seu certificado, procure as mensagens de erro no portal para obter informações adicionais sobre o problema com o certificado. Os problemas comuns do certificado incluem:

- Certificado expirado
- O certificado é auto-assinado
- Falta a chave privada

O visor da mensagem de erro no canto superior direito enquanto tenta fazer o upload do certificado. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

## <a name="next-steps"></a>Passos seguintes

[Publicar aplicações com o Proxy da Aplicação do Azure AD](application-proxy-add-on-premises-application.md)
