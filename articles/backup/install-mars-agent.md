---
title: Instale o agente microsoft Azure Recovery Services (MARS)
description: Saiba como instalar o agente do Microsoft Azure Recovery Services (MARS) para fazer o back-up das máquinas Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: cf6b332c308bb0224dbfed546f8b3ba819abcb02
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673107"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instale o agente MARS de backup Azure

Este artigo explica como instalar o agente do Microsoft Azure Recovery Services (MARS). Mars também é conhecido como o agente Azure Backup.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O Azure Backup utiliza o agente MARS para fazer backup de ficheiros, pastas e estado de sistema a partir de máquinas no local e VMs Azure. Os reforços estão guardados num cofre dos Serviços de Recuperação em Azure. Pode dirigir o agente:

* Diretamente no local, as máquinas Windows. Estas máquinas podem voltar diretamente para um cofre dos Serviços de Recuperação em Azure.
* Nos VMs Azure que executam o Windows lado a lado com a extensão de backup Azure VM. O agente recoloca ficheiros e pastas específicos no VM.
* Numa instância do Microsoft Azure Backup Server (MABS) ou num servidor do System Center Data Protection Manager (DPM). Neste cenário, as máquinas e cargas de trabalho voltam a ser MABS ou Gestor de Proteção de Dados. Em seguida, MABS ou Data Protection Manager usa o agente MARS para voltar a um cofre em Azure.

Os dados disponíveis para cópia de segurança dependem de onde o agente está instalado.

