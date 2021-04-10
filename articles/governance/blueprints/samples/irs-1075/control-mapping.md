---
title: Controlos de amostras de amostras de INFRAS 1075
description: Controlo do mapeamento da amostra de planta do IRS 1075. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 08ce69d13f201e6f0bd1d649ebfe3bbf5c38f927
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98045228"
---
# <a name="control-mapping-of-the-irs-1075-blueprint-sample"></a>Mapeamento de controlo da amostra de planta do IRS 1075

O artigo seguinte detalha como o Azure Blueprints IRS 1075 mapeia a amostra de amostra para os controlos do IRS 1075. Para obter mais informações sobre os controlos, consulte [o IRS 1075](https://www.irs.gov/pub/irs-pdf/p1075.pdf).

Os seguintes mapeamentos são para os **controlos irs 1075.** Utilize a navegação no direito de saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa [da Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a **\[ Pré-visualização \] : Auditar controlos IRS 1075 e implementar extensões VM específicas para apoiar os requisitos** de auditoria incorporados na iniciativa política.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/irs-1075/control-mapping.md).

## <a name="9321-ac-2-account-management"></a>9.3.2.1 Gestão de Conta AC-2

Este projeto ajuda-o a rever contas que podem não cumprir os requisitos de gestão de conta da sua organização. Este projeto atribui definições [de Política Azure](../../../policy/overview.md) que auditam contas externas com permissões lidas, escritas e proprietárias numa subscrição e contas prevadadas. Ao rever as contas auditadas por estas políticas, pode tomar as medidas adequadas para garantir que os requisitos de gestão da conta são cumpridos.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="9321-ac-2-7-account-management--role-based-schemes"></a>9.3.2.1 AC-2 (7) Gestão de Contas | Regimes Role-Based

A Azure implementa [o controlo de acesso baseado em funções (Azure RBAC) para ajudá-lo](../../../../role-based-access-control/overview.md) a gerir quem tem acesso a recursos em Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Esta planta também atribui definições [de Política Azure](../../../policy/overview.md) à utilização de auditação da autenticação do Azure Ative Directory para servidores SQL e Tecido de Serviço. A utilização da autenticação do Azure Ative Directory permite uma gestão simplificada da permissão e uma gestão centralizada da identidade dos utilizadores de bases de dados e outros serviços da Microsoft. Além disso, este projeto atribui uma definição de Política Azure para auditar o uso de regras Azure RBAC personalizadas. Compreender onde são implementadas as regras Azure RBAC personalizadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do Azure RBAC são propensas a erros.

- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL
- Auditar o uso das regras personalizadas do RBAC
- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente

## <a name="9321-ac-2-12-account-management--account-monitoring--atypical-usage"></a>9.3.2.1 AC-2 (12) Gestão de Contas | Monitorização de Conta / Utilização Atípica

O acesso de máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. Todos os pedidos de JIT para aceder a máquinas virtuais estão registados no Registo de Atividades que lhe permite monitorizar para uma utilização atípica. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar o acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="9314-ac-4-information-flow-enforcement"></a>9.3.1.4 Execução do fluxo de informação AC-4

A partilha de recursos de origem cruzada (CORS) pode permitir que os recursos dos Serviços de Aplicação sejam solicitados a partir de um domínio externo. A Microsoft recomenda que apenas os domínios necessários interajam com as suas aplicações API, função e web. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) para ajudá-lo a monitorizar as restrições de acesso a recursos CORS no Centro de Segurança Azure. Compreender as implementações do CORS pode ajudá-lo a verificar se os controlos de fluxo de informação são implementados.

- O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web

## <a name="9315-ac-5-separation-of-duties"></a>9.3.1.5 SEPARAÇÃO DE DireitoS

Ter apenas um proprietário de assinatura Azure não permite despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições Azure pode aumentar o potencial de uma violação através de uma conta de proprietário comprometida. Este projeto ajuda-o a manter um número adequado de proprietários de subscrições da Azure, atribuindo definições [da Azure Policy](../../../policy/overview.md) que auditam o número de proprietários para subscrições Azure. Esta planta também atribui definições de Política Azure que o ajudam a controlar a adesão do grupo de administradores em máquinas virtuais Windows. Gerir permissões de proprietário de subscrição e administrador de máquinas virtuais pode ajudá-lo a implementar a separação adequada dos deveres.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Auditoria Windows VMs em que o grupo de administradores contém qualquer um dos membros especificados
- Auditoria VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores contém qualquer um dos membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="9316-ac-6-7-least-privilege--review-of-user-privileges"></a>9.3.1.6 AC-6 (7) Menor privilégio | Revisão dos Privilégios de Utilização

