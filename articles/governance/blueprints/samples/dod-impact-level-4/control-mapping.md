---
title: Controlos de amostra de amostra de tamanho do DoD Impact Level 4
description: Controlo do mapeamento da amostra de planta do DoD Impact Level 4. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 10/26/2020
ms.topic: sample
ms.openlocfilehash: 376ddd68373026a93eaf08774cee0a1f8ce06bec
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92675736"
---
# <a name="control-mapping-of-the-dod-impact-level-4-blueprint-sample"></a>Mapeamento de controlo da amostra de planta do DoD Impact Level 4

O artigo seguinte detalha como o Azure Blueprints Department of Defense Impact Level 4 (DoD IL4) mapes de amostra para os controlos do DoD Impact Level 4. Para obter mais informações sobre os controlos, consulte [o Guia de Requisitos de Segurança da Computação em Nuvem do DoD (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf). A Defense Information Systems Agency (DISA) é uma agência do Departamento de Defesa dos EUA (DoD) que é responsável pelo desenvolvimento e manutenção do Guia de Requisitos de Segurança da Computação em Nuvem do DoD (SRG). O SRG define os requisitos de segurança de base para os fornecedores de serviços na nuvem (CSPs) que hospedam informações, sistemas e aplicações do DoD, e para a utilização do DoD de serviços na nuvem.  

Os seguintes mapeamentos são para os controlos **DoD Impact Level 4.** Utilize a navegação no direito de saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa [da Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a **\[ iniciativa política de pré-visualização \] : DoD Impact Level 4** incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/dod-impact-level-4/control-mapping.md).

## <a name="ac-2-account-management"></a>Gestão de Conta AC-2

Este projeto ajuda-o a rever contas que podem não cumprir os requisitos de gestão de conta da sua organização. Este projeto atribui definições [de Política Azure](../../../policy/overview.md) que auditam contas externas com permissões lidas, escritas e do proprietário numa subscrição e contas prevadadas. Ao rever as contas auditadas por estas políticas, pode tomar as medidas adequadas para garantir que os requisitos de gestão da conta são cumpridos.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Gestão de Conta / Regimes Role-Based

A Azure implementa [o controlo de acesso baseado em funções (Azure RBAC) para ajudá-lo](../../../../role-based-access-control/overview.md) a gerir quem tem acesso a recursos em Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Esta planta também atribui definições [de Política Azure](../../../policy/overview.md) à utilização de auditação da autenticação do Azure Ative Directory para servidores SQL e Tecido de Serviço. A utilização da autenticação do Azure Ative Directory permite uma gestão simplificada da permissão e uma gestão centralizada da identidade dos utilizadores de bases de dados e outros serviços da Microsoft. Além disso, este projeto atribui uma definição de Política Azure para auditar o uso de regras Azure RBAC personalizadas. Compreender onde as regras Azure RBAC personalizadas são implementadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do Azure RBAC são propensas a erros.

- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL
- Auditar o uso das regras personalizadas do RBAC
- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Gestão de Contas / Monitorização de Conta / Utilização Atípica

O acesso de máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. Todos os pedidos de JIT para aceder a máquinas virtuais estão registados no Registo de Atividades que lhe permite monitorizar para uma utilização atípica. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar o acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="ac-4-information-flow-enforcement"></a>AC-4 Informação Fluxo de Fluxo

A partilha de recursos de origem cruzada (CORS) pode permitir que os recursos dos Serviços de Aplicação sejam solicitados a partir de um domínio externo. A Microsoft recomenda que apenas os domínios necessários interajam com as suas aplicações API, função e web. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) para ajudá-lo a monitorizar as restrições de acesso a recursos CORS no Centro de Segurança Azure. Compreender as implementações do CORS pode ajudá-lo a verificar se os controlos de fluxo de informação são implementados.

- O CORS não deve permitir que todos os recursos acedam à sua Aplicação Web

## <a name="ac-5-separation-of-duties"></a>AC-5 Separação de Deveres

