---
title: Benefícios da migração clássica de implantação nos Serviços de Domínio Azure AD ! Microsoft Docs
description: Saiba mais sobre os benefícios de migrar uma implementação clássica dos Serviços de Domínio do Diretório Ativo Azure para o modelo de implementação do Gestor de Recursos
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 93dcc1202c08be905cf08513f38e79a8a7674e01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88650133"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Benefícios da migração do modelo de implementação clássico para gestor de recursos nos Serviços de Domínio do Diretório Ativo Azure

O Azure Ative Directory Domain Services (Azure AD DS) permite migrar um domínio gerido existente que utiliza o modelo de implementação Clássico para o modelo de implementação do Gestor de Recursos. Os domínios geridos pela Azure AD DS que utilizam o modelo de implementação do Gestor de Recursos fornecem funcionalidades adicionais, tais como a política de palavras-passe de grãos finos, registos de auditoria e proteção de bloqueio de conta.

Este artigo descreve os benefícios para a migração. Para começar, consulte os [Serviços de Domínio Ad Azure Migrate Azure do modelo de rede virtual Clássico ao Gestor de Recursos.][howto-migrate]

> [!NOTE]
> Em 2017, a Azure AD Domain Services tornou-se disponível para acolher numa rede Azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro utilizando as capacidades modernas do Gestor de Recursos Azure. Como as implementações do Azure Resource Manager substituem totalmente as implementações clássicas, as implementações clássicas da rede virtual Azure AD DS serão retiradas a 1 de março de 2023.
>
> Para mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Benefícios migratórios

O processo de migração requer um domínio gerido existente que utiliza o modelo de implementação Clássico e move-se para usar o modelo de implementação do Gestor de Recursos. Quando migra um domínio gerido do modelo de implementação Classic para Resource Manager, evita-se a necessidade de voltar a juntar máquinas para o domínio gerido ou eliminar o domínio gerido e criar um de raiz. Os VM continuam a ser unidos ao domínio gerido no final do processo de migração.

Após a migração, o Azure AD DS fornece muitas funcionalidades que só estão disponíveis para domínios utilizando o modelo de implementação do Gestor de Recursos, tais como:

* [Suporte à política de palavra-passe de grão fino][password-policy].
* Velocidades de sincronização mais rápidas entre os serviços AZure AD e Azure AD Domain Services.
* Dois novos [atributos que sincronizam do gestor da AD Azure][attributes]  -  *manager* e *do staffID.*
* Acesso a controladores de domínio de maior potência quando [atualizar o SKU][skus].
* Proteção de bloqueio de conta AD.
* [Notificações por e-mail para alertas no seu domínio gerido][email-alerts].
* [Utilize livros de trabalho Azure e monitor Azure para visualizar registos de auditoria e atividade de login][workbooks].
* Nas regiões apoiadas, [Zonas de Disponibilidade Azure.][availability-zones]
* Integrações com outros produtos Azure, tais como [Azure Files,][azure-files] [HD Insights][hd-insights]e Windows [Virtual Desktop][wvd].
* O suporte tem acesso a mais telemetria e pode ajudar a resolver problemas de forma mais eficaz.
* Encriptação em repouso usando [Discos Geridos Azure][managed-disks] para os dados dos controladores de domínio geridos.

Domínios geridos que utilizam um modelo de implementação do Gestor de Recursos ajudam-no a manter-se atualizado com as novas funcionalidades mais recentes. Não existem novas funcionalidades disponíveis para domínios geridos que utilizem o modelo de implementação Classic.

## <a name="next-steps"></a>Passos seguintes

Para começar, consulte os [Serviços de Domínio Ad Azure Migrate Azure do modelo de rede virtual Clássico ao Gestor de Recursos.][howto-migrate]

<!-- LINKS - INTERNAL -->
[password-policy]: password-policy.md
[skus]: change-sku.md
[email-alerts]: notifications.md
[workbooks]: use-azure-monitor-workbooks.md
[azure-files]: ../storage/files/storage-files-identity-auth-active-directory-domain-service-enable.md
[hd-insights]: ../hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds.md
[wvd]: ../virtual-desktop/overview.md
[availability-zones]: ../availability-zones/az-overview.md
[howto-migrate]: migrate-from-classic-vnet.md
[attributes]: synchronization.md#attribute-synchronization-and-mapping-to-azure-ad-ds
[managed-disks]: ../virtual-machines/managed-disks-overview.md
