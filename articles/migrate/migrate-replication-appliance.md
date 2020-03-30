---
title: Aplicação de replicação do Azure Migrate
description: Saiba mais sobre o aparelho de replicação Azure Migrate para migração VMWare baseada em agente.
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: 4521fce6310b319d155a2f0c418cd934be7e2cb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245866"
---
# <a name="replication-appliance"></a>Aparelho de replicação

Este artigo descreve o aparelho de replicação utilizado pela [Ferramenta Migração](migrate-services-overview.md#azure-migrate-server-migration-tool) do Servidor ao migrar VMware VMs, máquinas físicas e VMs de nuvem privada/pública para Azure, utilizando a migração baseada em agentes. 


## <a name="overview"></a>Descrição geral

O aparelho de replicação é implantado quando configura a migração baseada em vMware ou servidores físicos. É implantado como uma única máquina no local, seja como VMware VM ou servidor físico. Funciona:

- **Aparelho de replicação**: O aparelho de replicação coordena as comunicações e gere a replicação de dados, para VMware VMware no local e servidores físicos replicando-se para o Azure.
- **Servidor de processos**: O servidor de processo, que é instalado por defeito no aparelho de replicação, e faz o seguinte:
    - **Gateway de replicação:** Funciona como um portal de replicação. Recebe dados de replicação de máquinas ativadas para replicação. Otimiza dados de replicação com cache, compressão e encriptação, e envia-os para o Azure.
    - **Instalador**de agentes : Realiza uma instalação push do Serviço de Mobilidade. Este serviço deve ser instalado e em funcionamento em cada máquina no local que pretende replicar para migração.

## <a name="appliance-deployment"></a>Implantação de aparelhos

**Utilizado para** | **Detalhes**
--- |  ---
Migração baseada em vmware vm | Você descarrega o modelo OVA do hub De migração Azure, e importa para vCenter Server para criar o vM do aparelho.
Migração baseada em máquinas físicas | Se não tiver uma infraestrutura VMware, ou se não conseguir criar um VMware VMM utilizando um modelo OVA, descarregue um instalador de software a partir do hub Da Migração Azure e execute-o para configurar a máquina do aparelho.

## <a name="appliance-requirements"></a>Requisitos do aparelho

Quando configura o aparelho de replicação utilizando o modelo OVA fornecido no hub De migração Azure, o aparelho executa o Windows Server 2016 e cumpre os requisitos de suporte. Se configurar manualmente o aparelho de replicação num servidor físico, certifique-se de que está em conformidade com os requisitos.

**Componente** | **Requisito**
--- | ---
 | **VMware VM**
PowerCLI | [A versão PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o aparelho de replicação estiver a funcionar num VMware VM.
Tipo NIC | VMXNET3 (se o aparelho for um VMware VM)
 | **Definições de hardware**
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | Três: O disco OS, o disco de cache do servidor de processo e a unidade de retenção.
Espaço de disco gratuito (cache) | 600 GB
Espaço de disco gratuito (disco de retenção) | 600 GB
**Definições de software** |
Sistema operativo | Windows Server 2016 ou Windows Server 2012 R2
Licença | O aparelho vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias.<br/><br/> Se o período de avaliação estiver perto de expirar, recomendamos que descarregue e implante um novo aparelho, ou que ative a licença do sistema operativo do VM do aparelho.
Região do sistema operativo | Inglês (en-us)
TLS | O TLS 1.2 deve ser ativado.
.NET Framework | .NET A estrutura 4.6 ou posterior deve ser instalada na máquina (com uma encriptação forte ativada.
MySQL | O MySQL deve ser instalado no aparelho.<br/> O MySQL deve ser instalado. Pode instalar manualmente ou a Recuperação do Local pode instalá-la durante a implantação do aparelho.
Outras aplicações | Não faça outras aplicações no aparelho de replicação.
Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V
Políticas de grupo | Não permita estas políticas de grupo: <br> - Impedir o acesso ao pedido de comando. <br> - Impedir o acesso às ferramentas de edição de registo. <br> - Lógica de confiança para anexos de ficheiros. <br> - Ligue a execução do guião. <br> [Mais informações](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)
IIS | - Nenhum website preexistente <br> - Nenhum site/aplicação pré-existente ouvindo na porta 443 <br>- Ativar [a autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Ativar a definição [de FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)
**Definições de rede** |
Tipo de endereço IP | Estático
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)
Tipo NIC | VMXNET3

## <a name="mysql-installation"></a>Instalação MySQL 

O MySQL deve ser instalado na máquina do aparelho de replicação. Pode ser instalado utilizando um destes métodos.

**Método** | **Detalhes**
--- | ---
Descarregue e instale manualmente | Descarregue a aplicação MySQL & colocá-la na pasta C:\Temp\ASRSetup e, em seguida, instale manualmente.<br/> Quando configurar o aparelho, o MySQL mostrará como já está instalado.
Sem download online | Coloque a aplicação do instalador MySQL na pasta C:\Temp\ASRSetup. Quando instalar o aparelho e clicar para descarregar e instalar o MySQL, a configuração utilizará o instalador que adicionou.
Descarregue e instale em Azure Migrate | Quando instalar o aparelho e for solicitado para o MySQL, **selecione Descarregar e instalar**.

## <a name="url-access"></a>Acesso url

O aparelho de replicação precisa de acesso a estes URLs.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Utilizado para transferência e coordenação de dados replicados
\*.store.core.windows.net | Utilizado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para aceder à conta de armazenamento que armazena dados replicados
\*.hypervrecoverymanager.windowsazure.com | Utilizado para operações de gestão de replicação e coordenação
https:\//management.azure.com | Utilizado para operações de gestão de replicação e coordenação
*.services.visualstudio.com | Usado para efeitos de telemetria (é opcional)
time.nist.gov | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
time.windows.com | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https:\//login.live.com <br/> https:\//graph.windows.net <br/> https:\//login.windows.net <br/> https:\//www.live.com <br/> https:\//www.microsoft.com  | A configuração ovf precisa de acesso a estes URLs. São utilizados para controlo de acesso e gestão de identidade pela Azure Ative Directory
https:\//dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para completar o download do MySQL

## <a name="port-access"></a>Acesso portuário

**Dispositivo** | **Conexão**
--- | ---
VMs | O serviço de Mobilidade em funcionamento em VMs comunica com o aparelho de replicação no local (servidor de configuração) na entrada da porta HTTPS 443, para gestão de replicação.<br/><br/> Os VMs enviam dados de replicação para o servidor de processo (funcionando na máquina do servidor de configuração) na entrada da porta HTTPS 9443. Esta porta pode ser modificada.
Aparelho de replicação | O aparelho de replicação orquestra a replicação com Azure sobre a saída da porta HTTPS 443.
Servidor de processos | O servidor de processos recebe dados de replicação, otimiza e encripta-os, e envia-os para o armazenamento Azure sobre a saída da porta 443.<br/> Por defeito, o servidor de processo funciona no aparelho de replicação.


## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de um VM, começa a replicação inicial ao armazenamento Azure, utilizando a política de replicação especificada. 
2. O tráfego replica-se para os pontos finais do armazenamento Azure através da internet. A replicação do tráfego através de uma rede privada virtual (VPN) de um site no local para Azure não é suportada.
3. Após os acabamentos iniciais da replicação, começa a replicação delta. As alterações rastreadas para uma máquina estão registadas.
4. A comunicação acontece da seguinte forma:
    - Os VMs comunicam com o aparelho de replicação na entrada da porta HTTPS 443, para gestão de replicação.
    - O aparelho de replicação orquestra a replicação com Azure sobre a saída da porta HTTPS 443.
    - Os VMs enviam dados de replicação para o servidor de processo (em execução no aparelho de replicação) na entrada da porta HTTPS 9443. Esta porta pode ser modificada.
    - O servidor de processos recebe dados de replicação, otimiza e encripta-os, e envia-os para o armazenamento Azure sobre a saída da porta 443.
5. Os dados de replicação registam a primeira terra numa conta de armazenamento de cache em Azure. Estes registos são processados e os dados são armazenados num disco gerido pelo Azure.

![Arquitetura](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado manualmente a partir do centro de migração Azure. Recomendamos que execute sempre a versão mais recente.

1. Em Azure Migrate > Servers > Azure Migrate: Server Assessment, Servidores de Infraestruturas, clique em servidores de **configuração**.
2. Nos **servidores de Configuração,** aparece um link na **Versão agente** quando uma nova versão do aparelho de replicação está disponível. 
3. Descarregue o instalador para a máquina de aparelhode replicação e instale a atualização. O instalador deteta a corrente da versão em funcionamento no aparelho.
 
## <a name="next-steps"></a>Passos seguintes

- [Aprenda](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) a configurar o aparelho de replicação para a migração VMware VM baseada no agente.
- [Aprenda](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) a configurar o aparelho de replicação para servidores físicos.
