---
title: Guia de referência de operações de gestão de diretório ativo Azure
description: Este guia de referência de operações descreve os controlos e ações que deve tomar para garantir operações de gestão de identidade e acesso
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 66bce573be5a31641bdff809b8e9a79b617a703a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371006"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Guia de referência de operações de gestão de diretório ativo Azure

Esta secção do guia de referência de [operações Azure AD](active-directory-ops-guide-intro.md) descreve os controlos e ações que deve considerar para garantir e gerir o ciclo de vida das identidades e das suas atribuições.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir os proprietários a tarefas-chave

A Gestão do Diretório Ativo Azure requer a execução contínua de tarefas e processos operacionais fundamentais que podem não fazer parte de um projeto de implantação. Ainda é importante que crie estas tarefas para manter o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Definir o processo como criar subscrições do Azure | Varia por organização |
| Decida quem obtém licenças de Mobilidade Empresarial + Segurança | Equipa de Operações do IAM |
| Decida quem obtém as licenças microsoft 365 | Equipa de Produtividade |
| Decida quem obtém outras licenças, por exemplo, Dynamics, Visual Studio Codespaces | Proprietário de aplicação |
| Atribuir licenças | Equipa de Operações do IAM |
| Resolução de problemas e corrigir erros de atribuição de licenças | Equipa de Operações do IAM |
| Provisão de identidades para aplicações em Azure AD | Equipa de Operações do IAM |

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário para tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="assigning-owners-recommended-reading"></a>Atribuição de proprietários recomenda leitura

- [Atribuir funções de administrador no Azure Active Directory](../roles/permissions-reference.md)
- [Governação no Azure](../../governance/index.yml)

## <a name="on-premises-identity-synchronization"></a>Sincronização de identidade no local

### <a name="identify-and-resolve-synchronization-issues"></a>Identificar e resolver problemas de sincronização

