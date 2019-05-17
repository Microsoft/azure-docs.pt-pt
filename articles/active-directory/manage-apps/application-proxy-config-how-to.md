---
title: Como configurar uma aplicação de Proxy de aplicações | Documentos da Microsoft
description: Saiba como criar e configurar uma aplicação de Proxy da aplicação em poucos passos simples
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
ms.openlocfilehash: fc9287aac567c8989564094564b92b82662e603f
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825937"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Como configurar uma aplicação de Proxy de aplicações

Este artigo ajuda-o a compreender como configurar uma aplicação de Proxy de aplicações no Azure AD para expor seus aplicativos no local para a cloud.

## <a name="recommended-documents"></a>Documentos recomendados 

Para saber mais sobre as configurações iniciais e a criação de uma aplicação de Proxy de aplicações através do Portal de administração, siga os [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md).

Para obter detalhes sobre como configurar conectores, consulte [ativar o Proxy de aplicações no portal do Azure](application-proxy-add-on-premises-application.md).

Para obter informações sobre a carregar certificados e a utilização de domínios personalizados, consulte [trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Criar o aplicativo/definir os URLs

Se estiver a seguir os passos a [publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md) documentação e estão recebendo um erro ao criar o aplicativo, consulte os detalhes do erro para obter informações e sugestões para saber como corrigir o aplicação. A maioria das mensagens de erro incluem uma correção sugerida. Para evitar erros comuns, certifique-se:

-   For um administrador com permissão para criar uma aplicação de Proxy de aplicações

-   O URL interno é exclusivo

-   O URL externo é exclusivo

-   Os URLs de começar com http ou https e terminar com uma "/"

-   O URL deve ser um nome de domínio, não um endereço IP

A mensagem de erro deve exibir no canto superior direito, ao criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Linha de notificação](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurar grupos de conectores/conector

Se estiver a ter dificuldades em configurar seu aplicativo devido a aviso sobre os conectores e os grupos de conector, veja as instruções sobre como ativar o Proxy de aplicações para obter detalhes sobre como transferir conectores. Se quiser obter mais informações sobre conectores, consulte a [documentação dos conectores](application-proxy-connectors.md).

Se os conectores estão inativos, isso significa que eles são incapazes de chegarem ao serviço. Isto é, muitas vezes, porque todas as portas necessárias não estão abertas. Para ver uma lista de portas necessárias, consulte a secção de pré-requisitos da documentação do ativar o Proxy de aplicações.

## <a name="upload-certificates-for-custom-domains"></a>Carregar certificados para domínios personalizados

Domínios personalizados permitem-lhe especificar o domínio da sua URLs externos. Para utilizar domínios personalizados, terá de carregar o certificado para esse domínio. Para obter informações sobre a utilização de domínios personalizados e certificados, consulte [trabalhar com domínios personalizados no Proxy de aplicações do Azure AD](application-proxy-configure-custom-domain.md). 

Se tiver problemas com a carregar o certificado, procure as mensagens de erro no portal para obter mais informações sobre o problema com o certificado. Problemas de certificado comuns incluem:

-   Certificado expirado

-   O certificado é autoassinado

-   Certificado não tem a chave privada

Apresentar a mensagem de erro no canto superior direito carregar o certificado ao testar. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

   ![Linha de notificação](./media/application-proxy-config-how-to/error-message2.png)

## <a name="next-steps"></a>Passos Seguintes
[Publicar aplicações com o Proxy de aplicações do Azure AD](application-proxy-add-on-premises-application.md)
