---
title: Como configurar o aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD | Documentos da Microsoft
description: Como pode rapidamente configurar a conta de usuário avançada, aprovisionamento e desaprovisionamento para aplicativos já listados na Galeria de aplicações do Azure AD
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
ms.openlocfilehash: be43f0e100bc96de2be916cbf52bca7d3ba51431
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65784540"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Como configurar o aprovisionamento de utilizadores a uma aplicação da galeria do Azure AD

*Aprovisionamento de contas de utilizador* é o ato de criar, atualizar, e/ou desativar os registos de conta de utilizador no arquivo de perfil de utilizador local de um aplicativo. A maioria das aplicações SaaS e cloud armazenam a função de utilizadores e permissões no seu próprio arquivo de perfil de utilizador local e a presença de tal um registo de utilizador no arquivo local está *necessário* para início de sessão único e acesso ao seu trabalho.

No portal do Azure, o **aprovisionamento** separador no painel de navegação esquerdo, para uma aplicação empresarial que modos de aprovisionamento são suportados para essa aplicação. Isto pode ser um de dois valores:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configuração de uma aplicação de aprovisionamento Manual

*Manual* aprovisionamento significa que as contas de utilizador tem de ser criadas manualmente com os métodos fornecidos por essa aplicação. Isso pode significar o início de sessão num portal administrativo para essa aplicação e adicionar utilizadores que utilizam uma interface do usuário baseada na web. Ou poderia carregar uma planilha com detalhes de conta de usuário, usando um mecanismo fornecido por essa aplicação. Consulte a documentação fornecida pela aplicação, ou entre em contato com o programador da aplicação para determinar a wat mecanismos estão disponíveis.

Se Manual é o único modo mostrado para um determinado aplicativo, significa que nenhum automática aprovisionamento do Azure AD conector foi criado para a aplicação ainda. Ou, significa que a aplicação não suporta a API de gestão de pré-requisitos do utilizador no qual a criação de um conector de aprovisionamento automatizado.

Se gostaria de pedido de suporte para o aprovisionamento automático para uma determinada aplicação, pode preencher um pedido através da [pedidos de aplicações do Azure Active Directory](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configuração de uma aplicação para aprovisionamento automático

*Automática* significa que um Azure AD aprovisionamento conector foi desenvolvido para esta aplicação. Para obter mais informações sobre o Azure AD que o serviço de aprovisionamento e como ele funciona, consulte [automatizar o aprovisionamento do utilizador e cancelar o aprovisionamento a aplicações SaaS com o Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning).

Para obter mais informações sobre como aprovisionar utilizadores e grupos a uma aplicação específicos, consulte [gerir o aprovisionamento de contas de utilizador para aplicações empresariais de](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-manage-provisioning).

As etapas reais necessárias para ativar e configurar o aprovisionamento automático variam dependendo do aplicativo.

>[!NOTE]
>Deve começar encontrando o tutorial de configuração específicas para configurar o aprovisionamento para a sua aplicação e ao seguir os passos para configurar a aplicação e o Azure AD para criar a ligação de aprovisionamento. 
>
>

Tutoriais de aplicação podem ser encontrados em [lista de tutoriais sobre como integrar aplicações de SaaS no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Uma coisa importante a considerar ao configurar o aprovisionamento ser para rever e configurar os mapeamentos de atributos e fluxos de trabalho que definem qual usuário (ou grupo) propriedades fluxo a partir do Azure AD à aplicação. Isto inclui a definição "da propriedade correspondente" que ser utilizado para identificar exclusivamente e corresponder ao utilizadores/grupos entre os dois sistemas. Para obter mais informações sobre este processo importante.

## <a name="next-steps"></a>Passos Seguintes
[Personalizar mapeamentos de atributos de Provisionamento para aplicações de SaaS no Azure Active Directory de usuários](https://docs.microsoft.com/azure/active-directory/active-directory-saas-customizing-attribute-mappings)

