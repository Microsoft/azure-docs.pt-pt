---
title: Proteção dos dados dos clientes em Azure
description: Este artigo aborda a forma como o Azure protege os dados dos clientes.
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
ms.openlocfilehash: 630304bec17dd34befab4e5bd9f1cfdfb6505645
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811425"
---
# <a name="azure-customer-data-protection"></a>Proteção de dados dos clientes azure   
O acesso aos dados dos clientes pelas operações da Microsoft e pessoal de suporte é negado por padrão. Quando o acesso aos dados do cliente é concedido, é necessária a aprovação da liderança e, em seguida, o acesso é cuidadosamente gerido e registado. Os requisitos de controlo de acesso são estabelecidos pela seguinte Política de Segurança Do Azure:

- Sem acesso aos dados do cliente, por defeito.
- Não há contas de utilizador ou administrador em máquinas virtuais do cliente (VMs).
- Conceder o menor privilégio que é necessário para completar a tarefa; auditoria e pedidos de acesso de registo.

O pessoal de suporte azure é atribuído contas únicas do Ative Directy corporativo pela Microsoft. O Azure conta com o Ative Directory corporativo da Microsoft, gerido pela Microsoft Information Technology (MSIT), para controlar o acesso aos principais sistemas de informação. É necessária a autenticação de vários fatores e o acesso só é concedido a partir de consolas seguras.

Todas as tentativas de acesso são monitorizadas e podem ser exibidas através de um conjunto básico de relatórios.

## <a name="data-protection"></a>Proteção de dados
A Azure fornece aos clientes uma forte segurança de dados, tanto por defeito como como opções de cliente.

**Segregação**de dados : O Azure é um serviço multi-inquilino, o que significa que várias implementações de clientes e VMs são armazenados no mesmo hardware físico. O Azure usa o isolamento lógico para segregar os dados de cada cliente dos dados dos outros. A segregação proporciona a escala e os benefícios económicos dos serviços multi-inquilinos, ao mesmo tempo que impede rigorosamente os clientes de acederem aos dados uns dos outros.

**Proteção de dados em repouso**: Os clientes são responsáveis por garantir que os dados armazenados no Azure são encriptados de acordo com as suas normas. O Azure oferece uma vasta gama de capacidades de encriptação, dando aos clientes a flexibilidade para escolherem a solução que melhor responda às suas necessidades. O Azure Key Vault ajuda os clientes a manter facilmente o controlo das chaves que são usadas por aplicações e serviços na nuvem para encriptar dados. A encriptação do disco Azure permite que os clientes criptografem VMs. A encriptação do Serviço de Armazenamento Azure permite encriptar todos os dados que são colocados na conta de armazenamento de um cliente.

**Proteção de dados em trânsito**: Os clientes podem ativar a encriptação para o tráfego entre os seus próprios VMs e utilizadores finais. O Azure protege os dados em trânsito de ou para componentes externos e dados em trânsito interna, como entre duas redes virtuais. O Azure utiliza o protocolo de segurança da camada de transporte (TLS) 1.2 ou posterior com chaves de encriptação RSA/SHA256 de 2.048 bits, conforme recomendado pelo CESG/NCSC, para encriptar comunicações entre:

- O cliente e a nuvem.
- Internamente entre sistemas Azure e centros de dados.

**Encriptação**: A encriptação de dados armazenados e em trânsito pode ser implementada pelos clientes como uma melhor prática para garantir a confidencialidade e integridade dos dados. É simples para os clientes configurar os seus serviços de nuvem Azure para usar TLS para proteger as comunicações da internet e até entre os seus VMs hospedados no Azure.

**Redundância**de dados : A Microsoft ajuda a garantir que os dados são protegidos se houver um ciberataque ou danos físicos num datacenter. Os clientes podem optar por:

- Armazenamento no país/na região para considerações de conformidade ou latência.
- Armazenamento fora do país/fora da região para fins de segurança ou recuperação de desastres.

Os dados podem ser replicados dentro de uma área geográfica selecionada para despedimento, mas não podem ser transmitidos fora dele. Os clientes têm múltiplas opções para replicar dados, incluindo o número de cópias e o número e localização dos datacenters de replicação.

Quando criar a sua conta de armazenamento, selecione uma das seguintes opções de replicação:

- **Armazenamento localmente redundante (LRS)**: Armazenamento localmente redundante mantém três cópias dos seus dados. LRS é replicado três vezes num único local e numa única região. O LRS protege os seus dados de falhas normais de hardware, mas não de uma falha de uma única instalação.
- **Armazenamento redundante em zona (ZRS)**: O armazenamento redundante em zonas mantém três cópias dos seus dados. O ZRS é replicado três vezes em duas a três instalações para proporcionar uma durabilidade mais elevada do que o LRS. A replicação ocorre numa única região ou em duas regiões. O ZRS ajuda a garantir que os seus dados são duráveis numa única região.
- **Armazenamento geo-redundante (GRS)**: O armazenamento geo-redundante está ativado para a sua conta de armazenamento por padrão quando a criar. O GRS mantém seis cópias dos seus dados. Com GRS, os seus dados são replicados três vezes dentro da região primária. Os seus dados também são replicados três vezes numa região secundária a centenas de quilómetros da região primária, proporcionando o mais alto nível de durabilidade. Em caso de falha na região primária, o Armazenamento Azure falha na região secundária. GrS ajuda a garantir que os seus dados são duráveis em duas regiões distintas.

**Destruição de dados**: Quando os clientes apagam dados ou saem do Azure, a Microsoft segue padrões rigorosos de sobreposição de recursos de armazenamento antes da sua reutilização, bem como a destruição física de hardware desativado. A Microsoft executa uma supressão completa dos dados a pedido do cliente e sobre a rescisão do contrato.

## <a name="customer-data-ownership"></a>Propriedade dos dados do cliente
A Microsoft não inspeciona, aprova ou monitoriza aplicações que os clientes implementam para o Azure. Além disso, a Microsoft não sabe que tipo de dados os clientes escolhem armazenar no Azure. A Microsoft não reclama a propriedade dos dados sobre as informações do cliente que foram introduzidas no Azure.

## <a name="records-management"></a>Gestão de registos
O Azure estabeleceu requisitos internos de retenção de registos para dados de back-end. Os clientes são responsáveis pela identificação dos seus próprios requisitos de retenção de registos. Para registos armazenados no Azure, os clientes são responsáveis pela extração dos seus dados e pela conservação dos seus conteúdos fora do Azure por um período de retenção especificado pelo cliente.

O Azure permite que os clientes exportem dados e relatórios de auditoria do produto. As exportações são guardadas localmente para reter a informação por um período de tempo de retenção definido pelo cliente.

## <a name="electronic-discovery-e-discovery"></a>Descoberta electrónica (e-discovery)
Os clientes da Azure são responsáveis pelo cumprimento dos requisitos de e-discovery na utilização dos serviços Azure. Se os clientes da Azure tiverem de preservar os seus dados de clientes, podem exportar e guardar os dados localmente. Além disso, os clientes podem solicitar a exportação dos seus dados do departamento de Apoio ao Cliente do Azure. Além de permitir que os clientes exportem os seus dados, o Azure realiza uma extensa exploração madeireira e monitorização interna.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para garantir a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Componentes e limites do sistema de informação Azure](infrastructure-components.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
