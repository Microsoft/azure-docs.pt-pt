---
title: Recursos do Azure, no local e a segurança física | Documentos da Microsoft
description: O artigo descreve os datacenters do Azure, incluindo a infraestrutura física, segurança e ofertas de conformidade.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 5f8378de18796fd679354c88880942df83399ad5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60586803"
---
# <a name="azure-facilities-premises-and-physical-security"></a>Recursos do Azure, no local e a segurança física
Azure é composta por um [infraestruturas de datacenters globalmente distribuídos](https://azure.microsoft.com/global-infrastructure/), oferecendo suporte a milhares de serviços online e que abrangem mais de 100 instalações altamente seguras em todo o mundo.

A infra-estrutura foi concebida para aproximar as aplicações aos utilizadores em todo o mundo, preservando a residência dos dados e oferecendo opções abrangentes de conformidade e resiliência para os clientes. O Azure tem 52 regiões em todo o mundo e está disponível em 140 países.

Uma região é um conjunto de centros de dados é interligados através de uma rede grande e resiliente. A rede inclui a distribuição de conteúdo, balanceamento de carga, redundância e encriptação por predefinição. Com regiões mais globais do que qualquer outro fornecedor de cloud, Azure dá-lhe a flexibilidade de implementar aplicações onde são mais necessários.

As regiões do Azure estão organizadas em geografias. Uma geografia do Azure garante que os requisitos de residência, de soberania, de conformidade e de resiliência dos dados são honrados dentro dos limites geográficos.

As geografias permitem aos clientes com necessidades de residência de dados específicos e conformidade aproximarem os seus dados e aplicações. As geografias são tolerantes a falhas para resistirem a falhas completas na região, através da sua ligação para a infraestrutura de rede dedicada de alta capacidade.

As zonas de disponibilidade são localizações fisicamente separadas dentro de uma região do Azure. Cada zona de disponibilidade é constituída por um ou mais datacenters equipados com energia, refrigeração e redes. As zonas de disponibilidade permitem-lhe executar aplicações críticas com elevada disponibilidade e baixa latência replicação.

A figura seguinte mostra como a infraestrutura global do Azure pares zonas de disponibilidade de região e dentro do mesmo limite de residência de dados para elevada disponibilidade, recuperação após desastre e cópia de segurança.

![Limite de residência de dados do diagrama que mostra][1]

Os datacenters distribuídos geograficamente permite Microsoft ser próximas aos clientes, para reduzir a latência de rede e permitir a cópia de segurança georredundante e ativação pós-falha.

## <a name="physical-security"></a>Segurança física
Microsoft projeta, cria e opera centros de dados de forma estritamente controla o acesso físico às áreas em que os seus dados são armazenados. A Microsoft compreende a importância de proteger os seus dados e está empenhada em ajudar a proteger os centros de dados que contém os seus dados. Temos uma divisão de inteiro na Microsoft dedicado a conceber, criar e utilizar os recursos físicos de suporte do Azure. Esta equipe é investido em manter a segurança física de topo de gama.

A Microsoft leva uma abordagem em camadas para segurança física, para reduzir o risco dos utilizadores não autorizados, obtendo acesso físico aos dados e os recursos de datacenter. Os Datacenters geridos pela Microsoft têm extensa camadas de proteção: aceder a aprovação no perímetro do recurso, no perímetro da criação, dentro da construção e no chão do Centro de dados. Camadas de segurança física são:

- **Pedido de acesso e aprovação.** Tem de solicitar acesso antes de chegar ao centro de dados. Tem de fornecer uma justificação de negócio válida para sua visita, tal como a conformidade ou de fins de auditoria. Todos os pedidos são aprovados numa base de acesso a necessidade por funcionários da Microsoft. Uma base de necessidade de aceder ajuda a manter o número de indivíduos necessário para concluir uma tarefa nos centros de dados para o mínimo absoluto. Após a Microsoft concede permissão, um indivíduo tem acesso apenas para a área discreta do Centro de dados necessária, com base na justificativa comercial aprovados. As permissões estão limitadas a um determinado período de tempo e, em seguida, expirarem.

- **Perímetro do recurso.** Quando chega a um Data Center, tem de passar por um ponto de acesso bem definidos. Normalmente, os limites tall (alta) feitos de cimento e concreto abrangem cada polegada de perímetro. Existem câmeras em torno de centros de dados, com uma equipe de segurança de monitorização seus vídeos em todos os momentos.

- **Entrada de construção.** A centro de dados entrada é composta pelo responsáveis pela segurança profissional, que já passaram por rigorosas verificações de treinamento e em segundo plano. Estes responsáveis pela segurança também regularmente patrol o Centro de dados e monitorizar os vídeos de câmeras dentro do datacenter em todos os momentos.

- **Dentro do prédio.** Depois de introduzir a criação, tem de passar a autenticação de dois fatores com biometria para continuar a se mover através do Centro de dados. Se a sua identidade é validada, pode introduzir apenas a parte do que aprovou o acesso ao centro de dados. Pode ficar lá apenas durante o período de tempo aprovado.

- **Chão do Centro de dados.** Só é permitidas no chão que esteja aprovado para introduzir. É necessário passar um bloqueio de deteção de metal completa do corpo. Para reduzir o risco de dados não autorizado ou saia do Centro de dados sem seu conhecimento, apenas os dispositivos aprovados podem se tornam no chão do Centro de dados. Além disso, o monitor de vídeo câmeras front e back de cada servidor em bastidor. Quando sair do chão do Centro de dados, novamente tem de passar por meio de bloqueio de deteção de metal completa do corpo. Para deixar o Centro de dados, tem de passar por uma verificação de segurança adicional.

A Microsoft requer visitantes surrender distintivos após divergência em qualquer instalação da Microsoft.

## <a name="physical-security-reviews"></a>Revisões de segurança física
Periodicamente, realizamos avaliações de segurança física das instalações, para garantir que os data Centers corretamente os requisitos de segurança do Azure de endereço. O pessoal de fornecedor de alojamento de datacenter fornece a gestão de serviço do Azure. O pessoal não pode iniciar sessão para sistemas do Azure e não tiver acesso físico para o compartimentos e salas de colocalização do Azure.

## <a name="data-bearing-devices"></a>Dispositivos de efeito de dados
A Microsoft utiliza os procedimentos de práticas recomendados e uma solução de apagamento de dados que seja [NIST 800 88 em conformidade](https://csrc.nist.gov/publications/detail/sp/800-88/archive/2006-09-01). Unidades de disco rígido que não não possível limpar, vamos utilizar um processo de destruição que destrói-lo e renderiza a recuperação de informações impossível. Este processo de destruição pode ser disintegrate, que piquem, pulverize ou incinerate. Podemos determinar o meio de disposição, de acordo com o tipo de recurso. Vamos manter os registos de destruição.  

## <a name="equipment-disposal"></a>Disposição de equipamentos
Após fim-de-vida um sistema, as equipas operacionais da Microsoft siga manipulação de dados rigorosas e procedimentos de disposição de hardware para garantir que o hardware que contém os dados não é disponibilizado para partes não fidedignas. Utilizamos uma abordagem de eliminação segura para as unidades de disco rígido que o suportam. Unidades de disco rígido que não não possível limpar, vamos utilizar um processo de destruição que destrói a unidade e renderiza a recuperação de informações impossível. Este processo de destruição pode ser disintegrate, que piquem, pulverize ou incinerate. Podemos determinar o meio de disposição, de acordo com o tipo de recurso. Vamos manter os registos de destruição. Todos os serviços do Azure utilizam serviços de gestão de armazenamento e a disposição de mídia aprovados.

## <a name="compliance"></a>Conformidade
Iremos criar e gerir a infraestrutura do Azure para satisfazer um vasto leque de normas de conformidade internacionais e do setor, tais como o ISO 27001, HIPAA, FedRAMP, SOC 1 e SOC 2. Também podemos atender a normas específicas, incluindo a IRAP na Austrália, G-Cloud no Reino Unido e MTCS em Singapura. Auditorias rigorosas de terceiros, como pelo British Standards Institute, verifique se a aderência aos controles de segurança rigorosos ditados por estas normas.

Para obter uma lista completa de normas de conformidade que o Azure cumpre, consulte a [ofertas de conformidade](https://www.microsoft.com/trustcenter/compliance/complianceofferings). 

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura do Azure, veja:

- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
- [Proteção de dados do cliente do Azure](azure-protection-of-customer-data.md)

<!--Image references-->
[1]: ./media/azure-physical-security/data-residency-boundary.png
