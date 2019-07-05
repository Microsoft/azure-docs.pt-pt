---
title: Mapeamento de controle do exemplo - esquema de v3.2.1 PCI-DSS-
description: Mapeamento de controle do exemplo de plano gráfico v3.2.1 pagamento Card Industry Data Security Standard para a política do Azure e o RBAC.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 38b1cc6249da98e11167416c8e18d06de1645679
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/03/2019
ms.locfileid: "67540939"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapeamento de controle do exemplo de plano gráfico de v3.2.1 PCI-DSS

O seguinte artigo detalha como o exemplo de esquema do Azure esquemas PCI-DSS v3.2.1 mapeia para os controles de v3.2.1 PCI-DSS. Para obter mais informações sobre os controles, consulte [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Os seguintes mapeamentos são para o **PCI-DSS v3.2.1:2018** controles. Utilize a navegação no lado direito para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com um [do Azure Policy](../../../policy/overview.md) iniciativa. Para rever a iniciativa concluída, abra **diretiva** no portal do Azure e selecione o **definições** página. Em seguida, localize e selecione o  **[pré-visualização] v3.2.1:2018 do PCI de auditoria controla e implementar extensões de VM específicas para suportar os requisitos de auditoria** iniciativa de política incorporada.

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 proteção de limites

Este esquema ajuda-o a gerir e controlar redes atribuindo [do Azure Policy](../../../policy/overview.md) definições que monitoriza os grupos de segurança de rede com regras permissivas. As regras que são demasiado permissivas podem permitir o acesso à rede não-intencionais e devem ser revistas. Este esquema atribui um definições de política do Azure que monitorizar pontos finais desprotegidos, aplicativos e contas de armazenamento. Pontos finais e aplicações que não estão protegidas por uma firewall e as contas de armazenamento com acesso sem restrições, podem permitir não-intencionais acesso às informações contidas dentro do sistema de informações.

- Auditar o acesso de rede sem restrições a contas de armazenamento
- O acesso através de ponto de final com acesso à Internet deve ser restrito

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 proteção criptográfica

Este esquema ajuda-o a impor a política com o uso de controles de cryptograph atribuindo [do Azure Policy](../../../policy/overview.md) definições que impõem uma auditoria e de controles específicos cryptograph utilizam de definições de criptografia fracas. Noções básicas sobre onde os recursos do Azure podem ter configurações de criptografia não ideal pode ajudar a efetuar ações corretivas para garantir que recursos estão configurados de acordo com a política de segurança de informações. Especificamente, as políticas atribuídas por esse plano gráfico exigirem encriptação de dados transparente nas bases de dados do SQL; Auditar encriptação em falta nas contas de armazenamento e as variáveis de conta de automatização. Também existem políticas de qual o endereço de auditoria inseguras ligações para as contas de armazenamento, aplicações de funções, aplicação Web, aplicações API e a Cache de Redis e auditar a comunicação desencriptada do Service Fabric.

- Função de aplicação só deve estar acessível através de HTTPS
- Aplicação Web só deve estar acessível através de HTTPS
- Aplicação API só devem estar acessível através de HTTPS
- Monitorizar a base de dados SQL não encriptada no Centro de segurança do Azure
- Encriptação de disco deve ser aplicada em máquinas virtuais
- Variáveis de conta de automatização devem ser encriptadas
- Devem ser ativadas apenas ligações seguras para a Cache de Redis
- Deve ser ativada para contas de armazenamento com transferência segura
- Clusters do Service Fabric devem ter a propriedade de ClusterProtectionLevel definida como EncryptAndSign
- Deve ser ativada a encriptação de dados transparente nas bases de dados SQL
- Implementar a encriptação de dados transparente de BD SQL

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 e 11.2.1 atualizações do sistema e de análise de vulnerabilidade

Este esquema ajuda a gerenciar vulnerabilidades do sistema de informações através da atribuição [do Azure Policy](../../../policy/overview.md) definições que monitorar em falta atualizações do sistema, vulnerabilidades do sistema operativo, vulnerabilidades SQL e máquinas virtuais vulnerabilidades no Centro de segurança do Azure. Centro de segurança do Azure fornece capacidades de relatórios que permitem que tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implementados.

- Monitorizar o Endpoint Protection em falta no Centro de segurança do Azure
- Implementar extensão Microsoft IaaSAntimalware predefinida para o Windows Server
- Implementar a deteção de ameaças em servidores SQL
- Atualizações do sistema devem ser instaladas nos seus computadores
- Vulnerabilidades na configuração de segurança nas suas máquinas devem ser resolvidas
- Vulnerabilidades nos seus bancos de dados do SQL devem ser resolvidas
- Vulnerabilidades devem ser resolvidas por uma solução de avaliação de vulnerabilidade

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 separação de funções

Ter apenas um proprietário da subscrição do Azure não permite para redundância administrativa. Por outro lado, se ter existem demasiados proprietários de subscrição do Azure, pode aumentar o potencial de falhas de segurança por meio de uma conta de proprietário comprometido. Este esquema ajuda a manter um número adequado de proprietários de subscrições do Azure através da atribuição [do Azure Policy](../../../policy/overview.md) definições de que o número de proprietários das subscrições do Azure de auditoria. Gerir permissões de proprietário da subscrição pode ajudar a implementar a separação apropriada de atribuições.

- Deve haver mais do que um proprietário atribuído à sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b gestão de direitos de acesso privilegiado

Este esquema ajuda-o a restringir e controlar os direitos de acesso privilegiado através da atribuição [do Azure Policy](../../../policy/overview.md) definições para auditar as contas externas com o proprietário, escrever e/ou permissões e contas de funcionário de leitura com o proprietário e/ou de escrita permissões de que não têm a autenticação multifator ativada. O Azure implementa o controlo de acesso baseado em funções (RBAC) para gerir quem tem acesso aos recursos do Azure. Compreender onde regras personalizadas de RBAC são implementar pode ajudar a verificar necessidade e a implementação adequada, como regras personalizadas de RBAC são suscetíveis a erros. Este plano gráfico também atribui [do Azure Policy](../../../policy/overview.md) utilizam de definições de auditoria de autenticação do Azure Active Directory para servidores SQL. Com o Azure Active Directory authentication simplifica a gestão de permissão e centraliza a gestão de identidades de utilizadores de base de dados e outro da Microsoft  
Serviços.
 
- As contas externas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- MFA deve ser ativada em contas com permissões de proprietário na sua subscrição
- MFA deve ser ativadas contas com permissões de escrita na sua subscrição
- MFA deve ser ativada em contas com permissões de leitura na sua subscrição
- Um administrador do Azure Active Directory deve ser fornecido para servidores SQL
- Auditar a utilização de regras personalizadas de RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 menos privilégios e revisão de utilizador de direitos de acesso

O Azure implementa baseada em funções controlo de acesso (RBAC) para ajuda a gerenciar quem tem acesso a recursos no Azure. Utilizar o portal do Azure, pode rever quem tem acesso aos recursos do Azure e as respetivas permissões. Este esquema atribui [do Azure Policy](../../../policy/overview.md) definições de auditoria a contas que devem ser priorizadas para revisão, incluindo contas depreciadas e as contas externas com permissões elevadas.

- As contas preteridas devem ser removidas da sua subscrição
- As contas preteridas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 remoção de ou ajuste dos direitos de acesso

O Azure implementa o controlo de acesso baseado em funções (RBAC) para ajudar a gerir quem tem acesso a recursos no Azure. Utilizar o Azure Active Directory e o RBAC, pode atualizar funções de utilizador para refletir alterações organizacionais. Quando necessário, contas podem ser impedidas de iniciar sessão (ou removidas), que remove imediatamente direitos de acesso aos recursos do Azure. Este esquema atribui [do Azure Policy](../../../policy/overview.md) definições de conta depreciada que deve ser considerada para remoção de auditoria.

- As contas preteridas devem ser removidas da sua subscrição
- As contas preteridas com permissões de proprietário devem ser removidas da sua subscrição

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 autenticação baseada em palavra-passe

Este esquema ajuda-o a impor senhas fortes atribuindo [do Azure Policy](../../../policy/overview.md) definições de que as VMs do Windows que não impõem força mínima e outros requisitos de palavra-passe de auditoria. Deteção de VMs em violação da política de força de palavra-passe ajuda-o a efetuar ações corretivas para garantir que as palavras-passe para todas as contas de utilizador VM estão em conformidade com a política.

- [Pré-visualização]: Audit Windows VMs that do not have a maximum password age of 70 days
- [Pré-visualização]: Deploy requirements to audit Windows VMs that do not have a maximum password age of 70 days
- [Pré-visualização]: Audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Pré-visualização]: Deploy requirements to audit Windows VMs that do not restrict the minimum password length to 14 characters
- [Pré-visualização]: Audit Windows VMs that allow re-use of the previous 24 passwords
- [Pré-visualização]: Deploy requirements to audit Windows VMs that allow re-use of the previous 24 passwords

