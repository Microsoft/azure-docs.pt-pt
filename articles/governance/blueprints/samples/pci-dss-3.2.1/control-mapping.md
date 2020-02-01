---
title: Controlos de amostras de plantas PCI-DSS v3.2.1
description: Controle o mapeamento da amostra de segurança de dados da indústria de cartões de pagamento v3.2.1 para a Política Azure e rBAC.
ms.date: 01/29/2020
ms.topic: sample
ms.openlocfilehash: db21ac9d628e777b6ff2cc86516cfb1497f5a62f
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76905648"
---
# <a name="control-mapping-of-the-pci-dss-v321-blueprint-sample"></a>Mapeamento de controlo da amostra de planta PCI-DSS v3.2.1

O seguinte artigo detalha como os mapas da amostra de plantas Do IPC-DSS v3.2.1 do Azure Blueprints para os comandos PCI-DSS v3.2.1. Para obter mais informações sobre os controlos, consulte [PCI-DSS v3.2.1](https://www.pcisecuritystandards.org/documents/PCI_DSS_v3-2-1.pdf).

Os seguintes mapeamentos são para os comandos **PCI-DSS v3.2.1:2018.** Use a navegação à direita para ir diretamente para um mapeamento de controle específico. Muitos dos controles mapeados são implementados com uma iniciativa de [Azure Policy](../../../policy/overview.md) . Para examinar a iniciativa completa, abra a **política** no portal do Azure e selecione a página **definições** . Em seguida, encontre e selecione os **controlos\[Preview\] Audit PCI v3.2.1:2018 e implemente extensões vm específicas para apoiar os requisitos** de auditoria embutidas na iniciativa política incorporada.

> [!IMPORTANT]
> Cada controle abaixo é associado a uma ou mais definições de [Azure Policy](../../../policy/overview.md) . Essas políticas podem ajudá-lo a [avaliar a conformidade](../../../policy/how-to/get-compliance-data.md) com o controle; no entanto, muitas vezes não há uma correspondência completa ou 1:1 entre um controle e uma ou mais políticas. Como tal, em **conformidade** com Azure Policy refere-se apenas às próprias políticas; Isso não garante que você esteja totalmente em conformidade com todos os requisitos de um controle. Além disso, o padrão de conformidade inclui controles que não são abordados por nenhuma definição de Azure Policy no momento. Portanto, a conformidade em Azure Policy é apenas uma visão parcial do seu status de conformidade geral. As associações entre controles e definições de Azure Policy para esta amostra do Blueprint de conformidade podem mudar ao longo do tempo. Para exibir o histórico de alterações, consulte o [histórico de confirmação do GitHub](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/pci-dss-3.2.1/control-mapping.md).

## <a name="132-and-134-boundary-protection"></a>1.3.2 e 1.3.4 Proteção de fronteiras

Este plano ajuda-o a gerir e controlar redes atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam grupos de segurança de rede com regras permissivas. As regras que são muito permissivas podem permitir o acesso à rede indesejado e devem ser examinadas. Este projeto atribui uma definição de Política Azure que monitoriza pontos finais, aplicações e contas de armazenamento desprotegidas. Pontos de extremidade e aplicativos que não são protegidos por um firewall e contas de armazenamento com acesso irrestrito podem permitir o acesso não intencional às informações contidas no sistema de informações.

- Auditar o acesso irrestrito à rede para contas de armazenamento
- O acesso através da Internet virado para o ponto final deve ser restringido

## <a name="34a-41-41g-41h-and-653-cryptographic-protection"></a>3.4.a, 4.1, 4.1.g, 4.1.h e 6.5.3 Proteção Criptográfica

Esta planta ajuda-o a aplicar a sua política com a utilização de controlos criptógrafos, atribuindo definições de [Política Azure](../../../policy/overview.md) que impõem controlos criptográficos específicos e utilização de auditoria de configurações criptográficas fracas. Entender onde os recursos do Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar medidas corretivas para garantir que os recursos sejam configurados de acordo com sua política de segurança de informações. Especificamente, as políticas atribuídas por esta planta requerem encriptação transparente de dados nas bases de dados SQL; auditoria falta de encriptação em contas de armazenamento e variáveis de conta de automação. Existem também políticas que abordam ligações inseguras de auditoria a contas de armazenamento, Aplicações de Função, WebApp, Aplicações API e Redis Cache, e auditam comunicação de tecido de serviço não encriptado.

- Função de aplicação só deve estar acessível através de HTTPS
- Aplicação Web só deve estar acessível através de HTTPS
- O aplicativo de API só deve ser acessível via HTTPS
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas
- A encriptação do disco deve ser aplicada em máquinas virtuais
- Variáveis da conta de automação devem ser encriptadas
- Apenas ligações seguras ao seu Redis Cache devem ser ativadas
- Transferência segura para contas de armazenamento deve ser ativada
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign
- Transparent Data Encryption em bancos de dados SQL devem ser habilitadas
- Implementar encriptação transparente de dados SQL DB

## <a name="51-62-66-and-1121-vulnerability-scanning-and-system-updates"></a>5.1, 6.2, 6.6 e 11.2.1 Verificação de vulnerabilidades e atualizações do sistema

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam as atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais no Azure Security Center. A central de segurança do Azure fornece recursos de relatório que permitem que você tenha informações em tempo real sobre o estado de segurança dos recursos do Azure implantados.

- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure
- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server
- Implementar deteção de ameaças em servidores SQL
- As atualizações do sistema devem ser instaladas nas suas máquinas
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades

## <a name="711-712-and-713-separation-of-duties"></a>7.1.1. 7.1.2 e 7.1.3 Separação de direitos

Ter apenas um proprietário de assinatura do Azure não permite redundância administrativa. Por outro lado, ter muitos proprietários de assinatura do Azure pode aumentar o potencial de uma violação por meio de uma conta de proprietário comprometida. Este plano ajuda-o a manter um número adequado de proprietários de subscrições azure, atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam o número de proprietários para subscrições do Azure. O gerenciamento de permissões de proprietário de assinatura pode ajudá-lo a implementar a separação apropriada de tarefas.

- Deve haver mais de um proprietário atribuído à sua subscrição
- Um máximo de 3 proprietários deve ser designado para a sua subscrição 

## <a name="32-721-831a-and-831b-management-of-privileged-access-rights"></a>3.2, 7.2.1, 8.3.1.a e 8.3.1.b Gestão dos Direitos de Acesso Privilegiados

Este plano ajuda-o a restringir e controlar direitos de acesso privilegiados, atribuindo definições de [Política Azure](../../../policy/overview.md) para auditar contas externas com o proprietário, escrever e/ou ler permissões e contas de empregados com permissões de proprietário e/ou escrever permissões que não tenham autenticação multifactorativada. O Azure implementa o RBAC (controle de acesso baseado em função) para gerenciar quem tem acesso aos recursos do Azure. Entender onde as regras personalizadas do RBAC são implementadas pode ajudá-lo a verificar a necessidade e a implementação adequada, pois as regras personalizadas de RBAC são propensas a erros. Esta planta também atribui definições de [Política Azure](../../../policy/overview.md) para auditar a utilização da autenticação do Diretório Ativo Azure para servidores SQL. A utilização da autenticação do Diretório Ativo Azure simplifica a gestão da permissão e centraliza a gestão de identidade dos utilizadores de bases de dados e de outros Microsoft  
serviços.
 
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição
- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- A MFA deve ser habilitada para contas com permissões de gravação em sua assinatura
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Auditar o uso de regras personalizadas de RBAC

## <a name="812-and-815-least-privilege-and-review-of-user-access-rights"></a>8.1.2 e 8.1.5 Menos Privilégio e Revisão dos Direitos de Acesso ao Utilizador

O Azure implementa o controlo de acesso baseado em funções (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Usando o portal do Azure, você pode revisar quem tem acesso aos recursos do Azure e suas permissões. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) a contas de auditoria que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

## <a name="813-removal-or-adjustment-of-access-rights"></a>8.1.3 Remoção ou Ajustamento dos Direitos de Acesso

O Azure implementa o controlo de acesso baseado em funções (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o Azure Ative Directory e o RBAC, pode atualizar as funções dos utilizadores para refletir as alterações organizacionais. Quando necessário, as contas podem ser impedidas de entrar (ou removidas), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui definições de [Política Azure](../../../policy/overview.md) para auditoria de conta depreciada que deve ser considerada para remoção.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição

## <a name="823ab-824ab-and-825-password-based-authentication"></a>8.2.3.a,b, 8.2.4.a,b e 8.2.5 Autenticação baseada em palavra-passe

Este plano ajuda-o a impor senhas fortes atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A conscientização das VMs em violação da política de força da senha ajuda você a tomar medidas corretivas para garantir que as senhas de todas as contas de usuário da VM estejam em conformidade com a política.

- \[Pré-visualização\]: Audite VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Implementar requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Preview\]: Implementar requisitos para auditar VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Auditar VMs do Windows que permitem a reutilização das 24 senhas anteriores
- \[Preview\]: Implementar requisitos para auditar VMs do Windows que permitam a reutilização das 24 senhas anteriores

## <a name="103-and-1054-audit-generation"></a>10.3 e 10.5.4 Geração de Auditoria

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam definições de registo nos recursos do Azure.
Os logs de diagnóstico fornecem informações sobre as operações que foram executadas nos recursos do Azure. Os logs do Azure dependem de relógios internos sincronizados para criar um registro de eventos correlacionado ao tempo entre recursos.

- A auditoria deve ser habilitada nas configurações de segurança de dados avançadas no SQL Server
- Definição de diagnóstico de auditoria
- Auditar configurações de auditoria no nível do SQL Server
- Implantar a auditoria em servidores SQL
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
> [projeto PCI-DSS v3.2.1 - Desdobrar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).