A Azure implementa [o controlo de acesso baseado em funções (Azure RBAC) para ajudá-lo](../../../../role-based-access-control/overview.md) a gerir quem tem acesso a recursos em Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Este projeto atribui definições [de Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão. Rever estes indicadores de conta pode ajudá-lo a garantir que os controlos de privilégios são implementados.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Auditoria Windows VMs em que o grupo de administradores contém qualquer um dos membros especificados
- Auditoria VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores contém qualquer um dos membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="93112-ac-17-1-remote-access--automated-monitoring--control"></a>9.3.1.12 AC-17 (1) Acesso remoto | Monitorização automatizada / Controlo

Esta planta ajuda-o a monitorizar e a controlar o acesso remoto, atribuindo definições [de Política Azure](../../../policy/overview.md) para monitorizar a depuração remota da aplicação Azure App Service. O plano também atribui definições de política que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem senhas. Além disso, a planta atribui uma definição de Política Azure que o ajuda a monitorizar o acesso ilimitado às contas de armazenamento. A monitorização destes indicadores pode ajudá-lo a garantir que os métodos de acesso remoto cumpram a sua política de segurança.

- \[Pré-visualização \] : Audit Linux VMs que permitem ligações remotas a partir de contas sem senhas
- \[Pré-visualização \] : Implementar requisitos para auditar Os VMs do Linux que permitem ligações remotas a partir de contas sem senhas
- Auditoria acesso ilimitado à rede a contas de armazenamento
- Depuragem remota deve ser desligada para app API
- Depuragem remota deve ser desligada para a App de Função
- Depuragem remota deve ser desligada para aplicação web

## <a name="9313-au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>9.3.1.3 AU-3 (2) Conteúdo dos Registos de Auditoria | Gestão Centralizada do Conteúdo Recorde de Auditoria Planeada

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho log Analytics que permite a configuração e gestão centralizadas. Esta planta ajuda-o a garantir que os eventos são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria em VMSS - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows

## <a name="9335-au-5-response-to-audit-processing-failures"></a>9.3.3.5 Resposta a falhas no processamento de auditoria

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que monitorizam as configurações de auditoria e registo de eventos. A monitorização destas configurações pode fornecer um indicador de uma falha do sistema de auditoria ou de uma configuração errada e ajudá-lo a tomar medidas corretivas.

- Definição de diagnóstico de auditoria
- Audit SQL nível de nível de auditoria Definições de auditoria
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL

## <a name="9336-au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>9.3.3.6 AU-6 (4) Análise, Análise e Reporte | Análise e Análise Central

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho do Log Analytics que permite relatórios e análises centralizados. Esta planta ajuda-o a garantir que os eventos são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria em VMSS - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows

## <a name="93311-au-12-audit-generation"></a>9.3.3.11 Geração de Auditoria AU-12

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam as definições de registo nos recursos do Azure.
Estas definições de política auditam e impõem a implantação do agente Log Analytics em máquinas virtuais Azure e configuração de definições de auditoria para outros tipos de recursos Azure. Estas definições de política também auditam a configuração de registos de diagnóstico para fornecer informações sobre operações que são realizadas dentro dos recursos Azure. Além disso, a auditoria e a Segurança Avançada de Dados estão configuradas em servidores SQL.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria em VMSS - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows
- Definição de diagnóstico de auditoria
- Audit SQL nível de nível de auditoria Definições de auditoria
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- Implementar auditorias em servidores SQL
- Implementar definições de diagnóstico para grupos de segurança de rede

## <a name="9357-cm-7-2-least-functionality--prevent-program-execution"></a>9.3.5.7 CM-7 (2) Menor funcionalidade | Impedir a execução do programa

O controlo de aplicações adaptativas no Azure Security Center é uma solução inteligente e automatizada de filtragem de aplicações que pode bloquear ou impedir que softwares específicos sejam funcionando nas suas máquinas virtuais. O controlo da aplicação pode ser executado num modo de execução que proíbe a execução de aplicações não aprovadas. Esta planta atribui uma definição de Política Azure que o ajuda a monitorizar máquinas virtuais onde uma lista permitida por aplicação é recomendada, mas ainda não foi configurada.

- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais

## <a name="9357-cm-7-5-least-functionality--authorized-software--whitelisting"></a>9.3.5.7 CM-7 (5) Menor funcionalidade | Software autorizado / Whitelisting

O controlo de aplicações adaptativas no Azure Security Center é uma solução inteligente e automatizada de filtragem de aplicações que pode bloquear ou impedir que softwares específicos sejam funcionando nas suas máquinas virtuais. O controlo de aplicações ajuda-o a criar listas de candidaturas aprovadas para as suas máquinas virtuais. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista permitida por aplicação é recomendada, mas ainda não foi configurada.

- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais

## <a name="93511-cm-11-user-installed-software"></a>9.3.5.11 CM-11 User-Installed Software

O controlo de aplicações adaptativas no Azure Security Center é uma solução inteligente e automatizada de filtragem de aplicações que pode bloquear ou impedir que softwares específicos sejam funcionando nas suas máquinas virtuais. O controlo de aplicações pode ajudá-lo a impor e monitorizar o cumprimento das políticas de restrição de software. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista permitida por aplicação é recomendada, mas ainda não foi configurada.

- Os controlos de aplicações adaptativos devem ser ativados em máquinas virtuais

## <a name="9366-cp-7-alternate-processing-site"></a>9.3.6.6 Cp-7 Local de Processamento Alternativo

A Azure Site Recovery replica cargas de trabalho em máquinas virtuais de um local primário para um local secundário. Se ocorrer uma paragem no local primário, a carga de trabalho falha sobre a localização secundária. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que audita máquinas virtuais sem a recuperação de desastres configurada. A monitorização deste indicador pode ajudá-lo a garantir que os controlos de contingência necessários estão em vigor.

- Auditar máquinas virtuais sem recuperação de desastres configurada

## <a name="9372-ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>9.3.7.2 IA-2 (1) Identificação e Autenticação (Utilizadores Organizacionais) | Acesso à Rede a Contas Privilegiadas

Este projeto ajuda-o a restringir e a controlar o acesso privilegiado atribuindo definições [da Azure Policy](../../../policy/overview.md) a contas de auditoria com o proprietário e/ou a escrever permissões que não tenham autenticação de vários fatores ativadas. A autenticação multi-factor ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação esteja comprometida. Ao monitorizar contas sem autenticação multi-factor ativada, pode identificar contas que possam ser mais comprometidas.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- O MFA deve ser ativado em contas com permissões de escrita na sua subscrição

## <a name="9372-ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>9.3.7.2 IA-2 (2) Identificação e Autenticação (Utilizadores Organizacionais) | Acesso à Rede a Contas Não Privilegiadas

Este plano ajuda-o a restringir e a controlar o acesso, atribuindo uma definição [de Política Azure](../../../policy/overview.md) a contas de auditoria com permissões de leitura que não têm autenticação multi-factor ativada. A autenticação multi-factor ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação esteja comprometida. Ao monitorizar contas sem autenticação multi-factor ativada, pode identificar contas que possam ser mais comprometidas.

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="9375-ia-5-authenticator-management"></a>9.3.7.5 Gestão de Autenticadores IA-5

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem palavras-passe e/ou têm permissões incorretas definidas no ficheiro passwd. Esta planta também atribui definições de política que auditam a configuração do tipo de encriptação de palavra-passe para máquinas virtuais do Windows. A monitorização destes indicadores ajuda-o a garantir que os autenticadores do sistema cumprem a política de identificação e autenticação da sua organização.

- \[Pré-visualização \] : Audit Linux VMs que não têm as permissões de ficheiros passwd definidas para 0644
- \[Pré-visualização \] : Auditoria Linux VMs que têm contas sem senhas
- \[Pré-visualização \] : Auditar VMs do Windows que não armazenam palavras-passe utilizando encriptação reversível
- \[Pré-visualização \] : Implementar requisitos para auditar Os VMs do Linux que não têm as permissões de ficheiros passwd definidas para 0644
- \[Pré-visualização \] : Implementar requisitos para auditar Os VMs do Linux que têm contas sem senhas
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não armazenam palavras-passe usando encriptação reversível

## <a name="9375-ia-5-1-authenticator-management--password-based-authentication"></a>9.3.7.5 IA-5 (1) Gestão de Autenticadores | Autenticação Password-Based

Este plano ajuda-o a impor senhas fortes atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam máquinas virtuais do Windows que não impõem a força mínima e outros requisitos de senha. A sensibilização das máquinas virtuais em violação da política de força de senha ajuda-o a tomar ações corretivas para garantir que as palavras-passe de todas as contas de utilizadores de máquinas virtuais cumprem a política de senha da sua organização.

- \[Pré-visualização \] : Audit Windows VMs que permitem a reutilização das 24 palavras-passe anteriores
- \[Pré-visualização \] : Audit Windows VMs que não têm uma idade máxima de senha de 70 dias
- \[Pré-visualização \] : Audit Windows VMs que não têm uma idade mínima de senha de 1 dia
- \[Pré-visualização \] : Audit Windows VMs que não têm a definição de complexidade da palavra-passe ativada
- \[Pré-visualização \] : Audit Windows VMs que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização \] : Auditar VMs do Windows que não armazenam palavras-passe utilizando encriptação reversível
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que permitam reutilizar as 24 palavras-passe anteriores
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não tenham uma idade mínima de senha de 1 dia
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não armazenam palavras-passe usando encriptação reversível

