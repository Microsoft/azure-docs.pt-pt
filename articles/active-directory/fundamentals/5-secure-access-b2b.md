---
title: Transição para colaboração governada com a Azure Ative Directory B2B Collaboration
description: Mover-se para a colaboração governada com a colaboração Azure Ad B2B.
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
ms.openlocfilehash: 654e1c163a16f427958320ea081c38338e0ab4e1
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98725397"
---
# <a name="transition-to-governed-collaboration-with-azure-active-directory-b2b-collaboration"></a>Transição para colaboração governada com a colaboração B2B do Azure Ative Directory 

Controlar a sua colaboração é fundamental para garantir o acesso externo aos seus recursos. Antes de avançar com este artigo, certifique-se de que tem:

* [Determinou a sua postura de segurança](1-secure-access-posture.md)

* [Descobriu o seu estado atual](2-secure-access-current-state.md)

* [Criei um plano de segurança](3-secure-access-plan.md)

* [Compreendido como grupos e segurança trabalham juntos](4-secure-access-groups.md)

Depois de fazeres essas coisas, estás pronto para entrar em colaboração controlada. Este artigo irá guiá-lo a mover toda a sua colaboração externa para [a colaboração do Azure Ative Directory B2B](../external-identities/what-is-b2b.md) (Azure AD B2B). Azure Ad B2B é uma característica das [identidades externas da AD Azure.](../external-identities/compare-with-b2c.md)

## <a name="control-who-your-organization-collaborates-with"></a>Controle com quem a sua organização colabora

Tem de decidir se limita quais as organizações com as quais os seus utilizadores podem colaborar e com quem dentro da sua organização pode iniciar a colaboração. A maioria das organizações toma a abordagem de permitir que as unidades de negócio decidam com quem colaboram, e delegando a aprovação e supervisão conforme necessário. Por exemplo, algumas organizações governamentais, de educação e de serviços financeiros não permitem uma colaboração aberta. Pode desejar utilizar as funcionalidades AZURE AD para a colaboração no âmbito, como discutido no resto desta secção.

### <a name="determine-collaboration-partners"></a>Determinar parceiros de colaboração

Em primeiro lugar, certifique-se de que documentou as organizações com as qual está a colaborar, e os domínios para os utilizadores dessas organizações. Um parceiro de colaboração pode ter vários domínios. Por exemplo, um parceiro pode ter várias unidades de negócio com domínios separados.

Em seguida, determine se deseja ativar a colaboração futura com 

* qualquer domínio (mais inclusivo)

* todos os domínios, exceto aqueles explicitamente negados 

* apenas domínios específicos (mais restritivos)

> [!NOTE]
> Quanto mais restritivas forem as definições de colaboração, maior é a probabilidade de os seus utilizadores saírem do seu quadro de colaboração aprovado. Recomendamos que permita a colaboração mais ampla que as suas necessidades de segurança permitirão, e reveja de perto essa colaboração em vez de ser excessivamente restritiva. 

Note também que a limitação a um único domínio pode inadvertidamente impedir a colaboração autorizada com organizações, que têm outros domínios não relacionados para os seus utilizadores. Por exemplo, se fizer negócios com uma organização Contoso, o ponto inicial de contacto com a Contoso pode ser um dos seus colaboradores baseados nos EUA que tem um e-mail com um domínio ".com". No entanto, se permitir apenas o domínio ".com", poderá omitir inadvertidamente os seus empregados canadianos que têm domínio ".ca". 

Existem circunstâncias em que gostaria apenas de permitir parceiros específicos de colaboração. Por exemplo, um sistema universitário só pode querer permitir o acesso do seu próprio corpo docente a um inquilino de recursos. Ou um conglomerado só pode querer permitir que subsidiárias específicas colaborem entre si para alcançar o cumprimento de um quadro necessário.

#### <a name="using-allow-and-deny-lists"></a>Utilizar listas de permitir e negar

Pode utilizar uma lista de autorizações ou negar lista para [restringir convites a utilizadores B2B](../external-identities/allow-deny-list.md) de organizações específicas. Pode usar apenas uma lista de admissões ou de negação, não ambos.

* Uma [lista de autorizações](../external-identities/allow-deny-list.md) limita a colaboração apenas aos domínios listados; todos os outros domínios estão efetivamente na lista de negação.

