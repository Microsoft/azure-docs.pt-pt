---
title: Criar uma estratégia resiliente de gestão de controlo de acessos - Azure AD
description: Este documento fornece orientações sobre estratégias que uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante perturbações imprevistas
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.workload: identity
ms.date: 06/08/2020
ms.author: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7e4d0c41990fcc23dd19b5682997f6381bfdb20
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "97937098"
---
# <a name="create-a-resilient-access-control-management-strategy-with-azure-active-directory"></a>Crie uma estratégia resiliente de gestão de controlo de acessos com o Azure Ative Directory

>[!NOTE]
> The information contained in this document represents the current view of Microsoft Corporation on the issues discussed as of the date of publication. Uma vez que a Microsoft tem de responder às mudanças nas condições de mercado, não deve ser interpretada como um compromisso por parte da Microsoft, e a Microsoft não pode garantir a exatidão de qualquer informação apresentada após a data de publicação.

As organizações que dependem de um único controlo de acesso, como a autenticação de vários fatores (MFA) ou uma única localização de rede, para garantir os seus sistemas de TI são suscetíveis de aceder a falhas nas suas apps e recursos se esse controlo de acesso único ficar indisponível ou mal configurado. Por exemplo, uma catástrofe natural pode resultar na indisponibilidade de grandes segmentos de infraestruturas de telecomunicações ou de redes empresariais. Esta perturbação poderia impedir que os utilizadores finais e os administradores pudessem entrar.

Este documento fornece orientações sobre estratégias que uma organização deve adotar para fornecer resiliência para reduzir o risco de bloqueio durante perturbações imprevistas com os seguintes cenários:

 1. As organizações podem aumentar a sua resiliência para reduzir o risco de bloqueio antes de **uma perturbação,** implementando estratégias de mitigação ou planos de contingência.
 2. As organizações podem continuar a aceder a apps e recursos que escolherem **durante uma interrupção,** tendo estratégias de mitigação e planos de contingência em vigor.
 3. As organizações devem certificar-se de que preservam a informação, como registos,  **após uma interrupção** e antes de reverterem quaisquer contingências que implementaram.
 4. Organizações que não implementaram estratégias de prevenção ou planos alternativos podem ser capazes de implementar **opções de emergência** para lidar com a perturbação.

## <a name="key-guidance"></a>Orientação chave

Há quatro principais takeaways neste documento:

* Evite o bloqueio do administrador utilizando contas de acesso de emergência.
* Implementar MFA usando o Acesso Condicional em vez de por utilizador MFA.
* Atenuar o bloqueio do utilizador utilizando vários controlos de acesso condicional.
* Atenuar o bloqueio do utilizador fornecendo vários métodos de autenticação ou equivalentes para cada utilizador.

## <a name="before-a-disruption"></a>Antes de uma rutura

Atenuar uma perturbação real deve ser o principal foco de uma organização no tratamento de problemas de controlo de acesso que possam surgir. A mitigação inclui o planeamento de um evento real, além de implementar estratégias para garantir que os controlos e operações de acesso não sejam afetados durante as perturbações.

### <a name="why-do-you-need-resilient-access-control"></a>Por que precisa de um controlo de acesso resiliente?

 Identidade é o plano de controlo dos utilizadores que acedem a apps e recursos. O seu sistema de identidade controla quais os utilizadores e em que condições, como controlos de acesso ou requisitos de autenticação, os utilizadores têm acesso às aplicações. Quando um ou mais requisitos de autenticação ou controlo de acesso não estão disponíveis para os utilizadores autenticar em circunstâncias imprevistas, as organizações podem experimentar uma ou ambas as seguintes questões:

* **Bloqueio do administrador:** Os administradores não podem gerir o inquilino ou os serviços.
* **Bloqueio do utilizador:** Os utilizadores não podem aceder a apps ou recursos.

### <a name="administrator-lockout-contingency"></a>Contingência de bloqueio do administrador

Para desbloquear o acesso administrativo ao seu inquilino, deverá criar contas de acesso de emergência. Estas contas de acesso de emergência, também conhecidas como contas *de vidro quebrado,* permitem o acesso à configuração AD do Azure quando os procedimentos normais de acesso à conta privilegiada não estão disponíveis. Pelo menos duas contas de acesso de emergência devem ser criadas seguindo as [recomendações]( ../users-groups-roles/directory-emergency-access.md)da conta de acesso de emergência .

