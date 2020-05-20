---
title: Planeie uma implantação de acesso condicional do Diretório Ativo Azure
description: Aprenda a conceber políticas de Acesso Condicional e implemente eficazmente na sua organização.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: joflore
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5d4ae1c9926c7ea1d18bf5c87fbed837edc2a5d5
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83641482"
---
# <a name="plan--a-conditional-access-deployment"></a>Planear uma implantação de acesso condicional

Planear a sua implementação de Acesso Condicional é fundamental para alcançar a estratégia de acesso da sua organização para apps e recursos.

Num mundo móvel e em primeira nuvem, os seus utilizadores acedem aos recursos da sua organização a partir de qualquer lugar utilizando uma variedade de dispositivos e aplicações. Como resultado, concentrar-se em quem pode aceder a um recurso já não é suficiente. Também é necessário ter em conta onde está o utilizador, o dispositivo a ser utilizado, o recurso a ser acedido, e muito mais. 

O Azure Ative Directory (Azure AD) Acesso Condicional (CA) analisa sinais como utilizador, dispositivo e localização para automatizar decisões e impor políticas de acesso organizacional para recursos. Pode utilizar as políticas ca para aplicar controlos de acesso como a Autenticação multi-factor (MFA). As políticas ca permitem-lhe solicitar aos utilizadores de MFA quando necessário para a segurança, e ficar fora do caminho dos utilizadores quando não for necessário.

![Conditional Access overview (Descrição geral do Acesso Condicionado)](./media/plan-conditional-access/conditional-access-overview-how-it-works.png)

A Microsoft fornece políticas condicionais padrão chamadas falhas de [segurança](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) que garantem um nível básico de segurança. No entanto, a sua organização pode precisar de mais flexibilidade do que os incumprimentos de segurança oferecem. Pode utilizar o Acesso Condicional para personalizar os incumprimentos de segurança com mais granularidade e configurar novas políticas que satisfaçam os seus requisitos.

## <a name="learn"></a>Learn

Antes de começar, certifique-se de que compreende como funciona o [Acesso Condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) e quando deve usá-lo.

### <a name="benefits"></a>Benefícios

Os benefícios da implementação do Acesso Condicional são:

* Aumentar a produtividade. Só interrompa os utilizadores com uma condição de inscrição como o MFA quando um ou mais sinais o justificarem. As políticas ca permitem controlar quando os utilizadores são solicitados para MFA, quando o acesso é bloqueado, e quando devem usar um dispositivo de confiança.

* Gerir o risco. Automatizar a avaliação de risco com condições de política significa que os insinsos de risco são ao mesmo tempo identificados e remediados ou bloqueados. Acoplamento do Acesso Condicional com [Proteção de Identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/overview), que deteta anomalias e eventos suspeitos, permite-lhe direcionar-se quando o acesso aos recursos é bloqueado ou fechado. 

* Abordar o cumprimento e a governação. O Acesso Condicional permite-lhe auditar o acesso a candidaturas, apresentar termos de utilização para consentimento e restringir o acesso com base em políticas de conformidade.

* Gerir o custo. A mudança das políticas de acesso à AD Azure reduz a dependência de soluções personalizadas ou no local para acesso condicional e os seus custos de infraestrutura.

### <a name="license-requirements"></a>Requisitos de licença

Consulte [os requisitos](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)da licença de acesso condicional .

