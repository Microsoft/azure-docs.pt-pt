---
title: Exemplo-esquema NIST SP 800-53 R4 Blueprint – mapeamento de controle
description: Mapeamento de controle da amostra de Blueprint do NIST SP 800-53 R4 para Azure Policy.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 6d0b9202cdece44378964ee98d63a126162849fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003045"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Mapeamento de controle da amostra de Blueprint do NIST SP 800-53 R4

O artigo a seguir fornece detalhes sobre como a amostra do Blueprint do NIST SP 800-53 R4 de plantas do Azure é mapeada para os controles NIST SP 800-53 R4. Para obter mais informações sobre os controles, consulte [NIST SP 800-53](https://nvd.nist.gov/800-53).

Os seguintes mapeamentos são para os controles **NIST SP 800-53 (Rev. 4)** . Use a navegação à direita para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, localize e selecione **a\] \[visualização: Auditar controles do NIST SP 800-53 R4 e implantar extensões de VM específicas para** dar suporte à iniciativa de política interna de requisitos de auditoria.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/nist-sp-800-53-rev4/control-mapping.md).

## <a name="ac-2-account-management"></a>Gerenciamento de conta AC-2

Este projeto ajuda você a examinar as contas que podem não estar em conformidade com os requisitos de gerenciamento de conta da sua organização. Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que auditam contas externas com permissões de leitura, gravação e proprietário em uma assinatura e contas preteridas. Ao examinar as contas auditadas por essas políticas, você pode tomar as medidas apropriadas para garantir que os requisitos de gerenciamento de conta sejam atendidos.

- As contas preteridas devem ser removidas da sua assinatura
- Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de leitura devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="ac-2-7-account-management--role-based-schemes"></a>Gerenciamento de conta AC-2 (7) | Esquemas baseados em função

O Azure implementa o RBAC ( [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) ) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Esse projeto também atribui definições de [Azure Policy](../../../policy/overview.md) para auditar o uso de autenticação de Azure Active Directory para SQL Servers e Service Fabric. Usar Azure Active Directory autenticação permite o gerenciamento de permissões simplificado e o gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft. Além disso, esse plano gráfico atribui uma definição de Azure Policy para auditar o uso de regras personalizadas de RBAC. Entender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar a necessidade e a implementação adequada, pois as regras personalizadas de RBAC são propensas a erros.

- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL
- Auditar o uso de regras personalizadas de RBAC
- Clusters de Service Fabric só devem usar Azure Active Directory para autenticação de cliente

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>Gerenciamento de conta AC-2 (12) | Monitoramento de conta/uso de atípicos

O acesso à máquina virtual JIT (just-in-time) bloqueia o tráfego de entrada para as máquinas virtuais do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. Todas as solicitações JIT para acessar máquinas virtuais são registradas no log de atividades, permitindo que você monitore o uso de atípicos. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar máquinas virtuais que podem dar suporte ao acesso just-in-time, mas que ainda não foram configuradas.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="ac-4-information-flow-enforcement"></a>Imposição de fluxo de informações AC-4

O CORS (compartilhamento de recursos entre origens) pode permitir que os recursos dos serviços de aplicativos sejam solicitados de um domínio externo. A Microsoft recomenda que você permita que apenas os domínios necessários interajam com a API, a função e os aplicativos Web. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) para ajudá-lo a monitorar as restrições de acesso de recursos CORS na central de segurança do Azure.
Entender as implementações de CORS pode ajudá-lo a verificar se os controles de fluxo de informações são implementados.

- O CORS não deve permitir que todos os recursos acessem seu aplicativo Web

## <a name="ac-5-separation-of-duties"></a>Separação de tarefas AC-5

Ter apenas um proprietário de assinatura do Azure não permite redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este projeto ajuda a manter um número apropriado de proprietários de assinatura do Azure atribuindo [Azure Policy](../../../policy/overview.md) definições que auditam o número de proprietários para assinaturas do Azure. Esse projeto também atribui definições de Azure Policy que ajudam a controlar a associação do grupo de administradores em máquinas virtuais do Windows. O gerenciamento de permissões de proprietário de assinatura e administrador de máquina virtual pode ajudá-lo a implementar a separação apropriada de tarefas.

