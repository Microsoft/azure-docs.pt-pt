---
title: Criar uma estratégia resiliente de gestão de controlo de acesso - Azure AD
description: Este documento fornece orientações sobre estratégias que uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante perturbações imprevistas
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ca5817e744ff81efcd549bc328d7ce5eeedb2d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908739"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Criar uma estratégia resiliente de gestão de controlo de acesso com o Diretório Ativo azure

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Uma vez que a Microsoft deve responder às alterações das condições de mercado, não deve ser interpretada como um compromisso por parte da Microsoft, e a Microsoft não pode garantir a exatidão de qualquer informação apresentada após a data de publicação.

As organizações que dependem de um controlo único de acesso, como a autenticação de vários fatores (MFA) ou uma única localização da rede, para garantir os seus sistemas informáticos são suscetíveis de aceder a falhas nas suas apps e recursos se esse controlo de acesso único ficar indisponível ou mal configurado. Por exemplo, uma catástrofe natural pode resultar na indisponibilidade de grandes segmentos de infraestruturas de telecomunicações ou redes empresariais. Tal perturbação poderia impedir que os utilizadores finais e os administradores pudessem iniciar o contrato.

Este documento fornece orientações sobre estratégias que uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante perturbações imprevistas com os seguintes cenários:

 1. As organizações podem aumentar a sua resiliência para reduzir o risco de bloqueio antes de **uma perturbação,** implementando estratégias de mitigação ou planos de contingência.
 2. As organizações podem continuar a aceder a apps e recursos que escolham **durante uma perturbação,** tendo estratégias de mitigação e planos de contingência em vigor.
 3. As organizações devem certificar-se de que preservam informações, como registos, **após uma interrupção** e antes de reverterem quaisquer contingências que implementaram.
 4. Organizações que não tenham implementado estratégias de prevenção ou planos alternativos podem ser capazes de implementar **opções** de emergência para lidar com a perturbação.

## <a name="key-guidance"></a>Orientação chave

Há quatro takeaways chave neste documento:

* Evite o bloqueio do administrador utilizando contas de acesso de emergência.
* Implementar MFA utilizando acesso condicional (CA) em vez de MFA por utilizador.
* Atenuar o bloqueio do utilizador utilizando vários controlos de acesso condicional (CA).
* Atenuar o bloqueio do utilizador, disponibilizando múltiplos métodos de autenticação ou equivalentes para cada utilizador.

## <a name="before-a-disruption"></a>Antes de uma rutura

Atenuar uma perturbação real deve ser o principal foco de uma organização na abordagem de questões de controlo de acesso que possam surgir. A mitigação inclui o planeamento de um evento real e estratégias de implementação para garantir que os controlos e operações de acesso não sejam afetados durante as perturbações.

### <a name="why-do-you-need-resilient-access-control"></a>Por que precisa de um controlo de acesso resistente?

 A identidade é o plano de controlo dos utilizadores que acedem a apps e recursos. O seu sistema de identidade controla quais os utilizadores e em que condições, tais como controlos de acesso ou requisitos de autenticação, os utilizadores têm acesso às aplicações. Quando um ou mais requisitos de autenticação ou controlo de acesso não estão disponíveis para os utilizadores autenticarem devido a circunstâncias imprevistas, as organizações podem experimentar uma ou ambas as seguintes questões:

* **Bloqueio do administrador:** Os administradores não podem gerir o inquilino ou os serviços.
* **Bloqueio do utilizador:** Os utilizadores não podem aceder a apps ou recursos.

### <a name="administrator-lockout-contingency"></a>Contingência de bloqueio de administrador

Para desbloquear o acesso do administrador ao seu inquilino, deve criar contas de acesso de emergência. Estas contas de acesso de emergência, também conhecidas como contas de *vidro de rutura,* permitem o acesso à configuração da AD Azure quando não existem procedimentos normais de acesso privilegiado à conta. Devem ser criadas pelo menos duas contas de acesso de emergência na sequência das recomendações da conta de [acesso de emergência.]( https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)

### <a name="mitigating-user-lockout"></a>Bloqueio de utilizador atenuante

 Para mitigar o risco de bloqueio do utilizador, utilize as políticas de Acesso Condicional com vários controlos para dar aos utilizadores uma escolha de como irão aceder a apps e recursos. Ao dar a um utilizador a escolha entre, por exemplo, iniciar sessão com mfa **ou** iniciar sessão a partir de um dispositivo gerido **ou** iniciar sessão a partir da rede corporativa, se um dos controlos de acesso estiver indisponível, o utilizador tem outras opções para continuar a trabalhar.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Incorpore os seguintes controlos de acesso nas suas políticas de acesso condicional existentes para organização:

1. Fornecer múltiplos métodos de autenticação para cada utilizador que dependa de diferentes canais de comunicação, por exemplo, a aplicação Microsoft Authenticator (baseada na Internet), o token OATH (gerado no dispositivo) e o SMS (telefonónico).
2. Implemente o Windows Hello for Business em dispositivos Windows 10 para satisfazer os requisitos do MFA diretamente a partir do acesso ao dispositivo.
3. Utilize dispositivos fidedignos através do [Azure AD Hybrid Join](https://docs.microsoft.com/azure/active-directory/devices/overview) ou [dos dispositivos geridos](https://docs.microsoft.com/intune/planning-guide)pela Microsoft Intune . Os dispositivos fidedignos melhorarão a experiência do utilizador porque o próprio dispositivo fidedigno pode satisfazer os fortes requisitos de autenticação da política sem um desafio mfa para o utilizador. O MFA será então necessário ao inscrever um novo dispositivo e ao aceder a apps ou recursos de dispositivos não confiáveis.
4. Utilize políticas baseadas em riscos de proteção de identidade Azure AD que impeçam o acesso quando o utilizador ou o inserido estiver em risco em vez de políticas fixas de MFA.

>[!NOTE]
> As políticas baseadas no risco requerem licenças [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

O exemplo seguinte descreve as políticas que deve criar para fornecer um controlo de acesso resiliente para o utilizador aceder às suas apps e recursos. Neste exemplo, irá requerer um grupo de segurança **AppUsers** com os utilizadores-alvo a que pretende dar acesso, um grupo chamado **CoreAdmins** com os administradores principais, e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência.
Este conjunto de políticas de exemplo irá conceder aos utilizadores selecionados em **AppUsers,** acesso a aplicações selecionadas se estiverem a ligar-se a partir de um dispositivo de confiança OU fornecerá uma autenticação forte, por exemplo, O MFA. Exclui contas de emergência e administradores centrais.

**Definição de políticas de mitigação da AC:**

* Política 1: Bloquear o acesso a pessoas fora dos grupos-alvo
  * Utilizadores e Grupos: Incluir todos os utilizadores. Excluir Utilizadores de Aplicações, CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Inclua todas as aplicações
  * Condições: (Nenhuma)
  * Controlo de Subvenções: Bloco
* Política 2: Conceder acesso a AppUsers que necessitem de dispositivo fidedigno MFA OU.
  * Utilizadores e Grupos: Incluir Utilizadores de Aplicações. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Inclua todas as aplicações
  * Condições: (Nenhuma)
  * Controlo de subvenção: O acesso ao subsídio, requer autenticação de vários fatores, exige que o dispositivo seja compatível. Para vários controlos: Exija um dos comandos selecionados.

### <a name="contingencies-for-user-lockout"></a>Contingências para bloqueio do utilizador

Em alternativa, a sua organização também pode criar políticas de contingência. Para criar políticas de contingência, deve definir critérios de compensação entre a continuidade do negócio, os custos operacionais, os custos financeiros e os riscos de segurança. Por exemplo, pode ativar uma política de contingência apenas a um subconjunto de utilizadores, para um subconjunto de aplicações, para um subconjunto de clientes, ou a partir de um subconjunto de localizações. As políticas de contingência darão aos administradores e utilizadores finais acesso a apps e recursos, durante uma perturbação quando não foi implementado nenhum método de mitigação.
Compreender a sua exposição durante uma perturbação ajuda a reduzir o seu risco e é uma parte crítica do seu processo de planeamento. Para criar o seu plano de contingência, determine primeiro os seguintes requisitos de negócio da sua organização:

1. Determine antecipadamente as suas aplicações críticas da sua missão: Quais são as aplicações a que deve dar acesso, mesmo com uma postura de menor risco/segurança? Construa uma lista destas aplicações e certifique-se de que os seus outros stakeholders (negócios, segurança, legal, liderança) todos concordam que se todo o controlo de acesso desaparecer, estas aplicações ainda devem continuar a funcionar. É provável que acabe com categorias de:
   * **Aplicações críticas** da missão de categoria 1 que não podem estar indisponíveis por mais de alguns minutos, por exemplo Apps que afetam diretamente as receitas da organização.
   * **Aplicações importantes de categoria 2** que o negócio precisa de ser acessível dentro de algumas horas.
   * **Aplicativos de baixa prioridade** de categoria 3 que podem suportar uma interrupção de alguns dias.
2. Para aplicações nas categorias 1 e 2, a Microsoft recomenda-lhe pré-planar o nível de acesso que pretende permitir:
   * Deseja permitir acesso total ou sessão restrita, como limitar os downloads?
   * Deseja permitir o acesso a parte da app, mas não a toda a aplicação?
   * Pretende permitir o acesso do trabalhador da informação e bloquear o acesso do administrador até que o controlo de acesso seja restaurado?
3. Para essas aplicações, a Microsoft também recomenda que planeie quais as vias de acesso que irá abrir deliberadamente e quais as que irá fechar:
   * Deseja permitir o acesso do navegador apenas e bloquear clientes ricos que possam guardar dados offline?
   * Pretende permitir o acesso apenas aos utilizadores dentro da rede corporativa e manter os utilizadores externos bloqueados?
   * Só pretende permitir o acesso de certos países ou regiões durante a interrupção?
   * Quer que as políticas para as políticas de contingência, especialmente para aplicações críticas da missão, falhem ou tenham sucesso se não houver um controlo de acesso alternativo?

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Uma política de acesso condicional de contingência é uma **política de desativação** que omite os controlos de MFA Azure, MFA de terceiros, baseados em riscos ou baseados em dispositivos. Em seguida, quando a sua organização decide ativar o seu plano de contingência, os administradores podem ativar a política e desativar as políticas regulares baseadas no controlo.

>[!IMPORTANT]
> Desativar as políticas que impõem a segurança dos seus utilizadores, mesmo que temporariamente, reduzirá a sua postura de segurança enquanto o plano de contingência estiver em vigor.

* Configure um conjunto de políticas de recuo se uma rutura num tipo de credencial ou um mecanismo de controlo de acesso tiver impacto no acesso às suas apps. Configure uma política em estado de desativação que exija a Adesão do Domínio como um controlo, como um backup para uma política ativa que requer um fornecedor de MFA de terceiros.
* Reduza o risco de maus atores adivinharem palavras-passe, quando o MFA não é necessário, seguindo as práticas no livro branco de orientação de [palavra-passe.](https://aka.ms/passwordguidance)
* Implemente o Reset de [Palavras-Passe autosserviço azure AD (SSPR)](https://docs.microsoft.com/azure/active-directory/authentication/quickstart-sspr) e a Proteção de [Passwords AD Azure](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-ban-bad-on-premises-deploy) para garantir que os utilizadores não usam senha e termos comuns que escolha banir.
* Utilize políticas que restringem o acesso dentro das aplicações se um determinado nível de autenticação não for atingido em vez de simplesmente voltar ao acesso total. Por exemplo:
  * Configure uma política de backup que envie a reivindicação restrita da sessão para Exchange e SharePoint.
  * Se a sua organização utilizar o Microsoft Cloud App Security, considere recuar para uma política que envolve o MCAS e, em seguida, o MCAS permite o acesso apenas a leitura, mas não o upload.
* Nomeie as suas políticas para garantir que é fácil encontrá-las durante uma perturbação. Incluir os seguintes elementos no nome da política:
  * Um número de *etiqueta* para a apólice.
  * Texto para mostrar, esta política é apenas para emergências. Por exemplo: **ATIVAR EM EMERGÊNCIA**
  * A *perturbação* a que se aplica. Por exemplo: **Durante a interrupção do MFA**
  * Um *número de sequência* para mostrar a ordem tem de ativar as políticas.
  * As *aplicações* a que se aplica.
  * Os *controlos* aplicar-se-ão.
  * As *condições* que requer.
  
Esta norma de nomeação para as políticas de contingência será a seguinte: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

O exemplo seguinte: **Exemplo A - Política de Contingência da AC para restaurar o Acesso a Aplicações de Colaboração críticas**de missão, é uma contingência corporativa típica. Neste cenário, a organização normalmente requer MFA para todos os acessos Exchange Online e SharePoint Online, e a perturbação neste caso é que o fornecedor de MFA para o cliente tem uma paragem (seja Azure MFA, fornecedor de MFA no local, ou MFA de terceiros). Esta política atenua esta falha, permitindo aos utilizadores específicos o acesso a estas aplicações a partir de dispositivos Windows fidedignos apenas quando acedem à aplicação a partir da sua rede corporativa de confiança. Excluirá igualmente as contas de emergência e os administradores fundamentais destas restrições. Os utilizadores visados terão então acesso ao Exchange Online e ao SharePoint Online, enquanto outros utilizadores ainda não terão acesso às aplicações devido à falha. Este exemplo exigirá uma localização de rede nomeada **CorpNetwork** e um grupo de segurança **ContingênciaAccess** com os utilizadores-alvo, um grupo chamado **CoreAdmins** com os administradores principais, e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência. A contingência requer quatro políticas para fornecer o acesso desejado. 

**Exemplo A - Políticas de Contingência da AC para restaurar o acesso a aplicações de colaboração críticas da missão:**

* Política 1: Exigir dispositivos juntos ao domínio para troca e sharePoint
  * Nome: EM001 - ENABLE IN EMERGENCY: MFA Disruption[1/4] - Exchange SharePoint - Require Hybrid Azure AD Join
  * Utilizadores e Grupos: Incluir O Acesso de Contingência. Excluir CoreAdmins e EmergencyAccess
  * Cloud Apps: Exchange Online e SharePoint Online
  * Condições: Qualquer
  * Controlo de subvenções: Exigir a adesão ao domínio
  * Estado: Deficientes
* Política 2: Bloquear plataformas que não o Windows
  * Nome: EM002 - ENABLE IN EMERGENCY: MFA Disruption[2/4] - Exchange SharePoint - Acesso ao bloco exceto Windows
  * Utilizadores e Grupos: Incluir todos os utilizadores. Excluir CoreAdmins e EmergencyAccess
  * Cloud Apps: Exchange Online e SharePoint Online
  * Condições: Plataforma de dispositivos Incluir todas as plataformas, excluir Windows
  * Controlo de Subvenções: Bloco
  * Estado: Deficientes
* Política 3: Bloquear redes que não a CorpNetwork
  * Nome: EM003 - ENABLE IN EMERGENCY: MFA Disruption[3/4] - Exchange SharePoint - Acesso ao bloco exceto Rede Corporativa
  * Utilizadores e Grupos: Incluir todos os utilizadores. Excluir CoreAdmins e EmergencyAccess
  * Cloud Apps: Exchange Online e SharePoint Online
  * Condições: Localizações Incluam qualquer localização, excluem CorpNetwork
  * Controlo de Subvenções: Bloco
  * Estado: Deficientes
* Política 4: Bloquear explicitamente a EAS
  * Nome: EM004 - ENABLE IN EMERGENCY: MFA Disruption[4/4] - Exchange - Block EAS para todos os utilizadores
  * Utilizadores e Grupos: Incluir todos os utilizadores
  * Aplicativos cloud: Inclua intercâmbio online
  * Condições: Aplicativos de cliente: Exchange Ative Sync
  * Controlo de Subvenções: Bloco
  * Estado: Deficientes

Ordem de ativação:

1. Excluir O ContingênciaAccess, CoreAdmins e EmergencyAccess da política de MFA existente. Verifique um utilizador no ContingênciaAccess pode aceder ao SharePoint Online e trocar online.
2. Ativar a Política 1: Verifique os utilizadores em dispositivos Juntos ao Domínio que não estejam nos grupos de exclusão são capazes de aceder ao Exchange Online e ao SharePoint Online. Verificar que os utilizadores do grupo Excluir podem aceder ao SharePoint Online e ao Exchange a partir de qualquer dispositivo.
3. Ativar a Política 2: Verificar os utilizadores que não estão no grupo de exclusão não podem chegar ao SharePoint Online e trocar online a partir dos seus dispositivos móveis. Verificar os utilizadores do grupo Excluir pode aceder ao SharePoint e ao Exchange a partir de qualquer dispositivo (Windows/iOS/Android).
4. Ativar a Política 3: Verificar os utilizadores que não estejam nos grupos de exclusão não podem aceder ao SharePoint e trocar a rede corporativa, mesmo com uma máquina unida de domínio. Verificar que os utilizadores do grupo Excluir podem aceder ao SharePoint e ao Exchange de qualquer rede.
5. Ativar a Política 4: Verifique que todos os utilizadores não podem obter o Exchange Online a partir das aplicações de correio nativo em dispositivos móveis.
6. Desative a política de MFA existente para SharePoint Online e Exchange Online.

Neste próximo exemplo, **as políticas de A. B - Contingência para permitir o acesso móvel ao Salesforce**, o acesso de uma aplicação de negócios é restaurado. Neste cenário, o cliente normalmente exige que os seus colaboradores de vendas tenham acesso à Salesforce (configurada para um único sinal com a Azure AD) a partir de dispositivos móveis para serem apenas permitidos a partir de dispositivos conformes. A perturbação neste caso é que há um problema na avaliação da conformidade do dispositivo e a paralisação está a acontecer num momento sensível em que a equipa de vendas precisa de acesso à Salesforce para fechar negócios. Estas políticas de contingência vão permitir aos utilizadores críticos o acesso à Salesforce a partir de um dispositivo móvel para que possam continuar a fechar negócios e não perturbar o negócio. Neste exemplo, a **SalesforceContingência contém** todos os colaboradores de Vendas que precisam de reter acesso e **salesAdmins** contém os administradores necessários da Salesforce.

**Exemplo B - Políticas de Contingência DA:**

* Política 1: Bloqueie todos os que não estão na equipa de Contingência de Vendas
  * Nome: EM001 - ENABLE IN EMERGENCY: Device Compliance Disruption[1/2] - Salesforce - Block Todos os utilizadores, exceto SalesforceContingência
  * Utilizadores e Grupos: Incluir todos os utilizadores. Excluir SalesAdmins e SalesforceContingência
  * Cloud Apps: Salesforce.
  * Condições: nenhuma
  * Controlo de Subvenções: Bloco
  * Estado: Deficientes
* Política 2: Bloquear a equipa de Vendas de qualquer outra plataforma que não seja móvel (para reduzir a área de ataque da superfície)
  * Nome: EM002 - ENABLE IN EMERGENCY: Device Compliance Disruption[2/2] - Salesforce - Block All platforms exceto iOS e Android
  * Utilizadores e Grupos: Incluir A Contingência Salesforce. Excluir SalesAdmins
  * Cloud Apps: Salesforce
  * Condições: Plataforma de Dispositivos Inclui todas as plataformas, exclua iOS e Android
  * Controlo de Subvenções: Bloco
  * Estado: Deficientes

Ordem de ativação:

1. Excluir SalesAdmins e SalesforceContingência da política de conformidade de dispositivos existente para a Salesforce. Verifique se um utilizador do grupo SalesforceContingênciapode aceder à Salesforce.
2. Ativar a Política 1: Verificar utilizadores fora do SalesContingênciay não pode aceder à Salesforce. Verifique os utilizadores nas SalesAdmins e SalesforceContingência pode aceder à Salesforce.
3. Ativar a Política 2: Verificar os utilizadores do grupo SalesContingênciay não pode aceder ao Salesforce a partir dos seus portáteis Windows/Mac, mas ainda pode aceder a partir dos seus dispositivos móveis. Verifique que a SalesAdmin ainda pode aceder à Salesforce a partir de qualquer dispositivo.
4. Desative a política de conformidade do dispositivo existente para a Salesforce.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Implemente a sincronização de hash de senha mesmo que seja federado ou utilize a autenticação pass-through

O bloqueio do utilizador também pode ocorrer se as seguintes condições forem verdadeiras:

- A sua organização utiliza uma solução de identidade híbrida com autenticação ou federação pass-through.
- Os seus sistemas de identidade no local (tais como Diretório Ativo, AD FS ou um componente dependente) não estão disponíveis. 
 
Para ser mais resiliente, a sua organização deve ativar a sincronização de hash de [palavra-passe,](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)pois permite-lhe mudar para a utilização de [sincronde](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-user-signin) de hash de palavra-passe se os seus sistemas de identidade no local estiverem desligados.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft
 Ative a sincronização de hash de palavra-passe utilizando o assistente Azure AD Connect, independentemente de a sua organização utilizar a federação ou a autenticação pass-through.

>[!IMPORTANT]
> Não é necessário converter utilizadores de autenticação federada para gerida para utilizar sincronização de hash password.

## <a name="during-a-disruption"></a>Durante uma rutura

Se optou por implementar um plano de mitigação, poderá sobreviver automaticamente a uma única perturbação do controlo de acesso. No entanto, se optou por criar um plano de contingência, poderá ativar as suas políticas de contingência durante a interrupção do controlo de acesso:

1. Ative as suas políticas de contingência que concedem aos utilizadores direcionados, acesso a aplicações específicas, a partir de redes específicas.
2. Desative as suas políticas regulares baseadas no controlo.

### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Dependendo das atenuações ou contingências utilizadas durante uma perturbação, a sua organização pode estar a conceder acesso apenas com senhas. Nenhuma salvaguarda constitui um risco considerável de segurança que deve ser ponderado cuidadosamente. As organizações devem:

1. Como parte da sua estratégia de controlo de mudanças, documente todas as alterações e o estado anterior para poder reverter quaisquer contingências que implementou assim que os controlos de acesso estiverem totalmente operacionais.
2. Assuma que os atores maliciosos tentarão recolher senhas através de spray de senha ou ataques de phishing enquanto desativa mFA. Além disso, os maus atores podem já ter senhas que anteriormente não concedam acesso a qualquer recurso que possa ser tentado durante esta janela. Para utilizadores críticos como executivos, pode mitigar parcialmente este risco redefinindo as suas palavras-passe antes de desativar o MFA para eles.
3. Arquivar toda a atividade de entrada para identificar quem acede ao que durante o tempo em que o MFA foi desativado.
4. [Triagem todas as deteções de risco reportadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) durante esta janela.

## <a name="after-a-disruption"></a>Depois de uma rutura

Desfazer as alterações que fez como parte do plano de contingência ativado assim que o serviço for restaurado que causou a perturbação. 

1. Ativar as políticas regulares
2. Desative as suas políticas de contingência. 
3. Recue quaisquer outras alterações que tenha feito e documentado durante a interrupção.
4. Se usou uma conta de acesso de emergência, lembre-se de regenerar credenciais e proteger fisicamente os novos detalhes das credenciais como parte dos procedimentos da sua conta de acesso de emergência.
5. Continue a [triagem de todas as deteções de risco reportadas](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) após a interrupção para atividades suspeitas.
6. Revogue todos os tokens de atualização que foram emitidos [usando powerShell](https://docs.microsoft.com/powershell/module/azuread/revoke-azureaduserallrefreshtoken?view=azureadps-2.0) para direcionar um conjunto de utilizadores. Revogar todas as fichas de atualização é importante para contas privilegiadas utilizadas durante a interrupção e fazê-lo irá forçá-los a reautenticar e cumprir o controlo das políticas restauradas.

## <a name="emergency-options"></a>Opções de emergência

 Em caso de emergência e a sua organização não implementou previamente um plano de mitigação ou contingência, siga as recomendações na secção [contingências para](#contingencies-for-user-lockout) o bloqueio do utilizador se já utilizarem políticas de Acesso Condicional para impor o MFA.
Se a sua organização estiver a usar políticas de legado mfa por utilizador, então pode considerar a seguinte alternativa:

1. Se tiver o endereço IP de saída da rede corporativa, pode adicioná-los como IPs fidedignos para permitir a autenticação apenas à rede corporativa.
   1. Se não tiver o inventário de endereços IP de saída, ou necessário para permitir o acesso dentro e fora da rede corporativa, pode adicionar todo o espaço de endereçoI4 como IPs fidedigno, especificando 0.0.0.0/1 e 128.0.0.0.0.0.0.0.0.0.0/1.

>[!IMPORTANT]
 > Se alargar os endereços IP confiáveis para desbloquear o acesso, não serão geradas deteções de risco associadas a endereços IP (por exemplo, viagens impossíveis ou locais desconhecidos).

>[!NOTE]
 > Configurar [iPs fidedignos](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-mfasettings) para O MFA Azure só está disponível com [licenças Azure AD Premium.](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-licensing)

## <a name="learn-more"></a>Saiba mais

* [Documentação da Autenticação do Azure AD](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-iis)
* [Gerir contas administrativas de acesso de emergência em Azure AD](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access)
* [Configure locais nomeados em Diretório Ativo Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations)
  * [Set-MsolDomainFederationSettings](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)
* [Como configurar o Diretório Ativo Azure híbrido juntou-se a dispositivos](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-plan)
* [Guia de Implementação do Windows Hello para Empresas](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Orientação de passwords - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Quais são as condições no Acesso Condicional do Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/conditions)
* [O que são os controlos de acesso no Acesso Condicional do Diretório Ativo Azure?](https://docs.microsoft.com/azure/active-directory/conditional-access/controls)
