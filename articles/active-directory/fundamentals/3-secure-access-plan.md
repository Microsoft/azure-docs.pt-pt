---
title: Criar um plano de segurança para acesso externo ao Azure Ative Directory
description: Planeie a segurança para o acesso externo aos recursos da sua organização.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 202f2190e68b89d790c628248ae89f0cb274ff76
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725216"
---
# <a name="3-create-a-security-plan-for-external-access"></a>3. Criar um plano de segurança para o acesso externo 

Agora que [determinou a sua desejada postura de segurança de segurança para acesso externo](1-secure-access-posture.md) e [descobriu o seu estado atual de colaboração,](2-secure-access-current-state.md)pode criar um plano externo de segurança e governação do utilizador. 

Este plano deve documentar o seguinte:

* As aplicações e outros recursos que devem ser agrupados para acesso.

* As condições de inscrição adequadas para utilizadores externos. Estes podem incluir o estado do dispositivo, a localização de inscrição, os requisitos de aplicação do cliente e o risco do utilizador.

* Políticas empresariais sobre quando rever e remover o acesso. 

* Populações de utilizadores devem ser agrupadas e tratadas da mesma forma.

Uma vez documentadas estas áreas, pode utilizar políticas de gestão de identidade e acesso da Microsoft ou de qualquer outro fornecedor de identidade (IdP) para implementar este plano.

## <a name="document-resources-to-be-grouped-for-access"></a>Recursos documentais a serem agrupados para acesso

Existem várias formas de agrupar recursos para acesso. 

* As Equipas microsoft agrulem ficheiros, linhas de conversação e outros recursos num só local. Deve formular uma estratégia de acesso externo para as Equipas microsoft. Consulte [acesso seguro a Equipas, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md).

* Os Pacotes de Acesso à Gestão de Direitos permitem-lhe criar um único pacote de aplicações e outros recursos aos quais pode conceder acesso. 

* As políticas de Acesso Condicional podem ser aplicadas a até 250 aplicações com os mesmos requisitos de acesso.

Seja como for que consigas aceder, tens de documentar quais as aplicações que devem ser agrupadas. As considerações devem incluir:

* **Perfil de risco**. Qual é o risco para o seu negócio se um mau ator teve acesso a uma aplicação? Considere codificar cada aplicação como alto, médio ou baixo risco. Tenha cuidado ao agrupar aplicações de alto risco com aplicações de baixo risco. 

   * Aplicações documentais que nunca devem ser partilhadas também com utilizadores externos.

* **Quadros de conformidade**. E se algum quadro de conformidade tiver de cumprir o pedido? Quais são os requisitos de acesso e revisão?

* **Candidaturas a funções ou departamentos específicos** de emprego. Existem aplicações que devem ser agrupadas porque todos os utilizadores numa função ou departamento de trabalho específico precisarão de acesso?

* **Aplicações centradas na colaboração.** Que aplicações focadas na colaboração devem ter acesso aos utilizadores externos? As equipas da Microsoft e o SharePoint podem ter de estar acessíveis. Para aplicações de produtividade dentro do Office 365, como o Word e o Excel, os utilizadores externos trarão as suas próprias licenças, ou precisarão licenciá-las e fornecer acesso?

Para cada agrupamento de aplicações e recursos que pretende tornar acessíveis aos utilizadores externos, documente o seguinte:

* Um nome descritivo para o grupo, por *exemplo, High_Risk_External_Access_Finance*. 

* Lista completa de todas as aplicações e recursos do grupo.

* Proprietários de aplicações e recursos e informações de contacto.

* Se o acesso é controlado por TI ou delegado ao proprietário do negócio.

* Quaisquer pré-requisitos, por exemplo, completar uma verificação de antecedentes ou uma formação, para acesso.

* Quaisquer requisitos de conformidade para o acesso aos recursos.

* Quaisquer desafios adicionais, por exemplo, que requerem a autenticação de vários fatores para recursos específicos.

* Quantas vezes o acesso será revisto, por quem e onde será documentado.

Este tipo de plano de governação pode e deve também ser concluído para acesso interno.

## <a name="document-sign-in-conditions-for-external-users"></a>Documente as condições de inscrição para utilizadores externos.

Como parte do seu plano, deve determinar os requisitos de inscrição para os seus utilizadores externos à medida que acedem aos recursos. Os requisitos de inscrição baseiam-se frequentemente no perfil de risco dos recursos e na avaliação dos riscos da inscrição dos utilizadores.

As condições de inscrição são configuradas no [Acesso Condicional Azure AD](../conditional-access/overview.md) e são compostas por uma condição e um resultado. Por exemplo, quando exigir a autenticação de vários fatores

**Condições de inscrição baseadas no risco de recursos.**

| Perfil de risco de aplicação| Considere estas políticas para desencadear a autenticação de vários fatores |
| - |-|
| Baixo risco| Exigir MFA para conjuntos de aplicações específicos |
| Risco de med| Exigir MFA quando outros riscos estiverem presentes |
| Alto risco| Exigir MFA sempre para utilizadores externos |


