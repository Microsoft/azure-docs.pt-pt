---
title: Aplicação de replicação do Azure Migrate
description: Saiba mais sobre o aparelho de replicação Azure Migrate para migração VMWare baseada em agentes.
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: conceptual
ms.date: 01/30/2020
ms.openlocfilehash: ec277bcc3e361561f54e72c54526d65487c113b4
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/07/2020
ms.locfileid: "96754101"
---
# <a name="replication-appliance"></a>Aparelho de replicação

Este artigo descreve o aparelho de replicação utilizado por [Azure Migrate: Ferramenta de migração de servidores](migrate-services-overview.md#azure-migrate-server-migration-tool) ao migrar VMware VMs, máquinas físicas e VMs de nuvem privada/pública para Azure, utilizando migração baseada em agentes. 


## <a name="overview"></a>Descrição geral

O aparelho de replicação é implantado quando configura a migração baseada em agentes de VMware VMs ou servidores físicos. É implantado como uma única máquina no local, seja como um VMware VM ou um servidor físico. Corre:

- **Aparelho de replicação**: O aparelho de replicação coordena as comunicações e gere a replicação de dados, para VMware VMware no local e servidores físicos replicando-se em Azure.
- **Servidor de processo**: O servidor de processo, que é instalado por predefinição no aparelho de replicação, e faz o seguinte:
    - **Gateway de replicação**: Funciona como um portal de replicação. Recebe dados de replicação de máquinas ativadas para replicação. Otimiza dados de replicação com caching, compressão e encriptação, e envia-os para Azure.
    - **Instalador de** agente : Executa uma instalação de push do Serviço de Mobilidade. Este serviço deve ser instalado e em funcionamento em cada máquina no local que pretende replicar para migração.

## <a name="appliance-deployment"></a>Implantação do aparelho

**Utilizado para** | **Detalhes**
--- |  ---
**Migração baseada em agentes VMware VM** | Você descarrega o modelo OVA a partir do hub Azure Migrate, e importa para vCenter Server para criar o VM do aparelho.
**Migração baseada em agentes físicos** | Se não tiver uma infraestrutura VMware, ou se não conseguir criar um VMware VM utilizando um modelo OVA, descarregue um instalador de software a partir do hub Azure Migrate e execute-o para configurar a máquina do aparelho.

> [!NOTE]
> Se estiver a ser implantado no Governo Azure, utilize o ficheiro de instalação para implantar o aparelho de replicação.

## <a name="appliance-requirements"></a>Requisitos do aparelho

Quando configura o aparelho de replicação utilizando o modelo OVA fornecido no hub Azure Migrate, o aparelho executa o Windows Server 2016 e cumpre os requisitos de suporte. Se configurar o aparelho de replicação manualmente num servidor físico, certifique-se de que está em conformidade com os requisitos.

**Componente** | **Requisito**
--- | ---
 | **Aparelho VMware VM**
PowerCLI | [A versão 6.0 do PowerCLI](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) deve ser instalada se o aparelho de replicação estiver a funcionar num VMware VMware VM.
Tipo NIC | VMXNET3 (se o aparelho for um VMware VM)
 | **Definições de hardware**
Núcleos de CPU | 8
RAM | 16 GB
Número de discos | Três: O disco DE, o disco de cache do servidor de processo e a unidade de retenção.
Espaço livre em disco (cache) | 600 GB
Espaço livre em disco (disco de retenção) | 600 GB
**Definições de software** |
Sistema operativo | Windows Server 2016 ou Windows Server 2012 R2
Licença | O aparelho vem com uma licença de avaliação do Windows Server 2016, que é válida por 180 dias.<br/><br/> Se o período de avaliação estiver próximo de expirar, recomendamos que descarregue e implemente um novo aparelho, ou que ative a licença do sistema operativo do aparelho VM.
Região do sistema operativo | Inglês (en-us)
TLS | TLS 1.2 deve ser ativado.
.NET Framework | .NET Quadro 4.6 ou posteriormente deve ser instalado na máquina (com criptografia forte ativada.
MySQL | O MySQL deve ser instalado no aparelho.<br/> O MySQL deve ser instalado. Pode instalar manualmente ou a Recuperação do Local pode instalá-la durante a colocação do aparelho.
Outras aplicações | Não execute outras aplicações no aparelho de replicação.
Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V
Políticas de grupo | Não ative estas políticas de grupo: <br> - Impedir o acesso à ordem de comando. <br> - Impedir o acesso a ferramentas de edição de registo. <br> - Lógica de confiança para anexos de ficheiros. <br> - Ligue a execução do guião. <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))
IIS | - Nenhum website pre-existente <br> - Nenhum website/aplicação pré-existente na porta 443 <br>- Ativar a  [autenticação anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Ativar a definição [fastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))
**Definições de rede** |
Tipo de endereço IP | Estático
Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)
Tipo NIC | VMXNET3

## <a name="mysql-installation"></a>Instalação MySQL 

O MySQL deve ser instalado na máquina do aparelho de replicação. Pode ser instalado com um destes métodos.

**Método** | **Detalhes**
--- | ---
Faça o download e instale manualmente | Descarregue a aplicação MySQL & colocá-la na pasta C:\Temp\ASRSetup e, em seguida, instale manualmente.<br/> Quando configurar o aparelho, o MySQL apresentará como já instalado.
Sem download online | Coloque a aplicação do instalador MySQL na pasta C:\Temp\ASRSetup. Quando instalar o aparelho e clicar para descarregar e instalar o MySQL, a configuração utilizará o instalador que adicionou.
Faça o download e instale em Azure Migrate | Quando instalar o aparelho e for solicitado para o MySQL, selecione **Descarregue e instale**.

