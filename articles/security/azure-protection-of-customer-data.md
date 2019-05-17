---
title: Proteção de dados do cliente no Azure
description: Este artigo aborda como o Azure protege os dados dos clientes.
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
ms.openlocfilehash: 04163d1fa2a46a2de877702d479f439a5e8711d7
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/14/2019
ms.locfileid: "65603144"
---
# <a name="azure-customer-data-protection"></a>Proteção de dados do cliente do Azure   
O acesso a dados do cliente por parte de funcionários operacionais e de suporte da Microsoft é negado por defeito. Quando o acesso aos dados dos clientes é concedido, é necessária aprovação de liderança e, em seguida, acesso é cuidadosamente gerido e registado. Os requisitos de controlo de acesso são estabelecidos pela política de segurança do Azure seguintes:

- Sem acesso aos dados dos clientes, por predefinição.
- Não existem contas de utilizador ou administrador sobre as máquinas virtuais (VMs).
- Conceder o privilégio mínimo necessário para concluir a tarefa; auditoria e registo de pedidos de acesso.

O pessoal de suporte do Azure é atribuído a contas exclusivas do Active Directory empresariais pela Microsoft. Azure baseia-se no Microsoft Active Directory empresarial, gerida pela Microsoft informações tecnologia (MSIT), para controlar o acesso a sistemas de informações da chave. É necessária a autenticação multifator e acesso ser concedido apenas a partir de consolas seguras.

Todas as tentativas de acesso são monitorizadas e podem ser exibidas por meio de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
Azure fornece aos clientes com a segurança dos dados forte, por predefinição e como opções de cliente.

**Segregação de dados**: O Azure é um serviço de multi-inquilino, o que significa que o cliente várias implementações e as VMs são armazenadas no mesmo hardware físico. O Azure utiliza isolação lógica para separar os dados de cada um dos clientes dos dados de outras pessoas. Segregação fornece o dimensionamento e os benefícios económicos dos serviços de multi-inquilinos, enquanto impede rigorosamente os clientes de aceder aos dados do outro.

**Proteção de dados em repouso**: Os clientes são responsáveis por assegurar que os dados armazenados no Azure são encriptados de acordo com seus padrões. O Azure oferece uma vasta gama de capacidades de encriptação, dando aos clientes a flexibilidade de escolher a solução mais adequada às suas necessidades. O Azure Key Vault ajuda os clientes facilmente manter o controlo das chaves que são utilizados por aplicações e serviços cloud para encriptar os dados. O Azure Disk Encryption permite aos clientes encriptar VMs. Encriptação do serviço de armazenamento do Azure torna possível encriptar todos os dados que são colocados numa conta de armazenamento de um cliente.

**Proteção de dados em trânsito**: Os clientes podem ativar a encriptação para o tráfego entre as suas próprias VMs e os utilizadores finais. Azure protege os dados em trânsito de ou para componentes externos e dados em trânsito internamente, tal como entre duas redes virtuais. O Azure utiliza o protocolo Transport Layer Security (TLS) padrão da indústria 1.2 ou posterior com chaves de encriptação RSA/SHA256 2.048 bits, tal como recomendado pela CESG/NCSC, para encriptar comunicações entre:

- O cliente e a cloud.
- Internamente, entre centros de dados e de sistemas do Azure.

**Encriptação**: Encriptação de dados no armazenamento e em trânsito pode ser implementada por parte dos clientes, como melhor prática para garantir a confidencialidade e integridade dos dados. É simples para os clientes configurar os seus serviços cloud do Azure para utilizar SSL para proteger as comunicações da internet e até mesmo entre suas VMs alojadas no Azure.

**Redundância de dados**: Microsoft ajuda a garantir que os dados estão protegidos se existir um cyberattack ou danos físicos para um datacenter. Os clientes podem optar por:

- Armazenamento de no-país/na região e para as considerações de conformidade ou latência.
- Armazenamento de fora-de-país/fora-da-região para fins de recuperação após desastre ou de segurança.

Os dados podem ser replicados dentro de uma área geográfica selecionada para redundância, mas não podem ser transmitidos fora. Os clientes têm várias opções para replicar dados, incluindo o número de cópias e o número e a localização de centros de dados de replicação.

