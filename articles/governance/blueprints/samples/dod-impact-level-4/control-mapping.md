---
title: Controlos de amostra de planta de nível de impacto do DoD 4
description: Mapeamento de controlo da amostra de planta do Nível de Impacto 4 do DoD. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 02/09/2020
ms.topic: sample
ms.openlocfilehash: 15ab3bc8bf53d54161ecc3b1f0dc138c3ff923c1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154845"
---
# <a name="control-mapping-of-the-dod-impact-level-4-blueprint-sample"></a>Mapeamento de controlo da amostra de planta do Nível de Impacto 4 do DoD

O seguinte artigo detalha como o Departamento de Impacto de Projetos de Defesa do Azure Blueprints nível 4 (DoD IL4) mapeia os mapas de amostras do Nível de Impacto do DoD 4. Para mais informações sobre os controlos, consulte o Guia de Requisitos de Segurança da Computação em [Nuvem do DoD (SRG)](https://dl.dod.cyber.mil/wp-content/uploads/cloud/pdf/Cloud_Computing_SRG_v1r3.pdf). A Agência de Sistemas de Informação de Defesa (DISA) é uma agência do Departamento de Defesa dos EUA (DoD) que é responsável pelo desenvolvimento e manutenção do Guia de Requisitos de Segurança da Computação em Nuvem do DoD (SRG). O SRG define os requisitos de segurança básicos para os fornecedores de serviços na nuvem (CSPs) que acolhem informações, sistemas e aplicações do DoD, e para a utilização do DoD de serviços na nuvem.  

Os seguintes mapeamentos são para os **controlos DoD Impact Level 4.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a **\[Preview\]: DoD Impact Level 4** iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo.
> Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/DoDIL4/control-mapping.md).

## <a name="ac-2-account-management"></a>Gestão de Conta AC-2

Este plano ajuda-o a rever contas que podem não cumprir os requisitos de gestão de conta da sua organização. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) que auditam contas externas com permissões de leitura, escrita e proprietário numa subscrição e contas depreciadas. Ao rever as contas auditadas por estas políticas, pode tomar as medidas adequadas para garantir que os requisitos de gestão de contas sejam cumpridos.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="ac-2-7-account-management--role-based-schemes"></a>AC-2 (7) Gestão de Conta  Esquemas baseados em papéis

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Esta planta atribui também definições de [Política Azure](../../../policy/overview.md) para auditar a autenticação do Diretório Ativo Azure para servidores SQL e Tecido de Serviço. Usar Azure Active Directory autenticação permite o gerenciamento de permissões simplificado e o gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft. Além disso, este projeto atribui uma definição de Política Azure para auditar a utilização de regras rbac personalizadas. Entender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar a necessidade e a implementação adequada, pois as regras personalizadas de RBAC são propensas a erros.

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Auditar o uso de regras personalizadas de RBAC
- Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>AC-2 (12) Gestão de Contas  Monitorização da Conta / Utilização Atípica

O acesso à máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. Todos os pedidos de Acesso a Máquinas Virtuais do JIT estão registados no Registo de Atividade, permitindo-lhe monitorizar para utilização atípica. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="ac-4-information-flow-enforcement"></a>Execução do fluxo de informação AC-4

A partilha de recursos de origem cruzada (CORS) pode permitir que os recursos dos Serviços de Aplicações sejam solicitados a partir de um domínio externo. A Microsoft recomenda que permita que apenas os domínios necessários interajam com as suas aplicações API, função e web. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) para ajudá-lo a monitorizar as restrições de acesso aos recursos cors no Centro de Segurança Azure. Compreender as implementações do CORS pode ajudá-lo a verificar se os controlos de fluxo de informação são implementados.

- O CORS não deve permitir que todos os recursos acessem seu aplicativo Web

## <a name="ac-5-separation-of-duties"></a>SEparação de direitos AC-5

