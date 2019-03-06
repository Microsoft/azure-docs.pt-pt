---
title: Design de identidade híbrida - estratégia de adoção de ciclo de vida do Azure | Documentos da Microsoft
description: Ajuda a definir as tarefas de gestão de identidade híbrida, de acordo com as opções disponíveis para cada fase do ciclo de vida.
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
ms.openlocfilehash: 19b312f284d557c2c1344b82b9fcd570d3392077
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433744"
---
# <a name="determine-hybrid-identity-lifecycle-adoption-strategy"></a>Determinar a estratégia de adoção de ciclo de vida de identidade híbrida
Nesta tarefa, vai definir a estratégia de gestão de identidade para a sua solução de identidade híbrida cumprir os requisitos de negócios que definiu nas [determinar as tarefas de gestão de identidade híbrida](plan-hybrid-identity-design-considerations-hybrid-id-management-tasks.md).

Para definir as tarefas de gestão de identidade híbrida, de acordo com o ciclo de vida de identidade de ponto-a-ponto, apresentados anteriormente neste passo, terá de considerar as opções disponíveis para cada fase do ciclo de vida.

## <a name="access-management-and-provisioning"></a>Gestão de acesso e aprovisionamento
Com uma solução de gestão de acesso de conta boa, sua organização pode controlar exatamente quem tem acesso a quais informações em toda a organização.

Controlo de acesso é uma função fundamental de um sistema de provisionamento centralizado e ponto único. Além de proteger informações confidenciais, controlos de acesso expõem as contas existentes que tenham autorizações não aprovadas ou são já não é necessário. Para controlar as contas obsoletas, as informações de conta em conjunto de ligações de sistema aprovisionamento com informações autoritativas sobre os utilizadores que possuem as contas. Informações de identidade do utilizador autoritativa normalmente são mantidas nos bancos de dados e diretórios de recursos humanos.

Contas de empresas de TI sofisticados incluem centenas de parâmetros que definem as autoridades e esses detalhes podem ser controlados pelo seu sistema de provisionamento. Novos utilizadores podem ser identificados com os dados que fornece a partir da origem autoritativa. A capacidade de aprovação do pedido de acesso inicia os processos que aprovem (ou rejeitarem) aprovisionamento para os mesmos de recursos.

