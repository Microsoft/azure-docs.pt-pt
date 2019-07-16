---
title: Mapeamento de controle de exemplo - esquema de carga de trabalho da ISO 27001 ASE/SQL-
description: Mapeamento de controle do exemplo de esquema de carga de trabalho de base de dados do ISO 27001 aplicação serviço ambiente/SQL para o Azure Policy e RBAC.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 59e47c448f58235114c8fb3147637b77dd5fcf23
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226054"
---
# <a name="control-mapping-of-the-iso-27001-asesql-workload-blueprint-sample"></a>Mapeamento de controle do exemplo de plano gráfico de carga de trabalho de ISO 27001 ASE/SQL

O seguinte artigo detalha como o exemplo de plano gráfico de carga de trabalho do Azure esquemas ISO 27001 ASE/SQL é mapeada para os controlos da ISO 27001. Para obter mais informações sobre os controles, consulte [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos são para o **ISO 27001:2013** controles. Utilize a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com um [do Azure Policy](../../../policy/overview.md) iniciativa. Para rever a iniciativa concluída, abra **diretiva** no portal do Azure e selecione o **definições** página. Em seguida, localize e selecione o  **\[pré-visualização\] auditoria ISO 27001:2013 controla e implementar extensões de VM específicas para suportar os requisitos de auditoria** iniciativa de política incorporada.

## <a name="a612-segregation-of-duties"></a>A.6.1.2 segregação de funções

Ter apenas um proprietário da subscrição do Azure não permite para redundância administrativa. Por outro lado, se ter existem demasiados proprietários de subscrição do Azure, pode aumentar o potencial de falhas de segurança por meio de uma conta de proprietário comprometido. Este esquema ajuda a manter um número adequado de proprietários de subscrições do Azure através da atribuição de dois [do Azure Policy](../../../policy/overview.md) definições de que o número de proprietários das subscrições do Azure de auditoria. Gerir permissões de proprietário da subscrição pode ajudar a implementar a separação apropriada de atribuições.

- \[Pré-visualização\]: Número mínimo de proprietários para a subscrição de auditoria
- \[Pré-visualização\]: Número máximo de proprietários para uma subscrição de auditoria

## <a name="a821-classification-of-information"></a>Classificação de A.8.2.1 de informações

Do Azure [serviço de avaliação de vulnerabilidades do SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) pode ajudar a detetar armazenados nas suas bases de dados confidenciais e inclui recomendações para classificar dados. Este esquema atribui uma [Azure Policy](../../../policy/overview.md) definição de auditoria que vulnerabilidades identificadas durante a análise de avaliação de vulnerabilidade de SQL estiverem resolvidas.

- \[Pré-visualização\]: Monitorizar os resultados da avaliação de Vulnerabilidade do SQL no Centro de segurança do Azure

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 acesso às redes e serviços de rede

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para gerir quem tem acesso aos recursos do Azure. Este esquema ajuda-o a controlar o acesso aos recursos do Azure através da atribuição de sete [do Azure Policy](../../../policy/overview.md) definições. Estas políticas auditar a utilização de tipos de recursos e configurações que podem permitir mais permissivas acesso a recursos.
Recursos de compreensão que cumpram estes podem políticas ajuda tome medidas corretivas para garantir um acesso a recursos do Azure é restrita para os utilizadores autorizados.

- \[Pré-visualização\]: Implementar a extensão de VM para fazer a auditoria a contas de VM do Linux com sem palavras-passe
- \[Pré-visualização\]: Implementar a extensão VM para VM do Linux que permite ligações remotas de contas com sem palavras-passe de auditoria
- \[Pré-visualização\]: Auditar contas de VM do Linux com sem palavras-passe
- \[Pré-visualização\]: Permitir ligações remotas de contas com sem palavras-passe de VM do Linux de auditoria
- Auditar a utilização das contas de armazenamento clássicas
- Auditar a utilização das máquinas virtuais clássicas
- Auditar VMs que não utilizam discos geridos

## <a name="a923-management-of-privileged-access-rights"></a>Gestão A.9.2.3 de privilégio direitos de acesso

Este esquema ajuda-o a restringir e controlar os direitos de acesso privilegiado através da atribuição de quatro [do Azure Policy](../../../policy/overview.md) definições para auditar as contas externas com o proprietário e/ou contas e permissões de escrita com proprietário e/ou permissões de escrita que não têm a autenticação multifator ativada. O Azure implementa o controlo de acesso baseado em funções (RBAC) para gerir quem tem acesso aos recursos do Azure. Este esquema também atribui três definições de política do Azure para auditar a utilização da autenticação do Azure Active Directory para servidores SQL e o Service Fabric. Autenticação com o Azure Active Directory permite a gestão de permissões simplificado e o gerenciamento centralizado de identidades de utilizadores de base de dados e outros serviços Microsoft. Este esquema também atribui uma definição de política do Azure para auditar a utilização de regras personalizadas do RBAC. Compreender onde regras personalizadas de RBAC são implementar pode ajudar a verificar necessidade e a implementação adequada, como regras personalizadas de RBAC são suscetíveis a erros.

- \[Pré-visualização\]: Contas com permissões de proprietário que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: Contas com permissões de escrita que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: As contas externas com permissões de proprietário de uma subscrição de auditoria
- \[Pré-visualização\]: Auditar contas externas com permissões de escrita numa subscrição
- Auditar o aprovisionamento de um administrador do Azure Active Directory para o SQL server
- Auditar a utilização do Azure Active Directory para autenticação de cliente no Service Fabric
- Auditar a utilização de regras personalizadas de RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>Gestão de A.9.2.4 de informações secretas autenticação de utilizadores

Este esquema atribui três [do Azure Policy](../../../policy/overview.md) definições de auditoria a contas que não têm a autenticação multifator ativada. Multi-factor authentication ajuda a proteger contas, mesmo que uma parte das informações de autenticação for comprometida. Ao monitorizar contas sem autenticação multifator ativada, pode identificar as contas que podem ser mais probabilidade de ser comprometido. Este esquema também atribui duas definições de política do Azure VM do Linux de auditoria permissões de arquivo de palavra-passe para o alertar se eles estão definidos incorretamente. Esta configuração permite-lhe tomar medidas corretivas para se certificar de autenticadores não são comprometidos.

- \[Pré-visualização\]: Contas com permissões de proprietário que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: Contas com permissões de leitura que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: Contas com permissões de escrita que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: Implementar a extensão de VM para auditar as permissões de arquivo passwd de VM do Linux
- \[Pré-visualização\]: Permissões de arquivo de /etc/passwd de VM do Linux de auditoria são definidas para 0644

## <a name="a925-review-of-user-access-rights"></a>Revisão A.9.2.5 dos direitos de acesso de utilizador

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para ajudar a gerir quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema atribui quatro [do Azure Policy](../../../policy/overview.md) definições de auditoria a contas que devem ser priorizadas para revisão, incluindo contas depreciadas e as contas externas com permissões elevadas.

- \[Pré-visualização\]: As contas preteridas numa assinatura de auditoria
- \[Pré-visualização\]: As contas preteridas com permissões de proprietário de uma subscrição de auditoria
- \[Pré-visualização\]: As contas externas com permissões de proprietário de uma subscrição de auditoria
- \[Pré-visualização\]: Auditar contas externas com permissões de escrita numa subscrição

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Remoção de A.9.2.6 ou ajuste dos direitos de acesso

Azure implementa [controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para ajudar a gerir quem tem acesso a recursos no Azure. Usando [do Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e RBAC, pode atualizar funções de utilizador para refletir alterações organizacionais. Quando necessário, contas podem ser impedidas de iniciar sessão (ou removidas), que remove imediatamente direitos de acesso aos recursos do Azure. Este esquema atribui dois [do Azure Policy](../../../policy/overview.md) definições de conta depreciada que deve ser considerada para remoção de auditoria.

- \[Pré-visualização\]: As contas preteridas numa assinatura de auditoria
- \[Pré-visualização\]: As contas preteridas com permissões de proprietário de uma subscrição de auditoria

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 seguro logon procedimentos

Este esquema atribui três definições de política do Azure para fazer a auditoria a contas que não têm a autenticação multifator ativada. O Azure multi-factor Authentication fornece segurança adicional, exigindo que uma segunda forma de autenticação e proporciona uma autenticação segura. Ao monitorizar contas sem autenticação multifator ativada, pode identificar as contas que podem ser mais probabilidade de ser comprometido.

- \[Pré-visualização\]: Contas com permissões de proprietário que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: Contas com permissões de leitura que não sejam MFA ativada numa subscrição de auditoria
- \[Pré-visualização\]: Contas com permissões de escrita que não sejam MFA ativada numa subscrição de auditoria

## <a name="a943-password-management-system"></a>Sistema de gestão de palavra-passe A.9.4.3

Este esquema ajuda-o a impor senhas fortes atribuindo 10 [do Azure Policy](../../../policy/overview.md) definições de que as VMs do Windows que não impõem força mínima e outros requisitos de palavra-passe de auditoria. Deteção de VMs em violação da política de força de palavra-passe ajuda-o a efetuar ações corretivas para garantir que as palavras-passe para todas as contas de utilizador VM estão em conformidade com a política.

- \[Pré-visualização\]: Implementar a extensão de VM para fazer auditoria Windows VM impõe requisitos de complexidade de palavra-passe
- \[Pré-visualização\]: Implementar a extensão de VM para a duração de senha máxima de VM do Windows 70 dias de auditoria
- \[Pré-visualização\]: Implementar a extensão de VM para auditar a idade de palavra-passe mínima de VM do Windows 1 dia
- \[Pré-visualização\]: Implementar VM extensão para auditar as palavras-passe de VM do Windows tem de ser, pelo menos, de 14 caracteres
- \[Pré-visualização\]: Implementar a extensão de VM a auditoria da VM do Windows não deve permitir palavras-passe de 24 anteriores
- \[Pré-visualização\]: VM do Windows de auditoria impõe requisitos de complexidade de palavra-passe
- \[Pré-visualização\]: Duração de senha máxima de VM do Windows 70 dias de auditoria
- \[Pré-visualização\]: Idade mínima da palavra-passe do Windows VM 1 dia de auditoria
- \[Pré-visualização\]: Palavras-passe da VM do Windows de auditoria tem de ser, pelo menos, de 14 caracteres
- \[Pré-visualização\]: VM do Windows de auditoria não deve permitir palavras-passe de 24 anteriores

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Política A.10.1.1 sobre a utilização de controlos criptográficos

Este esquema ajuda-o a impor a política no uso de controles de cryptograph atribuindo 13 [do Azure Policy](../../../policy/overview.md) utilizam de definições que impõem controles cryptograph específico e de auditoria das definições de criptografia fracas.
Noções básicas sobre onde os recursos do Azure podem ter configurações de criptografia não ideal pode ajudar a efetuar ações corretivas para garantir que recursos estão configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse plano gráfico exigirem a encriptação para as contas de armazenamento de BLOBs e contas de armazenamento do data lake; Exigir encriptação de dados transparente nas bases de dados do SQL; Auditar encriptação em falta em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automatização; Auditar inseguras ligações para as contas de armazenamento, aplicações de funções, aplicação Web, aplicações API e a Cache de Redis; Auditar encriptação de palavra-passe da máquina de virtual fraco; e auditar a comunicação desencriptada do Service Fabric.

- \[Pré-visualização\]: Auditar o acesso apenas de HTTPS para uma aplicação de funções
- \[Pré-visualização\]: Auditar o acesso apenas de HTTPS para uma aplicação Web
- \[Pré-visualização\]: Auditar o acesso apenas de HTTPS para uma aplicação API
- \[Pré-visualização\]: Auditar encriptação de BLOBs em falta para contas de armazenamento
- \[Pré-visualização\]: Implementar a extensão de VM a auditoria da VM do Windows não deve armazenar senhas usando criptografia reversível
- \[Pré-visualização\]: VM do Windows de auditoria não deve armazenar senhas usando criptografia reversível
- \[Pré-visualização\]: Monitorizar a base de dados SQL não encriptada no Centro de segurança do Azure
- \[Pré-visualização\]: Monitorizar discos de VM não encriptados no Centro de segurança do Azure
- Habilitação da criptografia de variáveis de conta de automatização de auditoria
- Auditar que ative apenas as ligações seguras para a Cache de Redis
- Transferência segura de auditoria a contas de armazenamento
- A definição da propriedade ClusterProtectionLevel para EncryptAndSign nos recursos de infraestrutura do serviço de auditoria
- Auditar o estado de encriptação de dados transparente

## <a name="a1241-event-logging"></a>Registo de eventos A.12.4.1

Este esquema ajuda-o a garantir que os eventos de sistema são registados através da atribuição de sete [do Azure Policy](../../../policy/overview.md) definições de auditoria definições de registo nos recursos do Azure.
Os registos de diagnóstico fornecem informações aprofundadas de operações executadas nos recursos do Azure.

- \[Pré-visualização\]: Implementação de agente de dependência de auditoria - imagem de VM (SO) não listados
- \[Pré-visualização\]: Auditar a implementação de agente de dependência no VMSS - imagem de VM (SO) não listados
- \[Pré-visualização\]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- \[Pré-visualização\]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- \[Pré-visualização\]: Monitorizar a base de dados SQL não auditada no Centro de segurança do Azure
- Definição de diagnóstico de auditoria
- Definições de auditoria ao nível do SQL server de auditoria

## <a name="a1243-administrator-and-operator-logs"></a>Registos de A.12.4.3 administrador e o operador

Este esquema ajuda a garantir que os eventos de sistema são registados através da atribuição de sete definições de política do Azure que auditoria definições de registo nos recursos do Azure. Os registos de diagnóstico fornecem informações aprofundadas de operações executadas nos recursos do Azure.

- \[Pré-visualização\]: Implementação de agente de dependência de auditoria - imagem de VM (SO) não listados
- \[Pré-visualização\]: Auditar a implementação de agente de dependência no VMSS - imagem de VM (SO) não listados
- \[Pré-visualização\]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- \[Pré-visualização\]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- \[Pré-visualização\]: Monitorizar a base de dados SQL não auditada no Centro de segurança do Azure
- Definição de diagnóstico de auditoria
- Definições de auditoria ao nível do SQL server de auditoria

## <a name="a1244-clock-synchronization"></a>Sincronização do relógio A.12.4.4

Este esquema ajuda a garantir que os eventos de sistema são registados através da atribuição de sete definições de política do Azure que auditoria definições de registo nos recursos do Azure. Registos do Azure dependem sincronizados relógios internos para criar um registo de tempo de mensagens em fila de eventos de todos os recursos.

- \[Pré-visualização\]: Implementação de agente de dependência de auditoria - imagem de VM (SO) não listados
- \[Pré-visualização\]: Auditar a implementação de agente de dependência no VMSS - imagem de VM (SO) não listados
- \[Pré-visualização\]: Implementação de agente da análise de registo de auditoria - imagem de VM (SO) não listados
- \[Pré-visualização\]: Auditar a implementação de agente do Log Analytics no VMSS - imagem de VM (SO) não listados
- \[Pré-visualização\]: Monitorizar a base de dados SQL não auditada no Centro de segurança do Azure
- Definição de diagnóstico de auditoria
- Definições de auditoria ao nível do SQL server de auditoria

## <a name="a1251-installation-of-software-on-operational-systems"></a>Instalação de A.12.5.1 de software em sistemas operacionais

Controlo de aplicação adaptável é a solução do Centro de segurança do Azure que ajuda a controlar quais aplicativos pode ser executadas nas suas VMs localizadas no Azure. Este esquema atribui uma definição de política do Azure que monitoriza alterações para o conjunto de aplicações permitidas. Esta capacidade ajuda-o a controlar a instalação de software e aplicações em VMs do Azure.

- \[Pré-visualização\]: Monitorizar aplicação possíveis listas de permissões no Centro de segurança do Azure

## <a name="a1261-management-of-technical-vulnerabilities"></a>Gestão de A.12.6.1 de vulnerabilidades técnicas

Este esquema ajuda a gerenciar vulnerabilidades do sistema de informações através da atribuição de cinco [do Azure Policy](../../../policy/overview.md) definições que monitorar em falta atualizações do sistema, vulnerabilidades do sistema operativo, vulnerabilidades SQL e máquinas virtuais vulnerabilidades no Centro de segurança do Azure. Centro de segurança do Azure fornece capacidades de relatórios que permitem que tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implementados.

- \[Pré-visualização\]: Monitorizar o Endpoint Protection em falta no Centro de segurança do Azure
- \[Pré-visualização\]: Monitorizar atualizações de sistema em falta no Centro de segurança do Azure
- \[Pré-visualização\]: Monitorizar vulnerabilidades do SO no Centro de segurança do Azure
- \[Pré-visualização\]: Monitorizar os resultados da avaliação de Vulnerabilidade do SQL no Centro de segurança do Azure
- \[Pré-visualização\]: Monitor de vulnerabilidades de VM no Centro de segurança do Azure

## <a name="a1262-restrictions-on-software-installation"></a>Restrições de A.12.6.2 numa instalação de software

Controlo de aplicação adaptável é a solução do Centro de segurança do Azure que ajuda a controlar quais aplicativos pode ser executadas nas suas VMs localizadas no Azure. Este esquema atribui uma definição de política do Azure que monitoriza alterações para o conjunto de aplicações permitidas. Restrições de instalação de software podem ajudar a reduzir a probabilidade de introdução de vulnerabilidades de software.

- \[Pré-visualização\]: Monitorizar aplicação possíveis listas de permissões no Centro de segurança do Azure

## <a name="a1311-network-controls"></a>Controlos de rede A.13.1.1

Este esquema ajuda-o a gerir e controlar redes atribuindo um [do Azure Policy](../../../policy/overview.md) definição que monitoriza os grupos de segurança de rede com regras permissivas. As regras que são demasiado permissivas podem permitir o acesso à rede não-intencionais e devem ser revistas. Este esquema também atribui três definições de política do Azure que monitorizam os pontos finais desprotegidos, aplicativos e contas de armazenamento. Pontos finais e aplicações que não estão protegidas por uma firewall e as contas de armazenamento com acesso sem restrições, podem permitir não-intencionais acesso às informações contidas dentro do sistema de informações.

- \[Pré-visualização\]: Monitorizar o acesso de rede permissivo no Centro de segurança do Azure
- \[Pré-visualização\]: Monitorizar pontos finais de redes desprotegidos no Centro de segurança do Azure
- \[Pré-visualização\]: Monitorizar aplicações web desprotegidas no Centro de segurança do Azure
- Auditar o acesso de rede sem restrições a contas de armazenamento

## <a name="a1321-information-transfer-policies-and-procedures"></a>Procedimentos e políticas de transferência de informações de A.13.2.1

O plano gráfico ajuda a garantir a transferência de informações com serviços do Azure é segura atribuindo dois [do Azure Policy](../../../policy/overview.md) definições de auditoria inseguras ligações para as contas de armazenamento e a Cache de Redis.

- Auditar que ative apenas as ligações seguras para a Cache de Redis
- Transferência segura de auditoria a contas de armazenamento

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
