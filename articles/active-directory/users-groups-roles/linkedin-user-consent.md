---
title: LinkedIn partilha de dados de integração e consentimento - Azure Active Directory | Documentos da Microsoft
description: Explica como a integração do LinkedIn partilha dados através de aplicações da Microsoft no Azure Active Directory
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/28/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71eeed62c6de2a3771909e8489ca816930993435
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57442227"
---
# <a name="linkedin-integration-data-sharing-and-consent"></a>Partilha de dados de integração do LinkedIn e consentimento

Como administrador do Azure Active Directory (Azure AD), pode permitir que os utilizadores na sua organização para dar consentimento para ligar o seu trabalho da Microsoft conta escolar ou profissional com a respetiva conta do LinkedIn. Quando os utilizadores liguem as contas, informações e destaques do LinkedIn estão disponíveis em algumas aplicações da Microsoft e serviços. Os usuários também podem esperar a sua experiência de redes no LinkedIn a ser melhorado e enriquecida com informações da Microsoft.

Para ver informações do LinkedIn nos serviços e aplicações da Microsoft, os utilizadores tem de dar consentimento para ligar as suas contas Microsoft e LinkedIn. Para ligar as suas contas pela primeira vez que clicam para ver informações de LinkedIn de alguém num cartão de perfil do Outlook, OneDrive ou SharePoint Online, é pedido aos utilizadores. Ligações de conta do LinkedIn não são totalmente ativadas para os seus utilizadores até que eles o consentimento para a experiência de e para ligar as suas contas.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Benefícios de compartilhar informações do LinkedIn

Acesso às informações do LinkedIn dentro de serviços e aplicações da Microsoft torna mais fácil para os seus utilizadores para se ligar, interagir com e criar relações profissionais com colegas, clientes e parceiros dentro e fora da sua organização. Novos utilizadores podem familiarize-se mais rapidamente ao ligar-se com os colegas, aprender mais sobre eles e acesso fácil a obter mais informações. Eis um exemplo de como as informações do LinkedIn aparecem no cartão perfil nas aplicações da Microsoft:

![Ativar a integração do LinkedIn](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Ativar e anunciar a integração do LinkedIn

Tem de ser um administrador do Azure Active Directory para gerir a definição para a sua organização. Pode ativá-la para todos os utilizadores ou para um conjunto específico de utilizadores.

1. Para ativar ou desativar a integração, siga os passos em [integração do LinkedIn](linkedin-integration.md).
2. Quando a anunciar a integração do LinkedIn na sua organização, indicar a seus usuários as FAQ sobre [informações do LinkedIn nos serviços e aplicações da Microsoft](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). O artigo fornece informações sobre onde informações do LinkedIn aparecem, como ligar a contas e muito mais.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consentimento de utilizador para acesso a dados na Microsoft e LinkedIn

Dados que são acedidos a partir do LinkedIn não são permanentemente armazenados nos serviços Microsoft. Dados que são acedidos da Microsoft não estão armazenados permanentemente com o LinkedIn, exceto para contatos. Microsoft Contacts são armazenadas no LinkedIn até que os utilizadores removê-los, conforme descrito em [eliminar contactos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Quando os utilizadores liguem as contas, informações e as informações do LinkedIn estão disponíveis em algumas aplicações da Microsoft, como o cartão de perfil. Os usuários também podem esperar a sua experiência de redes no LinkedIn a ser melhorado e enriquecida com informações da Microsoft.
Quando os utilizadores na sua organização ligar o seu trabalho LinkedIn e da Microsoft contas escolares ou profissionais, eles tem duas opções:

* Conceder permissão para os dados ser acedido a partir de ambas as contas. Isso significa que dê permissão da Microsoft ou conta para aceder a dados a partir da respetiva conta do LinkedIn e para profissional [sua conta LinkedIn para aceder a dados de seu trabalho da Microsoft conta escolar ou profissional](https://www.linkedin.com/help/linkedin/answer/84077).
* Conceder permissão para apenas os dados LinkedIn aceder ao seu Microsoft de trabalho e conta escolar.

Os utilizadores possam desligar as contas e remover as permissões de acesso de dados em qualquer altura, e [os utilizadores podem controlar como o seu próprio perfil do LinkedIn é visualizado](https://www.linkedin.com/help/linkedin/answer/83), incluindo se o respetivo perfil pode ser visualizado nas aplicações da Microsoft.

### <a name="linkedin-account-data"></a>Dados de conta do LinkedIn

Quando se liga as suas contas Microsoft e LinkedIn, permite que o LinkedIn forneça os seguintes dados para a Microsoft:

* Os dados de perfil – inclui a identidade do LinkedIn, informações de contacto e as informações partilhadas com outras pessoas na sua [perfil do LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Interessa dados – inclui interesses no LinkedIn, como as pessoas e os tópicos a seguir, grupos de cursos e de conteúdo, como e partilharem.
* Dados de subscrições – inclui as subscrições para aplicativos do LinkedIn e serviços, bem como os dados associados. 
* Ligações de dados – inclui sua [LinkedIn rede](https://www.linkedin.com/help/linkedin/answer/110) incluindo perfis e informações de contato das suas ligações de dia 1 grau.

Dados que são acedidos a partir do LinkedIn não são permanentemente armazenados nos serviços Microsoft. Para obter mais informações sobre a utilização da Microsoft dos dados pessoais, consulte a [declaração de privacidade do Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Os dados da conta de escolar ou profissional da Microsoft

Quando se liga as suas contas Microsoft e LinkedIn, permite que a Microsoft fornecer os seguintes dados para o LinkedIn:

* Os dados de perfil – inclui informações como seu nome próprio, último nome, fotografia do perfil, endereço de e-mail, manager e as pessoas que gere.
* Dados de calendário – inclui reuniões em seus calendários, os tempos, localizações e as informações de contacto dos participantes. Informações sobre a reunião, como a agenda, conteúdo ou a cumprir o título não estão incluídas nos dados de calendário.
* Interessa dados – inclui os interesses à sua conta, com base na utilização de serviços Microsoft, como Cortana e Bing para empresas.
* Dados de subscrições - incluem as assinaturas fornecidas pela sua organização para aplicações da Microsoft e serviços, como o Office 365.
* Entra em contacto com dados – inclui a lista de contactos no Outlook, Skype e outros serviços da conta Microsoft, incluindo as informações de contacto para as pessoas que comunicam com frequência ou colaborar com. Contactos serão periodicamente importados, armazenados e utilizado pelo LinkedIn, por exemplo, para sugerir ligações, ajudar a, contactos de organizar e Mostrar atualizações sobre contactos.

Dados que são acedidos da Microsoft não estão armazenados permanentemente com o LinkedIn, exceto para contatos. Microsoft Contacts são armazenadas no LinkedIn, até que os utilizadores removê-los. Saiba mais sobre [eliminar contactos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Para obter mais informações sobre a utilização do LinkedIn dos dados pessoais, consulte a [política de privacidade do LinkedIn](https://www.linkedin.com/legal/privacy-policy). Para serviços, transferência de dados e armazenamento do LinkedIn, podem fluxo de dados provenientes da União Europeia para os Estados Unidos e vice-versa, e sua privacidade é protegida, conforme descrito em [as transferências de dados da União Europeia](https://www.linkedin.com/help/linkedin/answer/62533).

## <a name="next-steps"></a>Passos Seguintes

* [LinkedIn em aplicativos da Microsoft com a sua conta escolar ou profissional](https://www.linkedin.com/help/linkedin/answer/84077)