### <a name="mitigating-user-lockout"></a>Atenuando o bloqueio do utilizador

 Para mitigar o risco de bloqueio do utilizador, utilize políticas de Acesso Condicional com múltiplos controlos para dar aos utilizadores uma escolha de como irão aceder a apps e recursos. Ao dar a um utilizador a escolha entre, por exemplo, iniciar sessão com MFA **ou** iniciar sessão a partir de um dispositivo gerido **ou** iniciar sessão a partir da rede corporativa, se um dos controlos de acesso não estiver disponível, o utilizador tem outras opções para continuar a trabalhar.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Incorpore os seguintes controlos de acesso nas suas políticas de acesso condicional existentes para organização:

1. Disponibilização de múltiplos métodos de autenticação para cada utilizador que se baseiam em diferentes canais de comunicação, por exemplo, a aplicação Microsoft Authenticator (baseada na Internet), o token OATH (gerado no dispositivo) e o SMS (telefonia). O seguinte script PowerShell irá ajudá-lo a identificar com antecedência, quais os métodos adicionais que os seus utilizadores devem registar: [Script for Azure AD MFA análise de método de autenticação](/samples/azure-samples/azure-mfa-authentication-method-analysis/azure-mfa-authentication-method-analysis/).
2. Implementar o Windows Hello for Business em dispositivos Windows 10 para satisfazer os requisitos de MFA diretamente a partir do sôm-in do dispositivo.
3. Utilize dispositivos fidedignos através de dispositivos [Azure AD Hybrid Join](../devices/overview.md) ou [Microsoft Intune Managed](/intune/planning-guide). Os dispositivos fidedignos melhorarão a experiência do utilizador porque o próprio dispositivo de confiança pode satisfazer os fortes requisitos de autenticação da política sem um desafio MFA ao utilizador. O MFA será então necessário ao inscrever um novo dispositivo e ao aceder a apps ou recursos de dispositivos não fidedqui nem fided contas.
4. Utilize políticas baseadas em risco de proteção de identidade Azure AD que impeçam o acesso quando o utilizador ou o início de sação estão em risco em vez de políticas fixas de MFA.
5. Se estiver a proteger o acesso VPN utilizando a extensão NPS do MFA Azure AD, considere a sua solução VPN como uma [aplicação SAML](../manage-apps/view-applications-portal.md) e determine a categoria de aplicação conforme recomendado abaixo. 

