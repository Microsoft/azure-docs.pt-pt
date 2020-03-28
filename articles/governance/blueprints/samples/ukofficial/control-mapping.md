---
title: OFICIAL do Reino Unido & controlos de amostras de plantas do NHS do Reino Unido
description: Mapeamento de controlo das amostras de plantas do NHS oficial do Reino Unido e do Reino Unido. Cada controlo é mapeado para uma ou mais Políticas Azure que ajudam na avaliação.
ms.date: 12/04/2019
ms.topic: sample
ms.openlocfilehash: 5bef590013a9ef06b791e58dc6c82e74dffe1a17
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2020
ms.locfileid: "74851371"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Mapeamento de controlo das amostras de plantas do NHS oficial do Reino Unido e do Reino Unido

O seguinte artigo detalha como as amostras de plantas do NHS do Reino Unido mapeiam para os controlos do NHS oficial do Reino Unido e do Reino Unido. Para obter mais informações sobre os controlos, consulte [o UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Os seguintes mapeamentos são para os controlos **do NHS oficial** do Reino Unido e **do Reino Unido.** Utilize a navegação à direita para saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa da [Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra a **Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione os ** \[\] controlos do Serviço DE INFORMAÇÃO do Reino Unido e do NhS do Reino Unido e implemente extensões vm específicas para apoiar os requisitos** de auditoria iniciativa política incorporada.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições de [Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um 1:1 ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a política do** Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por quaisquer definições de Política Azure neste momento. Portanto, o cumprimento na Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de plano de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Dados relativos à proteção do trânsito

O plano ajuda-o a garantir que a transferência de informação com os serviços Azure é segura, atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam ligações inseguras a contas de armazenamento e Redis Cache.

- Apenas ligações seguras ao seu Redis Cache devem ser ativadas
- A transferência segura para contas de armazenamento deve ser ativada
- Mostre resultados de auditoria de servidores web do Windows que não estão a usar protocolos de comunicação seguros
- Implemente pré-requisitos para auditar servidores web do Windows que não estão a usar protocolos de comunicação seguros
- A versão TLS mais recente deve ser utilizada na sua app API
- A versão TLS mais recente deve ser utilizada na sua Web App
- A versão TLS mais recente deve ser usada na sua App de Funções

## <a name="23-data-at-rest-protection"></a>2.3 Dados na proteção do descanso

Esta planta ajuda-o a impor a sua política sobre a utilização de controlos criptógrafos, atribuindo definições de [Política Azure](../../../policy/overview.md) que impõem controlos criptográficos específicos e uso de auditoria de configurações criptográficas fracas.
Compreender onde os seus recursos Azure podem ter configurações criptográficas não ótimas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos estão configurados de acordo com a sua política de segurança de informação. Especificamente, as políticas atribuídas por esta planta requerem encriptação para contas de armazenamento de data lake; requerem encriptação transparente de dados nas bases de dados SQL; auditoria falta de encriptação em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação; auditar ligações inseguras às contas de armazenagem e à Redis Cache; auditar encriptação fraca da palavra-passe de máquina virtual; e auditar comunicação não encriptada do Tecido de Serviço.

- A encriptação do disco deve ser aplicada em máquinas virtuais
- Variáveis da conta de automação devem ser encriptadas
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para encriptar AndSign
- Encriptação transparente de dados nas bases de dados SQL deve ser ativada
- Implementar encriptação transparente de dados SQL DB
- Exigir encriptação nas contas da Data Lake Store
- Locais permitidos (foi codificado duramente para "UK SOUTH" e "UK WEST")
- Localizações permitidas para grupos de recursos (foi codificada duramente para "UK SOUTH" e "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 Gestão de Vulnerabilidades

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que monitorizam a proteção de pontos finais em falta, as atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades SQL e vulnerabilidades de máquinas virtuais. Estes insights fornecem informações em tempo real sobre o estado de segurança dos seus recursos implantados e podem ajudá-lo a priorizar ações de reparação.

- Monitor desaparecido Proteção de Ponto final no Centro de Segurança Azure
- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema nos conjuntos de escala de máquinas virtuais devem ser instaladas
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- Vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada nos seus casos geridos pela SQL
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquinas virtuais devem ser remediadas
- A segurança avançada de dados deve ser ativada nos seus casos geridos pela SQL
- A segurança avançada de dados deve ser ativada nos seus servidores SQL

## <a name="53-protective-monitoring"></a>5.3 Monitorização protetora

Este plano ajuda-o a proteger os ativos do sistema de informação atribuindo definições de [Política Azure](../../../policy/overview.md) que fornecem monitorização protetora no acesso sem restrições, permitem a atividade da lista e ameaças.

- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais
- Auditar máquinas virtuais sem recuperação de desastres configurada
- Norma de proteção DDoS deve ser ativada
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' em definições avançadas de segurança de dados geridas pela SQL
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- Implementar deteção de ameaças em servidores SQL
- Implementar extensão padrão microsoft IaaSAntimalware para Windows Server

## <a name="9-secure-user-management"></a>9 Gestão segura do utilizador 

O Azure implementa o controlo de acesso baseado em funções (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e suas permissões. Este plano ajuda-o a restringir e controlar os direitos de acesso atribuindo definições de [Política Azure](../../../policy/overview.md) para auditar contas externas com permissões e contas de leitura/escrita com o proprietário, ler e/ou escrever permissões que não tenham autenticação multifactorativa.

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

## <a name="10-identity-and-authentication"></a>10 Identidade e Autenticação

Este plano ajuda-o a restringir e controlar os direitos de acesso atribuindo definições de [Política Azure](../../../policy/overview.md) para auditar contas externas com permissões e contas de leitura/escrita com o proprietário, ler e/ou escrever permissões que não tenham autenticação multifactorativa.

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

Esta planta atribui definições de Política Azure para auditar a utilização da autenticação do Diretório Ativo Azure para servidores SQL e Tecido de Serviço. A utilização da autenticação do Diretório Ativo Azure permite a gestão simplificada da permissão e a gestão centralizada da identidade dos utilizadores de bases de dados e de outros serviços da Microsoft.

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente

Este projeto atribui igualmente definições de Política Azure a contas de auditoria que devem ser priorizadas para revisão, incluindo contas amortizadas e contas externas. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de Política Azure para auditoria de conta depreciada que deve ser considerada para remoção.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Esta planta também atribui uma definição de Política Azure que audita permissões de ficheiros de senha SléLlo VM para alertar se são definidas incorretamente. Este design permite-lhe tomar medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs Linux que não têm as permissões de ficheiro sinuosas definidas para 0644

Este plano ajuda-o a impor senhas fortes atribuindo definições de Política Azure que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A consciência dos VMs em violação da política de força da palavra-passe ajuda-o a tomar medidas corretivas para garantir que as palavras-passe para todas as contas de utilizadores vm estão em conformidade com a política.

- \[Pré-visualização\]: Implementar pré-requisitos para auditar VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não tenham uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não tenham uma idade mínima de senha de 1 dia
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que não limitem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Implemente pré-requisitos para auditar VMs do Windows que permitam a reutilização das 24 senhas anteriores
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não tenham a definição de complexidade da palavra-passe ativada
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não têm uma idade máxima de senha de 70 dias
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não têm uma idade mínima de senha de 1 dia
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- \[Pré-visualização\]: Mostrar resultados de auditoria de VMs do Windows que permitem a reutilização das 24 senhas anteriores

Este plano também ajuda a controlar o acesso aos recursos do Azure, atribuindo definições de Política Azure. Estas políticas auditam a utilização de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos. Compreender os recursos que violam estas políticas pode ajudá-lo a tomar medidas corretivas para garantir o acesso aos recursos do Azure é restrito aos utilizadores autorizados.

- \[Pré-visualização\]: Implementar requisitos para auditar VMs Linux que tenham contas sem senhas
- \[Pré-visualização\]: Implementar requisitos para auditar VMs Linux que permitem ligações remotas a partir de contas sem senhas
- \[Pré-visualização\]: Audite VMs Linux que tenham contas sem senhas
- \[Pré-visualização\]: Audite VMs Linux que permitem ligações remotas a partir de contas sem senhas
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos do Azure
- Máquinas virtuais devem ser migradas para novos recursos do Gestor de Recursos Do Azure
- VMs de auditoria que não usam discos geridos

## <a name="11-external-interface-protection"></a>11 Proteção de Interface Externa

Além de utilizar mais de 25 políticas para uma gestão adequada e segura do utilizador, esta planta ajuda-o a proteger as interfaces de serviço de acesso não autorizado, atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza contas de armazenamento sem restrições. As contas de armazenamento com acesso sem restrições podem permitir o acesso não intencional a informações contidas no sistema de informação. Este projeto também atribui uma política que permite controlos adaptáveis de aplicações em máquinas virtuais.

- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Os controlos adaptáveis de aplicação devem ser ativados em máquinas virtuais
- As regras do NSGs para aplicações web em IaaS devem ser endurecidas
- O acesso através da Internet virado para o ponto final deve ser restringido
- As regras do Grupo de Segurança da Rede para as máquinas virtuais viradas para a Internet devem ser endurecidas
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquinavirtual
- O controlo de acesso à rede Just-In-Time deve ser aplicado em máquinas virtuais
- Auditoria sem restrições ao acesso da rede às contas de armazenamento
- Depuração remota deve ser desligada para app de função
- Depuração remota deve ser desligada para aplicação web
- Depuração remota deve ser desligada para App API
- Aplicação Web só deve ser acessível em HTTPS
- App de funções só deve ser acessível através de HTTPS
- A Aplicação API só deve ser acessível em HTTPS

## <a name="12-secure-service-administration"></a>12 Administração de Serviços Seguros

O Azure implementa o controlo de acesso baseado em funções (RBAC) para o ajudar a gerir quem tem acesso a recursos no Azure. Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e suas permissões. Este plano ajuda-o a restringir e controlar direitos de acesso privilegiados, atribuindo cinco definições de [Política Azure](../../../policy/overview.md) para auditar contas externas com o proprietário e/ou escrever permissões e contas com o proprietário, e/ou escrever permissões que não tenham autenticação multifactorativa.

Os sistemas utilizados para a administração de um serviço na nuvem terão acesso altamente privilegiado a esse serviço. O seu compromisso teria um impacto significativo, incluindo os meios para contornar os controlos de segurança e roubar ou manipular grandes volumes de dados. Os métodos utilizados pelos administradores do prestador de serviços para gerir o serviço operacional devem ser concebidos para mitigar qualquer risco de exploração que possa comprometer a segurança do serviço. Se este princípio não for implementado, um intruso pode ter os meios para contornar os controlos de segurança e roubar ou manipular grandes volumes de dados.

- MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- MFA deve ser ativado contas com permissões de escrita na sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Esta planta atribui definições de Política Azure para auditar a utilização da autenticação do Diretório Ativo Azure para servidores SQL e Tecido de Serviço. A utilização da autenticação do Diretório Ativo Azure permite a gestão simplificada da permissão e a gestão centralizada da identidade dos utilizadores de bases de dados e de outros serviços da Microsoft.

- Um administrador de Diretório Ativo Azure deve ser provisionado para servidores SQL
- Os clusters de tecido de serviço só devem utilizar o Diretório Ativo Azure para a autenticação do cliente

Este projeto atribui igualmente definições de Política Azure para contas de auditoria que devem ser priorizadas para revisão, incluindo contas depreciadas e contas externas com permissões elevadas. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que remove imediatamente os direitos de acesso aos recursos do Azure. Este projeto atribui duas definições de Política Azure para auditoria de conta depreciada que deve ser considerada para remoção.

- As contas depreciadas devem ser removidas da sua subscrição
- Contas depreciadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Esta planta também atribui uma definição de Política Azure que audita permissões de ficheiros de senha SléLlo VM para alertar se são definidas incorretamente. Este design permite-lhe tomar medidas corretivas para garantir que os autenticadores não sejam comprometidos.

- \[Pré-visualização\]: Audit Linux VM /etc/passwd file permissions estão definidos para 0644

## <a name="13-audit-information-for-users"></a>13 Informações de Auditoria para Utilizadores

Este plano ajuda-o a garantir que os eventos do sistema são registados atribuindo definições de [Política Azure](../../../policy/overview.md) que auditam definições de registo nos recursos do Azure. Uma política atribuída também audita se as máquinas virtuais não estiverem a enviar registos para um espaço de trabalho específico de análise de registos.

- A auditoria deve ser ativada em definições avançadas de segurança de dados no Servidor SQL
- Definição de diagnóstico de auditoria
- \[Pré-visualização\]: Implementar o Agente de Análise de Registo para VMs Linux
- \[Pré-visualização\]: Implementar o agente de análise de registo para VMs do Windows
- Implementar o observador da rede quando as redes virtuais são criadas

## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo dos projetos do NHS oficial do Reino Unido e do Reino Unido, visite os seguintes artigos para saber sobre a visão geral e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Plantas do NHS](./index.md)
> do Reino Unido - Visão geral do[UK OFFICIAL e uk NHS blueprints - Implementar passos](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Conheça o ciclo de vida da [planta.](../../concepts/lifecycle.md)
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
