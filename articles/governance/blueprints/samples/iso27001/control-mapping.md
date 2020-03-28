---
title: Controlos de amostras de plantas ISO 27001
description: Mapeamento de controlo da amostra de plantas ISO 27001. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 501884491dbef85cdf8a29cb5fdcef44a68235a9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920572"
---
# <a name="control-mapping-of-the-iso-27001-blueprint-sample"></a>Mapeamento de controlo da amostra de plantas ISO 27001

O seguinte artigo detalha como os mapas da amostra de plantas Azure ISO 27001 para os controlos ISO 27001. Para obter mais informações sobre os controlos, consulte [ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos são para os controlos **ISO 27001:2013.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione os ** \[controlos ISO 27001:2013 da Auditoria de Pré-visualização\] e implemente extensões vm específicas para apoiar os requisitos** de auditoria iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Segregação de funções

Ter apenas um proprietário de assinatura Azure não permite o despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições do Azure pode aumentar o potencial de violação através de uma conta de proprietário comprometida. Este plano ajuda-o a manter um número adequado de proprietários de subscrições Azure, atribuindo duas definições de [Política Azure](../../../policy/overview.md) que auditam o número de proprietários para subscrições do Azure. Gerir as permissões do proprietário da subscrição pode ajudá-lo a implementar a separação adequada de deveres.

- \[Pré-visualização\]: Auditar número mínimo de proprietários para uma subscrição
- \[Pré-visualização\]: Auditar o número máximo de proprietários para uma subscrição

## <a name="a821-classification-of-information"></a>A.8.2.1 Classificação da informação

O serviço de Avaliação de [VulnerabilidadeS SQL](https://docs.microsoft.com/azure/sql-database/sql-vulnerability-assessment) da Azure pode ajudá-lo a descobrir dados sensíveis armazenados nas suas bases de dados e inclui recomendações para classificar esses dados. Este projeto atribui uma definição de [Política Azure](../../../policy/overview.md) para auditar que as vulnerabilidades identificadas durante a avaliação de vulnerabilidade sql são remediadas.

- \[Pré-visualização\]: Monitor SQL resultados de avaliação de vulnerabilidade no Centro de Segurança Azure

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Acesso a redes e serviços de rede

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para gerir quem tem acesso aos recursos do Azure. Este plano ajuda-o a controlar o acesso aos recursos do Azure, atribuindo sete definições de [Política Azure.](../../../policy/overview.md) Estas políticas auditam a utilização de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos.
Compreender os recursos que violam estas políticas pode ajudá-lo a tomar medidas corretivas para garantir o acesso aos recursos do Azure é restrito aos utilizadores autorizados.

- \[Pré-visualização\]: Implementar extensão VM para auditar contas De VM Linux sem senhas
- \[Pré-visualização\]: Implementar extensão VM para auditar Linux VM permitindo ligações remotas a partir de contas sem senhas
- \[Pré-visualização\]: Audite contas VM Linux sem senhas
- \[Pré-visualização\]: AuditLin VM permitindo ligações remotas a partir de contas sem senhas
- Utilização auditada de contas de armazenamento clássicas
- Utilização de auditoria de máquinas virtuais clássicas
- VMs de auditoria que não usam discos geridos

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gestão dos direitos de acesso privilegiados

Este plano ajuda-o a restringir e controlar direitos de acesso privilegiados, atribuindo quatro definições de [Política Azure](../../../policy/overview.md) para auditar contas externas com o proprietário e/ou escrever permissões e contas com permissões de proprietário e/ou escrever que não tenham autenticação multifactorativa. O Azure implementa o controlo de acesso baseado em funções (RBAC) para gerir quem tem acesso aos recursos do Azure. Esta planta atribui ainda três definições de Política Azure para auditar a utilização da autenticação do Diretório Ativo Azure para servidores SQL e Tecido de Serviço. A utilização da autenticação do Diretório Ativo Azure permite a gestão simplificada da permissão e a gestão centralizada da identidade dos utilizadores de bases de dados e de outros serviços da Microsoft. Este projeto atribui também uma definição de Política Azure para auditar a utilização de regras rbac personalizadas. Compreender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do RBAC são propensas a erros.

- \[Pré-visualização\]: Contas de auditoria com permissões do proprietário que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Contas de auditoria com permissões de escrita que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Audite contas externas com permissões do proprietário numa subscrição
- \[Pré-visualização\]: Audite contas externas com permissões de escrita numa subscrição
- Fornecimento de auditoria de um administrador de Diretório Ativo Azure para servidor SQL
- Auditoria utilização do Diretório Ativo Azure para autenticação de clientes em Tecido de Serviço
- Utilização de auditoria de regras rbac personalizadas

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gestão de informações secretas de autenticação dos utilizadores

Este projeto atribui três definições de [Política Azure](../../../policy/overview.md) para contas de auditoria que não têm autenticação multifactor ativada. A autenticação de vários fatores ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação seja comprometida. Ao monitorizar contas sem autenticação multifactor ativada, pode identificar contas que possam ser mais suscetíveis de serem comprometidas. Esta planta também atribui duas definições de Política Azure que auditam permissões de ficheiros de senha SléLux VM para alertar se são definidas incorretamente. Esta configuração permite-lhe tomar medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[Pré-visualização\]: Contas de auditoria com permissões do proprietário que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Contas de auditoria com permissões de leitura que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Contas de auditoria com permissões de escrita que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Implementar extensão VM para auditar permissões de ficheiros passwd Linux VM
- \[Pré-visualização\]: Audit Linux VM /etc/passwd file permissions estão definidos para 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revisão dos direitos de acesso dos utilizadores

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui quatro definições de [Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas.

- \[Pré-visualização\]: Auditoria contas depreciadas numa subscrição
- \[Pré-visualização\]: Auditoria contas depreciadas com permissões do proprietário numa subscrição
- \[Pré-visualização\]: Audite contas externas com permissões do proprietário numa subscrição
- \[Pré-visualização\]: Audite contas externas com permissões de escrita numa subscrição

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Remoção ou ajustamento dos direitos de acesso

O Azure implementa [o controlo de acesso baseado em funções](../../../../role-based-access-control/overview.md) (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando [o Azure Ative Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e o RBAC, pode atualizar as funções dos utilizadores para refletir as alterações organizacionais. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de [Política Azure](../../../policy/overview.md) para auditoria de conta depreciada que deve ser considerada para remoção.

- \[Pré-visualização\]: Auditoria contas depreciadas numa subscrição
- \[Pré-visualização\]: Auditoria contas depreciadas com permissões do proprietário numa subscrição

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Procedimentos de login seguros

Este projeto atribui três definições de Política Azure para contas de auditoria que não têm autenticação multifactor ativada. A Autenticação Multi-Factor Azure proporciona segurança adicional, exigindo uma segunda forma de autenticação e entrega ndo autenticação forte. Ao monitorizar contas sem autenticação multifactor ativada, pode identificar contas que possam ser mais suscetíveis de serem comprometidas.

- \[Pré-visualização\]: Contas de auditoria com permissões do proprietário que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Contas de auditoria com permissões de leitura que não são MFA habilitadas numa subscrição
- \[Pré-visualização\]: Contas de auditoria com permissões de escrita que não são MFA habilitadas numa subscrição

## <a name="a943-password-management-system"></a>A.9.4.3 Sistema de gestão de passwords

Este plano ajuda-o a impor senhas fortes atribuindo 10 definições de [Política Azure](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A consciência dos VMs em violação da política de força da palavra-passe ajuda-o a tomar medidas corretivas para garantir que as palavras-passe para todas as contas de utilizadores vm estão em conformidade com a política.

- \[Pré-visualização\]: Implementar extensão VM para auditoria Windows VM aplica requisitos de complexidade de passwords
- \[Pré-visualização\]: Implementar extensão VM para auditar idade máxima de senha do Windows VM 70 dias
- \[Pré-visualização\]: Implementar extensão VM para auditar a idade mínima de senha do Windows VM 1 dia
- \[Pré-visualização\]: Implementar extensão VM para auditar palavras-passe do Windows VM deve ter pelo menos 14 caracteres
- \[Pré-visualização\]: Implementar extensão VM para auditar Windows VM não deve permitir 24 senhas anteriores
- \[Pré-visualização\]: Auditoria Windows VM aplica requisitos de complexidade de senhas
- \[Pré-visualização\]: Auditwindows VM idade máxima máxima 70 dias
- \[Pré-visualização\]: Auditwindows VM idade mínima 1 dia
- \[Pré-visualização\]: Auditwindows VM palavras-passe devem ter pelo menos 14 caracteres
- \[Pré-visualização\]: AuditWindows VM não deve permitir 24 senhas anteriores

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Política sobre a utilização de controlos criptográficos

Esta planta ajuda-o a impor a sua política sobre a utilização de controlos criptógrafos, atribuindo 13 definições de [Política Azure](../../../policy/overview.md) que impõem controlos criptográficos específicos e o uso de auditoria de configurações criptográficas fracas.
Compreender onde os seus recursos Azure podem ter configurações criptográficas não ótimas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos estão configurados de acordo com a sua política de segurança de informação. Especificamente, as políticas atribuídas por esta planta requerem encriptação para contas de armazenamento de blob e contas de armazenamento de dados; requerem encriptação transparente de dados nas bases de dados SQL; auditoria falta de encriptação em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação; auditar ligações inseguras a contas de armazenamento, Aplicações de Função, Web App, Aplicações API e Redis Cache; auditar encriptação fraca da palavra-passe de máquina virtual; e auditar comunicação não encriptada do Tecido de Serviço.

- \[Pré-visualização\]: Audit HTTPS só acesso a uma App de Funções
- \[Pré-visualização\]: Audit HTTPS só acesso a uma Aplicação Web
- \[Pré-visualização\]: Audit HTTPS só acesso a uma app API
- \[Pré-visualização\]: Auditoria em falta de encriptação blob para contas de armazenamento
- \[Pré-visualização\]: Implementar extensão VM para auditar Windows VM não deve armazenar palavras-passe usando encriptação reversível
- \[Pré-visualização\]: Auditwindows VM não deve armazenar palavras-passe usando encriptação reversível
- \[Pré-visualização\]: Monitor enão encriptado Discos VM no Centro de Segurança Azure
- Habilitação de auditoria de encriptação de variáveis de conta de automação
- Auditoria que permite apenas ligações seguras ao seu Redis Cache
- Auditoria transferência segura para contas de armazenamento
- Audite a definição da propriedade ClusterProtectionLevel para encriptar AndSign em tecido de serviço
- Auditar o estado de encriptação de dados transparente
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="a1241-event-logging"></a>A.12.4.1 Registo de registo saqueado

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo sete definições de [Política Azure](../../../policy/overview.md) que auditam as definições de registo nos recursos do Azure.
Os registos de diagnóstico fornecem informações sobre as operações que foram realizadas dentro dos recursos do Azure.

- \[Pré-visualização\]: Implementação do Agente de Dependência da Auditoria - Imagem VM (OS) não cotada
- \[Pré-visualização\]: Implementação de Agente de Dependência de Auditoria em VMSS - Imagem VM (OS) não cotada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não listada
- Definição de diagnóstico de auditoria
- Audite as definições de auditoria ao nível do servidor SQL
- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Registos de administrador e operador

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo sete definições de Política Azure que auditam as definições de registo nos recursos do Azure. Os registos de diagnóstico fornecem informações sobre as operações que foram realizadas dentro dos recursos do Azure.

- \[Pré-visualização\]: Implementação do Agente de Dependência da Auditoria - Imagem VM (OS) não cotada
- \[Pré-visualização\]: Implementação de Agente de Dependência de Auditoria em VMSS - Imagem VM (OS) não cotada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não listada
- Definição de diagnóstico de auditoria
- Audite as definições de auditoria ao nível do servidor SQL
- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Sincronização do relógio

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo sete definições de Política Azure que auditam as definições de registo nos recursos do Azure. Os registos azure dependem de relógios internos sincronizados para criar um registo de eventos correlacionados com o tempo em todos os recursos.

- \[Pré-visualização\]: Implementação do Agente de Dependência da Auditoria - Imagem VM (OS) não cotada
- \[Pré-visualização\]: Implementação de Agente de Dependência de Auditoria em VMSS - Imagem VM (OS) não cotada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria - Imagem VM (OS) não listada
- \[Pré-visualização\]: Implementação de agente de análise de registo de auditoria em VMSS - Imagem VM (OS) não listada
- Definição de diagnóstico de auditoria
- Audite as definições de auditoria ao nível do servidor SQL
- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Instalação de software em sistemas operacionais

O controlo adaptativo da aplicação é a solução do Azure Security Center que o ajuda a controlar quais as aplicações que podem ser executadas nos seus VMs localizados em Azure. Este projeto atribui uma definição de Política Azure que monitoriza as alterações ao conjunto de aplicações permitidas. Esta capacidade ajuda-o a controlar a instalação de software e aplicações em VMs Azure.

- \[Pré-visualização\]: Monitorize possível aplicação Whitelisting no Azure Security Center

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Gestão de vulnerabilidades técnicas

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo cinco definições de [Política Azure](../../../policy/overview.md) que monitorizam as atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos azure implantados.

- \[Pré-visualização\]: Monitor desaparecido Proteção de ponto final no Centro de Segurança Azure
- \[Pré-visualização\]: Monitor de si em falta atualizações do sistema no Centro de Segurança do Azure
- \[Pré-visualização\]: Monitorize as vulnerabilidades do OS no Centro de Segurança Azure
- \[Pré-visualização\]: Monitor SQL resultados de avaliação de vulnerabilidade no Centro de Segurança Azure
- \[Pré-visualização\]: Monitor De vulnerabilidades VM no Centro de Segurança Azure

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrições à instalação de software

O controlo adaptativo da aplicação é a solução do Azure Security Center que o ajuda a controlar quais as aplicações que podem ser executadas nos seus VMs localizados em Azure. Este projeto atribui uma definição de Política Azure que monitoriza as alterações ao conjunto de aplicações permitidas. Restrições à instalação de software podem ajudá-lo a reduzir a probabilidade de introdução de vulnerabilidades de software.

- \[Pré-visualização\]: Monitorize possível aplicação Whitelisting no Azure Security Center

## <a name="a1311-network-controls"></a>A.13.1.1 Controlos de rede

Este plano ajuda-o a gerir e controlar redes atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza os grupos de segurança da rede com regras permissivas. Regras demasiado permissivas podem permitir o acesso não intencional à rede e devem ser revistas. Este projeto também atribui três definições de Política Azure que monitorizam pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- \[Pré-visualização\]: Monitor acesso permissivo à rede no Centro de Segurança Azure
- \[Pré-visualização\]: Monitorize pontos finais de rede desprotegidos no Centro de Segurança Azure
- \[Pré-visualização\]: Monitorize aplicação web desprotegida no Centro de Segurança Azure
- Auditoria sem restrições ao acesso da rede às contas de armazenamento

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Políticas e procedimentos de transferência de informação

O plano ajuda-o a garantir que a transferência de informação com os serviços Azure é segura, atribuindo duas definições de [Política Azure](../../../policy/overview.md) para auditar ligações inseguras a contas de armazenamento e Redis Cache.

- Auditoria que permite apenas ligações seguras ao seu Redis Cache
- Auditoria transferência segura para contas de armazenamento

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo do projeto ISO 27001, visite os seguintes artigos para saber sobre a arquitetura e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Iso 27001 blueprint - Visão geral](./index.md)
> [ISO 27001 - Desdobrar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).