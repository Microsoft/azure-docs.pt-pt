---
title: Proteger o PaaS web e móveis com o serviço de aplicações do Azure | Documentos da Microsoft
description: 'Saiba mais sobre a segurança do serviço de aplicações do Azure melhores práticas para proteger as suas aplicações móveis e web de PaaS. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 6e5034d0ff8f14a9fc381f6fd1a214a91ad4d1ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444406"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Melhores práticas para proteger o PaaS web e móveis com o serviço de aplicações do Azure

Neste artigo, discutimos a uma coleção de [App Service do Azure](../app-service/overview.md) melhores práticas de segurança para proteger as suas aplicações móveis e web de PaaS. Essas práticas recomendadas são derivadas da nossa experiência com o Azure e as experiências dos clientes, como.

Serviço de aplicações do Azure é uma oferta de plataforma-como-serviço (PaaS) que lhe permite criar aplicações web e móveis para qualquer plataforma ou dispositivo e ligue-se aos dados em qualquer local e na cloud ou no local. Serviço de aplicações inclui as capacidades web e móveis que anteriormente foram entregues em separado como Web sites do Azure e serviços móveis do Azure. Também inclui novas capacidades para automatizar processos de negócio e o alojar APIs da nuvem. Como um serviço integrado único, o serviço de aplicações fornece um conjunto avançado de capacidades para a web, móveis e cenários de integração.

## <a name="authenticate-through-azure-active-directory-ad"></a>Autenticar através do Azure Active Directory (AD)
Serviço de aplicações fornece um serviço OAuth 2.0 para o seu fornecedor de identidade. OAuth 2.0 se concentra na simplicidade de desenvolvedor do cliente ao mesmo tempo, os fluxos de autorização específico para aplicações web, aplicativos de desktop e celulares. Azure AD utiliza o OAuth 2.0 para lhe permitir autorizar o acesso a dispositivos móveis e aplicações web. Para obter mais informações, consulte [autenticação e autorização no serviço de aplicações do Azure](../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Restringir o acesso com base na função 
Restringir o acesso é fundamental para as organizações que desejam aplicar políticas de segurança para acesso a dados. Pode utilizar o controlo de acesso baseado em funções (RBAC) para atribuir permissões a utilizadores, grupos e aplicações num determinado âmbito, como a necessidade de conhecer e princípios de segurança de privilégio mínimo. Para saber mais sobre como conceder aos utilizadores acesso a aplicações, veja [o que é o controlo de acesso baseado em funções](../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Proteger as suas chaves
Não importa quão bom é de sua segurança se perder as suas chaves de subscrição. O Cofre de Chaves do Azure ajuda a salvaguardar as chaves criptográficas e os segredos utilizados pelas aplicações em cloud e pelos serviços. Com o Key Vault, pode encriptar chaves e segredos (tal como chaves de autenticação, chaves de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estão protegidas por módulos de segurança de hardware (HSMs). Para maior segurança, pode importar ou gerar chaves nos HSMs. Também pode utilizar o Key Vault para gerir os certificados TLS com renovação automática. Ver [o que é o Azure Key Vault](../key-vault/key-vault-whatis.md) para saber mais. 

## <a name="restrict-incoming-source-ip-addresses"></a>Restringir os endereços IP de origem recebidos
[Ambientes de serviço de aplicações](../app-service/environment/intro.md) tem um recurso de integração de rede virtual que lhe permite restringir os endereços IP de origem recebidos por meio dos grupos de segurança de rede (NSGs). Se não estiver familiarizado com as redes virtuais do Azure (VNETs), isso é uma funcionalidade que permite que colocar muitos dos seus recursos do Azure numa rede não internet encaminhável que controlam o acesso a. Para obter mais informações, consulte [integrar a sua aplicação com uma rede Virtual do Azure](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Passos Seguintes
Este artigo apresentou-a uma coleção de serviço de aplicações práticas recomendada de segurança para proteger as suas aplicações móveis e web de PaaS. Para saber mais sobre como proteger as suas implementações de PaaS, consulte:

- [Proteger implementações PaaS](security-paas-deployments.md)
- [Protegendo bancos de dados de PaaS no Azure](security-paas-applications-using-sql.md)
