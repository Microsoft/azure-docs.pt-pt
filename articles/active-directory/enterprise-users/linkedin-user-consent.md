---
title: Partilha e consentimento de dados linkedIn - Azure Ative Directory / Microsoft Docs
description: Explica como a integração do LinkedIn partilha dados através de aplicações da Microsoft no Azure Ative Directory
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: how-to
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d90391dd5512b08ee6995a32bcd0b0f111b0f2b
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92377451"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>LinkedIn liga a partilha e consentimento de dados

Pode permitir que os utilizadores da sua organização Ative Directory (Azure AD) consintam em ligar o seu trabalho ou conta escolar da Microsoft com a sua conta LinkedIn. Depois de um utilizador ligar as suas contas, as informações e os destaques do LinkedIn estão disponíveis em algumas aplicações e serviços da Microsoft. Os utilizadores também podem esperar que a sua experiência de networking no LinkedIn seja melhorada e enriquecida com informações da Microsoft.

Para ver informações do LinkedIn nas aplicações e serviços da Microsoft, os utilizadores devem consentir em ligar as suas próprias contas Microsoft e LinkedIn. Os utilizadores são solicitados a ligar as suas contas na primeira vez que clicam para ver as informações do LinkedIn de alguém num cartão de perfil no Outlook, OneDrive ou SharePoint Online. As ligações de conta LinkedIn não estão totalmente ativadas para os seus utilizadores até que consintam na experiência e conectem as suas contas.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Benefícios da partilha de informação do LinkedIn

O acesso à informação do LinkedIn dentro de aplicações e serviços da Microsoft facilita a ligação, envolvimento e construção de relações profissionais com colegas, clientes e parceiros dentro e fora da sua organização. Os novos utilizadores podem acelerar mais rapidamente, conectando-se com os colegas, aprendendo mais sobre eles e facilmente acedendo a mais informações. Aqui está um exemplo de como as informações do LinkedIn aparecem no cartão de perfil nas aplicações da Microsoft:

