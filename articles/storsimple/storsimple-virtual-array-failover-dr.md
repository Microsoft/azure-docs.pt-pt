---
title: StorSimple Virtual Array desastre dispositivo e de recuperação de ativação pós-falha | Documentos da Microsoft
description: Saiba mais sobre como realizar a ativação pós-falha sua matriz Virtual StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: be3d98dc0b3a8119fb853493440c6fc78d65c5a2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57454243"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Ativação pós-falha de dispositivo e de recuperação após desastre para a sua matriz Virtual do StorSimple através do portal do Azure

## <a name="overview"></a>Descrição geral
Este artigo descreve a recuperação após desastre para o Microsoft Azure StorSimple Virtual Array incluindo os passos detalhados para a ativação pós-falha para uma matriz. Uma ativação pós-falha permite-lhe mover seus dados a partir de um *origem* dispositivo no Centro de dados para um *destino* dispositivo. O dispositivo de destino pode estar localizado no mesmo ou outra localização geográfica. A ativação pós-falha destina-se a todo o dispositivo. Durante a ativação pós-falha, os dados de cloud para o dispositivo de origem forem alterados propriedade para que o dispositivo de destino.

Este artigo é apenas aplicável para as matrizes virtuais do StorSimple. Para efetuar a ativação pós-falha de um dispositivo da 8000 série, aceda a [dispositivo ativação pós-falha e recuperação após desastre do dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>O que é a ativação pós-falha do dispositivo e de recuperação do desastre?

Num cenário de recuperação (DR) após desastre, o dispositivo primário deixa de funcionar. Neste cenário, pode mover os dados de cloud associados ao dispositivo com falha para outro dispositivo. Pode utilizar o dispositivo primário como o *origem* e especifique outro dispositivo, como o *destino*. Esse processo é chamado para o *ativação pós-falha*. Durante a ativação pós-falha, todos os volumes ou compartilhamentos do dispositivo de origem alteram a propriedade e são transferidos para o dispositivo de destino. Nenhuma filtragem dos dados é permitida.

DR é modelada como uma restauração completa de dispositivo usando o calor com base no mapa disposição em camadas e de controlo. Um mapa térmico é definido ao atribuir um valor de gráfico para os dados com base na leitura e escrita padrões. Este gráfico mapear, em seguida, escalões os segmentos de dados de gráficos mais baixos para a cloud em primeiro lugar, mantendo os segmentos de dados de calor elevada (mais usado) no escalão local. Durante uma DR, o StorSimple utiliza o mapa gráfico para restaurar e realimentar os dados da cloud. O dispositivo obtém todas as volumes/partilhas na última cópia de segurança recente (conforme determinado internamente) e executa uma restauração a partir dessa cópia de segurança. A matriz virtual orquestra todo o processo de DR.

> [!IMPORTANT]
> O dispositivo de origem é eliminado no fim da ativação pós-falha do dispositivo e, por conseguinte, não é suportada uma reativação pós-falha.
> 
> 

Recuperação após desastre é orquestrada por meio do recurso de ativação pós-falha do dispositivo e é iniciada a partir da **dispositivos** painel. Este painel organizará todos os dispositivos do StorSimple ligados ao seu serviço StorSimple Device Manager. Para cada dispositivo, pode ver o nome amigável, estado, capacidade aprovisionada e máxima, tipo e modelo.

## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para a ativação pós-falha do dispositivo

### <a name="prerequisites"></a>Pré-requisitos

Para uma ativação pós-falha do dispositivo, certifique-se de que os seguintes pré-requisitos são cumpridos:

* O dispositivo de origem tem de estar num **Deactivated** estado.
* O dispositivo de destino tem de ser apresentado como **preparado para configurar a** no portal do Azure. Aprovisione uma matriz de virtual de destino da capacidade igual ou superior. Utilize a IU da web local para configurar e registar com êxito a matriz virtual de destino.
  
  > [!IMPORTANT]
  > Não tente configurar o dispositivo virtual registado através do serviço. Nenhuma configuração de dispositivo deve ser efetuada através do serviço.
  > 
  > 
* O dispositivo de destino não pode ter o mesmo nome que o dispositivo de origem.
* O dispositivo de origem e de destino tem de ser do mesmo tipo. Só pode efetuar a ativação pós-falha de uma matriz virtual configurada como um servidor de ficheiros para outro servidor de ficheiros. O mesmo é verdadeiro para um servidor de iSCSI.
* Para um servidor de ficheiros de DR, recomendamos que o dispositivo de destino se associe ao mesmo domínio como origem. Esta configuração garante que as permissões de partilha são resolvidas automaticamente. Apenas a ativação pós-falha para um dispositivo de destino no mesmo domínio.
* Os dispositivos de destino disponíveis para a DR são dispositivos que tenham a mesma capacidade ou capacidade superior em comparação com o dispositivo de origem. Os dispositivos que estão ligados ao seu serviço, mas que não cumprem os critérios de espaço suficiente não estão disponíveis como dispositivos de destino.

### <a name="other-considerations"></a>Outras considerações

* Para uma ativação pós-falha planeada 
  
  * Recomendamos que obter todos os volumes ou compartilhamentos do dispositivo de origem offline.
  * Recomendamos que faça uma cópia de segurança do dispositivo e, em seguida, continuar com a ativação pós-falha para minimizar a perda de dados. 
* Para uma ativação pós-falha não planeada, o dispositivo utiliza a cópia de segurança mais recente para restaurar os dados.

### <a name="device-failover-prechecks"></a>Prechecks de ativação pós-falha do dispositivo

Antes de começa a DR, o dispositivo efetua prechecks. Estas verificações ajudam a garantir que não ocorrerem erros quando começa a DR. Os prechecks incluem:

* A validar a conta de armazenamento.
* A verificar a conectividade de cloud para o Azure.
* A verificar o espaço disponível no dispositivo de destino.
* A verificar se tem um volume de dispositivo de origem de servidor de iSCSI
  
  * nomes válidos do ACR.
  * IQN válido (a não exceder 220 carateres).
  * senhas CHAP válidas (12 a 16 carateres).

Se qualquer um dos anterior prechecks falhar, não é possível continuar com o DR. Resolver esses problemas e, em seguida, repita o DR.

Após a DR é concluído com êxito, a propriedade dos dados na cloud no dispositivo de origem é transferida para o dispositivo de destino. O dispositivo de origem já não é, em seguida, está disponível no portal. Acesso a todas as volumes/partilhas no dispositivo de origem é bloqueado e o dispositivo de destino é ativo.

> [!IMPORTANT]
> Apesar do dispositivo já não está disponível, a máquina virtual que aprovisionou no sistema host ainda está a consumir recursos. Depois do DR foi concluído com êxito, pode eliminar esta máquina virtual do sistema anfitrião.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Ativação pós-falha para uma matriz virtual

Recomendamos que Aprovisiona, configura e registar outra matriz Virtual do StorSimple com o seu serviço StorSimple Device Manager antes de executar este procedimento.

> [!IMPORTANT]
> 
> * Não é possível efetuar a ativação pós-falha de um dispositivo da série 8000 do StorSimple para um dispositivo virtual 1200.
> * Pode efetuar a ativação pós-falha de um dispositivo virtual Federal Information Processing Standard (FIPS) ativada para outro dispositivo FIPS ativada ou para um dispositivo não FIPS implementado no portal do Governo.


Execute os seguintes passos para restaurar o dispositivo para um dispositivo virtual do StorSimple de destino.

1. Aprovisionar e configurar um dispositivo de destino que cumpra os [pré-requisitos para a ativação pós-falha do dispositivo](#prerequisites). Conclua a configuração do dispositivo através da IU da web local e registá-lo ao seu serviço StorSimple Device Manager. Se criar um servidor de ficheiros, vá para o passo 1 [configurado como servidor de ficheiros](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device). Se criar um servidor de iSCSI, vá para o passo 1 [configurado como servidor de iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Tire volumes/partilhas offline no anfitrião. Para colocar as volumes/partilhas offline, consulte as instruções específicas do sistema operacional para o anfitrião. Se não já offline, terá de efetuar todas as volumes/partilhas offline no dispositivo, fazendo o seguinte.
   
    1. Aceda a **dispositivos** painel e selecione o seu dispositivo.
   
    2. Aceda a **definições > Gerir > partilhas** (ou **definições > Gerir > Volumes**). 
   
    3. Selecione um partilha/volume, clique com botão direito e selecione **colocar offline**. 
   
    4. Quando lhe for pedida confirmação, verificar **compreendo o impacto de colocar esta partilha offline.** 
   
    5. Clique em **colocar offline**.

3. No seu serviço StorSimple Device Manager, aceda a **gestão > dispositivos**. Na **dispositivos** painel, selecione e clique no seu dispositivo de origem.

4. No seu **dashboard do dispositivo** painel, clique em **desativar**.

5. Na **desativar** painel, lhe for pedida a confirmação. Desativação de dispositivo é um *permanente* processo que não pode ser anulado. Também é lembrado tirar seus compartilhamentos/volumes offline no anfitrião. Escreva o nome do dispositivo para confirmar e clique em **desativar**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. A desativação é iniciado. Receberá uma notificação após a desativação é concluída com êxito.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na página de dispositivos, o estado do dispositivo agora será alterado para **Deactivated**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Na **dispositivos** painel, selecione e clique no dispositivo de origem desativado para a ativação pós-falha. 
9. Na **dashboard do dispositivo** painel, clique em **efetuar a ativação pós-falha**. 
10. Na **efetuar a ativação pós-falha do dispositivo** painel, faça o seguinte:
    
    1. O campo de dispositivo de origem é preenchido automaticamente. Tenha em atenção o tamanho total dos dados para o dispositivo de origem. O tamanho de dados deve ser inferior a capacidade disponível no dispositivo de destino. Reveja os detalhes associados ao dispositivo de origem como o nome do dispositivo, capacidade total e os nomes das partilhas de com ativação pós-falha.

    2. Na lista pendente de dispositivos disponíveis, selecione um **dispositivo de destino**. Apenas os dispositivos que tem capacidade suficiente são apresentados na lista pendente.

    3. Verifique se **compreendo que esta operação irá falhar com dados para o dispositivo de destino**. 

    4. Clique em **efetuar a ativação pós-falha**.
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Inicia uma tarefa de ativação pós-falha e receber a notificação. Aceda a **dispositivos > tarefas** para monitorizar a ativação pós-falha.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Na **tarefas** painel, verá uma tarefa de ativação pós-falha criada para o dispositivo de origem. Esta tarefa efetua as prechecks de DR.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Depois dos prechecks de DR com êxito, a tarefa de ativação pós-falha irá gerar tarefas de restauro para cada partilha/volume existente no seu dispositivo de origem.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Depois de concluída a ativação pós-falha, vá para o **dispositivos** painel.
    
    1. Selecione e clique no dispositivo StorSimple que foi utilizado como o dispositivo de destino para o processo de ativação pós-falha.
    2. Aceda a **definições > gestão > partilhas** (ou **Volumes** se o servidor iSCSI). Na **partilhas** painel, pode ver todas as partilhas (volumes) do dispositivo antigo.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Precisará [criar um alias de DNS](https://support.microsoft.com/kb/168322) para que todos os aplicativos que estão a tentar ligar, podem obter redirecionados para o novo dispositivo.

## <a name="errors-during-dr"></a>Erros durante a DR

**Falha de conectividade da cloud durante a DR**

Se a conectividade da cloud é interrompida DR é iniciado e antes do dispositivo restauro foi concluído, o DR irá falhar. Irá receber uma notificação de falha. O dispositivo de destino para a DR é marcado como *inutilizável.* Não é possível utilizar o mesmo dispositivo de destino para o DRs futuras.

**Não existem dispositivos de destino compatível**

Se os dispositivos de destino disponível não tem espaço suficiente, verá um erro para o efeito que não há nenhum dispositivo de destino compatível.

**Precheck falhas**

Se um dos prechecks não for satisfeito, em seguida, verá precheck falhas.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastres de continuidade de negócio (BCDR)

Um cenário de recuperação (BCDR) de desastres de continuidade de negócio ocorre quando todo o datacenter do Azure deixa de funcionar. Isso pode afetar o seu serviço StorSimple Device Manager e os dispositivos associados do StorSimple.

Se existirem dispositivos do StorSimple que foram registados imediatamente antes da ocorrência de um desastre, em seguida, estes dispositivos do StorSimple poderão ter de ser eliminado. Após o desastre, pode recriar e configurar esses dispositivos.

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como [administrar a sua matriz Virtual do StorSimple com a IU web local](storsimple-ova-web-ui-admin.md).

