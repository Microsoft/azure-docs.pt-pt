---
title: Garantir aplicações móveis de & web da PaaS
titleSuffix: Azure App Service
description: 'Conheça as melhores práticas de segurança do Serviço de Aplicações Azure para garantir as suas aplicações web e móveis PaaS. '
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
ms.openlocfilehash: c3f3c7fbaa043a03b70ab770c06e493716c70daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77500283"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Boas práticas para garantir aplicações web e móveis paaS usando o Serviço de Aplicações Azure

Neste artigo, discutimos uma coleção de boas práticas de segurança do Serviço de [Aplicações Azure](/azure/app-service/overview) para garantir as suas aplicações web e móveis PaaS. Estas boas práticas derivam da nossa experiência com o Azure e das experiências de clientes como você.

O Azure App Service é uma oferta de plataforma-as-a-service (PaaS) que permite criar aplicações web e móveis para qualquer plataforma ou dispositivo e conectar-se a dados em qualquer lugar, na nuvem ou no local. O Serviço de Aplicações inclui as capacidades web e móveis que foram previamente entregues separadamente como Websites Azure e Serviços Móveis Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um único serviço integrado, o App Service traz um conjunto rico de capacidades para cenários web, móveis e integração.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autenticar através do Diretório Ativo Azure (AD)
O Serviço de Aplicações fornece um serviço OAuth 2.0 para o seu fornecedor de identidade. O OAuth 2.0 foca-se na simplicidade do desenvolvedor de clientes, ao mesmo tempo que fornece fluxos de autorização específicos para aplicações web, aplicações de desktop e telemóveis. A Azure AD utiliza o OAuth 2.0 para permitir autorizar o acesso a aplicações móveis e web. Para saber mais, consulte autenticação e autorização no Serviço de [Aplicações Azure.](../../app-service/overview-authentication-authorization.md)

## <a name="restrict-access-based-on-role"></a>Restringir o acesso com base no papel
Restringir o acesso é imperativo para as organizações que querem impor políticas de segurança para o acesso de dados. Pode utilizar o controlo de acesso baseado em funções (RBAC) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito, tais como a necessidade de conhecer e menos privilegiar princípios de segurança. Para saber mais sobre a concessão de acesso aos utilizadores às aplicações, consulte [o que é o controlo de acesso baseado em papéis.](/azure/role-based-access-control/overview)

## <a name="protect-your-keys"></a>Proteja as suas chaves
Não importa o quão boa é a sua segurança se perder as chaves de subscrição. O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Com o Key Vault, pode encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs. Também pode utilizar o Key Vault para gerir os seus certificados TLS com renovação automática. Veja [o que é azure key vault](../../key-vault/key-vault-overview.md) para saber mais.

## <a name="restrict-incoming-source-ip-addresses"></a>Restringir os endereços IP de origem de entrada
[O App Service Environments](../../app-service/environment/intro.md) tem uma funcionalidade de integração de rede virtual que o ajuda a restringir os endereços IP de origem através de grupos de segurança de rede (NSGs). Se não está familiarizado com as Redes Virtuais Azure (VNETs), esta é uma capacidade que lhe permite colocar muitos dos seus recursos Azure numa rede não-internet e redirecionável a que controla o acesso. Para saber mais, consulte [Integrar a sua app com uma Rede Virtual Azure.](../../app-service/web-sites-integrate-with-vnet.md)

Para o Serviço de Aplicações no Windows, também pode restringir os endereços IP de forma dinâmica configurando o web.config. Para mais informações, consulte [Dynamic IP Security](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe uma coleção de boas práticas de segurança do Serviço de Aplicações para garantir as suas aplicações web e móveis PaaS. Para saber mais sobre como garantir as suas implementações paas, consulte:

- [Proteger implementações PaaS](paas-deployments.md)
- [Proteger bases de dados de PaaS no Azure](paas-applications-using-sql.md)
