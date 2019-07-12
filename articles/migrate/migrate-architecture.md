---
title: Arquitetura de migração do Azure | Documentos da Microsoft
description: Disponibiliza uma descrição geral do serviço do Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 16fbf8d3523ac2ab36447aa51a93a0bb5a9fad54
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811430"
---
# <a name="azure-migrate-architecture-and-processes"></a>Arquitetura de migração do Azure e os processos

[O Azure Migrate](migrate-overview.md) fornece um hub de ferramentas que ajudam a detetar, avaliar e migrar cargas de trabalho, aplicações e infraestrutura para o Microsoft Azure. O hub inclui as ferramentas do Azure Migrate e ofertas de (ISV fornecedor) de terceiros independentes de software. 

 Este artigo resume a avaliação e a arquitetura de migração e a processos de avaliação do servidor de migrar do Azure e migração do servidor de migrar do Azure.

## <a name="assessment-with-azure-migrate-server-assessment"></a>Avaliação com o Azure Migrate avaliação de servidor

Avaliação Server do Azure Migrate pode avaliar VMware VMs e VMs de Hyper-V para migração para o Azure. Avaliação funciona da seguinte forma:

1. **Preparar a avaliação da**: No local, tudo de uma simples o Azure Migrate aplicação no local como uma VM de VMware ou VM do Hyper-V e registre-se a aplicação com o Azure Migrate.
2. **Detetar VMs**: A aplicação do Azure Migrate é executado para detetar VMs no local. 
    - Nada tem de ser instalado em VMs detetadas.
    - Metadados VM incluem informações sobre núcleos, memória, discos, tamanhos de discos e adaptadores de rede.
    - Dados de desempenho incluem informações sobre a CPU e memória, utilização, IOPS, débito de disco (MBps), de disco e rede de saída (MBps)
- **Reunir e avaliar máquinas**: Após a conclusão da deteção, no Azure portal que reúne VMs localizadas em grupos que normalmente consistem em VMs que pretende migrar em conjunto. Execute uma avaliação num grupo.


## <a name="vmware-assessment"></a>Avaliação de VMware 

O diagrama resume como a avaliação da VM de VMware funciona através de avaliação do servidor de migrar do Azure.

![Arquitetura da avaliação da VMware](./media/migrate-architecture/sas-architecture-vmware.png)

O processo é o seguinte:

1. **Implementar a aplicação do Azure Migrate**:

    a. Transfira o modelo de (OVA) do portal do Azure.

    b. Importá-lo para a máquina do servidor vCenter para criar a VM.

    c. Ligar a esta VM e configurar as definições básicas para o mesmo para registá-lo com o Azure Migrate.

2. **Iniciar deteção**:

    a. Ligue-se para a aplicação de Recoletor em execução na aplicação para iniciar a deteção.

    b. A aplicação envia continuamente os dados de metadados e o desempenho das VMs para o Azure.

    - A aplicação é sempre ligada ao serviço Azure Migrate.
    - Alterações de ambiente durante a deteção contínua são capturadas. Por exemplo adicionando VMs âmbito de deteção ou a adição de discos de VM ou NICs.

3. **Avaliar máquinas**:

    a. Após a conclusão da deteção, no Azure portal que reúne VMs localizadas em grupos.  Normalmente por um grupo de VMs que pretende migrar em conjunto.

    b. Execute uma avaliação para cada grupo.

4. **Rever a avaliação**: 

    a. Depois de concluída a avaliação, vê-lo no portal.

    b. Para análise adicional, baixe a avaliação no formato Excel.



### <a name="vmware-ports"></a>Portas de VMware
O Azure Migrate utiliza as seguintes portas de ligação do VMware:

**Origem** | **Target** | **Porta** | **Detalhes**
--- | --- | --- | ---
Aplicação do Azure Migrate | Serviço do Azure Migrate | Destino-TCP 443 | Envie dados de desempenho e de metadados para o Azure Migrate.
Aplicação do Azure Migrate | vCenter Server | Destino-TCP 443 | Ligar ao vCenter Server para dados de metadados e o desempenho. 443 é padrão, mas pode ser modificada com vCenter escuta numa porta diferente. 
Cliente RDP | Aplicação do Azure Migrate | Target-TCP3389 | Ligação de RDP para a deteção de Acionador da aplicação.

### <a name="collected-vmware-metadata"></a>Metadados recolhidos do VMware

A aplicação envia metadados e dados relacionados com o desempenho das VMs para o Azure.