- Um máximo de 3 proprietários deve ser designado para sua assinatura
- Auditar VMs do Windows nas quais o grupo de administradores contém qualquer um dos membros especificados
- Auditar VMs do Windows nas quais o grupo de administradores não contém todos os membros especificados
- Implantar requisitos para auditar VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar requisitos para auditar VMs do Windows nas quais o grupo de administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>CA-6 (7) privilégios mínimos | Revisão de privilégios de usuário

O Azure implementa o RBAC ( [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) ) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) para auditar contas que devem ser priorizadas para análise. A revisão desses indicadores de conta pode ajudá-lo a garantir que os controles de privilégios mínimos sejam implementados.

- Um máximo de 3 proprietários deve ser designado para sua assinatura
- Auditar VMs do Windows nas quais o grupo de administradores contém qualquer um dos membros especificados
- Auditar VMs do Windows nas quais o grupo de administradores não contém todos os membros especificados
- Implantar requisitos para auditar VMs do Windows nas quais o grupo Administradores contém qualquer um dos membros especificados
- Implantar requisitos para auditar VMs do Windows nas quais o grupo de administradores não contém todos os membros especificados
- Deve haver mais de um proprietário atribuído à sua assinatura

## <a name="ac-16-security-attributes"></a>Atributos de segurança AC-16

O recurso de classificação e descoberta de dados do Advanced Data Security para o banco de dados SQL do Azure fornece recursos para descobrir, classificar, rotular e proteger os dados confidenciais em seus bancos. Pode ser utilizada para oferecer visibilidade sobre o estado de classificação da base de dados e para controlar o acesso aos dados confidenciais na base de dados e além dos respetivos limites. A segurança de dados avançada pode ajudá-lo a garantir as informações associadas aos atributos de segurança apropriados para sua organização. Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) para monitorar e impor o uso da segurança de dados avançada no SQL Server. 

- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas
- A segurança de dados avançada deve estar ativada nos seus servidores SQL
- Implantar a segurança de dados avançada em servidores SQL

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>AC-17 (1) acesso remoto | Monitoramento/controle automatizado

Este projeto ajuda você a monitorar e controlar o acesso remoto atribuindo definições de [Azure Policy](../../../policy/overview.md) para monitores que a depuração remota para o aplicativo de serviço Azure app está desativada e definições de política que auditam máquinas virtuais Linux que permitem Remote conexões de contas sem senhas. Esse projeto também atribui uma definição de Azure Policy que ajuda a monitorar o acesso irrestrito a contas de armazenamento. O monitoramento desses indicadores pode ajudá-lo a garantir que os métodos de acesso remoto estejam em conformidade com sua política de segurança.

- \[Versão\]prévia: Auditar VMs do Linux que permitem conexões remotas de contas sem senhas
- \[Versão\]prévia: Implantar requisitos para auditar VMs Linux que permitem conexões remotas de contas sem senhas
- Auditar o acesso irrestrito à rede para contas de armazenamento
- A depuração remota deve ser desativada para o aplicativo de API
- A depuração remota deve ser desativada para Aplicativo de funções
- Depuração remota deve ser desativada para a aplicação Web

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>AU-3 (2) conteúdo dos registros de auditoria | Gerenciamento centralizado de conteúdo de registro de auditoria planejado

Os dados de log coletados pelo Azure Monitor são armazenados em um espaço de trabalho Log Analytics habilitando a configuração e o gerenciamento centralizados. Este projeto ajuda a garantir que os eventos sejam registrados por meio da atribuição de definições de [Azure Policy](../../../policy/overview.md) que auditam e impõem a implantação do agente de log Analytics em máquinas virtuais do Azure.