| Fase de gestão do ciclo de vida | No local | Nuvem | Híbrido |
| --- | --- | --- | --- |
| Aprovisionamento e gestão de conta |Ao utilizar a função de servidor Serviços de domínio do Active Directory® (AD DS), pode criar uma infraestrutura escalável, segura e gerível para a gestão de recursos e de utilizador e fornecer suporte para aplicações com diretório ativado como o Microsoft® Exchange Server. <br><br> [Pode provisionar grupos no AD DS através de um Gestor de identidade](https://technet.microsoft.com/library/ff686261.aspx) <br>[Pode aprovisionar utilizadores no AD DS](https://technet.microsoft.com/library/ff686263.aspx) <br><br> Os administradores podem utilizar o controlo de acesso para gerir o acesso de utilizador para recursos partilhados por motivos de segurança. No Active Directory, o controlo de acesso é administrado ao nível do objeto por definir diferentes níveis de acesso ou permissões, para objetos, como o controlo total, escrita, acesso de leitura ou não. Controlo de acesso no Active Directory define como diferentes usuários pode usar objetos do Active Directory. Por predefinição, as permissões em objetos no Active Directory estão definidas para a definição mais segura. |Tem de criar uma conta para cada utilizador que irá aceder a um serviço cloud da Microsoft. Também pode alterar contas de utilizador ou eliminá-los quando já não forem necessários. Por predefinição, os utilizadores não têm permissões de administrador, mas, opcionalmente, pode atribuí-las. <br><br> No Azure Active Directory, um dos principais recursos é a capacidade para gerir o acesso aos recursos. Estes recursos podem fazer parte do diretório, como no caso de permissões para gerir objetos através de funções no diretório, ou dos recursos externos ao diretório, como aplicações SaaS, serviços do Azure e sites SharePoint ou recursos no local. <br><br> Em acesso de center do Azure Active Directory da solução de gestão é o grupo de segurança. O proprietário do recurso (ou o administrador do diretório), pode atribuir um grupo para fornecer um determinado direito de acesso aos recursos que possui. Os membros do grupo serão fornecidos o acesso e o proprietário do recurso pode delegar o direito de gerir a lista de membros de um grupo para alguém – como um Gestor de departamento ou um administrador de suporte técnico<br> <br> Os grupos de gerenciamento na secção do Azure AD, fornece mais informações sobre como gerir o acesso por meio dos grupos. |Amplie as identidades do Active Directory para a cloud através de sincronização e a Federação |

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções
Acesso baseado em funções (RBAC) utiliza funções de controle e aprovisionamento políticas para avaliar, testar e impor seus processos de negócios e regras para conceder acesso a utilizadores. Os administradores de chave a criar políticas de aprovisionamento e atribuir utilizadores a funções e que definir conjuntos de elegibilidade para os recursos para estas funções. RBAC estende a solução de gestão de identidade para utilizar processos baseados em software e reduzir a interação manual do utilizador no processo de aprovisionamento.
RBAC do Azure AD permite que a empresa restringir o número de operações que uma pessoa individual que pode fazer uma vez que ele tem acesso ao portal do Azure. Ao utilizar o RBAC para controlar o acesso ao portal, os administradores de TI AC delegar o acesso ao utilizar as seguintes abordagens de gerenciamento de acesso:

* **Atribuição de função com base em grupo**: Pode atribuir acesso a grupos do Azure AD que podem ser sincronizados do Active Directory local. Isto permite-lhe aproveitar os investimentos existentes que sua organização tornou-se em ferramentas e os processos de gestão de grupos. Também pode utilizar a funcionalidade de gestão de grupo delegada do Azure AD Premium.
* **Tire partido de funções no Azure incorporado**: Pode utilizar três funções — proprietário, Contribuidor e leitor, para garantir que os utilizadores e grupos têm permissão para realizar apenas as tarefas que precisam para realizar seus trabalhos.
* **Acesso granular aos recursos**: Pode atribuir funções a utilizadores e grupos para uma subscrição específica, grupo de recursos ou um recurso do Azure individual como um Web site ou a base de dados. Dessa forma, pode certificar-se de que os utilizadores têm acesso a todos os recursos que precisam e nenhum acesso a recursos que eles não precisam de gerir.

## <a name="provisioning-and-other-customization-options"></a>Aprovisionamento e outras opções de personalização
Sua equipe pode utilizar planos de negócios e os requisitos para decidir como muito personalizar a solução de identidade. Por exemplo, uma grande empresa poderá exigir um plano de implementação faseado para fluxos de trabalho e os adaptadores personalizados que se baseia numa linha de tempo para o aprovisionamento de forma incremental os aplicativos que são amplamente utilizados em localizações geográficas. Outro plano de personalização pode fornecer para dois ou mais aplicativos a ser aprovisionado em toda a empresa, após os testes com êxito. Interação de aplicativos para o usuário pode ser personalizada e procedimentos para o aprovisionamento de recursos podem ser alterados para acomodar o aprovisionamento automatizado.

Pode desaprovisionar para remover um componente ou serviço. Por exemplo, uma conta de desaprovisionamento significa que a conta for eliminada a partir de um recurso.

O modelo híbrido de aprovisionamento de recursos combina a pedido e abordagens baseada em funções, que são ambas suportadas pelo Azure AD. Para um subconjunto de funcionários ou sistemas gerenciados, uma empresa pode desejar automatizar o acesso com a atribuição baseada em funções. Uma empresa também pode processar todos os outros pedidos de acesso ou as exceções por meio de um modelo baseado no pedido. Algumas empresas podem começar com a atribuição manual e evoluir no sentido de um modelo híbrido, com uma intenção de uma implantação totalmente baseado em funções no futuro.

Outras empresas poderão pouco prático por razões de negócio alcançar a completa baseada em funções de aprovisionamento e uma abordagem híbrida, como um objetivo desejada de destino. Ainda outras empresas possam ser satisfeito com apenas com base no pedido de aprovisionamento e não quer investir esforço adicional para definir e gerir políticas de aprovisionamento baseado em funções e automatizadas.

## <a name="license-management"></a>Gestão de licenças
Gestão de licença baseada em grupo no Azure AD permite aos administradores atribuir utilizadores a um grupo de segurança e do Azure AD atribui automaticamente licenças para todos os membros do grupo. Se um utilizador, em seguida, é adicionado ou removido do grupo, uma licença será automaticamente atribuída ou removida, conforme adequada.

Pode utilizar grupos de sincronizar a partir do AD no local ou gerir no Azure AD. Associação isso com a gestão de grupos Self-Service do Azure AD premium, pode facilmente delegar a atribuição de licenças para os tomadores de decisões adequadas. Pode ter a garantia de que os problemas, como os conflitos de licença e dados de localização em falta são automaticamente classificados.

## <a name="self-regulating-user-administration"></a>Administração de utilizadores auto-regulador
Quando começa a sua organização aprovisionar os recursos em todas as organizações de internas, implementa a capacidade de administração auto-regulador do utilizador. Pode obter as vantagens e benefícios do provisionamento de usuários entre fronteiras organizacionais. Neste ambiente, uma alteração no estado de um utilizador é refletida automaticamente em direitos de acesso nos limites da organização e as geografias. Pode reduzir os custos de aprovisionamento e simplificar os processos de acesso e aprovação. A implementação apercebe-se a todo o potencial de implementação de controlo de acesso baseado em funções para a gestão de acesso de ponto a ponto na sua organização. Pode reduzir os custos administrativos através de procedimentos automatizados para que rege o aprovisionamento de utilizadores. Pode melhorar a segurança através da automatização de imposição de políticas de segurança e simplificar e centralizar a gestão de ciclo de vida de utilizadores e o aprovisionamento de recursos para o grande número de usuários.

> [!NOTE]
> Para obter mais informações, consulte como configurar o Azure AD para a gestão de acesso de aplicações self-service
> 
> 

Funcionam os serviços com base na licença (baseado em elegibilidade) do Azure AD através da ativação de uma subscrição no seu inquilino/serviço de diretório do Azure AD. Assim que a subscrição está ativa as capacidades de serviço podem ser geridas por administradores/serviço de diretório e utilizadas por utilizadores licenciados. 

## <a name="integration-with-other-3rd-party-providers"></a>Integração com outros fornecedores de terceiros 3

O Azure Active Directory fornece início de sessão único em e a segurança de acesso de aplicação para milhares de aplicações SaaS e aplicações web no local. Para obter mais informações, consulte [integrar aplicações com o Azure Active Directory](../develop/quickstart-v1-integrate-apps-with-azure-ad.md)

## <a name="define-synchronization-management"></a>Definir a gestão de sincronização
A integração dos diretórios no local com o Azure AD torna os utilizadores mais produtivos, ao fornecer-lhes uma identidade comum para acederem a recursos na nuvem e no local. Com esta integração, utilizadores e as organizações podem aproveitar o seguinte:

* As organizações podem fornecer aos utilizadores com uma identidade híbrida comum em serviços baseados na cloud, tirar partido do Windows Server Active Directory e, em seguida, ligar ao Azure Active Directory ou no local.
* Os administradores possam fornecer acesso condicional com base no recurso de aplicação, dispositivos e identidade de utilizador, localização de rede e autenticação multifator.
* Os utilizadores podem tirar partido sua identidade comum através de contas no Azure AD para o Office 365, Intune, aplicações SaaS e aplicações de terceiros.
* Os programadores podem criar aplicações que tiram partido do modelo de identidade comum, integrando aplicativos do Active Directory no local ou do Azure para aplicações baseadas na nuvem

A figura a seguir tem um exemplo de uma visão geral do processo de sincronização de identidade.

![Sync](./media/plan-hybrid-identity-design-considerations/identitysync.png)

Processo de sincronização de identidade

Reveja a tabela seguinte para comparar as opções de sincronização:

| Opção de gestão de sincronização | Vantagens | Desvantagens |
| --- | --- | --- |
| Baseadas na sincronização (através de DirSync ou AADConnect) |Utilizadores e grupos sincronizados a partir de aplicações no local e na cloud <br>  **O controle de diretiva**: Diretivas de conta podem ser definidas por meio do Active Directory, que oferece ao administrador a capacidade de gerir as políticas de palavra-passe, estação de trabalho, restrições, controles de bloqueio de saída e muito mais, sem ter de efetuar tarefas adicionais na cloud.  <br>  **Controlo de acesso**: Pode restringir o acesso ao serviço cloud, para que os serviços podem ser acedidos através do ambiente Corporativo, através de servidores online, ou ambos. <br>  Reduzidos chamadas de suporte: Se os utilizadores tiverem menos palavras-passe para se lembrar, é menos provável que se esqueça-los. <br>  Segurança: Identidades de utilizador e as informações estão protegidas porque todos os servidores e serviços utilizados no início de sessão único, controladas e ser controlado no local. <br>  Suporte para autenticação forte: Pode utilizar a autenticação forte (também chamada de autenticação de dois fatores) com o serviço cloud. No entanto, se usar uma autenticação segura, tem de utilizar o início de sessão único. | |
| Baseada em Federação (através do AD FS) |Ativado por serviço de Token de segurança (STS). Quando configurar um STS para fornecer acesso de início de sessão único com um serviço cloud da Microsoft, que irá criar uma confiança federada entre os STS no local e o domínio federado que especificou no seu inquilino do Azure AD. <br> Permite que os utilizadores finais utilizem o mesmo conjunto de credenciais para obter acesso a vários recursos <br>os utilizadores finais não tem de manter vários conjuntos de credenciais. No entanto, os utilizadores têm de fornecer as credenciais para cada um dos participantes recursos., B2B e B2C cenários suportados. |Requer pessoal especializado selecionado para a implantação e manutenção de dedicada no local, servidores do AD FS. Existem restrições quanto à utilização de autenticação forte se planeia utilizar o AD FS para seu STS. Para obter mais informações, consulte [configurar opções avançadas para o AD FS 2.0](https://go.microsoft.com/fwlink/?linkid=235649). |

> [!NOTE]
> Para obter mais informações, consulte [integrar as identidades no local com o Azure Active Directory](whatis-hybrid-identity.md).
> 
> 

## <a name="see-also"></a>Consultar Também
[Descrição geral das considerações de design](plan-hybrid-identity-design-considerations-overview.md)

