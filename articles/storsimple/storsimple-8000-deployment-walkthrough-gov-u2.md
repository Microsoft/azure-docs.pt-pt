---
title: Implantar o dispositivo StorSimple 8000 Series no portal governamental | Microsoft Docs
description: Descreve as etapas e as práticas recomendadas para implantar o dispositivo StorSimple da série 8000 executando a atualização 3 e posterior e o serviço no portal do Azure governamental.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2017
ms.author: alkohli
ms.openlocfilehash: 22084f9c59070c2efaa112ebfbb0c5ecc647145e
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68965888"
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Implantar seu dispositivo StorSimple local no portal do governo

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição geral
Bem-vindo à implementação do dispositivo Microsoft Azure StorSimple. Esses tutoriais de implantação se aplicam à série StorSimple 8000 que executa o software da atualização 3 ou posterior no portal do Azure governamental. Esta série de tutoriais inclui uma lista de verificação de configuração, uma listagem de pré-requisitos de configuração e etapas de configuração detalhadas para seu dispositivo StorSimple.

As informações nestes tutoriais pressupõem que leu as precauções de segurança e descompactou, montou em bastidor e instalou os cabos do dispositivo StorSimple. Se ainda precisar de efetuar essas tarefas, comece por ler as [precauções de segurança](storsimple-safety.md). Siga as instruções específicas do dispositivo para descompactar, montar em bastidor e instalar os cabos do dispositivo.

* [Descompactar, montar em bastidor e instalar os cabos do 8100](storsimple-8100-hardware-installation.md)
* [Descompactar, montar em bastidor e instalar os cabos do 8600](storsimple-8600-hardware-installation.md)

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que consulte a lista de verificação das configurações antes de começar. O processo de implementação e configuração pode demorar algum tempo a concluir.

> [!NOTE]
> As informações de implementação do StorSimple publicadas no site do Microsoft Azure aplicam-se exclusivamente aos dispositivos StorSimple da série 8000. Para obter informações completas sobre os dispositivos da série 7000, aceda a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com). Para obter informações sobre a implementação nas séries 7000, consulte o [manual de início rápido do sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).


## <a name="deployment-steps"></a>Passos da implementação
Efetue estes passos obrigatórios para configurar o dispositivo StorSimple e ligá-lo ao serviço Gestor de Dispositivos do StorSimple. Além das etapas necessárias, há etapas e procedimentos opcionais que talvez você precise concluir durante a implantação. As instruções passo-a-passo para a implementação indicam quando deve efetuar cada um dos seguintes passos opcionais.

