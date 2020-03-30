---
title: Falha e recuperação de desastres para StorSimple Virtual Array
description: Saiba mais sobre como falhar com o seu StorSimple Virtual Array.
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
ms.openlocfilehash: 464fa05f658dd6e6e25d79f8840ceeb939383149
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467220"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Recuperação após desastre e ativação pós-falha do dispositivo para a Matriz Virtual do StorSimple através do portal do Azure

## <a name="overview"></a>Descrição geral
Este artigo descreve a recuperação de desastres para o seu Microsoft Azure StorSimple Virtual Array, incluindo os passos detalhados para falhar em outra matriz virtual. Uma falha permite-lhe mover os seus dados de um dispositivo *de origem* no datacenter para um *dispositivo-alvo.* O dispositivo-alvo pode estar localizado na mesma localização geográfica ou numa localização geográfica diferente. A falha do dispositivo é para todo o dispositivo. Durante o failover, os dados da nuvem para o dispositivo de origem alteram a propriedade para a do dispositivo alvo.

Este artigo é aplicável apenas a StorSimple Virtual Arrays. Para falhar num dispositivo da série 8000, vá ao [Dispositivo de failover e à recuperação de desastres do seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>O que é a recuperação de desastres e a falha do dispositivo?

Num cenário de recuperação de desastres (DR), o dispositivo primário para de funcionar. Neste cenário, pode mover os dados da nuvem associados ao dispositivo falhado para outro dispositivo. Pode utilizar o dispositivo primário como *fonte* e especificar outro dispositivo como *alvo*. Este processo é referido como o *fracasso.* Durante a failover, todos os volumes ou ações do dispositivo de origem mudam de propriedade e são transferidos para o dispositivo-alvo. Não é permitida a filtragem dos dados.

O DR é modelado como um dispositivo completo restaurar usando o tiering e rastreio baseados em mapas de calor. Um mapa de calor é definido atribuindo um valor de calor aos dados baseados em padrões de leitura e escrita. Este mapa de calor então nimetou os pedaços de dados de calor mais baixos para a nuvem primeiro, mantendo os pedaços de dados de calor elevado (mais utilizados) no nível local. Durante um DR, o StorSimple utiliza o mapa de calor para restaurar e reidratar os dados da nuvem. O dispositivo adquire todos os volumes/ações da última cópia de segurança recente (conforme determinado internamente) e realiza uma restauração a partir dessa cópia de segurança. A matriz virtual orquestra todo o processo de DR.

> [!IMPORTANT]
> O dispositivo de origem é eliminado no final do failover do dispositivo e, portanto, não é suportado um failback.
> 
> 

A recuperação de desastres é orquestrada através da funcionalidade failover do dispositivo e é iniciada a partir da lâmina **dispositivos.** Esta lâmina tabula todos os dispositivos StorSimple ligados ao seu serviço StorSimple Device Manager. Para cada dispositivo, pode ver o nome, estado, capacidade e capacidade máxima, tipo e modelo amigáveis.

## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para a ativação pós-falha do dispositivo

### <a name="prerequisites"></a>Pré-requisitos

Para uma falha no dispositivo, certifique-se de que os seguintes pré-requisitos estão satisfeitos:

* O dispositivo de origem tem de estar num estado **desativado.**
* O dispositivo-alvo tem de aparecer como **Pronto para ser montado** no portal Azure. Fornecer um conjunto virtual-alvo da mesma capacidade ou superior. Utilize a UI web local para configurar e registar com sucesso a matriz virtual alvo.
  
  > [!IMPORTANT]
  > Não tente configurar o dispositivo virtual registado através do serviço. Nenhuma configuração do dispositivo deve ser realizada através do serviço.
  > 
  > 
* O dispositivo-alvo não pode ter o mesmo nome que o dispositivo de origem.
* A fonte e o dispositivo alvo têm de ser do mesmo tipo. Só é possível falhar sobre um conjunto virtual configurado como servidor de ficheiros para outro servidor de ficheiros. O mesmo acontece com um servidor iSCSI.
* Para um servidor de ficheiros DR, recomendamos que se junte ao dispositivo-alvo no mesmo domínio que a fonte. Esta configuração garante que as permissões de partilha são automaticamente resolvidas. Apenas é suportada a falha de um dispositivo-alvo no mesmo domínio.
* Os dispositivos-alvo disponíveis para DR são dispositivos com a mesma ou maior capacidade em comparação com o dispositivo de origem. Os dispositivos que estão ligados ao seu serviço mas que não cumprem os critérios de espaço suficiente não estão disponíveis como dispositivos-alvo.

### <a name="other-considerations"></a>Outras considerações

* Para uma falha planeada:
  
  * Recomendamos que leve todos os volumes ou partilhas do dispositivo de origem offline.
  * Recomendamos que pegue uma cópia de segurança do dispositivo e, em seguida, proceda à falha para minimizar a perda de dados.
* Para uma falha não planeada, o dispositivo utiliza a cópia de segurança mais recente para restaurar os dados.

### <a name="device-failover-prechecks"></a>Falha no dispositivo pré-verificação

Antes de o DR começar, o dispositivo realiza pré-verificações. Estas verificações ajudam a garantir que não ocorram erros quando a DR começa. As pré-verificações incluem:

* Validando a conta de armazenamento.
* Verificando a conectividade da nuvem com O Azure.
* Verificando o espaço disponível no dispositivo alvo.
* Verificar se um volume de dispositivo de origem do servidor iSCSI tem
  
  * nomes ACR válidos.
  * IQN válido (não superior a 220 caracteres).
  * senhas chap válidas (12-16 caracteres de comprimento).

Se algum dos pré-controlos anteriores falhar, não pode prosseguir com o DR. Resolva estes problemas e, em seguida, tente novamente dr.

Após a conclusão com sucesso do DR, a propriedade dos dados da nuvem no dispositivo de origem é transferida para o dispositivo-alvo. O dispositivo de origem já não está disponível no portal. O acesso a todos os volumes/partilhas do dispositivo de origem está bloqueado e o dispositivo-alvo torna-se ativo.

> [!IMPORTANT]
> Embora o dispositivo já não esteja disponível, a máquina virtual que disponibilizou no sistema de acolhimento ainda está a consumir recursos. Uma vez concluído com sucesso o DR, pode eliminar esta máquina virtual do seu sistema de hospedagem.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Falhar numa matriz virtual

Recomendamos que o proponha, configure e registe outro StorSimple Virtual Array com o seu serviço StorSimple Device Manager antes de executar este procedimento.

> [!IMPORTANT]
> 
> * Não é possível falhar de um dispositivo da série StorSimple 8000 para um dispositivo virtual de 1200.
> * Pode falhar a partir de um dispositivo virtual de processamento de informação federal (FIPS) ativado para outro dispositivo fips ativado ou para um dispositivo não-FIPS implantado no portal do Governo.


Execute os seguintes passos para restaurar o dispositivo num dispositivo virtual StorSimple alvo.

1. Fornecer e configurar um dispositivo-alvo que satisfaça os [pré-requisitos para](#prerequisites)a falha do dispositivo . Complete a configuração do dispositivo através da UI web local e registe-o no seu serviço StorSimple Device Manager. Se criar um servidor de ficheiros, vá ao passo 1 da [configuração como servidor](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)de ficheiros . Se criar um servidor iSCSI, vá ao passo 1 da [configuração como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Leve volumes/partilhas offline no hospedeiro. Para desativar os volumes/partilhas, consulte as instruções específicas do sistema operativo para o hospedeiro. Se ainda não estiver offline, é necessário desligar todos os volumes/partilhas do dispositivo, fazendo o seguinte.
   
    1. Vá à lâmina **do Dispositivo e** selecione o seu dispositivo.
   
    2. Vá a **Definições > Gerir > Partilhas** (ou **Configurações > Gerir volumes de >).** 
   
    3. Selecione uma partilha/volume, clique à direita e selecione **Desligar**. 
   
    4. Quando solicitado para confirmação, verifique **se entendo o impacto de retirar esta parte offline.** 
   
    5. Clique **Em desligar.**

3. No seu serviço StorSimple Device Manager, vá à **Management > Devices**. Na lâmina **dos Dispositivos,** selecione e clique no seu dispositivo de origem.

4. Na lâmina do **painel do dispositivo,** clique em **Desativar**.

5. Na lâmina **Deactivate,** é solicitado para confirmação. A desativação do dispositivo é um processo *permanente* que não pode ser desfeito. Também é lembrado para desligar as suas ações/volumes no hospedeiro. Digite o nome do dispositivo para confirmar e clique em **Desativar**.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. A desativação começa. Receberá uma notificação após a desativação ser concluída com sucesso.
   
    ![](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na página dispositivos, o estado do dispositivo passará a ser **desativado**.
    ![](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Na lâmina **dos Dispositivos,** selecione e clique no dispositivo de origem desativado para obter falhas. 
9. Na lâmina do painel do **dispositivo,** clique **em Falhar em cima**. 
10. Na **lâmina fail sobre** o dispositivo, faça o seguinte:
    
    1. O campo do dispositivo de origem é automaticamente povoado. Note o tamanho total dos dados para o dispositivo de origem. O tamanho dos dados deve ser inferior à capacidade disponível no dispositivo-alvo. Reveja os detalhes associados ao dispositivo de origem, tais como o nome do dispositivo, a capacidade total e os nomes das ações que são reprovadas.

    2. A partir da lista de desativação dos dispositivos disponíveis, escolha um **dispositivo Target**. Apenas os dispositivos com capacidade suficiente são apresentados na lista de abandono.

    3. Verifique se **entendo que esta operação falhará sobre os dados do dispositivo-alvo**. 

    4. Clique **em Falhar mais .**
    
        ![](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Inicia-se um trabalho de failover e recebe uma notificação. Vá ao **Devices > Jobs** para monitorizar a falha.
    
     ![](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Na lâmina **Jobs,** vê-se um trabalho de falha criado para o dispositivo de origem. Este trabalho realiza os pré-controlos do DR.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Após o sucesso dos pré-controlos dr, o trabalho de failover irá gerar trabalhos de restauro para cada parte/volume que existe no seu dispositivo de origem.
    
    ![](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Depois de concluída a falha, vá à lâmina **do Dispositivo.**
    
    1. Selecione e clique no dispositivo StorSimple que foi usado como o dispositivo-alvo para o processo de failover.
    2. Vá a **Definições > Gestão > Ações** (ou **Volumes** se o servidor iSCSI). Na lâmina **Partilha,** pode ver todas as ações (volumes) do dispositivo antigo.
        ![](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Terá de [criar um pseudónimo DNS](https://support.microsoft.com/kb/168322) para que todas as aplicações que estão a tentar ligar possam ser redirecionadas para o novo dispositivo.

## <a name="errors-during-dr"></a>Erros durante o DR

**Falha na conectividade da nuvem durante o DR**

Se a conectividade da nuvem for interrompida após o início do DR e antes de o dispositivo restaurar, o DR falhará. Recebe uma notificação de falha. O dispositivo-alvo para DR é marcado como *inutilizável.* Não é possível utilizar o mesmo dispositivo-alvo para futuros DRs.

**Sem dispositivos-alvo compatíveis**

Se os dispositivos-alvo disponíveis não tiverem espaço suficiente, verá um erro no sentido de não existirem dispositivos-alvo compatíveis.

**Pré-verificação falhas**

Se um dos pré-controlos não estiver satisfeito, então verá falhas de pré-verificação.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastres de continuidade de negócios (BCDR)

Um cenário de recuperação de desastres de continuidade de negócios (BCDR) ocorre quando todo o datacenter Azure deixa de funcionar. Isto pode afetar o seu serviço StorSimple Device Manager e os dispositivos StorSimple associados.

Se existem dispositivos StorSimple que foram registados pouco antes de ocorrer um desastre, então estes dispositivos StorSimple podem ter de ser eliminados. Após o desastre, pode recriar e configurar esses dispositivos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [administrar o seu StorSimple Virtual Array utilizando a Web UI local](storsimple-ova-web-ui-admin.md).

