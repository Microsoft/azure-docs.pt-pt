---
title: Segurança física dos centros de dados do Azure - Microsoft Azure Microsoft Docs
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
ms.date: 04/28/2019
ms.author: terrylan
ms.openlocfilehash: 19fd257e76cdf811343f1c1c772fb3646933bb86
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83774053"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Instalações, instalações e segurança física do Azure
Este artigo descreve o que a Microsoft faz para garantir a infraestrutura Azure.

## <a name="datacenter-infrastructure"></a>Infraestrutura de datacenter
O Azure é composto por uma infraestrutura de [datacenter distribuída globalmente,](https://azure.microsoft.com/global-infrastructure/)apoiando milhares de serviços online e abrangendo mais de 100 instalações altamente seguras em todo o mundo.

A infraestrutura destina-se a aproximar as aplicações dos utilizadores em todo o mundo, preservando a residência de dados e oferecendo opções abrangentes de conformidade e resiliência para os clientes. O Azure tem 58 regiões em todo o mundo e está disponível em 140 países/regiões.

Uma região é um conjunto de datacenters que está interligado através de uma rede maciça e resiliente. A rede inclui distribuição de conteúdo, equilíbrio de carga, redundância e encriptação por padrão. Com regiões mais globais do que qualquer outro fornecedor de nuvem, o Azure dá-lhe a flexibilidade para implementar aplicações onde você precisa delas.

As regiões de Azure estão organizadas em geografias. Uma geografia Azure garante que os requisitos de residência de dados, soberania, conformidade e resiliência sejam honrados dentro das fronteiras geográficas.

As localizações geográficas permitem que os clientes com uma residência de dados e necessidades de conformidade específicas mantenham os respetivos dados e aplicações próximos. As geografias são tolerantes a falhas para suportar a completa falha da região, através da sua ligação à infraestrutura dedicada de rede de alta capacidade.

As zonas de disponibilidade são locais fisicamente separados dentro de uma região de Azure. Cada zona de disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes independentes. As zonas de disponibilidade permitem-lhe executar aplicações críticas de missão com elevada disponibilidade e replicação de baixa latência.

O número seguinte mostra como a infraestrutura global Azure emparelha região e zonas de disponibilidade dentro do mesmo limite de residência de dados para alta disponibilidade, recuperação de desastres e backup.

![Diagrama mostrando limite de residência de dados](./media/physical-security/data-residency-boundary.png)

Os centros de dados distribuídos geograficamente permitem que a Microsoft esteja próxima dos clientes, reduza a latência da rede e permita uma cópia de segurança georedundante e failover.

## <a name="physical-security"></a>Segurança física
A Microsoft projeta, constrói e opera datacenters de forma a controlar rigorosamente o acesso físico às áreas onde os seus dados são armazenados. A Microsoft compreende a importância de proteger os seus dados e está empenhada em ajudar a proteger os datacenters que contêm os seus dados. Temos uma divisão inteira na Microsoft dedicada a projetar, construir e operar as instalações físicas de apoio ao Azure. Esta equipa está a investir na manutenção da segurança física de última geração.

A Microsoft tem uma abordagem em camadas de segurança física, para reduzir o risco de utilizadores não autorizados terem acesso físico aos dados e aos recursos do datacenter. Os datacenters geridos pela Microsoft têm extensas camadas de proteção: aprovação de acesso no perímetro das instalações, no perímetro do edifício, dentro do edifício e no piso do datacenter. Camadas de segurança física são:

- **Pedido de acesso e aprovação.** Deve solicitar acesso antes de chegar ao datacenter. É-lhe exigido que forneça uma justificação de negócio válida para a sua visita, como finalidades de conformidade ou auditoria. Todos os pedidos são aprovados numa base de necessidade de acesso pelos colaboradores da Microsoft. Uma base de necessidade de acesso ajuda a manter o número de indivíduos necessários para completar uma tarefa nos datacenters ao mínimo. Após a Microsoft conceder permissão, um indivíduo só tem acesso à área discreta do datacenter necessário, com base na justificação do negócio aprovado. As permissões limitam-se a um determinado período de tempo e, em seguida, expiram.

- **Perímetro das instalações.** Quando chega a um datacenter, é-lhe exigido que passe por um ponto de acesso bem definido. Tipicamente, cercas altas feitas de aço e betão englobam cada centímetro do perímetro. Há câmaras nos centros de dados, com uma equipa de segurança a monitorizar os seus vídeos a todo o momento.

- **Entrada do prédio.** A entrada do datacenter está equipada com oficiais de segurança profissional que foram submetidos a rigorosos treinos e verificações de antecedentes. Estes agentes de segurança também patrulham rotineiramente o datacenter, e monitorizam os vídeos das câmaras dentro do datacenter em todos os momentos.

- **Dentro do edifício.** Depois de entrar no edifício, tem de passar a autenticação de dois fatores com biometria para continuar a mover-se através do datacenter. Se a sua identidade for validada, só pode introduzir a parte do datacenter a que aprovou o acesso. Só pode ficar lá durante o tempo aprovado.

- **Piso do datacenter.** Só pode entrar no chão onde está aprovado para entrar. É obrigado a passar por um rastreio de deteção de metais de corpo inteiro. Para reduzir o risco de dados não autorizados entrarem ou sairem do datacenter sem o nosso conhecimento, apenas dispositivos aprovados podem entrar no piso do datacenter. Além disso, as câmaras de vídeo monitorizam a frente e a parte traseira de cada prateleira de servidores. Quando sair do piso do datacenter, deve passar novamente pelo rastreio de deteção de metais de corpo inteiro. Para sair do datacenter, é obrigado a passar por um exame de segurança adicional.

A Microsoft exige que os visitantes entreguem emblemas à partida de qualquer instalação da Microsoft.

## <a name="physical-security-reviews"></a>Avaliações de segurança física
Periodicamente, realizamos revisões de segurança física das instalações, para garantir que os datacenters abordam adequadamente os requisitos de segurança do Azure. O pessoal do fornecedor de hospedagem do datacenter não fornece a gestão de serviços Azure. O pessoal não pode entrar nos sistemas Azure e não tem acesso físico à sala de collocalização azure e às gaiolas.

## <a name="data-bearing-devices"></a>Dispositivos de suporte de dados
A Microsoft utiliza procedimentos de boas práticas e uma solução de limpeza que é compatível com [nIST 800-88](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Para discos rígidos que não podem ser apagados, usamos um processo de destruição que a destrói e torna impossível a recuperação da informação. Este processo de destruição pode ser desintegrar, triturar, pulverizar ou incinerar. Determinamos os meios de eliminação de acordo com o tipo de ativo. Guardamos registos da destruição.  

## <a name="equipment-disposal"></a>Eliminação de equipamentos
Após o fim de vida de um sistema, o pessoal operacional da Microsoft segue rigorosos procedimentos de manipulação de dados e eliminação de hardware para garantir que o hardware que contém os seus dados não é disponibilizado a partes não confiáveis. Usamos uma abordagem de apagamento segura para discos rígidos que o suportam. Para discos rígidos que não podem ser apagados, usamos um processo de destruição que destrói o disco e torna impossível a recuperação da informação. Este processo de destruição pode ser desintegrar, triturar, pulverizar ou incinerar. Determinamos os meios de eliminação de acordo com o tipo de ativo. Guardamos registos da destruição. Todos os serviços Azure utilizam serviços aprovados de armazenamento e gestão de eliminação de mídia.

## <a name="compliance"></a>Conformidade
Concebemos e gerimos a infraestrutura Azure para cumprir um vasto conjunto de normas internacionais e específicas de conformidade, tais como ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Também cumprimos normas específicas de país ou região, incluindo Australia IRAP, UK G-Cloud e Singapura MTCS. Auditorias rigorosas de terceiros, como as efetuadas pelo British Standards Institute, verificam a adesão aos rigorosos controlos de segurança destes mandatos de normas.

Para obter uma lista completa das normas de conformidade a que o Azure adere, consulte as ofertas de [Conformidade.](https://www.microsoft.com/trustcenter/compliance/complianceofferings)

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)