Hoje em dia, pode [impor a autenticação de vários fatores para utilizadores B2B no seu inquilino.](../external-identities/b2b-tutorial-require-mfa.md) 

**Sinal baseado no utilizador e no dispositivo em condições**.

| Risco de utilização ou de inscrição| Considere estas políticas |
| - | - |
| Dispositivo| Exigir dispositivos em conformidade |
| Aplicações móveis| Exigir aplicações aprovadas |
| Proteção de identidade mostra alto risco| Exigir que o utilizador altere a palavra-passe |
| Localização da rede| Exigir o registo de um intervalo específico de endereços IP para projetos altamente confidenciais |

Hoje, para utilizar o estado do dispositivo como entrada para uma apólice, o dispositivo deve ser registado ou associado ao seu inquilino. 

[As políticas baseadas no risco de proteção de identidade](../conditional-access/howto-conditional-access-policy-risk.md) podem ser utilizadas. No entanto, as questões devem ser atenuadas no arrendatário do utilizador.

Para [localizações de rede,](../conditional-access/howto-conditional-access-policy-location.md)pode restringir o acesso a qualquer gama de endereços IP que possua. Pode usar isto se apenas quiser que parceiros externos acedam a uma aplicação enquanto estão no local da sua organização.

[Saiba mais sobre políticas de acesso condicional.](../conditional-access/overview.md)

## <a name="document-access-review-policies"></a>Políticas de revisão de acesso documental

Documente as suas políticas de negócio para quando precisar de rever o acesso aos recursos e quando precisa de remover o acesso à conta para utilizadores externos. As entradas para estas decisões podem incluir:

* Requisitos detalhados em quaisquer quadros de conformidade.

* Políticas e processos internos de negócios

* Comportamento do utilizador

Embora as suas políticas sejam altamente personalizadas às suas necessidades, considere o seguinte:

* **Comentários sobre acesso à gestão de direitos.** Utilize a funcionalidade em Gestão de Direitos para

   * [Expire automaticamente os pacotes de acesso](../governance/entitlement-management-access-package-lifecycle-policy.md), e assim o acesso externo do utilizador aos recursos incluídos.

   * Desaveja uma [frequência de revisão necessária](../governance/entitlement-management-access-reviews-create.md) para avaliações de acesso.

   * Se estiver a utilizar [organizações conectadas](../governance/entitlement-management-organization.md) para agrupar todos os utilizadores de um único parceiro, agende avaliações regulares com o proprietário do negócio e o representante do parceiro.

* **Microsoft 365 Grupos**. Desempeca uma [política de expiração do grupo](/microsoft-365/solutions/microsoft-365-groups-expiration-policy?view=o365-worldwide) para os Grupos Microsoft 365 para os quais os utilizadores externos são convidados. 

* **Outras opções.** Se os utilizadores externos tiverem acesso fora dos pacotes de acesso à Gestão de Direitos ou dos grupos Microsoft 365, criem um processo de negócio para rever quando as contas devem ser feitas inativas ou eliminadas. Por exemplo:

   * Remova a capacidade de inscrição para qualquer conta não assinada durante 90 dias.

   * Avaliar as necessidades de acesso e tomar medidas no final de cada projeto com utilizadores externos.

 

## <a name="determine-your-access-control-methods"></a>Determine os seus métodos de controlo de acesso

Agora que sabe ao que quer controlar o acesso, como esses ativos devem ser agrupados para acesso comum, e que exigem políticas de revisão de acesso e de acesso, pode decidir como realizar o seu plano. 

Algumas funcionalidades, por [exemplo, Gestão de Direitos,](../governance/entitlement-management-overview.md)só estão disponíveis com licenças Azure AD Premium 2 (P2). As licenças Microsoft 365 E5 e Office 365 E5 incluem licenças Azure AD P2. 

Outras combinações de Microsoft 365, Office 365 e AZure AD também permitem algumas funcionalidades para gerir utilizadores externos. Consulte [a Proteção de Informações](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-tenantlevel-services-licensing-guidance/microsoft-365-security-compliance-licensing-guidance) para obter mais informações.

> [!NOTE]
> As licenças são por utilizador. Portanto, pode ter utilizadores específicos, incluindo administradores e proprietários de empresas delegados de controlo de acesso, no nível Azure AD P2 ou Microsoft 365 E5 sem permitir essas licenças para todos os utilizadores. Os seus primeiros 50.000 utilizadores externos são gratuitos. Se não ativar licenças P2 para os seus outros utilizadores internos, não poderão utilizar funcionalidades de gestão de direitos como pacotes Access. 


