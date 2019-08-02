---
title: Proteção de dados do cliente no Azure
description: Este artigo aborda como o Azure protege os dados do cliente.
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
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 741cbc82f2ed3ffffb553b146d981b4e35a273f4
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68726687"
---
# <a name="azure-customer-data-protection"></a>Proteção de dados do cliente do Azure   
O acesso a dados do cliente por parte de funcionários operacionais e de suporte da Microsoft é negado por defeito. Quando o acesso aos dados do cliente é concedido, a aprovação da liderança é necessária e, em seguida, o acesso é gerenciado e registrado cuidadosamente. Os requisitos de controle de acesso são estabelecidos pela seguinte política de segurança do Azure:

- Por padrão, não há acesso aos dados do cliente.
- Não há contas de usuário ou de administrador em VMs (máquinas virtuais) do cliente.
- Conceder o privilégio mínimo necessário para concluir a tarefa; solicitações de acesso de auditoria e log.

A equipe de suporte do Azure recebe contas exclusivas de Active Directory corporativa pela Microsoft. O Azure conta com o Active Directory corporativo da Microsoft, gerenciado pela MSIT (tecnologia de informações da Microsoft), para controlar o acesso aos principais sistemas de informações. A autenticação multifator é necessária e o acesso é concedido somente de consoles seguros.

Todas as tentativas de acesso são monitoradas e podem ser exibidas por meio de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
O Azure fornece aos clientes uma segurança de dados forte, por padrão, e como opções de cliente.

Segregação de **dados**: O Azure é um serviço de vários locatários, o que significa que várias implantações e VMs de clientes são armazenadas no mesmo hardware físico. O Azure usa o isolamento lógico para separar os dados de cada cliente dos dados de outras pessoas. A diferenciação fornece os benefícios de escala e econômico dos serviços multilocatários, ao mesmo tempo em que impede rigorosamente o acesso dos dados de um outro.

**Proteção de dados em repouso**: Os clientes são responsáveis por garantir que os dados armazenados no Azure sejam criptografados de acordo com seus padrões. O Azure oferece uma ampla gama de recursos de criptografia, proporcionando aos clientes a flexibilidade de escolher a solução que melhor atenda às suas necessidades. Azure Key Vault ajuda os clientes a manter facilmente o controle das chaves usadas por aplicativos e serviços em nuvem para criptografar dados. Azure Disk Encryption permite que os clientes criptografem as VMs. O Azure Criptografia do Serviço de Armazenamento torna possível criptografar todos os dados que são colocados na conta de armazenamento de um cliente.

**Proteção de dados em trânsito**: Os clientes podem habilitar a criptografia para o tráfego entre suas próprias VMs e usuários finais. O Azure protege os dados em trânsito para ou de componentes externos e dados em trânsito internamente, como entre duas redes virtuais. O Azure usa o protocolo TLS 1,2 ou mais recente do setor com as chaves de criptografia RSA/SHA256 de 2.048 bits, conforme recomendado pelo CESG/NCSC, para criptografar as comunicações entre:

- O cliente e a nuvem.
- Internamente entre sistemas e data centers do Azure.

**Encriptação**: A criptografia de dados no armazenamento e em trânsito pode ser implantada pelos clientes como uma prática recomendada para garantir a confidencialidade e a integridade dos dados. É fácil para os clientes configurar seus serviços de nuvem do Azure para usar SSL para proteger as comunicações da Internet e até mesmo entre suas VMs hospedadas no Azure.

**Redundância de dados**: A Microsoft ajuda a garantir que os dados sejam protegidos se houver um dano físico ou cyberattack em um datacenter. Os clientes podem optar por:

- Armazenamento no país/região para considerações de conformidade ou latência.
- Armazenamento fora do país/fora de região para fins de segurança ou recuperação de desastre.

Os dados podem ser replicados dentro de uma área geográfica selecionada para redundância, mas não podem ser transmitidos fora dele. Os clientes têm várias opções para replicar dados, incluindo o número de cópias e o número e o local dos data centers de replicação.

Ao criar sua conta de armazenamento, selecione uma das seguintes opções de replicação:

- **LRS (armazenamento com redundância local)** : O Armazenamento localmente redundante mantém três cópias dos seus dados. LRS é replicado três vezes num único local e numa única região. O LRS protege seus dados contra falhas normais de hardware, mas não de uma falha de um único recurso.
- **Armazenamento com redundância de zona (ZRS)** : O Armazenamento com redundância entre zonas mantém três cópias dos seus dados. O ZRS é replicado três vezes em duas a três instalações para fornecer maior durabilidade do que o LRS. A replicação ocorre em uma única região ou em duas regiões. O ZRS ajuda a garantir que seus dados sejam duráveis em uma única região.
- **Armazenamento com redundância geográfica (GRS)** : O Armazenamento georredundante está ativado para a sua conta do Storage por predefinição aquando da sua criação. O GRS mantém seis cópias dos seus dados. Com o GRS, seus dados são replicados três vezes na região primária. Seus dados também são replicados três vezes em uma região secundária a centenas de quilômetros de distância da região primária, fornecendo o nível mais alto de durabilidade. No caso de uma falha na região primária, o armazenamento do Azure faz failover para a região secundária. O GRS ajuda a garantir que seus dados sejam duráveis em duas regiões separadas.

**Destruição de dados**: Quando os clientes excluem dados ou saem do Azure, a Microsoft segue padrões estritos para substituir recursos de armazenamento antes de sua reutilização, bem como a destruição física de hardware encerrado. A Microsoft executa uma exclusão completa dos dados na solicitação do cliente e no encerramento do contrato.

## <a name="customer-data-ownership"></a>Propriedade de dados do cliente
A Microsoft não inspeciona, aprova ou monitora aplicativos que os clientes implantam no Azure. Além disso, a Microsoft não sabe que tipo de dados os clientes optam por armazenar no Azure. A Microsoft não alega a propriedade dos dados sobre as informações do cliente inseridas no Azure.

## <a name="records-management"></a>Gerenciamento de registros
O Azure estabeleceu registros internos-requisitos de retenção para dados de back-end. Os clientes são responsáveis por identificar seus próprios requisitos de retenção de registros. Para registros armazenados no Azure, os clientes são responsáveis por extrair seus dados e reter seu conteúdo fora do Azure para um período de retenção especificado pelo cliente.

O Azure permite que os clientes exportem dados e relatórios de auditoria do produto. As exportações são salvas localmente para manter as informações de um período de tempo de retenção definido pelo cliente.

## <a name="electronic-discovery-e-discovery"></a>Descoberta eletrônica (e-Discovery)
Os clientes do Azure são responsáveis por cumprir os requisitos de descoberta eletrônica no uso dos serviços do Azure. Se os clientes do Azure precisarem preservar os dados do cliente, eles poderão exportar e salvar os dados localmente. Além disso, os clientes podem solicitar exportações de seus dados do departamento de atendimento ao cliente do Azure. Além de permitir que os clientes exportem seus dados, o Azure realiza o log e o monitoramento extensivos internamente.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, consulte:

- [Instalações do Azure, local e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura do Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informações do Azure](infrastructure-components.md)
- [Arquitetura de rede do Azure](infrastructure-network.md)
- [Rede de produção do Azure](production-network.md)
- [Recursos de segurança do banco de dados SQL do Azure](infrastructure-sql.md)
- [Operações e gerenciamento de produção do Azure](infrastructure-operations.md)
- [Monitoramento de infraestrutura do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura do Azure](infrastructure-integrity.md)
