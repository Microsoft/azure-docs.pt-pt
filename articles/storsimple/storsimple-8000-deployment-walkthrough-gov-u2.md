---
title: Implementar dispositivo da série StorSimple 8000 no portal do Governo | Microsoft Docs
description: Descreve os passos e as melhores práticas para a implementação do dispositivo da série StorSimple 8000 em execução da Atualização 3 e posterior, e o serviço no portal do Governo Azure.
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
ms.openlocfilehash: d736c09fc1c9490f79dfc526895970e01b8b45cc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "94963186"
---
# <a name="deploy-your-on-premises-storsimple-device-in-the-government-portal"></a>Coloque o seu dispositivo StorSimple no portal do Governo

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Descrição Geral
Bem-vindo à implementação do dispositivo Microsoft Azure StorSimple. Estes tutoriais de implementação aplicam-se ao software StorSimple 8000 Series running Update 3 ou mais tarde no portal do Governo Azure. Esta série de tutoriais inclui uma lista de verificação de configuração, uma lista de pré-requisitos de configuração e passos de configuração detalhados para o seu dispositivo StorSimple.

As informações nestes tutoriais pressupõem que leu as precauções de segurança e descompactou, montou em bastidor e instalou os cabos do dispositivo StorSimple. Se ainda precisar de efetuar essas tarefas, comece por ler as [precauções de segurança](storsimple-8000-safety.md). Siga as instruções específicas do dispositivo para descompactar, montar em bastidor e instalar os cabos do dispositivo.

* [Descompactar, montar em bastidor e instalar os cabos do 8100](storsimple-8100-hardware-installation.md)
* [Descompactar, montar em bastidor e instalar os cabos do 8600](storsimple-8600-hardware-installation.md)

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. Recomendamos que consulte a lista de verificação das configurações antes de começar. O processo de implementação e configuração pode demorar algum tempo a concluir.

