---
title: Exemplo de diagrama de serviços compartilhados ISO 27001 – mapeamento de controle
description: Mapeamento de controle do exemplo de plano gráfico dos serviços compartilhados ISO 27001. Cada controle é mapeado para uma ou mais políticas do Azure que auxiliam na avaliação.
ms.date: 03/14/2019
ms.topic: sample
ms.openlocfilehash: e3bba30ddffe85a067d091a328ae4cc4de7258df
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74037381"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mapeamento de controle do exemplo de plano gráfico dos serviços compartilhados ISO 27001

O artigo a seguir fornece detalhes sobre como a amostra do plano gráfico dos serviços compartilhados do ISO 27001 do Azure é mapeada para os controles ISO 27001. Para obter mais informações sobre os controles, consulte [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os mapeamentos a seguir são para os controles **ISO 27001:2013** . Use a navegação à direita para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, localize e selecione a **\[visualização\] auditoria de controles ISO 27001:2013 e implante extensões de VM específicas para dar suporte** à iniciativa de política interna de requisitos de auditoria.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>Uma diferenciação de. 6.1.2 de funções

Ter apenas um proprietário de assinatura do Azure não permite redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este projeto ajuda a manter um número apropriado de proprietários de assinatura do Azure atribuindo duas definições de [Azure Policy](../../../policy/overview.md) que auditam o número de proprietários para assinaturas do Azure. O gerenciamento de permissões de proprietário de assinatura pode ajudá-lo a implementar a separação apropriada de tarefas.

- \]de visualização de \[: auditar o número mínimo de proprietários de uma assinatura
- \]de visualização de \[: auditar o número máximo de proprietários de uma assinatura

## <a name="a821-classification-of-information"></a>Uma classificação. 8.2.1 de informações

O [serviço de avaliação de vulnerabilidades do SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) do Azure pode ajudá-lo a descobrir dados confidenciais armazenados em seus bancos de dados e inclui recomendações para classificá-los. Este projeto atribui uma definição de [Azure Policy](../../../policy/overview.md) para auditar que vulnerabilidades identificadas durante a verificação da avaliação de vulnerabilidade do SQL são corrigidas.

- \]de visualização do \[: monitorar resultados da avaliação de vulnerabilidade do SQL na central de segurança do Azure

## <a name="a912-access-to-networks-and-network-services"></a>A. 9.1.2 acesso a redes e serviços de rede

O Azure implementa o RBAC ( [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) ) para gerenciar quem tem acesso aos recursos do Azure. Este projeto ajuda você a controlar o acesso aos recursos do Azure atribuindo sete definições de [Azure Policy](../../../policy/overview.md) . Essas políticas usam a auditoria do uso de tipos de recursos e configurações que podem permitir acesso mais permissivo aos recursos.
Compreender os recursos que estão em violação dessas políticas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos do Azure de acesso sejam restritos a usuários autorizados.

- \]de visualização de \[: implantar a extensão de VM para auditar contas de VM Linux sem senhas
- \]de visualização de \[: implantar a extensão de VM para auditar a VM do Linux permitindo conexões remotas de contas sem senhas
- \[\]de visualização: auditar contas de VM do Linux sem senhas
- \[\]de visualização: auditar a VM do Linux permitindo conexões remotas de contas sem senhas
- Auditar o uso de contas de armazenamento clássicas
- Auditar o uso de máquinas virtuais clássicas
- Auditar VMs que não usam discos gerenciados

## <a name="a923-management-of-privileged-access-rights"></a>A. 9.2.3 gerenciamento de direitos de acesso privilegiado

Este projeto ajuda a restringir e controlar direitos de acesso privilegiado atribuindo quatro definições de [Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões de proprietário e/ou gravação e contas com permissões de proprietário e/ou gravação que não têm a autenticação multifator habilitada. O Azure implementa o RBAC (controle de acesso baseado em função) para gerenciar quem tem acesso aos recursos do Azure. Esse projeto também atribui três definições de Azure Policy para auditar o uso da autenticação Azure Active Directory para servidores SQL e Service Fabric. Usar Azure Active Directory autenticação permite o gerenciamento de permissões simplificado e o gerenciamento centralizado de identidades de usuários de banco de dados e outros serviços da Microsoft. Esse projeto também atribui uma definição de Azure Policy para auditar o uso de regras personalizadas de RBAC. Entender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar a necessidade e a implementação adequada, pois as regras personalizadas de RBAC são propensas a erros.

- \]de visualização de \[: auditar contas com permissões de proprietário que não são habilitadas para MFA em uma assinatura
- \[\]de visualização: contas de auditoria com permissões de gravação que não são habilitadas para MFA em uma assinatura
- \]de visualização de \[: auditar contas externas com permissões de proprietário em uma assinatura
- \]de visualização de \[: auditar contas externas com permissões de gravação em uma assinatura
- Auditar o provisionamento de um administrador de Azure Active Directory para o SQL Server
- Auditar o uso de Azure Active Directory para autenticação de cliente no Service Fabric
- Auditar o uso de regras personalizadas de RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A. 9.2.4 o gerenciamento de informações secretas de autenticação de usuários

Este projeto atribui três definições de [Azure Policy](../../../policy/overview.md) para auditar contas que não têm a autenticação multifator habilitada. A autenticação multifator ajuda a manter as contas seguras mesmo que uma parte das informações de autenticação seja comprometida. Ao monitorar contas sem a autenticação multifator habilitada, você pode identificar contas que podem ser mais prováveis de serem comprometidas. Esse plano gráfico também atribui duas definições de Azure Policy que auditam as permissões de arquivo de senha de VM do Linux para alertar se elas estiverem definidas incorretamente. Essa configuração permite que você adote ações corretivas para garantir que os autenticadores não sejam comprometidos.

- \]de visualização de \[: auditar contas com permissões de proprietário que não são habilitadas para MFA em uma assinatura
- \]de visualização de \[: auditar contas com permissões de leitura que não são habilitadas para MFA em uma assinatura
- \[\]de visualização: contas de auditoria com permissões de gravação que não são habilitadas para MFA em uma assinatura
- \]de visualização de \[: implantar extensão de VM para auditar permissões de arquivo de senha de VM do Linux
- \[\]de visualização: as permissões de auditoria do arquivo/etc/passwd de VM do Linux são definidas como 0644

## <a name="a925-review-of-user-access-rights"></a>A. 9.2.5 revisão dos direitos de acesso do usuário

O Azure implementa o RBAC ( [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) ) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui quatro definições de [Azure Policy](../../../policy/overview.md) para auditar contas que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas.

- \]de visualização de \[: auditar contas preteridas em uma assinatura
- \]de visualização de \[: auditar contas preteridas com permissões de proprietário em uma assinatura
- \]de visualização de \[: auditar contas externas com permissões de proprietário em uma assinatura
- \]de visualização de \[: auditar contas externas com permissões de gravação em uma assinatura

## <a name="a926-removal-or-adjustment-of-access-rights"></a>Remoção ou ajuste de direitos de acesso de. 9.2.6

O Azure implementa o RBAC ( [controle de acesso baseado em função](../../../../role-based-access-control/overview.md) ) para ajudá-lo a gerenciar quem tem acesso aos recursos no Azure. Usando [Azure Active Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e RBAC, você pode atualizar funções de usuário para refletir alterações organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de [Azure Policy](../../../policy/overview.md) para auditar a conta depreciada que deve ser considerada para remoção.

- \]de visualização de \[: auditar contas preteridas em uma assinatura
- \]de visualização de \[: auditar contas preteridas com permissões de proprietário em uma assinatura

## <a name="a942-secure-log-on-procedures"></a>Procedimentos de logon seguro do. 9.4.2

Este projeto atribui três definições de Azure Policy para auditar contas que não têm a autenticação multifator habilitada. A autenticação multifator do Azure fornece segurança adicional, exigindo uma segunda forma de autenticação e fornece autenticação forte. Ao monitorar contas sem a autenticação multifator habilitada, você pode identificar contas que podem ser mais prováveis de serem comprometidas.

- \]de visualização de \[: auditar contas com permissões de proprietário que não são habilitadas para MFA em uma assinatura
- \]de visualização de \[: auditar contas com permissões de leitura que não são habilitadas para MFA em uma assinatura
- \[\]de visualização: contas de auditoria com permissões de gravação que não são habilitadas para MFA em uma assinatura

## <a name="a943-password-management-system"></a>Um sistema de gerenciamento de senhas. 9.4.3

Este projeto ajuda a impor senhas fortes atribuindo 10 definições de [Azure Policy](../../../policy/overview.md) que auditam as VMs do Windows que não impõem a força mínima e outros requisitos de senha. A conscientização das VMs em violação da política de força da senha ajuda você a tomar medidas corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- \]de visualização de \[: implantar a extensão de VM para auditar a VM do Windows impõe requisitos de complexidade de senha
- \]de visualização de \[: implantar a extensão de VM para auditar a senha máxima da VM Windows duração de 70 dias
- \[\]de visualização: implantar a extensão de VM para auditar a duração mínima da senha do Windows na VM 1 dia
- \[\]de visualização: implantar a extensão de VM para auditar as senhas de VM Windows devem ter pelo menos 14 caracteres
- \]de visualização de \[: implantar a extensão de VM para auditar a VM do Windows não deve permitir 24 senhas anteriores
- \]de visualização do \[: auditar a VM do Windows impõe requisitos de complexidade de senha
- \]de visualização do \[: auditar a senha máxima da VM do Windows duração de 70 dias
- \]de visualização do \[: auditar a duração mínima da senha do Windows da VM 1 dia
- \[\]de visualização: auditar senhas de VM do Windows deve ter pelo menos 14 caracteres
- \]de visualização do \[: auditar a VM do Windows não deve permitir 24 senhas anteriores

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>Uma política. 10.1.1 sobre o uso de controles de criptografia

Este projeto ajuda a impor a política sobre o uso de controles cryptograph atribuindo 13 definições de [Azure Policy](../../../policy/overview.md) que impõem controles cryptograph específicos e auditam o uso de configurações de criptografia fracas.
Entender onde os recursos do Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar medidas corretivas para garantir que os recursos sejam configurados de acordo com sua política de segurança de informações. Especificamente, as políticas atribuídas por este projeto exigem criptografia para contas de armazenamento de BLOBs e contas de armazenamento do data Lake; exigir Transparent Data Encryption em bancos de dados SQL; auditar criptografia ausente em contas de armazenamento, bancos de dados SQL, discos de máquina virtual e variáveis de conta de automação; auditar conexões inseguras para contas de armazenamento, aplicativos de funções, aplicativos Web, aplicativos de API e cache Redis; auditar a criptografia de senha de máquina virtual fraca; e auditar a comunicação de Service Fabric não criptografada.

- \[visualização\]: auditoria de acesso somente HTTPS para um Aplicativo de funções
- \]de visualização de \[: auditoria de acesso somente HTTPS para um aplicativo Web
- \]de visualização de \[: auditoria de acesso somente HTTPS para um aplicativo de API
- \]de visualização de \[: auditoria de criptografia de blob ausente para contas de armazenamento
- \]de visualização de \[: implantar a extensão de VM para auditar a VM do Windows não deve armazenar senhas usando criptografia reversível
- \[\]de visualização: auditar a VM do Windows não deve armazenar senhas usando criptografia reversível
- \]de visualização de \[: monitorar discos de VM não criptografados na central de segurança do Azure
- Habilitação de auditoria de criptografia de variáveis de conta de automação
- Auditoria de habilitação de somente conexões seguras para o cache Redis
- Auditar a transferência segura para contas de armazenamento
- Auditar a configuração da propriedade ClusterProtectionLevel para EncryptAndSign em Service Fabric
- Auditar o estado de encriptação de dados transparente
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas

## <a name="a1241-event-logging"></a>Um log de eventos. 12.4.1

Este projeto ajuda a garantir que os eventos do sistema sejam registrados, atribuindo sete definições de [Azure Policy](../../../policy/overview.md) que auditam as configurações de log nos recursos do Azure.
Os logs de diagnóstico fornecem informações sobre as operações que foram executadas nos recursos do Azure.

- \[\]de visualização: auditoria de Dependency Agent implantação – imagem de VM (SO) não listada
- \[visualização\]: auditoria Dependency Agent implantação no VMSS-imagem da VM (SO) não listada
- \[visualização\]: auditoria Log Analytics implantação do agente – imagem da VM (SO) não listada
- \[visualização\]: auditoria Log Analytics implantação do agente em VMSS-imagem da VM (SO) não listada
- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server

## <a name="a1243-administrator-and-operator-logs"></a>Um administrador. 12.4.3 e logs de operador

Este projeto ajuda a garantir que os eventos do sistema sejam registrados, atribuindo sete definições de Azure Policy que auditam as configurações de log nos recursos do Azure. Os logs de diagnóstico fornecem informações sobre as operações que foram executadas nos recursos do Azure.

- \[\]de visualização: auditoria de Dependency Agent implantação – imagem de VM (SO) não listada
- \[visualização\]: auditoria Dependency Agent implantação no VMSS-imagem da VM (SO) não listada
- \[visualização\]: auditoria Log Analytics implantação do agente – imagem da VM (SO) não listada
- \[visualização\]: auditoria Log Analytics implantação do agente em VMSS-imagem da VM (SO) não listada
- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server

## <a name="a1244-clock-synchronization"></a>Uma sincronização de relógio. 12.4.4

Este projeto ajuda a garantir que os eventos do sistema sejam registrados, atribuindo sete definições de Azure Policy que auditam as configurações de log nos recursos do Azure. Os logs do Azure dependem de relógios internos sincronizados para criar um registro de eventos correlacionado ao tempo entre recursos.

- \[\]de visualização: auditoria de Dependency Agent implantação – imagem de VM (SO) não listada
- \[visualização\]: auditoria Dependency Agent implantação no VMSS-imagem da VM (SO) não listada
- \[visualização\]: auditoria Log Analytics implantação do agente – imagem da VM (SO) não listada
- \[visualização\]: auditoria Log Analytics implantação do agente em VMSS-imagem da VM (SO) não listada
- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server

## <a name="a1251-installation-of-software-on-operational-systems"></a>Uma instalação do. 12.5.1 do software em sistemas operacionais

O controle de aplicativo adaptável é uma solução da central de segurança do Azure que ajuda a controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Este projeto atribui uma definição de Azure Policy que monitora as alterações no conjunto de aplicativos permitidos. Esse recurso ajuda a controlar a instalação de software e aplicativos em VMs do Azure.

- \[visualização\]: monitorar a lista de permissões de aplicativos possíveis na central de segurança do Azure

## <a name="a1261-management-of-technical-vulnerabilities"></a>A. 12.6.1 gerenciamento de vulnerabilidades técnicas

Este projeto ajuda a gerenciar as vulnerabilidades do sistema de informações atribuindo cinco definições de [Azure Policy](../../../policy/overview.md) que monitoram atualizações de sistema ausentes, vulnerabilidades de sistema operacional, vulnerabilidades de SQL e vulnerabilidades de máquina virtual na central de segurança do Azure. A central de segurança do Azure fornece recursos de relatório que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados.

- \]de visualização de \[: monitorar Endpoint Protection ausente na central de segurança do Azure
- \]de visualização de \[: monitorar atualizações de sistema ausentes na central de segurança do Azure
- \]de visualização do \[: monitorar vulnerabilidades do sistema operacional na central de segurança do Azure
- \]de visualização do \[: monitorar resultados da avaliação de vulnerabilidade do SQL na central de segurança do Azure
- \]de visualização do \[: monitorar vulnerabilidades da VM na central de segurança do Azure

## <a name="a1262-restrictions-on-software-installation"></a>A. 12.6.2 restrições na instalação do software

O controle de aplicativo adaptável é uma solução da central de segurança do Azure que ajuda a controlar quais aplicativos podem ser executados em suas VMs localizadas no Azure. Este projeto atribui uma definição de Azure Policy que monitora as alterações no conjunto de aplicativos permitidos. As restrições de instalação de software podem ajudá-lo a reduzir a probabilidade de introdução de vulnerabilidades de software.

- \[visualização\]: monitorar a lista de permissões de aplicativos possíveis na central de segurança do Azure

## <a name="a1311-network-controls"></a>Controles de rede A. 13.1.1

Este projeto ajuda você a gerenciar e controlar redes atribuindo uma definição de [Azure Policy](../../../policy/overview.md) que monitora grupos de segurança de rede com regras permissivas. As regras que são muito permissivas podem permitir o acesso à rede indesejado e devem ser examinadas. Esse projeto também atribui três definições de Azure Policy que monitoram pontos de extremidade desprotegidos, aplicativos e contas de armazenamento. Pontos de extremidade e aplicativos que não são protegidos por um firewall e contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- \]de visualização do \[: monitorar o acesso à rede permissivo na central de segurança do Azure
- \]de visualização do \[: monitorar pontos de extremidade de rede desprotegidos na central de segurança do Azure
- \]de visualização do \[: monitorar aplicativo Web desprotegido na central de segurança do Azure
- Auditar o acesso irrestrito à rede para contas de armazenamento

## <a name="a1321-information-transfer-policies-and-procedures"></a>A. 13.2.1 procedimentos e políticas de transferência de informações

O plano gráfico ajuda a garantir que a transferência de informações com os serviços do Azure seja segura atribuindo duas definições de [Azure Policy](../../../policy/overview.md) para auditar conexões inseguras a contas de armazenamento e cache Redis.

- Auditoria de habilitação de somente conexões seguras para o cache Redis
- Auditar a transferência segura para contas de armazenamento

## <a name="next-steps"></a>Passos seguintes

Agora que você analisou o mapeamento de controle do plano gráfico de serviços compartilhados ISO 27001, visite os seguintes artigos para saber mais sobre a arquitetura e como implantar esse exemplo:

> [!div class="nextstepaction"]
> [Plano de serviços compartilhados iso 27001-visão geral](./index.md)
> [especificação dos serviços compartilhados ISO 27001 – implantar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).