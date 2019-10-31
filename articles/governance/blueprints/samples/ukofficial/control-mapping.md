---
title: Exemplos do NHS & oficial do Reino Unido no Reino Unido – mapeamento de controle
description: Mapeamento de controle dos exemplos do plano gráfico do Reino Unido e do Reino Unido NHS. Cada controle é mapeado para uma ou mais políticas do Azure que auxiliam na avaliação.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: e86c7040e94553b032b565e282639047a1ed8750
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73162953"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapeamento de controle dos exemplos do plano gráfico do Reino Unido e do Reino Unido NHS

O artigo a seguir fornece detalhes sobre como os exemplos do plano gráfico do Reino Unido e do Reino Unido NHS são mapeados para os controles OFFICIAL e NHS do Reino Unido. Para obter mais informações sobre os controles, consulte [oficial do Reino Unido](https://www.gov.uk/government/publications/government-security-classifications).

Os mapeamentos a seguir são para os controles **oficial do Reino Unido** e **NHS do Reino Unido** . Use a navegação à direita para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, localize e selecione os **controles de\[visualização\] auditoria do Reino Unido e do Reino Unido NHS e implante extensões de VM específicas para dar suporte** à iniciativa de política interna de requisitos de auditoria.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 proteção de dados em trânsito

O plano gráfico ajuda a garantir que a transferência de informações com os serviços do Azure seja segura atribuindo definições de [Azure Policy](../../../policy/overview.md) que auditam conexões inseguras a contas de armazenamento e cache Redis.

- Somente conexões seguras para o cache Redis devem ser habilitadas
- A transferência segura para contas de armazenamento deve ser habilitada

## <a name="23-data-at-rest-protection"></a>2,3 proteção de dados em repouso

Este projeto ajuda a reforçar a política sobre o uso de controles cryptograph atribuindo definições de [Azure Policy](../../../policy/overview.md) que impõem controles cryptograph específicos e auditam o uso de configurações de criptografia fracas.
Entender onde os recursos do Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar medidas corretivas para garantir que os recursos sejam configurados de acordo com sua política de segurança de informações. Especificamente, as políticas atribuídas por este projeto exigem criptografia para contas de armazenamento do data Lake; exigir Transparent Data Encryption em bancos de dados SQL; auditar criptografia ausente em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação; auditar conexões inseguras para contas de armazenamento e cache Redis; auditar a criptografia de senha de máquina virtual fraca; e auditar a comunicação de Service Fabric não criptografada.

- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas
- A criptografia de disco deve ser aplicada em máquinas virtuais
- As variáveis da conta de automação devem ser criptografadas
- A transferência segura para contas de armazenamento deve ser habilitada
- Service Fabric clusters devem ter a propriedade ClusterProtectionLevel definida como EncryptAndSign
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas
- Implantar a Transparent Data Encryption do banco de dados SQL
- Exigir criptografia em contas de Data Lake Store
- Locais permitidos (foram embutidos em código para "sul do Reino Unido" e "oeste do Reino Unido")
- Locais permitidos para grupos de recursos (foi embutido no código "sul do Reino Unido" e "oeste do Reino Unido")

## <a name="52-vulnerability-management"></a>5,2 gerenciamento de vulnerabilidades

Este projeto ajuda você a gerenciar as vulnerabilidades do sistema de informações atribuindo definições de [Azure Policy](../../../policy/overview.md) que monitoram a proteção de ponto de extremidade ausente, atualizações do sistema ausentes, vulnerabilidades do sistema operacional, vulnerabilidades do SQL e virtual vulnerabilidades do computador. Esses insights fornecem informações em tempo real sobre o estado de segurança de seus recursos implantados e podem ajudá-lo a priorizar as ações de correção.

- Monitorar Endpoint Protection ausentes na central de segurança do Azure
- As atualizações do sistema devem ser instaladas em seus computadores
- Vulnerabilidades na configuração de segurança em seus computadores devem ser corrigidas
- Vulnerabilidades em seus bancos de dados SQL devem ser corrigidas
- Vulnerabilidades devem ser corrigidas por uma solução de avaliação de vulnerabilidade

## <a name="53-protective-monitoring"></a>Monitoramento de proteção 5,3

Este projeto ajuda a proteger os ativos do sistema de informações atribuindo definições de [Azure Policy](../../../policy/overview.md) que fornecem monitoramento de proteção de acesso irrestrito, atividade de lista de permissões e ameaças.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais
- Implantar a detecção de ameaças em servidores SQL
- Implantar a extensão antimalware do Microsoft IaaS padrão para o Windows Server

## <a name="9-secure-user-management--10-identity-and-authentication"></a>9 gerenciamento seguro de usuários/10 identidade e autenticação

O Azure implementa o RBAC (controle de acesso baseado em função) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Este projeto ajuda a restringir e controlar os direitos de acesso atribuindo definições de [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou permissões de leitura/gravação e contas com permissão de proprietário, leitura e/ou gravação que não têm vários fatores autenticação habilitada.

- A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura
- A MFA deve ser habilitada em contas com permissões de leitura em sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- Contas externas com permissões de leitura devem ser removidas da sua assinatura

Este projeto atribui definições de Azure Policy para auditar o uso de autenticação de Azure Active Directory para SQL Servers e Service Fabric. Usar Azure Active Directory autenticação permite o gerenciamento de permissões simplificado e o gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft.

- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL
- Clusters de Service Fabric só devem usar Azure Active Directory para autenticação de cliente

Esse projeto também atribui definições de Azure Policy para auditar contas que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de Azure Policy para auditar a conta depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua assinatura
- Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Esse plano gráfico também atribui uma definição de Azure Policy que audita as permissões de arquivo de senha de VM do Linux para alertar se elas estiverem definidas incorretamente. Esse design permite que você tome medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[\]de visualização: as permissões de auditoria do arquivo/etc/passwd de VM do Linux são definidas como 0644

Este projeto ajuda a impor senhas fortes atribuindo definições de Azure Policy que auditam as VMs do Windows que não impõem a força mínima e outros requisitos de senha. A conscientização das VMs em violação da política de força da senha ajuda você a tomar medidas corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- \]de visualização do \[: implantar requisitos para auditar VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \]de visualização do \[: implantar requisitos para auditar VMs do Windows que não têm uma duração máxima de senha de 70 dias
- \]de visualização do \[: implantar requisitos para auditar VMs do Windows que não têm uma duração mínima de senha de 1 dia
- \]de visualização do \[: implantar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da senha a 14 caracteres
- \]de visualização do \[: implantar requisitos para auditar VMs do Windows que permitem o reuso das 24 senhas anteriores
- \[\]de visualização: auditar VMs do Windows que não têm a configuração de complexidade de senha habilitada
- \[\]de visualização: auditar VMs do Windows que não têm uma duração máxima de senha de 70 dias
- \]de visualização de \[: auditar VMs do Windows que não têm uma duração mínima de senha de 1 dia
- \[\]de visualização: auditar VMs do Windows que não restringem o comprimento mínimo da senha a 14 caracteres
- \[\]de visualização: auditar VMs do Windows que permitem o reuso das 24 senhas anteriores

Este projeto também ajuda a controlar o acesso aos recursos do Azure atribuindo definições de Azure Policy. Essas políticas usam a auditoria do uso de tipos de recursos e configurações que podem permitir acesso mais permissivo aos recursos. Compreender os recursos que estão em violação dessas políticas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos do Azure de acesso sejam restritos a usuários autorizados.

- \]de visualização do \[: implantar requisitos para auditar VMs do Linux que têm contas sem senhas
- \]de visualização do \[: implantar requisitos para auditar VMs do Linux que permitem conexões remotas de contas sem senhas
- \[\]de visualização: auditar VMs do Linux que têm contas sem senhas
- \[\]de visualização: auditar VMs do Linux que permitem conexões remotas de contas sem senhas
- As contas de armazenamento devem ser migradas para novos recursos de Azure Resource Manager
- As máquinas virtuais devem ser migradas para novos recursos de Azure Resource Manager
- Auditar VMs que não usam discos gerenciados

## <a name="11-external-interface-protection"></a>11 proteção de interface externa

Além de usar mais de 25 políticas para o gerenciamento de usuários seguro apropriado, este projeto ajuda a proteger interfaces de serviço contra acesso não autorizado, atribuindo uma definição de [Azure Policy](../../../policy/overview.md) que monitora contas de armazenamento irrestritas. As contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações. Esse projeto também atribui uma política que habilita controles de aplicativo adaptáveis em máquinas virtuais.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- Os controles de aplicativo adaptáveis devem ser habilitados em máquinas virtuais

## <a name="12-secure-service-administration"></a>12 administração segura de serviços

O Azure implementa o RBAC (controle de acesso baseado em função) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Este projeto ajuda a restringir e controlar direitos de acesso privilegiado atribuindo cinco definições de [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou gravação e contas com o proprietário e/ou permissões de gravação que não têm autenticação multifator habilitada.

Os sistemas usados para administração de um serviço de nuvem terão acesso altamente privilegiado a esse serviço. Seu comprometimento teria um impacto significativo, incluindo os meios para ignorar os controles de segurança e roubar ou manipular grandes volumes de dados. Os métodos usados pelos administradores do provedor de serviços para gerenciar o serviço operacional devem ser projetados para atenuar qualquer risco de exploração que possa prejudicar a segurança do serviço. Se esse princípio não for implementado, um invasor poderá ter meios para ignorar os controles de segurança e roubar ou manipular grandes volumes de dados.

- A MFA deve ser habilitada em contas com permissões de proprietário em sua assinatura
- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Este projeto atribui definições de Azure Policy para auditar o uso de autenticação de Azure Active Directory para SQL Servers e Service Fabric. Usar Azure Active Directory autenticação permite o gerenciamento de permissões simplificado e o gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft.

- Um administrador de Azure Active Directory deve ser provisionado para servidores SQL
- Clusters de Service Fabric só devem usar Azure Active Directory para autenticação de cliente

Esse projeto também atribui definições de Azure Policy para auditar contas que devem ser priorizadas para análise, incluindo contas depreciadas e contas externas com permissões elevadas. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de Azure Policy para auditar a conta depreciada que deve ser considerada para remoção.

- As contas preteridas devem ser removidas da sua assinatura
- Contas preteridas com permissões de proprietário devem ser removidas da sua assinatura
- Contas externas com permissões de proprietário devem ser removidas da sua assinatura
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Esse plano gráfico também atribui uma definição de Azure Policy que audita as permissões de arquivo de senha de VM do Linux para alertar se elas estiverem definidas incorretamente. Esse design permite que você tome medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[\]de visualização: as permissões de auditoria do arquivo/etc/passwd de VM do Linux são definidas como 0644

## <a name="13-audit-information-for-users"></a>13 informações de auditoria para usuários

Este projeto ajuda a garantir que os eventos do sistema sejam registrados por meio da atribuição de definições de [Azure Policy](../../../policy/overview.md) que auditam as configurações de log nos recursos do Azure. Uma política atribuída também audita se as máquinas virtuais não estão enviando logs para um espaço de trabalho do log Analytics especificado.

- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server
- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- \]de visualização de \[: implantar o agente de Log Analytics para VMs Linux
- \]de visualização do \[: implantar o agente de Log Analytics para VMs do Windows
- Implantar o observador de rede quando redes virtuais forem criadas

## <a name="next-steps"></a>Passos seguintes

Agora que você analisou o mapeamento de controle dos planos gráficos do Reino Unido e do Reino Unido NHS, visite os artigos a seguir para saber mais sobre a visão geral e como implantar este exemplo:

> [!div class="nextstepaction"]
> [Plantas NHSis do Reino Unido e do Reino Unido-visão geral](./index.md)
> [ru Official e plantas de NHS do Reino Unido-etapas de implantação](./deploy.md)

Artigos adicionais sobre plantas e como usá-los:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).