Quando criar a sua conta de armazenamento, selecione uma das seguintes opções de replicação:

- **Armazenamento localmente redundante (LRS)**: O Armazenamento localmente redundante mantém três cópias dos seus dados. LRS é replicado três vezes num único local e numa única região. O LRS protege os dados de falhas de normal hardware, mas não a partir de uma falha de um único local.
- **Armazenamento com redundância de zona (ZRS)**: O Armazenamento com redundância entre zonas mantém três cópias dos seus dados. O ZRS é replicado três vezes em duas ou três instalações para fornecer uma durabilidade superior ao LRS. A replicação ocorre numa única região ou em duas regiões. O ZRS ajuda a garantir que seus dados são duráveis numa única região.
- **Armazenamento georredundante (GRS)**: O Armazenamento georredundante está ativado para a sua conta do Storage por predefinição aquando da sua criação. O GRS mantém seis cópias dos seus dados. Com a GRS, os dados são replicados três vezes dentro da região primária. Os dados também são replicados três vezes numa região secundária a centenas de quilómetros de distância da região primária, fornecendo o nível mais elevado de durabilidade. Se ocorrer uma falha na região primária, armazenamento do Azure efetua a ativação pós-falha para a região secundária. GRS ajuda a garantir que os seus dados são duráveis em duas regiões separadas.

**A destruição de dados**: Quando os clientes eliminar dados, ou desistir do Azure, Microsoft segue normas rigorosas para substituição de recursos de armazenamento antes de sua reutilização, bem como a destruição física do hardware desativado. Microsoft executa uma eliminação completa de dados no pedido de cliente e no término do contrato.

## <a name="customer-data-ownership"></a>Propriedade de dados do cliente
Não inspeciona as Microsoft, aprovar ou monitorizar aplicações que os clientes implementar no Azure. Além disso, a Microsoft não sabe o que tipo de clientes de dados se optar por armazenar no Azure. Microsoft não reivindica a propriedade de dados sobre as informações do cliente que são inseridas no Azure.

## <a name="records-management"></a>Gerenciamento de registros
Azure estabeleceu requisitos internos de retenção de registos de dados back-end. Os clientes são responsáveis por identificar os seus próprios requisitos de retenção do registo. Para registos que são armazenados no Azure, os clientes são responsáveis por extrair seus dados e manter o seu conteúdo fora do Azure durante um período de retenção especificada do cliente.

Azure permite aos clientes exportar os dados e relatórios do produto de auditoria. As exportações são guardadas localmente para manter as informações para uma retenção definida pelo cliente. o período de tempo.

## <a name="electronic-discovery-e-discovery"></a>Deteção eletrónica (deteção eletrónica)
Os clientes do Azure são responsáveis por respeitar os requisitos de deteção eletrónica em termos de uso dos serviços do Azure. Se os clientes do Azure devem preservar os seus dados do cliente, pode exportar e salvar os dados localmente. Além disso, os clientes podem solicitar exportações dos seus dados do departamento de suporte ao cliente Azure. Além de permitir que os clientes exportar os seus dados, o Azure leva extensas de registo e monitorização internamente.

## <a name="next-steps"></a>Passos Seguintes
Para saber mais sobre o que a Microsoft faz para proteger a infraestrutura do Azure, veja:

- [Recursos do Azure, no local e a segurança física](azure-physical-security.md)
- [Disponibilidade da infraestrutura do Azure](azure-infrastructure-availability.md)
- [Componentes de sistema de informações do Azure e limites](azure-infrastructure-components.md)
- [Arquitetura de rede do Azure](azure-infrastructure-network.md)
- [Rede de produção do Azure](azure-production-network.md)
- [Funcionalidades de segurança da base de dados SQL do Azure](azure-infrastructure-sql.md)
- [Gerenciamento e operações de produção do Azure](azure-infrastructure-operations.md)
- [Monitorização de infraestrutura do Azure](azure-infrastructure-monitoring.md)
- [Integridade de infraestrutura do Azure](azure-infrastructure-integrity.md)