Ter apenas um proprietário de assinatura Azure não permite despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições Azure pode aumentar o potencial de uma violação através de uma conta de proprietário comprometida. Este projeto ajuda-o a manter um número adequado de proprietários de subscrições da Azure, atribuindo definições [da Azure Policy](../../../policy/overview.md) que auditam o número de proprietários para subscrições Azure. Esta planta também atribui definições de Política Azure que o ajudam a controlar a adesão do grupo de administradores em máquinas virtuais Windows. Gerir permissões de proprietário de subscrição e administrador de máquinas virtuais pode ajudá-lo a implementar a separação adequada dos deveres.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Auditoria Windows VMs em que o grupo de administradores contém qualquer um dos membros especificados
- Auditoria VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores contém qualquer um dos membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Menos Privilégio / Revisão dos Privilégios de Utilização

A Azure implementa [o controlo de acesso baseado em funções (Azure RBAC) para ajudá-lo](../../../../role-based-access-control/overview.md) a gerir quem tem acesso a recursos em Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Este projeto atribui definições [de Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão. Rever estes indicadores de conta pode ajudá-lo a garantir que os controlos de privilégios são implementados.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Auditoria Windows VMs em que o grupo de administradores contém qualquer um dos membros especificados
- Auditoria VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores contém qualquer um dos membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo de administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Acesso Remoto / Monitorização automatizada / Controlo

Esta planta ajuda-o a monitorizar e a controlar o acesso remoto, atribuindo definições [de Política Azure](../../../policy/overview.md) aos monitores que a depuração remota para a aplicação do Azure App Service é desligada e definições de política que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem palavras-passe. Esta planta também atribui uma definição de Política Azure que o ajuda a monitorizar o acesso ilimitado às contas de armazenamento. A monitorização destes indicadores pode ajudá-lo a garantir que os métodos de acesso remoto cumpram a sua política de segurança.

- \[Pré-visualização \] : Audit Linux VMs que permitem ligações remotas a partir de contas sem senhas
- \[Pré-visualização \] : Implementar requisitos para auditar Os VMs do Linux que permitem ligações remotas a partir de contas sem senhas
- Auditoria acesso ilimitado à rede a contas de armazenamento
- Depuragem remota deve ser desligada para app API
- Depuragem remota deve ser desligada para a App de Função
- Depuragem remota deve ser desligada para aplicação web

## <a name="ac-23-data-mining"></a>AC-23 Data Mining

Este plano fornece definições de política que o ajudam a garantir que as notificações de segurança de dados estão devidamente ativadas. Além disso, esta planta garante que a auditoria e a segurança avançada dos dados são configuradas nos Servidores SQL.

- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas pelo SQL
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- Os tipos avançados de proteção de ameaças devem ser definidos para 'All' em definições de segurança de dados avançadas de casos
- A auditoria deve ser ativada em definições avançadas de segurança de dados no SQL Server
- As notificações por e-mail para administradores e proprietários de subscrições devem ser ativadas nas definições avançadas de segurança de dados do servidor SQL
- As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados de casos DE SQL
- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança
- As definições avançadas de segurança de dados para a ocorrência gerida pelo SQL devem conter um endereço de e-mail para receber alertas de segurança

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Conteúdo dos Registos de Auditoria Gestão Centralizada do Conteúdo Recorde de Auditoria Planeada

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho log Analytics que permite a configuração e gestão centralizadas. Esta planta ajuda-o a garantir que os eventos são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual Linux
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual do Windows
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Resposta AU-5 às falhas de processamento de auditoria

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que monitorizam as configurações de auditoria e registo de eventos. A monitorização destas configurações pode fornecer um indicador de uma falha do sistema de auditoria ou de uma configuração errada e ajudá-lo a tomar medidas corretivas.

- Definição de diagnóstico de auditoria
- A auditoria deve ser ativada em definições avançadas de segurança de dados no SQL Server
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Análise, Análise e Reporte de Auditoria Análise e Análise Central

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho do Log Analytics que permite relatórios e análises centralizados. Esta planta ajuda-o a garantir que os eventos são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual Linux
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual do Windows
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) Análise, Análise e Reporte de Auditoria Capacidades de Integração / Digitalização e Monitorização

Esta planta fornece definições de política que auditam registos com a análise da avaliação de vulnerabilidades em máquinas virtuais, conjuntos de escala de máquinas virtuais, servidores de base de dados SQL e servidores SQL Managed Instance. Estas definições de política também auditam a configuração de registos de diagnóstico para fornecer informações sobre operações que são realizadas dentro dos recursos Azure. Estas informações fornecem informações em tempo real sobre o estado de segurança dos seus recursos mobilizados e podem ajudá-lo a priorizar ações de reparação. Para uma análise e monitorização detalhadas da vulnerabilidade, recomendamos que aproveite também o Azure Sentinel e o Azure Security Center.

