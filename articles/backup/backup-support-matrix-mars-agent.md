---
title: Matriz de suporte para o agente MARS
description: Este artigo resume o suporte de backup do Azure quando faz cópias de segurança que estão a executar o agente Microsoft Azure Recovery Services (MARS).
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 20bca0e9ca9dfd735501e68bd0e5a6d69d2ef68e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576504"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Matriz de suporte para cópia de segurança com o agente Microsoft Azure Recovery Services (MARS)

Pode utilizar o [serviço de backup Azure](backup-overview.md) para fazer backup em máquinas e aplicações no local e para fazer backup de máquinas virtuais Azure (VMs). Este artigo resume as definições e limitações de suporte quando utiliza o agente Microsoft Azure Recovery Services (MARS) para fazer o back up das máquinas.

## <a name="the-mars-agent"></a>O agente MARS

O Azure Backup utiliza o agente MARS para fazer cópias de segurança de dados de máquinas no local e VMs Azure para um cofre de serviços de recuperação de reserva em Azure. O agente MARS pode:

- Executar no local máquinas Windows para que possam voltar diretamente para um cofre de serviços de recuperação de backup em Azure.
- Corra em VMs Windows para que possam voltar diretamente para um cofre.
- Execute no Microsoft Azure Backup Server (MABS) ou num servidor Do System Center Data Protection Manager (DPM). Neste cenário, as máquinas e cargas de trabalho voltam para o MABS ou para o servidor DPM. O agente MARS, em seguida, apoia este servidor para um cofre em Azure.

> [!NOTE]
>A Azure Backup não suporta o ajuste automático do relógio para o horário de verão (DST). Modificar a política para garantir que as poupanças da luz do dia são tidas em conta para evitar discrepâncias entre o tempo real e o tempo de reserva programado.

