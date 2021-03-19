---
title: Segurança física dos datacenters da Azure - Microsoft Azure | Microsoft Docs
description: O artigo descreve o que a Microsoft faz para proteger os datacenters do Azure, incluindo infraestruturas físicas, segurança e ofertas de conformidade.
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
ms.date: 07/10/2020
ms.author: terrylan
ms.openlocfilehash: 3eac3c9abda74f0414fc97512f011d235d3991f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87076320"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Instalações, instalações e segurança física
Este artigo descreve o que a Microsoft faz para proteger a infraestrutura Azure.

## <a name="datacenter-infrastructure"></a>Infraestrutura de datacenter
O Azure é composto por uma infraestrutura de [datacenter distribuída globalmente,](https://azure.microsoft.com/global-infrastructure/)apoiando milhares de serviços online e abrangendo mais de 100 instalações altamente seguras em todo o mundo.

A infraestrutura destina-se a aproximar as aplicações dos utilizadores em todo o mundo, preservando a residência de dados e oferecendo opções abrangentes de conformidade e resiliência para os clientes. O Azure tem 58 regiões em todo o mundo e está disponível em 140 países/regiões.

Uma região é um conjunto de datacenters que está interligado através de uma rede maciça e resiliente. A rede inclui distribuição de conteúdos, equilíbrio de carga, redundância e [encriptação da camada de ligação de dados por padrão](encryption-overview.md#encryption-of-data-in-transit) para todo o tráfego Azure dentro de uma região ou viajar entre regiões. Com regiões mais globais do que qualquer outro fornecedor de nuvem, o Azure dá-lhe a flexibilidade para implementar aplicações onde precisa.

As regiões de Azure estão organizadas em geografias. Uma geografia azul garante que os requisitos de residência, soberania, conformidade e resiliência sejam respeitados dentro dos limites geográficos.

As localizações geográficas permitem que os clientes com uma residência de dados e necessidades de conformidade específicas mantenham os respetivos dados e aplicações próximos. As geografias são tolerantes a falhas para resistir a falhas completas da região, através da sua ligação à infraestrutura dedicada e de alta capacidade de rede.

As zonas de disponibilidade são locais fisicamente separados dentro de uma região de Azure. Cada zona de disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. As zonas de disponibilidade permitem executar aplicações críticas de missão com alta disponibilidade e replicação de baixa latência.

O número a seguir mostra como a infraestrutura global Azure emparelha a região e as zonas de disponibilidade dentro do mesmo limite de residência de dados para alta disponibilidade, recuperação de desastres e backup.

![Diagrama mostrando limite de residência de dados](./media/physical-security/data-residency-boundary.png)

Os centros de dados distribuídos geograficamente permitem que a Microsoft esteja próxima dos clientes, reduza a latência da rede e permita a cópia de segurança e falha geo redundantes.

## <a name="physical-security"></a>Segurança física
A Microsoft projeta, constrói e opera datacenters de forma a controlar rigorosamente o acesso físico às áreas onde os seus dados são armazenados. A Microsoft compreende a importância de proteger os seus dados e está empenhada em ajudar a proteger os datacenters que contêm os seus dados. Temos uma divisão inteira na Microsoft dedicada a projetar, construir e operar as instalações físicas de apoio ao Azure. Esta equipa está investida na manutenção da segurança física de última geração.

A Microsoft tem uma abordagem em camadas de segurança física, para reduzir o risco de utilizadores não autorizados terem acesso físico aos dados e aos recursos do datacenter. Os datacenters geridos pela Microsoft têm extensas camadas de proteção: aprovação de acesso no perímetro da instalação, no perímetro do edifício, dentro do edifício e no piso do datacenter. Camadas de segurança física são:

- **Pedido de acesso e aprovação.** Deve solicitar acesso antes de chegar ao datacenter. Você é obrigado a fornecer uma justificação de negócio válida para a sua visita, como conformidade ou fins de auditoria. Todos os pedidos são aprovados numa base de necessidade de acesso pelos colaboradores da Microsoft. Uma base de necessidade de acesso ajuda a manter o número de indivíduos necessários para completar uma tarefa nos centros de dados ao mínimo. Após a Microsoft conceder permissão, um indivíduo só tem acesso à área discreta do datacenter necessário, com base na justificação do negócio aprovado. As permissões são limitadas a um determinado período de tempo e, em seguida, expiram.

- **O perímetro das instalações.** Quando chega a um datacenter, é-lhe exigido que passe por um ponto de acesso bem definido. Normalmente, cercas altas feitas de aço e betão englobam cada centímetro do perímetro. Há câmaras à volta dos datacenters, com uma equipa de segurança a monitorizar os seus vídeos a todo o momento.

- **Entrada do prédio.** A entrada do datacenter é dotado de agentes de segurança profissional que foram submetidos a rigorosos treinos e verificações de antecedentes. Estes agentes de segurança também patrulham rotineiramente o datacenter e monitorizam os vídeos das câmaras dentro do datacenter a todo o momento.

- **Dentro do edifício.** Depois de entrar no edifício, tem de passar a autenticação de dois fatores com biometria para continuar a mover-se através do datacenter. Se a sua identidade for validada, pode introduzir apenas a parte do datacenter a que aprovou o acesso. Só pode ficar lá durante o tempo aprovado.

- **Datacenter floor.** Só pode entrar no chão que está autorizado a entrar. É obrigado a passar por um rastreio de deteção de metais corporais. Para reduzir o risco de entrada ou saída de dados não autorizados do datacenter sem o nosso conhecimento, apenas os dispositivos aprovados podem entrar no piso do datacenter. Além disso, as câmaras de vídeo monitorizam a frente e a parte traseira de cada prateleira do servidor. Quando sai do piso do datacenter, deve passar novamente pelo rastreio de deteção de metais corporais. Para deixar o datacenter, é obrigado a passar por uma verificação de segurança adicional.

A Microsoft exige que os visitantes entreguem crachás à partida de qualquer instalação da Microsoft.

## <a name="physical-security-reviews"></a>Avaliações de segurança física
Periodicamente, realizamos revisões físicas de segurança das instalações, para garantir que os datacenters abordam corretamente os requisitos de segurança da Azure. O pessoal do fornecedor de hospedagem do datacenter não fornece a gestão do serviço Azure. O pessoal não pode entrar nos sistemas Azure e não tem acesso físico à sala de colagens e jaulas do Azure.

## <a name="data-bearing-devices"></a>Dispositivos de rolamento de dados
A Microsoft utiliza procedimentos de boas práticas e uma solução de limpeza que é [compatível com o NIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Para discos rígidos que não podem ser apagados, usamos um processo de destruição que o destrói e torna impossível a recuperação de informação. Este processo de destruição pode ser para desintegrar, triturar, pulverizar ou incinerar. Determinamos os meios de eliminação de acordo com o tipo de ativo. Guardamos registos da destruição.  

## <a name="equipment-disposal"></a>Eliminação de equipamentos
Após o fim de vida de um sistema, o pessoal operacional da Microsoft segue rigorosos procedimentos de tratamento de dados e eliminação de hardware para garantir que o hardware que contém os seus dados não é disponibilizado a partes não fidedquis. Usamos uma abordagem de apagamento segura para discos rígidos que a suportam. Para discos rígidos que não podem ser apagados, usamos um processo de destruição que destrói a unidade e torna impossível a recuperação de informação. Este processo de destruição pode ser para desintegrar, triturar, pulverizar ou incinerar. Determinamos os meios de eliminação de acordo com o tipo de ativo. Guardamos registos da destruição. Todos os serviços da Azure utilizam serviços aprovados de armazenamento e gestão de eliminação de meios de comunicação.

## <a name="compliance"></a>Conformidade
Concebemos e gerimos a infraestrutura Azure para cumprir um vasto conjunto de normas de conformidade internacionais e específicas da indústria, tais como ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Também cumprimos normas específicas para o país ou para a região, incluindo a Austrália IRAP, UK G-Cloud e Singapura MTCS. Auditorias rigorosas de terceiros, como as realizadas pelo British Standards Institute, verificam a adesão aos rigorosos controlos de segurança que estas normas mandam.

Para obter uma lista completa de normas de conformidade a que a Azure adere, consulte as [ofertas de Conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)


