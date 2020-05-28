---
title: Benefícios da migração clássica de implantação em Serviços de Domínio Azure AD [ Microsoft Docs
description: Saiba mais sobre os benefícios da migração de uma implantação clássica de Serviços de Domínio de Diretório Ativo Azure para o modelo de implementação do Gestor de Recursos
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: iainfou
ms.openlocfilehash: 29d7772ef688134e8fc9009ec32cffacc3f23ca7
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014456"
---
# <a name="benefits-of-migration-from-the-classic-to-resource-manager-deployment-model-in-azure-active-directory-domain-services"></a>Benefícios da migração do modelo de implantação classic para gestor de recursos em Serviços de Domínio ativo do Diretório Azure

O Azure Ative Directory Domain Services (AD DS) permite migrar um domínio gerido existente que utiliza o modelo de implementação Classic para o modelo de implementação do Gestor de Recursos. Os domínios geridos pela Azure AD DS que utilizam o modelo de implementação do Gestor de Recursos fornecem funcionalidades adicionais, tais como a política de passwords de grãos finos, registos de auditoria e proteção de bloqueio de conta.

Este artigo descreve os benefícios para a migração. Para começar, consulte [migrate Azure AD Domain Services do modelo clássico de rede virtual para Gestor][howto-migrate]de Recursos .

> [!NOTE]
> Em 2017, a Azure AD Domain Services tornou-se disponível para hospedar numa rede azure Resource Manager. Desde então, conseguimos construir um serviço mais seguro utilizando as capacidades modernas do Gestor de Recursos Azure. Uma vez que as implementações do Azure Resource Manager substituem totalmente as implementações clássicas, as implementações clássicas da rede virtual Azure AD DS serão retiradas a 1 de março de 2023.
>
> Para mais informações, consulte o [aviso oficial de depreciação](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="migration-benefits"></a>Benefícios migratórios

O processo de migração tem uma instância AD DS Azure existente que utiliza o modelo de implementação Classic e move-se para usar o modelo de implementação do Gestor de Recursos. Ao migrar um domínio gerido pelo Azure AD DS do modelo de implementação Classic to Resource Manager, evita a necessidade de voltar a juntar as máquinas ao domínio gerido ou eliminar a instância Azure AD DS e criar uma de raiz. Os VMs continuam a ser associados ao domínio gerido pela AD DS Azure no final do processo de migração.

Após a migração, o Azure AD DS fornece muitas funcionalidades que só estão disponíveis para domínios utilizando o modelo de implementação do Gestor de Recursos, como o seguinte:

* [Suporte à política de passwords de grãos finos.][password-policy]
* Velocidades de sincronização mais rápidas entre os Serviços de Domínio Azure AD e Azure AD.
* Dois novos [atributos que sincronizam do][attributes]gestor da AD azure  -  *manager* e *do funcionárioID.*
* Acesso a controladores de domínio mais poderosos quando [atualizar o SKU][skus].
* Proteção de bloqueio de conta ad.
* [Notificações por e-mail para alertas no seu domínio gerido][email-alerts].
* [Utilize livros de trabalho azure e monitor Azure para visualizar registos de auditoria e atividade de login.][workbooks]
* Nas regiões apoiadas, [as Zonas de Disponibilidade Azure.][availability-zones]
* Integrações com outros produtos Azure, tais como [Ficheiros Azure,][azure-files] [Insights HD][hd-insights]e [Windows Virtual Desktop.][wvd]
* O apoio tem acesso a mais telemetria e pode ajudar a resolver problemas de forma mais eficaz.
* A encriptação em repouso em repouso utilizando [discos geridos][managed-disks] pelo Azure para os dados dos controladores de domínio geridos.

O Azure AD DS geriu domínios que utilizam um modelo de implementação do Gestor de Recursos, ajudando-o a manter-se atualizado com as novas funcionalidades mais recentes. Novas funcionalidades não estão disponíveis para domínios geridos pelo Azure AD DS que utilizam o modelo de implementação Classic.

## <a name="next-steps"></a>Próximos passos

Para começar, consulte [migrate Azure AD Domain Services do modelo clássico de rede virtual para Gestor][howto-migrate]de Recursos .

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
[managed-disks]: ../virtual-machines/windows/managed-disks-overview.md
