---
title: StorSimple Cloud Appliance Atualização 3 | Microsoft Docs
description: Saiba como criar, implementar e gerir uma StorSimple Cloud Appliance numa rede virtual do Microsoft Azure. (Aplica-se a Atualização 3 do StorSimple e posterior).
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/08/2017
ms.author: alkohli
ms.openlocfilehash: df7866d4f87f55523e8139232e48d81cb17c80e4
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57763168"
---
# <a name="deploy-and-manage-a-storsimple-cloud-appliance-in-azure-update-3-and-later"></a>Implementar e gerir uma StorSimple Cloud Appliance no Azure (Atualização 3 e posterior)

## <a name="overview"></a>Descrição geral

A StorSimple Cloud Appliance Série 8000 é uma capacidade adicional que é fornecida com a solução do Microsoft Azure StorSimple. A StorSimple Cloud Appliance é executada numa máquina virtual numa rede virtual do Microsoft Azure e pode utilizá-la para efetuar cópias de segurança e clonar dados a partir dos anfitriões.

Este artigo descreve o processo passo-a-passo para implementar e gerir uma StorSimple Cloud Appliance no Azure. Depois de ler este artigo, irá:

* Compreender como a aplicação da cloud é diferente do dispositivo físico.
* Conseguir criar e configurar a aplicação da cloud.
* Ligar à aplicação da cloud.
* Aprender a trabalhar com a aplicação da cloud.

Este tutorial aplica-se a todas as StorSimple Cloud Appliances que executam a Atualização 3 e posterior.

#### <a name="cloud-appliance-model-comparison"></a>Comparação de modelo de aplicação da cloud

O StorSimple Cloud Appliance está disponível em dois modelos, um 8010 convencional (anteriormente conhecido como 1100) e um premium 8020 (apresentado na Atualização 2). A tabela seguinte apresenta uma comparação dos dois modelos.