## <a name="93143-ra-5-vulnerability-scanning"></a>9.3.14.3 Varredura de vulnerabilidade RA-5

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center.
O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados. Esta planta também atribui definições de política que auditam e aplicam a Segurança Avançada de Dados em servidores SQL. A segurança avançada de dados incluiu a avaliação de vulnerabilidades e as capacidades avançadas de proteção de ameaças para ajudá-lo a compreender as vulnerabilidades nos seus recursos implantados.

- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- As vulnerabilidades na configuração de segurança nas suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="93164-sc-5-denial-of-service-protection"></a>9.3.16.4 SC-5 Negação de Proteção de Serviços

O nível padrão de negação de serviço distribuído (DDoS) da Azure fornece funcionalidades adicionais e capacidades de mitigação sobre o nível básico de serviço. Estas funcionalidades adicionais incluem a integração do Azure Monitor e a capacidade de rever relatórios de mitigação pós-ataque. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que audita se o nível padrão DDoS estiver ativado. Compreender a diferença de capacidade entre os níveis de serviço pode ajudá-lo a selecionar a melhor solução para resolver as proteções de negação de serviço para o seu ambiente Azure.

- A Norma de Proteção DDoS deve ser ativada

## <a name="93165-sc-7-boundary-protection"></a>9.3.16.5 Proteção de Fronteiras SC-7