As suas opções de backup dependem do local onde o agente está instalado. Para obter mais informações, consulte [a arquitetura Azure Backup utilizando o agente MARS](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Para obter informações sobre a arquitetura de backup MABS e DPM, consulte [Back up to DPM ou MABS](backup-architecture.md#architecture-back-up-to-dpmmabs). Consulte também [os requisitos](backup-support-matrix-mabs-dpm.md) para a arquitetura de reserva.

**Instalação** | **Detalhes**
--- | ---
Descarregue o mais recente agente MARS | Você pode baixar a versão mais recente do agente a partir do cofre, ou [descarregá-la diretamente](https://aka.ms/azurebackup_agent).
Instale diretamente numa máquina | Pode instalar o agente MARS diretamente num servidor Windows no local ou num VM do Windows que esteja a executar qualquer um dos [sistemas operativos suportados](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).
Instalar num servidor de backup | Quando configurar o DPM ou o MABS para fazer o back up até ao Azure, descarregue e instale o agente MARS no servidor. Pode instalar o agente em [sistemas operativos suportados](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) na matriz de suporte do servidor de backup.

> [!NOTE]
> Por predefinição, os VMs Azure que estão habilitados para backup têm uma instalação de extensão Azure Backup. Esta extensão confirma todo o VM. Pode instalar e executar o agente MARS num VM Azure ao lado da extensão se pretender fazer o back up de pastas e ficheiros específicos, em vez do VM completo.
> Quando executou o agente MARS num VM Azure, ele faz o back-up de ficheiros ou pastas que estão em armazenamento temporário no VM. As cópias de segurança falham se os ficheiros ou pastas forem removidos do armazenamento temporário ou se o armazenamento temporário for removido.

## <a name="cache-folder-support"></a>Suporte de pasta cache

Quando utiliza o agente MARS para fazer o armazenamento de dados, o agente tira uma imagem dos dados e armazena-os numa pasta de cache local antes de enviar os dados para o Azure. A pasta cache (scratch) tem vários requisitos:

**Cache** | **Detalhes**
--- | ---
Tamanho |  O espaço livre na pasta cache deve ser de pelo menos 5 a 10% do tamanho total dos seus dados de backup.
Localização | A pasta cache deve ser armazenada localmente na máquina que está a ser apoiada, e deve estar online. A pasta cache não deve estar numa partilha de rede, em suportes amovíveis ou num volume offline.
Pasta | A pasta de cache não deve ser encriptada num volume deduplicado ou numa pasta que seja comprimida, que seja escassa, ou que tenha um ponto de reparse.
Alterações de localização | Pode alterar a localização da cache, parando o motor de reserva `net stop bengine` () e copiando a pasta de cache para uma nova unidade. (Certifique-se de que a nova unidade tem espaço suficiente.) Em seguida, atualize duas entradas de registo em **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** **(Config/ScratchLocation** e **Config/CloudBackUpProvider/ScratchLocation**) para a nova localização e reinicie o motor.

## <a name="networking-and-access-support"></a>Suporte em rede e acesso

### <a name="url-and-ip-access"></a>Acesso a URL e IP

O agente MARS precisa de acesso a estes URLs:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

E a estes endereços IP:

- 20.190.128.0/18
- 40.126.0.0/18

O acesso a todos os endereços URLs e IP listados acima utiliza o protocolo HTTPS na porta 443.

Ao fazer o backup de ficheiros e pastas a partir de VMs Azure usando o Agente MARS, a rede virtual Azure também precisa de ser configurada para permitir o acesso. Se utilizar grupos de segurança de rede (NSG), utilize a etiqueta de serviço *AzureBackup* para permitir o acesso de saída ao Azure Backup. Além da etiqueta Azure Backup, também precisa de permitir a conectividade para a autenticação e transferência de dados, criando [regras de NSG semelhantes](../virtual-network/network-security-groups-overview.md#service-tags) para Azure *AD (AzureActiveDirectory)* e Azure Storage *(Armazenamento).* Os seguintes passos descrevem o processo para criar uma regra para a etiqueta de backup Azure:

1. Em **Todos os Serviços,** vá aos **grupos de segurança da Rede** e selecione o grupo de segurança da rede.
2. Selecione **regras de segurança de saída** em **Definições**.
3. Selecione **Adicionar**. Introduza todos os detalhes necessários para a criação de uma nova regra, conforme descrito nas [definições de regras de segurança](../virtual-network/manage-network-security-group.md#security-rule-settings). Certifique-se de que a opção **Destino** está definida para tag de serviço *de serviço* e de **destino** está definida para *AzureBackup*.
4. **Selecione Adicionar** para salvar a regra de segurança de saída recém-criada.

Pode igualmente criar regras de segurança de saída NSG para Azure Storage e Azure AD. Para obter mais informações sobre etiquetas de serviço, consulte [este artigo.](../virtual-network/service-tags-overview.md)

### <a name="azure-expressroute-support"></a>Suporte Azure ExpressRoute

Pode fazer o back up dos seus dados através do Azure ExpressRoute com o espreitamento público (disponível para circuitos antigos) e o espreitamento da Microsoft. Backup sobre olhando privado não é apoiado.

Com o acompanhamento público: Garantir o acesso aos seguintes domínios/endereços:

* URLs
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Com o estomamento da Microsoft, selecione os seguintes serviços/regiões e valores comunitários relevantes:

- Azure Backup (de acordo com a localização do cofre dos Serviços de Recuperação)
- Diretório Ativo Azure (12076:5060)
- Azure Storage (de acordo com a localização do cofre dos Serviços de Recuperação)

Para obter mais informações, consulte os [requisitos de encaminhamento ExpressRoute](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>O "Peering Público" é precotado para novos circuitos.

### <a name="private-endpoint-support"></a>Suporte private Endpoint

Agora pode utilizar o Private Endpoints para fazer o back up de segurança dos seus dados dos servidores até ao cofre dos Serviços de Recuperação. Uma vez que o Azure Ative Directory não suporta atualmente pontos finais privados, os IPs e FQDNs necessários para o Azure Ative Directory terão de ser autorizados a aceder separadamente.

Quando utilizar o Agente MARS para fazer o apoio aos seus recursos no local, certifique-se de que a sua rede no local (que contém os seus recursos a serem apoiados) é espremiada com o Azure VNet que contém um ponto final privado para o cofre. Em seguida, pode continuar a instalar o agente MARS e configurar a cópia de segurança. No entanto, deve garantir que todas as comunicações de backup acontecem apenas através da rede de esprevação.

Se remover os pontos finais privados do cofre depois de um agente da MARS ter sido registado, terá de voltar a registar o contentor com o cofre. Não precisas de parar de proteção para eles.

Leia mais sobre [os pontos finais privados para a Azure Backup](private-endpoints.md).

### <a name="throttling-support"></a>Apoio de estrangulamento

**Funcionalidade** | **Detalhes**
--- | ---
Controlo de largura de banda | Suportado. No agente MARS, utilize **change Properties** para ajustar a largura de banda.
Limitação da rede | Não disponível para máquinas com apoio que executam o Windows Server 2008 R2, Windows Server 2008 SP2 ou Windows 7.

## <a name="supported-operating-systems"></a>Sistemas operativos suportados

>[!NOTE]
> O agente MARS não suporta SKUs do Núcleo do Servidor do Windows.

Pode utilizar o agente MARS para fazer o back-up diretamente para o Azure nos sistemas operativos listados abaixo que funcionam:

1. Servidores Windows no local
2. VMs Azure executando Janelas

Os sistemas operativos devem ser de 64 bits e devem estar a executar os mais recentes pacotes de serviços e atualizações. O quadro que se segue resume estes sistemas operativos:

**Sistema operativo** | **Ficheiros/pastas** | **Estado do sistema** | **Requisitos de software/módulo**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Yes | No |  Verifique a versão do servidor correspondente para obter os requisitos de software/módulo
Windows 8.1 (Enterprise, Pro)| Yes |No | Verifique a versão do servidor correspondente para obter os requisitos de software/módulo
Windows 8 (Enterprise, Pro) | Yes | No | Verifique a versão do servidor correspondente para obter os requisitos de software/módulo
Windows Server 2016 (Standard, Datacenter, Essentials) | Yes | Yes | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributável <br> - Consola de Gestão da Microsoft (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Yes | Yes | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributável <br> - Consola de Gestão da Microsoft (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Yes | Yes |- .NET 4.5 <br> -Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributável <br> - Consola de Gestão da Microsoft (MMC) 3.0 <br> - Serviço e Gestão de Imagem de Implantação (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Yes | No | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributável <br> - Consola de Gestão da Microsoft (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Yes | Yes | - .NET 4.5 <br> - Windows PowerShell <br> - Mais recente compatível Microsoft VC++ Redistributável <br> - Consola de Gestão da Microsoft (MMC) 3.0

Para obter mais informações, consulte [os sistemas operativos MABS e DPM suportados.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Sistemas operativos no final do suporte

Os seguintes sistemas operativos estão no final do suporte e é fortemente recomendado atualizar o sistema operativo para continuar protegido.

Se os compromissos existentes impedirem a atualização do sistema operativo, considere migrar os servidores windows para Azure VMs e aproveitar as cópias de segurança Azure VM para continuar a manter-se protegida. Visite [a página de migração aqui](https://azure.microsoft.com/migration/windows-server/) para obter mais informações sobre a migração do seu servidor Windows.

Para ambientes no local ou ambientes hospedados, onde não é possível atualizar o sistema operativo ou migrar para Azure, ative atualizações de segurança estendidas para que as máquinas continuem protegidas e suportadas. Note que apenas edições específicas são elegíveis para Atualizações de Segurança Alargadas. Visite a [página de PERGUNTAS Frequentes](https://www.microsoft.com/windows-server/extended-security-updates) para saber mais.

| **Sistema Operativo**                                       | **Ficheiros/pastas** | **Estado do sistema** | **Requisitos de software/módulo**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Yes               | No                 | Verifique a versão do servidor correspondente para obter os requisitos de software/módulo |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Yes               | Yes                | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatível Microsoft VC++ Redistributável <br>  - Consola de Gestão da Microsoft (MMC) 3.0 <br>  - Serviço e Gestão de Imagem de Implantação (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Yes               | No                 | - .NET 3.5, .NET 4.5 <br>  - Windows PowerShell <br>  - Compatível Microsoft VC++ Redistributável <br>  - Consola de Gestão da Microsoft (MMC) 3.0 <br>  - Serviço e Gestão de Imagem de Implantação (DISM.exe) <br>  - Base virtual do Servidor 2005 + KB KB948515 |

## <a name="backup-limits"></a>Limites de backup

### <a name="size-limits"></a>Limites de tamanho

O Azure Backup limita o tamanho de uma fonte de dados de ficheiro ou pasta que pode ser apoiada. Os itens que retrossondo de um único volume não podem exceder os tamanhos resumidos nesta tabela:

**Sistema operativo** | **Limite de tamanho**
--- | ---
Windows Server 2012 ou posterior |54 400 GB
Windows Server 2008 R2 SP1 |1.700 GB
Windows Server 2008 SP2| 1.700 GB
Windows 8 ou posterior| 54 400 GB
Windows 7| 1.700 GB

### <a name="minimum-retention-limits"></a>Limites mínimos de retenção

Seguem-se as durações mínimas de retenção que podem ser definidas para os diferentes pontos de recuperação:

|Ponto de recuperação |Duração  |
|---------|---------|
|Ponto de recuperação diário    |   7 dias      |
|Ponto de recuperação semanal     |    4 semanas     |
|Ponto de recuperação mensal    |   3 meses      |
|Ponto de recuperação anual  |      1 ano   |

### <a name="other-limitations"></a>Outras limitações

- A MARS não suporta a proteção de várias máquinas com o mesmo nome para um único cofre.

## <a name="supported-file-types-for-backup"></a>Tipos de ficheiros suportados para cópia de segurança

**Tipo** | **Suporte**
--- | ---
Encriptado<sup>*</sup>| Suportado.
Comprimidos | Suportado.
Disperso | Suportado.
Comprimido e escasso |Suportado.
Ligações fixas| Não suportado. Saltou.
Ponto de reanálise| Não suportado. Saltou.
Encriptado e escasso |Não suportado. Saltou.
Fluxo comprimido| Não suportado. Saltou.
Fluxo escasso| Não suportado. Saltou.
OneDrive (ficheiros sincronizados são fluxos escassos)| Não suportado.
Pastas com replicação DFS ativadas | Não suportado.

\* Certifique-se de que o agente MARS tem acesso aos certificados necessários para aceder aos ficheiros encriptados. Ficheiros inacessíveis serão ignorados.

## <a name="supported-drives-or-volumes-for-backup"></a>Unidades ou volumes suportados para backup

**Unidade/volume** | **Suporte** | **Detalhes**
--- | --- | ---
Volumes apenas de leitura| Não suportado | Volume Copy Shadow Service (VSS) só funciona se o volume for possível.
Volumes offline| Não suportado |O VSS só funciona se o volume estiver online.
Partilha de rede| Não suportado |O volume deve ser local no servidor.
Volumes bloqueados por BitLocker| Não suportado |O volume deve ser desbloqueado antes do início da cópia de segurança.
Identificação do sistema de ficheiros| Não suportado |Apenas o NTFS é apoiado.
Meios de comunicação amovíveis| Não suportado |Todas as fontes de produto de reserva devem ter um estado *fixo.*
Unidades deduplicadas | Suportado | A Azure Backup converte dados desduplicados para dados normais. Otimiza, encripta, armazena e envia os dados para o cofre.

## <a name="support-for-initial-offline-backup"></a>Suporte para backup inicial offline

O Azure Backup suporta *a sementeira offline* para transferir dados de backup iniciais para o Azure utilizando discos. Este suporte é útil se a sua cópia de segurança inicial for provável que esteja na gama de tamanho de terabytes (TBs). A cópia de segurança offline é suportada para:

- Cópia de segurança direta de ficheiros e pastas em máquinas no local que estão a executar o agente MARS.
- Cópia de segurança de cargas de trabalho e ficheiros de um servidor DPM ou MABS.

A cópia de segurança offline não pode ser usada para ficheiros do estado do sistema.

## <a name="support-for-data-restoration"></a>Suporte para a restauração de dados

Ao utilizar a funcionalidade [de Restauro Instantâneo](backup-instant-restore-capability.md) da Cópia de Segurança Azure, pode restaurar os dados antes de serem copiados para o cofre. A máquina que está a apoiar deve estar a funcionar .NET Framework 4.5.2 ou superior.

As cópias de segurança não podem ser restauradas numa máquina-alvo que está a executar uma versão anterior do sistema operativo. Por exemplo, uma cópia de segurança tirada de um computador que está a executar o Windows 7 pode ser restaurada no Windows 8 ou posteriormente. Mas uma cópia de segurança tirada de um computador que está a executar o Windows 8 não pode ser restaurada num computador que está a executar o Windows 7.

## <a name="previous-mars-agent-versions"></a>Versões anteriores do agente MARS

A tabela que se segue lista as versões anteriores do agente com os seus links de descarregamento. Recomendamos que atualize a versão do agente para o mais tardar, para que possa aproveitar as funcionalidades mais recentes e o melhor desempenho.

**Versions** (Versões) | **Artigos KB**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | Não disponível
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | Não disponível
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | Não disponível
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | Não disponível
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | Não disponível
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | Não disponível
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | Não disponível
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>As versões de agente MARS com pequena fiabilidade e melhorias de desempenho não têm um artigo KB.

## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre [a arquitetura de backup que usa o agente MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Saiba o que é suportado quando [executar o agente MARS no MABS ou num servidor DPM](backup-support-matrix-mabs-dpm.md).