Ter apenas um proprietário de assinatura do Azure não permite redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este plano ajuda-o a manter um número adequado de proprietários de subscrições azure, atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam o número de proprietários para subscrições do Azure. Esta planta também atribui definições de Política Azure que o ajudam a controlar a adesão do grupo de Administradores em máquinas virtuais Windows. Gerir o proprietário da subscrição e permissões de administrador de máquinas virtuais pode ajudá-lo a implementar a separação adequada de deveres.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Audite VMs windows em que o grupo administradores contém qualquer um dos membros especificados
- Audite VMs windows em que o grupo administradores não contém todos os membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo Administradores não contenha todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>AC-6 (7) Menos Privilégio [ Privilégio Mínimo] Revisão dos Privilégios do Utilizador

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão. Rever estes indicadores de conta pode ajudá-lo a garantir que os controlos de menor privilégio sejam implementados.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Audite VMs windows em que o grupo administradores contém qualquer um dos membros especificados
- Audite VMs windows em que o grupo administradores não contém todos os membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo administradores contém qualquer um dos membros especificados
- Implementar requisitos para auditar VMs do Windows em que o grupo Administradores não contenha todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) Acesso Remoto / Monitorização/Controlo Automatizado

Esta planta ajuda-o a monitorizar e controlar o acesso remoto atribuindo definições de [Política Azure](../../../policy/overview.md) para monitorizar que a depuração remota para aplicação do Serviço de Aplicações Azure é desligada e definições de políticas que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem senhas. Esta planta também atribui uma definição de Política Azure que o ajuda a monitorizar o acesso ilimitado a contas de armazenamento. Monitorizar estes indicadores pode ajudá-lo a garantir que os métodos de acesso remoto satisfaçam a sua política de segurança.

- \[Pré-\]de Pré-visualização : Audite VMs Linux que permitem ligações remotas a partir de contas sem senhas
- \[Preview\]: Implementar requisitos para auditar VMs Linux que permitem ligações remotas a partir de contas sem senhas
- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- A depuração remota deve ser desativada para o aplicativo de API
- A depuração remota deve ser desativada para Aplicativo de funções
- Depuração remota deve ser desativada para a aplicação Web

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) Conteúdo dos Registos de Auditoria / Gestão Centralizada de Conteúdos De Registo de Auditoria Planeada

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho de Log Analytics que permite a configuração e gestão centralizadas. Este plano ajuda-o a garantir que os eventos são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não cotada
- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não cotada
- \[pré-visualização\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[pré-visualização\]: Implementar o agente de análise de registo para VMs Linux
- \[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)
- \[\]de pré-visualização : Implementar o agente de análise de registo para VMs do Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Resposta AU-5 às falhas no processamento de auditoria

Esta planta atribui definições de [Política Azure](../../../policy/overview.md) que monitorizam as configurações de auditoria e registo de eventos. Monitorizar estas configurações pode fornecer um indicador de uma falha ou configuração errada do sistema de auditoria e ajudá-lo a tomar medidas corretivas.

- Definição de diagnóstico de auditoria
- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada de dados deve ser ativada nos seus servidores SQL

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) Revisão, Análise e Relatório de Auditoria Análise e Análise Central

Os dados de registo recolhidos pelo Azure Monitor são armazenados num espaço de trabalho de Log Analytics que permite reportagens e análises centralizadas. Este plano ajuda-o a garantir que os eventos são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure.

- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não cotada
- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não cotada
- \[pré-visualização\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[pré-visualização\]: Implementar o agente de análise de registo para VMs Linux
- \[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)
- \[\]de pré-visualização : Implementar o agente de análise de registo para VMs do Windows

## <a name="au-6-5-audit-review-analysis-and-reporting--integration--scanning-and-monitoring-capabilities"></a>AU-6 (5) Revisão, Análise e Relatório de Auditoria Capacidades de Integração / Digitalização e Monitorização

Esta planta fornece definições de políticas que auditam registos com análise de avaliação de vulnerabilidade em máquinas virtuais, conjuntos de escala de máquinas virtuais, instâncias geridas por SQL e servidores SQL.
Estas definições de política também auditam a configuração de registos de diagnóstico para fornecer informações sobre as operações que são realizadas dentro dos recursos do Azure. Estes insights fornecem informações em tempo real sobre o estado de segurança dos seus recursos implantados e podem ajudá-lo a priorizar ações de reparação.
Para uma análise e monitorização detalhadas da vulnerabilidade, recomendamos que aproveite também o Azure Sentinel e o Azure Security Center.

- \[pré-visualização\]: A avaliação da vulnerabilidade deve ser ativada em máquinas virtuais
- \[pré-visualização\]: Ativar o Monitor Azure para VMs
- \[pré-visualização\]: Ativar o Monitor Azure para conjuntos de escala VM (VMSS)
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- Definição de diagnóstico de auditoria
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas

## <a name="au-12-audit-generation"></a>Geração de Auditoria AU-12

Esta planta fornece definições de política que auditam e impõem a implementação do agente Log Analytics em máquinas virtuais Azure e configuração de definições de auditoria para outros tipos de recursos Azure.
Estas definições de política também auditam a configuração de registos de diagnóstico para fornecer informações sobre as operações que são realizadas dentro dos recursos do Azure. Adicionalmente, a auditoria e a Segurança Avançada de Dados estão configuradas nos servidores SQL.

- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não cotada
- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não cotada
- \[pré-visualização\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[pré-visualização\]: Implementar o agente de análise de registo para VMs Linux
- \[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)
- \[\]de pré-visualização : Implementar o agente de análise de registo para VMs do Windows
- Definição de diagnóstico de auditoria
- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- Implementar auditoria sql
- Implementar definições de diagnóstico para grupos de segurança de rede

## <a name="au-12-01-audit-generation--system-wide--time-correlated-audit-trail"></a>AU-12 (01) Geração de Auditoria [ Geração de Auditoria] Trail de auditoria de nível de sistema / correlacionado com o tempo

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam definições de registo nos recursos do Azure.
Esta política incorporada requer que você especifique um conjunto de tipos de recursos para verificar se as configurações de diagnóstico estão ativadas ou não.

- Definição de diagnóstico de auditoria

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>CM-7 (2) Menos Funcionalidade  Impedir a execução do programa

O controlo adaptativo de aplicações no Azure Security Center é uma solução inteligente e automatizada de whitelisting de aplicação de ponta a ponta que pode bloquear ou impedir que software específico seja funcionado nas suas máquinas virtuais. O controlo da aplicação pode ser executado num modo de execução que proíbe a aplicação não aprovada de funcionar. Esta planta atribui uma definição de Política Azure que o ajuda a monitorizar máquinas virtuais onde uma lista branca de aplicação é recomendada, mas ainda não foi configurada.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>CM-7 (5) Menos Funcionalidade  Software autorizado / Whitelisting

O controlo adaptativo de aplicações no Azure Security Center é uma solução inteligente e automatizada de whitelisting de aplicação de ponta a ponta que pode bloquear ou impedir que software específico seja funcionado nas suas máquinas virtuais. O controlo de aplicações ajuda-o a criar listas de aplicações aprovadas para as suas máquinas virtuais. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista branca de aplicação é recomendada, mas ainda não foi configurada.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="cm-11-user-installed-software"></a>Software instalado pelo utilizador CM-11

O controlo adaptativo de aplicações no Azure Security Center é uma solução inteligente e automatizada de whitelisting de aplicação de ponta a ponta que pode bloquear ou impedir que software específico seja funcionado nas suas máquinas virtuais. O controlo de aplicações pode ajudá-lo a impor e monitorizar o cumprimento das políticas de restrição de software. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais onde uma lista branca de aplicação é recomendada, mas ainda não foi configurada.

- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais

## <a name="cp-7-alternate-processing-site"></a>Cp-7 Local de Processamento Alternativo

A Recuperação do Site Azure replica cargas de trabalho em máquinas virtuais desde um local primário até um local secundário. Se ocorrer uma paragem no local primário, a carga de trabalho falha na localização secundária. Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) que audita máquinas virtuais sem recuperação de desastres configurada. A monitorização deste indicador pode ajudá-lo a garantir que os controlos de contingência necessários estão em vigor.

- Auditar máquinas virtuais sem recuperação de desastres configurada

## <a name="cp-9-05--information-system-backup--transfer-to-alternate-storage-site"></a>CP-9 (05) Backup do Sistema de Informação / Transfer para local de armazenamento alternativo

Esta planta atribui definições de Política Azure que auditam as informações de backup do sistema da organização ao site de armazenamento alternativo eletronicamente. Para envio físico de metadados de armazenamento, considere utilizar a Caixa de Dados Azure.

- Armazenamento geo-redundante deve ser ativado para contas de armazenamento
- Backup geo-redundante deve ser ativado para Base de Dados Azure para PostgreSQL
- Backup geo-redundante deve ser ativado para Base de Dados Azure para MySQL
- Backup geo-redundante deve ser ativado para base de dados Azure para MariaDB
- Backup geo-redundante a longo prazo deve ser ativado para bases de dados Azure SQL

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>IA-2 (1) Identificação e Autenticação (Utilizadores Organizacionais)  Utilizadores Organizacionais)  Utilizadores Organizacionais)  Acesso à Rede a Contas Privilegiadas

