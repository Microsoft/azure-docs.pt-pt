---
title: Como configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD
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
ROBOTS: NOINDEX
ms.openlocfilehash: ed2a1b1f4eaf665e893026c8b3bbc77ddad5c98e
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/07/2020
ms.locfileid: "77063582"
---
# <a name="how-to-configure-user-provisioning-to-an-azure-ad-gallery-application"></a>Como configurar o provisionamento de usuário para um aplicativo da galeria do Azure AD

*O provisionamento* da conta de utilizador é o ato de criar, atualizar e/ou desativar os registos da conta de utilizador na loja de perfis de utilizador local de uma aplicação. A maioria das aplicações cloud e SaaS armazenam a função e permissões dos utilizadores na própria loja de perfis de utilizador local do utilizador, e a presença de tal registo de utilizador na loja local do utilizador é *necessária* para um único registo e acesso ao trabalho.

No portal Azure, o separador **Provisioning** no painel de navegação esquerdo para uma App Enterprise exibe quais os modos de provisionamento suportados para essa aplicação. Isso pode ser um dos dois valores:

## <a name="configuring-an-application-for-manual-provisioning"></a>Configurando um aplicativo para provisionamento manual

*O* fornecimento manual significa que as contas do utilizador devem ser criadas manualmente utilizando métodos fornecidos pela aplicação. Isso pode significar fazer logon em um portal administrativo para esse aplicativo e adicionar usuários usando uma interface de usuário baseada na Web. Ou pode estar carregando uma planilha com detalhes da conta de usuário, usando um mecanismo fornecido por esse aplicativo. Consulte a documentação fornecida pelo aplicativo ou entre em contato com o desenvolvedor do aplicativo para determinar quais mecanismos estão disponíveis.

Se *o Manual* for o único modo mostrado para uma determinada aplicação, significa que ainda não existe um conector de fornecimento de AD Azure automático para a aplicação. Ou isso significa que o aplicativo não oferece suporte aos pré-requisitos para a API de gerenciamento de usuários da Microsoft, que é usada para criar um conector de provisionamento automatizado.

Se quiser solicitar apoio para o fornecimento automático de uma determinada aplicação, pode preencher um pedido utilizando os Pedidos de [Aplicação de Diretório Ativo Azure](https://aka.ms/aadapprequest).

## <a name="configuring-an-application-for-automatic-provisioning"></a>Configurando um aplicativo para provisionamento automático

*Automática* significa que foi desenvolvido um conector de fornecimento de AD Azure para esta aplicação. Para obter mais informações sobre o serviço de provisionamento de AD Azure e como funciona, consulte o [Fornecimento de Utilizadores automáticoe e o Deprovisionamento às Aplicações SaaS com o Diretório Ativo Azure.](../app-provisioning/user-provisioning.md)

Para obter mais informações sobre como fornecer utilizadores e grupos específicos a uma aplicação, consulte [a Gestão do fornecimento de conta de utilizador para aplicações empresariais.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

As etapas reais necessárias para habilitar e configurar o provisionamento automático variam de acordo com o aplicativo.

> [!NOTE]
> Você deve começar encontrando o tutorial de instalação específico para configurar o provisionamento para seu aplicativo e seguindo essas etapas para configurar o aplicativo e o Azure AD para criar a conexão de provisionamento.

Os tutoriais de aplicações podem ser encontrados na [Lista de Tutoriais sobre Como Integrar Apps SaaS com Diretório Ativo Azure](../saas-apps/tutorial-list.md).

Uma coisa importante a ser considerada ao configurar o provisionamento é examinar e configurar os mapeamentos de atributo e os fluxos de trabalho que definem quais propriedades de usuário (ou grupo) fluem do Azure AD para o aplicativo. Isso inclui a definição da "propriedade correspondente" que é usada para identificar exclusivamente e corresponder usuários/grupos entre os dois sistemas. Consulte o link em *Next Steps* para obter mais informações sobre mapeamentos de atributos.

## <a name="next-steps"></a>Passos seguintes
[Personalização de mapeamentode atributos para aplicações SaaS em Diretório Ativo Azure](../app-provisioning/customize-application-attributes.md)

