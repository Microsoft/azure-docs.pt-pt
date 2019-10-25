---
title: Como configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD | Microsoft Docs
description: Como você pode configurar rapidamente o provisionamento e o desprovisionamento de conta de usuário avançado para aplicativos já listados na Galeria de aplicativos do Azure AD
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
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8bcc53b97b1187314404cfe075f6593f437e7bf4
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2019
ms.locfileid: "72789053"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Como configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD

O *provisionamento de conta de usuário* é o ato de criar, atualizar e/ou desabilitar registros de conta de usuário no armazenamento de perfil de usuário local de um aplicativo. A maioria dos aplicativos de nuvem e SaaS armazenam a função de usuários e as permissões no próprio armazenamento de perfil de usuário local do usuário e a presença desse registro de usuário no armazenamento local do usuário é *necessária* para o logon único e o acesso ao trabalho.

No portal do Azure, a guia **provisionamento** no painel de navegação esquerdo de um aplicativo empresarial exibe quais modos de provisionamento têm suporte para esse aplicativo. Isso pode ser um dos dois valores:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configurando um aplicativo para provisionamento manual

Provisionamento *manual* significa que as contas de usuário devem ser criadas manualmente usando métodos fornecidos pelo aplicativo. Isso pode significar fazer logon em um portal administrativo para esse aplicativo e adicionar usuários usando uma interface de usuário baseada na Web. Ou pode estar carregando uma planilha com detalhes da conta de usuário, usando um mecanismo fornecido por esse aplicativo. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

Se *manual* for o único modo mostrado para um determinado aplicativo, isso significa que ainda não há nenhum conector de provisionamento do Azure ad automático para o aplicativo. Ou isso significa que o aplicativo não oferece suporte aos pré-requisitos para a API de gerenciamento de usuários da Microsoft, que é usada para criar um conector de provisionamento automatizado.

Se desejar solicitar suporte para o provisionamento automático para um determinado aplicativo, você poderá preencher uma solicitação usando as [solicitações de Azure Active Directory aplicativo](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurando um aplicativo para provisionamento automático

*Automático* significa que um conector de provisionamento do Azure ad foi desenvolvido para este aplicativo. Para obter mais informações sobre o serviço de provisionamento do Azure AD e como ele funciona, consulte [automatizar o provisionamento e desprovisionamento de usuários para aplicativos SaaS com Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Para obter mais informações sobre como provisionar usuários e grupos específicos para um aplicativo, consulte [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam de acordo com o aplicativo.

> [!NOTE]
> Você deve começar encontrando o tutorial de instalação específico para configurar o provisionamento para seu aplicativo e seguindo essas etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento. 

Os tutoriais do aplicativo podem ser encontrados na [lista de tutoriais sobre como integrar aplicativos SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Uma coisa importante a ser considerada ao configurar o provisionamento é examinar e configurar os mapeamentos de atributo e os fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Isso inclui a definição da "propriedade correspondente" que é usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Consulte o link nas *próximas etapas* para obter mais informações sobre mapeamentos de atributo.

## <a name="next-steps"></a>Passos seguintes
[Personalizando mapeamentos de atributo de provisionamento de usuário para aplicativos SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

