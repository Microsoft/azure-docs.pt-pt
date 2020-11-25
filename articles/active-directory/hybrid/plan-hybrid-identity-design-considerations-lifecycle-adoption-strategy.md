---
title: Design de identidade híbrida - estratégia de adoção de ciclo de vida Azure / Microsoft Docs
description: Ajuda a definir as tarefas híbridas de gestão da identidade de acordo com as opções disponíveis para cada fase de ciclo de vida.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 420b6046-bd9b-4fce-83b0-72625878ae71
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/30/2018
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf68406d4b0806e1d533e0bb8669a01939387989
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95997754"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Determinar estratégia híbrida de adoção de ciclo de vida
Nesta tarefa, definirá a estratégia de gestão de identidade para a sua solução de identidade híbrida para satisfazer os requisitos de negócio definidos nas [tarefas de gestão de identidade híbrida.](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)

Para definir as tarefas híbridas de gestão da identidade de acordo com o ciclo de vida de identidade de ponta a ponta apresentado no início deste passo, terá de considerar as opções disponíveis para cada fase do ciclo de vida.

## <a name="access-management-and-provisioning"></a>Gestão de acessos e provisionamento
Com uma boa solução de gestão de acesso a conta, a sua organização pode rastrear precisamente quem tem acesso a que informação em toda a organização.

O controlo de acesso é uma função crítica de um sistema centralizado de provisionamento de pontos únicos. Além de proteger informações sensíveis, os controlos de acesso expõem as contas existentes que têm autorizações não aprovadas ou que já não são necessárias. Para controlar contas obsoletas, o sistema de provisionamento liga informações de conta com informações autorizadas sobre os utilizadores que possuem as contas. A informação de identidade do utilizador autoritária é normalmente mantida nas bases de dados e nos diretórios de recursos humanos.

As contas em empresas de TI sofisticadas incluem centenas de parâmetros que definem as autoridades, e estes detalhes podem ser controlados pelo seu sistema de provisionamento. Os novos utilizadores podem ser identificados com os dados que fornece a partir da fonte autoritária. A capacidade de aprovação do pedido de acesso inicia os processos que aprovam (ou rejeitam) o fornecimento de recursos para os mesmos.

| Fase de gestão do ciclo de vida | No local | Cloud | Híbrido |
| --- | --- | --- | --- |
| Gestão e Provisionamento de Contas |Ao utilizar a função de servidor dos Serviços de Domínio do Active Directory® (AD DS), pode criar uma infraestrutura escalável, segura e gerível para a gestão de recursos e utilizadores e fornecer o suporte para as aplicações com diretório ativado como o Microsoft Exchange Server. <br><br> [Você pode providenciar grupos em DS AD através de um gestor de identidade](/previous-versions/mim/ff686261(v=ws.10)) <br>[Pode providenciar utilizadores em DS AD](/previous-versions/mim/ff686263(v=ws.10)) <br><br> Os administradores podem usar o controlo de acesso para gerir o acesso do utilizador a recursos partilhados para fins de segurança. No Ative Directory, o controlo de acesso é administrado ao nível do objeto, definindo diferentes níveis de acesso, ou permissões, a objetos como Controlo Total, Escrita, Leitura ou Sem Acesso. O controlo de acesso no Ative Directory define como diferentes utilizadores podem usar objetos ative directy. Por predefinição, as permissões em objetos no Ative Directory são definidas para a definição mais segura. |Tem de criar uma conta para todos os utilizadores que acedam a um serviço de cloud da Microsoft. Também pode alterar as contas dos utilizadores ou eliminá-las quando já não são necessárias. Por predefinição, os utilizadores não têm permissões de administrador, mas podem atribuí-las opcionalmente. <br><br> Dentro do Azure Ative Directory, uma das principais características é a capacidade de gerir o acesso aos recursos. Estes recursos podem fazer parte do diretório, como no caso de permissões para gerir objetos através de funções no diretório, ou dos recursos externos ao diretório, como aplicações SaaS, serviços do Azure e sites SharePoint ou recursos no local. <br><br> No centro da solução de gestão de acessos da Azure Ative Directory está o grupo de segurança. O proprietário do recurso (ou o administrador do diretório), pode atribuir um grupo para fornecer um determinado direito de acesso aos recursos que possui. Os membros do grupo terão acesso, e o titular do recurso pode delegar o direito de gerir a lista de membros de um grupo para outra pessoa – como um gerente de departamento ou um administrador de helpdesk<br> <br> Os grupos managing na secção AD Azure, fornecem mais informações sobre a gestão do acesso através de grupos. |Alargar as identidades do Diretório Ativo para a nuvem através da sincronização e da federação |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
O Azure role-based access control (Azure RBAC) usa papéis e políticas de provisionamento para avaliar, testar e impor os seus processos e regras de negócio para conceder acesso aos utilizadores. Os administradores-chave criam políticas de provisionamento e atribuem os utilizadores a funções e definem conjuntos de direitos aos recursos para estas funções. O Azure RBAC alarga a solução de gestão de identidade para utilizar processos baseados em software e reduzir a interação manual do utilizador no processo de provisionamento.
O Azure RBAC permite à empresa restringir o número de operações que um indivíduo pode fazer uma vez que tenha acesso ao portal Azure. Ao utilizar o Azure RBAC para controlar o acesso ao portal, o IT Admins ca delegado de acesso utilizando as seguintes abordagens de gestão de acesso:

* **Atribuição de funções baseada em grupo**: Pode atribuir acesso a grupos AD Azure que podem ser sincronizados a partir do seu Diretório Ativo local. Isto permite-lhe alavancar os investimentos existentes que a sua organização tem feito em ferramentas e processos de gestão de grupos. Também pode utilizar a função de gestão de grupos delegada do Azure AD Premium.
* **Alavancagem incorporada em funções no Azure**: Pode utilizar três funções — Proprietário, Colaborador e Leitor, para garantir que os utilizadores e grupos tenham permissão para fazer apenas as tarefas que precisam para fazer o seu trabalho.
* **Acesso granular aos recursos**: Pode atribuir funções a utilizadores e grupos para uma determinada subscrição, grupo de recursos ou um recurso Azure individual, como um website ou base de dados. Desta forma, pode garantir que os utilizadores têm acesso a todos os recursos de que necessitam e sem acesso a recursos que não precisam de gerir.

## <a name="provisioning-and-other-customization-options"></a>Disposição e outras opções de personalização
A sua equipa pode usar planos de negócios e requisitos para decidir quanto para personalizar a solução de identidade. Por exemplo, uma grande empresa pode exigir um plano de lançamento faseado para fluxos de trabalho e adaptadores personalizados que se baseia numa linha temporal para provisões incrementais que são amplamente utilizadas em geografias. Outro plano de personalização pode prever que duas ou mais aplicações sejam a ser a provisionadas em toda uma organização, após testes bem sucedidos. A interação entre o utilizador e a aplicação pode ser personalizada e os procedimentos de a provisionamento de recursos podem ser alterados para acomodar o fornecimento automatizado.

Pode desprovisionar para remover um serviço ou componente. Por exemplo, desprovisionar uma conta significa que a conta é eliminada de um recurso.

O modelo híbrido de a provisionamento de recursos combina pedidos e abordagens baseadas em papéis, ambas apoiadas pela Azure AD. Para um subconjunto de colaboradores ou sistemas geridos, uma empresa pode querer automatizar o acesso com uma atribuição baseada em funções. Uma empresa também pode lidar com todos os outros pedidos de acesso ou exceções através de um modelo baseado em pedidos. Algumas empresas podem começar com a atribuição manual, e evoluir para um modelo híbrido, com a intenção de uma implementação totalmente baseada em papéis num futuro futuro.

Outras empresas poderão achar impraticável, por razões comerciais, alcançar um provisionamento completo baseado em papéis e visar uma abordagem híbrida como um objetivo desejado. Ainda assim, outras empresas poderão ficar satisfeitas apenas com o provisionamento baseado em pedidos e não querer investir esforços adicionais para definir e gerir políticas de provisionamento automatizados baseadas em funções.

## <a name="license-management"></a>Gestão de licenças
A gestão de licenças baseada em grupo em Azure AD permite que os administradores atribuam os utilizadores a um grupo de segurança e a Azure AD atribui automaticamente licenças a todos os membros do grupo. Se um utilizador for posteriormente adicionado ou removido do grupo, uma licença será automaticamente atribuída ou removida conforme o caso.

Pode utilizar grupos que sincroniza a partir de AD no local ou gerir em Azure AD. Emparelhando isto com o Azure AD premium Self-Service Group Management, pode delegar facilmente a atribuição de licenças aos decisores apropriados. Pode ter a certeza de que problemas como conflitos de licenças e dados de localização em falta são automaticamente resolvidos.

## <a name="self-regulating-user-administration"></a>Administração de utilizadores autorregulados
Quando a sua organização começa a fornecê-lo recursos em todas as organizações internas, implementa a capacidade de administração de utilizadores autorregulado. Você pode perceber as vantagens e benefícios de aprovisionar utilizadores através dos limites organizacionais. Neste ambiente, uma mudança no estado de um utilizador reflete-se automaticamente nos direitos de acesso através das fronteiras e geografias da organização. Pode reduzir os custos de provisionamento e agilizar os processos de acesso e aprovação. A implementação realiza todo o potencial de implementação de um controlo de acesso baseado em papéis para a gestão de acessos de ponta a ponta na sua organização. Pode reduzir os custos administrativos através de procedimentos automatizados para reger o fornecimento de utilizadores. Pode melhorar a segurança automatizando a aplicação da política de segurança e racionalizar e centralizar a gestão do ciclo de vida dos utilizadores e o fornecimento de recursos para as grandes populações de utilizadores.