Esta planta ajuda-o a gerir e a controlar a fronteira do sistema, atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza as recomendações de endurecimento do grupo de segurança da rede no Centro de Segurança Azure. O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do grupo de segurança da rede para reduzir a potencial superfície de ataque. Além disso, este plano também atribui definições de política que monitorizam pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- As regras do Grupo de Segurança da Rede para máquinas virtuais que enfrentam a Internet devem ser endurecidas
- O acesso através da Internet face ao ponto final deve ser restringido
- As regras do NSGs para aplicações web em IaaS devem ser endurecidas
- Auditoria acesso ilimitado à rede a contas de armazenamento

## <a name="93165-sc-7-3-boundary-protection--access-points"></a>9.3.16.5 SC-7 (3) Proteção das fronteiras | Pontos de Acesso

O acesso de máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. O acesso virtual à máquina JIT ajuda-o a limitar o número de ligações externas aos seus recursos em Azure. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar o acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="93165-sc-7-4-boundary-protection--external-telecommunications-services"></a>9.3.16.5 SC-7 (4) Proteção das fronteiras | Serviços externos de Telecomunicações

O acesso de máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. O acesso a máquinas virtuais JIT ajuda-o a gerir exceções à sua política de fluxo de tráfego, facilitando os processos de pedido de acesso e aprovação. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar o acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="96163-sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>9.6.16.3 SC-8 (1) Confidencialidade e Integridade da Transmissão | Proteção Física Criptográfica ou Alternativa

Esta planta ajuda-o a proteger a confidencialidade e a integridade das informações transmitidas, atribuindo definições [da Política Azure](../../../policy/overview.md) que o ajudam a monitorizar o mecanismo criptográfico implementado para protocolos de comunicações. Garantir que as comunicações estão devidamente encriptadas pode ajudá-lo a satisfazer os requisitos da sua organização ou proteger informações de divulgação e modificação não autorizadas.