| Passo | Descrição |
| --- | --- |
| **PRÉ-REQUISITOS** |Estes têm de ser concluídos no processo de preparação para a implementação futura. |
| [Lista de verificação das configurações de implementação](#deployment-configuration-checklist) |Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação. |
| [Pré-requisitos da implementação](#deployment-prerequisites) |Eles validam que o ambiente está pronto para implantação. |
|  | |
| **IMPLEMENTAÇÃO PASSO-A-PASSO** |Estes passos são obrigatórios para implementar o dispositivo StorSimple para produção. |
| [Etapa 1: Criar um novo serviço](#step-1-create-a-new-service) |Configure a gestão e o armazenamento na cloud do dispositivo StorSimple. *Se já existir um serviço para outros dispositivos StorSimple, ignore este passo*. |
| [Etapa 2: Obter a chave de registro do serviço](#step-2-get-the-service-registration-key) |Use essa chave para registrar e conectar seu dispositivo StorSimple ao serviço de gerenciamento. |
| [Etapa 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Ligue o dispositivo à rede e registe-o com o Azure para concluir a configuração através do serviço de gestão. |
| [Etapa 4: Concluir a configuração mínima do dispositivo](#step-4-complete-minimum-device-setup) </br>Opcional: Atualize seu dispositivo StorSimple. |Utilize o serviço de gestão para concluir a configuração do dispositivo e ativá-lo para fornecer armazenamento. |
| [Etapa 5: Criar um contêiner de volume](#step-5-create-a-volume-container) |Crie um contentor para os volumes de aprovisionamento. Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. |
| [Etapa 6: Criar um volume](#step-6-create-a-volume) |Aprovisione o(s) volume(s) de armazenamento no dispositivo StorSimple para os servidores. |
| [Etapa 7: Montar, inicializar e formatar um volume](#step-7-mount-initialize-and-format-a-volume) </br>Opcional: Configure o MPIO. |Ligue os servidores ao armazenamento iSCSI fornecido pelo dispositivo. Opcionalmente, configure o MPIO para garantir que os servidores possam tolerar falha de conexão, rede e interface. |
| [Etapa 8: Fazer um backup](#step-8-take-a-backup) |Configure a política de cópia de segurança para proteger os seus dados |
|  | |
| **OUTROS PROCEDIMENTOS** |Poderá ter de consultar estes procedimentos durante a implementação da solução. |
| [Configurar uma nova conta do Storage para o serviço](#configure-a-new-storage-account-for-the-service) | |
| [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Verificar e aplicar atualizações](#scan-for-and-apply-updates) | |
| [Obter o IQN de um anfitrião do Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Criar uma cópia de segurança manual](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Lista de verificação das configurações de implementação
Antes de implantar seu dispositivo StorSimple, você precisará coletar informações para configurar o software em seu dispositivo. Preparar com antecedência algumas destas informações ajudará a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Baixe e use esta lista de verificação para anotar os detalhes de configuração durante a implantação do dispositivo.

[Transferir a lista de verificação das configurações da implementação do StorSimple](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Pré-requisitos de implementação
As secções seguintes explicam os pré-requisitos de configuração do serviço Gestor de Dispositivos do StorSimple e do dispositivo StorSimple.

### <a name="for-the-storsimple-device-manager-service"></a>Para o serviço Gestor de Dispositivos do StorSimple
Antes de começar, certifique-se de que:

* Tem a conta Microsoft com credenciais de acesso.
* Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
* A subscrição do Microsoft Azure está ativada para o serviço Gestor de Dispositivos do StorSimple. A subscrição deve ser comprada através do [Contrato Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/).
* Tem acesso ao software de emulação do terminal como o PuTTY.

### <a name="for-the-device-in-the-datacenter"></a>Para o dispositivo no datacenter
Antes de configurar o dispositivo, certifique-se de que:

* O dispositivo é completamente descompactado, montado em bastidor e é feita a instalação dos cabos de alimentação, rede e acesso de série, conforme descrito em:
  
  * [Descompactar, montar em bastidor e instalar os cabos do dispositivo 8100](storsimple-8100-hardware-installation.md)
  * [Descompactar, montar em bastidor e instalar os cabos do dispositivo 8600](storsimple-8600-hardware-installation.md)

### <a name="for-the-network-in-the-datacenter"></a>Para a rede no datacenter
Antes de começar, certifique-se de que:

* As portas na firewall do datacenter estão abertas para permitir tráfego no iSCSI e na nuvem, tal como descrito em [Requisitos de rede do dispositivo StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).

## <a name="step-by-step-deployment"></a>Implementação passo-a-passo
Siga as seguintes instruções passo-a-passo para implementar o dispositivo StorSimple no datacenter.

## <a name="step-1-create-a-new-service"></a>Passo 1: Criar um novo serviço
Um serviço Gestor de Dispositivos do StorSimple pode gerir diversos dispositivos StorSimple. Execute as etapas a seguir para criar uma nova instância do serviço StorSimple Device Manager.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço. Esta conta do Storage será utilizada quando criar um contentor de volume.
> 
> * Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se você habilitou a criação automática de uma conta de armazenamento, [vá para a etapa 2: Obtenha a chave](#step-2-get-the-service-registration-key)de registro do serviço.


## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço
Assim que o serviço Gestor de Dispositivos do StorSimple estiver ativo e em execução, será necessário obter a chave de registo do serviço. Essa chave é usada para registrar e conectar seu dispositivo StorSimple ao serviço.

Execute as etapas a seguir no portal do governo.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passo 3: Configurar e registrar o dispositivo por meio do Windows PowerShell para StorSimple
Utilize o Windows PowerShell para StorSimple para concluir a configuração inicial do dispositivo StorSimple, conforme explicado no procedimento seguinte. Terá de utilizar o software de emulação do terminal para concluir este passo. Para mais informações, consulte [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passo 4: Concluir a configuração mínima do dispositivo
Para a configuração mínima do dispositivo StorSimple, é necessário:

* Forneça um nome amigável ao dispositivo.
* Defina o fuso horário do dispositivo.
* Atribuir endereços IP fixos a ambos os controladores.

Execute as etapas a seguir no portal do Azure governamental para concluir a configuração mínima do dispositivo.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>Passo 5: Criar um contentor de volume
Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. Terá de criar um contentor de volume antes de poder começar a aprovisionar volumes no dispositivo StorSimple.

Execute as etapas a seguir no portal do governo para criar um contêiner de volume.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passo 6: Criar um volume
Depois de criar um contentor de volume, pode aprovisionar um volume de armazenamento no dispositivo StorSimple para os servidores. Execute as etapas a seguir no portal do governo para criar um volume.

> [!IMPORTANT]
> O StorSimple Device Manager pode criar apenas volumes com provisionamento dinâmico.  No entanto, não é possível criar volumes parcialmente aprovisionados.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passo 7: Montar, inicializar e formatar um volume
Execute estas etapas no host do Windows Server.

> [!IMPORTANT]
> * Para uma maior disponibilidade da solução StorSimple, recomendamos que configure o MPIO nos servidores de anfitrião (opcionais) antes de configurar o iSCSI. A configuração do MPIO nos servidores de anfitrião irá garantir que os servidores podem tolerar falhas de ligação, da rede ou da interface.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI no anfitrião do Windows Server, consulte [Configurar o MPIO para o dispositivo StorSimple](storsimple-configure-mpio-windows-server.md). Estas instruções também incluem os passos para montar, inicializar e formatar os volumes do StorSimple.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI no anfitrião do Linux, consulte [Configurar o MPIO para o anfitrião Linux do StorSimple](storsimple-configure-mpio-on-linux.md).

Se decidir não configurar o MPIO, execute os seguintes passos para montar, inicializar e formatar os volumes do StorSimple num anfitrião do Windows Server.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Etapa 8: Fazer um backup
As cópias de segurança fornecem proteção para um ponto anterior no tempo dos volumes e melhoram a capacidade de recuperação enquanto minimizam os tempos de restauro. Pode efetuar dois tipos de cópia de segurança no dispositivo StorSimple: instantâneos locais e instantâneos de nuvem. Cada um destes tipos de cópia de segurança pode ser **Agendado** ou **Manual**.

Execute as etapas a seguir no portal do governo para criar um backup agendado.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Pode efetuar uma cópia de segurança manual em qualquer altura. Para obter os procedimentos, vá para [Criar uma cópia de segurança manual](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta do Storage para o serviço
Este passo é opcional e só precisa de o executar se não tiver ativado a criação automática de uma conta do Storage com o serviço. É obrigatória uma conta do Storage do Microsoft Azure para criar um contentor de volume do StorSimple.

Se precisar de criar uma conta do Storage do Azure numa região diferente, consulte [Acerca das Contas do Storage do Azure](../storage/common/storage-create-storage-account.md) para obter instruções passo-a-passo.

Execute as etapas a seguir no portal do governo, na página **serviço StorSimple Device Manager** .

[!INCLUDE [storsimple-configure-new-storage-account-u1](../../includes/storsimple-8000-configure-new-storage-account-u2.md)]

## <a name="use-putty-to-connect-to-the-device-serial-console"></a>Utilizar o PuTTY para ligar à consola de série do dispositivo
Para ligar ao Windows PowerShell para StorSimple, terá de utilizar o software de emulação do terminal como o PuTTY. Pode utilizar o PuTTY quando aceder ao dispositivo diretamente através da consola de série ou ao abrir uma sessão telnet a partir de um computador remoto.

[!INCLUDE [Use PuTTY to connect to the device serial console](../../includes/storsimple-use-putty.md)]

## <a name="scan-for-and-apply-updates"></a>Procurar e aplicar atualizações
A atualização do dispositivo pode demorar várias horas. Para obter passos detalhados sobre como instalar a atualização mais recente, aceda a [Instalar Atualização 4](storsimple-8000-install-update-4.md).

## <a name="get-the-iqn-of-a-windows-server-host"></a>Obter o IQN de um anfitrião do Windows Server
Execute os passos seguintes para obter o Nome Qualificado do iSCSI (IQN) de um anfitrião do Windows que está a executar o Windows Server® 2012.

[!INCLUDE [Get IQN of your Windows Server host](../../includes/storsimple-get-iqn.md)]

## <a name="create-a-manual-backup"></a>Criar uma cópia de segurança manual
Execute as etapas a seguir no portal governamental para criar um backup manual sob demanda para um único volume em seu dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passos Seguintes
* Configurar um [dispositivo virtual](storsimple-8000-cloud-appliance-u2.md).
* Utilizar o [serviço Gestor de Dispositivos do StorSimple](storsimple-8000-manager-service-administration.md) para gerir o dispositivo StorSimple.

