---
title: Instale o agente Microsoft Azure Recovery Services (MARS)
description: Saiba como instalar o agente Microsoft Azure Recovery Services (MARS) para fazer o back up das máquinas Windows.
ms.topic: conceptual
ms.date: 03/03/2020
ms.openlocfilehash: fb59c245c469791233ce973b00426a127b116535
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90975306"
---
# <a name="install-the-azure-backup-mars-agent"></a>Instale o agente Azure Backup MARS

Este artigo explica como instalar o agente Microsoft Azure Recovery Services (MARS). Mars também é conhecido como o agente Azure Backup.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O Azure Backup utiliza o agente MARS para fazer cópias de segurança de ficheiros, pastas e estado do sistema a partir de máquinas no local e VMs Azure. Esses reforços estão guardados num cofre dos Serviços de Recuperação em Azure. Pode executar o agente:

* Diretamente nas máquinas Windows no local. Estas máquinas podem voltar diretamente para um cofre dos Serviços de Recuperação em Azure.
* Em VMs Azure que executam o Windows lado a lado com a extensão de backup Azure VM. O agente regista ficheiros e pastas específicos no VM.
* Numa instância do Microsoft Azure Backup Server (MABS) ou num servidor do System Center Data Protection Manager (DPM). Neste cenário, as máquinas e as cargas de trabalho voltam para o MABS ou gestor de proteção de dados. Em seguida, o MABS ou Gestor de Proteção de Dados utiliza o agente MARS para fazer o back até um cofre em Azure.

Os dados disponíveis para cópia de segurança dependem do local onde o agente está instalado.

