---
title: Planeie uma implantação de acesso condicional do Diretório Ativo Azure
description: Saiba como desenhar políticas de Acesso Condicional e implementar eficazmente na sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/16/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13756be041f88883d84f9558308c7fe5c9be2d0e
ms.sourcegitcommit: aacbf77e4e40266e497b6073679642d97d110cda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/12/2021
ms.locfileid: "98116012"
---
# <a name="plan-a-conditional-access-deployment"></a>Planear a implementação do Acesso Condicional

Planear a sua implementação de Acesso Condicional é fundamental para alcançar a estratégia de acesso da sua organização a apps e recursos.

Num mundo móvel, primeiro em nuvem, os seus utilizadores acedem aos recursos da sua organização a partir de qualquer lugar usando uma variedade de dispositivos e aplicações. Como resultado, concentrar-se em quem pode aceder a um recurso já não é suficiente. Também é necessário considerar onde está o utilizador, o dispositivo que está a ser utilizado, o recurso a ser acedido e muito mais. 

O Azure Ative Directory (Azure AD) Análises de acesso condicional de comentários como utilizador, dispositivo e localização para automatizar decisões e impor políticas organizacionais de acesso a recursos. Pode utilizar políticas de Acesso Condicional para aplicar controlos de acesso como a Autenticação Multi-Factor (MFA). As políticas de Acesso Condicional permitem-lhe solicitar aos utilizadores mFA quando necessário para a segurança e ficar fora do caminho dos utilizadores quando não for necessário.