> [!NOTE]
> As informações de implementação do StorSimple publicadas no site do Microsoft Azure aplicam-se exclusivamente aos dispositivos StorSimple da série 8000. Para obter informações completas sobre os dispositivos da série 7000, aceda a: [http://onlinehelp.storsimple.com/](http://onlinehelp.storsimple.com) . Para obter informações sobre a implementação nas séries 7000, consulte o [manual de início rápido do sistema StorSimple](http://onlinehelp.storsimple.com/111_Appliance/).


## <a name="deployment-steps"></a>Passos da implementação
Efetue estes passos obrigatórios para configurar o dispositivo StorSimple e ligá-lo ao serviço Gestor de Dispositivos do StorSimple. Além das etapas necessárias, existem etapas e procedimentos opcionais que poderá ter de completar durante a implantação. As instruções passo-a-passo para a implementação indicam quando deve efetuar cada um dos seguintes passos opcionais.

| Passo | Description |
| --- | --- |
| **Pré-requisitos** |Estes têm de ser concluídos no processo de preparação para a implementação futura. |
| [Lista de verificação das configurações de implementação](#deployment-configuration-checklist) |Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação. |
| [Pré-requisitos de implantação](#deployment-prerequisites) |Estes validam que o ambiente está pronto para ser implantado. |
|  | |
| **IMPLANTAÇÃO PASSO A PASSO** |Estes passos são obrigatórios para implementar o dispositivo StorSimple para produção. |
| [Passo 1: Criar um novo serviço](#step-1-create-a-new-service) |Configure a gestão e o armazenamento na cloud do dispositivo StorSimple. *Ignore este passo se tiver um serviço existente para outros dispositivos StorSimple*. |
| [Passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key) |Utilize esta chave para registar e ligar o seu dispositivo StorSimple ao serviço de gestão. |
| [Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para storSimple](#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |Ligue o dispositivo à rede e registe-o com o Azure para concluir a configuração através do serviço de gestão. |
| [Passo 4: Completar a configuração mínima do dispositivo](#step-4-complete-minimum-device-setup) </br>Opcional: Atualizar o dispositivo StorSimple. |Utilize o serviço de gestão para concluir a configuração do dispositivo e ativá-lo para fornecer armazenamento. |
| [Passo 5: Criar um contentor de volume](#step-5-create-a-volume-container) |Crie um contentor para os volumes de aprovisionamento. Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. |
| [Passo 6: Criar um volume](#step-6-create-a-volume) |Aprovisione o(s) volume(s) de armazenamento no dispositivo StorSimple para os servidores. |
| [Passo 7: Montar, inicializar e formatar um volume](#step-7-mount-initialize-and-format-a-volume) </br>Opcional: Configurar o MPIO. |Ligue os servidores ao armazenamento iSCSI fornecido pelo dispositivo. Opcionalmente, configure o MPIO para garantir que os seus servidores podem tolerar falhas de ligação, rede e interface. |
| [Passo 8: Efetuar uma cópia de segurança](#step-8-take-a-backup) |Configure a política de cópia de segurança para proteger os seus dados |
|  | |
| **OUTROS PROCEDIMENTOS** |Poderá ter de consultar estes procedimentos durante a implementação da solução. |
| [Configurar uma nova conta do Storage para o serviço](#configure-a-new-storage-account-for-the-service) | |
| [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console) | |
| [Procurar e aplicar atualizações](#scan-for-and-apply-updates) | |
| [Obter o IQN de um anfitrião do Windows Server](#get-the-iqn-of-a-windows-server-host) | |
| [Criar uma cópia de segurança manual](#create-a-manual-backup) | |


## <a name="deployment-configuration-checklist"></a>Lista de verificação das configurações de implementação
Antes de implementar o seu dispositivo StorSimple, terá de recolher informações para configurar o software no seu dispositivo. Preparar com antecedência algumas destas informações ajudará a simplificar o processo de implementação do dispositivo StorSimple no seu ambiente. Descarregue e utilize esta lista de verificação para ter em conta os detalhes de configuração à medida que implementa o seu dispositivo.

[Transferir a lista de verificação das configurações da implementação do StorSimple](https://www.microsoft.com/download/details.aspx?id=49159)

## <a name="deployment-prerequisites"></a>Pré-requisitos da implementação
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
Um serviço Gestor de Dispositivos do StorSimple pode gerir diversos dispositivos StorSimple. Execute os seguintes passos para criar uma nova instância do serviço StorSimple Device Manager.

[!INCLUDE [storsimple-8000-create-new-service-gov](../../includes/storsimple-8000-create-new-service-gov.md)]

> [!IMPORTANT]
> Se não tiver ativado a criação automática de uma conta do Storage com o serviço, terá de criar pelo menos uma conta do Storage depois de ter criado com sucesso um serviço. Esta conta do Storage será utilizada quando criar um contentor de volume.
> 
> * Se não tiver criado automaticamente uma conta do Storage, vá para [Configurar uma nova conta do Storage para o serviço](#configure-a-new-storage-account-for-the-service) para obter instruções detalhadas.
> * Se tiver ativado a criação automática de uma conta do Storage, vá para [Passo 2: Obter a chave de registo do serviço](#step-2-get-the-service-registration-key).


## <a name="step-2-get-the-service-registration-key"></a>Passo 2: Obter a chave de registo do serviço
Assim que o serviço Gestor de Dispositivos do StorSimple estiver ativo e em execução, será necessário obter a chave de registo do serviço. Esta chave é utilizada para registar e ligar o seu dispositivo StorSimple ao serviço.

Execute os seguintes passos no portal do Governo.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

## <a name="step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple"></a>Passo 3: Configurar e registar o dispositivo através do Windows PowerShell para StorSimple
Utilize o Windows PowerShell para StorSimple para concluir a configuração inicial do dispositivo StorSimple, conforme explicado no procedimento seguinte. Terá de utilizar o software de emulação do terminal para concluir este passo. Para mais informações, consulte [Utilizar o PuTTY para ligar à consola de série do dispositivo](#use-putty-to-connect-to-the-device-serial-console).

[!INCLUDE [storsimple-8000-configure-and-register-device-gov](../../includes/storsimple-8000-configure-and-register-device-gov-u2.md)]

## <a name="step-4-complete-minimum-device-setup"></a>Passo 4: Concluir a configuração mínima do dispositivo
Para a configuração mínima do dispositivo StorSimple, é necessário:

* Forneça um nome amigável ao dispositivo.
* Defina o fuso horário do dispositivo.
* Atribuir endereços IP fixos a ambos os controladores.

Execute os seguintes passos no portal do Governo Azure para completar a configuração mínima do dispositivo.

[!INCLUDE [storsimple-8000-complete-minimum-device-setup-u2](../../includes/storsimple-8000-complete-minimum-device-setup-u2.md)]

## <a name="step-5-create-a-volume-container"></a>Passo 5: Criar um contentor de volume
Um contentor de volume possui uma conta do Storage, largura de banda e definições de encriptação em todos os volumes nele contidos. Terá de criar um contentor de volume antes de poder começar a aprovisionar volumes no dispositivo StorSimple.

Execute os seguintes passos no portal do Governo para criar um recipiente de volume.

[!INCLUDE [storsimple-8000-create-volume-container](../../includes/storsimple-8000-create-volume-container.md)]

## <a name="step-6-create-a-volume"></a>Passo 6: Criar um volume
Depois de criar um contentor de volume, pode aprovisionar um volume de armazenamento no dispositivo StorSimple para os servidores. Execute os seguintes passos no portal do Governo para criar um volume.

> [!IMPORTANT]
> O StorSimple Device Manager só pode criar volumes pouco abastetados.  No entanto, não é possível criar volumes parcialmente aprovisionados.

[!INCLUDE [storsimple-8000-create-volume](../../includes/storsimple-8000-create-volume-u2.md)]

## <a name="step-7-mount-initialize-and-format-a-volume"></a>Passo 7: Montar, inicializar e formatar um volume
Execute estes passos no anfitrião do Windows Server.

> [!IMPORTANT]
> * Para uma maior disponibilidade da solução StorSimple, recomendamos que configure o MPIO nos servidores de anfitrião (opcionais) antes de configurar o iSCSI. A configuração do MPIO nos servidores de anfitrião irá garantir que os servidores podem tolerar falhas de ligação, da rede ou da interface.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI no anfitrião do Windows Server, consulte [Configurar o MPIO para o dispositivo StorSimple](./storsimple-8000-configure-mpio-windows-server.md). Estas instruções também incluem os passos para montar, inicializar e formatar os volumes do StorSimple.
> * Para obter as instruções de configuração e instalação do MPIO e do iSCSI no anfitrião do Linux, consulte [Configurar o MPIO para o anfitrião Linux do StorSimple](storsimple-configure-mpio-on-linux.md).

Se decidir não configurar o MPIO, execute os seguintes passos para montar, inicializar e formatar os volumes do StorSimple num anfitrião do Windows Server.

[!INCLUDE [storsimple-mount-initialize-format-volume](../../includes/storsimple-mount-initialize-format-volume.md)]

## <a name="step-8-take-a-backup"></a>Passo 8: Efetuar uma cópia de segurança
As cópias de segurança fornecem proteção para um ponto anterior no tempo dos volumes e melhoram a capacidade de recuperação enquanto minimizam os tempos de restauro. Pode efetuar dois tipos de cópia de segurança no dispositivo StorSimple: instantâneos locais e instantâneos de nuvem. Cada um destes tipos de cópia de segurança pode ser **Agendado** ou **Manual**.

Execute os seguintes passos no portal do Governo para criar um backup programado.

[!INCLUDE [storsimple-8000-take-backup](../../includes/storsimple-8000-take-backup.md)]

Pode efetuar uma cópia de segurança manual em qualquer altura. Para obter os procedimentos, vá para [Criar uma cópia de segurança manual](#create-a-manual-backup).

## <a name="configure-a-new-storage-account-for-the-service"></a>Configurar uma nova conta do Storage para o serviço
Este passo é opcional e só precisa de o executar se não tiver ativado a criação automática de uma conta do Storage com o serviço. É obrigatória uma conta do Storage do Microsoft Azure para criar um contentor de volume do StorSimple.

Se precisar de criar uma conta do Storage do Azure numa região diferente, consulte [Acerca das Contas do Storage do Azure](../storage/common/storage-account-create.md) para obter instruções passo-a-passo.

Execute os seguintes passos no portal do Governo, na página **de serviço StorSimple Device Manager.**

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
Execute os seguintes passos no portal do Governo para criar uma cópia de segurança manual a pedido para um único volume no seu dispositivo StorSimple.

[!INCLUDE [Create a manual backup](../../includes/storsimple-8000-create-manual-backup.md)]

## <a name="next-steps"></a>Passos seguintes
* Configurar um [dispositivo virtual](storsimple-8000-cloud-appliance-u2.md).
* Utilizar o [serviço Gestor de Dispositivos do StorSimple](storsimple-8000-manager-service-administration.md) para gerir o dispositivo StorSimple.