## <a name="url-access"></a>Acesso a URL

O aparelho de replicação precisa de acesso a estes URLs na nuvem pública de Azure.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.com | Utilizado para transferência e coordenação de dados replicados
\*.store.core.windows.net | Utilizado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para aceder à conta de armazenamento que armazena dados replicados
\*.hypervrecoverymanager.windowsazure.com | Utilizado para operações de gestão e coordenação de replicação
https:\//management.azure.com | Utilizado para operações de gestão e coordenação de replicação
*.services.visualstudio.com | Utilizado para fins de telemetria (é opcional)
time.windows.com | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | A configuração do aparelho precisa de acesso a estes URLs. São utilizados para controlo de acessos e gestão de identidade pela Azure Ative Directory
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL. Em algumas regiões, o download pode ser redirecionado para o URL CDN. Certifique-se de que o URL CDN também é permitido se necessário.


## <a name="azure-government-url-access"></a>Acesso à URL do Governo de Azure

O aparelho de replicação precisa de acesso a estes URLs no Governo de Azure.

**URL** | **Detalhes**
--- | ---
\*.backup.windowsazure.us | Utilizado para transferência e coordenação de dados replicados
\*.store.core.windows.net | Utilizado para transferência e coordenação de dados replicados
\*.blob.core.windows.net | Usado para aceder à conta de armazenamento que armazena dados replicados
\*.hypervrecoverymanager.windowsazure.us | Utilizado para operações de gestão e coordenação de replicação
https:\//management.usgovcloudapi.net | Utilizado para operações de gestão e coordenação de replicação
*.services.visualstudio.com | Utilizado para fins de telemetria (é opcional)
time.nist.gov | Utilizados para verificar a sincronização da hora entre o sistema e a hora global.
https:\//login.microsoftonline.com <br/> https:\//secure.aadcdn.microsoftonline-p.com <br/> https: \/ /login.live.com <br/> https: \/ /graph.windows.net <br/> https:\//login.windows.net <br/> https: \/ /www.live.com <br/> https: \/ /www.microsoft.com  | A instalação do aparelho com OVA necessita de acesso a estes URLs. São utilizados para controlo de acesso e gestão de identidade pela Azure Ative Directory.
https: \/ /dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi | Para concluir o download do MySQL. Em algumas regiões, o download pode ser redirecionado para o URL CDN. Certifique-se de que o URL CDN também é permitido se necessário.

## <a name="port-access"></a>Acesso portuário

**Dispositivo** | **Ligação**
--- | ---
VMs | O serviço de Mobilidade em execução em VMs comunica com o aparelho de replicação no local (servidor de configuração) na entrada HTTPS 443, para gestão de replicação.<br/><br/> Os VMs enviam dados de replicação para o servidor de processo (em execução na máquina do servidor de configuração) na entrada HTTPS 9443. Esta porta pode ser modificada.
Aparelho de replicação | O aparelho de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
Servidor de processo | O servidor de processo recebe dados de replicação, otimiza e encripta-os e envia-os para o armazenamento Azure sobre a porta 443 de saída.<br/> Por predefinição, o servidor de processo funciona no aparelho de replicação.


## <a name="replication-process"></a>Processo de replicação

1. Quando ativa a replicação de um VM, começa a replicação inicial do armazenamento do Azure, utilizando a política de replicação especificada. 
2. O tráfego replica-se para os pontos finais públicos de armazenamento Azure através da internet. A replicação do tráfego sobre uma rede privada virtual local (VPN) de um site no local para Azure não é suportado.
3. Após o final da replicação inicial, começa a replicação delta. As alterações rastreadas para uma máquina estão registadas.
4. A comunicação acontece da seguinte forma:
    - Os VMs comunicam com o aparelho de replicação na porta HTTPS 443 entrada, para gestão de replicação.
    - O aparelho de replicação orquestra a replicação com o Azure sobre a porta HTTPS 443.
    - Os VMs enviam dados de replicação para o servidor de processo (em execução no aparelho de replicação) na entrada HTTPS 9443. Esta porta pode ser modificada.
    - O servidor de processo recebe dados de replicação, otimiza e encripta-os e envia-os para o armazenamento Azure sobre a porta 443 de saída.
5. Os registos de dados de replicação aterram pela primeira vez numa conta de armazenamento de cache em Azure. Estes registos são processados e os dados são armazenados num disco gerido aZure.

![O diagrama mostra a arquitetura do processo de replicação.](./media/migrate-replication-appliance/architecture.png)

## <a name="appliance-upgrades"></a>Atualizações de aparelhos

O aparelho é atualizado manualmente a partir do hub Azure Migrate. Recomendamos que execute sempre a versão mais recente.

1. No Azure Migrate > Servidores > Azure Migrate: Avaliação do servidor, servidores de infraestruturas, clique em **servidores de configuração**.
2. Nos **servidores de Configuração,** aparece uma ligação na **Versão Agente** quando uma nova versão do aparelho de replicação está disponível. 
3. Descarregue o instalador para a máquina do aparelho de replicação e instale a atualização. O instalador deteta a corrente da versão em funcionamento no aparelho.
 
## <a name="next-steps"></a>Passos seguintes

- [Saiba como](tutorial-migrate-vmware-agent.md#set-up-the-replication-appliance) configurar o aparelho de replicação para a migração VMware VMware baseada em agentes.
- [Saiba como](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) configurar o aparelho de replicação para servidores físicos.