Este plano ajuda-o a restringir e controlar o acesso privilegiado através da atribuição de definições de [Política Azure](../../../policy/overview.md) para auditar contas com o proprietário e/ou escrever permissões que não tenham autenticação multifactor ativada. A autenticação multifator ajuda a manter as contas seguras mesmo que uma parte das informações de autenticação seja comprometida. Ao monitorar contas sem a autenticação multifator habilitada, você pode identificar contas que podem ser mais prováveis de serem comprometidas.

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de escrita na sua subscrição

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>IA-2 (2) Identificação e Autenticação (Utilizadores Organizacionais)  Utilizadores Organizacionais)  Utilizadores Organizacionais)  Utilizadores Organizacionais)  Utilizadores Organizacionais)  Utilizadores Organizacionais Acesso à Rede a Contas Não Privilegiadas

Este plano ajuda-o a restringir e controlar o acesso atribuindo uma definição de [Política Azure](../../../policy/overview.md) para auditar contas com permissões de leitura que não tenham autenticação multifactor ativada. A autenticação multifator ajuda a manter as contas seguras mesmo que uma parte das informações de autenticação seja comprometida. Ao monitorar contas sem a autenticação multifator habilitada, você pode identificar contas que podem ser mais prováveis de serem comprometidas.

- MFA deve ser ativado em contas com permissões de leitura na sua subscrição

## <a name="ia-5-authenticator-management"></a>Gestão do Autenticador IA-5

Esta planta atribui definições [de Política Azure](../../../policy/overview.md) que auditam máquinas virtuais Linux que permitem ligações remotas a partir de contas sem senhas e/ou têm permissões incorretas definidas no ficheiro passwd. Esta planta também atribui definições de política que auditam a configuração do tipo de encriptação de palavra-passe para máquinas virtuais windows. Monitorizar estes indicadores ajuda-o a garantir que os autenticadores do sistema cumprem a política de identificação e autenticação da sua organização.

- \[Pré-visualização\]: Audit Linux VMs que não têm as permissões de ficheiro sinuosas definidas para 0644
- \[Pré-visualização\]: Auditlin VMs que têm contas sem senhas
- \[Pré-visualização\]: Auditar VMs do Windows que não armazenam senhas usando encriptação reversível
- \[Pré-visualização\]: Implementar requisitos para auditar VMs Linux que não tenham as permissões de ficheiro sinuosas definidas para 0644
- \[Preview\]: Implementar requisitos para auditar VMs Linux que tenham contas sem senhas
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não armazenam palavras-passe usando encriptação reversível

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>IA-5 (1) Gestão do Autenticador / Autenticação baseada em palavra-passe

Este plano ajuda-o a impor senhas fortes atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam máquinas virtuais do Windows que não impõem a força mínima e outros requisitos de senha. A consciencialização das máquinas virtuais em violação da política de força da palavra-passe ajuda-o a tomar medidas corretivas para garantir que as palavras-passe de todas as contas de utilizadores de máquinas virtuais cumpram a política de passwords da sua organização.

- \[Pré-visualização\]: Auditar VMs do Windows que permitem a reutilização das 24 senhas anteriores
- \[Pré-visualização\]: Audite VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Auditar VMs do Windows que não têm uma idade mínima de senha de 1 dia
- \[Pré-visualização\]: Audite VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Pré-visualização\]: Auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Auditar VMs do Windows que não armazenam senhas usando encriptação reversível
- \[Preview\]: Implementar requisitos para auditar VMs do Windows que permitam a reutilização das 24 senhas anteriores
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não tenham uma idade mínima de senha de 1 dia
- \[Preview\]: Implementar requisitos para auditar VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Preview\]: Implementar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não armazenam palavras-passe usando encriptação reversível