Se forem necessárias funcionalidades adicionais, poderá também necessitar de licenças relacionadas. Para mais informações, consulte [os preços do Diretório Ativo azure.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="prerequisites"></a>Pré-requisitos

* Um inquilino Azure AD em funcionamento com Azure AD Premium ou licença de teste habilitada. Se necessário, [crie um de graça.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Uma conta com privilégios de administrador de Acesso Condicional.

* Um utilizador não administrador com uma palavra-passe que conheça, como testuser. Se precisar de criar um utilizador, consulte [Quickstart: Adicione novos utilizadores ao Diretório Ativo Do Azure](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory).

* Um grupo do que o utilizador não administrador é membro. Se precisar de criar um grupo, consulte [Criar um grupo e adicionar membros no Azure Ative Directory](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal).

### <a name="training-resources"></a>Recursos de formação

Os seguintes recursos podem ser úteis à medida que aprende sobre acesso condicional:


**Vídeos**
* [O que é o Acesso Condicional?](https://youtu.be/ffMAw2IVO7A)
* [Como implementar acesso condicional?](https://youtu.be/c_izIRNJNuk)
* [Como lançar políticas de AC para utilizadores finais?](https://youtu.be/0_Fze7Zpyvc)
* [Acesso Condicional com controlos do dispositivo](https://youtu.be/NcONUf-jeS4)
* [Acesso Condicional com o Multi-Factor Authentication](https://youtu.be/Tbc-SU97G-w)
* [Acesso Condicional na Mobilidade Empresarial + Segurança](https://youtu.be/A7IrxAH87wc)
* [Acesso Condicional baseado em dispositivos](https://in.video.search.yahoo.com/search/video;_ylt=AwrPiBX0yHRcZiMAhFa7HAx.;_ylu=X3oDMTB0N2poMXRwBGNvbG8Dc2czBHBvcwMxBHZ0aWQDBHNlYwNwaXZz?p=conditional+access+videos+microsoft&fr2=piv-web&fr=mcafee)

**Cursos online no PluralSight**
* [Gestão de Identidade de Design no Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
* [Autenticação de Design para microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
* [Autorização de Design para o Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)

**FAQ**

[FaQ de acesso condicional Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/faqs)
## <a name="plan-the-deployment-project"></a>Planear o projeto de implantação

Considere as suas necessidades organizacionais enquanto determina a estratégia para esta implantação no seu ambiente.
### <a name="engage-the-right-stakeholders"></a>Envolver as partes interessadas certas
Quando os projetos tecnológicos falham, normalmente fazem-no devido a expectativas desajustadas sobre impacto, resultados e responsabilidades. Para evitar estas armadilhas, [certifique-se](https://aka.ms/deploymentplans) de que está a envolver as partes interessadas certas e que as funções do projeto são claras.

### <a name="plan-communications"></a>Planear as comunicações
A comunicação é fundamental para o sucesso de qualquer novo serviço. Comunicar proativamente com os seus utilizadores como a sua experiência vai mudar, quando irá mudar, e como obter suporte se experimentarem problemas.

### <a name="plan-a-pilot"></a>Planeie um piloto
Quando estiverem prontas novas políticas para o seu ambiente, desempenhá-las em fases no ambiente de produção. Em primeiro lugar, aplique uma política a um pequeno conjunto de utilizadores num ambiente de teste e verifique se a política se comporta como esperado. Consulte [as melhores práticas para um piloto.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)

> [!NOTE]
> Para lançar novas políticas não específicas aos administradores, exclua todos os administradores. Isto garante que os administradores ainda podem aceder à apólice e fazer alterações ou revogá-la se houver um impacto significativo. Valide sempre a apólice com grupos de utilizadores mais pequenos antes de se aplicar a todos os utilizadores.

## <a name="understand-ca-policy-components"></a>Compreender os componentes da política da AC

As políticas ca são declarações se então: Se uma condição atribuída for satisfeita, aplique estes controlos de acesso. 

![Conditional Access overview (Descrição geral do Acesso Condicionado)](media/plan-conditional-access/10.png)

Ao configurar as políticas de CA, as condições são chamadas *de atribuições*. As políticas ca permitem-lhe impor controlos de acesso nas aplicações da sua organização com base em determinadas atribuições.

![atribuições e controlos de acesso ](media/plan-conditional-access/ca-policy-access.png)


Para mais informações, consulte [a Construção de uma política da AC.](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies)

[Atribuições](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) definem o

* [utilizadores e grupos](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-users-groups) a serem afetados pela política

* [aplicativos de nuvem ou ações](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps) a que a política vai aplicar 

* [condições](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions) em que a política se aplicará. 
<p>

![criar tela de política](media/plan-conditional-access/create-policy.png)

[As](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) definições de controlo de acesso determinam como impor uma política:

* [Conceder ou bloquear](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-grant) o acesso a aplicações na nuvem.

* [Os controlos de sessão](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session) permitem experiências limitadas dentro de aplicações específicas na nuvem.

### <a name="ask-the-right-questions-to-build-your-policies"></a>Faça as perguntas certas para construir as suas políticas

As políticas respondem a perguntas sobre quem deve aceder aos seus recursos, a que recursos devem aceder e em que condições. As políticas podem ser concebidas para conceder acesso ou bloquear o acesso. Certifique-se de fazer as perguntas certas sobre o que a sua política está a tentar alcançar. 

Documente as respostas às perguntas de cada política antes de a construir. 

#### <a name="common-questions-about-assignments"></a>Perguntas comuns sobre atribuições

[Utilizadores e Grupos](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-users-groups)

* Que utilizadores e grupos serão incluídos ou excluídos da apólice?

* Esta política inclui todos os utilizadores, grupo específico de utilizadores, funções de diretório ou utilizadores externos?

[Aplicações na app ou ações](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-cloud-apps)

* A que aplicação a política se aplicará?

* Que ações de utilizador estarão sujeitas a esta política?

[Condições](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions)

* Que plataformas de dispositivos serão incluídas ou excluídas da apólice?

* Quais são os locais de confiança da organização?

* Que locais serão incluídos ou excluídos da apólice?

* Que tipos de aplicativos de cliente (navegador, mobile, clientes de desktop, aplicações com métodos de autenticação legado) serão incluídos ou excluídos da apólice?

* Tem políticas que conduzam excluindo dispositivos Azure AD AD ou Hybrid Azure AD aderiu a dispositivos de políticas? 

* Se utilizar [a Proteção de Identidade,](https://docs.microsoft.com/azure/active-directory/identity-protection/overview)pretende incorporar a proteção contra o risco de inscrição?

#### <a name="common-questions-about-access-controls"></a>Questões comuns sobre controlos de acesso

[Concessão ou Bloco](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-grant) 

Deseja conceder acesso aos recursos exigindo um ou mais dos seguintes?

* Requerer MFA

* Exigir que o dispositivo seja marcado como conforme

* Requerer dispositivo híbrido Azure AD

* Exigir aplicação de cliente aprovada

* Pedir uma política de proteção de aplicações

[Controlo de sessão](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-session)

Deseja impor algum dos seguintes controlos de acesso em aplicações na nuvem?

* Use permissões forçadas por aplicações

* Utilizar controlo de aplicativos de acesso condicional

* Impor frequência de inscrição

* Use sessões persistentes de navegador

### <a name="access-token-issuance"></a>Emissão de fichas de acesso

É importante entender como são emitidos fichas de acesso. 

![Diagrama de emissão de fichas de acesso](media/plan-conditional-access/CA-policy-token-issuance.png)

**Note especialmente que se não for necessária nenhuma atribuição, e nenhuma política de AC estiver em vigor, o comportamento padrão é emitir um sinal**de acesso . 

Por exemplo, considere uma política em que:

Se o utilizador estiver no Grupo 1, em seguida, force o MFA a aceder à App 1.

Se um utilizador não no Grupo 1 tentar aceder à aplicação não existe "se" condição é cumprida, e é emitido um sinal. Excluir utilizadores fora do Grupo 1 requer uma política separada para bloquear todos os outros utilizadores.

## <a name="follow-best-practices"></a>Siga as melhores práticas

O quadro de Acesso Condicional proporciona-lhe uma grande flexibilidade de configuração. No entanto, uma grande flexibilidade também significa que deve rever cuidadosamente cada política de configuração antes de a lançar para evitar resultados indesejáveis.

### <a name="apply-ca-policies-to-every-app"></a>Aplicar políticas de CA em cada app

As fichas de acesso são emitidas por defeito se uma condição da Política ca não acionar um controlo de acesso. Certifique-se de que cada aplicação tem pelo menos uma política de acesso condicional aplicada

> [!IMPORTANT]
> Tenha muito cuidado ao usar o bloco e todas as aplicações numa única política. Isto poderia bloquear administradores fora do Portal da Administração Azure, e exclusões não podem ser configuradas para importantes pontos finais como o Microsoft Graph.

### <a name="minimize-the-number-of-ca-policies"></a>Minimizar o número de políticas de AC

A criação de uma política para cada aplicação não é eficiente e leva a uma administração difícil. O Acesso Condicional só aplicará as primeiras 195 políticas por utilizador. Recomendamos que analise as suas apps e as agrupe em aplicações que tenham os mesmos requisitos de recursos para os mesmos utilizadores. Por exemplo, se todas as aplicações do Office 365 ou todas as aplicações de RH tiverem os mesmos requisitos para os mesmos utilizadores, criem uma única política e incluam todas as aplicações às quais se aplica. 

### <a name="set-up-emergency-access-accounts"></a>Criar contas de acesso de emergência

Se configurar mal uma apólice, pode bloquear as organizações do portal Azure. Atenuar o impacto do bloqueio acidental do administrador através da criação de duas ou mais contas de [acesso de emergência](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) na sua organização.

* Crie uma conta de utilizador dedicada à administração de políticas e excluída de todas as suas políticas.

* Quebrar cenário de vidro para ambientes híbridos:

  * Crie um grupo de segurança no local e sincronize-o com a AD Azure. O grupo de segurança deve conter a sua conta dedicada à administração de políticas. 

   * Isentar este grupo de segurança formar todas as políticas da AC.

   * Quando ocorrer uma paragem de serviço, adicione os seus outros administradores ao grupo no local, conforme apropriado, e force uma sincronização. Isto anima a sua isenção às políticas da AC.

### <a name="set-up-report-only-mode"></a>Configurar o modo apenas para relatórios

Pode ser difícil prever o número e os nomes dos utilizadores afetados por iniciativas comuns de implantação tais como:

* bloqueando a autenticação do legado
* exigindo MFA
* implementação de políticas de risco de inscrição

[O modo apenas de relatório](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) permite que os administradores avaliem o impacto das políticas de AC antes de as permitir em seu ambiente.

Aprenda a configurar o [modo apenas](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-report-only)de relatório numa política de CA .

### <a name="plan-for-disruption"></a>Plano de interrupção

Se contar com um único controlo de acesso, como o MFA ou uma localização de rede, para proteger os seus sistemas informáticos, é suscetível a falhas de acesso se esse controlo de acesso único ficar indisponível ou mal configurado. Para reduzir o risco de bloqueio durante perturbações imprevistas, [planeie estratégias](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls) a adotar para a sua organização.

### <a name="set-naming-standards-for-your-policies"></a>Detete os padrões de nomeação para as suas políticas

O padrão de nomeação ajuda-o a encontrar políticas e a compreender o seu propósito sem as abrir no portal de administração Azure. Recomendamos que nomeie a sua política para mostrar:

* Um número de sequência

* As aplicações cloud que se aplica

* A resposta

* A quem se aplica

* Quando se aplica (se aplicável)

![Padrão de nomeação](media/plan-conditional-access/11.png)

**Exemplo;** Uma política para exigir que os Utilizadores de Marketing acedam à aplicação Dynamics CRP a partir de redes externas pode ser:

![Padrão de nomeação](media/plan-conditional-access/naming-example.png)

Um nome descritivo ajuda-o a manter uma visão geral da sua implementação de Acesso Condicional. O Número de Sequência é útil se precisar de fazer referência a uma apólice numa conversa. Por exemplo, quando se fala com um administrador ao telefone, pode pedir-lhes que abram a política CA01 para resolver um problema.

#### <a name="naming-standards-for-emergency-access-controls"></a>Normas de nomeação para controlos de acesso de emergência

Para além das suas políticas ativas, implemente políticas de desativação que atuam como controlos de [acesso resilientes secundários em cenários](https://docs.microsoft.com/azure/active-directory/authentication/concept-resilient-controls)de paragem ou de emergência. O seu padrão de nomeação para as políticas de contingência deve incluir:
* Enable IN EMERGENCY no início para fazer com que o nome se destaque entre as outras políticas.

* O nome da perturbação deve aplicar-se.

* Um número de sequência de ordenação para ajudar o administrador a saber em que políticas de ordem devem ser ativadas.

**Exemplo**

O seguinte nome indica que esta política é a primeira de quatro políticas para permitir se houver uma perturbação do MFA:

EM01 - ENABLE IN EMERGENCY: MFA Disruption [1/4] - Exchange SharePoint: Require hybrid Azure AD join For VIP users.

### <a name="exclude-countries-from-which-you-never-expect-a-sign-in"></a>Exclua países dos quais nunca se espera um sessão.

O diretório ativo Azure permite-lhe criar [localizações nomeadas.](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) Crie um local nomeado que inclua todos os países a partir dos quais nunca esperaria que ocorresse um sessão. Em seguida, crie uma política para todas as aplicações que bloqueie o acesso a partir desse local nomeado. **Certifique-se de isentar os seus administradores desta política.**

### <a name="plan-your-policy-deployment"></a>Planeie a sua implementação de políticas

Quando estiverem prontas novas políticas para o seu ambiente, certifique-se de que revê cada política antes de a lançar para evitar resultados indesejáveis. Consulte a seguinte documentação para compreender informações importantes sobre como as políticas são aplicadas e como evitar questões

* [O que deve saber](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

* [O que deve evitar fazer](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

## <a name="common-policies"></a>Políticas comuns

Ao planear a sua solução política de CA, avalie se precisa de criar políticas para alcançar os seguintes resultados.

### <a name="require-mfa"></a>Requerer MFA

Casos de utilização comum para exigir acesso ao MFA:

* [Por administradores](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-admin-mfa)

* [Para aplicações específicas](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-mfa)

* [Para todos os utilizadores](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-all-users-mfa)

* [A partir de locais de rede, não se confia](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)

* [Para a Gestão Azure](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-azure-management)

### <a name="respond-to-potentially-compromised-accounts"></a>Responder a contas potencialmente comprometidas

Com as políticas de AC, pode implementar respostas automatizadas aos inscrições por identidades potencialmente comprometidas. A probabilidade de uma conta estar comprometida é expressa sob a forma de níveis de risco. Existem dois níveis de risco calculados pela Proteção de Identidade: risco de inscrição e risco do utilizador. As três políticas padrão que se seguem podem ser ativadas.

* [Exigir que todos os utilizadores se registem para o MFA](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

* [Requerer uma alteração de senha para utilizadores de alto risco](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

* [Exigir MFA para utilizadores com risco médio ou elevado de inscrição](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-risk)

### <a name="require-managed-devices"></a>Exigir dispositivos geridos

A proliferação de dispositivos suportados para aceder aos seus recursos na nuvem ajuda a melhorar a produtividade dos seus utilizadores. Provavelmente não quer que certos recursos no seu ambiente sejam acedidos por dispositivos com um nível de proteção desconhecido. Para esses recursos, [requerer que os utilizadores só possam aceder a eles através de um dispositivo gerido](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices).

### <a name="require-approved-client-apps"></a>Exigir aplicações aprovados do cliente

Os colaboradores usam os seus dispositivos móveis para tarefas pessoais e de trabalho. Para cenários BYOD deve decidir se gere todo o dispositivo ou apenas os dados do mesmo. se gerir apenas dados e acesso, pode [exigir aplicações em nuvem aprovadas](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) que possam proteger os seus dados corporativos. por exemplo, pode exigir que o e-mail seja acedido apenas via outlook móvel, e não através de um programa de correio genérico.

### <a name="block-access"></a>Bloquear o acesso

A opção de [bloquear todos os acessos](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-block-access) é poderosa. Pode ser usado, por exemplo, quando está a migrar uma aplicação para o Azure AD, mas ainda não está pronto para que ninguém assine. Acesso ao bloco: 

* Substitui todas as outras atribuições para um utilizador

* Tem o poder de impedir toda a sua organização de se inscrever no seu inquilino.

> [!IMPORTANT]
> Se criar uma política para bloquear o acesso a todos os utilizadores, não se esqueça de excluir as contas de acesso de emergência e considere excluir todos os administradores da apólice.

Outros cenários comuns em que pode bloquear o acesso aos seus utilizadores são:

* [Bloqueie certas localizações da rede](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-policy-location) para aceder às suas aplicações na nuvem. Pode usar esta política para bloquear certos países de onde sabe que o tráfego não deve vir.

* A Azure AD suporta a autenticação do legado. No entanto, a autenticação do legado não suporta mfa e muitos ambientes exigem que ele aborde a segurança da identidade. Neste caso, pode [bloquear aplicações utilizando a autenticação do legado](https://docs.microsoft.com/azure/active-directory/conditional-access/block-legacy-authentication) de aceder aos recursos dos seus inquilinos.

## <a name="build-and-test-policies"></a>Políticas de construção e teste

Em cada fase da sua implementação, certifique-se de que está a avaliar que os resultados são como esperado. 

Quando estiverem prontas novas políticas, implante-as em fases no ambiente de produção:

* Fornecer comunicação de mudança interna aos utilizadores finais.

* Comece com um pequeno conjunto de utilizadores e verifique se a apólice se comporta como esperado.

* Quando expande uma política para incluir mais utilizadores, continue a excluir todos os administradores. Excluindo os administradores garante que alguém ainda tem acesso a uma apólice se for necessária uma mudança.

* Aplicar uma política a todos os utilizadores apenas depois de ser completamente testada. Certifique-se de que tem pelo menos uma conta de administrador à qual uma apólice não se aplica.

### <a name="create-test-users"></a>Criar utilizadores de teste

Crie um conjunto de utilizadores de teste que reflitam os utilizadores no seu ambiente de produção. A criação de utilizadores de testes permite verificar o funcionao das políticas como esperado antes de afetar utilizadores reais e potencialmente perturbar o seu acesso a apps e recursos.

Algumas organizações têm inquilinos de teste para este fim. No entanto, pode ser difícil recriar todas as condições e aplicações num inquilino de teste para testar totalmente o resultado de uma política.

### <a name="create-a-test-plan"></a>Criar um plano de teste

O plano de teste é importante para ter uma comparação entre os resultados esperados e os resultados reais. Deve ter sempre uma expectativa antes de testar algo. A tabela que se segue descreve casos de teste exemplo. Ajuste os cenários e resultados esperados com base na configuração das suas políticas ca.

| Política| Cenário| Resultado Esperado |
| - | - | - |
| [Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)| Utilizador autorizado assina na App enquanto está numa localização/trabalho confiável| O utilizador não é solicitado ao MFA |
| [Exigir MFA quando não estiver no trabalho](https://docs.microsoft.com/azure/active-directory/conditional-access/untrusted-networks)| Utilizador autorizado assina na App sem localização/trabalho confiável| O utilizador é solicitado ao MFA e pode iniciar sessão com sucesso |
| [Exigir MFA (para administrador)](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-baseline-protect-administrators)| Global Admin assina na App| Administrador é solicitado ao MFA |
| [Inícios de sessão de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-sign-in-risk-policy)| User assina na App usando um [navegador Tor](https://microsoft.sharepoint.com/azure/active-directory/active-directory-identityprotection-playbook)| Administrador é solicitado ao MFA |
| [Gestão de dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)| Utilizador autorizado tenta iniciar sessão a partir de um dispositivo autorizado| Acesso Concedido |
| [Gestão de dispositivos](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)| Utilizador autorizado tenta iniciar sessão a partir de um dispositivo não autorizado| Acesso bloqueado |
| [Alteração de palavra-passe para utilizadores de risco](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-user-risk-policy)| Utilizador autorizado tenta iniciar sessão com credenciais comprometidas (sinal de alto risco)| O utilizador é solicitado a alterar a palavra-passe ou o acesso é bloqueado com base na sua política |


 

### <a name="configure-the-test-policy"></a>Configure a política de teste

No [portal Azure,](https://portal.azure.com/)configura as políticas ca no âmbito do Diretório Ativo do Azure > Segurança > Acesso Condicional.

Se quiser saber mais sobre como criar políticas ca, consulte este exemplo: [a política da AC para solicitar o MFA quando um utilizador se inscreve no portal Azure](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json). Este QuickStart ajuda-o a:

* Familiarize-se com a interface do utilizador

* Obtenha uma primeira impressão de como funciona o Acesso Condicional

### <a name="enable-the-policy-in-report-only-mode"></a>Ativar a política em modo apenas de relatório

Para avaliar o impacto da sua política, comece por permitir a política no [modo apenas para relatórios](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only). As políticas apenas de relatório são avaliadas durante o sign-in, mas os controlos de subvenção e os controlos de sessão não são aplicados. Uma vez que guarde a política em modo apenas de relatório, pode ver o impacto nos registos em tempo real nos registos de inscrição. A partir dos registos de início de sessão, selecione um evento e navegue até ao separador apenas do Relatório para ver o resultado de cada política apenas de relatório.


![reportar apenas modo ](media/plan-conditional-access/report-only-mode.png)

Selecionando a política, também pode ver como as atribuições e controlos de acesso da apólice foram avaliados utilizando o ecrã de detalhes da Política. Para que uma política se aplique a um sign-in, cada uma das atribuições configuradas deve ser satisfeita. 

### <a name="understand-the-impact-of-your-policies-using-the-insights-and-reporting-workbook"></a>Compreenda o impacto das suas políticas utilizando o livro Insights and Reporting

Pode ver o impacto agregado das suas políticas de Acesso Condicional no livro Insights e Reporting. Para aceder ao livro de trabalho, precisa de uma subscrição do Monitor Azure e terá de [transmitir os seus registos de entrada para um espaço](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)de trabalho de Log Analytics . 

### <a name="simulate-sign-ins-using-the-what-if-tool"></a>Simular inscrições utilizando a ferramenta "e se"

Outra forma de validar a sua política de Acesso Condicional é utilizando a [ferramenta "e se",](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access-what-if)que simula quais as políticas aplicáveis a um utilizador que sessão se aplica em circunstâncias hipotéticas. Selecione os atributos de entrada que pretende testar (como utilizador, aplicação, plataforma do dispositivo e localização) e veja quais as políticas que se aplicariam.

> [!NOTE] 
> Embora uma execução simulada lhe dê uma boa ideia do impacto que uma política de AC tem, não substitui um teste real.

### <a name="test-your-policy"></a>Teste a sua política

Efetue cada teste no seu plano de teste com os utilizadores do teste.

**Certifique-se de testar os critérios de exclusão de uma política**. Por exemplo, pode excluir um utilizador ou grupo de uma política que exija MFA. Teste se os utilizadores excluídos forem solicitados para mfa, porque a combinação de outras políticas pode exigir MFA para esses utilizadores.

### <a name="roll-back-policies"></a>Reverter as políticas

Caso necessite de reverter as suas políticas recentemente implementadas, utilize uma ou mais das seguintes opções:

* **Desativar a apólice.** A desativação de uma apólice garante que não se aplica quando um utilizador tenta iniciar sessão. Pode sempre voltar e ativar a política quando quiser usá-la.

![ativar imagem política](media/plan-conditional-access/enable-policy.png)

* **Excluir um utilizador ou grupo de uma política.** Se um utilizador não conseguir aceder à aplicação, pode optar por excluir o utilizador da apólice.

![excluir utilizadores e grupos](media/plan-conditional-access/exclude-users-groups.png)

> [!NOTE]
>  Esta opção deve ser utilizada com moderação, apenas em situações em que o utilizador é de confiança. O utilizador deve ser adicionado de volta à política ou grupo o mais rapidamente possível.

* **Apague a apólice.** Se a apólice já não for necessária, [apague-a.](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json)

## <a name="manage-access-to-cloud-apps"></a>Gerir o acesso a aplicações na nuvem

Utilize as seguintes opções de Gestão para controlar e gerir as suas políticas ca:

![acesso à gestão](media/plan-conditional-access/manage-access.png)


### <a name="named-locations"></a>Localizações com nome

O estado de localização de uma política de CA permite-lhe ligar as definições de controlos de acesso às localizações da rede dos seus utilizadores. Com [Localizações Nomeadas,](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition)pode criar agrupamentos lógicos de gamas de endereços IP ou países e regiões.

### <a name="custom-controls"></a>Controlos personalizados

[Os controlos personalizados](https://docs.microsoft.com/azure/active-directory/conditional-access/controls) redirecionam os seus utilizadores para um serviço compatível para satisfazer os requisitos de autenticação fora do Azure AD. Para satisfazer este controlo, o navegador de um utilizador é redirecionado para o serviço externo, executa qualquer autenticação necessária e é depois redirecionado para a AD Azure. A Azure AD verifica a resposta e, se o utilizador foi autenticado ou validado com sucesso, o utilizador continua no fluxo de Acesso Condicional.

### <a name="terms-of-use"></a>Termos de utilização

Antes de aceder a certas aplicações na nuvem no seu ambiente, pode obter o consentimento dos utilizadores através da aceitação dos seus Termos de Utilização (ToU). Siga este [Quickstart para criar Termos de Utilização](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou).

### <a name="classic-policies"></a>Políticas clássicas

No [portal Azure,](https://portal.azure.com/)pode encontrar as suas políticas de CA no âmbito do Diretório Ativo do Azure > acesso condicional > Acesso Condicional. A sua organização também pode ter políticas de AC mais antigas não criadas usando esta página. Estas políticas são conhecidas como políticas clássicas. Recomendamos que [considere migrar estas políticas clássicas no portal Azure.](https://docs.microsoft.com/azure/active-directory/conditional-access/best-practices)

## <a name="troubleshoot-conditional-access"></a>Resolver problemas de acesso condicional

Quando um utilizador estiver a ter um problema com uma política de CA, colete as seguintes informações para facilitar a resolução de problemas.

* Nome do princípio do utilizador

* Nome do exibição do utilizador

* Nome do sistema operativo

* Carimbo de tempo (aproximação está ok)

* Aplicação-alvo

* Tipo de aplicação do cliente (navegador vs cliente)

* Id de correlação (isto é exclusivo do sign-in)

Se o utilizador recebeu uma mensagem com um link Mais detalhes, pode recolher a maior parte desta informação para si.

![Não consigo chegar à mensagem de erro da aplicação](media/plan-conditional-access/cant-get-to-app.png)

Depois de recolher a informação, consulte os seguintes recursos:

* [Problemas de login com Acesso Condicional](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) – Compreender resultados inesperados de entrada relacionados com acesso condicional utilizando mensagens de erro e registo de login de AD Azure.

* [Utilização da ferramenta What-If](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access-what-if) - Compreenda por que razão uma apólice foi ou não aplicada a um utilizador numa circunstância específica ou se uma política se aplicaria num estado conhecido.

## <a name="next-steps"></a>Passos Seguintes

[Saiba mais sobre a autenticação multifactor](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)

[Saiba mais sobre proteção de identidade](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection)

[Gerir as políticas ca com a Microsoft Graph API](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)