- \[Pré-visualização \] : A avaliação da vulnerabilidade deve ser ativada em Máquinas Virtuais
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- Definição de diagnóstico de auditoria
- A avaliação da vulnerabilidade deve ser ativada nas suas instâncias geridas pelo SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado

## <a name="au-12-audit-generation"></a>Geração de auditoria AU-12

Este projeto fornece definições de política que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure e configuração de definições de auditoria para outros tipos de recursos Azure.
Estas definições de política também auditam a configuração de registos de diagnóstico para fornecer informações sobre operações que são realizadas dentro dos recursos Azure. Além disso, a auditoria e a Segurança Avançada de Dados estão configuradas em servidores SQL.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual Linux
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual do Windows
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows
- Definição de diagnóstico de auditoria
- A auditoria deve ser ativada em definições avançadas de segurança de dados no SQL Server
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- Implementar auditorias em servidores SQL
- Implementar definições de diagnóstico para grupos de segurança de rede

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) Geração de Auditoria / System-Wide / Time-Correlated Audit Trail

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam as definições de registo nos recursos do Azure.
Esta política incorporada requer que especifique uma série de tipos de recursos para verificar se as definições de diagnóstico estão ativadas ou não.

- Definição de diagnóstico de auditoria

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Menor Funcionalidade / Impedir a execução do programa

O controlo de aplicações adaptativas no Azure Security Center é uma solução inteligente e automatizada de aplicação de ponta a ponta que permite a solução de lista de chamadas que pode bloquear ou impedir que softwares específicos sejam executadas nas suas máquinas virtuais. O controlo da aplicação pode ser executado num modo de execução que proíbe a execução de aplicações não aprovadas. Esta planta atribui uma definição de Política Azure que o ajuda a monitorizar máquinas virtuais onde uma lista de pedidos é recomendada, mas ainda não foi configurada.

- Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Menor Funcionalidade / Software autorizado / Whitelisting

O controlo de aplicações adaptativas no Azure Security Center é uma solução inteligente e automatizada de aplicação de ponta a ponta que permite a solução de lista de chamadas que pode bloquear ou impedir que softwares específicos sejam executadas nas suas máquinas virtuais. O controlo de aplicações ajuda-o a criar listas de candidaturas aprovadas para as suas máquinas virtuais. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista de pedidos é recomendada, mas ainda não foi configurada.

- Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas

## <a name="cm-11-user-installed-software"></a>CM-11 User-Installed Software

O controlo de aplicações adaptativas no Azure Security Center é uma solução inteligente e automatizada de aplicação de ponta a ponta que permite a solução de lista de chamadas que pode bloquear ou impedir que softwares específicos sejam executadas nas suas máquinas virtuais. O controlo de aplicações pode ajudá-lo a impor e monitorizar o cumprimento das políticas de restrição de software. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista de pedidos é recomendada, mas ainda não foi configurada.

- Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas

## <a name="cp-7-alternate-processing-site"></a>Cp-7 Local de Processamento Alternativo

A Azure Site Recovery replica cargas de trabalho em máquinas virtuais de um local primário para um local secundário. Se ocorrer uma paragem no local primário, a carga de trabalho falha sobre a localização secundária. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que audita máquinas virtuais sem a recuperação de desastres configurada. A monitorização deste indicador pode ajudá-lo a garantir que os controlos de contingência necessários estão em vigor.

- Auditar máquinas virtuais sem recuperação de desastres configurada

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>Reserva do Sistema de Informação CP-9 (05) Transferência para Local de Armazenamento Alternativo

Esta planta atribui definições de Política Azure que auditam as informações de backup do sistema da organização para o local de armazenamento alternativo eletronicamente. Para envio físico de metadados de armazenamento, considere a utilização da Caixa de Dados Azure.

