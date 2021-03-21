---
title: ISO 27001 Serviços Partilhados controlos de amostragem
description: Controlo do mapeamento da amostra de projeto iso 27001 dos Serviços Partilhados. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 64266e72bf2aa24ac7fbf70f87f27739709fa0be
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626346"
---
# <a name="control-mapping-of-the-iso-27001-shared-services-blueprint-sample"></a>Mapeamento de controlo da amostra de projeto iso 27001 dos serviços partilhados

O artigo seguinte detalha como o Azure Blueprints ISO 27001 Shared Services mapeia mapas de amostras para os controlos ISO 27001. Para obter mais informações sobre os controlos, consulte [a ISO 27001](https://www.iso.org/isoiec-27001-information-security.html).

Os seguintes mapeamentos são para os controlos **ISO 27001:2013.** Utilize a navegação no direito de saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa [da Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione os **\[ \] controlos iso 27001:2013 da Auditoria de Pré-visualização e implemente extensões VM específicas para apoiar os requisitos** de auditoria integrados na iniciativa política.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/iso27001-shared/control-mapping.md).

## <a name="a612-segregation-of-duties"></a>A.6.1.2 Segregação de funções

Ter apenas um proprietário de assinatura Azure não permite despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições Azure pode aumentar o potencial de uma violação através de uma conta de proprietário comprometida. Este projeto ajuda-o a manter um número adequado de proprietários de subscrições da Azure, atribuindo duas definições [da Azure Policy](../../../policy/overview.md) que auditam o número de proprietários para subscrições Azure. Gerir permissões de proprietário de subscrição pode ajudá-lo a implementar a separação adequada de deveres.

- Um máximo de 3 proprietários deve ser designado para a sua subscrição
- Deve haver mais de um proprietário atribuído à sua subscrição

## <a name="a821-classification-of-information"></a>A.8.2.1 Classificação da informação

O serviço de Avaliação de [Vulnerabilidades SQL](../../../../azure-sql/database/sql-vulnerability-assessment.md) da Azure pode ajudá-lo a descobrir dados sensíveis armazenados nas suas bases de dados e inclui recomendações para classificar esses dados. Esta planta atribui uma definição [de Política Azure](../../../policy/overview.md) à auditoria de que as vulnerabilidades identificadas durante a avaliação da vulnerabilidade SQL são remediadas.

- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas

## <a name="a912-access-to-networks-and-network-services"></a>A.9.1.2 Acesso a redes e serviços de rede

[O controlo de acesso baseado em funções Azure (Azure RBAC)](../../../../role-based-access-control/overview.md) ajuda a gerir quem tem acesso aos recursos Azure. Esta planta ajuda-o a controlar o acesso aos recursos da Azure, atribuindo sete definições [de Política Azure.](../../../policy/overview.md) Estas políticas auditam a utilização de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos.
Compreender os recursos que estão a violar estas políticas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos Azure de acesso são restritos aos utilizadores autorizados.

- Mostrar resultados de auditoria de VMs Linux que têm contas sem senhas
- Mostrar resultados de auditoria de VMs Linux que permitem ligações remotas a partir de contas sem senhas
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager
- VMs de auditoria que não usam discos geridos

## <a name="a923-management-of-privileged-access-rights"></a>A.9.2.3 Gestão de direitos de acesso privilegiados

Este projeto ajuda-o a restringir e a controlar os direitos de acesso privilegiados, atribuindo quatro definições [de Política Azure](../../../policy/overview.md) para auditar contas externas com o proprietário e/ou escrever permissões e contas com o proprietário e/ou escrever permissões que não têm autenticação de vários fatores ativadas. O controlo de acesso baseado em funções Azure (Azure RBAC) ajuda a gerir quem tem acesso aos recursos Azure. Esta planta também atribui três definições de Política Azure para auditar a utilização da autenticação do Azure Ative Directory para servidores SQL e Tecido de Serviço. A utilização da autenticação do Azure Ative Directory permite uma gestão simplificada da permissão e uma gestão centralizada da identidade dos utilizadores de bases de dados e outros serviços da Microsoft. Esta planta também atribui uma definição de Política Azure para auditar o uso de regras Azure RBAC personalizadas. Compreender onde as regras Azure RBAC personalizadas são implementadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do Azure RBAC são propensas a erros.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- A MFA deve ser ativada em contas com permissões de escrita na sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL
- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente
- Auditar o uso das regras personalizadas do RBAC

## <a name="a924-management-of-secret-authentication-information-of-users"></a>A.9.2.4 Gestão de informação secreta de autenticação dos utilizadores

Este projeto atribui três definições [de Política Azure](../../../policy/overview.md) a contas de auditoria que não têm autenticação multi-factor ativada. A autenticação multi-factor ajuda a manter as contas seguras mesmo que uma peça de informação de autenticação esteja comprometida. Ao monitorizar contas sem autenticação multi-factor ativada, pode identificar contas que possam ser mais comprometidas. Esta planta também atribui duas definições de Política Azure que auditam permissões de ficheiros de senha Linux VM para alertar se estiverem definidas incorretamente. Esta configuração permite-lhe tomar medidas corretivas para garantir que os autenticadores não estão comprometidos.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- A MFA deve ser ativada em contas com permissões de escrita na sua subscrição
- Mostre os resultados da auditoria dos VMs Linux que não têm as permissões de ficheiros passwd definidas para 0644

## <a name="a925-review-of-user-access-rights"></a>A.9.2.5 Revisão dos direitos de acesso ao utilizador

[O controlo de acesso baseado em funções (Azure RBAC) ajuda-o](../../../../role-based-access-control/overview.md) a gerir quem tem acesso a recursos em Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Este projeto atribui quatro definições [de Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão, incluindo contas amortizadas e contas externas com permissões elevadas.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

## <a name="a926-removal-or-adjustment-of-access-rights"></a>A.9.2.6 Remoção ou ajustamento dos direitos de acesso

[O controlo de acesso baseado em funções (Azure RBAC) ajuda-o](../../../../role-based-access-control/overview.md) a gerir quem tem acesso a recursos em Azure. Utilizando [o Azure Ative Directory](../../../../active-directory/fundamentals/active-directory-whatis.md) e o Azure RBAC, pode atualizar as funções dos utilizadores para refletir as alterações organizacionais. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que elimina imediatamente os direitos de acesso aos recursos da Azure. Este projeto atribui duas definições [da Política Azure](../../../policy/overview.md) à auditoria de conta amortizada que deve ser considerada para remoção.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="a942-secure-log-on-procedures"></a>A.9.4.2 Procedimentos de início de sessão seguros

Este projeto atribui três definições de Política Azure a contas de auditoria que não têm autenticação multi-factor ativada. A Azure AD Multi-Factor Authentication proporciona segurança adicional exigindo uma segunda forma de autenticação e proporciona uma autenticação forte. Ao monitorizar contas sem autenticação multi-factor ativada, pode identificar contas que possam ser mais comprometidas.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- A MFA deve ser ativada em contas com permissões de escrita na sua subscrição

## <a name="a943-password-management-system"></a>Sistema de gestão de palavras-passe A.9.4.3

Este plano ajuda-o a impor senhas fortes atribuindo 10 definições [de Política Azure](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A sensibilização dos VMs em violação da política de força de senha ajuda-o a tomar ações corretivas para garantir que as palavras-passe de todas as contas de utilizador VM estão em conformidade com a política.

- Mostrar os resultados da auditoria dos VM do Windows que não têm a definição de complexidade da palavra-passe ativada
- Mostrar resultados de auditoria de VMs do Windows que não têm uma idade máxima de senha de 70 dias
- Mostrar os resultados da auditoria dos VM do Windows que não têm uma idade mínima de senha de 1 dia
- Mostrar os resultados da auditoria dos VM do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- Mostrar resultados de auditoria a VMs do Windows que permitem a reutilização das 24 palavras-passe anteriores

## <a name="a1011-policy-on-the-use-of-cryptographic-controls"></a>A.10.1.1 Política sobre a utilização de controlos criptográficos

Esta planta ajuda-o a impor a sua política sobre a utilização de controlos criptógrafos, atribuindo 13 definições [de Política Azure](../../../policy/overview.md) que impõem controlos criptógrafos específicos e auditam o uso de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos são configurados de acordo com a sua política de segurança de informação. Especificamente, as políticas atribuídas por esta planta requerem encriptação para contas de armazenamento de bolhas e contas de armazenamento de lago de dados; requerer encriptação de dados transparentes nas bases de dados SQL; auditoria em falta de encriptação em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação; ligações inseguras de auditoria a contas de armazenamento, Apps de Função, Web App, API Apps e Redis Cache; encriptação de senha de máquina virtual fraca; e auditoria comunicação de tecido de serviço não encriptado.

- A App de função só deve estar acessível através do HTTPS
- A Aplicação Web só deve ser acessível em HTTPS
- A API App só deve estar acessível em HTTPS
- Mostrar resultados de auditoria de VMs do Windows que não armazenam senhas usando encriptação reversível
- A encriptação do disco deve ser aplicada em máquinas virtuais
- As variáveis de conta de automação devem ser encriptadas
- Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis
- A transferência segura para contas de armazenamento deve ser ativada
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign
- A encriptação transparente de dados nas bases de dados SQL deve ser ativada

## <a name="a1241-event-logging"></a>A.12.4.1 Registo de eventos

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo sete definições [de Política Azure](../../../policy/overview.md) que auditam as definições de registo nos recursos do Azure.
Os registos de diagnóstico fornecem informações sobre as operações que foram realizadas dentro dos recursos da Azure.

- Implementação de agente de dependência de auditoria - VM Image (OS) não listado
- Implementação de agente de dependência de auditoria em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- [Pré-visualização]: Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- Definição de diagnóstico de auditoria
- A auditoria no servidor SQL deve ser ativada

## <a name="a1243-administrator-and-operator-logs"></a>A.12.4.3 Registos de administrador e operador

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo sete definições de Política Azure que auditam as definições de registo nos recursos do Azure. Os registos de diagnóstico fornecem informações sobre as operações que foram realizadas dentro dos recursos da Azure.

- Implementação de agente de dependência de auditoria - VM Image (OS) não listado
- Implementação de agente de dependência de auditoria em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- [Pré-visualização]: Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- Definição de diagnóstico de auditoria
- A auditoria no servidor SQL deve ser ativada

## <a name="a1244-clock-synchronization"></a>A.12.4.4 Sincronização do relógio

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo sete definições de Política Azure que auditam as definições de registo nos recursos do Azure. Os registos Azure dependem de relógios internos sincronizados para criar um registo correlacionado com o tempo de eventos entre recursos.

- Implementação de agente de dependência de auditoria - VM Image (OS) não listado
- Implementação de agente de dependência de auditoria em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- [Pré-visualização]: Implementação do agente de análise de registo de auditoria - VM Image (OS) não listado
- Implementação de agente de auditação Log Analytics em conjuntos de escala de máquina virtual - VM Image (OS) não listado
- Definição de diagnóstico de auditoria
- A auditoria no servidor SQL deve ser ativada

## <a name="a1251-installation-of-software-on-operational-systems"></a>A.12.5.1 Instalação de software em sistemas operacionais

O controlo de aplicações adaptativas é a solução do Azure Security Center que o ajuda a controlar quais aplicações podem ser executadas nos seus VMs localizados em Azure. Esta planta atribui uma definição de Política Azure que monitoriza as alterações ao conjunto de aplicações permitidas. Esta capacidade ajuda-o a controlar a instalação de software e aplicações em VMs Azure.

- Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas

## <a name="a1261-management-of-technical-vulnerabilities"></a>A.12.6.1 Gestão de vulnerabilidades técnicas

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação, atribuindo cinco definições [de Política Azure](../../../policy/overview.md) que monitorizam atualizações de sistemas em falta, vulnerabilidades do sistema operativo, vulnerabilidades de SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados.

- Monitor que falta proteção de ponto final no Centro de Segurança Azure
- As atualizações de sistema devem ser instaladas nos seus computadores
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="a1262-restrictions-on-software-installation"></a>A.12.6.2 Restrições à instalação de software

O controlo de aplicações adaptativas é a solução do Azure Security Center que o ajuda a controlar quais aplicações podem ser executadas nos seus VMs localizados em Azure. Esta planta atribui uma definição de Política Azure que monitoriza as alterações ao conjunto de aplicações permitidas. As restrições à instalação de software podem ajudá-lo a reduzir a probabilidade de introdução de vulnerabilidades de software.

- Os controlos de aplicação adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas

## <a name="a1311-network-controls"></a>A.13.1.1 Controlos de rede

Este projeto ajuda-o a gerir e a controlar redes atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza grupos de segurança de rede com regras permissivas. As regras demasiado permissivas podem permitir o acesso não intencional à rede e devem ser revistas. Esta planta também atribui três definições de Política Azure que monitorizam pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- O acesso através da Internet face ao ponto final deve ser restringido
- As contas de armazenamento devem restringir o acesso à rede

## <a name="a1321-information-transfer-policies-and-procedures"></a>A.13.2.1 Políticas e procedimentos de transferência de informação

O plano ajuda-o a garantir que a transferência de informação com os serviços Azure é segura, atribuindo duas definições [de Política Azure](../../../policy/overview.md) para auditar ligações inseguras às contas de armazenamento e à Cache Redis.

- Apenas devem ser ativadas ligações seguras à sua Cache Azure para Redis
- A transferência segura para contas de armazenamento deve ser ativada

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo do projeto iso 27001 Shared Services, visite os seguintes artigos para saber sobre a arquitetura e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Projeto de serviços partilhados ISO 27001 - Visão geral](./index.md) 
>  [Projeto iso 27001 Serviços Partilhados - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
