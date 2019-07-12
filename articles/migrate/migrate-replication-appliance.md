---
title: Arquitetura de aplicação de replicação de migrar do Azure | Documentos da Microsoft
description: Fornece uma descrição geral da aplicação de replicação do Azure Migrate
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: 4f4dc307bee4190a0e94ace493053e0cfd01150e
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811443"
---
# <a name="replication-appliance"></a>Aplicação de replicação

Este artigo descreve a aplicação de replicação utilizada pelo Azure Migrate: Avaliação de servidor quando migrar VMs de VMware e máquinas físicas e pública/privada nuvem VMs para o Azure, usando uma migração com base em agente. 

A ferramenta está disponível na [do Azure Migrate](migrate-overview.md) hub. O hub fornece ferramentas nativas para avaliação e migração, bem como ferramentas de outros serviços do Azure e de terceiros fornecedores de software independentes (ISVs).


## <a name="appliance-overview"></a>Descrição geral da aplicação

A aplicação de replicação é implementada como uma única máquina no local, como uma VM de VMware ou um servidor físico. Ele é executado:
- **Aplicação de replicação**: A aplicação de replicação coordena as comunicações e gere a replicação de dados, para VMs de VMware no local e servidores físicos que replicam para o Azure.
- **Servidor de processos**: O servidor de processos, que é instalado por predefinição na aplicação da replicação e faz o seguinte:
    - **Gateway de replicação**: Ele atua como um gateway de replicação. Ele recebe dados de replicação das máquinas ativadas para replicação. Otimiza os dados de replicação com colocação em cache, compressão e encriptação e envia-os para o Azure.
    - **Instalador do agente**: Executa uma instalação push do serviço de mobilidade. Este serviço tem de estar instalado e em execução em cada máquina no local que pretende replicar para a migração.

## <a name="appliance-deployment"></a>Implementação da aplicação

**Implementar como** | **Utilizado para** | **Detalhes**
--- | --- |  ---
VM de VMware | Normalmente utilizado quando migrar VMs do VMware com a ferramenta de migração de migrar do Azure com migração baseada em agente. | Transfira o modelo de OVA do hub do Azure Migrate e importar para o vCenter Server para criar a aplicação da VM.
Uma máquina física | Utilizada ao migrar servidores físicos no local, se não tiver uma infraestrutura do VMware, ou se não for possível criar uma VM do VMware com um modelo de OVA. | Transfira um instalador de software do hub do Azure Migrate e execute-o para configurar a máquina da aplicação.

## <a name="appliance-deployment-requirements"></a>Requisitos de implementação da aplicação

[Revisão](migrate-support-matrix-vmware.md#agent-based-migration-replication-appliance-requirements) os requisitos de implementação.



## <a name="appliance-license"></a>Licença de aplicação
A aplicação vem com uma licença de avaliação do Windows Server 2016, o que é válida por 180 dias. Se o período de avaliação está próximo de expiração, recomendamos que transfira e implemente um novo dispositivo ou que ativa a licença de sistema operativo da aplicação da VM.

## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação para uma VM, começa a replicação inicial para o armazenamento do Azure, através da política de replicação especificada. 
2. Tráfego replica para o armazenamento do Azure pontos finais públicos na Internet. Replicar o tráfego através de uma rede privada virtual (VPN) do site a site a partir de um site no local para o Azure não é suportada.
3. Depois de concluída a replicação inicial, começa a replicação de diferenças. Alterações registadas relativas a uma máquina são registadas.
4. Comunicação acontece da seguinte forma:
    - As VMs comunicar com a aplicação de replicação na porta HTTPS 443 entrada, para a gestão da replicação.
    - A aplicação de replicação orquestra a replicação com o Azure através da porta HTTPS 443 de saída.
    - VMs enviam dados de replicação para o servidor de processos (em execução na aplicação da replicação) na porta HTTPS 9443 de entrada. Esta porta pode ser modificada.
    - O servidor de processos recebe dados de replicação, otimiza e criptografa- e envia-os para o armazenamento do Azure através da porta 443 saída.
5. Os dados de replicação regista ' s land primeiro numa conta de armazenamento de cache no Azure. Estes registos são processados e os dados são armazenados no Azure disco gerido.

![Arquitetura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações da aplicação

A aplicação é atualizada manualmente a partir do hub do Azure Migrate. Recomendamos que execute sempre a versão mais recente.

1. No Azure Migrate > servidores > do Azure Migrate: Avaliação de servidor, servidores de infraestrutura, clique em **servidores de configuração**.
2. Na **servidores de configuração**, um link será exibido na **versão do agente** quando estiver disponível uma nova versão da aplicação da replicação. 
3. Transferir o instalador para a máquina de aplicação de replicação e instalar a atualização. O instalador Deteta a versão atual em execução na aplicação da.
 
## <a name="next-steps"></a>Passos Seguintes

[Saiba como](tutorial-assess-vmware.md#set-up-the-appliance-vm) para configurar a aplicação para VMware.
[Saiba como](tutorial-assess-hyper-v.md#set-up-the-appliance-vm) para configurar a aplicação para o Hyper-V.