A Microsoft recomenda que tenha uma boa base e compreensão dos problemas no seu ambiente no local que podem resultar em problemas de sincronização na nuvem. Uma vez que ferramentas automatizadas como [idFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) e [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md#why-use-azure-ad-connect-health) podem gerar um grande volume de falsos positivos, recomendamos que identifique erros de sincronização que tenham sido deixados por resolver durante mais de 100 dias, limpando esses objetos por engano. Erros de sincronização não resolvidos a longo prazo podem gerar incidentes de suporte. [Erros de resolução de problemas durante a sincronização](../hybrid/tshoot-connect-sync-errors.md) fornecem uma visão geral de diferentes tipos de erros de sincronização, alguns dos cenários possíveis que causam esses erros e formas potenciais de corrigir os erros.

### <a name="azure-ad-connect-sync-configuration"></a>Configuração Azure AD Connect Sync

Para permitir todas as experiências híbridas, postura de segurança baseada no dispositivo e integração com a Azure AD, é necessário sincronizar as contas de utilizador que os seus colaboradores usam para iniciar sessão nos seus desktops.

Se não sincronizar o login dos utilizadores da floresta, então deve alterar a sincronização para vir da floresta adequada.

#### <a name="synchronization-scope-and-object-filtering"></a>Âmbito de sincronização e filtragem de objetos

A remoção de baldes conhecidos de objetos que não são obrigados a ser sincronizados tem os seguintes benefícios operacionais:

- Menos fontes de erros de sincronização
- Ciclos de sincronização mais rápidos
- Menos "lixo" para levar para a frente a partir do local, por exemplo, a poluição da lista global de endereços para contas de serviço no local que não são relevantes na nuvem

> [!NOTE]
> Se descobrir que está a importar muitos objetos que não estão a ser exportados para a nuvem, deve filtrar por OU ou atributos específicos.

Exemplos de objetos a excluir são:

- Contas de serviço que não são usadas para aplicações na nuvem
- Grupos que não devem ser usados em cenários de nuvem, como os usados para conceder acesso a recursos
- Utilizadores ou contactos que sejam identidades externas que se destinam a ser representados com a Colaboração AD B2B da Azure
- Contas de computador onde os colaboradores não se destinam a aceder a aplicações na nuvem a partir, por exemplo, de servidores

> [!NOTE]
> Se uma única identidade humana tiver várias contas a partir de algo como uma migração, fusão ou aquisição de domínios antigos, só deve sincronizar a conta utilizada pelo utilizador no dia-a-dia, por exemplo, o que eles usam para iniciar sessão no seu computador.

Idealmente, você vai querer alcançar um equilíbrio entre reduzir o número de objetos para sincronizar e a complexidade nas regras. Geralmente, uma combinação entre [a filtragem](../hybrid/how-to-connect-sync-configure-filtering.md) OU/contentor mais um simples mapeamento de atributos ao atributo cloudFiltered é uma combinação eficaz de filtragem.

> [!IMPORTANT]
> Se utilizar a filtragem de grupo na produção, deverá transitar para outra abordagem de filtragem.

#### <a name="sync-failover-or-disaster-recovery"></a>Falha de sincronização ou recuperação de desastres

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o Sync Server ficar offline por qualquer motivo, as alterações nas instalações não podem ser atualizadas na nuvem e podem resultar em problemas de acesso para os utilizadores. Por isso, é importante definir uma estratégia de failover que permita aos administradores retomar rapidamente a sincronização após o servidor de sincronização ficar offline. Tais estratégias podem enquadrar-se nas seguintes categorias:

- **Implementar o Azure AD Connect Server(s) no Modo de Fase** - permite que um administrador "promova" o servidor de preparação para a produção através de um simples interruptor de configuração.
- **Utilizar virtualização** - Se a ligação AD A Azul for implantada numa máquina virtual (VM), os administradores podem aproveitar a sua pilha de virtualização para migrar ou redistribuir rapidamente o VM e, portanto, retomar a sincronização.

Se a sua organização não tiver uma estratégia de recuperação de desastres e de failover para o Sync, não deve hesitar em implementar o Azure AD Connect no Modo de Preparação. Da mesma forma, se houver um desfasamento entre a sua configuração de produção e de paragem, deve re-linha de base O modo de paragem Azure AD Connect para corresponder à configuração de produção, incluindo versões e configurações de software.

![Uma imagem da configuração do modo de paragem Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Manter-se a par

A Microsoft atualiza regularmente o Azure AD Connect. Mantenha-se atual para tirar partido das melhorias de desempenho, correções de bugs e novas capacidades que cada nova versão fornece.

Se a sua versão Azure AD Connect estiver com mais de seis meses de atraso, deverá atualizar para a versão mais recente.

#### <a name="source-anchor"></a>Âncora de origem

A utilização **de ms-DS-consistência como** âncora de [origem](../hybrid/plan-connect-design-concepts.md) permite uma migração mais fácil de objetos através de florestas e domínios, o que é comum na consolidação/limpeza/limpeza do Domínio AD, fusões, aquisições e alienações.

Se estiver atualmente a utilizar o **ObjectGuid** como âncora de origem, recomendamos que mude para a utilização **da Ms-DS-ConsistencyGuid**.

#### <a name="custom-rules"></a>Regras personalizadas

As regras personalizadas Azure AD Connect fornecem a capacidade de controlar o fluxo de atributos entre objetos no local e objetos de nuvem. No entanto, o excesso de usususou ou maluque as regras aduaneiras pode introduzir os seguintes riscos:

- Complexidade de resolução de problemas
- Degradação do desempenho ao realizar operações complexas através de objetos
- Maior probabilidade de divergência de configuração entre o servidor de produção e o servidor de paragem
- Sobrecarga adicional ao atualizar o Azure AD Connect se as regras personalizadas forem criadas dentro da precedência superior a 100 (utilizadas pelas regras incorporadas)

Se está a utilizar regras excessivamente complexas, deve investigar as razões da complexidade e encontrar oportunidades de simplificação. Da mesma forma, se criou regras personalizadas com valor de precedência superior a 100, deve corrigir as regras para que não estejam em risco ou entrem em conflito com o conjunto padrão.

Exemplos de má interpretação de regras personalizadas incluem:

- **Compensar os dados sujos no diretório** - Neste caso, recomenda-se trabalhar com os proprietários da equipa de AD e limpar os dados no diretório como tarefa de remediação, e ajustar processos para evitar a reintrodução de dados ruins.
- **Remediação pontual de utilizadores individuais** - É comum encontrar regras que casos especiais outliers, geralmente devido a um problema com um utilizador específico.
- **"CloudFiltering" demasiado complicado** - Embora a redução do número de objetos seja uma boa prática, existe o risco de criar e complicar demasiado o âmbito de sincronização utilizando muitas regras de sincronização. Se houver uma lógica complexa para incluir/excluir objetos para além da filtragem da U, recomenda-se lidar com esta lógica fora da sincronização e rotular os objetos com um simples atributo "cloudFiltered" que pode fluir com uma simples Regra de Sincronização.

#### <a name="azure-ad-connect-configuration-documenter"></a>Documento de configuração AZure AD Connect

O [Azure AD Connect Configuration Documenter](https://github.com/Microsoft/AADConnectConfigDocumenter) é uma ferramenta que pode usar para gerar documentação de uma instalação Azure AD Connect para permitir uma melhor compreensão da configuração da sincronização, criar confiança para acertar as coisas e saber o que foi alterado quando aplicou uma nova construção ou configuração do Azure AD Connect ou de regras de sincronização personalizadas adicionadas ou atualizadas. As capacidades atuais da ferramenta incluem:

- Documentação da configuração completa da sincronização Azure AD Connect.
- Documentação de quaisquer alterações na configuração de dois servidores de sincronização Azure AD Connect ou alterações a partir de uma determinada linha de base de configuração.
- Geração de um script de implementação PowerShell para migrar as diferenças de regras de sincronização ou personalizações de um servidor para outro.

## <a name="assignment-to-apps-and-resources"></a>Atribuição a apps e recursos

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenciamento baseado em grupo para serviços na nuvem da Microsoft

O Azure Ative Directory dinamiza a gestão de licenças através [de licenças baseadas em grupo](./active-directory-licensing-whatis-azure-portal.md) para serviços na nuvem da Microsoft. Desta forma, o IAM fornece a infraestrutura de grupo e a gestão delegada desses grupos às equipas adequadas nas organizações. Existem várias formas de criar a adesão de grupos em Azure AD, incluindo:

- **Sincronizados a partir de instalações** - Os grupos podem vir de diretórios no local, o que pode ser um bom ajuste para organizações que estabeleceram processos de gestão de grupos que podem ser estendidos para atribuir licenças na Microsoft 365.

- **Atributo/dinâmico** - Os grupos podem ser criados na nuvem com base numa expressão baseada em atributos do utilizador, por exemplo, o Departamento é igual a "vendas". A Azure AD mantém os membros do grupo, mantendo-o consistente com a expressão definida. A utilização deste tipo de grupo para atribuição de licenças permite uma atribuição de licença baseada em atributos, o que é um bom ajuste para organizações que têm alta qualidade de dados no seu diretório.

- **Propriedade delegada** - Os grupos podem ser criados na nuvem e podem ser designados proprietários. Desta forma, pode capacitar os empresários, por exemplo, a equipa de colaboração ou a equipa de BI, para definir quem deve ter acesso.

Se estiver a utilizar um processo manual para atribuir licenças e componentes aos utilizadores, recomendamos que implemente o licenciamento baseado em grupo. Se o seu processo atual não monitorizar erros de licenciamento ou o que é atribuído versus Disponível, deverá definir melhorias no processo para resolver erros de licenciamento e monitorizar a atribuição de licenças.

Outro aspeto da gestão da licença é a definição de planos de serviço (componentes da licença) que devem ser habilitados com base em funções de trabalho na organização. A concessão de acesso a planos de serviço que não são necessários, pode levar os utilizadores a ver ferramentas no portal do Office para as quais não foram treinados ou não devem ser utilizados. Pode impulsionar o volume adicional do balcão de ajuda, o provisionamento desnecessário, e colocar a sua conformidade e governação em risco, por exemplo, ao providenciar o OneDrive para negócios a indivíduos que podem não ser autorizados a partilhar conteúdo.

Utilize as seguintes orientações para definir planos de serviço aos utilizadores:

- Os administradores devem definir "pacotes" de planos de serviço a serem oferecidos aos utilizadores com base no seu papel, por exemplo, trabalhador de colarinho branco contra trabalhador de piso.
- Criar grupos por cluster e atribuir a licença com plano de serviço.
- Opcionalmente, um atributo pode ser definido para manter as embalagens para os utilizadores.

> [!IMPORTANT]
> O licenciamento baseado em grupo em Azure AD introduz o conceito de utilizadores em um estado de erro de licenciamento. Se notar algum erro de licenciamento, deve [identificar e resolver](../enterprise-users/licensing-groups-resolve-problems.md) imediatamente quaisquer problemas de atribuição de licenças.

![Uma imagem de um ecrã de computador Descrição gerada automaticamente](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Gestão do ciclo de vida

Se estiver a utilizar uma ferramenta, como [o Microsoft Identity Manager](/microsoft-identity-manager/) ou um sistema de terceiros, que se baseia numa infraestrutura no local, recomendamos que descarrege a atribuição da ferramenta existente, implemente o licenciamento baseado em grupo e defina uma gestão do ciclo de vida em grupo com base em [grupos.](../enterprise-users/licensing-group-advanced.md#use-group-based-licensing-with-dynamic-groups) Da mesma forma, se o seu processo existente não responder por novos colaboradores ou funcionários que abandonem a organização, deve implementar licenças baseadas em grupos baseados em grupos dinâmicos e definir um ciclo de vida de membros do grupo. Finalmente, se o licenciamento baseado em grupo é implementado contra grupos no local que não têm gestão do ciclo de vida, considere usar grupos de nuvem para permitir capacidades como a propriedade delegada ou a adesão dinâmica baseada em atributos.

### <a name="assignment-of-apps-with-all-users-group"></a>Atribuição de aplicativos com o grupo "Todos os utilizadores"

Os proprietários de recursos podem acreditar que o grupo **de todos os utilizadores** contém **apenas empregados da Empresa** quando podem realmente conter tanto os **empregados da Empresa** como os **Hóspedes.** Como resultado, deve ter especial cuidado ao utilizar o grupo **de todos os utilizadores** para a atribuição de aplicações e conceder acesso a recursos como conteúdo ou aplicações sharePoint.

> [!IMPORTANT]
> Se o grupo **de todos os utilizadores** estiver ativado e utilizado para políticas de acesso condicional, aplicações ou atribuição de recursos, certifique-se de [que o grupo](../external-identities/use-dynamic-groups.md) se não quiser que inclua utilizadores convidados. Além disso, deverá corrigir as suas atribuições de licenciamento criando e atribuindo a grupos que contenham apenas **colaboradores da Enterprise.** Por outro lado, se descobrir que o grupo **de todos os utilizadores** está ativado mas não está a ser utilizado para conceder acesso aos recursos, certifique-se de que a orientação operacional da sua organização é utilizar intencionalmente esse grupo (que inclui tanto **os Colaboradores da Empresa** como os **Hóspedes).**

### <a name="automated-user-provisioning-to-apps"></a>Fornecimento automatizado de utilizadores a apps

[O fornecimento automatizado de utilizadores](../app-provisioning/user-provisioning.md) às aplicações é a melhor forma de criar um fornecimento consistente, desprovisionamento e ciclo de vida de identidades em vários sistemas.

Se está atualmente a alojar aplicações de forma ad-hoc ou a usar coisas como ficheiros CSV, JIT ou uma solução no local que não aborde a gestão do ciclo de vida, recomendamos que [implemente o provisionamento de aplicações](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) com Azure AD para aplicações apoiadas e defina um padrão consistente para aplicações que ainda não são suportadas pela Azure AD.

![Serviço de prestação de Ad Azure](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Azure AD Connect delta sync cycle base

É importante compreender o volume de mudanças na sua organização e certificar-se de que não está a demorar muito tempo a ter um tempo de sincronização previsível.

A frequência [de sincronização delta padrão](../hybrid/how-to-connect-sync-feature-scheduler.md) é de 30 minutos. Se a sincronização delta estiver a demorar mais de 30 minutos de forma consistente, ou se houver discrepâncias significativas entre o desempenho da sincronização delta da encenação e da produção, deve investigar e rever os [fatores que influenciam o desempenho do Azure AD Connect](../hybrid/plan-connect-performance-factors.md).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Azure AD Connect leitura recomendada para resolução de problemas

- [Prepare atributos de diretório para sincronização com a Microsoft 365 utilizando a ferramenta IdFix](/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Erros de resolução de problemas durante a sincronização](../hybrid/tshoot-connect-sync-errors.md)

## <a name="summary"></a>Resumo

Há cinco aspetos para uma infraestrutura de identidade segura. Esta lista irá ajudá-lo a encontrar e a tomar rapidamente as ações necessárias para garantir e gerir o ciclo de vida das identidades e seus direitos na sua organização.

- Atribua os proprietários a tarefas-chave.
- Encontrar e resolver problemas de sincronização.
- Defina uma estratégia de failover para a recuperação de desastres.
- Dinamizar a gestão de licenças e atribuição de apps.
- Automatizar o fornecimento de utilizadores para apps.

## <a name="next-steps"></a>Passos seguintes

Começar com as [verificações e ações de gestão de autenticação.](active-directory-ops-guide-auth.md)