---
title: Segurança física de data centers do Azure-Microsoft Azure | Microsoft Docs
description: O artigo descreve o que a Microsoft faz para proteger os data centers do Azure, incluindo ofertas de infraestrutura física, segurança e conformidade.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 634f89b3123902d981ad07dd1404315387104322
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726734"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Instalações do Azure, local e segurança física
Este artigo descreve o que a Microsoft faz para proteger a infraestrutura do Azure.

## <a name="datacenter-infrastructure"></a>Infraestrutura do datacenter
O Azure é composto de uma [infraestrutura de datacenter distribuída globalmente](https://azure.microsoft.com/global-infrastructure/), com suporte a milhares de serviços online e abrangendo mais de 100 instalações altamente seguras em todo o mundo.

A infraestrutura foi projetada para trazer os aplicativos mais próximos aos usuários em todo o mundo, preservando a residência de dados e oferecendo opções abrangentes de conformidade e resiliência para os clientes. O Azure tem 52 regiões em todo o mundo e está disponível em 140 países/regiões.

Uma região é um conjunto de data centers que é interconectado por meio de uma rede maciça e resiliente. A rede inclui distribuição de conteúdo, balanceamento de carga, redundância e criptografia por padrão. Com regiões mais globais do que qualquer outro provedor de nuvem, o Azure oferece a flexibilidade para implantar aplicativos onde você precisa deles.

As regiões do Azure estão organizadas em geografias. Uma geografia do Azure garante que os requisitos de residência, de soberania, de conformidade e de resiliência dos dados são honrados dentro dos limites geográficos.

As geografias permitem aos clientes com necessidades de residência de dados específicos e conformidade aproximarem os seus dados e aplicações. As geografias são tolerantes a falhas para resistir à falha de região completa, por meio de sua conexão à infraestrutura de rede dedicada de alta capacidade.

As zonas de disponibilidade são locais fisicamente separados em uma região do Azure. Cada zona de disponibilidade é composta por um ou mais data centers equipados com energia, resfriamento e rede independentes. As zonas de disponibilidade permitem que você execute aplicativos de missão crítica com alta disponibilidade e replicação de baixa latência.

A figura a seguir mostra como a infraestrutura global do Azure emparelha a região e as zonas de disponibilidade dentro do mesmo limite de residência de dados para alta disponibilidade, recuperação de desastre e backup.

![Diagrama mostrando o limite de residência de dados](./media/physical-security/data-residency-boundary.png)

Os data centers distribuídos geograficamente permitem que a Microsoft esteja perto dos clientes, para reduzir a latência da rede e permitir o backup e o failover com redundância geográfica.

## <a name="physical-security"></a>Segurança física
A Microsoft projeta, cria e opera data centers de uma forma que controla estritamente o acesso físico às áreas em que os dados são armazenados. A Microsoft entende a importância de proteger seus dados e está comprometida em ajudar a proteger os data centers que contêm seus dados. Temos uma divisão completa da Microsoft dedicada à criação, à criação e à operação de instalações físicas que dão suporte ao Azure. Essa equipe está investido em manter a segurança física de ponta.

A Microsoft usa uma abordagem em camadas para a segurança física, a fim de reduzir o risco de usuários não autorizados obterem acesso físico aos dados e aos recursos do datacenter. Os data centers gerenciados pela Microsoft têm amplas camadas de proteção: aprovação de acesso no perímetro do recurso, no perímetro do prédio, dentro do prédio e no piso do datacenter. As camadas de segurança física são:

- **Solicitação e aprovação de acesso.** Você deve solicitar acesso antes de chegar ao datacenter. Você precisa fornecer uma justificativa de negócios válida para sua visita, como fins de conformidade ou auditoria. Todas as solicitações são aprovadas de acordo com a necessidade de acesso pelos funcionários da Microsoft. Uma base de necessidade de acesso ajuda a manter o número de pessoas necessárias para concluir uma tarefa nos data centers para o mínimo. Depois que a Microsoft concede permissão, uma pessoa só tem acesso à área discreta do datacenter necessária, com base na justificativa de negócios aprovada. As permissões são limitadas a um determinado período de tempo e expiram.

- **Perímetro do recurso.** Quando chegar a um datacenter, você precisará passar por um ponto de acesso bem definido. Normalmente, os limites altos feitos de aço e concreto abrangem cada polegada do perímetro. Há câmeras em todos os data centers, com uma equipe de segurança monitorando seus vídeos em todos os momentos.

- **Criando entrada.** A entrada do datacenter é uma equipe com responsáveis por profissionais de segurança que sofreram verificações rigorosas de treinamento e em segundo plano. Esses responsáveis pela segurança também patrulham rotineiramente o datacenter e monitoram os vídeos de câmeras dentro do datacenter em todos os momentos.

- **Dentro do edifício.** Depois de inserir o edifício, você deve passar a autenticação de dois fatores com a biometria para continuar movendo o datacenter. Se sua identidade for validada, você poderá inserir apenas a parte do datacenter para a qual você aprovou acesso. Você pode permanecer lá somente pela duração do tempo aprovado.

- **Piso do datacenter.** Você só é permitido no chão que você está aprovado para inserir. Você precisa passar por uma triagem de detecção de metal do corpo completo. Para reduzir o risco de dados não autorizados entrarem ou saírem do datacenter sem nosso conhecimento, somente os dispositivos aprovados podem fazer seu caminho no chão do datacenter. Além disso, as câmeras de vídeo monitoram o front e o verso de cada rack do servidor. Ao sair do piso do datacenter, você deve passar novamente pela triagem de detecção de metal do corpo completo. Para sair do datacenter, você precisa passar por uma verificação de segurança adicional.

A Microsoft exige que os visitantes retenham selos após a partida de qualquer instalação da Microsoft.

## <a name="physical-security-reviews"></a>Revisões de segurança físicas
Periodicamente, realizamos revisões de segurança físicas das instalações, para garantir que os data centers atendam adequadamente aos requisitos de segurança do Azure. O pessoal do provedor de hospedagem do datacenter não fornece gerenciamento de serviços do Azure. A equipe não pode entrar em sistemas do Azure e não tem acesso físico à sala e aos compartimentos de colocação do Azure.

## <a name="data-bearing-devices"></a>Dispositivos que ostentam dados
A Microsoft usa os procedimentos de práticas recomendadas e uma solução de apagamento que é [compatível com NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Para discos rígidos que não podem ser apagados, usamos um processo de destruição que destrói e renderiza a recuperação de informações impossível. Esse processo de destruição pode ser despedaçar, destruir, pulverizar ou incinere. Determinamos o meio de descarte de acordo com o tipo de ativo. Guardamos registros da destruição.  

## <a name="equipment-disposal"></a>Eliminação de equipamentos
Após o fim da vida útil do sistema, a equipe operacional da Microsoft segue procedimentos rigorosos de manipulação de dados e disposição de hardware para garantir que o hardware que contém seus dados não seja disponibilizado para partes não confiáveis. Usamos uma abordagem de apagamento seguro para discos rígidos que dão suporte a ele. Para discos rígidos que não podem ser apagados, usamos um processo de destruição que destrói a unidade e renderiza a recuperação de informações impossível. Esse processo de destruição pode ser despedaçar, destruir, pulverizar ou incinere. Determinamos o meio de descarte de acordo com o tipo de ativo. Guardamos registros da destruição. Todos os serviços do Azure usam serviços de gerenciamento de descarte e armazenamento de mídia aprovado.

## <a name="compliance"></a>Conformidade
Projetamos e gerenciamos a infraestrutura do Azure para atender a um amplo conjunto de padrões de conformidade internacionais e específicos do setor, como ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Também atendemos a padrões específicos de país ou região, incluindo a Austrália IRAP, o G-Cloud do Reino Unido e o Cingapura MTCS. Auditorias rigorosas de terceiros, como as feitas pelo British Standards Institute, verificam a adesão aos controles de segurança estritos que esses padrões exigem.

Para obter uma lista completa dos padrões de conformidade que o Azure segue, consulte as [ofertas de conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, consulte:

- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](protection-customer-data.md)