- A API App só deve estar acessível em HTTPS
- Auditar servidores web do Windows que não estão a utilizar protocolos de comunicação seguros
- Implementar requisitos para auditar servidores web do Windows que não estão a utilizar protocolos de comunicação seguros
- A App de função só deve estar acessível através do HTTPS
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- A transferência segura para contas de armazenamento deve ser ativada
- A Aplicação Web só deve ser acessível em HTTPS

## <a name="93166-sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>9.3.16.6 SC-28 (1) Proteção da Informação em Repouso | Proteção Criptográfica

Esta planta ajuda-o a aplicar a sua política sobre a utilização de controlos criptógrafos para proteger a informação em repouso, atribuindo definições [de Política Azure](../../../policy/overview.md) que impõem controlos criptógrafos específicos e auditam o uso de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos são configurados de acordo com a sua política de segurança de informação. Especificamente, as definições de política atribuídas por esta planta requerem encriptação para contas de armazenamento de lagos de dados; requerer encriptação de dados transparentes nas bases de dados SQL; e auditoria falta de encriptação em bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação.

- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- Implementar encriptação de dados transparentes SQL DB
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Exigir encriptação nas contas da Data Lake Store
- A encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="93172-si-2-flaw-remediation"></a>9.3.17.2 Correção de Falhas SI-2

Esta planta ajuda-o a gerir falhas no sistema de informação atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam atualizações de sistemas em falta, vulnerabilidades do sistema operativo, vulnerabilidades de SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados. Esta planta também atribui uma definição de política que garante a remendação do sistema operativo para conjuntos de escala de máquinas virtuais.

- Requerem remendos automáticos de imagem de SO em conjuntos de escala de máquina virtual
- As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas nas suas máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- As vulnerabilidades na configuração de segurança nas suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="93173-si-3-malicious-code-protection"></a>9.3.17.3 Proteção de código malicioso SI-3

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo a proteção de código malicioso, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center e aplicam a solução antimalware da Microsoft em máquinas virtuais Windows.

- Implementar extensão padrão do Microsoft IaaSAntimalware para o Windows Server
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual
- Monitor que falta proteção de ponto final no Centro de Segurança Azure

## <a name="93173-si-3-1-malicious-code-protection--central-management"></a>9.3.17.3 SI-3 (1) Proteção de Código Malicioso | Gestão Central

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo a proteção de código malicioso, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de gestão e reporte centralizadas que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados.

- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual
- Monitor que falta proteção de ponto final no Centro de Segurança Azure

## <a name="93174-si-4-information-system-monitoring"></a>9.3.17.4 Monitorização do Sistema de Informação SI-4

Esta planta ajuda-o a monitorizar o seu sistema auditando e aplicando a segurança de registo e dados através dos recursos da Azure. Especificamente, as políticas atribuídas à auditoria e aplicação do agente Log Analytics, e a definições de segurança reforçadas para bases de dados SQL, contas de armazenamento e recursos de rede. Estas capacidades podem ajudá-lo a detetar comportamentos anómalos e indicadores de ataques para que possa tomar as medidas adequadas.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria em VMSS - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escalas De VM Linux (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- \[Pré-visualização \] : Implementar o agente de análise de registo para conjuntos de escala vm do Windows (VMSS)
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- Implementar proteção avançada de ameaças em contas de armazenamento
- Implementar auditorias em servidores SQL
- Implementar o observador de rede quando as redes virtuais forem criadas
- Implementar deteção de ameaças em servidores SQL

## <a name="93174-si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>9.3.17.4 SI-4 (18) Monitorização do Sistema de Informação | Analisar Tráfego / Exfiltração Secreta

A Advanced Threat Protection for Azure Storage deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas de proteção incluem padrões de acesso anómalos, extratos/uploads anómalos e atividade de armazenamento suspeito. Estes indicadores podem ajudá-lo a detetar a exfiltração secreta de informação.

- Implementar proteção avançada de ameaças em contas de armazenamento

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo da planta do IRS 1075, visite os seguintes artigos para saber sobre a planta e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Projeto de IRS 1075 - Visão geral](./index.md) 
>  [Projeto DE IRS 1075 - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).