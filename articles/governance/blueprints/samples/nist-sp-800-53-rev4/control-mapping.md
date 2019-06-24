---
title: Mapeamento de controle - esquema de R4 SP do NIST 800-53 - de exemplo
description: Controlar o mapeamento de NIST SP 800-53 R4 esquema a amostra de política do Azure e o RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b887c4e6812d201dc83465a578f71e1742e8e9cf
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2019
ms.locfileid: "67342025"
---
# <a name="control-mapping-of-the-nist-sp-800-53-r4-blueprint-sample"></a>Controlar o mapeamento de NIST SP exemplo de plano gráfico 800-53 R4

O seguinte artigo detalha como o exemplo de esquema do Azure-SP esquemas NIST 800-53 R4 mapeia para o SP NIST 800-53 R4 controles. Para obter mais informações sobre os controles, consulte [SP do NIST 800-53](https://nvd.nist.gov/800-53).

Os seguintes mapeamentos são para o **SP do NIST 800-53 (4 de Rev)** controles. Utilize a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com um [do Azure Policy](../../../policy/overview.md) iniciativa. Para rever a iniciativa concluída, abra **diretiva** no portal do Azure e selecione o **definições** página. Em seguida, localize e selecione o  **[pré-visualização]: Auditar NIST SP 800-53 R4 controles e implementar extensões de VM específicas para suportar os requisitos de auditoria** iniciativa de política incorporada.

## <a name="ac-2-account-management"></a>Gestão de contas de AC-2

Este esquema ajuda-o a rever as contas que não podem estar em conformidade com requisitos de gestão de conta da sua organização. Este esquema atribui cinco definições de política do Azure que as contas externas com permissões de leitura, escrita e proprietário numa assinatura de auditoria e preterido contas. Ao rever as contas auditadas por estas políticas, pode tirar as medidas adequadas para garantir o cumprimento de requisitos de gestão de conta.

- [Pré-visualização]: Audit deprecated accounts on a subscription
- [Pré-visualização]: Audit deprecated accounts with owner permissions on a subscription
- [Pré-visualização]: Audit external accounts with owner permissions on a subscription
- [Pré-visualização]: Audit external accounts with read permissions on a subscription
- [Pré-visualização]: Audit external accounts with write permissions on a subscription

## <a name="ac-2-7-account-management--role-based-schemes"></a>Gestão de contas de AC-2 (7) | Esquemas de baseada em funções

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para ajudar a gerir quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema também atribui dois [do Azure Policy](../../../policy/overview.md) utilizam de definições de auditoria de autenticação do Azure Active Directory para servidores SQL e o Service Fabric. Autenticação com o Azure Active Directory permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft.

- Auditar o aprovisionamento de um administrador do Azure Active Directory para o SQL server
- Auditar a utilização do Azure Active Directory para autenticação de cliente no Service Fabric

## <a name="ac-2-12-account-management--account-monitoring--atypical-usage"></a>Gestão de contas de AC-2 (12) | Conta a utilização de monitorização / atípica

Just-in-time (JIT) máquina virtual acesso bloqueia o tráfego de entrada para máquinas virtuais do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário. Todos os pedidos JIT para aceder a máquinas virtuais são registados no registo de atividades, permitindo-lhe monitorizar utilização atípicos. Este esquema atribui uma [Azure Policy](../../../policy/overview.md) definição que o ajuda a monitorizar máquinas virtuais que pode oferecer suporte a acesso just-in-time, mas ainda não tenham sido configuradas.

- [Pré-visualização]: Monitorizar o acesso de apenas no Time (JIT) de rede possível no Centro de segurança do Azure

## <a name="ac-4-information-flow-enforcement"></a>Imposição de fluxo de informações de AC-4

Cross origin resource partilha (CORS) pode permitir que os recursos de serviços de aplicação para ser pedidos a partir de um domínio externo. A Microsoft recomenda que permite que apenas necessários domínios interagir com a API, função e aplicativos web. Este esquema atribui uma [do Azure Policy](../../../policy/overview.md) definição para ajudar a monitorizar as restrições de acesso de recursos CORS no Centro de segurança do Azure.
Noções básicas sobre implementações de CORS pode ajudar a verificar que os controlos de fluxo de informações são implementados.

- [Pré-visualização]: Audit CORS resource access restrictions for a Web Application

## <a name="ac-5-separation-of-duties"></a>5 de AC de separação de funções

Ter apenas um proprietário da subscrição do Azure não permite para redundância administrativa. Por outro lado, se ter existem demasiados proprietários de subscrição do Azure, pode aumentar o potencial de falhas de segurança por meio de uma conta de proprietário comprometido. Este esquema ajuda a manter um número adequado de proprietários de subscrições do Azure através da atribuição de dois [do Azure Policy](../../../policy/overview.md) definições de que o número de proprietários das subscrições do Azure de auditoria. Este esquema também atribui quatro definições de política do Azure que o ajudam a controlam a associação do grupo Administradores em máquinas de virtuais do Windows. Gerir permissões de administrador da máquina virtual e o proprietário da subscrição pode ajudar a implementar a separação apropriada de atribuições.

- [Pré-visualização]: Audit maximum number of owners for a subscription
- [Pré-visualização]: Audit minimum number of owners for subscription
- Exclui de auditoria que o grupo de administradores em VMs do Windows dos membros específicos
- Se o grupo de administradores em VMs do Windows inclui dos membros específicos de auditoria
- Implementar a extensão VM para que o grupo de administradores em VMs do Windows exclui dos membros específicos de auditoria
- Implementar a extensão de VM para auditar se o grupo de administradores em VMs do Windows inclui os membros especificados

## <a name="ac-6-7-least-privilege--review-of-user-privileges"></a>Privilégio mínimo de AC a 6 (7) | Revisão de privilégios de utilizador

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para ajudar a gerir quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema atribui seis [do Azure Policy](../../../policy/overview.md) definições de auditoria a contas que devem ser priorizadas para revisão. Rever estes indicadores de conta pode ajudar a garantir que são implementados controlos de privilégio mínimo.

- [Pré-visualização]: Audit maximum number of owners for a subscription
- [Pré-visualização]: Audit minimum number of owners for subscription
- Exclui de auditoria que o grupo de administradores em VMs do Windows dos membros específicos
- Se o grupo de administradores em VMs do Windows inclui dos membros específicos de auditoria
- Implementar a extensão VM para que o grupo de administradores em VMs do Windows exclui dos membros específicos de auditoria
- Implementar a extensão de VM para auditar se o grupo de administradores em VMs do Windows inclui os membros especificados

## <a name="ac-17-1-remote-access--automated-monitoring--control"></a>Acesso remoto de AC e 17 (1) | Controle / automatizada de monitorização

Este esquema ajuda-o a monitorizar e controlar acesso remoto através da atribuição de três [do Azure Policy](../../../policy/overview.md) definições para monitores de depuração para a aplicação de serviço de aplicações do Azure está desativada ou remotas e duas definições de política de auditoria Linux máquinas virtuais que permitir ligações remotas a partir de contas sem palavras-passe. Este esquema também atribui uma definição de política do Azure ajuda-o a monitorizar o acesso sem restrições para as contas de armazenamento. Estes indicadores de monitorização pode ajudar a garantir que os métodos de acesso remoto está em conformidade com a sua política de segurança.

- [Pré-visualização]: Audit remote debugging state for a Function App
- [Pré-visualização]: Audit remote debugging state for a Web Application
- [Pré-visualização]: Audit remote debugging state for an API App
- [Pré-visualização]: Audit that Linux VMs do not allow remote connections from accounts without passwords
- [Pré-visualização]: Deploy VM extension to audit that Linux VMs do not allow remote connections from accounts without passwords
- Auditar o acesso de rede sem restrições a contas de armazenamento

## <a name="au-3-2-content-of-audit-records--centralized-management-of-planned-audit-record-content"></a>Conteúdo de AU-3 (2) de registos de auditoria | Gerenciamento centralizado de conteúdo de registo de auditoria planeada

Dados de registo recolhidos pelo Azure Monitor são armazenados numa área de trabalho do Log Analytics ativar configuração centralizada e gestão. Este esquema ajuda-o a garantir que os eventos são registados através da atribuição de sete [do Azure Policy](../../../policy/overview.md) definições de auditoria e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure.

- [Pré-visualização]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- [Pré-visualização]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- [Pré-visualização]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VMs
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-5-response-to-audit-processing-failures"></a>Resposta de AU a 5 para fazer a auditoria de falhas de processamento

Este esquema atribui cinco [do Azure Policy](../../../policy/overview.md) definições a que monitorizam a auditoria e configurações de registo de eventos. Estas configurações de monitorização pode fornecer um indicador de uma falha de sistema de auditoria ou configuração incorreta e ajudar a tomar medidas corretivas.

- [Pré-visualização]: Monitor unaudited SQL servers in Azure Security Center
- Definição de diagnóstico de auditoria
- Instâncias SQL gerida de auditoria sem segurança de dados avançada
- Definições de auditoria ao nível do SQL server de auditoria
- Servidores SQL de auditoria sem segurança de dados avançada

## <a name="au-6-4-audit-review-analysis-and-reporting--central-review-and-analysis"></a>Revisão de auditoria de AU-6 (4), análises e relatórios | Revisão central e análise

Dados de registo recolhidos pelo Azure Monitor são armazenados na área de trabalho do Log Analytics ativar a geração de relatórios centralizada e análise. Este esquema ajuda-o a garantir que os eventos são registados através da atribuição de sete [do Azure Policy](../../../policy/overview.md) definições de auditoria e impõem a implantação do agente do Log Analytics em máquinas virtuais do Azure.

- [Pré-visualização]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- [Pré-visualização]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- [Pré-visualização]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VMs
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VMs

## <a name="au-12-audit-generation"></a>Geração de auditoria de 12 de AU

Este esquema ajuda-o a garantir que os eventos de sistema são registados através da atribuição de 15 [do Azure Policy](../../../policy/overview.md) definições de auditoria definições de registo nos recursos do Azure. Estas definições de política de auditoria e impõem a implementação do agente do Log Analytics em máquinas virtuais do Azure e a configuração das definições de auditoria para outros tipos de recursos do Azure. Estas definições de política também fazer auditoria da configuração dos registos de diagnóstico para fornecer informações sobre operações que são realizadas nos recursos do Azure. Além disso, auditoria e segurança de dados avançada estão configurados em servidores SQL.

- [Pré-visualização]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- [Pré-visualização]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- [Pré-visualização]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VMs
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VMs
- [Pré-visualização]: Monitor unaudited SQL servers in Azure Security Center
- Aplicar definições de diagnóstico para grupos de segurança de rede
- Definição de diagnóstico de auditoria
- Instâncias SQL gerida de auditoria sem segurança de dados avançada
- Definições de auditoria ao nível do SQL server de auditoria
- Servidores SQL de auditoria sem segurança de dados avançada
- Implementar a segurança avançada de dados em servidores SQL
- Implementar a auditoria em servidores SQL

## <a name="cm-7-2-least-functionality--prevent-program-execution"></a>Funcionalidade, pelo menos, 7 de CM (2) | Impedir a execução do programa

Controlo de aplicações adaptativos no Centro de segurança do Azure é uma solução de listas de permissões de aplicações de ponto-a-ponto inteligente e automatizado que pode bloquear ou impedir a execução nas suas máquinas virtuais de software específico. Controlo de aplicações pode ser executado num modo de imposição que proíbe o aplicativo não aprovados a execução. Este esquema atribui uma política do Azure a definição de que o ajuda a monitorizar máquinas virtuais em que uma lista de permissões de aplicação é recomendada, mas ainda não tiver sido configurada.

- [Pré-visualização]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-7-5-least-functionality--authorized-software--whitelisting"></a>Funcionalidade, pelo menos, 7 de CM (5) | Autorizado Software / lista de permissões

Controlo de aplicações adaptativos no Centro de segurança do Azure é uma solução de listas de permissões de aplicações de ponto-a-ponto inteligente e automatizado que pode bloquear ou impedir a execução nas suas máquinas virtuais de software específico. Controlo de aplicações ajuda-o a criar listas de aplicação aprovada para as suas máquinas virtuais. Este esquema atribui uma [do Azure Policy](../../../policy/overview.md) definição que o ajuda a monitorizar máquinas virtuais em que uma lista de permissões de aplicação é recomendada, mas ainda não tiver sido configurada.

- [Pré-visualização]: Monitor possible app whitelisting in Azure Security Center

## <a name="cm-11-user-installed-software"></a>Software instaladas por utilizadores do CM-11

Controlo de aplicações adaptativos no Centro de segurança do Azure é uma solução de listas de permissões de aplicações de ponto-a-ponto inteligente e automatizado que pode bloquear ou impedir a execução nas suas máquinas virtuais de software específico. Controlo de aplicações pode ajudá-lo a aplicar e monitorizar a conformidade com as políticas de restrição de software. Este esquema atribui uma [do Azure Policy](../../../policy/overview.md) definição que o ajuda a monitorizar máquinas virtuais em que uma lista de permissões de aplicação é recomendada, mas ainda não tiver sido configurada.

- [Pré-visualização]: Monitor possible app whitelisting in Azure Security Center

## <a name="cp-7-alternate-processing-site"></a>Site de processamento de alternativas de CP de 7

O Azure Site Recovery replica cargas de trabalho em execução em máquinas virtuais de uma localização primária para uma localização secundária. Se ocorrer uma falha no site primário, a carga de trabalho faz a ativação pós-falha da localização secundária. Este esquema atribui uma [do Azure Policy](../../../policy/overview.md) definição que Audita a máquinas virtuais sem recuperação após desastre configurada. Este indicador de monitorização pode ajudar a garantir que os controlos de contingência necessários estão em vigor.

- Máquinas de virtuais de auditoria sem recuperação após desastre configurado

## <a name="ia-2-1-identification-and-authentication-organizational-users--network-access-to-privileged-accounts"></a>Identificação de IA-2 (1) e autenticação (utilizadores organizacionais) | Acesso à rede para contas com privilégios

Este esquema ajuda-o a restringir e controlar o acesso privilegiado atribuindo dois [do Azure Policy](../../../policy/overview.md) definições para contas de auditoria com o proprietário e/ou permissões que não têm a autenticação multifator ativada de escrita. Multi-factor authentication ajuda a proteger contas, mesmo que uma parte das informações de autenticação for comprometida. Ao monitorizar contas sem autenticação multifator ativada, pode identificar as contas que podem ser mais probabilidade de ser comprometido.

- [Pré-visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Pré-visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription

## <a name="ia-2-2-identification-and-authentication-organizational-users--network-access-to-non-privileged-accounts"></a>Identificação de IA-2 (2) e autenticação (utilizadores organizacionais) | Acesso à rede para contas sem privilégios

Este esquema ajuda-o a restringir e controlar o acesso ao atribuir um [do Azure Policy](../../../policy/overview.md) ler de definição de auditoria a contas com permissões que não têm a autenticação multifator ativada. Multi-factor authentication ajuda a proteger contas, mesmo que uma parte das informações de autenticação for comprometida. Ao monitorizar contas sem autenticação multifator ativada, pode identificar as contas que podem ser mais probabilidade de ser comprometido.

- [Pré-visualização]: Audit accounts with read permissions who are not MFA enabled on a subscription

## <a name="ia-5-authenticator-management"></a>Gestão de IA-5 Authenticator

Este esquema atribui cinco [do Azure Policy](../../../policy/overview.md) definições de que as máquinas de virtuais do Linux que permitir ligações remotas a partir de contas sem palavras-passe e/ou ter permissões incorretas de auditoria definido no ficheiro passwd. Este esquema também atribui uma definição de política que audita conjugation do tipo de encriptação de palavra-passe para as máquinas virtuais do Windows. Monitorização estes ajuda de indicadores, que certifique-se de que estão em conformidade com a diretiva de identificação e autenticação da sua organização autenticadores de sistema.

- [Pré-visualização]: Audit that Linux VMs do not have accounts without passwords
- [Pré-visualização]: Deploy VM extension to audit that Linux VMs do not have accounts without passwords
- [Pré-visualização]: Audit that Linux VMs have the passwd file permissions set to 0644
- [Pré-visualização]: Audit that Windows VMs store passwords using reversible encryption
- [Pré-visualização]: Deploy VM extension to audit that Linux VMs have the passwd file permissions set to 0644

## <a name="ia-5-1-authenticator-management--password-based-authentication"></a>Gestão de autenticação de IA-5 (1) | Autenticação baseada em palavra-passe

Este esquema ajuda-o a impor senhas fortes atribuindo 12 [do Azure Policy](../../../policy/overview.md) definições de auditoria máquinas de virtuais do Windows que não impõem força mínima e outros requisitos de palavra-passe. Deteção de máquinas virtuais em violação da política de força de palavra-passe ajuda-o a efetuar ações corretivas para garantir que as palavras-passe para todas as contas de utilizador de máquina virtual está em conformidade com a política de palavra-passe da sua organização.

- [Pré-visualização]: Audit that Windows VMs cannot re-use the previous 24 passwords
- [Pré-visualização]: Audit that Windows VMs have a maximum password age of 70 days
- [Pré-visualização]: Audit that Windows VMs have a minimum password age of 1 day
- [Pré-visualização]: Audit that Windows VMs have the password complexity setting enabled
- [Pré-visualização]: Audit that Windows VMs restrict the minimum password length to 14 characters
- [Pré-visualização]: Audit that Windows VMs store passwords using reversible encryption
- [Pré-visualização]: Deploy VM extension to audit that Windows VMs cannot re-use the previous 24 passwords
- [Pré-visualização]: Deploy VM extension to audit that Windows VMs have a maximum password age of 70 days
- [Pré-visualização]: Deploy VM extension to audit that Windows VMs have a minimum password age of 1 day
- [Pré-visualização]: Deploy VM extension to audit that Windows VMs have the password complexity setting enabled
- [Pré-visualização]: Deploy VM extension to audit that Windows VMs restrict the minimum password length to 14 characters
- [Pré-visualização]: Deploy VM extension to audit that Windows VMs store passwords using reversible encryption

## <a name="ra-5-vulnerability-scanning"></a>Análise de vulnerabilidades de RA-5

Este esquema ajuda a gerenciar vulnerabilidades do sistema de informações através da atribuição de quatro [do Azure Policy](../../../policy/overview.md) definições que monitorizar vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquina virtual no Azure Centro de segurança. Centro de segurança do Azure fornece capacidades de relatórios que permitem que tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implementados. Este esquema também atribui três definições de política de auditoria e impõem segurança avançada de dados em servidores SQL. Segurança avançada de dados incluídas avaliação de vulnerabilidades e capacidades de proteção avançada contra ameaças para ajudar a compreender as vulnerabilidades em seus recursos implementados.

- Instâncias SQL gerida de auditoria sem segurança de dados avançada
- Servidores SQL de auditoria sem segurança de dados avançada
- Implementar a segurança avançada de dados em servidores SQL
- [Pré-visualização]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [Pré-visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Pré-visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Pré-visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="sc-5-denial-of-service-protection"></a>5 de SC negação de serviço de proteção

De distributed denial do Azure of escalão standard do serviço (DDoS) fornece recursos adicionais e capacidades de atenuação a camada de serviço básico. As seguintes funcionalidades adicionais incluem a integração do Azure Monitor e a capacidade de rever os relatórios de mitigação de pós ataques de. Este esquema atribui uma [do Azure Policy](../../../policy/overview.md) definição que Audita o escalão standard do DDoS está ativado. Compreender a diferença de capacidade entre os escalões de serviço pode ajudá-lo a selecionar a melhor solução a negação de endereço de proteções de serviço para o seu ambiente do Azure.

- [Pré-visualização]: Audit standard tier of DDoS protection is enabled for a virtual network

## <a name="sc-7-boundary-protection"></a>Proteção de limite de 7 de SC

Esse plano gráfico ajuda a gerenciar e controlar o limite de sistema ao atribuir um [do Azure Policy](../../../policy/overview.md) definição que monitoriza os grupos de segurança de rede com regras permissivas. As regras que são demasiado permissivas podem permitir o acesso à rede não-intencionais e devem ser revistas. Este esquema também atribui uma definição de política que monitoriza para recomendações da proteção de grupo de segurança de rede no Centro de segurança do Azure. Centro de segurança do Azure analisa os padrões de tráfego de máquinas virtuais de acesso à Internet e fornece recomendações de regra de grupo para reduzir a superfície de ataque potencial de segurança de rede.
Além disso, este esquema também atribui três definições de política que monitorizam os pontos finais desprotegidos, aplicativos e contas de armazenamento. Pontos finais e aplicações que não estão protegidas por uma firewall e as contas de armazenamento com acesso sem restrições, podem permitir não-intencionais acesso às informações contidas dentro do sistema de informações.

- [Pré-visualização]: Monitor Internet-facing virtual machines for Network Security Group traffic hardening recommendations
- [Pré-visualização]: Monitor permissive network access in Azure Security Center
- [Pré-visualização]: Monitor unprotected network endpoints in Azure Security Center
- [Pré-visualização]: Monitor unprotected web application in Azure Security Center
- Auditar o acesso de rede sem restrições a contas de armazenamento

## <a name="sc-7-3-boundary-protection--access-points"></a>Proteção de limite de SC-7 (3) | Pontos de acesso

Just-in-time (JIT) máquina virtual acesso bloqueia o tráfego de entrada para máquinas virtuais do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário. Acesso à máquina virtual JIT ajuda a limitar o número de ligações externas aos seus recursos no Azure. Este esquema atribui uma [Azure Policy](../../../policy/overview.md) definição que o ajuda a monitorizar máquinas virtuais que pode oferecer suporte a acesso just-in-time, mas ainda não tenham sido configuradas.

- [Pré-visualização]: Monitorizar o acesso de apenas no Time (JIT) de rede possível no Centro de segurança do Azure

## <a name="sc-7-4-boundary-protection--external-telecommunications-services"></a>Proteção de limite de SC-7 (4) | Serviços de telecomunicações externo

Just-in-time (JIT) máquina virtual acesso bloqueia o tráfego de entrada para máquinas virtuais do Azure, reduzindo a exposição a ataques, fornecendo acesso fácil para ligar a VMs quando necessário. Acesso à máquina virtual JIT ajuda a gerenciar exceções na sua política de fluxo de tráfego ao facilitar os processos de solicitação e aprovação de acesso. Este esquema atribui uma [Azure Policy](../../../policy/overview.md) definição que o ajuda a monitorizar máquinas virtuais que pode oferecer suporte a acesso just-in-time, mas ainda não tenham sido configuradas.

- [Pré-visualização]: Monitorizar o acesso de apenas no Time (JIT) de rede possível no Centro de segurança do Azure

## <a name="sc-28-1-protection-of-information-at-rest--cryptographic-protection"></a>28 de SC (1) de proteção de informações em repouso | Proteção criptográfica

Este esquema ajuda-o a impor a política no uso de controles de cryptograph para proteger as informações em repouso, atribuindo 9 [do Azure Policy](../../../policy/overview.md) definições que impõem controles cryptograph específico e auditoria usam weak criptográfico definições. Noções básicas sobre onde os recursos do Azure podem ter configurações de criptografia não ideal pode ajudar a efetuar ações corretivas para garantir que recursos estão configurados de acordo com a política de segurança de informações. Especificamente, as definições de política atribuídas por esse plano gráfico exigem a encriptação para contas de armazenamento do data lake; Exigir encriptação de dados transparente nas bases de dados do SQL; e auditar encriptação em falta em bases de dados SQL, discos de máquinas virtuais e as variáveis de conta de automatização.

- [Pré-visualização]: Monitor unencrypted SQL databases in Azure Security Center
- [Pré-visualização]: Monitor unencrypted VM Disks in Azure Security Center
- Transferência segura de auditoria a contas de armazenamento
- Instâncias SQL gerida de auditoria sem segurança de dados avançada
- Servidores SQL de auditoria sem segurança de dados avançada
- Auditar o estado de encriptação de dados transparente
- Implementar a segurança avançada de dados em servidores SQL
- Implementar a encriptação de dados transparente de BD SQL
- Impor a encriptação em contas do Data Lake Store

## <a name="si-2-flaw-remediation"></a>Remediação de falha de 2 de SI

Este esquema ajuda a gerenciar a falhas de sistema de informações através da atribuição de seis [do Azure Policy](../../../policy/overview.md) definições que monitorar em falta atualizações do sistema, vulnerabilidades do sistema operativo, vulnerabilidades SQL e máquinas virtuais vulnerabilidades no Centro de segurança do Azure. Centro de segurança do Azure fornece capacidades de relatórios que permitem que tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implementados. Este esquema também atribui uma definição de política que garante que a atualização automática do sistema operativo para conjuntos de dimensionamento de máquina virtual.

- [Pré-visualização]: Audit any missing system updates on virtual machine scale sets in Azure Security Center
- [Pré-visualização]: Audit OS vulnerabilities on your virtual machine scale sets in Azure Security Center
- [Pré-visualização]: Monitor missing system updates in Azure Security Center
- [Pré-visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Pré-visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Pré-visualização]: Monitor VM Vulnerabilities in Azure Security Center
- Impor a atualização automática de SO com verificações de estado de funcionamento de aplicações no VMSS

## <a name="si-3-malicious-code-protection"></a>Proteção de código malicioso de 3 de SI

Este esquema ajuda-o a gerir o endpoint protection, incluindo a proteção do código mal-intencionado, atribuindo três [do Azure Policy](../../../policy/overview.md) definições que monitorar em falta a proteção de ponto final em máquinas virtuais no Centro de segurança do Azure e impor a solução de antimalware da Microsoft em máquinas de virtuais do Windows.

- [Pré-visualização]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [Pré-visualização]: Monitor missing Endpoint Protection in Azure Security Center
- Implementar extensão Microsoft IaaSAntimalware predefinida para o Windows Server

## <a name="si-3-1-malicious-code-protection--central-management"></a>Proteção de código malicioso is-3 (1) | Gerenciamento central

Este esquema ajuda-o a gerir o endpoint protection, incluindo a proteção do código mal-intencionado, atribuindo dois [do Azure Policy](../../../policy/overview.md) definições que monitorar em falta a proteção de ponto final em máquinas virtuais no Centro de segurança do Azure. Centro de segurança do Azure Fornece gerenciamento centralizado e capacidades de relatórios que permitem que tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implementados.

- [Pré-visualização]: Audit the endpoint protection solution on virtual machine scale sets in Azure Security Center
- [Pré-visualização]: Monitor missing Endpoint Protection in Azure Security Center

## <a name="si-4-information-system-monitoring"></a>Sistema de SI 4 informações de monitorização

Este esquema ajuda-o a monitorizar o seu sistema, auditoria e imposição de segurança de dados e de registo de todos os recursos do Azure. Especificamente, as políticas atribuídas de auditoria e impõem a implantação do agente do Log Analytics e as definições de segurança avançada para bases de dados SQL, contas de armazenamento e recursos de rede. Estas capacidades podem ajudar a detetar comportamentos anómalos e indicadores de ataques para que possa tirar as medidas adequadas.

- [Pré-visualização]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- [Pré-visualização]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- [Pré-visualização]: Audit Log Analytics Workspace for VM - Report Mismatch
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Linux VMs
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VM Scale Sets (VMSS)
- [Pré-visualização]: Deploy Log Analytics Agent for Windows VMs
- Instâncias SQL gerida de auditoria sem segurança de dados avançada
- Servidores SQL de auditoria sem segurança de dados avançada
- Implementar a segurança avançada de dados em servidores SQL
- Implementar a proteção avançada contra ameaças em contas de armazenamento
- Implementar a auditoria em servidores SQL
- Implementar o observador de rede quando forem criadas redes virtuais
- Implementar a deteção de ameaças em servidores SQL

## <a name="si-4-18-information-system-monitoring--analyze-traffic--covert-exfiltration"></a>Sistema de informação de SI-4 (18) monitorização | Analisar o tráfego / converter filtragem

Proteção avançada contra ameaças do armazenamento do Azure Deteta tentativas invulgares e potencialmente prejudiciais de acesso ou exploração de contas de armazenamento. Alertas de proteção incluem padrões de acesso anómalos, extrai/carregamentos anómalos e atividade suspeita de armazenamento. Estes indicadores podem ajudá-lo a detetar converter filtragem de informações.

- Implementar a proteção avançada contra ameaças em contas de armazenamento

## <a name="next-steps"></a>Passos Seguintes

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).