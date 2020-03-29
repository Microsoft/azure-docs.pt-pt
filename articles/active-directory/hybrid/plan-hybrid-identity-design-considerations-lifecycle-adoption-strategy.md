---
title: Design de identidade híbrida - estratégia de adoção de ciclo de vida Azure [ Microsoft Docs
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
ms.openlocfilehash: 85f600c8bd46e699e80bf7b596574dc01467ef79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109321"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Determine a estratégia de adoção de ciclo de vida de identidade híbrida
Nesta tarefa, irá definir a estratégia de gestão de identidade para a sua solução de identidade híbrida para satisfazer os requisitos de negócio que definiu em [Determinar tarefas híbridas](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)de gestão de identidade.

Para definir as tarefas híbridas de gestão da identidade de acordo com o ciclo de vida de identidade de ponta a ponta apresentado no início deste passo, terá de considerar as opções disponíveis para cada fase do ciclo de vida.

## <a name="access-management-and-provisioning"></a>Gestão e provisionamento de acesso
Com uma boa solução de gestão de acesso a contas, a sua organização pode rastrear precisamente quem tem acesso a que informação em toda a organização.

O controlo de acesso é uma função crítica de um sistema centralizado de fornecimento de um ponto único. Além de proteger informações sensíveis, os controlos de acesso expõem contas existentes que têm autorizações não aprovadas ou que já não são necessárias. Para controlar contas obsoletas, o sistema de provisionamento liga informações de conta com informações autoritárias sobre os utilizadores que possuem as contas. A informação de identidade do utilizador autoritária é normalmente mantida nas bases de dados e diretórios dos recursos humanos.

As contas em empresas de TI sofisticadas incluem centenas de parâmetros que definem as autoridades, e estes detalhes podem ser controlados pelo seu sistema de provisionamento. Os novos utilizadores podem ser identificados com os dados que fornece a partir da fonte autoritária. A capacidade de aprovação do pedido de acesso inicia os processos que aprovam (ou rejeitam) o fornecimento de recursos para os mesmos.

| Fase de gestão do ciclo de vida | Nas instalações | Nuvem | Híbrido |
| --- | --- | --- | --- |
| Gestão e Provisionamento de Contas |Ao utilizar a função de servidor dos Serviços de Domínio do Active Directory® (AD DS), pode criar uma infraestrutura escalável, segura e gerível para a gestão de recursos e utilizadores e fornecer o suporte para as aplicações com diretório ativado como o Microsoft Exchange Server. <br><br> [Você pode fornecer grupos em DS AD através de um gestor de identidade](https://technet.microsoft.com/library/ff686261.aspx) <br>[Pode fornecer utilizadores em DS AD](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Os administradores podem usar o controlo de acesso para gerir o acesso dos utilizadores a recursos partilhados para fins de segurança. No Diretório Ativo, o controlo de acesso é administrado ao nível do objeto, definindo diferentes níveis de acesso, ou permissões, a objetos como Controlo Total, Escrita, Leitura ou Sem Acesso. O controlo de acesso no Diretório Ativo define como diferentes utilizadores podem usar objetos de Diretório Ativo. Por predefinição, as permissões em objetos em Diretório Ativo são definidas para a definição mais segura. |Tem de criar uma conta para todos os utilizadores que acedam a um serviço de cloud da Microsoft. Também pode alterar as contas dos utilizadores ou eliminá-las quando já não são necessárias. Por predefinição, os utilizadores não têm permissões de administrador, mas pode atribuí-las opcionalmente. <br><br> Dentro do Diretório Ativo Azure, uma das principais características é a capacidade de gerir o acesso aos recursos. Estes recursos podem fazer parte do diretório, como no caso de permissões para gerir objetos através de funções no diretório, ou dos recursos externos ao diretório, como aplicações SaaS, serviços do Azure e sites SharePoint ou recursos no local. <br><br> No centro da solução de gestão de acesso da Azure Ative Directory está o grupo de segurança. O proprietário do recurso (ou o administrador do diretório), pode atribuir um grupo para fornecer um determinado direito de acesso aos recursos que possui. Os membros do grupo terão acesso, e o proprietário do recurso pode delegar o direito de gerir a lista de membros de um grupo para outra pessoa – como um gestor de departamento ou um administrador de helpdesk<br> <br> Os grupos de gestão na secção AD Azure, fornecem mais informações sobre a gestão do acesso através de grupos. |Alargar as identidades do Diretório Ativo à nuvem através da sincronização e da federação |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
O controlo de acesso baseado em funções (RBAC) utiliza funções e políticas de provisionamento para avaliar, testar e impor os seus processos e regras de negócio para a concessão de acesso aos utilizadores. Os administradores-chave criam políticas de provisionamento e atribuem aos utilizadores funções e que definem conjuntos de direitos aos recursos para estas funções. O RBAC alarga a solução de gestão de identidade para utilizar processos baseados em software e reduzir a interação manual do utilizador no processo de provisionamento.
O Azure AD RBAC permite à empresa restringir o número de operações que um indivíduo pode fazer assim que tiver acesso ao portal Azure. Ao utilizar o RBAC para controlar o acesso ao portal, a IT Admins ca delega o acesso através das seguintes abordagens de gestão de acesso:

* **Atribuição de funções baseada em grupo:** Pode atribuir acesso a grupos AD Azure que podem ser sincronizados a partir do seu Diretório Ativo local. Isto permite-lhe alavancar os investimentos existentes que a sua organização tem feito em ferramentas e processos para gerir grupos. Também pode utilizar a funcionalidade de gestão de grupo seletiva do Azure AD Premium.
* **Alavancagem incorporada em funções no Azure**: Pode utilizar três funções — Proprietário, Colaborador e Leitor, para garantir que os utilizadores e grupos tenham autorização para fazer apenas as tarefas de que necessitam para fazerem o seu trabalho.
* **Acesso granular aos recursos**: Pode atribuir funções a utilizadores e grupos para uma determinada subscrição, grupo de recursos ou um recurso Azure individual, como um website ou base de dados. Desta forma, pode garantir que os utilizadores têm acesso a todos os recursos de que necessitam e sem acesso a recursos que não necessitam de gerir.

## <a name="provisioning-and-other-customization-options"></a>Opções de provisionamento e outras opções de personalização
A sua equipa pode usar planos e requisitos de negócio para decidir quanto personalizar a solução de identidade. Por exemplo, uma grande empresa pode exigir um plano de roll-out faseado para fluxos de trabalho e adaptadores personalizados que se baseia numa linha temporal para fornecer aplicações incrementalmente amplamente utilizadas em geografias. Outro plano de personalização poderá prever que duas ou mais aplicações sejam aprovisionadas em toda uma organização, após testes bem sucedidos. A interação entre o utilizador e a aplicação pode ser personalizada e os procedimentos de fornecimento de recursos podem ser alterados para acomodar o provisionamento automatizado.

Pode desfornecer para remover um serviço ou componente. Por exemplo, a desprovisionamento de uma conta significa que a conta é eliminada de um recurso.

O modelo híbrido de recursos de provisionamento combina abordagens de pedido e de função, ambas apoiadas pela Azure AD. Para um subconjunto de colaboradores ou sistemas geridos, uma empresa pode querer automatizar o acesso com atribuição baseada em papéis. Uma empresa também pode lidar com todos os outros pedidos de acesso ou exceções através de um modelo baseado em pedidos. Algumas empresas podem começar com a atribuição manual, e evoluir para um modelo híbrido, com a intenção de uma implementação totalmente baseada em papéis num futuro.

Outras empresas poderão achar impraticável por razões comerciais a obtenção de um provisionamento completo baseado em papéis e a visar uma abordagem híbrida como um objetivo procurado. Ainda assim, outras empresas poderão estar satisfeitas apenas com o fornecimento baseado em pedidos, e não querem investir esforços adicionais para definir e gerir políticas de provisionamento automatizadas baseadas em papéis.

## <a name="license-management"></a>Gestão de licenças
A gestão de licenças baseada em grupo saquea da AD Azure permite que os administradores atribuam os utilizadores a um grupo de segurança e a Azure AD atribui automaticamente licenças a todos os membros do grupo. Se um utilizador for posteriormente adicionado ou removido do grupo, uma licença será automaticamente atribuída ou removida conforme apropriado.

Pode utilizar grupos que sincroniza a partir de ad comercial ou gerir em Azure AD. Emparelhando-o com a Azure AD premium Self-Service Group Management pode facilmente delegar a atribuição de licença aos decisores apropriados. Pode ter a certeza de que problemas como conflitos de licenças e dados de localização em falta são automaticamente resolvidos.

## <a name="self-regulating-user-administration"></a>Autorregulação da administração dos utilizadores
Quando a sua organização começa a fornecer recursos em todas as organizações internas, implementa a capacidade de autorregulação da administração dos utilizadores. Você pode realizar as vantagens e benefícios de fornecer utilizadores através de fronteiras organizacionais. Neste ambiente, uma mudança no estatuto de utilizador reflete-se automaticamente nos direitos de acesso através das fronteiras e geografias da organização. Pode reduzir os custos de provisionamento e agilizar os processos de acesso e aprovação. A implementação realiza todo o potencial de implementação do controlo de acesso baseado em papéis para a gestão de acesso de ponta a ponta na sua organização. Pode reduzir os custos administrativos através de procedimentos automatizados para o fornecimento de utilizadores. Pode melhorar a segurança automatizando a aplicação da política de segurança e racionalizando e centralizando a gestão do ciclo de vida dos utilizadores e o fornecimento de recursos para as grandes populações de utilizadores.

> [!NOTE]
> Para mais informações, consulte a criação de Anúncios Azure para gestão de acesso a aplicações de autosserviço
> 
> 

Os serviços azure ad baseados em licença (baseados em direitos) funcionam ativando uma subscrição no seu diretório/inquilino de serviço Azure AD. Uma vez que a subscrição esteja ativa, as capacidades de serviço podem ser geridas por administradores de diretório/serviço e utilizadas por utilizadores licenciados. 

## <a name="integration-with-other-3rd-party-providers"></a>Integração com outros fornecedores de terceiros

O Azure Ative Directory fornece uma única assinatura e reforçou a segurança de acesso à aplicação a milhares de aplicações SaaS e aplicações web no local. Para mais informações, consulte Integração de [aplicações com Diretório Ativo Azure](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definir gestão de sincronização
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Com esta integração, os utilizadores e organizações podem tirar partido do seguinte:

* As organizações podem fornecer aos utilizadores uma identidade híbrida comum em todos os locais ou serviços baseados na nuvem, alavancando o Diretório Ativo do Servidor do Windows e, em seguida, conectando-se ao Azure Ative Directory.
* Os administradores podem fornecer Acesso Condicional com base no recurso de aplicação, dispositivo e identidade do utilizador, localização da rede e autenticação de vários fatores.
* Os utilizadores podem aproveitar a sua identidade comum através de contas em Azure AD para o Office 365, Intune, SaaS e aplicações de terceiros.
* Os desenvolvedores podem construir aplicações que alavancam o modelo de identidade comum, integrando aplicações em Ative Directory no local ou Azure para aplicações baseadas na nuvem

A figura que se segue tem um exemplo de uma visão de alto nível do processo de sincronização de identidade.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Processo de sincronização de identidade

Reveja o quadro seguinte para comparar as opções de sincronização:

| Opção de Gestão de Sincronização | Vantagens | Desvantagens |
| --- | --- | --- |
| Baseado em sincronização (através do DirSync ou DoADConnect) |Utilizadores e grupos sincronizados a partir de instalações e nuvem <br>  **Controlo de políticas**: As políticas de conta podem ser definidas através do Diretório Ativo, o que dá ao administrador a capacidade de gerir políticas de passwords, workstation, restrições, controlos de bloqueio e muito mais, sem ter de executar tarefas adicionais na nuvem.  <br>  **Controlo de acesso**: Pode restringir o acesso ao serviço de nuvem para que os serviços possam ser acedidos através do ambiente corporativo, através de servidores online, ou ambos. <br>  Chamadas de suporte reduzidas: Se os utilizadores tiverem menos senhas para se lembrarem, são menos propensos a esquecê-las. <br>  Segurança: As identidades e informações dos utilizadores estão protegidas porque todos os servidores e serviços utilizados em um único sinal, são dominados e controlados no local. <br>  Suporte para autenticação forte: Pode utilizar autenticação forte (também chamada autenticação de dois fatores) com o serviço na nuvem. No entanto, se utilizar uma autenticação forte, deve utilizar um único sinal. | |
| Baseado na Federação (através de AD FS) |Ativado pelo Serviço de Token de Segurança (STS). Quando configurar um STS para fornecer um único acesso de entrada com um serviço de nuvem microsoft, estará a criar uma confiança federada entre o seu STS no local e o domínio federado que especificou no seu inquilino Azure AD. <br> Permite que os utilizadores finais utilizem o mesmo conjunto de credenciais para obter acesso a vários recursos <br>os utilizadores finais não têm de manter múltiplos conjuntos de credenciais. No entanto, os utilizadores têm de fornecer as suas credenciais a cada um dos recursos participantes., B2B e B2C. |Requer pessoal especializado para a implantação e manutenção de servidores AD FS nas instalações. Existem restrições ao uso de autenticação forte se pretender utilizar AD FS para o seu STS. Para mais informações, consulte [Configurar Opções Avançadas para AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Para mais informações consulte, [integrando as suas identidades no local com o Diretório Ativo Azure](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Veja também
[Visão geral das considerações de conceção](plan-hybrid-identity-design-considerations-overview.md)