* Uma [lista de negação](../external-identities/allow-deny-list.md) permite a colaboração com qualquer domínio que não conste da lista de negação.

> [!IMPORTANT]
> Estas listas não se aplicam a utilizadores que já se encontram no seu diretório. Também não se aplicam ao OneDrive para Negócios e o SharePoint permite listas de negação que são separadas.

Algumas organizações usam uma lista de domínios conhecidos de "mau ator" fornecidos pelo seu fornecedor de segurança gerido para a sua lista de negação. Por exemplo, se a organização está legitimamente a fazer negócios com a Contoso e a usar um domínio .com, pode haver uma organização não relacionada que tem usado o domínio Contoso .org e tentando um ataque de phishing para se fazer passar por funcionários da Contoso. 

## <a name="control-how-external-users-gain-access"></a>Controlar como os utilizadores externos têm acesso

Existem muitas formas de colaborar com parceiros externos usando a Azure AD B2B. Para iniciar a colaboração, convida ou de outra forma permite ao seu parceiro aceder aos seus recursos. Os utilizadores podem ter acesso respondendo a:

* Resgatar [um convite enviado através de um e-mail,](../external-identities/redemption-experience.md)ou um link direto para [partilhar](../external-identities/redemption-experience.md) um recurso.

* Solicitando acesso através de [uma aplicação](../external-identities/self-service-sign-up-overview.md) que cria

* Solicitando acesso através do portal [O Meu Acesso](../governance/entitlement-management-request-access.md)

Ao ativar o Azure AD B2B, ativa a capacidade de convidar utilizadores convidados através de links diretos e convites por e-mail por padrão. Os convites via OTP e um portal de self-service estão atualmente em pré-visualização e devem ser ativados dentro das Identidades Externas | Definições de colaboração externa no portal AD Azure.

### <a name="control-who-can-invite-guest-users"></a>Controle quem pode convidar utilizadores convidados

Determine quem pode convidar os utilizadores convidados a aceder aos recursos.

* A configuração mais restritiva é permitir apenas administradores e os utilizadores que concedem o [papel de convidado](../external-identities/delegate-invitations.md) convidado para convidar os hóspedes.

* Se os seus requisitos de segurança o permitirem, recomendamos que todos os utilizadores com um utilizadorType do Membro convidem os hóspedes.

* Determine se deseja que os utilizadores com um utilizadorType do Guest, que é o tipo de conta padrão para utilizadores AZure AD B2B, possam convidar outros hóspedes. 

![Screenshot das configurações do convite dos convidados.](media/secure-external-access/5-guest-invite-settings.png)

 

### <a name="collect-additional-information-about-external-users"></a>Recolher informações adicionais sobre utilizadores externos

Se utilizar a gestão de direitos Azure AD, pode configurar perguntas para utilizadores externos responderem. As perguntas serão então apresentadas aos aprovadores para ajudá-los a tomar uma decisão. Pode configurar diferentes conjuntos de perguntas para cada política de [pacotes](../governance/entitlement-management-access-package-approval-policy.md) de acesso para que os aprovadores possam ter informações relevantes para o acesso que estão a aprovar. Por exemplo, se um pacote de acesso for destinado ao acesso do fornecedor, então o solicitador pode ser solicitado para o seu número de contrato de fornecedor. Um pacote de acesso diferente destinado aos fornecedores pode solicitar o seu país de origem.

Se utilizar um portal de self-service, pode utilizar [conectores API](../external-identities/api-connectors-overview.md) para recolher atributos adicionais sobre os utilizadores à medida que se inscrevem. Em seguida, pode potencialmente usar esses atributos para atribuir acesso. Por exemplo, se durante o processo de inscrição recolher o seu ID de fornecedor, poderá utilizar esse atributo para atribuí-los dinamicamente a um grupo ou pacote de acesso a esse fornecedor. Pode criar atributos personalizados no portal Azure e usá-los nos fluxos de utilizador de inscrição de autosserviço. Também pode ler e escrever estes atributos utilizando a [API](../../active-directory-b2c/manage-user-accounts-graph-api.md)do Gráfico microsoft . 

### <a name="troubleshoot-invitation-redemption-to-azure-ad-users"></a>Resgate de convites de resolução de problemas para utilizadores da AD Azure