## <a name="ra-5-vulnerability-scanning"></a>Digitalização de vulnerabilidadeRA-5

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. A central de segurança do Azure fornece recursos de relatório que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados. Esta planta também atribui definições de política que auditam e impõem segurança avançada de dados em servidores SQL. A segurança avançada de dados incluiu avaliação de vulnerabilidade e capacidades avançadas de proteção contra ameaças para ajudá-lo a entender vulnerabilidades nos seus recursos implantados.

- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- As vulnerabilidades na configuração de segurança das suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="sc-5-denial-of-service-protection"></a>SC-5 Negação de Proteção de Serviços

O nível padrão de negação de serviço distribuído da Azure (DDoS) fornece funcionalidades adicionais e capacidades de mitigação sobre o nível de serviço básico. Estas funcionalidades adicionais incluem a integração do Monitor Azure e a capacidade de rever relatórios de mitigação pós-ataque. Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) que audita se o nível padrão DDoS estiver ativado. Compreender a diferença de capacidade entre os níveis de serviço pode ajudá-lo a selecionar a melhor solução para lidar com a negação das proteções de serviço para o seu ambiente Azure.

- Norma de proteção DDoS deve ser ativada

## <a name="sc-7-boundary-protection"></a>Proteção de Fronteiras SC-7

Este plano ajuda-o a gerir e controlar o limite do sistema atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza as recomendações de endurecimento do grupo de segurança de rede no Azure Security Center. O Azure Security Center analisa os padrões de tráfego da Internet que enfrentam máquinas virtuais e fornece recomendações de regras do grupo de segurança da rede para reduzir a superfície de ataque potencial.
Além disso, este projeto também atribui definições de políticas que monitorizam pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos de extremidade e aplicativos que não são protegidos por um firewall e contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas
- O acesso através da Internet virado para o ponto final deve ser restringido
- As regras do NSGs para aplicações web em IaaS devem ser endurecidas
- Auditoria sem restrições ao acesso da rede às contas de armazenamento

## <a name="sc-7-3-boundary-protection--access-points"></a>SC-7 (3) Proteção de Fronteiras  Pontos de Acesso

O acesso à máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. O acesso à máquina virtual JIT ajuda-o a limitar o número de ligações externas aos seus recursos em Azure. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>SC-7 (4) Proteção de Fronteiras  Serviços Externos de Telecomunicações

O acesso à máquina virtual just-in-time (JIT) bloqueia o tráfego de entrada para máquinas virtuais Azure, reduzindo a exposição a ataques, proporcionando um fácil acesso à ligação aos VMs quando necessário. O acesso à máquina virtual JIT ajuda-o a gerir exceções à sua política de fluxo de tráfego facilitando os processos de pedido de acesso e aprovação. Esta planta atribui uma definição de [Política Azure](../../../policy/overview.md) que o ajuda a monitorizar máquinas virtuais que podem suportar acesso just-in-time mas ainda não foram configuradas.

- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>SC-8 (1) Confidencialidade e Integridade da Transmissão ; Proteção Física Criptográfica ou Alternativa

Esta planta ajuda-o a proteger a confidencialidade e integridade das informações transmitidas, atribuindo definições de [Política Azure](../../../policy/overview.md) que o ajudam a monitorizar o mecanismo criptográfico implementado para protocolos de comunicações. Garantir que as comunicações estão devidamente encriptadas pode ajudá-lo a satisfazer os requisitos da sua organização ou proteger informações contra divulgação e modificação não autorizadas.

- O aplicativo de API só deve ser acessível via HTTPS
- Audite servidores web do Windows que não estão a usar protocolos de comunicação seguros
- Implementar requisitos para auditar servidores web do Windows que não estão a usar protocolos de comunicação seguros
- Função de aplicação só deve estar acessível através de HTTPS
- Apenas ligações seguras ao seu Redis Cache devem ser ativadas
- Transferência segura para contas de armazenamento deve ser ativada
- Aplicação Web só deve estar acessível através de HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) Proteção de Informação em Repouso  Proteção Criptográfica

