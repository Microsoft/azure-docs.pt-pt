---
title: Partilha e consentimento de dados do LinkedIn - Diretório Ativo Azure / Microsoft Docs
description: Explica como a integração do LinkedIn partilha dados através de aplicações da Microsoft no Diretório Ativo do Azure
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74025397"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn liga a partilha e consentimento de dados da conta

Pode permitir que os utilizadores da sua organização Ative Directory (Azure AD) consintam em ligar o seu trabalho ou conta escolar da Microsoft à sua conta LinkedIn. Depois de um utilizador ligar as suas contas, as informações e os destaques do LinkedIn estão disponíveis em algumas aplicações e serviços da Microsoft. Os utilizadores também podem esperar que a sua experiência de networking no LinkedIn seja melhorada e enriquecida com informações da Microsoft.

Para ver informações do LinkedIn em aplicações e serviços da Microsoft, os utilizadores devem consentir em ligar as suas próprias contas Microsoft e LinkedIn. Os utilizadores são solicitados a ligar as suas contas na primeira vez que clicam para ver as informações do LinkedIn de alguém num cartão de perfil no Outlook, OneDrive ou SharePoint Online. As ligações da conta LinkedIn não estão totalmente ativas para os seus utilizadores até que consintam com a experiência e conectem as suas contas.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Benefícios da partilha de informações do LinkedIn

O acesso à informação do LinkedIn dentro de aplicações e serviços da Microsoft facilita a ligação, envolvimento e construção de relações profissionais com colegas, clientes e parceiros dentro e fora da sua organização. Os novos utilizadores podem acelerar mais rapidamente, conectando-se com os colegas, aprendendo mais sobre eles e acedendo facilmente a mais informações. Aqui está um exemplo de como a informação do LinkedIn aparece no cartão de perfil nas aplicações da Microsoft:

![Permitir a integração do LinkedIn na sua organização](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Ativar e anunciar integração do LinkedIn

Você deve ser um Administrador de Diretório Ativo Azure para gerir o cenário para a sua organização. Pode ativar para todos os utilizadores ou para um conjunto específico de utilizadores.

1. Para permitir ou desativar a integração, siga os passos em [Consentir a integração do LinkedIn para a sua organização Azure AD](linkedin-integration.md).
2. Quando anunciar a integração do LinkedIn na sua organização, aponte os seus utilizadores para as FAQ sobre informações do [LinkedIn nas aplicações e serviços](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381)da Microsoft. O artigo fornece informações sobre onde as informações do LinkedIn aparecem, como conectar contas, e muito mais.

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consentimento do utilizador para acesso a dados na Microsoft e linkedIn

Os dados acedidos a partir do LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Os dados acedidos a partir da Microsoft não são armazenados permanentemente no LinkedIn, com exceção dos Contactos. Os Contactos microsoft são armazenados no LinkedIn até que os utilizadores os removam, conforme descrito na [eliminação de contactos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Quando os utilizadores conectam as suas contas, informações e insights do LinkedIn estão disponíveis em algumas aplicações da Microsoft, como o cartão de perfil. Os utilizadores também podem esperar que a sua experiência de networking no LinkedIn seja melhorada e enriquecida com informações da Microsoft.
Quando os utilizadores da sua organização conectam o seu trabalho linkedIn e microsoft ou contas escolares, eles têm duas opções:

* Autorize o acesso dos dados a partir de ambas as contas. Isto significa que eles dão permissão para que a sua Microsoft ou conta de trabalho aceda mato a partir da sua conta LinkedIn, e para que a [sua conta LinkedIn aceda a dados a partir do seu trabalho ou conta escolar da Microsoft](https://www.linkedin.com/help/linkedin/answer/84077).
* Autorize apenas os dados do LinkedIn a serem acedidos pela sua conta de trabalho e escola da Microsoft.

Os utilizadores podem desligar as contas e remover permissões de acesso a dados a qualquer momento, e os utilizadores podem controlar a forma como o [seu próprio perfil LinkedIn é visualizado](https://www.linkedin.com/help/linkedin/answer/83)– incluindo se o seu perfil pode ser visto nas aplicações da Microsoft.

### <a name="linkedin-account-data"></a>Dados da conta LinkedIn

Quando liga as suas contas Microsoft e LinkedIn, permite que o LinkedIn forneça os seguintes dados à Microsoft:

* Os dados de perfil - incluem identidade LinkedIn, informações de contacto e as informações que partilha com outros no seu [perfil LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dados de interesses - inclui interesses no LinkedIn, como pessoas e tópicos que segue, grupos de cursos e conteúdo que gosta e partilha.
* Os dados de subscrições - inclui subscrições de aplicações e serviços do LinkedIn juntamente com dados associados. 
* Dados de ligações - inclui a sua [rede LinkedIn,](https://www.linkedin.com/help/linkedin/answer/110) incluindo perfis e informações de contacto das suas ligações de 1º grau.

Os dados acedidos a partir do LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Para obter mais informações sobre a utilização de dados pessoais da [Microsoft,](https://privacy.microsoft.com/privacystatement/)consulte a Declaração de Privacidade da Microsoft .

### <a name="microsoft-work-or-school-account-data"></a>Dados do trabalho da Microsoft ou da conta escolar

Quando liga as suas contas Microsoft e LinkedIn, permite que a Microsoft forneça os seguintes dados ao LinkedIn:

* Dados de perfil - inclui informações como o seu primeiro nome, apelido, foto de perfil, endereço de e-mail, gestor e pessoas que gere.
* Dados do calendário - inclui reuniões nos seus calendários, horários, locais e informações de contacto dos participantes. As informações sobre a reunião, como agenda, conteúdo ou título de reunião não estão incluídas nos dados do calendário.
* Dados de interesses - inclui os interesses associados à sua conta, com base na utilização de serviços da Microsoft, como cortana e Bing for Business.
* Dados de subscrições - inclui subscrições fornecidas pela sua organização a aplicações e serviços da Microsoft, como o Office 365.
* Os dados de contactos - incluem listas de contactos no Outlook, Skype e outros serviços de conta da Microsoft, incluindo as informações de contacto para pessoas com quem comunica ou colabora frequentemente. Os contactos serão periodicamente importados, armazenados e utilizados pelo LinkedIn, por exemplo, para sugerir ligações, ajudar a organizar contactos e mostrar atualizações sobre contactos.

Os dados acedidos a partir da Microsoft não são armazenados permanentemente no LinkedIn, com exceção dos Contactos. Os Contactos microsoft são armazenados no LinkedIn até que os utilizadores os removam. Saiba mais sobre [a apagar contactos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Para obter mais informações sobre a utilização de dados pessoais do LinkedIn, consulte a Política de [Privacidade do LinkedIn](https://www.linkedin.com/legal/privacy-policy). Para os serviços linkedIn, transferência de dados e armazenamento, os dados podem fluir da União Europeia para os Estados Unidos e voltar, e a sua privacidade está protegida como descrito nas transferências de [dados da União Europeia.](https://www.linkedin.com/help/linkedin/answer/62533)

## <a name="next-steps"></a>Passos seguintes

* [LinkedIn em aplicações da Microsoft com o seu trabalho ou conta escolar](https://www.linkedin.com/help/linkedin/answer/84077)