- \[Versão\]prévia: Implantação de agente de Log Analytics de auditoria-imagem de VM (SO) não listada
- \[Versão\]prévia: Auditoria de implantação de agente de Log Analytics em VMSS-imagem de VM (SO) não listada
- \[Versão\]prévia: Log Analytics do espaço de trabalho de auditoria para VM – relatar incompatibilidade
- \[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de VM Linux (VMSS)
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows

## <a name="au-5-response-to-audit-processing-failures"></a>Resposta AU-5 para falhas de processamento de auditoria

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que monitoram as configurações de log de eventos e auditoria. O monitoramento dessas configurações pode fornecer um indicador de uma falha do sistema de auditoria ou configuração incorreta e ajudá-lo a tomar uma ação corretiva.

- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas
- A segurança de dados avançada deve estar ativada nos seus servidores SQL

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>AU-6 (4) revisão de auditoria, análise e relatórios | Revisão e análise central

Os dados de log coletados pelo Azure Monitor são armazenados em um espaço de trabalho Log Analytics habilitando a análise e relatórios centralizados. Este projeto ajuda a garantir que os eventos sejam registrados por meio da atribuição de definições de [Azure Policy](../../../policy/overview.md) que auditam e impõem a implantação do agente de log Analytics em máquinas virtuais do Azure.

- \[Versão\]prévia: Implantação de agente de Log Analytics de auditoria-imagem de VM (SO) não listada
- \[Versão\]prévia: Auditoria de implantação de agente de Log Analytics em VMSS-imagem de VM (SO) não listada
- \[Versão\]prévia: Log Analytics do espaço de trabalho de auditoria para VM – relatar incompatibilidade
- \[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de VM Linux (VMSS)
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows

## <a name="au-12-audit-generation"></a>Geração de auditoria AU-12

Este projeto ajuda a garantir que os eventos do sistema sejam registrados por meio da atribuição de definições de [Azure Policy](../../../policy/overview.md) que auditam as configurações de log nos recursos do Azure. Essas definições de política auditam e impõem a implantação do agente de Log Analytics em máquinas virtuais do Azure e a configuração de configurações de auditoria para outros tipos de recursos do Azure. Essas definições de política também auditam a configuração dos logs de diagnóstico para fornecer informações sobre as operações executadas nos recursos do Azure. Além disso, a auditoria e a segurança avançada de dados são configuradas em servidores SQL.

- \[Versão\]prévia: Implantação de agente de Log Analytics de auditoria-imagem de VM (SO) não listada
- \[Versão\]prévia: Auditoria de implantação de agente de Log Analytics em VMSS-imagem de VM (SO) não listada
- \[Versão\]prévia: Log Analytics do espaço de trabalho de auditoria para VM – relatar incompatibilidade
- \[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de VM Linux (VMSS)
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows
- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas
- A segurança de dados avançada deve estar ativada nos seus servidores SQL
- Implantar a segurança de dados avançada em servidores SQL
- Implementar a Auditoria em servidores SQL
- Implantar configurações de diagnóstico para grupos de segurança de rede

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>Funcionalidade CM-7 (2) menos | Impedir a execução do programa

O controle de aplicativo adaptável na central de segurança do Azure é uma solução de lista de permissões de aplicativos de ponta a ponta inteligente e automatizada que pode bloquear ou impedir que software específico seja executado em suas máquinas virtuais. O controle de aplicativo pode ser executado em um modo de imposição que proíbe a execução de aplicativos não aprovados. Este projeto atribui uma definição de Azure Policy que ajuda a monitorar máquinas virtuais em que uma lista de permissões de aplicativo é recomendada, mas ainda não foi configurada.

- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>Funcionalidade do CM-7 (5) menos | Software autorizado/lista de permissões

O controle de aplicativo adaptável na central de segurança do Azure é uma solução de lista de permissões de aplicativos de ponta a ponta inteligente e automatizada que pode bloquear ou impedir que software específico seja executado em suas máquinas virtuais. O controle de aplicativo ajuda a criar listas de aplicativos aprovados para suas máquinas virtuais. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar máquinas virtuais em que uma lista de permissões de aplicativo é recomendada, mas ainda não foi configurada.

- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais

## <a name="cm-11-user-installed-software"></a>Software instalado pelo usuário do CM-11

O controle de aplicativo adaptável na central de segurança do Azure é uma solução de lista de permissões de aplicativos de ponta a ponta inteligente e automatizada que pode bloquear ou impedir que software específico seja executado em suas máquinas virtuais. O controle de aplicativo pode ajudá-lo a impor e monitorar a conformidade com as políticas de restrição de software. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar máquinas virtuais em que uma lista de permissões de aplicativo é recomendada, mas ainda não foi configurada.

- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais

## <a name="cp-7-alternate-processing-site"></a>Site de processamento alternativo CP-7

Azure Site Recovery Replica as cargas de trabalho em execução em máquinas virtuais de um local primário para um local secundário. Se ocorrer uma interrupção no site primário, a carga de trabalho falhará no local secundário. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que audita as máquinas virtuais sem a recuperação de desastres configurada. O monitoramento desse indicador pode ajudá-lo a garantir que os controles de contingência necessários estejam em vigor.

- Auditar máquinas virtuais sem recuperação de desastres configuradas

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificação e autenticação IA-2 (1) (usuários organizacionais) | Acesso à rede para contas com privilégios

Este projeto ajuda a restringir e controlar o acesso privilegiado atribuindo definições de [Azure Policy](../../../policy/overview.md) a contas de auditoria com permissões de proprietário e/ou gravação que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a manter as contas seguras mesmo que uma parte das informações de autenticação seja comprometida. Ao monitorar contas sem a autenticação multifator habilitada, você pode identificar contas que podem ser mais prováveis de serem comprometidas.

- A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada em contas com permissões de gravação em sua assinatura

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificação e autenticação IA-2 (2) (usuários organizacionais) | Acesso à rede para contas sem privilégios

Este projeto ajuda a restringir e controlar o acesso atribuindo uma definição de [Azure Policy](../../../policy/overview.md) a contas de auditoria com permissões de leitura que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a manter as contas seguras mesmo que uma parte das informações de autenticação seja comprometida. Ao monitorar contas sem a autenticação multifator habilitada, você pode identificar contas que podem ser mais prováveis de serem comprometidas.

- A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura

## <a name="ia-5-authenticator-management"></a>Gerenciamento de autenticador IA-5

Este projeto atribui definições de [Azure Policy](../../../policy/overview.md) que auditam as máquinas virtuais Linux que permitem conexões remotas de contas sem senhas e/ou têm permissões incorretas definidas no arquivo passwd. Este projeto também atribui definições de política que auditam a configuração do tipo de criptografia de senha para máquinas virtuais do Windows. O monitoramento desses indicadores ajuda a garantir que os autenticadores de sistema estejam em conformidade com a política de identificação e autenticação da sua organização.

- \[Versão\]prévia: Auditar VMs Linux que não têm as permissões de arquivo passwd definidas como 0644
- \[Versão\]prévia: Auditar VMs do Linux que têm contas sem senhas
- \[Versão\]prévia: Auditar VMs do Windows que não armazenam senhas usando criptografia reversível
- \[Versão\]prévia: Implantar requisitos para auditar VMs Linux que não têm as permissões de arquivo passwd definidas como 0644
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Linux que têm contas sem senhas
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que não armazenam senhas usando criptografia reversível

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>Gerenciamento de autenticador IA-5 (1) | Autenticação baseada em senha

Este projeto ajuda a impor senhas fortes atribuindo definições de [Azure Policy](../../../policy/overview.md) que auditam as máquinas virtuais do Windows que não impõem a força mínima e outros requisitos de senha. A conscientização de máquinas virtuais em violação da política de força de senha ajuda você a tomar medidas corretivas para garantir que as senhas de todas as contas de usuário de máquina virtual estejam em conformidade com a política de senha da sua organização.

- \[Versão\]prévia: Auditar VMs do Windows que permitem reutilização das 24 senhas anteriores
- \[Versão\]prévia: Auditar VMs do Windows que não têm uma duração máxima de senha de 70 dias
- \[Versão\]prévia: Auditar VMs do Windows que não têm uma duração mínima de senha de 1 dia
- \[Versão\]prévia: Auditar VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[Versão\]prévia: Auditar VMs do Windows que não restringem o comprimento mínimo da senha para 14 caracteres
- \[Versão\]prévia: Auditar VMs do Windows que não armazenam senhas usando criptografia reversível
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que permitem o reuso das 24 senhas anteriores
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que não têm uma duração máxima de senha de 70 dias
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que não têm uma duração mínima de senha de 1 dia
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da senha para 14 caracteres
- \[Versão\]prévia: Implantar requisitos para auditar VMs do Windows que não armazenam senhas usando criptografia reversível

## <a name="ra-5-vulnerability-scanning"></a>Verificação de vulnerabilidades de RA-5

Este projeto ajuda a gerenciar as vulnerabilidades do sistema de informações atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitorem vulnerabilidades do sistema operacional, vulnerabilidades do SQL e vulnerabilidades de máquinas virtuais na central de segurança do Azure. A central de segurança do Azure fornece recursos de relatório que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados. Este projeto também atribui definições de política que auditam e impõem a segurança de dados avançada em servidores SQL. A segurança avançada de dados inclui recursos de avaliação de vulnerabilidade e proteção avançada contra ameaças para ajudá-lo a entender as vulnerabilidades em seus recursos implantados.

- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas
- A segurança de dados avançada deve estar ativada nos seus servidores SQL
- Implantar a segurança de dados avançada em servidores SQL
- Vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Vulnerabilidades na configuração de segurança em suas máquinas virtuais devem ser corrigidas
- Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas
- Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade

## <a name="sc-5-denial-of-service-protection"></a>Proteção de negação de serviço SC-5

A camada padrão de DDoS (negação de serviço distribuído) do Azure fornece recursos adicionais e recursos de mitigação na camada de serviço básica. Esses recursos adicionais incluem a integração do Azure Monitor e a capacidade de revisar relatórios de mitigação após o ataque. Essa especificação técnica atribui uma definição de [Azure Policy](../../../policy/overview.md) que audita se a camada padrão de DDoS está habilitada. Entender a diferença de capacidade entre as camadas de serviço pode ajudá-lo a selecionar a melhor solução para tratar das proteções de negação de serviço para seu ambiente do Azure.

- A proteção contra DDoS Standard deve ser habilitada

## <a name="sc-7-boundary-protection"></a>Proteção de limite SC-7

Este projeto ajuda você a gerenciar e controlar o limite do sistema atribuindo uma definição de [Azure Policy](../../../policy/overview.md) que monitora recomendações de proteção de grupo de segurança de rede na central de segurança do Azure. A central de segurança do Azure analisa os padrões de tráfego das máquinas virtuais voltadas para a Internet e fornece recomendações de regra de grupo de segurança de rede para reduzir a superfície potencial de ataque.
Além disso, esse Blueprint também atribui definições de política que monitorem pontos de extremidade, aplicativos e contas de armazenamento desprotegidos. Pontos de extremidade e aplicativos que não são protegidos por um firewall e contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- As regras do grupo de segurança de rede para máquinas virtuais voltadas para a Internet devem ser protegidas
- O acesso pelo ponto de extremidade voltado para a Internet deve ser restrito
- As regras de NSGs para aplicativos Web em IaaS devem ser protegidas
- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="sc-7-3-boundary-protection--access-points"></a>Proteção de limite SC-7 (3) | Pontos de acesso

O acesso à máquina virtual JIT (just-in-time) bloqueia o tráfego de entrada para as máquinas virtuais do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. O acesso à máquina virtual JIT ajuda você a limitar o número de conexões externas com seus recursos no Azure. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar máquinas virtuais que podem dar suporte ao acesso just-in-time, mas que ainda não foram configuradas.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Proteção de limite SC-7 (4) | Serviços de telecomunicações externos

O acesso à máquina virtual JIT (just-in-time) bloqueia o tráfego de entrada para as máquinas virtuais do Azure, reduzindo a exposição a ataques e, ao mesmo tempo, fornecendo acesso fácil para se conectar às VMs quando necessário. O acesso à máquina virtual JIT ajuda você a gerenciar exceções em sua política de fluxo de tráfego facilitando os processos de solicitação e aprovação de acesso. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) que ajuda a monitorar máquinas virtuais que podem dar suporte ao acesso just-in-time, mas que ainda não foram configuradas.

- O controle de acesso à rede just-in-time deve ser aplicado em máquinas virtuais

## <a name="sc-8-1-transmission-confidentiality-and-integrity--cryptographic-or-alternate-physical-protection"></a>Integridade e confidencialidade da transmissão do SC-8 (1) | Proteção física criptográfica ou alternativa

Este projeto ajuda a proteger a confidencialidade e a integridade das informações transmitidas atribuindo definições de [Azure Policy](../../../policy/overview.md) que ajudam a monitorar o mecanismo criptográfico implementado para protocolos de comunicação. Garantir que as comunicações sejam criptografadas corretamente pode ajudá-lo a atender aos requisitos da sua organização ou a proteger as informações contra a divulgação e a modificação não autorizadas.

- O aplicativo de API só deve ser acessível via HTTPS
- Auditar servidores Web do Windows que não estão usando protocolos de comunicação segura
- Implantar requisitos para auditar servidores Web do Windows que não usam protocolos de comunicação segura
- Função de aplicação só deve estar acessível através de HTTPS
- Somente conexões seguras para o cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Aplicação Web só deve estar acessível através de HTTPS

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>SC-28 (1) proteção de informações em repouso | Proteção criptográfica

Este projeto ajuda a reforçar a política sobre o uso de controles cryptograph para proteger as informações em repouso atribuindo definições de [Azure Policy](../../../policy/overview.md) que impõem controles cryptograph específicos e auditam o uso de configurações de criptografia fracas. Entender onde os recursos do Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar medidas corretivas para garantir que os recursos sejam configurados de acordo com sua política de segurança de informações. Especificamente, as definições de política atribuídas por este projeto exigem criptografia para contas de armazenamento do data Lake; exigir Transparent Data Encryption em bancos de dados SQL; e auditar a criptografia ausente em bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação.

- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas
- A segurança de dados avançada deve estar ativada nos seus servidores SQL
- Implantar a segurança de dados avançada em servidores SQL
- Implantar a Transparent Data Encryption do banco de dados SQL
- A criptografia de disco deve ser aplicada em máquinas virtuais
- Exigir criptografia em contas de Data Lake Store
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas

## <a name="si-2-flaw-remediation"></a>Correção de falha de SI-2

Este projeto ajuda a gerenciar falhas do sistema de informações atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitoram atualizações de sistema ausentes, vulnerabilidades de sistema operacional, vulnerabilidades de SQL e vulnerabilidades de máquina virtual na segurança do Azure Centraliza. A central de segurança do Azure fornece recursos de relatório que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados. Este projeto também atribui uma definição de política que garante a aplicação de patches do sistema operacional para conjuntos de dimensionamento de máquinas virtuais.

- Exigir aplicação automática de patch de imagem de sistema operacional em conjuntos de dimensionamento de máquinas virtuais
- As atualizações do sistema em conjuntos de dimensionamento de máquinas virtuais devem ser instaladas
- As atualizações do sistema devem ser instaladas em suas máquinas virtuais
- Vulnerabilidades na configuração de segurança em seus conjuntos de dimensionamento de máquinas virtuais devem ser corrigidas
- Vulnerabilidades na configuração de segurança em suas máquinas virtuais devem ser corrigidas
- Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas
- Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade

## <a name="si-3-malicious-code-protection"></a>Proteção de código mal-intencionado SI-3

Este projeto ajuda a gerenciar o Endpoint Protection, incluindo a proteção de código mal-intencionado, atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitorem a proteção de ponto de extremidade ausente em máquinas virtuais na central de segurança do Azure e impõem a Microsoft solução antimalware em máquinas virtuais do Windows.

- Implantar a extensão padrão do Microsoft Iaasantimalware da para Windows Server
- A solução Endpoint Protection deve ser instalada em conjuntos de dimensionamento de máquinas virtuais
- Monitorar Endpoint Protection ausentes na central de segurança do Azure

## <a name="si-3-1-malicious-code-protection--central-management"></a>SI-3 (1) proteção de código mal-intencionado | Gerenciamento central

Este projeto ajuda a gerenciar o Endpoint Protection, incluindo a proteção de código mal-intencionado, atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitorem a proteção de ponto de extremidade ausente em máquinas virtuais na central de segurança do Azure. A central de segurança do Azure fornece recursos centralizados de gerenciamento e relatórios que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados.

- A solução Endpoint Protection deve ser instalada em conjuntos de dimensionamento de máquinas virtuais
- Monitorar Endpoint Protection ausentes na central de segurança do Azure

## <a name="si-4-information-system-monitoring"></a>Monitoramento do sistema de informações do SI-4

Este projeto ajuda você a monitorar o sistema ao auditar e impor o registro em log e a segurança de dados nos recursos do Azure. Especificamente, as políticas atribuiram auditoria e impõem a implantação do agente de Log Analytics e configurações de segurança aprimoradas para bancos de dados SQL, contas de armazenamento e recursos de rede. Esses recursos podem ajudá-lo a detectar comportamento anormal e indicadores de ataques para que você possa tomar as medidas apropriadas.

- \[Versão\]prévia: Implantação de agente de Log Analytics de auditoria-imagem de VM (SO) não listada
- \[Versão\]prévia: Auditoria de implantação de agente de Log Analytics em VMSS-imagem de VM (SO) não listada
- \[Versão\]prévia: Log Analytics do espaço de trabalho de auditoria para VM – relatar incompatibilidade
- \[Versão\]prévia: Implantar o agente de Log Analytics para conjuntos de dimensionamento de VM Linux (VMSS)
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs Linux
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMSS (conjuntos de dimensionamento de VMs) do Windows
- \[Versão\]prévia: Implantar o agente de Log Analytics para VMs do Windows
- A segurança de dados avançada deve ser habilitada em suas instâncias gerenciadas
- A segurança de dados avançada deve estar ativada nos seus servidores SQL
- Implantar a segurança de dados avançada em servidores SQL
- Implantar a proteção avançada contra ameaças em contas de armazenamento
- Implementar a Auditoria em servidores SQL
- Implantar o observador de rede quando redes virtuais forem criadas
- Implantar a detecção de ameaças em servidores SQL

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Monitoramento do sistema de informações do SI-4 (18) | Analisar o tráfego/cobrir vazamento

A proteção avançada contra ameaças para o armazenamento do Azure detecta tentativas incomuns e potencialmente prejudiciais de acessar ou explorar contas de armazenamento. Os alertas de proteção incluem padrões de acesso anormal, extratos/carregamentos anormais e atividade de armazenamento suspeita. Esses indicadores podem ajudá-lo a detectar vazamento de informações.

- Implantar a proteção avançada contra ameaças em contas de armazenamento

> [!NOTE]
> A disponibilidade de definições de Azure Policy específicas pode variar no Azure governamental e em outras nuvens nacionais.

## <a name="next-steps"></a>Passos seguintes

Agora que você analisou o mapeamento de controle do especificações do NIST SP 800-53 R4, visite os artigos a seguir para saber mais sobre o plano gráfico e como implantar esse exemplo:

> [!div class="nextstepaction"]
> [NIST SP 800-53 R4 Blueprint-visão geral](./index.md)
> [NIST SP 800-53 R4 Blueprint-etapas de implantação](./deploy.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).