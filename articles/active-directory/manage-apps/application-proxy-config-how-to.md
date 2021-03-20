---
title: Como configurar uma aplicação de procuração de aplicação | Microsoft Docs
description: Saiba como criar e configurar uma aplicação de procuração APplication em alguns passos simples
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 05/18/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 65f1488986dc38bba89a5b9a109a444b5f0d81f2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99255208"
---
# <a name="how-to-configure-an-application-proxy-application"></a>Como configurar uma aplicação de procuração de aplicação

Este artigo ajuda-o a entender como configurar uma aplicação de Procuração de Aplicação dentro da Azure AD para expor as suas aplicações no local à nuvem.

## <a name="recommended-documents"></a>Documentos recomendados

Para conhecer as configurações iniciais e a criação de uma aplicação Proxy de aplicação através do Portal do Administrador, siga as [aplicações Publicar utilizando o Azure AD Application Proxy](application-proxy-add-on-premises-application.md).

Para obter mais informações sobre a configuração dos conectores, consulte [Ativar o Proxy da aplicação no portal Azure](application-proxy-add-on-premises-application.md).

Para obter informações sobre o upload de certificados e a utilização de domínios personalizados, consulte [trabalhar com domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

## <a name="create-the-applicationsetting-the-urls"></a>Criar a Aplicação/Definição dos URLs

Se estiver a seguir os passos nas aplicações Da Publicação utilizando a documentação [proxy da aplicação AD AD Azure](application-proxy-add-on-premises-application.md) e estiver a obter um erro ao criar a aplicação, consulte os detalhes de erro para obter informações e sugestões de como corrigir a aplicação. A maioria das mensagens de erro incluem uma correção sugerida. Para evitar erros comuns, verifique:

- É um administrador com permissão para criar uma aplicação de Procuração de Aplicação
- O URL interno é único
- O URL externo é único
- Os URLs começam com http ou https, e terminam com um "/"
- O URL deve ser um nome de domínio, não um endereço IP

A mensagem de erro deve ser exibida no canto superior direito quando criar a aplicação. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

![Mostra onde encontrar o pedido de notificação no portal Azure](./media/application-proxy-config-how-to/error-message.png)

## <a name="configure-connectorsconnector-groups"></a>Configurar os conectores/grupos de conectores

Se tiver dificuldade em configurar a sua aplicação devido a um aviso sobre os conectores e grupos de conectores, consulte instruções para ativar o Application Proxy para obter mais informações sobre como descarregar conectores. Se quiser saber mais sobre conectores, consulte a documentação dos [conectores](application-proxy-connectors.md).

Se os seus conectores estiverem inativos, isto significa que não conseguem chegar ao serviço. Isto deve-se muitas vezes ao facto de todas as portas necessárias não estarem abertas. Para ver uma lista das portas necessárias, consulte a secção de pré-requisitos da documentação de procuração de aplicação habilitada.

## <a name="upload-certificates-for-custom-domains"></a>Carregar certificados para domínios personalizados

Os Domínios Personalizados permitem especificar o domínio dos seus URLs externos. Para utilizar domínios personalizados, é necessário fazer o upload do certificado para esse domínio. Para obter informações sobre a utilização de domínios e certificados personalizados, consulte [trabalhar com domínios personalizados no Azure AD Application Proxy](application-proxy-configure-custom-domain.md).

Se encontrar problemas no upload do seu certificado, procure as mensagens de erro no portal para obter informações adicionais sobre o problema com o certificado. Os problemas comuns dos certificados incluem:

- Certificado caducado
- O certificado é auto-assinado
- O certificado falta a chave privada

A mensagem de erro é exibida no canto superior direito enquanto tenta fazer o upload do certificado. Também pode selecionar o ícone de notificação para ver as mensagens de erro.

## <a name="next-steps"></a>Passos seguintes

[Publicar aplicações com o Proxy da Aplicação do Azure AD](application-proxy-add-on-premises-application.md)