| Modelo do dispositivo | 8010<sup>1</sup> | 8020 |
| --- | --- | --- |
| **Capacidade máxima** |30 TB |64 TB |
| **VM do Azure** |Standard_A3 (4 núcleos, 7 GB de memória)| Standard_DS3 (4 núcleos, 14 GB de memória)|
| **Disponibilidade por regiões** |Todas as regiões do Azure |As regiões do Azure que suportam o Armazenamento Premium e VMs DS3 do Azure<br></br>Utilize [esta lista](https://azure.microsoft.com/regions/services/) para ver se ambas as **Máquinas Virtuais > série DS** e o **Armazenamento > Armazenamento em Disco** estão disponíveis na sua região. |
| **Tipo de armazenamento** |Utiliza o armazenamento padrão do Azure para discos locais<br></br> Saiba como [criar uma conta do Storage padrão](../storage/common/storage-create-storage-account.md) |Utiliza o Armazenamento Premium do Azure para discos locais<sup>2</sup> <br></br> |
| **Orientações para a carga de trabalho** |Obtenção ao nível de item dos ficheiros a partir de cópias de segurança |Cenários de desenvolvimento e teste da cloud <br></br>Baixa latência e cargas de trabalho de desempenho superior<br></br>Dispositivo secundário para a recuperação após desastre |

<sup>1</sup> *Anteriormente conhecido como 1100*.

<sup>2</sup> *Os dispositivos 8010 e 8020 utilizam o armazenamento padrão do Azure para o escalão de nuvem. A diferença só existe no escalão local dentro do dispositivo*.

## <a name="how-the-cloud-appliance-differs-from-the-physical-device"></a>Como a aplicação da cloud é diferente do dispositivo físico

A StorSimple Cloud Appliance é uma versão exclusivamente de software do StorSimple que é executada num único nó na máquina virtual do Microsoft Azure. A aplicação da cloud suporta cenários de recuperação após desastre em que o dispositivo físico não está disponível. A aplicação da cloud é adequada para ser utilizada na obtenção ao nível dos itens a partir de cópias de segurança, na recuperação após desastre no local e em cenários de desenvolvimento e teste da cloud.

#### <a name="differences-from-the-physical-device"></a>Diferenças do dispositivo físico

A tabela seguinte mostra algumas diferenças fundamentais entre a StorSimple Cloud Appliance e o dispositivo físico StorSimple.

|  | Dispositivo físico | Aplicação da cloud |
| --- | --- | --- |
| **Localização** |Reside no datacenter. |É executado no Azure. |
| **Interfaces de rede** |Possui seis interfaces de rede: DADOS 0 a 5 de dados. |Tem apenas uma interface de rede: DADOS 0. |
| **Registo** |Registado durante o passo de configuração inicial. |O registo é uma tarefa separada. |
| **Chave de encriptação dos dados do serviço** |Volte a gerá-la no dispositivo físico e, em seguida, atualize a aplicação da cloud com a nova chave. |Não é possível voltar a gerar a chave a partir da aplicação da cloud. |
| **Tipos de volume suportados** |Suporta volumes afixados localmente e escalonados. |Suporta apenas volumes escalonados. |

## <a name="prerequisites-for-the-cloud-appliance"></a>Pré-requisitos para a aplicação da cloud

As secções seguintes explicam os pré-requisitos de configuração para a StorSimple Cloud Appliance. Antes de implementar uma aplicação da cloud, reveja as considerações de segurança para utilizar uma aplicação da cloud.

[!INCLUDE [StorSimple Cloud Appliance security](../../includes/storsimple-8000-cloud-appliance-security.md)]

#### <a name="azure-requirements"></a>Requisitos do Azure

Antes de aprovisionar a aplicação da cloud, terá de efetuar os seguintes preparativos no ambiente do Azure:

* Certifique-se de que tem um dispositivo físico da série StorSimple 8000 (modelo 8100 ou 8600) implementado e em execução no datacenter. Registe este dispositivo com o mesmo serviço de Gestor de Dispositivos do StorSimple para qual pretende criar uma StorSimple Cloud Appliance.
* Para a aplicação da cloud, [configure uma rede virtual no Azure](../virtual-network/manage-virtual-network.md#create-a-virtual-network). Se utilizar o Premium Storage, tem de criar uma rede virtual numa região do Azure que suporte o Premium Storage. As regiões de Armazenamento Premium são regiões que correspondem à linha para Armazenamento no disco na [lista de Serviços do Azure por Região](https://azure.microsoft.com/regions/services/).
* É recomendado que utilize o servidor DNS predefinido fornecido pelo Azure em vez de especificar o próprio nome de servidor DNS. Se o nome de servidor DNS não for válido ou se o servidor DNS não for capaz de resolver corretamente os endereços IP, a criação da aplicação da cloud irá falhar.
* As opções de ponto a site e site a site são opcionais, mas não obrigatórias. Se desejar, pode configurar estas opções para cenários mais avançados.
* Pode criar [máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-quick-create-portal.md) (servidores anfitriões) na rede virtual que pode utilizar os volumes expostos pela aplicação da cloud. Estes servidores devem cumprir os seguintes requisitos:

  * Ser uma VMs com Windows ou Linux e ter um software iniciador do iSCSI instalado.
  * Estar em execução na mesma rede virtual da aplicação da cloud.
  * Conseguir estabelecer ligação ao destino iSCSI da aplicação da cloud através do endereço IP da aplicação da cloud.
  * Certifique-se de que configurou o suporte para o tráfego do iSCSI e da nuvem na mesma rede virtual.

#### <a name="storsimple-requirements"></a>Requisitos do StorSimple

Efetue as seguintes atualizações no serviço Gestor de Dispositivos do StorSimple antes de criar uma aplicação da cloud:

* Adicionar [registos de controlo de acesso](storsimple-8000-manage-acrs.md) para as VMs que serão servidores de anfitrião para a aplicação da cloud.
* Utilizar uma [conta de armazenamento](storsimple-8000-manage-storage-accounts.md#add-a-storage-account) na mesma região da aplicação da cloud. As contas do Storage em regiões diferentes poderão ter um fraco desempenho. Pode utilizar uma conta de armazenamento Standard ou Premium com a aplicação da cloud. Obter mais informações sobre como criar uma [conta de armazenamento Standard](../storage/common/storage-create-storage-account.md).
* Utilize uma conta de armazenamento diferente para a criação da aplicação da cloud a partir da conta utilizada para os seus dados. Com a mesma conta do Storage, poderá obter um fraco desempenho.

Certifique-se de que tem as seguintes informações antes de começar:

* Conta do portal do Azure com credenciais de acesso.
* Uma cópia da chave de encriptação de dados do serviço a partir do seu dispositivo físico registado no serviço Gestor de Dispositivos do StorSimple.

## <a name="create-and-configure-the-cloud-appliance"></a>Criar e configurar a aplicação da cloud

Antes de executar estes procedimentos, certifique-se de que cumpriu os [Pré-requisitos para a aplicação da cloud](#prerequisites-for-the-cloud-appliance).

Execute os seguintes passos para criar a StorSimple Cloud Appliance.

### <a name="step-1-create-a-cloud-appliance"></a>Passo 1: Criar uma aplicação da cloud

Execute os seguintes passos para criar a StorSimple Cloud Appliance.

[!INCLUDE [Create a cloud appliance](../../includes/storsimple-8000-create-cloud-appliance-u2.md)]

Se a criação da aplicação da cloud falhar neste passo, é possível que não tenha conectividade à Internet. Para obter mais informações, aceda a [troubleshoot Internet connectivity failures](#troubleshoot-internet-connectivity-errors) (resolver problemas relacionados com falhas de conectividade à Internet) quando criar uma aplicação da cloud.

### <a name="step-2-configure-and-register-the-cloud-appliance"></a>Passo 2: Configurar e registar a aplicação da cloud

Antes de iniciar este procedimento, certifique-se de que tem uma cópia da chave de encriptação de dados do serviço. A chave de encriptação de dados do serviço é criada quando regista o seu primeiro dispositivo físico StorSimple com o serviço Gestor de Dispositivos do StorSimple. Foi-lhe indicado para guardá-la numa localização segura. Se não tiver uma cópia da chave de encriptação de dados do serviço, deve contactar o apoio técnico da Microsoft para obter assistência.

Execute os seguintes passos para configurar e registar a StorSimple Cloud Appliance.

[!INCLUDE [Configure and register a cloud appliance](../../includes/storsimple-8000-configure-register-cloud-appliance.md)]

### <a name="step-3-optional-modify-the-device-configuration-settings"></a>Passo 3: (Opcional) Modificar as definições de configuração do dispositivo

A seguinte secção descreve as definições de configuração do dispositivo necessárias para a StorSimple Cloud Appliance se pretender utilizar o CHAP, o Snapshot Manager do StorSimple ou alterar a palavra-passe de administrador de dispositivos.

#### <a name="configure-the-chap-initiator"></a>Configurar o iniciador do CHAP

Este parâmetro contém as credenciais que a sua aplicação da cloud (destino) espera dos iniciadores (servidores) que estão a tentar aceder aos volumes. Os iniciadores apresentam um nome de utilizador e uma palavra-passe ao CHAP para se identificarem a si próprios no dispositivo durante esta autenticação. Para obter os passos detalhados, consulte [Configurar o CHAP para o dispositivo](storsimple-8000-configure-chap.md#unidirectional-or-one-way-authentication).

#### <a name="configure-the-chap-target"></a>Configurar o destino do CHAP

Este parâmetro contém as credenciais que a aplicação da cloud utiliza quando um iniciador ativado pelo CHAP solicita uma autenticação mútua ou bidirecional. A aplicação da cloud utiliza um nome de utilizador e palavra-passe para o CHAP Inverso para se identificar a si próprio para o iniciador durante este processo de autenticação.

> [!NOTE]
> As definições de destino do CHAP são definições globais. Quando estas definições são aplicadas, todos os volumes associados à aplicação da cloud utilizam a autenticação do CHAP.

Para obter os passos detalhados, consulte [Configurar o CHAP para o dispositivo](storsimple-8000-configure-chap.md#bidirectional-or-mutual-authentication).

#### <a name="configure-the-storsimple-snapshot-manager-password"></a>Configurar a palavra-passe do Snapshot Manager do StorSimple

O software Snapshot Manager do StorSimple reside no anfitrião do Windows e permite aos administradores gerir cópias de segurança do dispositivo StorSimple sob a forma de instantâneos locais e na nuvem.

> [!NOTE]
> Para a aplicação da cloud, o anfitrião do Windows é uma máquina virtual do Azure.

Quando configurar um dispositivo no Snapshot Manager do StorSimple, é-lhe solicitado para indicar o endereço IP do dispositivo StorSimple e a palavra-passe para autenticar o dispositivo de armazenamento. Para obter passos detalhados, consulte [Configurar palavra-passe do Snapshot Manager do StorSimple](storsimple-8000-change-passwords.md#set-the-storsimple-snapshot-manager-password).

#### <a name="change-the-device-administrator-password"></a>Alterar palavra-passe do administrador do dispositivo

Quando utiliza a interface do Windows PowerShell para aceder à aplicação da cloud, é-lhe pedido para introduzir uma palavra-passe de administrador do dispositivo. Para garantir a segurança dos dados, tem de alterar esta palavra-passe antes de a aplicação da cloud poder ser utilizada. Para obter passos detalhados, consulte [Configurar palavra-passe de administrador dos dispositivos](../storsimple/storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="connect-remotely-to-the-cloud-appliance"></a>Ligar à aplicação da cloud remotamente

O acesso remoto à aplicação da cloud através da interface do Windows PowerShell não está ativado por predefinição. Tem de ativar a gestão remota da aplicação da cloud em primeiro lugar e, em seguida, no cliente utilizado para aceder à aplicação da cloud.

O procedimento de dois passos seguinte descreve como ligar remotamente à sua aplicação da cloud.

### <a name="step-1-configure-remote-management"></a>Passo 1: Configurar a gestão remota

Execute os seguintes passos para configurar a gestão remota da StorSimple Cloud Appliance.

[!INCLUDE [Configure remote management via HTTP for cloud appliance](../../includes/storsimple-8000-configure-remote-management-http-device.md)]

### <a name="step-2-remotely-access-the-cloud-appliance"></a>Passo 2: Aceder remotamente a aplicação da cloud

Depois de ativar a gestão remota na aplicação da cloud, utilize a comunicação remota do Windows PowerShell para ligar à aplicação a partir de outra máquina virtual dentro da mesma rede virtual. Por exemplo, pode ligar a partir da VM de anfitrião que configurou e utilizou para estabelecer ligação a iSCSI. Na maioria das implementações, irá abrir um ponto final público para aceder à VM do anfitrião que pode utilizar para aceder à aplicação da cloud.

> [!WARNING]
> **Para maior segurança, recomendamos vivamente que utilize o HTTPS quando ligar para os pontos finais e, em seguida, elimine os pontos finais depois de ter concluído a sessão remota do PowerShell.**

Tem de seguir os procedimentos em [Connecting remotely to your StorSimple device](storsimple-8000-remote-connect.md) (Ligar remotamente ao dispositivo StorSimple) para configurar remotamente a aplicação da cloud.

## <a name="connect-directly-to-the-cloud-appliance"></a>Ligar à aplicação da cloud diretamente

Também pode ligar à aplicação da cloud diretamente. Para ligar diretamente à aplicação da cloud a partir de outro computador fora da rede virtual ou fora do ambiente do Microsoft Azure, tem de criar pontos finais adicionais.

Execute os seguintes passos para criar um ponto final público na aplicação da cloud.

[!INCLUDE [Create public endpoints on a cloud appliance](../../includes/storsimple-8000-create-public-endpoints-cloud-appliance.md)]

Recomendamos que ligue a partir de outra máquina virtual dentro da mesma rede virtual, porque esta prática minimiza o número de pontos finais públicos na rede virtual. Neste caso, ligue à máquina virtual através de uma sessão de Ambiente de Trabalho Remoto e, em seguida, configure a máquina virtual para ser utilizada tal como faria com qualquer outro cliente Windows numa rede local. Não é necessário acrescentar o número de porta, porque a porta já é conhecida.

## <a name="get-private-ip-for-the-cloud-appliance"></a>Obter o IP privado da aplicação da cloud

Para que a aplicação da cloud se ligue ao servidor anfitrião na mesma rede virtual, precisa do endereço IP privado ou interno dessa aplicação. Execute os passos seguintes para obter o endereço IP privado da aplicação da cloud

1. Aceda à máquina virtual subjacente da sua aplicação da cloud. A máquina virtual tem o mesmo nome que a sua aplicação da cloud. Aceda a **Todos os Recursos**, indique o nome da aplicação da cloud e da subscrição e selecione o tipo como “máquinas virtuais”. Na lista de máquinas virtuais apresentadas, selecione e clique naquela que corresponde à aplicação da cloud.

     ![Selecione a máquina virtual da sua aplicação da cloud.](./media/storsimple-8000-cloud-appliance-u2/sca-vm.png)

2. Aceda a **Definições > Rede**. No painel do lado direito, pode ver o endereço IP privado da aplicação de cloud. Tome nota do mesmo.

    ![Obter o endereço IP privado da sua aplicação da cloud](./media/storsimple-8000-cloud-appliance-u2/sca-private-ip-vm-networking.png)

## <a name="work-with-the-storsimple-cloud-appliance"></a>Trabalhe com a StorSimple Cloud Appliance

Agora que já criou e configurou a StorSimple Cloud Appliance, está pronto para começar a trabalhar com a mesma. Pode trabalhar com contentores de volumes, volumes e políticas de cópia de segurança numa aplicação da cloud, tal como faria num dispositivo StorSimple físico. A única diferença é que necessita de se certificar que seleciona a aplicação da cloud a partir da lista de dispositivos. Consulte [use the StorSimple Device Manager service to manage a cloud appliance](storsimple-8000-manager-service-administration.md) (utilizar o serviço Gestor de Dispositivos StorSimple para gerir uma aplicação da cloud), para obter procedimentos passo-a-passo de várias tarefas de gestão da aplicação da cloud.

As secções seguintes abordam algumas das diferenças que encontra quando trabalha com a aplicação da cloud.

### <a name="maintain-a-storsimple-cloud-appliance"></a>Manter uma StorSimple Cloud Appliance

Porque é um dispositivo apenas de software, a manutenção da aplicação da cloud é mínima quando comparada com a manutenção do dispositivo físico.

Não pode atualizar uma aplicação da cloud. Utilize a versão mais recente do software para criar uma nova aplicação da cloud.


### <a name="storage-accounts-for-a-cloud-appliance"></a>Contas de armazenamento para uma aplicação da cloud

As contas de armazenamento são criadas para utilização pelo serviço Gestor de Dispositivos do StorSimple, pela aplicação da cloud e pelo dispositivo físico. Ao criar as suas contas de armazenamento, recomendamos que utilize um identificador de região no nome amigável. Isto ajuda a assegurar que a região é consistente em todos os componentes de sistema. Para uma aplicação da cloud, é importante que todos os componentes se encontrem na mesma região para evitar problemas de desempenho.

Para um procedimento passo-a-passo, consulte [Adicionar uma conta do Storage](storsimple-8000-manage-storage-accounts.md#add-a-storage-account).

### <a name="deactivate-a-storsimple-cloud-appliance"></a>Desativar uma StorSimple Cloud Appliance

Ao desativar uma aplicação da cloud, a ação elimina a VM e os recursos criados no aprovisionamento. Depois de a aplicação da cloud ter sido desativada, não é possível restaurá-la para o estado anterior. Antes de desativar a aplicação da cloud, certifique-se de que para ou elimina os clientes e os anfitriões que dependem dela.

Desativar uma aplicação da cloud resulta nas seguintes ações:

* A aplicação da cloud é removida.
* O disco do SO e os discos de dados criados para a aplicação da cloud são removidos.
* O serviço alojado e a rede virtual criados durante o aprovisionamento são retidos. Se não estiver a utilizá-los, deverá eliminá-los manualmente.
* Os instantâneos de cloud criados para a aplicação da cloud são retidos.

Para um procedimento passo-a-passo, consulte [Desativar e eliminar o dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

Assim que a aplicação da cloud é apresentada como desativada no painel do serviço Gestor de Dispositivos do StorSimple, pode eliminar a aplicação da cloud a partir da lista de dispositivos no painel **Dispositivos**.

### <a name="start-stop-and-restart-a-cloud-appliance"></a>Iniciar, parar e reiniciar uma aplicação da cloud
Ao contrário do dispositivo físico StorSimple, não existe nenhum botão de ligar ou desligar para uma StorSimple Cloud Appliance. No entanto, pode suceder que tenha de parar e reiniciar a aplicação da cloud.

A forma mais fácil de iniciar, parar e reiniciar uma aplicação da cloud é através do painel de serviço de Máquinas Virtuais. Aceda ao serviço Máquina virtual. Na lista de VMs, identifique a VM que corresponde à sua aplicação da cloud (mesmo nome) e clique no nome da VM. Quando observar o painel da máquina virtual, o estado da aplicação da cloud é **Em funcionamento** porque esta é iniciada por predefinição depois de criada. Pode iniciar, parar e reiniciar uma máquina virtual em qualquer altura.

[!INCLUDE [Stop and restart cloud appliance](../../includes/storsimple-8000-stop-restart-cloud-appliance.md)]

### <a name="reset-to-factory-defaults"></a>Repor as predefinições de fábrica
Se decidir que pretende recomeçar com a aplicação da cloud, basta desativá-la e eliminá-la e, em seguida, criar uma nova.

## <a name="fail-over-to-the-cloud-appliance"></a>Efetuar ativação pós-falha na aplicação da cloud
A recuperação após desastre (DR) é um dos principais cenários para os quais a StorSimple Cloud Appliance foi concebida. Neste cenário, o dispositivo físico StorSimple ou todo o datacenter pode não estar disponível. Felizmente, pode utilizar uma aplicação da cloud para restaurar as operações numa localização alternativa. Durante a DR, os contentores de volume do dispositivo de origem alteram a propriedade e são transferidos para a aplicação da cloud.

Os pré-requisitos para DR são:

* A aplicação da cloud é criada e configurada.
* Todos os volumes dentro do contentor de volumes estão offline.
* O contentor de volumes em relação ao qual efetua a ativação pós-falha encontra-se associado ao instantâneo de cloud.

> [!NOTE]
> * Ao utilizar uma aplicação da cloud como dispositivo secundário para DR, tenha em atenção que o 8010 tem 30 TB de Armazenamento Standard e o 8020 tem 64 TB de Armazenamento Premium. Quanto maior for a capacidade da aplicação da cloud 8020, mais adequada será para um cenário de DR.

Para um procedimento passo-a-passo, consulte [fail over to a cloud appliance](storsimple-8000-device-failover-cloud-appliance.md) (ativação pós-falha numa aplicação da cloud).

## <a name="delete-the-cloud-appliance"></a>Eliminar a aplicação da cloud
Se tiver configurado e utilizado anteriormente uma StorSimple Cloud Appliance, mas pretender agora parar a acumulação de encargos de computação pela respetiva utilização, tem de parar a aplicação da cloud. Parar a aplicação da cloud desaloca a VM. Esta ação irá parar a acumulação de encargos na sua subscrição. No entanto, irão continuar os encargos de armazenamento para os discos de SO e dados.

Para parar todos os encargos, tem de eliminar a aplicação da cloud. Para eliminar as cópias de segurança criadas pela aplicação da cloud, pode desativar ou eliminar o dispositivo. Para obter mais informações, consulte [Desativar e eliminar um dispositivo StorSimple](storsimple-8000-deactivate-and-delete-device.md).

[!INCLUDE [Delete a cloud appliance](../../includes/storsimple-8000-delete-cloud-appliance.md)]

## <a name="troubleshoot-internet-connectivity-errors"></a>Resolver problemas relacionados com falhas de conectividade Internet
Durante a criação de uma aplicação da cloud, se não existir conectividade à Internet, o passo de criação falha. Para resolver falhas de conectividade da Internet, execute os seguintes passos no portal do Azure:

1. [Criar uma máquina virtual do Windows no portal do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal). Esta máquina virtual deve utilizar a mesma conta de armazenamento, a mesma VNet e a mesma sub-rede que a aplicação da cloud utiliza. Se já tiver um anfitrião do Windows Server existente no Azure que utilize a mesma conta de armazenamento, VNet e sub-rede, também pode utilizá-lo para resolver problemas relacionados com a conectividade Internet.
2. Registe remotamente na máquina virtual criada no passo anterior.
3. Abra uma janela de comando dentro da máquina virtual (Win + R e em seguida, escreva `cmd`).
4. Na linha de comandos, escreva o comando seguinte.

    `nslookup windows.net`
5. Se `nslookup` falhar, significa que a falha de conectividade Internet está a impedir a aplicação da cloud de se registar no serviço Gestor de Dispositivos do StorSimple.
6. Faça as alterações necessárias à rede virtual para se certificar de que a aplicação da cloud é capaz de aceder a sites do Azure, como _windows.net_.

## <a name="next-steps"></a>Passos Seguintes
* Saber como [utilizar o serviço Gestor de Dispositivos do StorSimple para gerir uma aplicação da cloud](storsimple-8000-manager-service-administration.md).
* Compreender como [restaurar um volume StorSimple a partir de um conjunto de cópias de segurança](storsimple-8000-restore-from-backup-set-u2.md).
