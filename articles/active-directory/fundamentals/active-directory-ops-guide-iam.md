---
title: Guia de referência de identidade de diretório ativo azure e operações de gestão de acesso
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
ms.openlocfilehash: 5653fa7c67d36dbf2ee71f51f182168bccb69105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298619"
---
# <a name="azure-active-directory-identity-and-access-management-operations-reference-guide"></a>Guia de referência de identidade de diretório ativo azure e operações de gestão de acesso

Esta secção do guia de referência de operações da [AD Azure](active-directory-ops-guide-intro.md) descreve os controlos e ações que deve considerar para garantir e gerir o ciclo de vida das identidades e suas atribuições.

> [!NOTE]
> Estas recomendações estão em vigor a partir da data da publicação, mas podem mudar ao longo do tempo. As organizações devem avaliar continuamente as suas práticas de identidade à medida que os produtos e serviços da Microsoft evoluem ao longo do tempo.

## <a name="key-operational-processes"></a>Principais processos operacionais

### <a name="assign-owners-to-key-tasks"></a>Atribuir proprietários a tarefas-chave

A Gestão do Diretório Ativo azure requer a execução contínua de tarefas e processos operacionais fundamentais que podem não fazer parte de um projeto de lançamento. Ainda é importante que tenha configurado estas tarefas para manter o seu ambiente. As tarefas-chave e os seus proprietários recomendados incluem:

| Tarefa | Proprietário |
| :- | :- |
| Defina o processo de como criar subscrições do Azure | Varia por organização |
| Decida quem obtém licenças de Mobilidade Empresarial + Segurança | Equipa de Operações do IAM |
| Decida quem obtém o Office 365 licenças | Equipa de Produtividade |
| Decida quem obtém outras licenças, por exemplo, Dynamics, VSO | Proprietário de candidatura |
| Atribuir licenças | Equipa de Operações do IAM |
| Resolução de problemas e remediar erros de atribuição de licenças | Equipa de Operações do IAM |
| Identidades de provisão para aplicações em Azure AD | Equipa de Operações do IAM |

Ao rever a sua lista, poderá descobrir que precisa de atribuir um proprietário a tarefas que faltam a um proprietário ou ajustar a propriedade para tarefas com proprietários que não estejam alinhados com as recomendações acima.

#### <a name="assigning-owners-recommended-reading"></a>Atribuindo proprietários recomendado leitura