![Conditional Access overview (Descrição geral do Acesso Condicionado)](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

A Microsoft fornece políticas condicionais padrão chamadas [padrão de segurança](../fundamentals/concept-fundamentals-security-defaults.md) que garantem um nível básico de segurança. No entanto, a sua organização pode precisar de mais flexibilidade do que a oferta de incumprimentos de segurança. Pode utilizar o Acesso Condicional para personalizar falhas de segurança com mais granularidade e para configurar novas políticas que satisfaçam os seus requisitos.

## <a name="learn"></a>Learn

Antes de começar, certifique-se de que compreende como funciona [o Acesso Condicional](overview.md) e quando deve usá-lo.

### <a name="benefits"></a>Benefícios

Os benefícios da implementação do Acesso Condicional são:

* Aumentar a produtividade. Só interrompa os utilizadores com uma condição de inscrição como mFA quando um ou mais sinais o justificam. As políticas de Acesso Condicional permitem-lhe controlar quando os utilizadores são solicitados para MFA, quando o acesso é bloqueado e quando devem usar um dispositivo de confiança.

* Gerir o risco. Automatizar a avaliação dos riscos com as condições políticas significa que os sinais de risco são imediatamente identificados e remediados ou bloqueados. O Acoplamento de Acesso Condicional com [Proteção de Identidade,](../identity-protection/overview-identity-protection.md)que deteta anomalias e eventos suspeitos, permite-lhe direcionar o acesso aos recursos ou fechado. 

* Abordar a conformidade e a governação. O Acesso Condicional permite-lhe auditar o acesso às aplicações, apresentar termos de uso para consentimento e restringir o acesso com base em políticas de conformidade.

* Gerir o custo. A mudança das políticas de acesso ao Azure AD reduz a dependência de soluções personalizadas ou no local para acesso condicional e os seus custos de infraestrutura.

### <a name="license-requirements"></a>Requisitos de licença

Consulte [os requisitos de licença de acesso condicional](overview.md).

Se forem necessárias funcionalidades adicionais, também poderá necessitar de licenças relacionadas. Para mais informações, consulte [os preços do Azure Ative Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### <a name="prerequisites"></a>Pré-requisitos

* Um inquilino da Azure AD em funcionamento com Azure AD Premium ou licença de julgamento habilitado. Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uma conta com privilégios de administrador de acesso condicional.

* Um utilizador não administrador com uma palavra-passe que conhece, como testuser. Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Azure Ative Directory](../fundamentals/add-users-azure-active-directory.md).

* Um grupo do qual o utilizador não administrador é membro. Se precisar de criar um grupo, consulte [criar um grupo e adicionar membros no Diretório Ativo Azure](../fundamentals/active-directory-groups-create-azure-portal.md).

### <a name="training-resources"></a>Recursos de formação

Os seguintes recursos podem ser úteis à medida que aprende sobre o Acesso Condicional:


#### <a name="videos"></a>Vídeos

* [O que é o Acesso Condicional?](https://youtu.be/ffMAw2IVO7A)
* [Como implementar acesso condicional?](https://youtu.be/c_izIRNJNuk)
* [Como lançar políticas de Acesso Condicional aos utilizadores finais?](https://youtu.be/0_Fze7Zpyvc)
* [Acesso Condicional com controlos do dispositivo](https://youtu.be/NcONUf-jeS4)
* [Acesso Condicional com Azure AD MFA](https://youtu.be/Tbc-SU97G-w)
* [Acesso Condicional na Mobilidade Empresarial + Segurança](https://youtu.be/A7IrxAH87wc)


#### <a name="online-courses-on-pluralsight"></a>Cursos online no PluralSight

* [Gestão de identidade de design no Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Autenticação de design para Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Autorização de design para Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.

### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas

Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas no impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se de que está a envolver as partes interessadas certas](../fundamentals/active-directory-deployment-plans.md) e que as funções do projeto são claras.

### <a name="plan-communications"></a>Planear as comunicações

A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando vai mudar, e como ganhar apoio se eles experimentarem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto

Quando novas políticas estiverem prontas para o seu ambiente, implemente-as por fases no ambiente de produção. Em primeiro lugar, aplicar uma política a um pequeno conjunto de utilizadores num ambiente de teste e verificar se a política se comporta como esperado. Consulte [as melhores práticas para um piloto.](../fundamentals/active-directory-deployment-plans.md)

> [!NOTE]
> Para a elaboração de novas políticas não específicas dos administradores, exclua todos os administradores. Isto garante que os administradores ainda podem aceder à política e fazer alterações ou revogá-la se houver um impacto significativo. Valide sempre a política com grupos de utilizadores mais pequenos antes de se candidatar a todos os utilizadores.

## <a name="understand-conditional-access-policy-components"></a>Compreender componentes da política de acesso condicional
As políticas de acesso condicional são se-então declarações: Se uma atribuição for cumprida, em seguida, aplique estes controlos de acesso.

Ao configurar políticas de acesso condicional, as condições são chamadas *de atribuições*. As políticas de Acesso Condicional permitem-lhe impor controlos de acesso nas aplicações da sua organização com base em determinadas atribuições.


Para mais informações, consulte [a construção de uma política de acesso condicional.](concept-conditional-access-policies.md)

![criar tela de política](media/plan-conditional-access/create-policy.png)

[As atribuições](concept-conditional-access-policies.md#assignments) definem o

* [Utilizadores e grupos](concept-conditional-access-users-groups.md) a serem afetados pela política

* [Aplicativos na nuvem ou ações](concept-conditional-access-cloud-apps.md) às quais a política se aplicará 

* [Condições](concept-conditional-access-conditions.md) em que a apólice se aplicará.

As definições [dos controlos](concept-conditional-access-policies.md) de acesso determinam como aplicar uma política:

* [Conceder ou bloquear](concept-conditional-access-grant.md) o acesso a aplicativos na nuvem.

* [Os controlos de sessão](concept-conditional-access-session.md) permitem experiências limitadas dentro de aplicações específicas na nuvem.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Faça as perguntas certas para construir as suas políticas

As políticas respondem a perguntas sobre quem deve aceder aos seus recursos, que recursos devem aceder e em que condições. As políticas podem ser concebidas para garantir o acesso ou para bloquear o acesso. Certifique-se de fazer as perguntas certas sobre o que a sua política está a tentar alcançar. 

Documente as respostas a perguntas para cada política antes de a construir. 

#### <a name="common-questions-about-assignments"></a>Perguntas comuns sobre atribuições

[Utilizadores e Grupos](concept-conditional-access-users-groups.md)

* Quais os utilizadores e grupos que serão incluídos ou excluídos da apólice?

* Esta política inclui todos os utilizadores, grupo específico de utilizadores, funções de diretório ou utilizadores externos?

[Aplicações na app ou ações](concept-conditional-access-cloud-apps.md)

* A que aplicações a política se aplicará?

* Que ações dos utilizadores estarão sujeitas a esta política?

[Condições](concept-conditional-access-conditions.md)

* Quais as plataformas de dispositivos que serão incluídas ou excluídas da apólice?

* Quais são os locais de confiança da organização?

* Quais os locais incluídos ou excluídos da apólice?

* Que tipos de aplicativos para clientes (navegador, móvel, clientes de desktop, aplicações com métodos de autenticação legado) serão incluídos ou excluídos da apólice?

* Tem políticas que impulsionem a exclusão de dispositivos AZure AD Ou dispositivos híbridos Azure AD aderidos a dispositivos de políticas? 

* Se utilizar [a Proteção de](../identity-protection/concept-identity-protection-risks.md)Identidade, pretende incorporar a proteção contra o risco de inscrição?

#### <a name="common-questions-about-access-controls"></a>Questões comuns sobre controlos de acesso

[Concessão ou Bloco ](concept-conditional-access-grant.md) 

Deseja conceder acesso aos recursos exigindo um ou mais dos seguintes recursos?

* Requerer MFA

* Exigir que o dispositivo seja marcado como conforme

* Requerem o dispositivo híbrido Azure AD

* Requera uma aplicação de cliente aprovada

* Pedir uma política de proteção de aplicações

[Controlo de sessão](concept-conditional-access-session.md)

Deseja impor algum dos seguintes controlos de acesso em aplicações na nuvem?

* Use permissões forçadas de aplicativo

* Utilizar controlo de aplicativos de acesso condicional

* Impor a frequência de inscrição

* Use sessões de navegador persistentes

### <a name="access-token-issuance"></a>Emissão de ficha de acesso

É importante entender como os tokens de acesso são emitidos. 

![Diagrama de emissão de token de acesso](media/plan-conditional-access/CA-policy-token-issuance.png)

> [!NOTE]
> Se não for necessária nenhuma atribuição e não estiver em vigor nenhuma política de Acesso Condicional, o comportamento padrão é emitir um token de acesso. 

Por exemplo, considere uma política em que:

Se o utilizador estiver no Grupo 1, então force o MFA a aceder à App 1.

Se um utilizador que não estiver no Grupo 1 tentar aceder à aplicação, não é cumprida a condição de "se" e é emitido um sinal. Excluir utilizadores fora do Grupo 1 requer uma política separada para bloquear todos os outros utilizadores.

## <a name="follow-best-practices"></a>Siga as melhores práticas

A estrutura de Acesso Condicional proporciona-lhe uma grande flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que deve rever cuidadosamente cada política de configuração antes de a libertar para evitar resultados indesejáveis.

### <a name="apply-conditional-access-policies-to-every-app"></a>Aplicar políticas de acesso condicional a cada app

As fichas de acesso são por padrão emitidas se uma condição de política de acesso condicional não desencadear um controlo de acesso. Certifique-se de que cada aplicação tem pelo menos uma política de acesso condicional aplicada

> [!IMPORTANT]
> Tenha muito cuidado ao usar o bloco e todas as aplicações numa única política. Isto poderia bloquear administradores fora do Portal de Administração Azure, e as exclusões não podem ser configuradas para importantes pontos finais, como o Microsoft Graph.

### <a name="minimize-the-number-of-conditional-access-policies"></a>Minimizar o número de políticas de acesso condicional

Criar uma política para cada app não é eficiente e leva a uma administração difícil. O Acesso Condicional aplicará apenas as primeiras 195 políticas por utilizador. Recomendamos que analise as suas apps e as agrupe em aplicações que tenham os mesmos requisitos de recursos para os mesmos utilizadores. Por exemplo, se todas as aplicações da Microsoft 365 ou todas as aplicações DE RH tiverem os mesmos requisitos para os mesmos utilizadores, crie uma única política e inclua todas as aplicações a que se aplica. 

### <a name="set-up-emergency-access-accounts"></a>Criar contas de acesso de emergência

Se configurar mal uma apólice, pode bloquear as organizações fora do portal Azure. Atenuar o impacto do bloqueio acidental do administrador através da criação de [duas](../roles/security-emergency-access.md) ou mais contas de acesso de emergência na sua organização.

* Crie uma conta de utilizador dedicada à administração de políticas e excluída de todas as suas políticas.

### <a name="set-up-report-only-mode"></a>Configurar o modo apenas de relatório

Pode ser difícil prever o número e os nomes dos utilizadores afetados por iniciativas comuns de implantação, tais como:

* bloquear a autenticação do legado
* exigindo MFA
* implementação de políticas de risco de inscrição

[O modo apenas de relatório ](concept-conditional-access-report-only.md) permite que os administradores avaliem o impacto das políticas de Acesso Condicional antes de as permitirem no seu ambiente.

Saiba como [configurar o modo apenas de relatório numa política de acesso condicional](howto-conditional-access-insights-reporting.md).

### <a name="plan-for-disruption"></a>Plano de perturbação

Se depender de um único controlo de acesso, como MFA ou uma localização de rede, para proteger os seus sistemas de TI, é suscetível a falhas de acesso se esse controlo de acesso único ficar indisponível ou configurado de forma errada. Para reduzir o risco de bloqueio durante perturbações imprevistas, [planeie estratégias](../authentication/concept-resilient-controls.md) para adotar para a sua organização.

### <a name="set-naming-standards-for-your-policies"></a>Desaça padrões de nomeação para as suas políticas

O padrão de nomeação ajuda-o a encontrar políticas e a compreender o seu propósito sem abri-las no portal de administração Azure. Recomendamos que nomeie a sua política para mostrar:

* Um número de sequência

* A(s) app cloud aplica-se a

* A resposta

* A quem se aplica

* Quando se aplica (se aplicável)

![Screenshot que mostra os padrões de nomeação para políticas.](media/plan-conditional-access/11.png)

**Exemplo;** Uma política para exigir MFA para utilizadores de marketing que acedam à aplicação Dynamics CRP a partir de redes externas pode ser:

![Padrão de nomeação](media/plan-conditional-access/naming-example.png)

Um nome descritivo ajuda-o a manter uma visão geral da sua implementação do Acesso Condicional. O Número da Sequência é útil se precisar de fazer referência a uma política numa conversação. Por exemplo, quando fala com um administrador ao telefone, pode pedir-lhe que abra a política CA01 para resolver um problema.

#### <a name="naming-standards-for-emergency-access-controls"></a>Naming standards para controlos de acesso de emergência

Além das suas políticas ativas, implemente políticas de desativadas que atuam como [controlos secundários de acesso resiliente em cenários de paralisação ou de emergência.](../authentication/concept-resilient-controls.md) O seu padrão de nomeação para as políticas de contingência deve incluir:
* ENABLE IN EMERGENCY no início para fazer o nome destacar-se entre as outras políticas.

* O nome da perturbação a que deve aplicar-se.

* Um número de sequência de encomendas para ajudar o administrador a saber em que políticas de encomenda devem ser ativadas.

**Exemplo**

O nome que se segue indica que esta política é a primeira de quatro políticas para permitir se houver uma perturbação do MFA:

EM01 - ENABLE IN EMERGENCY: MFA Disruption [1/4] - Exchange SharePoint: Require hybrid Azure AD join for VIP users.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Excluir países dos quais nunca se espera uma inscrição.

O diretório ativo Azure permite-lhe criar [localizações nomeadas.](location-condition.md) Crie um local nomeado que inclua todos os países dos quais nunca esperaria que ocorresse uma sessão de sessão. Em seguida, crie uma política para todas as aplicações que bloqueiam o sessão a partir desse local nomeado. **Certifique-se de isentar os seus administradores desta política.**

### <a name="plan-your-policy-deployment"></a>Planeie a sua implementação de políticas

Quando novas políticas estiverem prontas para o seu ambiente, certifique-se de que revê cada política antes de a lançar para evitar resultados indesejáveis.

## <a name="common-policies"></a>Políticas comuns

Ao planear a sua solução de política de Acesso Condicional, avalie se precisa de criar políticas para alcançar os seguintes resultados.

* [Requerer MFA](#require-mfa)
* [Responder a contas potencialmente comprometidas](#respond-to-potentially-compromised-accounts)
* [Exigir dispositivos geridos](#require-managed-devices)
* [Exigir candidaturas de clientes aprovadas](#require-approved-client-apps)
* [Bloquear acesso](#block-access)

### <a name="require-mfa"></a>Requerer MFA

Casos de utilização comum para exigir acesso a MFA:

* [Por administradores](howto-conditional-access-policy-admin-mfa.md)

* [Para aplicativos específicos](../authentication/tutorial-enable-azure-mfa.md)

* [Para todos os utilizadores](howto-conditional-access-policy-all-users-mfa.md)

* [A partir de localizações de rede, você não confia](untrusted-networks.md)

* [Para a Gestão Azure](howto-conditional-access-policy-azure-management.md)

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com as políticas de Acesso Condicional, pode implementar respostas automatizadas a inscrições através de identidades potencialmente comprometidas. A probabilidade de uma conta estar comprometida é expressa sob a forma de níveis de risco. Existem dois níveis de risco calculados pela Proteção de Identidade: risco de inscrição e risco de utilização. As três políticas de incumprimento que podem ser ativadas.

* [Exigir que todos os utilizadores se registem para MFA](howto-conditional-access-policy-risk.md)

* [Requerer uma alteração de palavra-passe para utilizadores de alto risco](howto-conditional-access-policy-risk.md)

* [Exigir MFA para utilizadores com risco médio ou elevado de inscrição](howto-conditional-access-policy-risk.md)

### <a name="require-managed-devices"></a>Exigir dispositivos geridos

A proliferação de dispositivos suportados para aceder aos seus recursos na nuvem ajuda a melhorar a produtividade dos seus utilizadores. Provavelmente não quer que certos recursos no seu ambiente sejam acedidos por dispositivos com um nível de proteção desconhecido. Para esses recursos, [exija que os utilizadores só possam aceder-lhes através de um dispositivo gerido](require-managed-devices.md).

### <a name="require-approved-client-apps"></a>Exigir aplicações aprovados do cliente

Os colaboradores usam os seus dispositivos móveis para tarefas pessoais e de trabalho. Para cenários BYOD deve decidir se gere todo o dispositivo ou apenas os dados sobre o mesmo. se gerir apenas dados e acesso, pode [necessitar de aplicações de nuvem aprovadas](app-based-conditional-access.md) que possam proteger os seus dados corporativos. por exemplo, pode exigir que o e-mail seja acedido apenas através do Outlook mobile, e não através de um programa de correio genérico.

### <a name="block-access"></a>Bloquear o acesso

A opção de [bloquear todos os acessos](howto-conditional-access-policy-block-access.md) é poderosa. Pode ser usado, por exemplo, quando está a migrar uma aplicação para a Azure AD, mas ainda não está pronto para ninguém se inscrever. Acesso ao bloco: 

* Substitui todas as outras atribuições para um utilizador

* Tem o poder de impedir toda a sua organização de se inscrever no seu inquilino.

> [!IMPORTANT]
> Se criar uma política para bloquear o acesso a todos os utilizadores, não se esqueça de excluir contas de acesso de emergência e considere excluir todos os administradores da apólice.

Outros cenários comuns onde pode bloquear o acesso aos seus utilizadores são:

* [Bloqueie certas localizações da rede](howto-conditional-access-policy-location.md) para aceder às suas aplicações na nuvem. Pode usar esta política para bloquear certos países de onde sabe que o tráfego não deve vir.

* A Azure AD suporta a autenticação do legado. No entanto, a autenticação do legado não suporta mFA e muitos ambientes exigem que aborde a segurança de identidade. Neste caso, pode [bloquear aplicações usando a autenticação antiga ](block-legacy-authentication.md) de aceder aos recursos do seu inquilino.

## <a name="build-and-test-policies"></a>Construir e testar políticas

Em cada fase do seu destacamento, certifique-se de que está a avaliar que os resultados são esperados. 

Quando as novas políticas estiverem prontas, implemente-as por fases no ambiente produtivo:

* Fornecer uma comunicação de alteração interna aos utilizadores finais.

* Comece com um pequeno conjunto de utilizadores e verifique se a política se comporta como esperado.

* Quando expande uma política para incluir mais utilizadores, continue a excluir todos os administradores. Excluindo os administradores garante que alguém ainda tem acesso a uma política se for necessária uma mudança.

* Aplique uma política a todos os utilizadores apenas depois de testada minuciosamente. Certifique-se de que tem pelo menos uma conta de administrador à qual uma apólice não se aplica.

### <a name="create-test-users"></a>Criar utilizadores de teste

Crie um conjunto de utilizadores de teste que reflitam os utilizadores no seu ambiente de produção. A criação de utilizadores de teste permite-lhe verificar as políticas como esperado antes de impactar os utilizadores reais e potencialmente perturbar o seu acesso a apps e recursos.

Algumas organizações têm inquilinos de teste para este fim. No entanto, pode ser difícil recriar todas as condições e aplicações num inquilino de teste para testar totalmente o resultado de uma política.

### <a name="create-a-test-plan"></a>Criar um plano de teste

O plano de ensaio é importante para ter uma comparação entre os resultados esperados e os resultados reais. Deve sempre ter uma expectativa antes de testar algo. A tabela a seguir descreve casos de teste de exemplo. Ajuste os cenários e os resultados esperados com base na configuração das suas políticas de Acesso Condicional.

| Política| Cenário| Resultado Esperado |
| - | - | - |
| [Exigir MFA quando não estiver no trabalho](untrusted-networks.md)| O utilizador autorizado assina na App enquanto está numa localização/trabalho de confiança| O utilizador não é solicitado ao MFA |
| [Exigir MFA quando não estiver no trabalho](untrusted-networks.md)| O utilizador autorizado assina na App enquanto não está numa localização/trabalho fidedigna| O utilizador é solicitado ao MFA e pode assinar com sucesso |
| [Exigir MFA (para administração)](../fundamentals/concept-fundamentals-security-defaults.md)| Global Admin assina na App| Administração é solicitada ao MFA |
| [Inícios de sessão de risco](../identity-protection/howto-identity-protection-configure-risk-policies.md)| O utilizador assina na App usando um navegador não aprovado| Administração é solicitada ao MFA |
| [Gestão de dispositivos](require-managed-devices.md)| Utilizador autorizado tenta iniciar sedutor de um dispositivo autorizado| Acesso concedido |
| [Gestão de dispositivos](require-managed-devices.md)| Utilizador autorizado tenta iniciar sedutor de um dispositivo não autorizado| Acesso bloqueado |
| [Alteração de palavra-passe para utilizadores arriscados](../identity-protection/howto-identity-protection-configure-risk-policies.md)| O utilizador autorizado tenta entrar com credenciais comprometidas (sinal de alto risco)| O utilizador é solicitado a alterar a palavra-passe ou o acesso é bloqueado com base na sua política |


### <a name="configure-the-test-policy"></a>Configure a política de teste

No [portal Azure,](https://portal.azure.com/)configura políticas de acesso condicional ao abrigo do Diretório Ativo Azure > Segurança > Acesso Condicional.

Se quiser saber mais sobre como criar políticas de Acesso Condicional, consulte este exemplo: [Política de Acesso Condicional a solicitar para MFA quando um utilizador se inscreve no portal Azure](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json). Este arranque rápido ajuda-o a:

* Familiarize-se com a interface do utilizador

* Obtenha uma primeira impressão de como funciona o Acesso Condicional

### <a name="enable-the-policy-in-report-only-mode"></a>Ativar a política no modo apenas de relatório

Para avaliar o impacto da sua política, comece por permitir a política [apenas](concept-conditional-access-report-only.md)no modo de relatório . As políticas só para relatórios são avaliadas durante a entrada, mas os controlos de concessão e os controlos de sessão não são aplicados. Uma vez guardada a política apenas no modo reporte, pode ver o impacto nos logins em tempo real nos registos de entrada. A partir dos registos de inscrição, selecione um evento e navegue no separador Report-only para ver o resultado de cada política apenas de relatório.


![reportar apenas modo ](media/plan-conditional-access/report-only-mode.png)

Selecionando a política, também pode ver como as atribuições e os controlos de acesso da política foram avaliados usando o ecrã de detalhes da Política. Para que uma política se aplique a uma inscrição, cada uma das atribuições configuradas deve ser satisfeita. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Compreenda o impacto das suas políticas utilizando o livro insights e relatórios

Pode ver o impacto agregado das suas políticas de Acesso Condicional no livro insights e relatórios. Para aceder ao livro, precisa de uma subscrição do Azure Monitor e terá de [transmitir os seus registos de inscrição para um espaço de trabalho do Log Analytics](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Simular ins- ins utilizando a ferramenta "e se".

Outra forma de validar a sua política de Acesso Condicional é utilizando a [ferramenta "e se",](troubleshoot-conditional-access-what-if.md)que simula quais as políticas que se aplicariam a um utilizador que se inscreve em circunstâncias hipotéticas. Selecione os atributos de inscrição que pretende testar (como utilizador, aplicação, plataforma do dispositivo e localização) e veja quais as políticas que se aplicariam.

> [!NOTE] 
> Embora uma execução simulada lhe dê uma boa ideia do impacto que uma política de Acesso Condicional tem, não substitui um teste real.

### <a name="test-your-policy"></a>Teste a sua política

Realize cada teste no seu plano de teste com os utilizadores de teste.

**Certifique-se de que testa os critérios de exclusão de uma política**. Por exemplo, pode excluir um utilizador ou grupo de uma política que exija MFA. Teste se os utilizadores excluídos forem solicitados para mFA, porque a combinação de outras políticas pode exigir MFA para esses utilizadores.

### <a name="roll-back-policies"></a>Reverter as políticas

Caso precise de reverter as suas políticas recém-implementadas, utilize uma ou mais das seguintes opções:

* **Desative a apólice.** A desativação de uma apólice garante que não se aplica quando um utilizador tenta fazer o seu súmis. Pode sempre voltar e ativar a política quando quiser usá-la.

![permitir a imagem de política](media/plan-conditional-access/enable-policy.png)

* **Excluir um utilizador ou grupo de uma apólice.** Se um utilizador não conseguir aceder à aplicação, pode optar por excluir o utilizador da apólice.

![excluir utilizadores e grupos](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Esta opção deve ser utilizada com moderação, apenas em situações em que o utilizador é confiável. O utilizador deve ser adicionado de volta à política ou grupo o mais rapidamente possível.

* **Apagar a apólice.** Se a apólice já não for necessária, [elimine-a.](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json)

## <a name="manage-access-to-cloud-apps"></a>Gerir o acesso a aplicativos na nuvem

Utilize as seguintes opções de Gerir e gerir as suas políticas de Acesso Condicional:

![O Screenshot mostra as opções DE GEST PARA as políticas C A, incluindo localizações nomeadas, controlos personalizados, Termos de utilização, conectividade V P N e as políticas clássicas selecionadas.](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Localizações com nome

A condição de localização de uma política de acesso condicional permite-lhe ligar as definições dos controlos de acesso às localizações da rede dos seus utilizadores. Com [localizações nomeadas,](location-condition.md)pode criar agrupamentos lógicos de intervalos de endereços IP ou países e regiões.

### <a name="custom-controls"></a>Controlos personalizados

[Os controlos personalizados](controls.md) redirecionam os seus utilizadores para um serviço compatível para satisfazer os requisitos de autenticação fora do Azure AD. Para satisfazer este controlo, o navegador de um utilizador é redirecionado para o serviço externo, executa qualquer autenticação necessária e é depois redirecionado de volta para Azure AD. A Azure AD verifica a resposta e, se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de Acesso Condicional.

### <a name="terms-of-use"></a>Termos de utilização

Antes de aceder a determinadas aplicações em nuvem no seu ambiente, pode obter o consentimento dos utilizadores aceitando os seus Termos de Utilização (ToU). Siga este [Quickstart para criar Termos de Utilização](require-tou.md).

## <a name="troubleshoot-conditional-access"></a>Resolver problemas de acesso condicional

Quando um utilizador estiver a ter um problema com uma política de Acesso Condicional, recolha as seguintes informações para facilitar a resolução de problemas.

* Nome do princípio do utilizador

* Nome de exibição do utilizador

* Nome do sistema operativo

* Carimbo de tempo (aproximado é ok)

* Aplicação-alvo

* Tipo de aplicação de cliente (browser vs cliente)

* ID de correlação (este é exclusivo do sign-in)

Se o utilizador recebeu uma mensagem com um link Mais detalhes, pode recolher a maior parte desta informação para si.

![Não consegue chegar à mensagem de erro da aplicação](media/plan-conditional-access/cant-get-to-app.png)

Assim que tiver recolhido a informação, consulte os seguintes recursos:

* [Problemas de login com Acesso Condicional](troubleshoot-conditional-access.md) – Compreenda os resultados inesperados de login relacionados com o Acesso Condicional utilizando mensagens de erro e registo de login AD Azure.

* [Utilizando a ferramenta What-If](troubleshoot-conditional-access-what-if.md) - Compreenda por que uma política foi ou não aplicada a um utilizador em circunstâncias específicas ou se uma política seria aplicada num estado conhecido.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre a autenticação de vários fatores](../authentication/concept-mfa-howitworks.md)

[Saiba mais sobre a Proteção de Identidade](../identity-protection/overview-identity-protection.md)

[Gerir políticas de acesso condicional com a API do Gráfico microsoft](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy)