Existem três casos em que utilizadores convidados convidados de um parceiro de colaboração que usam a Azure AD terão dificuldade em resgatar um convite.

* Se utilizar uma lista de autorizações e o domínio do utilizador não estiver incluído numa lista de admissões.

* Se o inquilino do parceiro de colaboração tiver restrições de inquilino que impeçam a colaboração com utilizadores externos..

* Se o utilizador não fizer parte do inquilino da AD Azure do parceiro. Por exemplo, há utilizadores em contoso.com que estão apenas no Ative Directory (ou em outro IdP no local), só poderão resgatar convites através do processo OTP de e-mail. para obter mais informações, consulte o fluxo de resgate de [convites](../external-identities/redemption-experience.md).

## <a name="control-what-external-users-can-access"></a>Controlar o que os utilizadores externos podem aceder

A maioria das organizações não são monolíticas. Ou seja, existem alguns recursos que são bons para partilhar com utilizadores externos, e alguns não vão querer que utilizadores externos acedam. Portanto, deve controlar o acesso dos utilizadores externos. Considere usar [pacotes de gestão de direitos e acesso para controlar o acesso](6-secure-access-entitlement-managment.md) a recursos específicos.

Por padrão, os utilizadores convidados podem ver informações e atributos sobre membros do inquilino e outros parceiros, incluindo membros do grupo. Considere se os seus requisitos de segurança exigem a limitação do acesso externo do utilizador a estas informações.

![Screenshot de configurar definições de colaboração externa.](media/secure-external-access/5-external-collaboration-settings.png)

Recomendamos as seguintes restrições para os utilizadores convidados.

* **Limite o acesso dos hóspedes a grupos de navegação e outras propriedades no diretório**

   * Use as definições de colaboração externa para restringir a capacidade de leitura de grupos de que não são membros.

* **Bloqueie o acesso a aplicações apenas para funcionários.**

   * Crie uma política de Acesso Condicional para bloquear o acesso a aplicações integradas AZure AD que só são adequadas para utilizadores não convidados. 

* **Bloqueie o acesso ao portal Azure. Pode fazer raras exceções necessárias.** 

   * Crie uma política de acesso condicional que inclua todos os utilizadores convidados e externos e, em seguida, [implemente uma política para bloquear o acesso](../../role-based-access-control/conditional-access-azure-management.md).

 

## <a name="remove-users-who-no-longer-need-access"></a>Remova os utilizadores que já não precisam de acesso

Avalie o acesso atual para que possa [rever e remover utilizadores que já não precisam de acesso.](../governance/access-reviews-external-users.md) Inclua utilizadores externos no seu inquilino como hóspedes, e aqueles com contas de membro. 

Algumas organizações adicionaram utilizadores externos, como fornecedores, parceiros e empreiteiros como membros. Estes membros podem ter um atributo específico, ou nomes de utilizador que começam com, por exemplo,

* v- para os fornecedores

* p- para parceiros

* c- para empreiteiros

Avalie quaisquer utilizadores externos com contas dos membros para determinar se ainda precisam de acesso. Em caso afirmativo, transite estes utilizadores para Azure AD B2B, conforme descrito na secção seguinte.

Você também pode ter utilizadores convidados que não foram convidados através de Gestão de Direitos ou Azure AD B2B

Para encontrar estes utilizadores, pode:

* [Encontre utilizadores convidados não convidados através da Gestão de Direitos.](../governance/access-reviews-external-users.md)

   * Fornecemos um [script SAMPLE PowerShell.](https://github.com/microsoft/access-reviews-samples/tree/master/ExternalIdentityUse)

Transitar estes utilizadores para utilizadores Azure AD B2B, conforme descrito na secção seguinte.

## <a name="transition-your-current-external-users-to-b2b"></a>Transitar os seus atuais utilizadores externos para B2B

Se não tem usado Azure AD B2B, provavelmente tem utilizadores não empregados no seu inquilino. Recomendamos que faça a transição destas contas para contas externas de utilizadores Azure AD B2B e, em seguida, altere o Seu UserType para Guest. Isto permite-lhe tirar partido das muitas formas que o AZure AD e o Microsoft 365 permitem tratar os utilizadores externos de forma diferente. Algumas destas formas incluem:

* Facilmente incluindo ou excluindo utilizadores convidados em políticas de acesso condicional

* Facilmente incluindo ou excluindo utilizadores convidados em pacotes de acesso e avaliações de acesso

* Facilmente incluindo ou excluindo o acesso externo a Equipas, SharePoint e outros recursos.

Para fazer a transição destes utilizadores internos, mantendo o seu acesso atual, UPN e membros do grupo, consulte [convidar utilizadores externos para a colaboração B2B](../external-identities/invite-internal-users.md).

## <a name="decommission-undesired-collaboration-methods"></a>Desmantelar métodos de colaboração não apoiados

Para completar a sua transição para a colaboração governada, deve desmantelar métodos de colaboração não-desmedidos. O que desmantela baseia-se no grau de controlo que deseja que a TI exerça sobre a colaboração e a sua postura de segurança. Para obter informações sobre o controlo de TI versus utilizador final, consulte [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md).

Seguem-se os veículos de colaboração que poderá querer avaliar.

### <a name="direct-invitation-through-microsoft-teams"></a>Convite direto através das Equipas microsoft

Por predefinição, as equipas permitem o acesso externo, o que significa que a organização pode comunicar com todos os domínios externos. Se pretender restringir ou permitir domínios específicos apenas para equipas, pode fazê-lo no [portal Teams Admin](https://admin.teams.microsoft.com/company-wide-settings/external-communications). 


### <a name="direct-sharing-through-sharepoint-and-onedrive"></a>Partilha direta através do SharePoint e do OneDrive

A partilha direta através do SharePoint e do OneDrive pode adicionar utilizadores fora do processo de Gestão de Direitos. Para uma análise aprofundada destas configurações, consulte [o Manage Access com as Microsoft Teams, SharePoint e OneDrive para negócios,](9-secure-access-teams-sharepoint.md) pode também bloquear o uso do [OneDrive pessoal do utilizador,](/office365/troubleshoot/group-policy/block-onedrive-use-from-office) se assim o desejar.

### <a name="sending-documents-through-email"></a>Envio de documentos por e-mail

Os seus utilizadores enviarão documentos por e-mail para utilizadores externos. Considere como pretende restringir e encriptar o acesso a estes documentos utilizando etiquetas de sensibilidade. Para obter mais informações, consulte Gerir o acesso com as etiquetas Sensibilidade.

### <a name="unsanctioned-collaboration-tools"></a>Ferramentas de colaboração não esmanadas

A paisagem das ferramentas de colaboração é vasta. Os seus utilizadores provavelmente usam muitos fora dos seus deveres oficiais, incluindo plataformas como Google Docs, DropBox, Slack ou Zoom. É possível bloquear o uso de tais ferramentas a partir de uma rede corporativa ao nível de Firewall e com a gestão de aplicações móveis para dispositivos geridos pela organização. No entanto, isto também irá bloquear quaisquer casos sancionados destas plataformas e não iria bloquear o acesso a dispositivos não geridos. Bloqueie plataformas que não queira utilizar se necessário, e crie políticas de negócios para não utilizar as plataformas que precisa de utilizar. 

Para obter mais informações sobre a gestão de aplicações não proibidas, consulte:

* [Governar aplicações ligadas](/cloud-app-security/governance-actions)

* [Sancionar e não sancionar um pedido.](/cloud-app-security/governance-discovery)

 
### <a name="next-steps"></a>Próximos passos

Consulte os seguintes artigos sobre a garantia do acesso externo aos recursos. Recomendamos que tome as ações na ordem listada.

1. [Determine a sua postura de segurança para acesso externo](1-secure-access-posture.md)

2. [Descubra o seu estado atual](2-secure-access-current-state.md)

3. [Criar um plano de governação](3-secure-access-plan.md)

4. [Utilize grupos para segurança](4-secure-access-groups.md)

5. [Transição para Azure AD B2B](5-secure-access-b2b.md) (Você está aqui.)

6. [Acesso seguro com Gestão de Direitos](6-secure-access-entitlement-managment.md)

7. [Acesso seguro com políticas de acesso condicional](7-secure-access-conditional-access.md)

8. [Acesso seguro com etiquetas de sensibilidade](8-secure-access-sensitivity-labels.md)

9. [Acesso seguro a Microsoft Teams, OneDrive e SharePoint](9-secure-access-teams-sharepoint.md)