> [!NOTE]
> Geralmente, você faz backup de um VM Azure usando uma extensão de backup Azure no VM. Este método apoia todo o VM. Se pretender fazer o back-up de ficheiros e pastas específicos no VM, instale e utilize o agente MARS ao lado da extensão. Para mais informações, consulte [Arquitetura de um backup Azure VM incorporado.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Etapas do processo de backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar

* Saiba como o [Azure Backup utiliza o agente MARS para fazer backup](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)das máquinas windows .
* Conheça a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente MARS num servidor secundário de MABS ou Data Protection Manager.
* Reveja [o que é suportado e o que pode apoiar](backup-support-matrix-mars-agent.md) pelo agente mars.
* Certifique-se de que tem uma conta Azure se precisar de fazer o apoio a um servidor ou cliente ao Azure. Se não tiver uma conta, pode criar uma [gratuita](https://azure.microsoft.com/free/) em poucos minutos.
* Verifique o acesso à Internet nas máquinas que pretende fazer apoio.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação do armazenamento

Por padrão, os cofres utilizam [armazenamento geo-redundante (GRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

* Se o cofre for o seu principal mecanismo de reserva, recomendamos que utilize GRS.
* Pode utilizar [armazenamento redundante localmente (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) para reduzir os custos de armazenamento do Azure.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** sob a secção **Definições.**

1. Na página **Propriedades,** sob **configuração de backup,** selecione **Atualização**.

1. Selecione o tipo de replicação de armazenamento e selecione **Guardar**.

    ![Configuração de backup de atualização](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não pode modificar o tipo de replicação de armazenamento depois de o cofre ser configurado e conter itens de reserva. Se queres fazer isto, tens de recriar o cofre.
>

### <a name="verify-internet-access"></a>Verificar o acesso à Internet

Se a sua máquina tiver acesso limitado à Internet, certifique-se de que as definições de firewall na máquina ou proxy permitem os seguintes URLs e endereços IP:

* URLs
  * `www\.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
* Endereços IP
  * 20.190.128.0/18
  * 40.126.0.0/18

### <a name="use-azure-expressroute"></a>Utilizar azure ExpressRoute

Pode fazer o backup dos seus dados através do Azure ExpressRoute utilizando o público (disponível para circuitos antigos) e o peering da Microsoft. O apoio ao que o peering privado não é apoiado.

Para utilizar o público, primeiro garantir o acesso aos seguintes domínios e endereços:

* `http://www.msftncsi.com/ncsi.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Para utilizar o peering da Microsoft, selecione os seguintes serviços, regiões e valores comunitários relevantes:

* Diretório Ativo Azure (12076:5060)
* Região de Azure, de acordo com a localização do seu cofre dos Serviços de Recuperação
* Armazenamento Azure, de acordo com a localização do seu cofre dos Serviços de Recuperação

Para mais informações, consulte os requisitos de [encaminhamento expressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-routing).

> [!NOTE]
> O público está predestinado a novos circuitos.

Todos os URLs e endereços IP anteriores utilizam o protocolo HTTPS na porta 443.

## <a name="download-the-mars-agent"></a>Descarregue o agente MARS

Descarregue o agente MARS para que possa instalá-lo nas máquinas que pretende fazer recuar.

Se já instalou o agente em nenhuma máquina, certifique-se de que está a executar a versão mais recente do agente. Encontre a versão mais recente no portal ou vá diretamente para o [download](https://aka.ms/azurebackup_agent).

1. No cofre, sob **O Início,** selecione **Backup**.

    ![Abra o objetivo de reserva](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Em que está a funcionar a sua carga **de** **trabalho?** Selecione esta opção mesmo que pretenda instalar o agente MARS num VM Azure.
1. Em que é que pretende fazer **o backup?** Também pode selecionar **system state**. Muitas outras opções estão disponíveis, mas estas opções só são suportadas se estiver a executar um servidor de backup secundário. Selecione **Preparar infraestrutura**.

    ![Configure ficheiros e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Para **preparar a infraestrutura**, sob o agente de Serviços de **Recuperação de Instalações,** descarregue o agente MARS.

    ![Preparar a infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. No menu de descarregamento, selecione **Guardar**. Por predefinição, o ficheiro *MARSagentinstaller.exe* é guardado na pasta Transferências.

1. Selecione **Já descarregue ou use o mais recente Agente de Serviços**de Recuperação e, em seguida, baixe as credenciais do cofre.

    ![Transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selecione **Guardar**. O ficheiro é descarregado para a pasta Downloads. Não pode abrir o ficheiro de credenciais do cofre.

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Faça o ficheiro *MARSagentinstaller.exe* nas máquinas que pretende fazer recuar.
1. No assistente de configuração do agente MARS, selecione **Definições de instalação**. Lá, escolha onde instalar o agente e escolha um local para a cache. Em seguida, selecione **Seguinte**.
   * A Azure Backup utiliza a cache para armazenar instantâneos de dados antes de enviá-los para o Azure.
   * A localização da cache deve ter espaço livre igual a pelo menos 5% do tamanho dos dados que irá fazer.

    ![Escolha as definições de instalação no Assistente de Configuração do Agente MARS](./media/backup-configure-vault/mars1.png)

1. Para **configuração proxy,** especifique como o agente que funciona na máquina do Windows irá ligar-se à internet. Em seguida, selecione **Seguinte**.

   * Se utilizar um proxy personalizado, especifique quaisquer definições e credenciais de procuração necessárias.
   * Lembre-se que o agente precisa de acesso a [URLs específicos](#before-you-start).

    ![Configurar o acesso à Internet no feiticeiro MARS](./media/backup-configure-vault/mars2.png)

1. Para **a instalação,** reveja os pré-requisitos e selecione **Instalar**.
1. Depois de instalado o agente, selecione **Proceder à inscrição**.
1. No **Register Server Wizard** > **Identificação do Cofre,** navegue e selecione o ficheiro de credenciais que descarregou. Em seguida, selecione **Seguinte**.

    ![Adicione credenciais de cofre utilizando o Assistente do Servidor de Registo](./media/backup-configure-vault/register1.png)

1. Na página de Definição de **Encriptação,** especifique uma palavra-passe que será usada para encriptar e desencriptar cópias de segurança para a máquina.

    * Guarde a frase de passe num local seguro. Precisa para restaurar uma reserva.
    * Se perder ou esquecer a frase de passe, a Microsoft não pode ajudá-lo a recuperar os dados de backup.

1. Selecione **Concluir**. O agente está instalado e a sua máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="next-steps"></a>Passos seguintes

Saiba como fazer backup das [máquinas windows utilizando o agente Azure Backup MARS](backup-windows-with-mars-agent.md)
