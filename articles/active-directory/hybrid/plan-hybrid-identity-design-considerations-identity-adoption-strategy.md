---
title: Design de identidade híbrida - estratégia de adoção Azure [ Microsoft Docs
description: Com controlo de acesso condicional, o Diretório Ativo Azure verifica as condições específicas que escolhe ao autenticar o utilizador e antes de permitir o acesso à aplicação. Uma vez cumpridas essas condições, o utilizador é autenticado e é permitido o acesso à aplicação.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/26/2020
ms.locfileid: "67109324"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>Defina uma estratégia híbrida de adoção de identidade
Nesta tarefa, define a estratégia híbrida de adoção de identidade para a sua solução de identidade híbrida para satisfazer os requisitos de negócio que foram discutidos em:

* [Determinar as necessidades empresariais](plan-hybrid-identity-design-considerations-business-needs.md)
* [Determinar os requisitos de sincronização do diretório](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Determinar requisitos de autenticação de vários fatores](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir estratégia de necessidades de negócio
A primeira tarefa aborda a determinação das necessidades empresariais das organizações.  Isto pode ser muito amplo e o sacana pode ocorrer se você não tiver cuidado.  No início, mantenha-o simples, mas lembre-se sempre de planear um design que irá acomodar e facilitar a mudança no futuro.  Independentemente de se trata de um design simples ou extremamente complexo, o Azure Ative Directory é a plataforma Microsoft Identity que suporta o Office 365, microsoft Online Services e aplicações conscientes da nuvem.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três principais cenários de integração que são identidades na nuvem, identidades sincronizadas e identidades federadas.  Devia supor a adoção de uma destas estratégias de integração.  A estratégia que escolher pode variar e as decisões na escolha de um podem incluir, que tipo de experiência de utilizador pretende proporcionar, tem uma infraestrutura existente, e qual é a mais rentável.  

![cenários de integração](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

Os cenários definidos na figura acima são:

* **Identidades em nuvem**: estas são identidades que existem apenas na nuvem.  No caso da Azure AD, residiriam especificamente no seu diretório Azure AD.
* **Sincronizado**: estas são identidades que existem no local e na nuvem.  Utilizando o Azure AD Connect, estes utilizadores são criados ou unidos com as contas AD Azure existentes.  O hash de senha do utilizador é sincronizado desde o ambiente no local até à nuvem no que é chamado de hash de senha.  Ao utilizar sincronizado, a única ressalva é que, se um utilizador é desativado no ambiente no local, pode levar até três horas para que esse estado de conta apareça em Azure AD.  Isto deve-se ao intervalo de tempo de sincronização.
* **Federados**: estas identidades existem tanto no local como na nuvem.  Utilizando o Azure AD Connect, estes utilizadores são criados ou unidos com as contas AD Azure existentes.  

> [!NOTE]
> Para obter mais informações sobre as opções de Sincronização, leia [Integrando as suas identidades no local com o Diretório Ativo Azure](whatis-hybrid-identity.md).
> 
> 

O quadro seguinte ajuda a determinar as vantagens e desvantagens de cada uma das seguintes estratégias:

| Estratégia | Vantagens | Desvantagens |
| --- | --- | --- |
| **Identidades em nuvem** |Mais fácil de gerir para uma pequena organização. <br> Nada para instalar no local. Não é necessário nenhum hardware adicional<br>Facilmente desativado se o utilizador deixar a empresa |Os utilizadores terão de iniciar sessão no acesso a cargas de trabalho na nuvem <br> As palavras-passe podem ou não ser as mesmas para identidades em nuvem e no local |
| **Sincronizado** |Palavra-passe no local autentica tanto no local como na nuvem diretórios <br>Mais fácil de gerir para pequenas, médias ou grandes organizações <br>Os utilizadores podem ter um único sign-on (SSO) para alguns recursos <br> Método preferido da Microsoft para a sincronização <br> Mais fácil de gerir |Alguns clientes podem estar relutantes em sincronizar os seus diretórios com a nuvem devido à polícia específica da empresa |
| **Federados** |Os utilizadores podem ter um único sinal (SSO) <br>Se um utilizador for rescindido ou sair, a conta pode ser imediatamente desativada e o acesso revogado,<br> Suporta cenários avançados que não podem ser realizados com sincronização |Mais passos para configurar e configurar <br> Manutenção mais elevada <br> Pode exigir hardware adicional para a infraestrutura STS <br> Pode requerer hardware adicional para instalar o servidor da federação. Software adicional é necessário se AD FS for usado <br> Requerer uma configuração extensiva para o SSO <br> Ponto crítico de falha se o servidor da federação estiver em baixo, os utilizadores não serão capazes de autenticar |

### <a name="client-experience"></a>Experiência do cliente
A estratégia que utiliza ditará a experiência de inscrição do utilizador.  As seguintes tabelas fornecem-lhe informações sobre o que os utilizadores devem esperar que a sua experiência de inscrição seja.  Nem todos os fornecedores de identidade federados apoiam a SSO em todos os cenários.

**Aplicações de rede doman e privadas:**

|  | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Navegadores web |Autenticação baseada em formulários |único sinal, às vezes necessário para fornecer id organização |
| Outlook |Pedir credenciais |Pedir credenciais |
| Skype para Negócios (Lync) |Pedir credenciais |único sinal para Lync, credenciais solicitadas para a Troca |
| OneDrive para Empresas |Pedir credenciais |único sign-on |
| Subscrição do Office Pro Plus |Pedir credenciais |único sign-on |

**Fontes externas ou não fidedignas:**

|  | Identidade sincronizada | Identidade federada |
| --- | --- | --- |
| Navegadores web |Autenticação baseada em formulários |Autenticação baseada em formulários |
| Outlook, Skype for Business (Lync), OneDrive for Business, Office subscrição |Pedir credenciais |Pedir credenciais |
| Exchange ActiveSync |Pedir credenciais |único sinal para Lync, credenciais solicitadas para a Troca |
| Aplicações móveis |Pedir credenciais |Pedir credenciais |

Se determinou, a partir da tarefa 1, que tem um IDP de terceiros ou que vai utilizar um para fornecer à federação com o Azure AD, tem de estar atento às seguintes capacidades suportadas:

* Qualquer fornecedor SAML 2.0 que esteja em conformidade com o perfil SP-Lite pode suportar a autenticação à Azure AD e aplicações associadas
* Suporta a autenticação passiva, o que facilita a autenticação à OWA, SPO, etc.
* Os clientes Exchange Online podem ser suportados através do Perfil de Cliente Melhorado SAML 2.0 (ECP)

Deve também estar ciente das capacidades que não estarão disponíveis:

* Sem o apoio da WS-Trust/Federação, todos os outros clientes ativos quebram
  * Isto significa que nenhum cliente Lync, cliente OneDrive, Subscrição de Escritório, Office Mobile antes do Office 2016
* A transição do Office para a autenticação passiva permite-lhes suportar os IdPs PuroS SAML 2.0, mas o suporte continuará a ser numa base cliente-a-cliente

> [!NOTE]
> Para a lista mais atualizada leia o artigo Lista de compatibilidade da [Federação Azure AD](how-to-connect-fed-compatibility.md).
> 
> 

## <a name="define-synchronization-strategy"></a>Definir estratégia de sincronização
Nesta tarefa irá definir as ferramentas que serão usadas para sincronizar os dados da organização no local para a nuvem e que topologia deve usar.  Porque, a maioria das organizações usa o Ative Directory, a informação sobre a utilização do Azure AD Connect para responder às questões acima referidas é fornecida em alguns detalhes.  Para ambientes que não têm Diretório Ativo, existem informações sobre a utilização do FIM 2010 R2 ou mim 2016 para ajudar a planear esta estratégia.  No entanto, futuras versões do Azure AD Connect irão suportar diretórios LDAP, pelo que, dependendo da sua linha temporal, esta informação poderá ser capaz de ajudar.

### <a name="synchronization-tools"></a>Ferramentas de sincronização
Ao longo dos anos, várias ferramentas de sincronização têm existido e usadas para vários cenários.  Atualmente o Azure AD Connect é a ferramenta de eleição para todos os cenários suportados.  AAD Sync e DirSync também ainda estão por perto e podem até estar presentes no seu ambiente agora. 

> [!NOTE]
> Para obter as informações mais recentes sobre as capacidades suportadas de cada ferramenta, leia o artigo de comparação de ferramentas de [integração do Diretório.](plan-hybrid-identity-design-considerations-tools-comparison.md)  
> 
> 

### <a name="supported-topologies"></a>Topologias suportadas
Ao definir uma estratégia de sincronização, a topologia que é usada deve ser determinada. Dependendo da informação que foi determinada no passo 2, pode determinar qual a topologia adequada para usar. A única floresta, topologia azure única é a mais comum e consiste de uma única floresta de Diretório Ativo e uma instância única de Azure AD.  Isto vai ser usado na maioria dos cenários e é a topologia esperada quando utilizar a instalação Azure AD Connect Express, como mostra a figura abaixo.

![Topologies](./media/plan-hybrid-identity-design-considerations/single-forest.png) suportadas Cenário Único Da Floresta É comum que grandes e mesmo pequenas organizações tenham múltiplas florestas, como mostra a Figura 5.

> [!NOTE]
> Para obter mais informações sobre as diferentes topoologias no local e azure AD com a sincronização Azure AD Connect, leia o artigo [Topologies for Azure AD Connect](plan-connect-topologies.md).
> 
> 

![topologia multi-floresta](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

Cenário multi-florestal

Se for esse o caso, então a topologia azure ad única multi-floresta deve ser considerada se os seguintes itens forem verdadeiros:

* Os utilizadores têm apenas 1 identidade em todas as florestas – a secção de utilizadores exclusivamente identificantes abaixo descreve-a com mais detalhes.
* O utilizador autentica a floresta em que a sua identidade está localizada
* UPN e Source Anchor (id imutável) virão desta floresta
* Todas as florestas são acessíveis pela Azure AD Connect – isto significa que não precisa de ser unida e pode ser colocada num DMZ se isso facilitar isso.
* Os utilizadores têm apenas uma caixa de correio
* A floresta que acolhe a caixa de correio de um utilizador tem a melhor qualidade de dados para atributos visíveis na Lista de Endereços Globais de Intercâmbio (GAL)
* Se não houver caixa de correio no utilizador, então qualquer floresta pode ser usada para contribuir com estes valores
* Se tem uma caixa de correio ligada, então há também outra conta numa floresta diferente usada para iniciar sessão.

> [!NOTE]
> Os objetos que existem tanto no local como na nuvem estão "conectados" através de um identificador único. No contexto da Sincronização do Diretório, este identificador único é referido como o SourceAnchor. No contexto do Single Sign-On, este é referido como o ImutableId. [Conceitos de design para Azure AD Connect](plan-connect-design-concepts.md#sourceanchor) para mais considerações sobre a utilização do SourceAnchor.
> 
> 

Se o acima não for verdadeiro e tiver mais do que uma conta ativa ou mais do que uma caixa de correio, o Azure AD Connect escolherá um e ignorará o outro.  Se tiver ligado caixas de correio mas nenhuma outra conta, estas contas não serão exportadas para a AD Azure e esse utilizador não será membro de nenhum grupo.  Isto é diferente de como era no passado com o DirSync e é intencional para apoiar melhor estes cenários multiflorestais. Um cenário multiflorestal é mostrado na figura abaixo.

![vários inquilinos da AD Azure](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**Cenário multiflorestal multi-floresta múltipla da AD**

Recomenda-se ter apenas um único diretório em Azure AD para uma organização, mas é suportado uma relação 1:1 é mantida entre um servidor de sincronização Azure AD Connect e um diretório Azure AD.  Para cada instância de AD Azure, precisa de uma instalação de Azure AD Connect.  Além disso, a Azure AD, por design é isolada e os utilizadores num caso de Azure AD não serão capazes de ver os utilizadores em outro caso.

É possível e suportado ligar uma instância no local de Diretório Ativo a vários diretórios da AD Azure, como mostra a figura abaixo:

![única filtragem da floresta](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**Cenário de filtragem de uma floresta única**

Para isso, o seguinte deve ser verdade:

* Os servidores de sincronização Azure AD Connect devem ser configurados para filtrar para que cada um deles tenha um conjunto mutuamente exclusivo de objetos.  Isto fez, por exemplo, ao pesquisar cada servidor para um determinado domínio ou OU.
* Um domínio DNS só pode ser registado num único diretório Azure AD, pelo que as UPNs dos utilizadores no local devem utilizar espaços de nome separados
* Num dos casos de AD Azure, apenas os utilizadores poderão ver os utilizadores a partir da sua instância.  Não poderão ver utilizadores nos outros casos
* Apenas um dos diretórios da AD Azure pode permitir a Exchange hybrid com o AD no local
* A exclusividade mútua também se aplica ao retempo.  Isto faz com que algumas funcionalidades de retenção não suportadas com esta topologia, uma vez que estas assumem uma configuração única no local.  Isto inclui:
  * Redação do grupo com configuração padrão
  * Redação do dispositivo

O seguinte não é suportado e não deve ser escolhido como uma implementação:

* Não é suportado ter vários servidores de sincronização Azure AD Connect ligados ao mesmo diretório Azure AD mesmo que estejam configurados para sincronizar conjunto de objetos mutuamente exclusivos
* Não é suportado para sincronizar o mesmo utilizador com vários diretórios da Azure AD. 
* Também não é suportado para fazer uma mudança de configuração para fazer com que os utilizadores de um Anúncio Azure apareçam como contactos em outro diretório da AD Azure. 
* Também não é suportado para modificar a sincronização Azure AD Connect para ligar a vários diretórios da AD Azure.
* Os diretórios da Azure AD são por design isolados. Não é suportado para alterar a configuração da sincronização Azure AD Connect para ler dados de outro diretório Azure AD numa tentativa de construir uma GAL comum e unificada entre os diretórios. Também não é suportado para exportar utilizadores como contactos para outro AD no local usando o sincronizado Azure AD Connect.

> [!NOTE]
> Se a sua organização restringir os computadores da sua rede de ligar à Internet, este artigo lista os pontos finais (intervalos de endereços FQDNs, IPv4 e IPv6) que deve incluir na sua saída permitir listas de entrada e Internet Explorer Trusted Sites Zone de computadores clientes para garantir que os seus computadores podem usar com sucesso o Office 365. Para mais informações, leia o [Office 365 URLs e os intervalos](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)de endereços IP .
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Definir estratégia de autenticação de vários fatores
Nesta tarefa irá definir a estratégia de autenticação de vários fatores a utilizar.  A autenticação de multi-factores Azure vem em duas versões diferentes.  Um é baseado em nuvem e o outro está no local com base no Servidor Azure MFA.  Com base na avaliação acima, pode determinar qual a solução correta para a sua estratégia.  Utilize a tabela abaixo para determinar qual a opção de design que melhor preenche os requisitos de segurança da sua empresa:

Opções de design de vários fatores:

| Ativo para garantir | MFA na nuvem | MFA no local |
| --- | --- | --- |
| Aplicativos da Microsoft |sim |sim |
| Aplicações SaaS na galeria de aplicações |sim |sim |
| Aplicações IIS publicadas através do Proxy de Aplicação do Azure AD |sim |sim |
| Aplicações IIS não publicadas através da App AD Do Azure Proxy |não |sim |
| Acesso remoto como VPN, RDG |não |sim |

Apesar de ter decidido uma solução para a sua estratégia, ainda precisa de utilizar a avaliação de cima para onde os seus utilizadores estão localizados.  Isto pode fazer com que a solução mude.  Utilize a tabela abaixo para o ajudar a determinar isto:

| Localização do utilizador | Opção de design preferencial |
| --- | --- |
| Azure Active Directory |Multi-FactorAuthenticação na nuvem |
| Azure AD e AD no local utilizando federação com o AD FS |Ambos |
| AD Azure e no local utilizando Azure AD Connect sem sincronização de palavra-passe |Ambos |
| Azure AD e no local usando Azure AD Connect com sincronização de palavra-passe |Ambos |
| AD no local |Servidor Multi-Factor Authentication |

> [!NOTE]
> Deve também garantir que a opção de design de autenticação de vários fatores que selecionou suporta as funcionalidades necessárias para o seu design.  Para mais informações, leia [A escolha da solução de segurança de vários fatores para si](../authentication/concept-mfa-howitworks.md).
> 

## <a name="multi-factor-auth-provider"></a>Fornecedor auth multi-factor
A autenticação de vários fatores está disponível por padrão para administradores globais que possuam um inquilino do Diretório Ativo Azure. No entanto, se pretender estender a autenticação de vários fatores a todos os seus utilizadores e/ou pretender aos seus administradores globais poder usufruir de funcionalidades como o portal de gestão, saudações personalizadas e relatórios, então deve adquirir e configurar o Fornecedor de Autenticação Multifactor.

> [!NOTE]
> Deve também garantir que a opção de design de autenticação de vários fatores que selecionou suporta as funcionalidades necessárias para o seu design. 
> 
> 

## <a name="next-steps"></a>Passos seguintes
[Determinar os requisitos de proteção de dados](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Consulte também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

