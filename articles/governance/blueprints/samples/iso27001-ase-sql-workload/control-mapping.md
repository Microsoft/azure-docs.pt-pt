---
title: Exemplo - ISO 27001 esquema - mapeamento de controlo
description: Mapeamento de controle do exemplo de plano gráfico ISO 27001.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d487ce81babc2d1d6a35e3bdb1c13e1a24f8d1ca
ms.sourcegitcommit: 4133f375862fdbdec07b70de047d70c66ac29d50
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/15/2019
ms.locfileid: "58014193"
---
# <a name="control-mapping-of-the-azure-blueprints-iso-27001-blueprint-sample"></a>Mapeamento de controle do exemplo de esquema do Azure esquemas ISO 27001

O seguinte artigo detalha como o exemplo de plano gráfico de carga de trabalho do Azure esquemas ISO 27001 ASE/SQL é mapeada para os controlos da ISO 27001. Para obter mais informações sobre os controles, consulte [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos são para o **ISO 27001:2013** controles. Utilize a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com um [do Azure Policy](../../../policy/overview.md) iniciativa. Para rever a iniciativa concluída, abra **diretiva** no portal do Azure e selecione o **definições** página. Em seguida, localize e selecione o  **[pré-visualização] auditoria ISO 27001:2013 controla e implementar extensões de VM específicas para suportar os requisitos de auditoria** iniciativa de política incorporada.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 segregação de funções

Ter apenas um proprietário da subscrição do Azure não permite para redundância administrativa. Por outro lado, se ter existem demasiados proprietários de subscrição do Azure, pode aumentar o potencial de falhas de segurança por meio de uma conta de proprietário comprometido. Este esquema ajuda a manter um número adequado de proprietários de subscrições do Azure através da atribuição de dois [do Azure Policy](../../../policy/overview.md) definições de que o número de proprietários das subscrições do Azure de auditoria. Gerir permissões de proprietário da subscrição pode ajudar a implementar a separação apropriada de atribuições.

- [Pré-visualização]: Audit minimum number of owners for subscription
- [Pré-visualização]: Audit maximum number of owners for a subscription

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 acesso às redes e serviços de rede

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para gerir quem tem acesso aos recursos do Azure. Este esquema ajuda-o a controlar o acesso aos recursos do Azure através da atribuição de sete [do Azure Policy](../../../policy/overview.md) definições. Estas políticas auditar a utilização de tipos de recursos e configurações que podem permitir mais permissivas acesso a recursos.
Recursos de compreensão que cumpram estes podem políticas ajuda tome medidas corretivas para garantir um acesso a recursos do Azure é restrita para os utilizadores autorizados.

- [Pré-visualização]: Deploy VM extension to audit Linux VM accounts with no passwords
- [Pré-visualização]: Deploy VM extension to audit Linux VM allowing remote connections from accounts with no
  Palavras-passes
- [Pré-visualização]: Audit Linux VM accounts with no passwords
- [Pré-visualização]: Audit Linux VM allowing remote connections from accounts with no passwords
- Auditar utilização das contas de armazenamento clássicas
- Auditar a utilização das máquinas virtuais clássicas
- Auditar VMs que não utilizem discos geridos

## <a name="a922-user-access-provisioning"></a>Aprovisionamento de acesso de utilizadores do A.9.2.2

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para gerir quem tem acesso aos recursos do Azure. Este esquema atribui três [Azure Policy](../../../policy/overview.md) utilizam definições de auditoria [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) autenticação para servidores do SQL Server e [Service Fabric](../../../../service-fabric/service-fabric-overview.md). Autenticação com o Azure Active Directory permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft. Este esquema também atribui uma definição de política do Azure para auditar a utilização de regras personalizadas do RBAC. Compreender onde regras personalizadas de RBAC são implementar pode ajudar a verificar necessidade e a implementação adequada, como regras personalizadas de RBAC são suscetíveis a erros.

- Auditoria de aprovisionamento de um administrador do Azure Active Directory para o SQL server
- Auditoria de utilização do Azure Active Directory para autenticação de clientes no Service Fabric
- Auditar utilização das regras RBAC personalizadas

## <a name="a923-management-of-privileged-access-rights"></a>Gestão A.9.2.3 de privilégio direitos de acesso

Este esquema ajuda-o a restringir e controlar os direitos de acesso privilegiado através da atribuição de quatro [do Azure Policy](../../../policy/overview.md) definições para auditar as contas externas com o proprietário e/ou contas e permissões de escrita com proprietário e/ou permissões de escrita que não têm a autenticação multifator ativada.

- [Pré-visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Pré-visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Pré-visualização]: Audit external accounts with owner permissions on a subscription
- [Pré-visualização]: Audit external accounts with write permissions on a subscription

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Gestão de A.9.2.4 de informações secretas autenticação de utilizadores

Este esquema atribui três [do Azure Policy](../../../policy/overview.md) definições de auditoria a contas que não têm a autenticação multifator ativada. Multi-factor authentication ajuda a proteger contas, mesmo que uma parte das informações de autenticação for comprometida. Ao monitorizar contas sem autenticação multifator ativada, pode identificar as contas que podem ser mais probabilidade de ser comprometido. Este esquema também atribui duas definições de política do Azure VM do Linux de auditoria permissões de arquivo de palavra-passe para o alertar se eles estão definidos incorretamente. Esta configuração permite-lhe tomar medidas corretivas para se certificar de autenticadores não são comprometidos.

- [Pré-visualização]: Audit accounts with owner permissions who are not MFA enabled on a subscription
- [Pré-visualização]: Audit accounts with read permissions who are not MFA enabled on a subscription
- [Pré-visualização]: Audit accounts with write permissions who are not MFA enabled on a subscription
- [Pré-visualização]: Deploy VM extension to audit Linux VM passwd file permissions
- [Pré-visualização]: Audit Linux VM /etc/passwd file permissions are set to 0644

## <a name="a925-review-of-user-access-rights"></a>Revisão A.9.2.5 dos direitos de acesso de utilizador

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para ajuda a gerir quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema atribui quatro [do Azure Policy](../../../policy/overview.md) definições de auditoria a contas que devem ser priorizadas para revisão, incluindo contas depreciadas e as contas externas com permissões elevadas.

- [Pré-visualização]: Audit deprecated accounts on a subscription
- [Pré-visualização]: Audit deprecated accounts with owner permissions on a subscription
- [Pré-visualização]: Audit external accounts with owner permissions on a subscription
- [Pré-visualização]: Audit external accounts with write permissions on a subscription

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Remoção de A.9.2.6 ou ajuste dos direitos de acesso

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para ajuda a gerir quem tem acesso a recursos no Azure. Usando [do Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e RBAC, pode atualizar funções de utilizador para refletir alterações organizacionais. Quando necessário, contas podem ser impedidas de iniciar sessão (ou removidas), que remove imediatamente direitos de acesso aos recursos do Azure. Este esquema atribui dois [do Azure Policy](../../../policy/overview.md) definições de conta depreciada que deve ser considerada para remoção de auditoria.

- [Pré-visualização]: Audit deprecated accounts on a subscription
- [Pré-visualização]: Audit deprecated accounts with owner permissions on a subscription

## <a name="a943-password-management-system"></a>Sistema de gestão de palavra-passe A.9.4.3

Este esquema ajuda-o a impor senhas fortes atribuindo 10 [do Azure Policy](../../../policy/overview.md) definições de que as VMs do Windows que não impõem força mínima e outros requisitos de palavra-passe de auditoria. Deteção de VMs em violação da política de força de palavra-passe ajuda-o a efetuar ações corretivas para garantir que as palavras-passe para todas as contas de utilizador VM estão em conformidade com a política.

- [Pré-visualização]: Deploy VM extension to audit Windows VM enforces password complexity requirements
- [Pré-visualização]: Deploy VM extension to audit Windows VM maximum password age 70 days
- [Pré-visualização]: Deploy VM extension to audit Windows VM minimum password age 1 day
- [Pré-visualização]: Deploy VM extension to audit Windows VM passwords must be at least 14 characters
- [Pré-visualização]: Deploy VM extension to audit Windows VM should not allow previous 24 passwords
- [Pré-visualização]: Audit Windows VM enforces password complexity requirements
- [Pré-visualização]: Audit Windows VM maximum password age 70 days
- [Pré-visualização]: Audit Windows VM minimum password age 1 day
- [Pré-visualização]: Audit Windows VM passwords must be at least 14 characters
- [Pré-visualização]: Audit Windows VM should not allow previous 24 passwords

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Política A.10.1.1 sobre a utilização de controlos criptográficos

Este esquema ajuda-o a impor a política no uso de controles de cryptograph atribuindo 13 [do Azure Policy](../../../policy/overview.md) utilizam de definições que impõem controles cryptograph específico e de auditoria das definições de criptografia fracas.
Noções básicas sobre onde os recursos do Azure podem ter configurações de criptografia não ideal pode ajudar a efetuar ações corretivas para garantir que recursos estão configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse plano gráfico exigirem a encriptação para as contas de armazenamento de BLOBs e contas de armazenamento do data lake; Exigir encriptação de dados transparente nas bases de dados do SQL; Auditar encriptação em falta em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automatização; Auditar inseguras ligações para as contas de armazenamento, aplicações de funções, aplicação Web, aplicações API e a Cache de Redis; Auditar encriptação de palavra-passe da máquina de virtual fraco; e auditar a comunicação desencriptada do Service Fabric.

- [Pré-visualização]: Audit HTTPS only access for a Function App
- [Pré-visualização]: Audit HTTPS only access for a Web Application
- [Pré-visualização]: Audit HTTPS only access for an API App
- [Pré-visualização]: Audit missing blob encryption for storage accounts
- [Pré-visualização]: Deploy VM extension to audit Windows VM should not store passwords using reversible
  Encriptaçãon
- [Pré-visualização]: Audit Windows VM should not store passwords using reversible encryption
- [Pré-visualização]: Monitor unencrypted SQL database in Azure Security Center
- [Pré-visualização]: Monitor unencrypted VM Disks in Azure Security Center
- Auditoria de ativação da encriptação de variáveis de conta de Automatização
- Auditoria de ativação apenas de ligações seguras à Cache de Redis
- Auditoria de transferência segura para contas de armazenamento
- Auditar a definição da propriedade ClusterProtectionLevel para EncryptAndSign no Service Fabric
- Auditar o estado de encriptação de dados transparente

## <a name="a1241-event-logging"></a>Registo de eventos A.12.4.1

Este esquema ajuda-o a garantir que os eventos de sistema são registados através da atribuição de sete [do Azure Policy](../../../policy/overview.md) definições que auditoria definições em recursos do Azure de registo. Uma política atribuída audita também se as máquinas virtuais não estão a enviar registos para uma área de trabalho de análise de registo especificado.

- [Pré-visualização]: Implementação de agente de dependência de auditoria - imagem de VM (SO) não listados
- [Pré-visualização]: Auditar a implementação de agente de dependência no VMSS - imagem de VM (SO) não listados
- [Pré-visualização]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- [Pré-visualização]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- [Pré-visualização]: Monitor unaudited SQL database in Azure Security Center
- Definição de diagnóstico de auditoria
- Auditar as Definições de auditoria ao nível do servidor SQL

## <a name="a121-management-of-technical-vulnerabilities"></a>Gestão de A.12.1 de vulnerabilidades técnicas

Este esquema ajuda a gerenciar vulnerabilidades do sistema de informações através da atribuição de cinco [do Azure Policy](../../../policy/overview.md) definições que monitorar em falta atualizações do sistema, vulnerabilidades do sistema operativo, vulnerabilidades SQL e máquinas virtuais vulnerabilidades. Estas informações fornecem informações em tempo real sobre o estado de segurança dos seus recursos implementados e podem ajudá-lo a priorizar ações de remediação.

- [Pré-visualização]: Monitor missing Endpoint Protection in Azure Security Center
- [Pré-visualização]: Monitor missing system updates in Azure Security Center
- [Pré-visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Pré-visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Pré-visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="a1262-restrictions-on-software-installation"></a>Restrições de A.12.6.2 numa instalação de software

Controlo de aplicação adaptável é a solução do Centro de segurança do Azure que ajuda a controlar quais aplicativos pode ser executadas nas suas VMs localizadas no Azure. Este esquema atribui uma definição de política do Azure que monitoriza alterações para o conjunto de aplicações permitidas. Restrições de instalação de software podem ajudar a reduzir a probabilidade de introdução de vulnerabilidades de software.

- [Pré-visualização]: Monitor possible app Whitelisting in Azure Security Center

## <a name="a1311-network-controls"></a>Controlos de rede A.13.1.1

Este esquema ajuda-o a gerir e controlar redes atribuindo um [do Azure Policy](../../../policy/overview.md) definição que monitoriza os grupos de segurança de rede com regras permissivas. As regras que são demasiado permissivas podem permitir o acesso à rede não-intencionais e devem ser revistas.

- [Pré-visualização]: Monitor permissive network access in Azure Security Center

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procedimentos e políticas de transferência de informações de A.13.2.1

O plano gráfico ajuda a garantir a transferência de informações com serviços do Azure é segura atribuindo dois [do Azure Policy](../../../policy/overview.md) definições de auditoria inseguras ligações para as contas de armazenamento e a Cache de Redis.

- Auditoria de ativação apenas de ligações seguras à Cache de Redis
- Auditoria de transferência segura para contas de armazenamento

## <a name="a1413-protecting-application-services-transactions"></a>Transações de serviços de A.14.1.3 proteger aplicação

Este esquema ajuda a proteger os recursos de sistema de informações através da atribuição de três [do Azure Policy](../../../policy/overview.md) definições a que monitorizam os pontos finais desprotegidos, aplicativos e contas de armazenamento. Pontos finais e aplicações que não estão protegidas por uma firewall e as contas de armazenamento com acesso sem restrições, podem permitir não-intencionais acesso às informações contidas dentro do sistema de informações.

- [Pré-visualização]: Monitor unprotected network endpoints in Azure Security Center
- [Pré-visualização]: Monitor unprotected web application in Azure Security Center
- Auditar o acesso sem restrições à rede para as contas de armazenamento

## <a name="a1613-reporting-information-security-weaknesses"></a>Vulnerabilidades de segurança de informações de relatórios A.16.1.3

Este esquema ajuda a manter o reconhecimento de vulnerabilidades do sistema ao atribuir cinco [do Azure Policy](../../../policy/overview.md) definições que monitorizar vulnerabilidades, o estado de patch e software maligno alertas no Centro de segurança do Azure. Centro de segurança do Azure fornece capacidades de relatórios que permitem que tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implementados.

- [Pré-visualização]: Monitor missing Endpoint Protection in Azure Security Center
- [Pré-visualização]: Monitor missing system updates in Azure Security Center
- [Pré-visualização]: Monitor OS vulnerabilities in Azure Security Center
- [Pré-visualização]: Monitor SQL vulnerability assessment results in Azure Security Center
- [Pré-visualização]: Monitor VM Vulnerabilities in Azure Security Center

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu o mapeamento de controle do exemplo de plano gráfico de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL, visite os seguintes artigos para saber mais sobre a arquitetura e como implementar este exemplo:

> [!div class="nextstepaction"]
> [Esquema de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL - descrição geral](./index.md)
> [carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL esquema - implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).