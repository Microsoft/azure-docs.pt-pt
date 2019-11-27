---
title: Arquitetura do dispositivo de replicação de migrações para Azure
description: Fornece uma visão geral do dispositivo de replicação de migração do Azure para a migração baseada em agente.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: ba14767bde5d6cdca3a82dbe4e8a115ec25cc911
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186560"
---
# <a name="replication-appliance"></a>Dispositivo de replicação

Este artigo descreve o dispositivo de replicação usado pelas migrações para Azure: avaliação de servidor ao migrar VMs VMware, máquinas físicas e VMs de nuvem privada/pública para o Azure, usando uma migração baseada em agente. 

A ferramenta está disponível no Hub [migrações para Azure](migrate-overview.md) . O Hub fornece ferramentas nativas para avaliação e migração, bem como ferramentas de outros serviços do Azure e de fornecedores de software independentes (ISVs) de terceiros.


## <a name="appliance-overview"></a>Visão geral do dispositivo

O dispositivo de replicação é implantado como um único computador local, seja como uma VM VMware ou um servidor físico. Ele é executado:
- **Dispositivo de replicação**: o dispositivo de replicação coordena as comunicações e gerencia a replicação de dados para VMs VMware locais e servidores físicos replicando para o Azure.
- **Servidor de processo**: o servidor de processo, que é instalado por padrão no dispositivo de replicação e faz o seguinte:
    - **Gateway de replicação**: ele atua como um gateway de replicação. Ele recebe dados de replicação de computadores habilitados para replicação. Ele otimiza os dados de replicação com caching, compactação e criptografia e os envia para o Azure.
    - **Instalador do agente**: executa uma instalação por push do serviço de mobilidade. Esse serviço deve ser instalado e executado em cada computador local que você deseja replicar para a migração.

## <a name="appliance-deployment"></a>Implantação de dispositivo

**Implantar como** | **Usado para** | **Detalhes**
--- | --- |  ---
VM VMware | Geralmente usado ao migrar VMs VMware usando a ferramenta de migração de migrações para Azure com migração baseada em agente. | Você baixa o modelo OVA do Hub migrações para Azure e importa para vCenter Server para criar a VM do dispositivo.
Um computador físico | Usado ao migrar servidores físicos locais se você não tiver uma infra-estrutura do VMware ou se não for possível criar uma VM do VMware usando um modelo OVA. | Você baixa um instalador de software do Hub migrações para Azure e o executa para configurar o computador do dispositivo.

## <a name="appliance-deployment-requirements"></a>Requisitos de implantação de dispositivo

[Examine](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) os requisitos de implantação.



## <a name="appliance-license"></a>Licença de dispositivo
O dispositivo vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias. Se o período de avaliação estiver próximo da expiração, recomendamos que você baixe e implante um novo dispositivo ou ative a licença do sistema operacional da VM do dispositivo.

## <a name="replication-process"></a>Processo de replicação

1. Quando você habilita a replicação para uma VM, a replicação inicial para o armazenamento do Azure começa, usando a política de replicação especificada. 
2. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure pela Internet. Não há suporte para a replicação de tráfego em uma VPN (rede virtual privada) site a site de um site local para o Azure.
3. Após a conclusão da replicação inicial, a replicação delta começa. As alterações controladas para um computador são registradas.
4. A comunicação acontece da seguinte maneira:
    - As VMs se comunicam com o dispositivo de replicação na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - O dispositivo de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443 de saída.
    - As VMs enviam dados de replicação para o servidor de processo (em execução no dispositivo de replicação) na porta HTTPS 9443 de entrada. Essa porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza-os e criptografa-os e envia-os para o armazenamento do Azure pela porta 443 de saída.
5. Os logs de dados de replicação primeiro se esterram em uma conta de armazenamento de cache no Azure. Esses logs são processados e os dados são armazenados em um disco gerenciado do Azure.

![Arquitetura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações de dispositivo

O dispositivo é atualizado manualmente do Hub migrações para Azure. É recomendável que você sempre execute a versão mais recente.

1. No Azure migrar servidores > > migrações para Azure: avaliação de servidor, servidores de infraestrutura, clique em **servidores de configuração**.
2. Em **servidores de configuração**, um link aparece na **versão do agente** quando uma nova versão do dispositivo de replicação está disponível. 
3. Baixe o instalador no computador do dispositivo de replicação e instale a atualização. O instalador detecta a versão em execução no momento no dispositivo.
 
## <a name="next-steps"></a>Passos Seguintes

[Saiba como](tutorial-assess-vmware.md#set-up-the-appliance-vm) configurar o dispositivo para VMware.
[Saiba como](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) configurar o dispositivo para o Hyper-V.

