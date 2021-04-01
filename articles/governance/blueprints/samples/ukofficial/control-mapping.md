---
title: OFICIAL DO Reino Unido & controlos de amostras de amostras do NHS do Reino Unido
description: Controlo do mapeamento das amostras de plantas do NHS do Reino Unido e do Reino Unido. Cada controlo é mapeado para uma ou mais definições da Política Azure que ajudam na avaliação.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: bf10bcf7064306094664c1ff560187ef77645d4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99627571"
---
# <a name="control-mapping-of-the-uk-official-and-uk-nhs-blueprint-samples"></a>Controlo do mapeamento das amostras de plantas do NHS oficial do Reino Unido e do Reino Unido

O artigo seguinte detalha como o mapa de amostras de amostras do NHS oficial do Reino Unido e do Reino Unido para os controlos do NHS oficial e do Reino Unido. Para obter mais informações sobre os controlos, consulte [o UK OFFICIAL](https://www.gov.uk/government/publications/government-security-classifications).

Os seguintes mapeamentos são para os controlos **oficiais** do Reino Unido e **do NhS do Reino Unido.** Utilize a navegação no direito de saltar diretamente para um mapeamento de controlo específico. Muitos dos controlos mapeados são implementados com uma iniciativa [da Política Azure.](../../../policy/overview.md) Para rever a iniciativa completa, abra **a Política** no portal Azure e selecione a página **Definições.** Em seguida, encontre e selecione os **\[ \] controlos oficiais do NHS oficiais e britânicos de pré-visualização e implemente extensões VM específicas para apoiar os requisitos** de auditoria integrados na iniciativa política.

> [!IMPORTANT]
> Cada controlo abaixo está associado a uma ou mais definições [da Política Azure.](../../../policy/overview.md) Estas políticas podem ajudá-lo a avaliar o [cumprimento](../../../policy/how-to/get-compliance-data.md) do controlo; no entanto, muitas vezes não há um para um ou um jogo completo entre um controlo e uma ou mais políticas. Como tal, **a Conformidade** na Política Azure refere-se apenas às próprias políticas; isto não garante que esteja totalmente em conformidade com todos os requisitos de um controlo. Além disso, a norma de conformidade inclui controlos que não são abordados por nenhuma definição da Política Azure neste momento. Portanto, o cumprimento da Política Azure é apenas uma visão parcial do seu estado de conformidade geral. As associações entre controlos e definições de Política Azure para esta amostra de projeto de conformidade podem mudar ao longo do tempo. Para ver a história da mudança, consulte o [GitHub Commit History](https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/governance/blueprints/samples/ukofficial/control-mapping.md).

## <a name="1-data-in-transit-protection"></a>1 Dados relativos à proteção do trânsito

O plano ajuda-o a garantir que a transferência de informação com os serviços Azure é segura, atribuindo definições [da Azure Policy](../../../policy/overview.md) que auditam ligações inseguras às contas de armazenamento e à Cache Redis.

- Apenas devem ser ativadas ligações seguras à sua Cache Redis
- A transferência segura para contas de armazenamento deve ser ativada
- Mostrar os resultados da auditoria dos servidores web do Windows que não estão a utilizar protocolos de comunicação seguros
- A Aplicação Web só deve ser acessível em HTTPS
- A App de função só deve estar acessível através do HTTPS
- A API App só deve estar acessível em HTTPS

## <a name="23-data-at-rest-protection"></a>2.3 Dados relativos à proteção do repouso

Esta planta ajuda-o a impor a sua política sobre a utilização de controlos criptógrafos, atribuindo definições [de Política de Azure](../../../policy/overview.md) que impõem controlos criptógrafos específicos e auditam o uso de configurações criptográficas fracas. Compreender onde os seus recursos Azure podem ter configurações criptográficas não ideais pode ajudá-lo a tomar ações corretivas para garantir que os recursos são configurados de acordo com a sua política de segurança de informação. Especificamente, as políticas atribuídas por esta planta requerem encriptação para contas de armazenamento de data lake; requerer encriptação de dados transparentes nas bases de dados SQL; auditoria em falta de encriptação em contas de armazenamento, bases de dados SQL, discos de máquinas virtuais e variáveis de conta de automação; auditoria de ligações inseguras às contas de armazenamento e à Cache Redis; encriptação de senha de máquina virtual fraca; e auditoria comunicação de tecido de serviço não encriptado.

- A encriptação do disco deve ser aplicada em máquinas virtuais
- As variáveis de conta de automação devem ser encriptadas
- Os clusters de tecido de serviço devem ter a propriedade ClusterProtectionLevel definida para EncryptAndSign
- A encriptação transparente de dados nas bases de dados SQL deve ser ativada
- Implementar encriptação de dados transparentes SQL DB
- Exigir encriptação nas contas da Data Lake Store
- Locais permitidos (foi codificado para "UK SOUTH" e "UK WEST")
- Locais permitidos para grupos de recursos (foi codificado duramente para "UK SOUTH" e "UK WEST")

## <a name="52-vulnerability-management"></a>5.2 Gestão de Vulnerabilidades

Esta planta ajuda-o a gerir as vulnerabilidades do sistema de informação, atribuindo definições [de Política Azure](../../../policy/overview.md) que monitorizam a falta de proteção do ponto final, atualizações do sistema em falta, vulnerabilidades do sistema operativo, vulnerabilidades de SQL e vulnerabilidades de máquinas virtuais. Estas informações fornecem informações em tempo real sobre o estado de segurança dos seus recursos mobilizados e podem ajudá-lo a priorizar ações de reparação.

- Monitor que falta proteção de ponto final no Centro de Segurança Azure
- As atualizações de sistema devem ser instaladas nos seus computadores
- As atualizações do sistema em conjuntos de escala de máquinas virtuais devem ser instaladas
- As vulnerabilidades na configuração de segurança das suas máquinas devem ser remediadas
- As vulnerabilidades nas suas bases de dados SQL devem ser remediadas
- As vulnerabilidades devem ser remediadas por uma solução de Avaliação de Vulnerabilidades
- A avaliação da vulnerabilidade deve ser ativada nos seus servidores SQL
- A avaliação da vulnerabilidade deve ser ativada no seu exemplo gerido pelo SQL
- As vulnerabilidades na configuração de segurança nos conjuntos de escala de máquina virtual devem ser remediadas
- A segurança avançada dos dados deve ser ativada na sua instância gerida pelo SQL
- A segurança avançada dos dados deve ser ativada nos seus servidores SQL

## <a name="53-protective-monitoring"></a>5.3 Monitorização protetora

Esta planta ajuda-o a proteger os ativos do sistema de informação, atribuindo definições [da Política Azure](../../../policy/overview.md) que fornecem monitorização protetora no acesso sem restrições, permitem a atividade da lista e ameaças.

- As contas de armazenamento devem restringir o acesso à rede
- Os controlos de aplicações adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas
- Auditar máquinas virtuais sem recuperação de desastres configurada
- A Padrão de Proteção Azure DDoS deve ser ativado
- Os tipos avançados de proteção de ameaças devem ser definidos para 'All' em definições de segurança de dados avançadas de casos
- Os tipos avançados de proteção contra ameaças devem ser definidos para 'All' nas definições avançadas de segurança de dados do servidor SQL
- Implementar deteção de ameaças em servidores SQL
- Implementar extensão padrão do Microsoft IaaSAntimalware para o Windows Server

## <a name="9-secure-user-management"></a>9 Gestão Segura do Utilizador 

O controlo de acesso baseado em funções (Azure RBAC) ajuda-o a gerir quem tem acesso a recursos em Azure.
Utilizando o portal Azure, pode rever quem tem acesso aos recursos do Azure e às suas permissões. Este projeto ajuda-o a restringir e a controlar os direitos de acesso, atribuindo definições [da Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões e contas de proprietário e/ou de leitura/escrita com o proprietário, ler e/ou escrever permissões que não tenham autenticação de vários fatores ativadas.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- A MFA deve ser ativada em contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

## <a name="10-identity-and-authentication"></a>10 Identidade e Autenticação

Este projeto ajuda-o a restringir e a controlar os direitos de acesso, atribuindo definições [da Azure Policy](../../../policy/overview.md) para auditar contas externas com permissões e contas de proprietário e/ou de leitura/escrita com o proprietário, ler e/ou escrever permissões que não tenham autenticação de vários fatores ativadas.

- O MFA deve ser ativado em contas com permissões do proprietário na sua subscrição
- A MFA deve ser ativada em contas com permissões de escrita na sua subscrição
- MFA deve ser ativado em contas com permissões de leitura na sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição
- As contas externas com permissões de leitura devem ser removidas da sua subscrição

Esta planta atribui definições de Política Azure à utilização de auditação da autenticação do Azure Ative Directory para servidores SQL e Tecido de Serviço. A utilização da autenticação do Azure Ative Directory permite uma gestão simplificada da permissão e uma gestão centralizada da identidade dos utilizadores de bases de dados e outros serviços da Microsoft.

- Um administrador do Azure Ative Directory deve ser a provisionado para servidores SQL
- Os clusters de tecido de serviço só devem utilizar o Azure Ative Directy para a autenticação do cliente

Este projeto também atribui definições de Política Azure a contas de auditoria que devem ser priorizadas para revisão, incluindo contas amortizadas e contas externas. Quando necessário, as contas podem ser bloqueadas de iniciar sessão (ou removida), o que elimina imediatamente os direitos de acesso aos recursos da Azure.
Este projeto atribui duas definições da Política Azure à auditoria de conta amortizada que deve ser considerada para remoção.

- As contas preprecadas devem ser removidas da sua subscrição
- Contas pregridadas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões do proprietário devem ser removidas da sua subscrição
- As contas externas com permissões de escrita devem ser removidas da sua subscrição

Esta planta também atribui uma definição de Política Azure que audita permissões de ficheiros de senha Linux VM para alertar se estiverem definidas incorretamente. Este design permite-lhe tomar medidas corretivas para garantir que os autenticadores não estão comprometidos.

- Mostre os resultados da auditoria dos VMs Linux que não têm as permissões de ficheiros passwd definidas para 0644

Este plano ajuda-o a impor senhas fortes atribuindo definições de Política Azure que auditam VMs do Windows que não impõem a força mínima e outros requisitos de senha. A sensibilização dos VMs em violação da política de força de senha ajuda-o a tomar ações corretivas para garantir que as palavras-passe de todas as contas de utilizador VM estão em conformidade com a política.

- Mostrar os resultados da auditoria dos VM do Windows que não têm a definição de complexidade da palavra-passe ativada
- Mostrar resultados de auditoria de VMs do Windows que não têm uma idade máxima de senha de 70 dias
- Mostrar os resultados da auditoria dos VM do Windows que não têm uma idade mínima de senha de 1 dia
- Mostrar os resultados da auditoria dos VM do Windows que não restringem o comprimento mínimo da palavra-passe a 14 caracteres
- Mostrar resultados de auditoria a VMs do Windows que permitem a reutilização das 24 palavras-passe anteriores

Esta planta também ajuda a controlar o acesso aos recursos Azure, atribuindo definições de Política Azure. Estas políticas auditam a utilização de tipos de recursos e configurações que podem permitir um acesso mais permissivo aos recursos. Compreender os recursos que estão a violar estas políticas pode ajudá-lo a tomar medidas corretivas para garantir que os recursos Azure de acesso são restritos aos utilizadores autorizados.

- Mostrar resultados de auditoria de VMs Linux que têm contas sem senhas
- Mostrar resultados de auditoria de VMs Linux que permitem ligações remotas a partir de contas sem senhas
- As contas de armazenamento devem ser migradas para novos recursos do Gestor de Recursos Azure
- Máquinas virtuais devem ser migradas para novos recursos do Azure Resource Manager
- VMs de auditoria que não usam discos geridos

## <a name="11-external-interface-protection"></a>11 Proteção de Interface Externa

Além de utilizar mais de 25 políticas para uma gestão segura e adequada do utilizador, este plano ajuda-o a proteger as interfaces de serviço do acesso não autorizado, atribuindo uma definição [de Política Azure](../../../policy/overview.md) que monitoriza contas de armazenamento sem restrições.
As contas de armazenamento com acesso ilimitado podem permitir o acesso não intencional às informações contidas no sistema de informação. Esta planta também atribui uma política que permite controlos de aplicações adaptativos em máquinas virtuais.

- As contas de armazenamento devem restringir o acesso à rede
- Os controlos de aplicações adaptativos para definir aplicações seguras devem ser ativados nas suas máquinas
- O acesso através da Internet face ao ponto final deve ser restringido
- Recomendações de endurecimento de rede adaptativa devem ser aplicadas na internet face a máquinas virtuais
- A solução de proteção do ponto final deve ser instalada em conjuntos de escala de máquina virtual
- As portas de gestão das máquinas virtuais devem ser protegidas com controlo de acesso à rede just-in-time
- Depuragem remota deve ser desligada para apps de funções
- Depuragem remota deve ser desligada para aplicações web
- Depuragem remota deve ser desligada para apps da API

## <a name="13-audit-information-for-users"></a>13 Informações de Auditoria para Utilizadores

Esta planta ajuda-o a garantir que os eventos do sistema são registados atribuindo definições [de Política Azure](../../../policy/overview.md) que auditam as definições de registo nos recursos do Azure.
Uma política atribuída também audita se as máquinas virtuais não estiverem a enviar registos para um espaço de trabalho específico de análise de registos.

- A segurança avançada dos dados deve ser ativada nos seus servidores SQL
- Definição de diagnóstico de auditoria
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs Linux
- \[Pré-visualização \] : Implementar agente de análise de registo para VMs do Windows
- Implementar o observador de rede quando as redes virtuais forem criadas


## <a name="next-steps"></a>Passos seguintes

Agora que reviu o mapeamento de controlo das plantas do NHS do Reino Unido e do Reino Unido, visite os seguintes artigos para saber mais sobre a visão geral e como implementar esta amostra:

> [!div class="nextstepaction"]
> [Plantas do NHS oficial do Reino Unido e do Reino Unido - Visão geral](./index.md) 
>  [Plantas do NHS oficial do Reino Unido e do Reino Unido - Implementar etapas](./deploy.md)

Artigos adicionais sobre esquemas e como os utilizar:

- Saiba mais sobre o [ciclo de vida do esquema](../../concepts/lifecycle.md).
- Compreenda como utilizar [parâmetros estáticos e dinâmicos](../../concepts/parameters.md).
- Aprenda a personalizar a [ordem de sequenciação do esquema](../../concepts/sequencing-order.md).
- Saiba como utilizar o [bloqueio de recursos de esquema](../../concepts/resource-locking.md).
- Saiba como [atualizar as atribuições existentes](../../how-to/update-existing-assignments.md).