>[!NOTE]
> As políticas baseadas no risco requerem licenças [Azure AD Premium P2.](https://azure.microsoft.com/pricing/details/active-directory/)

O exemplo a seguir descreve as políticas que deve criar para fornecer um controlo de acesso resiliente para o utilizador aceder às suas apps e recursos. Neste exemplo, você vai precisar de um grupo de segurança **AppUsers** com os utilizadores-alvo a que você deseja dar acesso, um grupo chamado **CoreAdmins** com os administradores principais, e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência.
Este conjunto de políticas de exemplo irá conceder aos utilizadores selecionados em **AppUsers,** acesso a aplicações selecionadas se estiverem a ligar-se a partir de um dispositivo de confiança ou fornecer uma autenticação forte, por exemplo, MFA. Exclui contas de emergência e administradores principais.

**Política de mitigação da CA definida:**

* Política 1: Bloquear o acesso a pessoas fora dos grupos-alvo
  * Utilizadores e Grupos: Inclua todos os utilizadores. Excluir AppUsers, CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Inclua todas as aplicações
  * Condições: (Nenhuma)
  * Grant Control: Bloco
* Política 2: Conceder acesso a AppUsers que necessitem de dispositivo de confiança MFA OU.
  * Utilizadores e Grupos: Incluir AppUsers. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Inclua todas as aplicações
  * Condições: (Nenhuma)
  * Grant Control: Conceder acesso, exigir autenticação de vários fatores, exigir que o dispositivo seja compatível. Para vários controlos: Requerer um dos controlos selecionados.

### <a name="contingencies-for-user-lockout"></a>Contingências para o bloqueio do utilizador

Em alternativa, a sua organização também pode criar políticas de contingência. Para criar políticas de contingência, deve definir critérios de troca entre continuidade do negócio, custos operacionais, custos financeiros e riscos de segurança. Por exemplo, pode ativar uma política de contingência apenas para um subconjunto de utilizadores, para um subconjunto de aplicações, para um subconjunto de clientes, ou a partir de um subconjunto de localizações. As políticas de contingência darão aos administradores e utilizadores finais acesso a apps e recursos, durante uma interrupção quando não foi implementado nenhum método de mitigação. A Microsoft recomenda que as políticas de contingência sejam ativadas [apenas](../conditional-access/howto-conditional-access-insights-reporting.md) no modo de relatório quando não estão a ser utilizadas para que os administradores possam monitorizar o impacto potencial das políticas caso precisem de ser ativadas.

 Compreender a sua exposição durante uma interrupção ajuda a reduzir o seu risco e é uma parte crítica do seu processo de planeamento. Para criar o seu plano de contingência, determine primeiro os seguintes requisitos de negócio da sua organização:

1. Determine as aplicações críticas da sua missão com antecedência: Quais são as aplicações a que deve dar acesso, mesmo com uma postura de menor risco/segurança? Construa uma lista destas aplicações e certifique-se de que as suas outras partes interessadas (negócios, segurança, legal, liderança) concordam que se todo o controlo de acesso desaparecer, estas aplicações ainda devem continuar a funcionar. É provável que acabe com categorias de:
   * **Aplicações críticas de missão de categoria 1** que não podem estar indisponíveis por mais de alguns minutos, por exemplo, apps que afetam diretamente as receitas da organização.
   * **Aplicações importantes** da categoria 2 que o negócio precisa de estar acessível dentro de algumas horas.
   * **Aplicativos de baixa prioridade de categoria 3** que podem resistir a uma interrupção de alguns dias.
2. Para aplicações nas categorias 1 e 2, a Microsoft recomenda-lhe que pré-planeie o tipo de acesso que pretende permitir:
   * Deseja permitir o acesso total ou a sessão restrita, como limitar os downloads?
   * Deseja permitir o acesso a parte da app, mas não a aplicação inteira?
   * Deseja permitir o acesso do trabalhador da informação e bloquear o acesso ao administrador até que o controlo de acesso seja restaurado?
3. Para essas aplicações, a Microsoft também recomenda que planeie quais as vias de acesso que irá abrir deliberadamente e quais as que irá fechar:
   * Deseja permitir que o navegador aceda apenas e bloqueie clientes ricos que possam guardar dados offline?
   * Pretende permitir o acesso apenas aos utilizadores dentro da rede corporativa e manter os utilizadores externos bloqueados?
   * Deseja permitir o acesso de certos países ou regiões apenas durante a interrupção?
   * Deseja que as políticas para as políticas de contingência, especialmente para aplicações críticas da missão, falhem ou tenham sucesso se não estiver disponível um controlo de acesso alternativo?

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Uma política de acesso condicional de contingência é uma **política de backup** que omite O Azure AD MFA, MFA de terceiros, controlos baseados em risco ou baseados em dispositivos. A fim de minimizar perturbações inesperadas quando uma política de contingência está ativada, a política deve permanecer em modo apenas de relatório quando não estiver a ser utilizada. Os administradores podem monitorizar o impacto potencial das suas políticas de contingência utilizando o livro de Insights de Acesso Condicional. Quando a sua organização decide ativar o seu plano de contingência, os administradores podem ativar a política e desativar as políticas regulares baseadas no controlo.

>[!IMPORTANT]
> Desativar políticas que impõem a segurança dos seus utilizadores, mesmo que temporariamente, reduzirão a sua postura de segurança enquanto o plano de contingência estiver em vigor.

* Configure um conjunto de políticas de recuo se uma rutura num tipo de credencial ou um mecanismo de controlo de acesso tiver impacto no acesso às suas apps. Configure uma política em estado apenas de relatório que exija a Participação do Domínio como um controlo, como um backup para uma política ativa que requer um provedor de MFA de terceiros.
* Reduzir o risco de maus atores adivinharem palavras-passe, quando não é necessário o MFA, seguindo as práticas no livro branco de orientação da [palavra-passe.](https://aka.ms/passwordguidance)
* Implementar [Azure AD Self-Service Redefinição de Password (SSPR)](./tutorial-enable-sspr.md) e [Proteção de Password AD Azure](./howto-password-ban-bad-on-premises-deploy.md) para garantir que os utilizadores não usam a palavra-passe comum e os termos que escolhe proibir.
* Utilize políticas que restrinjam o acesso dentro das apps se um determinado nível de autenticação não for atingido em vez de simplesmente voltar ao acesso total. Por exemplo:
  * Configure uma política de backup que envia o pedido de sessão restrita para Exchange e SharePoint.
  * Se a sua organização utilizar o Microsoft Cloud App Security, considere recorrer a uma política que envolva o MCAS e, em seguida, o MCAS permite o acesso apenas à leitura, mas não o upload.
* Nomeie as suas políticas para garantir que é fácil encontrá-las durante uma interrupção. Incluir os seguintes elementos no nome da política:
  * Um *número de etiqueta* para a apólice.
  * Texto para mostrar, esta política é apenas para emergências. Por exemplo: **ATIVAR EM EMERGÊNCIA**
  * A *perturbação* a que se aplica. Por exemplo: **Durante a interrupção do MFA**
  * Um *número de sequência* para mostrar a ordem que deve ativar as políticas.
  * As *aplicações* a que se aplica.
  * Os *controlos* que aplicará.
  * As *condições* que requer.
  
Esta norma de nomeação para as políticas de contingência será a seguinte: 

```
EMnnn - ENABLE IN EMERGENCY: [Disruption][i/n] - [Apps] - [Controls] [Conditions]
```

O exemplo a seguir: **Exemplo A - Política de Acesso Condicional de Contingência para restaurar o Acesso a aplicações de colaboração críticas da missão,** é uma contingência corporativa típica. Neste cenário, a organização normalmente requer MFA para todo o acesso Online e SharePoint, e a disrupção neste caso é o provedor MFA para o cliente tem uma paragem (seja Azure AD MFA, fornecedor de MFA no local ou MFA de terceiros). Esta política atenua esta paralisação, permitindo aos utilizadores específicos o acesso a estas aplicações a partir de dispositivos Windows fidedignos apenas quando estes estiverem a aceder à aplicação a partir da sua rede corporativa confiável. Excluirá igualmente as contas de emergência e os administradores centrais destas restrições. Os utilizadores visados terão então acesso ao Exchange Online e ao SharePoint Online, enquanto outros utilizadores ainda não terão acesso às apps devido à paralisação. Este exemplo requer um nome de localização de rede **CorpNetwork** e um grupo de segurança **ContingênciaAccess** com os utilizadores-alvo, um grupo chamado **CoreAdmins** com os administradores principais, e um grupo chamado **EmergencyAccess** com as contas de acesso de emergência. A contingência requer quatro políticas para proporcionar o acesso desejado. 

**Exemplo A - Políticas de acesso condicional de contingência para restaurar o acesso a aplicações de colaboração críticas da missão:**

* Política 1: Exigir dispositivos de união de domínios para troca e SharePoint
  * Nome: EM001 - ENABLE IN EMERGENCY: MFA Disruption[1/4] - Exchange SharePoint - Require Hybrid Azure Ad Join
  * Utilizadores e Grupos: Incluir o ContingênciaAccess. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Exchange Online e SharePoint Online
  * Condições: Qualquer
  * Grant Control: Requer domínio associado
  * Estado: Apenas relatório
* Política 2: Bloquear plataformas que não o Windows
  * Nome: EM002 - ENABLE IN EMERGENCY: MFA Disruption[2/4] - Exchange SharePoint - Acesso ao bloco exceto o Windows
  * Utilizadores e Grupos: Inclua todos os utilizadores. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Exchange Online e SharePoint Online
  * Condições: Plataforma do Dispositivo Incluir todas as plataformas, excluir Windows
  * Grant Control: Bloco
  * Estado: Apenas relatório
* Política 3: Bloquear redes que não a CorpNetwork
  * Nome: EM003 - ENABLE IN EMERGENCY: MFA Disruption[3/4] - Exchange SharePoint - Acesso ao bloco, exceto Rede Corporativa
  * Utilizadores e Grupos: Inclua todos os utilizadores. Excluir CoreAdmins e EmergencyAccess
  * Aplicativos cloud: Exchange Online e SharePoint Online
  * Condições: As localizações Incluem qualquer localização, excluem a CorpNetwork
  * Grant Control: Bloco
  * Estado: Apenas relatório
* Política 4: Bloco EAS explicitamente
  * Nome: EM004 - ENABLE IN EMERGENCY: MFA Disruption[4/4] - Exchange - Bloco EAS para todos os utilizadores
  * Utilizadores e Grupos: Incluir todos os utilizadores
  * Aplicativos cloud: incluir intercâmbio online
  * Condições: Aplicativos cliente: Exchange Ative Sync
  * Grant Control: Bloco
  * Estado: Apenas relatório

Ordem de ativação:

1. Excluir o ContingênciaAccess, CoreAdmins e EmergencyAccess da política de MFA existente. Verifique se um utilizador em ContingênciaAccess pode aceder ao SharePoint Online e ao Exchange Online.
2. Ativar a Política 1: Verificar os utilizadores em dispositivos de adesão ao domínio que não se encontram nos grupos de exclusão podem aceder ao Exchange Online e ao SharePoint Online. Verifique se os utilizadores do grupo Excluir podem aceder ao SharePoint Online e trocar a partir de qualquer dispositivo.
3. Ativar a Política 2: Verificar se os utilizadores que não estão no grupo de exclusão não podem chegar ao SharePoint Online e ao Exchange Online a partir dos seus dispositivos móveis. Verifique se os utilizadores do grupo Excluir podem aceder ao SharePoint e à Exchange a partir de qualquer dispositivo (Windows/iOS/Android).
4. Ativar a Política 3: Verificar se os utilizadores que não estão nos grupos de exclusão não podem aceder ao SharePoint e trocar a rede corporativa, mesmo com uma máquina de domínio. Verifique se os utilizadores do grupo Excluir podem aceder ao SharePoint e ao Exchange a partir de qualquer rede.
5. Ativar a Política 4: Verifique se todos os utilizadores não podem obter o Exchange Online a partir das aplicações de correio nativas em dispositivos móveis.
6. Desative a política de MFA existente para SharePoint Online e Exchange Online.

Neste próximo exemplo, **As políticas de Acesso Condicional de Contingência para permitir o acesso móvel ao Salesforce,** o acesso de uma aplicação empresarial é restaurado. Neste cenário, o cliente normalmente exige aos seus colaboradores de vendas acesso à Salesforce (configurado para um único sinal com Azure AD) a partir de dispositivos móveis para serem autorizados apenas a partir de dispositivos compatíveis. A disrupção neste caso é que há um problema com a avaliação da conformidade do dispositivo e a paralisação está a acontecer num momento sensível em que a equipa de vendas precisa de acesso à Salesforce para fechar negócios. Estas políticas de contingência vão permitir aos utilizadores críticos o acesso à Salesforce a partir de um dispositivo móvel para que possam continuar a fechar negócios e não perturbar o negócio. Neste exemplo, **a SalesforceContingency** contém todos os colaboradores de Vendas que precisam de manter o acesso e **a SalesAdmins** contém administradores necessários da Salesforce.

**Exemplo B - Políticas de Acesso Condicional de Contingência:**

* Política 1: Bloquear todos os que não estão na equipa salesContinência
  * Nome: EM001 - ENABLE IN EMERGENCY: Device Compliance Disruption[1/2] - Salesforce - Bloquear Todos os utilizadores exceto salesforceContingency
  * Utilizadores e Grupos: Inclua todos os utilizadores. Excluir SalesAdmins e SalesforceContingency
  * Cloud Apps: Salesforce.
  * Condições: nenhuma
  * Grant Control: Bloco
  * Estado: Apenas relatório
* Política 2: Bloquear a equipa de Vendas de qualquer plataforma que não seja móvel (para reduzir a área de ataque da superfície)
  * Nome: EM002 - ENABLE IN EMERGENCY: Device Compliance Disruption[2/2] - Salesforce - Bloquear todas as plataformas exceto iOS e Android
  * Utilizadores e Grupos: Incluir a SalesforceContingency. Excluir SalesAdmins
  * Aplicativos cloud: Salesforce
  * Condições: Plataforma do Dispositivo Inclua todas as plataformas, exclua iOS e Android
  * Grant Control: Bloco
  * Estado: Apenas relatório

Ordem de ativação:

1. Excluir salesAdmins e SalesforceContingency da política de conformidade do dispositivo existente para a Salesforce. Verifique se um utilizador do grupo SalesforceContingency pode aceder à Salesforce.
2. Ativar a Política 1: Verificar se os utilizadores fora da SalesContingency não podem aceder à Salesforce. Verifique se os utilizadores nas SalesAdmins e SalesforceContingency podem aceder à Salesforce.
3. Ativar a Política 2: Verificar se os utilizadores do grupo SalesContingency não conseguem aceder à Salesforce a partir dos seus portáteis Windows/Mac, mas ainda podem aceder a partir dos seus dispositivos móveis. Verifique salesAdmin ainda pode aceder à Salesforce a partir de qualquer dispositivo.
4. Desative a política de conformidade do dispositivo existente para a Salesforce.

### <a name="contingencies-for-user-lockout-from-on-prem-resources-nps-extension"></a>Contingências para o bloqueio do utilizador a partir de recursos on-prem (extensão NPS)

Se estiver a proteger o acesso VPN utilizando a extensão NPS do MFA Azure AD, considere a sua solução VPN como uma [aplicação SAML](../manage-apps/view-applications-portal.md) e determine a categoria de aplicação conforme recomendado abaixo. 

Se implementou a extensão NPS MFA AD AD Ad para proteger recursos on-prem, como VPN e Remote Desktop Gateway, com MFA - deve considerar antecipadamente se está pronto para desativar mFA em caso de emergência.

Neste caso, pode desativar a extensão NPS, como resultado, o servidor NPS apenas verificará a autenticação primária e não aplicará MFA aos utilizadores.

Desativar a extensão de NPS: 
-   Exporte a chave de registo HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters como reserva. 
-   Eliminar os valores de registo de "AutorizaçõesDL" e "ExtensionDLls", não a chave parâmetros. 
-   Reiniciar o serviço de política de rede (IAS) para que as alterações produzam efeitos 
-   Determine se a autenticação primária para a VPN é bem sucedida.

Uma vez recuperado o serviço e estiver pronto para impor novamente o MFA aos seus utilizadores, ative a extensão NPS: 
-   Importe a chave de registo de HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\AuthSrv\Parameters de reserva 
-   Reiniciar o serviço de política de rede (IAS) para que as alterações produzam efeitos 
-   Determine se a autenticação primária e a autenticação secundária para VPN são bem sucedidas.
-   Reveja o servidor NPS e o registo VPN para determinar quais os utilizadores que se inscreveram durante a janela de emergência.

### <a name="deploy-password-hash-sync-even-if-you-are-federated-or-use-pass-through-authentication"></a>Implemente a sincronização de haxixe de palavra-passe mesmo que esteja federada ou utilize a autenticação pass-through

O bloqueio do utilizador também pode ocorrer se as seguintes condições forem verdadeiras:

- A sua organização utiliza uma solução de identidade híbrida com autenticação ou federação pass-through.
- Os seus sistemas de identidade no local (como Ative Directory, AD FS ou um componente dependente) não estão disponíveis. 
 
Para ser mais resiliente, a sua organização deve [ativar a sincronização de haxixe de palavra-passe](../hybrid/choose-ad-authn.md), pois [permite-lhe mudar para a sincronização de haxixe de palavra-passe](../hybrid/plan-connect-user-signin.md) se os seus sistemas de identidade no local estiverem desligados.

#### <a name="microsoft-recommendations"></a>Recomendações da Microsoft
 Ative a sincronização de haxixe de palavra-passe utilizando o assistente Azure AD Connect, independentemente de a sua organização utilizar a federação ou a autenticação pass-through.

>[!IMPORTANT]
> Não é necessário converter utilizadores de federados para autenticação gerida para utilizar a sincronização de haxixe de palavra-passe.

## <a name="during-a-disruption"></a>Durante uma interrupção

Se optar pela implementação de um plano de mitigação, poderá sobreviver automaticamente a uma única perturbação do controlo de acesso. No entanto, se optar por criar um plano de contingência, poderá ativar as suas políticas de contingência durante a interrupção do controlo de acesso:

1. Ativar as suas políticas de contingência que concedem aos utilizadores direcionados, acesso a aplicações específicas, a partir de redes específicas.
2. Desative as suas políticas regulares baseadas em controlo.

### <a name="microsoft-recommendations"></a>Recomendações da Microsoft

Dependendo das mitigações ou contingências que são usadas durante uma interrupção, a sua organização pode estar a conceder acesso apenas com senhas. Nenhuma salvaguarda é um risco considerável de segurança que deve ser ponderado cuidadosamente. As organizações devem:

1. Como parte da sua estratégia de controlo de mudanças, documente todas as alterações e o estado anterior para poder reverter quaisquer contingências que implementou assim que os controlos de acesso estiverem totalmente operacionais.
2. Assuma que os atores mal-intencionados tentarão recolher senhas através de spray de palavras-passe ou ataques de phishing enquanto desativa o MFA. Além disso, os maus atores podem já ter senhas que anteriormente não deram acesso a qualquer recurso que possa ser tentado durante esta janela. Para utilizadores críticos, como executivos, é possível atenuar parcialmente este risco, repondo as suas palavras-passe antes de desativar o MFA para os mesmos.
3. Arquive toda a atividade de inscrição para identificar quem acede ao que durante o tempo mFA foi desativado.
4. [Triagem todas as deteções de risco reportadas](../reports-monitoring/concept-sign-ins.md) durante esta janela.

## <a name="after-a-disruption"></a>Depois de uma rutura

Desfaça as alterações que fez como parte do plano de contingência ativado assim que o serviço for restaurado que causou a interrupção. 

1. Ativar as políticas regulares
2. Desative as suas políticas de contingência de volta ao modo reporte- apenas. 
3. Reverta quaisquer outras alterações que tenha feito e documentada durante a interrupção.
4. Se usou uma conta de acesso de emergência, lembre-se de regenerar credenciais e proteger fisicamente os novos detalhes das credenciais como parte dos procedimentos da sua conta de acesso de emergência.
5. Continue para [triagem todas as deteções de risco reportadas](../reports-monitoring/concept-sign-ins.md) após a interrupção para atividades suspeitas.
6. Revogue todos os tokens de atualização que foram emitidos [usando o PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken) para direcionar um conjunto de utilizadores. A revogação de todos os tokens de atualização é importante para as contas privilegiadas utilizadas durante a interrupção e fazê-lo irá forçá-los a reauthenticar e a cumprir o controlo das políticas restauradas.

## <a name="emergency-options"></a>Opções de emergência

 Em caso de emergência e a sua organização não implementou previamente um plano de mitigação ou contingência, então siga as recomendações nas [Contingências para](#contingencies-for-user-lockout) a secção de bloqueio do utilizador se já utilizarem políticas de Acesso Condicional para impor o MFA.
Se a sua organização estiver a utilizar políticas antigas de MFA por utilizador, então pode considerar a seguinte alternativa:

1. Se tiver o endereço IP de saída da rede corporativa, pode adicioná-los como IPs fidedignos para permitir a autenticação apenas na rede corporativa.
   1. Se não tiver o inventário de endereços IP de saída, ou necessário para permitir o acesso dentro e fora da rede corporativa, pode adicionar todo o espaço de endereço IPv4 como IPs fidedignos, especificando 0.0.0.0/1 e 128.0.0.0/1.

>[!IMPORTANT]
 > Se alargar os endereços IP fidedignos para desbloquear o acesso, não serão geradas as deteções de risco associadas a endereços IP (por exemplo, viagens impossíveis ou locais desconhecidos).

>[!NOTE]
 > Configurar [IPs fidedignos](./howto-mfa-mfasettings.md) para Azure AD MFA só está disponível com [licenças Azure AD Premium](./concept-mfa-licensing.md).

## <a name="learn-more"></a>Saber mais

* [Documentação da Autenticação do Azure AD](./howto-mfaserver-iis.md)
* [Gerir contas administrativas de acesso de emergência em Azure AD](../roles/security-emergency-access.md)
* [Configurar localizações nomeadas no Azure Ative Directory](../reports-monitoring/quickstart-configure-named-locations.md)
  * [Set-MsolDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings)
* [Como configurar o Azure Ative Directory híbrido juntou dispositivos](../devices/hybrid-azuread-join-plan.md)
* [Guia de Implementação do Windows Hello para Empresas](/windows/security/identity-protection/hello-for-business/hello-deployment-guide)
  * [Orientação de passwords - Microsoft Research](https://research.microsoft.com/pubs/265143/microsoft_password_guidance.pdf)
* [Quais são as condições no Azure Ative Directy Acesso Condicional?](../conditional-access/concept-conditional-access-conditions.md)
* [O que são controlos de acesso no Azure Ative Directory Conditional Access?](../conditional-access/controls.md)
* [O que é o modo de relatório de acesso condicional?](../conditional-access/concept-conditional-access-report-only.md)