- O armazenamento geo-redundante deve ser ativado para contas de armazenamento
- A cópia de segurança geo-redundante deve ser ativada para a Base de Dados Azure para PostgreSQL
- Backup geo-redundante deve ser ativado para Azure Database para MySQL
- Backup geo-redundante deve ser ativado para Azure Database for MariaDB
- Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identificação e Autenticação (Utilizadores Organizacionais) / Acesso à Rede a Contas Privilegiadas

Este projeto ajuda-o a restringir e a controlar o acesso privilegiado atribuindo definições [da Azure Policy](../../../policy/overview.md) a contas de auditoria com o proprietário e/ou a escrever permissões que não tenham autenticação de vários fatores ativadas. A autenticação multi-factor ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação esteja comprometida. Ao monitorizar contas sem autenticação multi-factor ativada, pode identificar contas que possam ser mais comprometidas.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- O MFA deve ser ativado em contas com permissões de escrita na sua subscrição

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identificação e Autenticação (Utilizadores Organizacionais) / Acesso à Rede a Contas Não Privilegiadas

Este plano ajuda-o a restringir e a controlar o acesso, atribuindo uma definição [de Política Azure](../../../policy/overview.md) a contas de auditoria com permissões de leitura que não têm autenticação multi-factor ativada. A autenticação multi-factor ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação esteja comprometida. Ao monitorizar contas sem autenticação multi-factor ativada, pode identificar contas que possam ser mais comprometidas.

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="ia-5-authenticator-management"></a>Gestão de Autenticadores IA-5

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem palavras-passe e/ou têm permissões incorretas definidas no ficheiro passwd. Esta planta também atribui definições de política que auditam a configuração do tipo de encriptação de palavra-passe para máquinas virtuais do Windows. A monitorização destes indicadores ajuda-o a garantir que os autenticadores do sistema cumprem a política de identificação e autenticação da sua organização.

- \[Pré-visualização \] : Audit Linux VMs que não têm as permissões de ficheiros passwd definidas para 0644
- \[Pré-visualização \] : Auditoria Linux VMs que têm contas sem senhas
- \[Pré-visualização \] : Auditar VMs do Windows que não armazenam palavras-passe utilizando encriptação reversível
- \[Pré-visualização \] : Implementar requisitos para auditar Os VMs do Linux que não têm as permissões de ficheiros passwd definidas para 0644
- \[Pré-visualização \] : Implementar requisitos para auditar Os VMs do Linux que têm contas sem senhas
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não armazenam palavras-passe usando encriptação reversível

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Gestão de Autenticadores / Autenticação Password-Based

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

## <a name="ir-6-2-incident-reporting--vulnerabilities-related-to-incidents"></a>IR-6 (2) Relatório de Incidentes / Vulnerabilidades Relacionadas com Incidentes

Este plano fornece definições de política que auditam registos com a análise da avaliação de vulnerabilidades em máquinas virtuais, conjuntos de escala de máquinas virtuais e servidores SQL. Estas informações fornecem informações em tempo real sobre o estado de segurança dos seus recursos mobilizados e podem ajudá-lo a priorizar ações de reparação.

- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas configurações de segurança dos contentores devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas

## <a name="ra-5-vulnerability-scanning"></a>Varredura de vulnerabilidade RA-5

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados. Esta planta também atribui definições de política que auditam e aplicam a Segurança Avançada de Dados em servidores SQL. A segurança avançada de dados incluiu a avaliação de vulnerabilidades e as capacidades avançadas de proteção de ameaças para ajudá-lo a compreender as vulnerabilidades nos seus recursos implantados.

- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- As vulnerabilidades na configuração de segurança nas suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Negação de Proteção de Serviços

O nível padrão de negação de serviço distribuído (DDoS) da Azure fornece funcionalidades adicionais e capacidades de mitigação sobre o nível básico de serviço. Estas funcionalidades adicionais incluem a integração do Azure Monitor e a capacidade de rever relatórios de mitigação pós-ataque. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que audita se o nível padrão DDoS estiver ativado. Compreender a diferença de capacidade entre os níveis de serviço pode ajudá-lo a selecionar a melhor solução para resolver as proteções de negação de serviço para o seu ambiente Azure.

- A Norma de Proteção DDoS deve ser ativada

## <a name="sc-7-boundary-protection"></a>Proteção de Fronteiras SC-7