> [!NOTE]
> Para obter mais informações, consulte configurar a Azure AD para gestão de acesso a aplicações de self service
> 
> 

Os serviços Azure AD baseados em licença funcionam ativando uma subscrição no seu diretório/inquilino de serviço Azure AD. Uma vez que a subscrição esteja ativa, as capacidades de serviço podem ser geridas por administradores de diretório/serviço e utilizadas por utilizadores licenciados. 

## <a name="integration-with-other-3rd-party-providers"></a>Integração com outros fornecedores de terceiros

O Azure Ative Directory fornece segurança de acesso a aplicações de assinatura única e reforçada a milhares de aplicações SaaS e aplicações web no local. Para mais informações, consulte [integrar aplicações com o Azure Ative Directory](../develop/quickstart-register-app.md)

## <a name="define-synchronization-management"></a>Definir gestão de sincronização
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Com esta integração, os utilizadores e organizações podem tirar partido do seguinte:

* As organizações podem fornecer aos utilizadores uma identidade híbrida comum em todos os locais ou serviços baseados na nuvem, alavancando o Windows Server Ative Directory e, em seguida, conectando-se ao Azure Ative Directory.
* Os administradores podem fornecer Acesso Condicional com base no recurso de aplicação, no dispositivo e identidade do utilizador, na localização da rede e na autenticação de vários fatores.
* Os utilizadores podem aproveitar a sua identidade comum através de contas em Azure AD para a Microsoft 365, Aplicações Intune, SaaS e aplicações de terceiros.
* Os desenvolvedores podem construir aplicações que alavancam o modelo de identidade comum, integrando aplicações em Ative Directory no local ou Azure para aplicações baseadas na nuvem

A figura a seguir tem um exemplo de uma visão de alto nível do processo de sincronização de identidade.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Processo de sincronização de identidade

Reveja a tabela seguinte para comparar as opções de sincronização:

| Opção de Gestão da Sincronização | Vantagens | Desvantagens |
| --- | --- | --- |
| Baseado em sincronização (através de DirSync ou AADConnect) |Utilizadores e grupos sincronizados a partir de instalações e nuvem <br>  **Controlo de políticas**: As políticas de conta podem ser definidas através do Ative Directory, o que dá ao administrador a capacidade de gerir políticas de palavras-passe, estação de trabalho, restrições, controlos de bloqueio e muito mais, sem ter de executar tarefas adicionais na nuvem.  <br>  **Controlo de acesso**: Pode restringir o acesso ao serviço de nuvem para que os serviços possam ser acedidos através do ambiente corporativo, através de servidores online, ou ambos. <br>  Chamadas de suporte reduzidas: Se os utilizadores tiverem menos palavras-passe para se lembrarem, são menos propensos a esquecê-las. <br>  Segurança: As identidades e informações dos utilizadores estão protegidas porque todos os servidores e serviços utilizados numa única sins de s ativação, são dominados e controlados no local. <br>  Suporte para autenticação forte: Pode utilizar a autenticação forte (também chamada de autenticação de dois fatores) com o serviço de nuvem. No entanto, se utilizar uma autenticação forte, deve utilizar uma única placa de sinalização. | |
| Baseado na Federação (através de AD FS) |Ativado pelo Serviço de Token de Segurança (STS). Quando configurar um STS para fornecer um único acesso de acesso de acesso com um serviço de cloud da Microsoft, estará a criar uma confiança federada entre o seu STS no local e o domínio federado que especificou no seu inquilino AD Azure. <br> Permite que os utilizadores finais utilizem o mesmo conjunto de credenciais para obter acesso a múltiplos recursos <br>os utilizadores finais não têm de manter vários conjuntos de credenciais. No entanto, os utilizadores têm de fornecer as suas credenciais a cada um dos cenários participantes., B2B e B2C apoiados. |Requer pessoal especializado para implantação e manutenção dedicadas nos servidores AD FS. Existem restrições ao uso de autenticação forte se pretender utilizar AD FS para o seu STS. Para obter mais informações, consulte [opções avançadas de configuração para AD FS 2.0](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/hh237448(v=ws.10)). |

> [!NOTE]
> Para mais informações consulte, [integrando as suas identidades no local com o Azure Ative Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Consulte também
[Visão geral de considerações de design](plan-hybrid-identity-design-considerations-overview.md)