> [!NOTE]
> Geralmente, você apoia um Azure VM usando uma extensão de Backup Azure no VM. Este método confirma todo o VM. Se pretender fazer o back up ficheiros e pastas específicos no VM, instale e utilize o agente MARS ao lado da extensão. Para mais informações, consulte [a Arquitetura de uma cópia de segurança Azure VM incorporada.](backup-architecture.md#architecture-built-in-azure-vm-backup)

![Etapas de processo de backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar

* Saiba como o [Azure Backup utiliza o agente MARS para fazer backup de máquinas windows](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
* Saiba mais sobre a [arquitetura de backup](backup-architecture.md#architecture-back-up-to-dpmmabs) que executa o agente MARS num servidor secundário de MABS ou Gestor de Proteção de Dados.
* [Reveja o que é apoiado e o que pode apoiar](backup-support-matrix-mars-agent.md) pelo agente MARS.
* Certifique-se de que tem uma conta Azure se precisar de fazer o back up de um servidor ou cliente para a Azure. Se não tiver uma conta, pode criar uma [gratuita](https://azure.microsoft.com/free/) em poucos minutos.
* Verifique o acesso à Internet nas máquinas que pretende fazer.
* Certifique-se de que o utilizador que executa a instalação e configuração do agente MARS tem privilégios de administrador local no servidor a proteger.

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="modify-storage-replication"></a>Modificar a replicação do armazenamento

Por predefinição, os cofres utilizam [armazenamento geo-redundante (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage).

* Se o cofre for o seu mecanismo de reserva primário, recomendamos que utilize GRS.
* Pode utilizar [o armazenamento localmente redundante (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) para reduzir os custos de armazenamento da Azure.

Para modificar o tipo de replicação de armazenamento:

1. No novo cofre, selecione **Propriedades** sob a secção **Definições.**

1. Na página **Propriedades,** em **Configuração de Cópia de Segurança,** selecione **Update**.

1. Selecione o tipo de replicação de armazenamento e **selecione Guardar**.

    ![Atualizar configuração de backup](./media/backup-afs/backup-configuration.png)

> [!NOTE]
> Não é possível modificar o tipo de replicação de armazenamento depois de o cofre ser configurado e contém itens de reserva. Se queres fazer isto, tens de recriar o cofre.
>

### <a name="verify-internet-access"></a>Verificar acesso à Internet

Se a sua máquina tiver acesso limitado à Internet, certifique-se de que as definições de firewall na máquina ou procuração permitem os seguintes endereços URLs e IP:

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

### <a name="use-azure-expressroute"></a>Use Azure ExpressRoute

Pode fazer o back up dos seus dados através do Azure ExpressRoute utilizando o espreitamento público (disponível para circuitos antigos) e o espreitamento da Microsoft. Backup sobre olhando privado não é apoiado.

Para utilizar o espreguiçadamento público, primeiro garantir o acesso aos seguintes domínios e endereços:

* `http://www.msftncsi.com/ncsi.txt`
* `http://www.msftconnecttest.com/connecttest.txt`
* `microsoft.com`
* `.WindowsAzure.com`
* `.microsoftonline.com`
* `.windows.net`

Para utilizar o espreguio da Microsoft, selecione os seguintes serviços, regiões e valores comunitários relevantes:

* Diretório Ativo Azure (12076:5060)
* Região de Azure, de acordo com a localização do cofre dos Serviços de Recuperação
* Azure Storage, de acordo com a localização do cofre dos Serviços de Recuperação

Para obter mais informações, consulte [os requisitos de encaminhamento ExpressRoute](../expressroute/expressroute-routing.md).

> [!NOTE]
> O olhar público é depreciado para novos circuitos.

Todos os urls e endereços IP anteriores utilizam o protocolo HTTPS na porta 443.

### <a name="private-endpoints"></a>Pontos Finais Privados

[!INCLUDE [Private Endpoints](../../includes/backup-private-endpoints.md)]

## <a name="download-the-mars-agent"></a>Descarregue o agente MARS

Descarregue o agente MARS para que possa instalá-lo nas máquinas que pretende fazer.

Se já instalou o agente em alguma máquina, certifique-se de que está a executar a versão mais recente do agente. Encontre a versão mais recente no portal ou vá diretamente para o [download](https://aka.ms/azurebackup_agent).

1. No cofre, em **"Getting Started",** selecione **Backup**.

    ![Abra o objetivo de reserva](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

1. Em Onde está a sua **On-premises**carga de trabalho **a funcionar?** Selecione esta opção mesmo que pretenda instalar o agente MARS num Azure VM.
1. Em Que é que pretende fazer **Files and folders** **o back-up?** Também pode selecionar **o Estado do Sistema.** Muitas outras opções estão disponíveis, mas estas opções só são suportadas se estiver a executar um servidor de backup secundário. Selecione **Preparar Infraestrutura**.

    ![Configurar ficheiros e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

1. Para **preparar a infraestrutura**, sob **o agente Install Recovery Services**, descarregue o agente MARS.

    ![Preparar a infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

1. No menu de descarregamento, **selecione Save**. Por predefinição, o ficheiro *MARSagentinstaller.exe* é guardado na pasta Transferências.

1. Selecione **Já faça o download ou utilize o mais recente Agente de Serviços de Recuperação**e, em seguida, baixe as credenciais do cofre.

    ![Transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

1. Selecione **Guardar**. O ficheiro é descarregado para a sua pasta Downloads. Não pode abrir o arquivo de credenciais do cofre.

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Faça o ficheiro *MARSagentinstaller.exe* nas máquinas que pretende fazer.
1. No assistente de configuração do agente MARS, selecione **Definições de instalação**. Lá, escolha onde instalar o agente e escolha um local para a cache. e selecione **Seguinte**.
   * A Azure Backup utiliza a cache para armazenar imagens de dados antes de enviá-las para a Azure.
   * A localização da cache deve ter espaço livre igual a pelo menos 5% do tamanho dos dados que irá apoiar.

    ![Escolha as definições de instalação no Assistente de Configuração do Agente MARS](./media/backup-configure-vault/mars1.png)

1. Para **configuração proxy**, especifique como o agente que executa na máquina do Windows irá ligar-se à internet. e selecione **Seguinte**.

   * Se utilizar um representante personalizado, especifique as definições e credenciais necessárias.
   * Lembre-se que o agente precisa de acesso a [URLs específicos.](#before-you-start)

    ![Configurar o acesso à Internet no assistente MARS](./media/backup-configure-vault/mars2.png)

1. Para **instalação,** reveja os pré-requisitos e **selecione Instalar**.
1. Depois de instalado o agente, **selecione Proceder ao Registo**.
1. No Registo de Identificação do **Cofre do Assistente do Servidor,**  >  **Vault Identification**navegue e selecione o ficheiro de credenciais que descarregou. e selecione **Seguinte**.

    ![Adicione credenciais de abóbada utilizando o Assistente do Servidor de Registo](./media/backup-configure-vault/register1.png)

1. Na página Definição de **Encriptação,** especifique uma palavra-passe que será usada para encriptar e desencriptar cópias de segurança para a máquina. [Consulte aqui](backup-azure-file-folder-backup-faq.md#what-characters-are-allowed-for-the-passphrase) mais informações sobre caracteres de frases-passe permitidas.

    * Guarde a frase num local seguro. Precisa dele para restaurar uma reserva.
    * Se perder ou esquecer a palavra-passe, a Microsoft não pode ajudá-lo a recuperar os dados de backup.

1. Selecione **Concluir**. O agente está agora instalado, e a sua máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="next-steps"></a>Passos seguintes

Saiba como [fazer backup das máquinas do Windows utilizando o agente Azure Backup MARS](backup-windows-with-mars-agent.md)
