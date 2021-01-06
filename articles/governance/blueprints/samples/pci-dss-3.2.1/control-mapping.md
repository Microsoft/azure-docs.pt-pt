---
title: PCI-DSS v3.2.1 controlos de amostras de plantas
description: Controlo do mapeamento da norma de segurança de dados da indústria de cartões de pagamento v3.2.1 amostra de planta para Azure Policy e Azure RBAC.
ms.date: 08/19/2020
ms.topic: sample
ms.openlocfilehash: 7391b32b23f6f0fb8b337a8178b83cffd7dba69b
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955693"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapeamento de controlo da amostra de planta PCI-DSS v3.2.1

O artigo seguinte detalha como o Azure Blueprints PCI-DSS v3.2.1 blueprint mapas de amostras para os controlos PCI-DSS v3.2.1. Para obter mais informações sobre os controlos, consulte [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Os seguintes mapeamentos são para os controlos **PCI-DSS v3.2.1:2018.** Utilize a navegação no direito de saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa [da Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione a iniciativa política incorporada **PCI v3.2.1:2018.**

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 Proteção das Fronteiras

Este projeto ajuda-o a gerir e a controlar redes atribuindo definições [de Política Azure](../../../policy/overview.md) que monitoriza grupos de segurança de rede com regras permissivas. As regras demasiado permissivas podem permitir o acesso não intencional à rede e devem ser revistas. Esta planta atribui uma definição de Política Azure que monitoriza pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- Auditoria acesso ilimitado à rede a contas de armazenamento
- O acesso através da Internet face ao ponto final deve ser restringido

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 Proteção Criptográfica

Esta planta ajuda-o a aplicar a sua política com a utilização de controlos criptógrafos, atribuindo definições [de Política de Azure](../../../policy/overview.md) que impõem controlos criptógrafos específicos e auditam o uso de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos são configurados de acordo com a sua política de segurança de informação. Especificamente, as políticas atribuídas por esta planta requerem encriptação transparente de dados nas bases de dados SQL; auditoria falta de encriptação em contas de armazenamento e variáveis de conta de automação. Existem também políticas que abordam as ligações inseguras de auditoria a contas de armazenamento, Apps de Função, WebApp, Apps API e Redis Cache, e auditam comunicação de Tecido de Serviço não encriptada.

- A App de função só deve estar acessível através do HTTPS
- A Aplicação Web só deve ser acessível em HTTPS
- A API App só deve estar acessível em HTTPS
- A encriptação transparente de dados nas bases de dados SQL deve ser ativada
- A encriptação do disco deve ser aplicada em máquinas virtuais
- As variáveis de conta de automação devem ser encriptadas
- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- A transferência segura para contas de armazenamento deve ser ativada
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign
- A encriptação transparente de dados nas bases de dados SQL deve ser ativada
- Implementar encriptação de dados transparentes SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 e 11.2.1 Vulnerabilidade Scanning e Atualizações do Sistema

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam atualizações de sistemas em falta, vulnerabilidades do sistema operativo, vulnerabilidades de SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos Azure implantados.

- Monitor que falta proteção de ponto final no Centro de Segurança Azure
- Implementar extensão padrão do Microsoft IaaSAntimalware para o Windows Server
- Implementar deteção de ameaças em servidores SQL
- As atualizações de sistema devem ser instaladas nos seus computadores
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 Separação dos Direitos

Ter apenas um proprietário de assinatura Azure não permite despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições Azure pode aumentar o potencial de uma violação através de uma conta de proprietário comprometida. Este projeto ajuda-o a manter um número adequado de proprietários de subscrições da Azure, atribuindo definições [da Azure Policy](../../../policy/overview.md) que auditam o número de proprietários para subscrições Azure. Gerir permissões de proprietário de subscrição pode ajudá-lo a implementar a separação adequada de deveres.

- Deve haver mais de um proprietário atribuído à sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b Gestão dos Direitos de Acesso Privilegiados

Este projeto ajuda-o a restringir e a controlar os direitos de acesso privilegiados, atribuindo definições [da Azure Policy](../../../policy/overview.md) para auditar contas externas com o proprietário, escrever e/ou ler permissões e contas de funcionários com permissões de proprietário e/ou de escrever permissões que não têm autenticação de vários fatores ativadas. O controlo de acesso baseado em funções Azure (Azure RBAC) ajuda a gerir quem tem acesso aos recursos Azure. Compreender onde as regras Azure RBAC personalizadas são implementadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do Azure RBAC são propensas a erros. Esta planta também atribui definições [de Política Azure](../../../policy/overview.md) à utilização de auditação da autenticação do Azure Ative Directory para servidores SQL. A utilização da autenticação do Azure Ative Directory simplifica a gestão de permissões e centraliza a gestão de identidade dos utilizadores de bases de dados e de outros Microsoft  
services.
 
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- A MFA deve ser ativada em contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL
- Auditar o uso das regras personalizadas do RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 Menor Privilégio e Revisão dos Direitos de Acesso ao Utilizador

O controlo de acesso baseado em funções (Azure RBAC) ajuda-o a gerir quem tem acesso a recursos em Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Este projeto atribui definições [de Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão, incluindo contas amortizadas e contas externas com permissões elevadas.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Remoção ou Ajustamento dos Direitos de Acesso

O controlo de acesso baseado em funções (Azure RBAC) ajuda-o a gerir quem tem acesso a recursos em Azure. Utilizando o Azure Ative Directory e o Azure RBAC, pode atualizar as funções dos utilizadores para refletir as alterações organizacionais. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que elimina imediatamente os direitos de acesso aos recursos da Azure. Este projeto atribui definições [da Política Azure](../../../policy/overview.md) à auditoria de conta amortizada que deve ser considerada para remoção.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 Autenticação baseada em palavra-passe

Este plano ajuda-o a impor senhas fortes atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A sensibilização dos VMs em violação da política de força de senha ajuda-o a tomar ações corretivas para garantir que as palavras-passe de todas as contas de utilizador VM estão em conformidade com a política.

- \[Pré-visualização \] : Audit Windows VMs que não têm uma idade máxima de senha de 70 dias
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização \] : Audit Windows VMs que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização \] : Audit Windows VMs que permitem a reutilização das 24 palavras-passe anteriores
- \[Pré-visualização \] : Implementar requisitos para auditar VMs do Windows que permitam reutilizar as 24 palavras-passe anteriores

## <a name="103-and-1054-audit-generation"></a>10.3 e 10.5.4 Geração de Auditoria

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam as definições de registo nos recursos do Azure.
Os registos de diagnóstico fornecem informações sobre as operações que foram realizadas dentro dos recursos da Azure. Os registos Azure dependem de relógios internos sincronizados para criar um registo correlacionado com o tempo de eventos entre recursos.

- A auditoria deve ser ativada em definições avançadas de segurança de dados no SQL Server
- Definição de diagnóstico de auditoria
- Audit SQL nível de nível de auditoria Definições de auditoria
- Implementar auditorias em servidores SQL
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager

## <a name="1236-and-1237-information-security"></a>12.3.6 e 12.3.7 Segurança da Informação

Este projeto ajuda-o a gerir e controlar a sua rede, atribuindo definições [da Azure Policy](../../../policy/overview.md) que auditam as localizações aceitáveis da rede e os produtos da empresa aprovados permitidos para o ambiente. Estes são personalizáveis por cada empresa através dos parâmetros políticos dentro de cada uma destas políticas.

- Localizações permitidas
- Locais permitidos para grupos de recursos

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo do projeto PCI-DSS v3.2.1, visite os seguintes artigos para saber mais sobre a visão geral e como implementar esta amostra:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 blueprint - Visão geral](./index.md) 
>  [PCI-DSS v3.2.1 blueprint - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).