## <a name="103-and-1054-audit-generation"></a>10.3 e 10.5.4 geração de auditoria

Este esquema ajuda a garantir que os eventos de sistema são registados através da atribuição [do Azure Policy](../../../policy/overview.md) definições de auditoria definições de registo nos recursos do Azure.
Os registos de diagnóstico fornecem informações aprofundadas de operações executadas nos recursos do Azure. Registos do Azure dependem sincronizados relógios internos para criar um registo de tempo de mensagens em fila de eventos de todos os recursos.

- Monitorizar os servidores SQL não auditadas no Centro de segurança do Azure
- Definição de diagnóstico de auditoria
- Definições de auditoria ao nível do SQL server de auditoria
- Implementar a auditoria em servidores SQL
- Contas de armazenamento devem ser migradas para novos recursos do Azure Resource Manager
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>Segurança de informações de 12.3.6 e 12.3.7

Esse plano gráfico ajuda a gerenciar e controlar a sua rede, atribuindo [do Azure Policy](../../../policy/overview.md) definições de auditoria as localizações de rede aceitável e os produtos da empresa aprovados permitido para o ambiente. Estes são personalizáveis por cada empresa através dos parâmetros de política dentro de cada uma destas políticas.

- Localizações permitidas
- Permitido localizações para grupos de recursos

## <a name="next-steps"></a>Passos Seguintes

Agora que reviu o mapeamento de controle do esquema de v3.2.1 PCI-DSS, visite os seguintes artigos para saber mais sobre a visão geral e como implementar este exemplo:

> [!div class="nextstepaction"]
> [Esquema de v3.2.1 PCI-DSS - descrição geral](./index.md)
> [PCI-DSS v3.2.1 esquema - implementar passos](./deploy.md)

## <a name="addition-articles-about-blueprints-and-how-to-use-them"></a>Artigos adicionais sobre esquemas e como utilizá-los:

- Saiba mais sobre o [ciclo de vida de um esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