## <a name="govern-access-with-azure-ad-p2-and-microsoft--office-365-e5"></a>Reger o acesso com Azure AD P2 e Microsoft / Office 365 E5
O Azure AD P2 e o Microsoft 365 E5 possuem todo o conjunto de ferramentas de segurança e governação.

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning-bolded-entries-are-preferred-methods"></a>Provisionamento, inscrição, revisão de acesso e desprovisionamento. Entradas audazes são métodos preferenciais

| Funcionalidade| Fornecimento de utilizadores externos| Impor reqs de inscrição.| Rever acesso| Acesso de desprovisionamento |
| - | - | - | - | - |
| Colaboração Azure AD B2B| Convidar via e-mail, OTP, self-service| | **Revisão periódica por parceiro**| Remover conta<br>Restringir o sinal |
| Gestão de Direitos| **Adicionar ao utilizador através de atribuição ou acesso self-service**| | Revisões de acesso|**Expiração ou remoção do pacote de acesso**|
| Grupos do Office 365| | | Rever membros do grupo| Expiração ou supressão do grupo<br> Grupo de formulário de remoção |
| Grupos de segurança do Azure AD| | **Políticas de acesso condicional** (Adicionar utilizadores externos aos grupos de segurança, se necessário)| |  |



 ### <a name="access-to-resources-bolded-entries-are-preferred-methods"></a>Acesso a recursos. Entradas audazes são métodos preferenciais

|Funcionalidade | APP & acesso a recursos| SharePoint & acesso OneDrive| Acesso das equipas| E-mail & segurança documental |
| - |-|-|-|-|
| Gestão de Direitos| **Adicionar ao utilizador através de atribuição ou acesso self-service**| **Pacotes de acesso**| **Pacotes de acesso**|  |
| Escritório 365 Grupo| | Acesso ao site(s) (e conteúdo associado) incluído com grupo| Acesso a equipas (e conteúdo associado)incluídos com grupo|  |
| Etiquetas de confidencialidade| | **Classificar e restringir manualmente o acesso**| **Classificar e restringir manualmente o acesso**| **Classificar e restringir manualmente o acesso** |
| Grupos de segurança do Azure AD| **Políticas condicionais de acesso ao acesso não incluídas nos pacotes de acesso**| | |  |


### <a name="entitlement-management"></a>Gestão de Direitos 

[Os pacotes de acesso](../governance/entitlement-management-access-package-create.md) à gestão de direitos permitem o fornecimento e desprovisionamento do acesso a grupos e equipas, aplicações e sites SharePoint. Pode definir quais as organizações ligadas que têm acesso, se os pedidos de autosserviço são permitidos e quais os fluxos de trabalho de aprovação necessários (se houver) para conceder acesso. Para garantir que o acesso não fique por aqui mais tempo do que o necessário, pode definir políticas de expiração e avaliações de acesso para cada pacote de acesso. 

 

## <a name="govern-access-with-azure-ad-p1-and-microsoft--office-365-e3"></a>Reger o acesso com Azure AD P1 e Microsoft / Office 365 E3
Pode alcançar uma governação robusta com o Azure AD P1 e o Microsoft 365 E3

### <a name="provisioning-signing-in-reviewing-access-and-deprovisioning"></a>Provisionamento, inscrição, revisão do acesso e desprovisionamento


|Funcionalidade | Fornecimento de utilizadores externos| Impor requisitos de inscrição| Rever acesso| Acesso de desprovisionamento |
| - |-|-|-|-|
| Colaboração Azure AD B2B| **Convidar via e-mail, OTP, self-service**| Federação Direta B2B| **Revisão periódica por parceiro**| Remover conta<br>Restringir o sinal |
| Microsoft ou Office 365 Grupos| | | | Expiração ou supressão do grupo.<br>Remoção do grupo. |
| Grupos de segurança| | **Adicione utilizadores externos a grupos de segurança (org, equipa, projeto, etc.)**| |  |
| Políticas de Acesso Condicional| | **Políticas de acesso condicional de acesso condicionado para utilizadores externos**| |  |


 ### <a name="access-to-resources"></a>Acesso a recursos.

|Funcionalidade | APP & acesso a recursos| SharePoint & acesso OneDrive| Acesso das equipas| E-mail & segurança documental |
| - |-|-|-|-|
| Microsoft ou Office 365 Grupos| | **Acesso ao site(s) incluídos com grupo (e conteúdo associado)**|**Acesso a equipas incluídas com o grupo Microsoft 365 (e conteúdo associado)**|  |
| Etiquetas de confidencialidade| | Classificar manualmente e restringir o acesso| Classificar manualmente e restringir o acesso.| Classificar manualmente para restringir e encriptar |
| Políticas de acesso condicional| Políticas de acesso condicional para controlo de acessos| | |  |
| Métodos adicionais| | Restringir o acesso do site sharePoint granularmente com grupos de segurança.<br>Não permitir a partilha direta.| **Restringir convites externos de dentro de equipas**|  |


### <a name="next-steps"></a>Próximos passos

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Crie um plano de governação](3-secure-access-plan.md) (Está aqui.)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)