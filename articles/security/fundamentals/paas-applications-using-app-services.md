---
title: Segurança paaS web & aplicações móveis
titleSuffix: Azure App Service
description: 'Saiba mais sobre as melhores práticas de segurança do Azure App Service para garantir as suas aplicações web e móveis PaaS. '
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
ms.openlocfilehash: e583bb1b9cad79ee3e48e838d81d6a504c44f72c
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408095"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Melhores práticas para garantir aplicações web e móveis paaS usando o Azure App Service

Neste artigo, discutimos uma coleção de boas práticas de segurança do [Azure App Service](../../app-service/overview.md) para garantir as suas aplicações web e móveis PaaS. Estas boas práticas derivam da nossa experiência com o Azure e das experiências de clientes como você.

O Azure App Service é uma oferta de plataforma como serviço (PaaS) que permite criar aplicações web e móveis para qualquer plataforma ou dispositivo e ligar-se a dados em qualquer lugar, na nuvem ou no local. O Serviço de Aplicações inclui as capacidades web e móveis que foram previamente entregues separadamente como Websites Azure e Azure Mobile Services. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um único serviço integrado, o App Service traz um rico conjunto de capacidades para cenários web, mobile e de integração.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autenticar através do Diretório Ativo Azure (AD)
O App Service fornece um serviço OAuth 2.0 para o seu fornecedor de identidade. O OAuth 2.0 foca-se na simplicidade do desenvolvedor de clientes, ao mesmo tempo que fornece fluxos de autorização específicos para aplicações web, aplicações de desktop e telemóveis. A Azure AD utiliza o OAuth 2.0 para permitir o acesso a aplicações móveis e web. Para saber mais, consulte [autenticação e autorização no Serviço de Aplicações Azure.](../../app-service/overview-authentication-authorization.md)

## <a name="restrict-access-based-on-role"></a>Restringir o acesso com base no papel
Restringir o acesso é imperativo para as organizações que querem impor políticas de segurança para o acesso aos dados. Pode utilizar o controlo de acesso baseado em funções (RBAC) para atribuir permissões aos utilizadores, grupos e aplicações num determinado âmbito, como a necessidade de conhecer e menos privilégios de princípios de segurança. Para saber mais sobre a concessão de acesso aos utilizadores às aplicações, consulte [o que é o controlo de acesso baseado em funções Azure (Azure RBAC)](../../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Proteja as suas chaves
Não importa quão boa é a sua segurança se perder as chaves de subscrição. O cofre de chave do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em nuvem e pelos serviços. Com o Key Vault, pode encriptar chaves e segredos (tais como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados, . Ficheiros PFX e palavras-passe) utilizando chaves protegidas por módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs. Também pode utilizar o Key Vault para gerir os seus certificados TLS com renovação automática. Veja [o que é Azure Key Vault](../../key-vault/general/overview.md) para saber mais.

## <a name="restrict-incoming-source-ip-addresses"></a>Restringir endereços IP de origem de entrada
[O App Service Environments](../../app-service/environment/intro.md) tem uma funcionalidade de integração de rede virtual que o ajuda a restringir os endereços IP de origem recebida através de grupos de segurança de rede (NSGs). Se não está familiarizado com as Redes Virtuais Azure (VNETs), esta é uma capacidade que lhe permite colocar muitos dos seus recursos Azure numa rede não internet e encaminhável a que controla o acesso. Para saber mais, consulte [Integrar a sua aplicação com uma Rede Virtual Azure.](../../app-service/web-sites-integrate-with-vnet.md)

Para o Serviço de Aplicações no Windows, também pode restringir os endereços IP de forma dinâmica, configurando o web.config. Para mais informações, consulte [a Dynamic IP Security](/iis/configuration/system.webServer/security/dynamicIpSecurity/).


## <a name="next-steps"></a>Passos seguintes
Este artigo apresentou-lhe uma coleção de boas práticas de segurança do Serviço de Aplicações para garantir as suas aplicações web e móveis PaaS. Para saber mais sobre a segurança das suas implementações paaS, consulte:

- [Proteger implementações PaaS](paas-deployments.md)
- [Proteger bases de dados de PaaS no Azure](paas-applications-using-sql.md)