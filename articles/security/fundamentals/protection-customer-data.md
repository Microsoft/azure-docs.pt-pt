---
title: Proteção dos dados dos clientes em Azure
description: Saiba como o Azure protege os dados dos clientes através da segregação de dados, da redundância de dados e da destruição de dados.
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
ms.openlocfilehash: 14589e4efe22d89468b069bf6ff7e3d9babcc714
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87543797"
---
# <a name="azure-customer-data-protection"></a>Proteção de dados do cliente Azure   
O acesso aos dados dos clientes pelas operações da Microsoft e o pessoal de suporte é negado por padrão. Quando o acesso a dados relacionados com um caso de apoio é concedido, só é concedido usando um modelo just-in-time (JIT) usando políticas que são auditadas e verificadas contra as nossas políticas de conformidade e privacidade.  Os requisitos de controlo de acesso são estabelecidos pela seguinte Política de Segurança Azure:

- Sem acesso aos dados dos clientes, por padrão.
- Nenhuma conta de utilizador ou administrador em máquinas virtuais do cliente (VMs).
- Conceda o menor privilégio que for necessário para completar a tarefa; pedidos de auditoria e acesso a registos.

O pessoal de suporte da Azure é atribuído a contas únicas do Ative Directory corporativo pela Microsoft. O Azure conta com o Ative Directory da Microsoft, gerido pela Microsoft Information Technology (MSIT), para controlar o acesso a sistemas de informação chave. É necessária autenticação multi-factor e o acesso é concedido apenas a partir de consolas seguras.

Todas as tentativas de acesso são monitorizadas e podem ser exibidas através de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
O Azure proporciona aos clientes uma forte segurança de dados, tanto por padrão como como opções de clientes.

**Segregação de dados**: O Azure é um serviço multi-inquilino, o que significa que várias implementações de clientes e VMs são armazenados no mesmo hardware físico. O Azure usa o isolamento lógico para segregar os dados de cada cliente a partir dos dados de outros. A segregação proporciona a escala e os benefícios económicos dos serviços multi-inquilinos, impedindo rigorosamente os clientes de acederem aos dados uns dos outros.

**Proteção de dados em repouso**: Os clientes são responsáveis por garantir que os dados armazenados no Azure são encriptados de acordo com os seus padrões. O Azure oferece uma vasta gama de capacidades de encriptação, dando aos clientes a flexibilidade para escolherem a solução que melhor satisfaça as suas necessidades. O Azure Key Vault ajuda os clientes a manter facilmente o controlo das chaves que são usadas por aplicações e serviços na nuvem para encriptar dados. A encriptação do disco Azure permite que os clientes criptografem VMs. A encriptação do serviço de armazenamento Azure permite encriptar todos os dados colocados na conta de armazenamento de um cliente.

**Proteção de dados em trânsito**: A Microsoft fornece uma série de opções que podem ser utilizadas pelos clientes para garantir dados em trânsito internamente dentro da rede Azure e externamente através da Internet para o utilizador final.  Estes incluem comunicação através de Redes Privadas Virtuais (utilizando encriptação IPsec/IKE), Segurança da Camada de Transporte (TLS) 1.2 ou posterior (através de componentes Azure, como O Gateway de Aplicação ou Porta Frontal Azure), protocolos diretamente nas máquinas virtuais Azure (como Windows IPsec ou SMB), e muito mais. 

Além disso, a "encriptação por padrão" utilizando o MACsec (uma norma IEEE na camada de ligação de dados) é ativada para todo o tráfego Azure que viaja entre centros de dados Azure para garantir a confidencialidade e integridade dos dados do cliente. 

**Redundância de dados**: A Microsoft ajuda a garantir que os dados são protegidos se houver um ciberataque ou danos físicos num datacenter. Os clientes podem optar por:

- Armazenagem no país/na região para considerações de conformidade ou de latência.
- Armazenamento fora do país/fora da região para fins de segurança ou recuperação de desastres.

Os dados podem ser replicados dentro de uma área geográfica selecionada para redundância, mas não podem ser transmitidos fora dela. Os clientes têm múltiplas opções para replicar dados, incluindo o número de cópias e o número e localização dos datacenters de replicação.

Ao criar a sua conta de armazenamento, selecione uma das seguintes opções de replicação:

- **Armazenamento localmente redundante (LRS)**: O armazenamento localmente redundante mantém três cópias dos seus dados. LRS é replicado três vezes num único local e numa única região. O LRS protege os seus dados de falhas normais de hardware, mas não de uma falha de uma única instalação.
- **Armazenamento redundante de zona (ZRS)**: O armazenamento redundante em zona mantém três cópias dos seus dados. O ZRS é replicado três vezes em duas a três instalações para proporcionar uma durabilidade mais elevada do que o LRS. A replicação ocorre numa única região ou em duas regiões. O ZRS ajuda a garantir que os seus dados são duráveis numa única região.
- **Armazenamento geo-redundante (GRS)**: O armazenamento geo-redundante está ativado para a sua conta de armazenamento por padrão quando a cria. O GRS mantém seis cópias dos seus dados. Com o GRS, os seus dados são replicados três vezes dentro da região primária. Os seus dados também são replicados três vezes numa região secundária a centenas de quilómetros da região primária, proporcionando o mais alto nível de durabilidade. Em caso de falha na região primária, o Azure Storage falha na região secundária. O GRS ajuda a garantir que os seus dados são duráveis em duas regiões distintas.

**Destruição de dados**: Quando os clientes eliminam dados ou deixam o Azure, a Microsoft segue padrões rigorosos de sobreposição de recursos de armazenamento antes da sua reutilização, bem como a destruição física de hardware desativado. A Microsoft executa uma eliminação completa dos dados a pedido do cliente e à rescisão do contrato.

## <a name="customer-data-ownership"></a>Propriedade de dados do cliente
A Microsoft não inspeciona, aprova ou monitoriza aplicações que os clientes implementam para o Azure. Além disso, a Microsoft não sabe que tipo de dados os clientes escolhem armazenar no Azure. A Microsoft não reclama a propriedade de dados sobre a informação do cliente que está inserida no Azure.

## <a name="records-management"></a>Gestão de registos
O Azure estabeleceu requisitos internos de retenção de registos para dados de back-end. Os clientes são responsáveis pela identificação dos seus próprios requisitos de retenção de registos. Para registos armazenados em Azure, os clientes são responsáveis por extrair os seus dados e reter os seus conteúdos fora do Azure durante um período de retenção especificado pelo cliente.

O Azure permite que os clientes exportem dados e relatórios de auditoria do produto. As exportações são guardadas localmente para reter a informação durante um período de tempo de retenção definido pelo cliente.

## <a name="electronic-discovery-e-discovery"></a>Descoberta electrónica (e-discovery)
Os clientes da Azure são responsáveis pelo cumprimento dos requisitos de e-discovery na sua utilização dos serviços Azure. Se os clientes da Azure tiverem de preservar os seus dados de clientes, podem exportar e guardar os dados localmente. Além disso, os clientes podem solicitar exportações dos seus dados do departamento de Apoio ao Cliente da Azure. Além de permitir que os clientes exportem os seus dados, a Azure realiza uma extensa exploração e monitorização interna.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação azul](infrastructure-components.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
