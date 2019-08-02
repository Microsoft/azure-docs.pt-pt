---
title: Protegendo aplicativos Web e móveis de PaaS usando o serviço de Azure App | Microsoft Docs
description: 'Saiba mais sobre as práticas recomendadas de segurança de serviço do Azure App para proteger seus aplicativos móveis e Web de PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/18/2019
ms.author: terrylan
ms.openlocfilehash: dd47f4b7e0e9e27714be3862494bfdabf122d458
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726801"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Práticas recomendadas para proteger aplicativos Web e móveis de PaaS usando o serviço Azure App

Neste artigo, discutiremos uma coleção de Azure App práticas recomendadas de segurança de [serviço](/azure/app-service/overview) para proteger seus aplicativos móveis e Web de PaaS. Essas práticas recomendadas derivam de nossa experiência com o Azure e as experiências de clientes como você.

O serviço de Azure App é uma oferta de PaaS (plataforma como serviço) que permite criar aplicativos Web e móveis para qualquer plataforma ou dispositivo e conectar-se a dados em qualquer lugar, na nuvem ou no local. O serviço de aplicativo inclui os recursos Web e móveis que foram fornecidos anteriormente separadamente como sites do Azure e serviços móveis do Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um único serviço integrado, o serviço de aplicativo traz um rico conjunto de recursos para cenários da Web, móveis e de integração.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autenticar por meio de Azure Active Directory (AD)
O serviço de aplicativo fornece um serviço OAuth 2,0 para seu provedor de identidade. O OAuth 2,0 concentra-se na simplicidade do desenvolvedor do cliente ao fornecer fluxos de autorização específicos para aplicativos Web, aplicativos de área de trabalho e celulares. O Azure AD usa o OAuth 2,0 para permitir que você autorize o acesso a aplicativos móveis e Web. Para saber mais, confira [autenticação e autorização no serviço Azure app](../../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Restringir o acesso com base na função
Restringir o acesso é imperativo para organizações que desejam impor políticas de segurança para acesso a dados. Você pode usar o RBAC (controle de acesso baseado em função) para atribuir permissões a usuários, grupos e aplicativos em um determinado escopo, como a necessidade de conhecer os princípios de segurança de privilégios mínimos. Para saber mais sobre como conceder acesso a aplicativos aos usuários, confira [o que é controle de acesso baseado em função](/azure/role-based-access-control/overview).

## <a name="protect-your-keys"></a>Proteger suas chaves
Não importa a qualidade de sua segurança se você perder suas chaves de assinatura. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Com o Key Vault, você pode criptografar chaves e segredos (como chaves de autenticação, chaves de conta de armazenamento, chaves de criptografia de dados,. Arquivos PFX e senhas) usando chaves que são protegidas por HSMs (módulos de segurança de hardware). Para maior segurança, pode importar ou gerar chaves nos HSMs. Você também pode usar Key Vault para gerenciar seus certificados TLS com renovação automática. Consulte [o que é Azure Key Vault](../../key-vault/key-vault-whatis.md) para saber mais.

## <a name="restrict-incoming-source-ip-addresses"></a>Restringir os endereços IP de origem de entrada
Os [ambientes do serviço de aplicativo](../../app-service/environment/intro.md) têm um recurso de integração de rede virtual que ajuda a restringir endereços IP de origem de entrada por meio de NSGs (grupos de segurança de rede). Se você não estiver familiarizado com as redes virtuais do Azure (VNETs), essa é uma funcionalidade que permite que você coloque muitos dos seus recursos do Azure em uma rede que não é roteável pela Internet para a qual você controla o acesso. Para saber mais, consulte [integrar seu aplicativo a uma rede virtual do Azure](../../app-service/web-sites-integrate-with-vnet.md).

Para o serviço de aplicativo no Windows, você também pode restringir os endereços IP dinamicamente Configurando o Web. config. Para obter mais informações, consulte [segurança de IP dinâmico](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Passos Seguintes
Este artigo apresentou a você uma coleção de práticas recomendadas de segurança do serviço de aplicativo para proteger seus aplicativos móveis e Web de PaaS. Para saber mais sobre como proteger suas implantações de PaaS, confira:

- [Proteger implementações PaaS](paas-deployments.md)
- [Protegendo bancos de dados PaaS no Azure](paas-applications-using-sql.md)
