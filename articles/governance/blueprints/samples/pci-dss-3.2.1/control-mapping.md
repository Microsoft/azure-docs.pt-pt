---
title: Controlos de amostras de plantas PCI-DSS v3.2.1
description: Controle o mapeamento da amostra de segurança de dados da indústria de cartões de pagamento v3.2.1 para a Política Azure e rBAC.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "76905648"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapeamento de controlo da amostra de planta PCI-DSS v3.2.1

O seguinte artigo detalha como os mapas da amostra de plantas Do IPC-DSS v3.2.1 do Azure Blueprints para os comandos PCI-DSS v3.2.1. Para obter mais informações sobre os controlos, consulte [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Os seguintes mapeamentos são para os comandos **PCI-DSS v3.2.1:2018.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione os ** \[controlos de Auditoria de Pré-Visualização\] PCI v3.2.1:2018 e implemente extensões vm específicas para apoiar os requisitos** de auditoria iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 Proteção de fronteiras

Este plano ajuda-o a gerir e controlar redes atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam grupos de segurança de rede com regras permissivas. Regras demasiado permissivas podem permitir o acesso não intencional à rede e devem ser revistas. Este projeto atribui uma definição de Política Azure que monitoriza pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos finais e aplicações que não estejam protegidas por uma firewall, e contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação.

- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- O acesso através da Internet virado para o ponto final deve ser restringido

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 Proteção Criptográfica

Esta planta ajuda-o a aplicar a sua política com a utilização de controlos criptógrafos, atribuindo definições de [Política Azure](../../../policy/overview.md) que impõem controlos criptográficos específicos e utilização de auditoria de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ótimas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos estão configurados de acordo com a sua política de segurança de informação. Especificamente, as políticas atribuídas por esta planta requerem encriptação transparente de dados nas bases de dados SQL; auditoria falta de encriptação em contas de armazenamento e variáveis de conta de automação. Existem também políticas que abordam ligações inseguras de auditoria a contas de armazenamento, Aplicações de Função, WebApp, Aplicações API e Redis Cache, e auditam comunicação de tecido de serviço não encriptado.

- App de funções só deve ser acessível através de HTTPS
- Aplicação Web só deve ser acessível em HTTPS
- A Aplicação API só deve ser acessível em HTTPS
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Variáveis da conta de automação devem ser encriptadas
- Apenas ligações seguras ao seu Redis Cache devem ser ativadas
- A transferência segura para contas de armazenamento deve ser ativada
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada
- Implementar encriptação transparente de dados SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 e 11.2.1 Verificação de vulnerabilidades e atualizações do sistema

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam as atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. O Azure Security Center fornece capacidades de reporte que lhe permitem ter informações em tempo real sobre o estado de segurança dos recursos azure implantados.

- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure
- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server
- Implementar deteção de ameaças em servidores SQL
- As atualizações de sistema devem ser instaladas nos seus computadores
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 Separação de direitos

Ter apenas um proprietário de assinatura Azure não permite o despedimento administrativo. Inversamente, ter demasiados proprietários de subscrições do Azure pode aumentar o potencial de violação através de uma conta de proprietário comprometida. Este plano ajuda-o a manter um número adequado de proprietários de subscrições azure, atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam o número de proprietários para subscrições do Azure. Gerir as permissões do proprietário da subscrição pode ajudá-lo a implementar a separação adequada de deveres.

- Deve haver mais de um proprietário atribuído à sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b Gestão dos Direitos de Acesso Privilegiados

Este plano ajuda-o a restringir e controlar direitos de acesso privilegiados, atribuindo definições de [Política Azure](../../../policy/overview.md) para auditar contas externas com o proprietário, escrever e/ou ler permissões e contas de empregados com permissões de proprietário e/ou escrever permissões que não tenham autenticação multifactorativada. O Azure implementa o controlo de acesso baseado em funções (RBAC) para gerir quem tem acesso aos recursos do Azure. Compreender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar necessidades e implementação adequada, uma vez que as regras personalizadas do RBAC são propensas a erros. Esta planta também atribui definições de [Política Azure](../../../policy/overview.md) para auditar a utilização da autenticação do Diretório Ativo Azure para servidores SQL. A utilização da autenticação do Diretório Ativo Azure simplifica a gestão da permissão e centraliza a gestão de identidade dos utilizadores de bases de dados e de outros Microsoft  
services.
 
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Utilização de auditoria de regras rbac personalizadas

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 Menos Privilégio e Revisão dos Direitos de Acesso ao Utilizador

O Azure implementa o controlo de acesso baseado em funções (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Remoção ou Ajustamento dos Direitos de Acesso

O Azure implementa o controlo de acesso baseado em funções (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o Azure Ative Directory e o RBAC, pode atualizar as funções dos utilizadores para refletir as alterações organizacionais. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) para auditoria de conta depreciada que deve ser considerada para remoção.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 Autenticação baseada em palavra-passe

Este plano ajuda-o a impor senhas fortes atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A consciência dos VMs em violação da política de força da palavra-passe ajuda-o a tomar medidas corretivas para garantir que as palavras-passe para todas as contas de utilizadores vm estão em conformidade com a política.

- \[Pré-visualização\]: Auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Auditar VMs do Windows que permitem a reutilização das 24 senhas anteriores
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que permitam a reutilização das 24 senhas anteriores

## <a name="103-and-1054-audit-generation"></a>10.3 e 10.5.4 Geração de Auditoria

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam definições de registo nos recursos do Azure.
Os registos de diagnóstico fornecem informações sobre as operações que foram realizadas dentro dos recursos do Azure. Os registos azure dependem de relógios internos sincronizados para criar um registo de eventos correlacionados com o tempo em todos os recursos.

- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL
- Definição de diagnóstico de auditoria
- Audite as definições de auditoria ao nível do servidor SQL
- Implementar auditoria sql
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos do Azure
- Máquinas virtuais devem ser migradas para novos recursos do Gestor de Recursos Do Azure

## <a name="1236-and-1237-information-security"></a>12.3.6 e 12.3.7 Segurança da Informação

Este plano ajuda-o a gerir e controlar a sua rede atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam as localizações de rede aceitáveis e os produtos da empresa aprovados permitidos para o ambiente. Estes são personalizáveis por cada empresa através dos parâmetros políticos dentro de cada uma destas políticas.

- Localizações permitidas
- Locais permitidos para grupos de recursos

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo da planta PCI-DSS v3.2.1, visite os seguintes artigos para saber sobre a visão geral e como implementar esta amostra:

> [!div class="nextstepaction"]
> [PcI-DSS v3.2.1 - Visão geral](./index.md)
> [PCI-DSS v3.2.1 - Desdobrar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).