Esta planta ajuda-o a impor a sua política sobre a utilização de controlos criptógrafos para proteger a informação em repouso, atribuindo definições de [Política Azure](../../../policy/overview.md) que impõem controlos criptográficos específicos e uso de auditoria de configurações criptográficas fracas. Entender onde os recursos do Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar medidas corretivas para garantir que os recursos sejam configurados de acordo com sua política de segurança de informações. Especificamente, as definições de política atribuídas por esta planta requerem encriptação para contas de armazenamento de data lake; requerem encriptação transparente de dados nas bases de dados SQL; e auditar a encriptação em falta nas bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação.

- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- Implementar encriptação transparente de dados SQL DB
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Exigir encriptação nas contas da Data Lake Store
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="si-2-flaw-remediation"></a>Reparação de falhas SI-2

Esta planta ajuda-o a gerir falhas no sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam as atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. A central de segurança do Azure fornece recursos de relatório que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados. Este projeto também atribui uma definição de política que garante a correção do sistema operativo para conjuntos de escala de máquinas virtuais.

- Remendar a imagem automática do OS em conjuntos de escala de máquina virtual
- As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas nas suas máquinas virtuais
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- As vulnerabilidades na configuração de segurança das suas máquinas virtuais devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="si-3-malicious-code-protection"></a>Proteção de Código Malicioso SI-3

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo proteção de código sucucionada, atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center e aplicam a solução antimalware da Microsoft em máquinas virtuais windows.

- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) Proteção de Código Malicioso  Gestão Central

Esta planta ajuda-o a gerir a proteção de pontos finais, incluindo proteção de código sucucionada, atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção de pontos finais em máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades centralizadas de gestão e reporte que lhe permitem ter uma visão em tempo real sobre o estado de segurança dos recursos azure implantados.

- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure

## <a name="si-4-information-system-monitoring"></a>Monitorização do sistema de informação SI-4

Esta planta ajuda-o a monitorizar o seu sistema auditando e aplicando a segurança de registos e dados através dos recursos do Azure. Especificamente, as políticas atribuídas auditoria e aplicação do agente Log Analytics, e definições de segurança melhoradas para bases de dados SQL, contas de armazenamento e recursos de rede. Estas capacidades podem ajudá-lo a detetar comportamentos anómalos e indicadores de ataques para que possa tomar as medidas adequadas.

- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não cotada
- \[pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não cotada
- \[pré-visualização\]: Audit Log Analytics Workspace for VM - Report Mismatch
- \[pré-visualização\]: Implementar o agente de análise de registo para conjuntos de escala SM (VMSS)
- \[pré-visualização\]: Implementar o agente de análise de registo para VMs Linux
- \[\]de pré-visualização: Implementar o agente de análise de registo para conjuntos de escala seletivas vM (VMSS)
- \[\]de pré-visualização : Implementar o agente de análise de registo para VMs do Windows
- A segurança avançada dos dados deve ser ativada nas suas instâncias geridas
- A segurança avançada de dados deve ser ativada nos seus servidores SQL
- Implementar segurança avançada de dados em servidores SQL
- Implementar proteção avançada de ameaças em contas de armazenamento
- Implementar auditoria sql
- Implementar o observador da rede quando as redes virtuais são criadas
- Implementar deteção de ameaças em servidores SQL
- Localizações permitidas
- Locais permitidos para grupos de recursos

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>SI-4 (18) Monitorização do Sistema de Informação  Analisar tráfego / exfiltração secreta

A Proteção Avançada de Ameaças para armazenamento azure deteta tentativas incomuns e potencialmente nocivas de acesso ou exploração de contas de armazenamento. Os alertas de proteção incluem padrões de acesso anómalos, extratos/uploads anómalos e atividade de armazenamento suspeito. Estes indicadores podem ajudá-lo a detetar a exfiltração secreta de informação.

- Implementar proteção avançada de ameaças em contas de armazenamento

> [!NOTE]
> A disponibilidade de definições específicas da Política Azure pode variar no Governo de Azure e noutras nuvens nacionais. 

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo da planta do Nível de Impacto 4 do DoD, visite os seguintes artigos para saber sobre a planta e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Planta do Nível de Impacto do DoD 4 - Visão geral](./index.md)
> planta do [Nível de Impacto do DoD 4 - Desdobrar passos](./deploy.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).