**ação** | **Detalhes**
--- | ---
**Metadados recolhidos** | nome VM do vCenter<br/> caminho de VM do vCenter (pasta de anfitrião/cluster)<br/> Endereços IP e MAC<br/> Sistema operativo<br/> Número de discos/núcleos/NICs<br/> Tamanho de memória e disco.
**Dados de desempenho recolhidos** | Utilização da CPU/memória<br/> Por dados do disco (débito de leitura/escrita de disco; leituras/escritas em disco por segundo)<br/> Dados NIC (rede na rede de saída).<br/><br/> Dados de desempenho são recolhidos continuamente depois da aplicação da liga-se ao vCenter Server. Não são recolhidos dados históricos.

## <a name="hyper-v-assessment"></a>Avaliação do Hyper-V

O diagrama resume como funciona a avaliação Hyper-V brilhar avaliação do servidor de migrar do Azure.

![Arquitetura da avaliação Hyper-V](./media/migrate-architecture/sas-architecture-hyper-v.png)

O processo é o seguinte:

1. **Criar a aplicação do Azure Migrate**:

    a. Baixe a VM em formato compactado no portal do Azure.

    b. Importá-lo para um anfitrião Hyper-V.

    c. Ligar-se à aplicação da VM. Configurar as definições básicas para o mesmo e registá-lo com o Azure Migrate.

2. **Iniciar deteção**:

    a. Ligue-se para a aplicação do Azure Migrate para iniciar a deteção. Nada tem de ser instalado em VMs detetadas.

    b. A aplicação envie continuamente os dados de metadados e o desempenho da VM para o Azure Migrate.

    - A aplicação é sempre ligada ao serviço Azure Migrate (utilizar sessões CIM).
    - Alterações de ambiente durante a deteção contínua são capturadas. Por exemplo adicionando VMs âmbito de deteção ou a adição de discos de VM ou NICs.


3. **Avaliar máquinas**:

    a. Após a conclusão da deteção, no Azure portal que reúne VMs localizadas em grupos.  Normalmente por um grupo de VMs que pretende migrar em conjunto.

    b. Execute uma avaliação para cada grupo.

4. **Rever a avaliação**:

    a. Depois de concluída a avaliação, vê-lo no portal.

    b. Para análise adicional, baixe a avaliação no formato Excel.

### <a name="hyper-v-ports"></a>Portas do Hyper-V

Serviço do Azure Migrate utiliza as seguintes portas de ligação para o Hyper-v:

**Origem** | **Target** | **Porta** | **Detalhes**
--- | --- | --- | ---
Aplicação do Azure Migrate | Serviço do Azure Migrate | Destino-TCP 443 | Envie dados de desempenho e de metadados para o Azure Migrate.
Aplicação do Azure Migrate | Anfitrião/cluster de Hyper-V | Portas de destino padrão WinRM-HTTP:5985; HTTPS:5986 | Ligar ao anfitrião para os dados de metadados e o desempenho. Utilizado para a ligação de sessão CIM
Cliente RDP | Aplicação do Azure Migrate | Target-TCP3389 | Ligação de RDP para a deteção de Acionador da aplicação.

## <a name="collected-hyper-v-vm-metadata"></a>Metadados recolhidos de VM de Hyper-V

A aplicação envia metadados e dados relacionados com o desempenho das VMs para o Azure.


**ação** | **Detalhes**
--- | ---
**Metadados recolhidos** | o nome da VM<br/> Caminho da VM (pasta de anfitrião/cluster)<br/> Endereços IP e MAC<br/> Sistema operativo<br/> Número de discos/núcleos/NICs<br/> Tamanho de memória e disco<br/> Disco IOPS, débito de disco (MBps), a saída de rede (MBps)
**Dados de desempenho recolhidos** |  Utilização da CPU/memória<br/> Por dados do disco (débito de leitura/escrita de disco; leituras/escritas em disco por segundo)<br/> Dados NIC (rede na rede de saída).<br/><br/> Dados de desempenho são recolhidos continuamente quando o dispositivo estiver ligado ao anfitrião do Hyper-V. Não são recolhidos dados históricos.




## <a name="migration-with-azure-migrate-server-migration"></a>Migração com o Azure Migrate a migração do servidor

Utilizar a migração de servidor de migrar do Azure, pode migrar o seguinte para o Azure:

- VMs VMware no local
- VMs de Hyper-V no local
- Servidores físicos no local
- Instâncias de VM do Windows de AWS
- Instâncias de VM do Windows de GCP

O processo de migração é ligeiramente diferente, consoante o que está migrando.


## <a name="migrate-vmware-vms"></a>Migrar VMs de VMware

Migração de servidor migrar do Azure oferece dois métodos para migrar VMs de VMware no local:

- **Replicação sem agente**: Migre VMs sem precisar instalar nada nos mesmos.
- **Replicação baseada em agente**. Instale um agente na VM para replicação.

Embora os replicação sem agente seja mais fácil de uma perspectiva de implantação, atualmente tem várias limitações. [Saiba mais](server-migrate-overview.md) sobre essas limitações.
 

### <a name="agent-based-migration"></a>Migração com base no agente

Com base em agente de migração de VMs de VMware requer um número de componentes a serem implantados, conforme resumido na tabela.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**Máquina do servidor de configuração** | Uma única VM do VMware que é implementada a partir de um modelo do OVF transferido no local.<br/><br/> A máquina é executada todos os componentes do Site Recovery no local, que incluem o servidor de configuração e o servidor de processos. | **Servidor de configuração**: Coordena as comunicações entre no local e o Azure e gere a replicação de dados.<br/><br/> **Servidor de processos**: Instalado por predefinição no servidor de configuração. Recebe dados de replicação. otimiza-os com colocação em cache, compressão e encriptação; e envia-os para o Azure. O servidor de processos também instala o serviço de mobilidade de recuperação de sites do Azure nas VMs e efetua a deteção automática de máquinas no local.
**Serviço de mobilidade** | O serviço de mobilidade tem de ser instalado em cada VM de VMware, replicar. | Recomendamos que permite que a instalação automática do servidor de processos. Em alternativa, pode instalar o serviço manualmente ou utilizar um método de implementação automatizada, como o System Center Configuration Manager.





### <a name="agent-based-replication-process"></a>Processo de replicação baseada em agente

![Processo de replicação](./media/migrate-architecture/v2a-architecture-henry.png)

1. Quando ativa a replicação para uma VM, começa a replicação inicial para o Azure. 
    - A replicação é o nível de bloco, quase contínua, utilizando o agente de serviço de mobilidade em execução na VM.
    - Definições de política de replicação estão aplicadas:
        - **Limiar RPO**. Esta definição não afeta os replicação. Ajuda com a monitorização. É gerado um evento, e, opcionalmente, enviado um e-mail, se o atual RPO excede o limite de limiar que especificar.
        - **Retenção do ponto de recuperação**. Esta definição especifica a distância back no tempo no que qual pretende ir quando ocorre uma interrupção. Máximo de retenção no armazenamento premium é de 24 horas. No armazenamento standard é 72 horas. 
        - **Instantâneos consistentes com a aplicação**. Instantâneos consistentes com a aplicação pode demorar cada 1 a 12 horas, consoante as suas necessidades de aplicação. Os instantâneos são instantâneos de Blobs do Azure standard. O agente de mobilidade em execução numa VM solicita um instantâneo VSS em conformidade com esta definição e os indicadores de ponto de ponto no tempo como uma aplicação consistente no fluxo de replicação.

2. Tráfego replica para o armazenamento do Azure pontos finais públicos na Internet.

    - Em alternativa, pode utilizar o ExpressRoute do Azure com [peering da Microsoft](../expressroute/expressroute-circuit-peerings.md#microsoftpeering).
    - Replicar o tráfego através de uma rede privada virtual (VPN) do site a site a partir de um site no local para o Azure não é suportada.
3. Depois de concluída a replicação inicial, começa a replicação das alterações delta para o Azure. Alterações registadas relativas a uma máquina são enviadas para o servidor de processos.
2. Comunicação acontece da seguinte forma:

    - As VMs comunicar com o servidor de configuração no local na porta HTTPS 443 entrada, para a gestão da replicação.
    - O servidor de configuração orquestra a replicação com o Azure através da porta HTTPS 443 de saída.
    - VMs enviam dados de replicação para o servidor de processos (em execução na máquina do servidor de configuração) na porta HTTPS 9443 de entrada. Esta porta pode ser modificada.
    - O servidor de processos recebe dados de replicação, otimiza e criptografa- e envia-os para o armazenamento do Azure através da porta 443 saída.
5. Os dados de replicação regista ' s land primeiro numa conta de armazenamento de cache no Azure. Estes registos são processados e os dados são armazenados no Azure (o disco de seed do Azure Site Recovery) de disco gerido. Os pontos de recuperação são criados neste disco.







## <a name="next-steps"></a>Passos Seguintes

- [Veja as perguntas mais frequentes](resources-faq.md) sobre o Azure Migrate.
- Para obter ajuda da Comunidade, visite o [migrar fórum MSDN do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureMigrate&filter=alltypes&sort=lastpostdesc) ou [Stack Overflow](https://stackoverflow.com/search?q=azure+migrate).