- [Atribuir funções de administrador no Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Governação no Azure](https://docs.microsoft.com/azure/security/governance-in-azure)

## <a name="on-premises-identity-synchronization"></a>Sincronização de identidade no local

### <a name="identify-and-resolve-synchronization-issues"></a>Identificar e resolver problemas de sincronização

A Microsoft recomenda que tenha uma boa base de base e compreensão dos problemas no seu ambiente no local que podem resultar em problemas de sincronização para a nuvem. Uma vez que ferramentas automatizadas como [o IdFix](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix) e [o Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-azure-ad-connect#why-use-azure-ad-connect-health) podem gerar um elevado volume de falsos positivos, recomendamos que identifique erros de sincronização que tenham sido deixados por resolver durante mais de 100 dias, limpando esses objetos por engano. Erros de sincronização não resolvidos a longo prazo podem gerar incidentes de apoio. Erros de resolução de problemas durante a sincronização fornecem uma visão geral de diferentes tipos de erros de [sincronização,](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors) alguns dos cenários possíveis que causam esses erros e formas potenciais de corrigir os erros.

### <a name="azure-ad-connect-sync-configuration"></a>Configuração de sincronização de ligação aD Azure

Para permitir todas as experiências híbridas, postura de segurança baseada no dispositivo e integração com a Azure AD, é necessário que sincronize as contas de utilizador que os seus colaboradores usam para iniciar sessão nos seus desktops.

Se não sincronizar os utilizadores da floresta, então deve alterar a sincronização para vir da floresta adequada.

#### <a name="synchronization-scope-and-object-filtering"></a>Scope de sincronização e filtragem de objetos

A remoção de baldes conhecidos de objetos que não são necessários para serem sincronizados tem os seguintes benefícios operacionais:

- Menos fontes de erros de sincronização
- Ciclos de sincronização mais rápidos
- Menos "lixo" para levar para a frente a partir do local, por exemplo, a poluição da lista global de endereços para contas de serviço no local que não são relevantes na nuvem

> [!NOTE]
> Se descobrir que está a importar muitos objetos que não estão a ser exportados para a nuvem, deve filtrar por OU ou atributos específicos.

Exemplos de objetos a excluir são:

- Contas de Serviço que não são usadas para aplicações na nuvem
- Grupos que não devem ser usados em cenários de nuvem, como os usados para dar acesso a recursos
- Utilizadores ou contactos que sejam identidades externas que se destinam a ser representados com a Colaboração Azure AD B2B
- Contas de computador onde os funcionários não são destinados a aceder a aplicações em nuvem a partir, por exemplo, servidores

> [!NOTE]
> Se uma única identidade humana tiver múltiplas contas aprovisionadas a partir de algo como migração de domínio sinuoso, fusão ou aquisição, você só deve sincronizar a conta usada pelo utilizador no dia-a-dia, por exemplo, o que eles usam para iniciar sessão no seu computador .

Idealmente, você vai querer alcançar um equilíbrio entre reduzir o número de objetos para sincronizar e a complexidade nas regras. Geralmente, uma combinação entre [filtragem](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering) de OU/recipiente mais um simples mapeamento de atributo saqueado ao atributo com cloudFiltered é uma combinação eficaz de filtragem.

> [!IMPORTANT]
> Se utilizar a filtragem em grupo na produção, deverá transitar para outra abordagem de filtragem.

#### <a name="sync-failover-or-disaster-recovery"></a>Falha de sincronização ou recuperação de desastres

O Azure AD Connect desempenha um papel fundamental no processo de provisionamento. Se o Sync Server ficar offline por qualquer motivo, as alterações às instalações não podem ser atualizadas na nuvem e podem resultar em problemas de acesso para os utilizadores. Por isso, é importante definir uma estratégia de failover que permite aos administradores retomar rapidamente a sincronização após o servidor de sincronização ficar offline. Tais estratégias podem enquadrar-se nas seguintes categorias:

- Implementar o **Azure AD Connect Server(s) no Modo de Preparação** - permite que um administrador "promova" o servidor de encenação para a produção através de um simples interruptor de configuração.
- **Utilizar a Virtualização** - Se a ligação Azure AD for implantada numa máquina virtual (VM), os administradores podem alavancar a sua pilha de virtualização para migrar ou reimplantar rapidamente o VM e, portanto, retomar a sincronização.

Se a sua organização não tem uma estratégia de recuperação de desastres e falha para o Sync, não deve hesitar em implementar o Azure AD Connect no Modo de Encenação. Da mesma forma, se houver um desfasamento entre a sua configuração de produção e encenação, deve re-baseline modo de preparação Azure AD Connect para corresponder à configuração de produção, incluindo versões de software e configurações.

![Uma imagem da configuração do modo de preparação do Azure AD Connect](./media/active-directory-ops-guide/active-directory-ops-img1.png)

#### <a name="stay-current"></a>Mantenha-se atualizado

A Microsoft atualiza regularmente o Azure AD Connect. Mantenha-se atual para aproveitar as melhorias de desempenho, correções de bugs e novas capacidades que cada nova versão proporciona.

Se a sua versão Azure AD Connect estiver com mais de seis meses de atraso, deverá fazer o upgrade para a versão mais recente.

#### <a name="source-anchor"></a>Âncora de origem

A utilização **de ms-DS-consistência como** [âncora de origem](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-design-concepts) permite uma migração mais fácil de objetos através de florestas e domínios, o que é comum na consolidação/limpeza do domínio ad, fusões, aquisições e alienações.

Se está atualmente a usar o **ObjectGuid** como âncora de origem, recomendamos que mude para utilizar **ms-DS-Consistência .**

#### <a name="custom-rules"></a>Regras personalizadas

As regras personalizadas do Azure AD Connect fornecem a capacidade de controlar o fluxo de atributos entre objetos no local e objetos de nuvem. No entanto, a utilização excessiva ou utilização indevida de regras personalizadas pode introduzir os seguintes riscos:

- Complexidade de resolução de problemas
- Degradação do desempenho ao realizar operações complexas através de objetos
- Maior probabilidade de divergência de configuração entre o servidor de produção e o servidor de encenação
- Despesas gerais adicionais ao atualizar o Azure AD Connect se forem criadas regras personalizadas dentro da precedência superior a 100 (utilizadas por regras incorporadas)

Se está a usar regras excessivamente complexas, deve investigar as razões da complexidade e encontrar oportunidades de simplificação. Da mesma forma, se criou regras personalizadas com valor de precedência superior a 100, deve corrigir as regras para que não estejam em risco ou estejam em conflito com o conjunto padrão.

Exemplos de utilização indevida de regras personalizadas incluem:

- **Compensar os dados sujos no diretório** - Neste caso, recomenda-se trabalhar com os proprietários da equipa ad e limpar os dados do diretório como tarefa de reparação e ajustar os processos para evitar a reintrodução de dados maus.
- **Remediação pontual de utilizadores individuais** - É comum encontrar regras que casos especiais são mais distantes, geralmente devido a um problema com um utilizador específico.
- **"CloudFiltering" sobrecomplicada** - Ao mesmo tempo que a redução do número de objetos é uma boa prática, existe o risco de criar e complicar demasiado o âmbito de sincronização usando muitas regras de sincronização. Se houver uma lógica complexa para incluir/excluir objetos para além da filtragem da U, é aconselhável lidar com esta lógica fora da sincronização e rotular os objetos com um simples atributo "cloudFiltered" que pode fluir com uma regra sincronizada simples.

#### <a name="azure-ad-connect-configuration-documenter"></a>Documentarista de configuração Azure AD Connect

O Documento de Configuração de [Ligação AD Azure](https://github.com/Microsoft/AADConnectConfigDocumenter) é uma ferramenta que pode utilizar para gerar documentação de uma instalação Azure AD Connect para permitir uma melhor compreensão da configuração de sincronização, criar confiança em acertar as coisas e saber o que foi alterado quando aplicou uma nova construção ou configuração de Azure AD Connect ou regras de sincronização personalizadaadicionadas ou atualizadas. As capacidades atuais da ferramenta incluem:

- Documentação da configuração completa do sincronizado Azure AD Connect.
- Documentação de quaisquer alterações na configuração de dois servidores de sincronização Azure AD Connect ou alterações a partir de uma determinada linha de base de configuração.
- Geração de um script de implementação PowerShell para migrar as diferenças de regra de sincronização ou personalizações de um servidor para outro.

## <a name="assignment-to-apps-and-resources"></a>Atribuição de apps e recursos

### <a name="group-based-licensing-for-microsoft-cloud-services"></a>Licenciamento baseado em grupo para serviços de nuvem microsoft

O Azure Ative Directory dinamiza a gestão das licenças através de [licenciamento baseado em grupo](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal) para serviços na nuvem da Microsoft. Desta forma, o IAM fornece as infraestruturas de grupo e a gestão delegada desses grupos às equipas adequadas das organizações. Existem várias formas de criar a adesão de grupos em Azure AD, incluindo:

- **Sincronizados** a partir de instalações - Os grupos podem vir de diretórios no local, o que pode ser um bom ajuste para organizações que estabeleceram processos de gestão de grupos que podem ser alargados para atribuir licenças no escritório 365.

- **Baseado em atributos/dinâmicos** - Os grupos podem ser criados na nuvem com base numa expressão baseada em atributos do utilizador, por exemplo, o Departamento é igual a "vendas". A Azure AD mantém os membros do grupo, mantendo-o consistente com a expressão definida. A utilização deste tipo de grupo para atribuição de licenças permite uma atribuição de licença baseada em atributos, o que é um bom ajuste para organizações que têm alta qualidade de dados no seu diretório.

- **Propriedade delegada** - Grupos podem ser criados na nuvem e podem ser designados proprietários. Desta forma, pode capacitar os empresários, por exemplo, a equipa de colaboração ou a equipa bi, para definir quem deve ter acesso.

Se está a utilizar atualmente um processo manual para atribuir licenças e componentes aos utilizadores, recomendamos que implemente o licenciamento baseado em grupo. Se o seu processo atual não monitorizar os erros de licenciamento ou o que é atribuído versus Disponível, deverá definir melhorias no processo para resolver erros de licenciamento e monitorizar a atribuição de licenças.

Outro aspeto da gestão da licença é a definição de planos de serviço (componentes da licença) que devem ser ativados com base em funções de trabalho na organização. A concessão de acesso a planos de serviço que não sejam necessários, pode resultar na aplicação de ferramentas no portal do Office para as quais não foram treinados ou que não deveriam estar a utilizar. Pode impulsionar volume adicional de mesa de ajuda, fornecimento desnecessário, e colocar a sua conformidade e governação em risco, por exemplo, ao fornecer o OneDrive para negócios a indivíduos que possam não ser autorizados a partilhar conteúdo.

Utilize as seguintes orientações para definir planos de serviço aos utilizadores:

- Os administradores devem definir "pacotes" de planos de serviço a oferecer aos utilizadores com base no seu papel, por exemplo, trabalhador de colarinho branco contra trabalhador de piso.
- Crie grupos por cluster e atribua a licença com plano de serviço.
- Opcionalmente, um atributo pode ser definido para manter os pacotes para os utilizadores.

> [!IMPORTANT]
> O licenciamento baseado em grupo em Azure AD introduz o conceito de utilizadores num estado de erro de licenciamento. Se notar erros de licenciamento, então deve [identificar imediatamente e resolver](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-resolve-problems) quaisquer problemas de atribuição de licenças.

![Uma imagem de um ecrã de computador Descrição gerada automaticamente](./media/active-directory-ops-guide/active-directory-ops-img2.png)

#### <a name="lifecycle-management"></a>Gestão do ciclo de vida

Se está a utilizar atualmente uma ferramenta, como o [Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/) ou o sistema de terceiros, que se baseia numa infraestrutura no local, recomendamos que descarregue a atribuição da ferramenta existente, implemente o licenciamento baseado em grupo e defina uma gestão de ciclo de vida em grupo baseada em [grupos.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-group-advanced#use-group-based-licensing-with-dynamic-groups) Da mesma forma, se o seu processo existente não contabilizar novos colaboradores ou funcionários que abandonem a organização, deverá implementar licenças baseadas em grupo com base em grupos dinâmicos e definir um ciclo de vida de membros do grupo. Finalmente, se o licenciamento baseado em grupo for implementado contra grupos no local que não têm gestão de ciclo de vida, considere usar grupos de nuvem para permitir capacidades como a propriedade delegada ou a adesão dinâmica baseada em atributos.

### <a name="assignment-of-apps-with-all-users-group"></a>Atribuição de apps com grupo "Todos os utilizadores"

Os proprietários de recursos podem acreditar que o grupo **All users** contém apenas **empregados da empresa** quando podem realmente conter tanto os **Empregados da Empresa** como os **Hóspedes.** Como resultado, deve ter especial cuidado ao utilizar o grupo **Todos os utilizadores** para atribuição de aplicações e conceder acesso a recursos como conteúdo do SharePoint ou aplicações.

> [!IMPORTANT]
> Se o grupo **De todos os utilizadores** estiver ativado e utilizado para políticas de acesso condicional, app ou atribuição de recursos, certifique-se de proteger o [grupo](https://docs.microsoft.com/azure/active-directory/b2b/use-dynamic-groups) se não quiser que inclua utilizadores convidados. Além disso, deve corrigir as suas atribuições de licenciamento criando e atribuindo a grupos que contêm apenas **colaboradores da Empresa.** Por outro lado, se descobrir que o grupo **All users** está habilitado, mas não está a ser utilizado para conceder acesso a recursos, certifique-se de que a orientação operacional da sua organização é utilizar intencionalmente esse grupo (que inclui tanto **os Colaboradores da Empresa** como os **Hóspedes).**

### <a name="automated-user-provisioning-to-apps"></a>Fornecimento automatizado de utilizadores a apps

[O fornecimento automatizado](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) de utilizadores às aplicações é a melhor forma de criar um fornecimento, desprovisionamento e ciclo de vida consistentes de identidades em vários sistemas.

Se atualmente está a fornecer aplicações de forma ad-hoc ou a usar coisas como ficheiros CSV, JIT ou uma solução no local que não aborda a gestão do ciclo de vida, recomendamos que implemente o fornecimento de [aplicações](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#how-do-i-set-up-automatic-provisioning-to-an-application) com a Azure AD para aplicações apoiadas e defina um padrão consistente para aplicações que ainda não são suportadas pela Azure AD.

![Serviço de provisionamento da Azure AD](./media/active-directory-ops-guide/active-directory-ops-img3.png)

### <a name="azure-ad-connect-delta-sync-cycle-baseline"></a>Linha de base do ciclo de sincronização delta Azure AD Connect

É importante compreender o volume de mudanças na sua organização e certificar-se de que não está a demorar muito tempo a ter um tempo de sincronização previsível.

A frequência [de sincronização delta padrão](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-scheduler) é de 30 minutos. Se o delta sync estiver a demorar mais de 30 minutos de forma consistente, ou houver discrepâncias significativas entre o desempenho da sincronização delta da encenação e produção, deve investigar e rever os [fatores que influenciam o desempenho do Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/plan-connect-performance-factors).

#### <a name="azure-ad-connect-troubleshooting-recommended-reading"></a>Leitura recomendada para resolução de problemas da Azure AD Connect

- [Prepare atributos de diretório para sincronização com o Office 365 utilizando a ferramenta IdFix - Office 365](https://docs.microsoft.com/office365/enterprise/prepare-directory-attributes-for-synch-with-idfix)
- [Azure AD Connect: Erros de resolução de problemas durante a sincronização](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-troubleshoot-sync-errors)

## <a name="summary"></a>Resumo

Há cinco aspetos para uma infraestrutura de identidade segura. Esta lista irá ajudá-lo a encontrar e tomar rapidamente as medidas necessárias para garantir e gerir o ciclo de vida das identidades e seus direitos na sua organização.

- Atribuir os proprietários a tarefas-chave.
- Encontrar e resolver problemas de sincronização.
- Defina uma estratégia de failover para a recuperação de desastres.
- Agilize a gestão de licenças e atribuição de apps.
- Automatizar o fornecimento de utilizadores a apps.

## <a name="next-steps"></a>Passos seguintes

Inicie-se com os controlos e ações de [gestão da autenticação.](active-directory-ops-guide-auth.md)