Esta planta ajuda-o a gerir e a controlar a fronteira do sistema, atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza as recomendações de endurecimento do grupo de segurança da rede no Centro de Segurança Azure. O Azure Security Center analisa os padrões de tráfego da Internet face às máquinas virtuais e fornece recomendações de regras do grupo de segurança da rede para reduzir a potencial superfície de ataque.
Além disso, este plano também atribui definições de política que monitorizam pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- As regras do Grupo de Segurança da Rede para máquinas virtuais que enfrentam a Internet devem ser endurecidas
- O acesso através da Internet face ao ponto final deve ser restringido
- As regras do NSGs para aplicações web em IaaS devem ser endurecidas
- Auditoria acesso ilimitado à rede a contas de armazenamento

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Proteção de Fronteiras / Pontos de Acesso

O acesso de máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. O acesso virtual à máquina JIT ajuda-o a limitar o número de ligações externas aos seus recursos em Azure. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar o acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Proteção de Fronteiras / Serviços externos de Telecomunicações

O acesso de máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando fácil acesso à ligação aos VM quando necessário. O acesso a máquinas virtuais JIT ajuda-o a gerir exceções à sua política de fluxo de tráfego, facilitando os processos de pedido de acesso e aprovação. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar o acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Confidencialidade e Integridade da Transmissão Proteção Física Criptográfica ou Alternativa

Esta planta ajuda-o a proteger a confidencialidade e a integridade das informações transmitidas, atribuindo definições [da Política Azure](../../../policy/overview.md) que o ajudam a monitorizar o mecanismo criptográfico implementado para protocolos de comunicações. Garantir que as comunicações estão devidamente encriptadas pode ajudá-lo a satisfazer os requisitos da sua organização ou proteger informações de divulgação e modificação não autorizadas.

- A API App só deve estar acessível em HTTPS
- Auditar servidores web do Windows que não estão a utilizar protocolos de comunicação seguros
- Implementar requisitos para auditar servidores web do Windows que não estão a utilizar protocolos de comunicação seguros
- A App de função só deve estar acessível através do HTTPS
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- A transferência segura para contas de armazenamento deve ser ativada
- A Aplicação Web só deve ser acessível em HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Proteção da Informação em Repouso Proteção Criptográfica

Esta planta ajuda-o a aplicar a sua política sobre a utilização de controlos criptógrafos para proteger a informação em repouso, atribuindo definições [de Política Azure](../../../policy/overview.md) que impõem controlos criptógrafos específicos e auditam o uso de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos são configurados de acordo com a sua política de segurança de informação. Especificamente, as definições de política atribuídas por esta planta requerem encriptação para contas de armazenamento de lagos de dados; requerer encriptação de dados transparentes nas bases de dados SQL; e auditoria falta de encriptação em bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação.

- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- Implementar encriptação de dados transparentes SQL DB
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Exigir encriptação nas contas da Data Lake Store
- A encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="si-2-flaw-remediation"></a>Reparação de falhas SI-2

Esta planta ajuda-o a gerir falhas no sistema de informação atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam atualizações de sistemas em falta, vulnerabilidades do sistema operativo, vulnerabilidades de SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados. Esta planta também atribui uma definição de política que garante a remendação do sistema operativo para conjuntos de escala de máquinas virtuais.

- Requerem remendos automáticos de imagem de SO em conjuntos de escala de máquina virtual
- As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas nas suas máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- As vulnerabilidades na configuração de segurança nas suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="si-02-06-flaw-remediation--removal-of-previous-versions-of-software--firmware"></a>SI-02 (06) Correção de Falhas Remoção de Versões Anteriores de Software / Firmware

Este projeto atribui definições de política que o ajudam a garantir que as aplicações estão a usar a versão mais recente de HTTP, Java, PHP, Python e TLS. Esta planta também atribui uma definição de política que garante que os Serviços Kubernetes são atualizados para a sua versão não vulnerável.

- Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Api
- Certifique-se de que 'versão HTTP' é a mais recente, se usada para executar a aplicação 'Função'
- Certifique-se de que 'VERSÃO HTTP' é a mais recente, se usada para executar a aplicação Web
- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Function
- Certifique-se de que 'Versão Java' é a mais recente, se usada como parte da aplicação Web
- Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação Api
- Certifique-se de que a 'versão PHP' é a mais recente, se usada como parte da aplicação WEB
- Certifique-se de que 'Python version' é a mais recente, se usada como parte da app Api
- Certifique-se de que 'Versão Python' é a mais recente, se usada como parte da aplicação Function
- Certifique-se de que 'Python version' é a mais recente, se usada como parte da aplicação Web
- A versão TLS mais recente deve ser usada na sua App API
- A versão TLS mais recente deve ser usada na sua App de Função
- A versão mais recente do TLS deve ser usada na sua Web App
- Os Serviços Kubernetes devem ser atualizados para uma versão não vulnerável de Kubernetes

## <a name="si-3-malicious-code-protection"></a>Proteção de código malicioso SI-3

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo a proteção de código malicioso, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center e aplicam a solução antimalware da Microsoft em máquinas virtuais Windows.

- Implementar extensão padrão do Microsoft IaaSAntimalware para o Windows Server
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual
- Monitor que falta proteção de ponto final no Centro de Segurança Azure

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Proteção de Código Malicioso / Gestão Central

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo a proteção de código malicioso, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de gestão e reporte centralizadas que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados.

- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual
- Monitor que falta proteção de ponto final no Centro de Segurança Azure

## <a name="si-4-information-system-monitoring"></a>Monitorização do sistema de informação SI-4

Esta planta ajuda-o a monitorizar o seu sistema auditando e aplicando a segurança de registo e dados através dos recursos da Azure. Especificamente, as políticas atribuídas à auditoria e aplicação do agente Log Analytics, e a definições de segurança reforçadas para bases de dados SQL, contas de armazenamento e recursos de rede. Estas capacidades podem ajudá-lo a detetar comportamentos anómalos e indicadores de ataques para que possa tomar as medidas adequadas.

- \[Pré-visualização \] : Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- \[Pré-visualização \] : Audit Log Analytics Workspace for VM - Report Incompatibilidade
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual Linux
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- Implementar o agente Desagregalhador para conjuntos de escala de máquina virtual do Windows
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Implementar segurança de dados avançada em servidores SQL
- Implementar proteção avançada de ameaças em contas de armazenamento
- Implementar auditorias em servidores SQL
- Implementar o observador de rede quando as redes virtuais forem criadas
- Implementar deteção de ameaças em servidores SQL
- Localizações permitidas
- Locais permitidos para grupos de recursos

## <a name="si-4-12-information-system-monitoring--automated-alerts"></a>SI-4 (12) Monitorização do Sistema de Informação / Alertas Automatizados

Este plano fornece definições de política que o ajudam a garantir que as notificações de segurança de dados estão devidamente ativadas. Além disso, esta planta garante que o nível de preços padrão está ativado para o Azure Security Center. Note que o nível de preços padrão permite a deteção de ameaças para redes e máquinas virtuais, fornecendo inteligência de ameaça, deteção de anomalias e análise de comportamento no Azure Security Center.

- A notificação por e-mail ao proprietário da subscrição para alertas de alta gravidade deve ser ativada
- Um endereço de e-mail de contacto de segurança deve ser fornecido para a sua subscrição 
- As notificações por e-mail aos administradores e proprietários de subscrições devem ser ativadas em definições avançadas de segurança de dados de casos DE SQL 
- As notificações por e-mail para administradores e proprietários de subscrições devem ser ativadas nas definições avançadas de segurança de dados do servidor SQL 
- Um número de telefone de contacto de segurança deve ser fornecido para a sua subscrição
- As definições avançadas de segurança de dados para o servidor SQL devem conter um endereço de e-mail para receber alertas de segurança
- O nível de preços padrão do Centro de Segurança deve ser selecionado

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Monitorização do Sistema de Informação Analisar Tráfego / Exfiltração Secreta

A Advanced Threat Protection for Azure Storage deteta tentativas incomuns e potencialmente nocivas de aceder ou explorar contas de armazenamento. Os alertas de proteção incluem padrões de acesso anómalos, extratos/uploads anómalos e atividade de armazenamento suspeito. Estes indicadores podem ajudá-lo a detetar a exfiltração secreta de informação.

- Implementar proteção avançada de ameaças em contas de armazenamento

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo da planta DoD Impact Level 4, visite os seguintes artigos para saber sobre a planta e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Projeto do DoD Impact Level 4 - Visão geral](./index.md) 
>  [Projeto do DoD Impact Level 4 - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).