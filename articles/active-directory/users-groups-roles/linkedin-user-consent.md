---
title: Compartilhamento e consentimento de dados do LinkedIn – Azure Active Directory | Microsoft Docs
description: Explica como a integração do LinkedIn compartilha dados por meio de aplicativos da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a21c986ccfe96bae5d341e0ba2ee6622d998d076
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74025397"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Consentimento e compartilhamento de dados de conexões de contas do LinkedIn

Você pode permitir que usuários em sua organização do Active Directory (Azure AD) consentim para conectar sua conta corporativa ou de estudante da Microsoft à sua conta do LinkedIn. Depois que um usuário conecta suas contas, as informações e os destaques do LinkedIn estão disponíveis em alguns aplicativos e serviços da Microsoft. Os usuários também podem esperar que sua experiência de rede no LinkedIn seja aprimorada e enriqueceda com as informações da Microsoft.

Para ver informações do LinkedIn em aplicativos e serviços da Microsoft, os usuários devem consentir para conectar suas próprias contas da Microsoft e do LinkedIn. Os usuários são solicitados a conectar suas contas na primeira vez em que clicam para ver as informações do LinkedIn de alguém em um cartão de perfil no Outlook, OneDrive ou SharePoint Online. As conexões de conta do LinkedIn não são totalmente habilitadas para seus usuários até que eles consentissem na experiência e conectem suas contas.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Benefícios do compartilhamento de informações do LinkedIn

O acesso às informações do LinkedIn nos aplicativos e serviços da Microsoft facilita para os usuários a conexão, o envolvimento e a criação de relações profissionais com colegas, clientes e parceiros dentro e fora da sua organização. Novos usuários podem se familiarizar mais rapidamente conectando-se com colegas, aprendendo mais sobre eles e acessando facilmente mais informações. Aqui está um exemplo de como as informações do LinkedIn aparecem no cartão de perfil nos aplicativos da Microsoft:

![Habilitando a integração do LinkedIn em sua organização](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Habilitar e anunciar a integração do LinkedIn

Você deve ser um administrador de Azure Active Directory para gerenciar a configuração da sua organização. Você pode habilitá-lo para todos os usuários ou para um conjunto específico de usuários.

1. Para habilitar ou desabilitar a integração, siga as etapas em [consentir a integração do LinkedIn para sua organização do Azure ad](linkedin-integration.md).
2. Ao anunciar a integração do LinkedIn em sua organização, aponte seus usuários para as perguntas frequentes sobre [informações do LinkedIn em aplicativos e serviços da Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). O artigo fornece informações sobre onde as informações do LinkedIn aparecem, como conectar contas e muito mais.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consentimento do usuário para acesso a dados no Microsoft e no LinkedIn

Os dados que são acessados no LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Os dados que são acessados pela Microsoft não são armazenados permanentemente com o LinkedIn, exceto para contatos. Os contatos da Microsoft são armazenados no LinkedIn até que os usuários os removam, conforme descrito em [excluindo contatos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Quando os usuários conectam suas contas, informações e ideias do LinkedIn estão disponíveis em alguns aplicativos da Microsoft, como o cartão de perfil. Os usuários também podem esperar que sua experiência de rede no LinkedIn seja aprimorada e enriqueceda com as informações da Microsoft.
Quando os usuários em sua organização conectam suas contas corporativas ou de estudante do LinkedIn e da Microsoft, eles têm duas opções:

* Conceda permissão para que os dados sejam acessados de ambas as contas. Isso significa que eles dão permissão para sua conta da Microsoft ou corporativa para acessar dados de sua conta do LinkedIn e para [sua conta do LinkedIn para acessar dados de sua conta corporativa ou de estudante da Microsoft](https://www.linkedin.com/help/linkedin/answer/84077).
* Conceda permissão somente aos dados do LinkedIn para serem acessados por sua conta corporativa e de estudante da Microsoft.

Os usuários podem desconectar contas e remover permissões de acesso a dados a qualquer momento, e [os usuários podem controlar como seu próprio perfil do LinkedIn é exibido](https://www.linkedin.com/help/linkedin/answer/83), incluindo se seu perfil pode ser exibido nos aplicativos da Microsoft.

### <a name="linkedin-account-data"></a>Dados da conta do LinkedIn

Ao conectar suas contas Microsoft e LinkedIn, você permite que o LinkedIn forneça os seguintes dados à Microsoft:

* Dados de perfil-inclui a identidade do LinkedIn, informações de contato e as informações que você compartilha com outras pessoas em seu [perfil do LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dados de interesses – inclui interesses sobre o LinkedIn, como pessoas e tópicos que você segue, os grupos de cursos e o conteúdo que você gosta e compartilha.
* Dados de assinaturas-inclui assinaturas para aplicativos e serviços do LinkedIn juntamente com dados associados. 
* Dados de conexões-inclui sua [rede do LinkedIn](https://www.linkedin.com/help/linkedin/answer/110) , incluindo perfis e informações de contato de suas conexões de 1 °.

Os dados que são acessados no LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Para obter mais informações sobre o uso de dados pessoais da Microsoft, consulte a [política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Dados da conta corporativa ou de estudante da Microsoft

Ao conectar suas contas Microsoft e LinkedIn, você permite que a Microsoft forneça os seguintes dados ao LinkedIn:

* Dados de perfil-inclui informações como seu nome, sobrenome, foto de perfil, endereço de email, gerente e pessoas que você gerencia.
* Dados de calendário-inclui reuniões em seus calendários, seus horários, localizações e informações de contato dos participantes. As informações sobre a reunião, como a pauta, o conteúdo ou o título da reunião, não estão incluídas nos dados do calendário.
* Dados de interesses-inclui os interesses associados à sua conta, com base no uso dos serviços da Microsoft, como o Cortana e o Bing for Business.
* Dados de assinaturas-inclui assinaturas fornecidas pela sua organização para aplicativos e serviços da Microsoft, como o Office 365.
* Dados de contatos – inclui listas de contatos no Outlook, Skype e outros serviços de conta Microsoft, incluindo as informações de contato para pessoas com quem você se comunica com frequência ou colabora. Os contatos serão importados periodicamente, armazenados e usados pelo LinkedIn, por exemplo, para sugerir conexões, ajudar a organizar contatos e mostrar atualizações sobre contatos.

Os dados que são acessados pela Microsoft não são armazenados permanentemente com o LinkedIn, exceto para contatos. Os contatos da Microsoft são armazenados no LinkedIn até que os usuários os removam. Saiba mais sobre como [excluir contatos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Para obter mais informações sobre o uso de dados pessoais do LinkedIn, consulte a [política de privacidade do LinkedIn](https://www.linkedin.com/legal/privacy-policy). Para serviços do LinkedIn, transferência de dados e armazenamento, os dados podem fluir da União Europeia para a Estados Unidos e voltar, e sua privacidade é protegida conforme descrito em [transferências de dados da União Europeia](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Passos seguintes

* [LinkedIn em aplicativos da Microsoft com sua conta corporativa ou de estudante](https://www.linkedin.com/help/linkedin/answer/84077)