![Permitir a integração do LinkedIn na sua organização](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Ativar e anunciar a integração do LinkedIn

Você deve ser um Azure Ative Directory Admin para gerir a configuração para a sua organização. Pode ative-lo para todos os utilizadores, ou para um conjunto específico de utilizadores.

1. Para ativar ou desativar a integração, siga os passos em [Consent to LinkedIn integração para a sua organização AD Azure.](linkedin-integration.md)
2. Quando anunciar a integração do LinkedIn na sua organização, aponte os seus utilizadores para as FAQ sobre [informações do LinkedIn em aplicações e serviços da Microsoft.](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381) O artigo fornece informações sobre onde a informação do LinkedIn aparece, [partilha de dados e privacidade,](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400) [como conectar contas](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) e muito mais.

Deve anunciar a Integração do Linkedin aos seus utilizadores, fornecendo-lhes todas as informações relacionadas com a [partilha de dados e privacidade com a Integração do Linkedin.](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400) 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Consentimento do utilizador para acesso a dados na Microsoft e LinkedIn

Os dados acedidos a partir do LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Os dados acedidos a partir da Microsoft não são armazenados permanentemente com o LinkedIn, exceto para Contactos. Os Contactos Microsoft são armazenados no LinkedIn até que os utilizadores os removam, conforme descrito na [eliminação de contactos importados do LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Quando os utilizadores conectam as suas contas, as informações e insights do LinkedIn estão disponíveis em algumas aplicações da Microsoft, como o cartão de perfil. Os utilizadores também podem esperar que a sua experiência de networking no LinkedIn seja melhorada e enriquecida com informações da Microsoft.
Quando os utilizadores da sua organização ligam o seu trabalho linkedIn e Microsoft ou contas escolares, têm duas opções:

* Dê permissão para que os dados sejam acedidos a partir de ambas as contas. Isto significa que eles autorizam a sua Microsoft ou conta de trabalho a aceder aos dados da sua conta linkedIn e [a sua conta LinkedIn a aceder aos dados a partir do seu trabalho da Microsoft ou da sua conta escolar.](https://www.linkedin.com/help/linkedin/answer/84077)
* Dê permissão para que apenas os dados do LinkedIn sejam acedidos pelo seu trabalho microsoft e conta escolar.

Os utilizadores podem desligar as contas e remover permissões de acesso a dados a qualquer momento, e [os utilizadores podem controlar como o seu próprio perfil linkedIn é visualizado](https://www.linkedin.com/help/linkedin/answer/83), incluindo se o seu perfil pode ser visualizado nas aplicações da Microsoft.

### <a name="linkedin-account-data"></a>Dados da conta linkedIn

Quando liga as suas contas Microsoft e LinkedIn, permite que o LinkedIn forneça os seguintes dados à Microsoft:

* Dados de perfil - inclui identidade do LinkedIn, informações de contacto e as informações que partilha com outros no seu [perfil LinkedIn](https://www.linkedin.com/help/linkedin/answer/15493).
* Dados de interesses - inclui interesses no LinkedIn, tais como pessoas e tópicos que segue, grupos de cursos e conteúdos que você gosta e compartilha.
* Dados de subscrições - inclui subscrições de aplicações e serviços linkedIn, juntamente com dados associados. 
* Dados de conexões - inclui a sua [rede LinkedIn,](https://www.linkedin.com/help/linkedin/answer/110) incluindo perfis e informações de contacto das suas ligações de 1º grau.

Os dados acedidos a partir do LinkedIn não são armazenados permanentemente nos serviços da Microsoft. Para obter mais informações sobre a utilização de dados pessoais da Microsoft, consulte a Declaração de Privacidade da [Microsoft.](https://privacy.microsoft.com/privacystatement/)

### <a name="microsoft-work-or-school-account-data"></a>Dados de trabalho ou conta escolar da Microsoft

Quando liga as suas contas Microsoft e LinkedIn, permite que a Microsoft forneça os seguintes dados ao LinkedIn:

* Dados de perfil - inclui informações como o seu primeiro nome, apelido, foto de perfil, endereço de e-mail, gestor e pessoas que gere.
* Dados do calendário - inclui reuniões nos seus calendários, horários, locais e informações de contacto dos participantes. A informação sobre a reunião, como agenda, conteúdo ou título de reunião, não está incluída nos dados do calendário.
* Dados de interesses - inclui os interesses associados à sua conta, com base na sua utilização de serviços da Microsoft, como cortana e Bing para negócios.
* Dados de subscrições - inclui subscrições fornecidas pela sua organização a aplicações e serviços da Microsoft, como o Microsoft 365.
* Contactos dados - inclui listas de contactos em Outlook, Skype e outros serviços de conta microsoft, incluindo as informações de contacto para pessoas com quem comunica ou colabora frequentemente. Os contactos serão periodicamente importados, armazenados e utilizados pelo LinkedIn, por exemplo, para sugerir ligações, ajudar a organizar contactos e mostrar atualizações sobre contactos.

Os dados acedidos a partir da Microsoft não são armazenados permanentemente com o LinkedIn, exceto para Contactos. Os Contactos Microsoft são armazenados no LinkedIn até que os utilizadores os removam. Saiba mais sobre [a eliminação dos contactos importados do LinkedIn.](https://www.linkedin.com/help/linkedin/answer/43377)

Para obter mais informações sobre a utilização de dados pessoais do LinkedIn, consulte a Política de [Privacidade linkedIn](https://www.linkedin.com/legal/privacy-policy). Para os serviços linkedIn, transferência de dados e armazenamento, os dados podem fluir da União Europeia para os Estados Unidos e voltar, e a sua privacidade está protegida como descrito nas [transferências de dados da União Europeia.](https://www.linkedin.com/help/linkedin/answer/62533)

## <a name="next-steps"></a>Passos seguintes

* [LinkedIn nas aplicações da Microsoft com o seu trabalho ou conta escolar](https://www.linkedin.com/help/linkedin/answer/84077)
