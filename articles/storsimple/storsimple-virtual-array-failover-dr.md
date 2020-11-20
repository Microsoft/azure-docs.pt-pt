---
title: Falha e recuperação de desastres para storSimple Virtual Array
description: Saiba mais sobre a recuperação de desastres para o seu Microsoft Azure StorSimple Virtual Array, incluindo os passos detalhados para falhar em outro conjunto virtual.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 3c1f9c62-af57-4634-a0d8-435522d969aa
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/27/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 509a3ac383cebd91821e9c4b872c253ab3b0a947
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94956199"
---
# <a name="disaster-recovery-and-device-failover-for-your-storsimple-virtual-array-via-azure-portal"></a>Recuperação após desastre e ativação pós-falha do dispositivo para a Matriz Virtual do StorSimple através do portal do Azure

## <a name="overview"></a>Descrição geral
Este artigo descreve a recuperação de desastres para o seu Microsoft Azure StorSimple Virtual Array, incluindo os passos detalhados para falhar em outra matriz virtual. Uma falha permite-lhe mover os seus dados de um dispositivo de *origem* no datacenter para um dispositivo *alvo.* O dispositivo-alvo pode estar localizado na mesma localização geográfica ou numa localização geográfica diferente. A falha do dispositivo é para todo o dispositivo. Durante o failover, os dados da nuvem do dispositivo de origem alteram a propriedade do dispositivo alvo.

Este artigo é aplicável apenas a Arrays Virtuais StorSimple. Para falhar mais de um dispositivo da série 8000, vá ao dispositivo de failover e à [recuperação de desastres do seu dispositivo StorSimple](./storsimple-8000-device-failover-disaster-recovery.md).

## <a name="what-is-disaster-recovery-and-device-failover"></a>O que é a recuperação de desastres e o fracasso do dispositivo?

Num cenário de recuperação de desastres (DR), o dispositivo primário deixa de funcionar. Neste cenário, é possível mover os dados da nuvem associados ao dispositivo falhado para outro dispositivo. Pode utilizar o dispositivo primário como *fonte* e especificar outro dispositivo como *alvo*. Este processo é referido como o *fracasso*. Durante o failover, todos os volumes ou ações do dispositivo de origem mudam de propriedade e são transferidos para o dispositivo alvo. Não é permitida a filtragem dos dados.

O DR é modelado como um dispositivo completo restaurado usando o tiering e o rastreio baseados no mapa de calor. Um mapa de calor é definido atribuindo um valor de calor aos dados com base em padrões de leitura e escrita. Este mapa de calor, em seguida, tiers os mais baixos pedaços de dados de calor para a nuvem primeiro, mantendo os pedaços de dados de calor alto (mais usados) no nível local. Durante um DR, o StorSimple utiliza o mapa de calor para restaurar e reidratar os dados da nuvem. O dispositivo recolhe todos os volumes/ações da última cópia de segurança recente (conforme determinado internamente) e executa uma restauração a partir dessa cópia de segurança. A matriz virtual orquestra todo o processo dr.

> [!IMPORTANT]
> O dispositivo de origem é eliminado no final da falha do dispositivo e, por isso, não é suportado um failback.
> 
> 

A recuperação de desastres é orquestrada através da função de failover do dispositivo e é iniciada a partir da lâmina **dispositivos.** Esta lâmina tabula todos os dispositivos StorSimple ligados ao seu serviço StorSimple Device Manager. Para cada dispositivo, pode ver o nome, estado, a provisionamento e a capacidade máxima, tipo e modelo.

## <a name="prerequisites-for-device-failover"></a>Pré-requisitos para a ativação pós-falha do dispositivo

### <a name="prerequisites"></a>Pré-requisitos

Para uma falha do dispositivo, certifique-se de que os seguintes pré-requisitos estão satisfeitos:

* O dispositivo de origem precisa de estar num estado **desativado.**
* O dispositivo alvo tem de aparecer como **Pronto para ser montado** no portal Azure. Provisionar um conjunto virtual de alvo da mesma capacidade ou maior. Utilize a UI web local para configurar e registar com sucesso o array virtual alvo.
  
  > [!IMPORTANT]
  > Não tente configurar o dispositivo virtual registado através do serviço. Nenhuma configuração do dispositivo deve ser realizada através do serviço.
  > 
  > 
* O dispositivo alvo não pode ter o mesmo nome que o dispositivo de origem.
* A fonte e o dispositivo alvo têm de ser do mesmo tipo. Só pode falhar sobre uma matriz virtual configurada como um servidor de ficheiros para outro servidor de ficheiros. O mesmo acontece com um servidor iSCSI.
* Para um servidor de ficheiros DR, recomendamos que se junte o dispositivo alvo ao mesmo domínio que a fonte. Esta configuração garante que as permissões de partilha são automaticamente resolvidas. Apenas a falha de um dispositivo alvo no mesmo domínio é suportada.
* Os dispositivos-alvo disponíveis para DR são dispositivos que têm a mesma capacidade ou maior capacidade em comparação com o dispositivo de origem. Os dispositivos que estão ligados ao seu serviço mas que não satisfaçam os critérios de espaço suficiente não estão disponíveis como dispositivos-alvo.

### <a name="other-considerations"></a>Outras considerações

* Para uma falha planeada:
  
  * Recomendamos que leve todos os volumes ou partilhas do dispositivo de origem offline.
  * Recomendamos que pegue uma cópia de segurança do dispositivo e, em seguida, proceda com a falha para minimizar a perda de dados.
* Para uma falha não planeada, o dispositivo utiliza a cópia de segurança mais recente para restaurar os dados.

### <a name="device-failover-prechecks"></a>Falha no dispositivo pré-verificação

Antes do início do DR, o dispositivo executa pré-verificações. Estas verificações ajudam a garantir que não ocorram erros quando o DR começa. Os pré-verificações incluem:

* Validação da conta de armazenamento.
* Verificando a conectividade na nuvem com O Azure.
* Verificando o espaço disponível no dispositivo alvo.
* Verificar se o volume de um dispositivo de origem do servidor iSCSI tem
  
  * nomes ACR válidos.
  * IQN válido (não superior a 220 caracteres).
  * senhas CHAP válidas (12-16 caracteres de comprimento).

Se algum dos pré-controlos anteriores falhar, não pode prosseguir com o DR. Resolva estas questões e, em seguida, recandiduça dr.

Após a conclusão do DR com sucesso, a propriedade dos dados da nuvem no dispositivo de origem é transferida para o dispositivo alvo. O dispositivo de origem já não está disponível no portal. O acesso a todos os volumes/partilhas do dispositivo de origem está bloqueado e o dispositivo-alvo fica ativo.

> [!IMPORTANT]
> Apesar de o dispositivo já não se encontra disponível, a máquina virtual que atei no sistema de acolhimento ainda está a consumir recursos. Uma vez que o DR esteja concluído com sucesso, pode eliminar esta máquina virtual do seu sistema de anfitrião.
> 
> 

## <a name="fail-over-to-a-virtual-array"></a>Falha em uma matriz virtual

Recomendamos que provisões, configurar e registar outro StorSimple Virtual Array com o seu serviço StorSimple Device Manager antes de executar este procedimento.

> [!IMPORTANT]
> 
> * Não é possível falhar de um dispositivo da série StorSimple 8000 para um dispositivo virtual de 1200.
> * Pode falhar de uma Norma Federal de Processamento de Informação (FIPS) ativada por dispositivo virtual para outro dispositivo fips ou para um dispositivo não-FIPS implantado no portal do Governo.


Execute os seguintes passos para restaurar o dispositivo num dispositivo virtual StorSimple alvo.

1. Provisão e configuração de um dispositivo-alvo que satisfaça os [requisitos pré-requisitos para a falha do dispositivo](#prerequisites). Preencha a configuração do dispositivo através da UI web local e registe-a no seu serviço StorSimple Device Manager. Se criar um servidor de ficheiros, vá para o passo 1 da [configuração como servidor](storsimple-virtual-array-deploy3-fs-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device)de ficheiros . Se criar um servidor iSCSI, vá ao passo 1 da [configuração como servidor iSCSI](storsimple-virtual-array-deploy3-iscsi-setup.md#step-1-complete-the-local-web-ui-setup-and-register-your-device).

2. Leve volumes/ações offline no anfitrião. Para desligar os volumes/ações, consulte as instruções específicas do sistema operativo para o anfitrião. Se ainda não estiver offline, tem de tirar todos os volumes/ações offline do dispositivo fazendo o seguinte.
   
    1. Vá à lâmina **devices** e selecione o seu dispositivo.
   
    2. Vá a **Configurações > Gerir > ações** (ou **Configurações > Gerir volumes >**). 
   
    3. Selecione uma partilha/volume, clique à direita e selecione **'Desligar offline'.** 
   
    4. Quando solicitado para confirmação, verifique **se entendo o impacto de tirar esta parte offline.** 
   
    5. Clique **em Tirar offline**.

3. No seu serviço StorSimple Device Manager, vá a **Management > Devices**. Na lâmina **dispositivos,** selecione e clique no seu dispositivo de origem.

4. Na lâmina **do painel de instrumentos do dispositivo,** clique em **Desativar**.

5. Na lâmina **desativada,** é solicitado a confirmação. A desativação do dispositivo é um processo *permanente* que não pode ser desfeito. Também é lembrado para tirar as suas ações/volumes offline no anfitrião. Digite o nome do dispositivo para confirmar e clique em **Desativar**.
   
    ![Screenshot da lâmina desativada. A caixa de nome do dispositivo é preenchida e o botão Desativado é realçado.](./media/storsimple-virtual-array-failover-dr/failover1.png)
6. A desativação começa. Receberá uma notificação após a desativação ser concluída com sucesso.
   
    ![Screenshot de uma barra de progresso indicando que o dispositivo está a ser desativado.](./media/storsimple-virtual-array-failover-dr/failover2.png)
7. Na página Dispositivos, o estado do dispositivo passará a ser alterado para **Desativado**.
    ![Screenshot da página dispositivos. As propriedades do dispositivo desativado são apresentadas, incluindo o estado, que está listado como Desativado.](./media/storsimple-virtual-array-failover-dr/failover3.png)
8. Na lâmina **dispositivos,** selecione e clique no dispositivo de origem desativado para falhar. 
9. Na lâmina do **painel de instrumentos** do dispositivo, clique **em 'Falha'.** 
10. Na lâmina do **dispositivo 'Falha' sobre** o dispositivo, faça o seguinte:
    
    1. O campo do dispositivo de origem é automaticamente povoado. Note o tamanho total dos dados para o dispositivo de origem. O tamanho dos dados deve ser inferior à capacidade disponível no dispositivo-alvo. Reveja os detalhes associados ao dispositivo de origem, como o nome do dispositivo, a capacidade total e os nomes das ações que são chumbadas.

    2. A partir da lista de dispositivos disponíveis, escolha um **dispositivo Target**. Apenas os dispositivos com capacidade suficiente são apresentados na lista de abandono.

    3. Verifique se **entendo que esta operação falhará sobre os dados do dispositivo-alvo.** 

    4. Clique **em 'Falhar' mais**.
    
        ![Screenshot da lâmina do dispositivo Fail over, com o dispositivo de origem e alvo preenchido, a opção verificada e o botão 'Falha' sobre.](./media/storsimple-virtual-array-failover-dr/failover4.png)
11. Inicia-se um trabalho de represo e recebes uma notificação. Vá a **Dispositivos > Jobs** para monitorizar a falha.
    
     ![Screenshot de uma barra de progresso indicando que o dispositivo está a falhar.](./media/storsimple-virtual-array-failover-dr/failover5.png)
12. Na lâmina **Jobs,** vê-se um trabalho de failover criado para o dispositivo de origem. Este trabalho realiza o pré-checks DR.
    
    ![Screenshot mostrando que um trabalho de failover começou com sucesso.](./media/storsimple-virtual-array-failover-dr/failover6.png)
    
     Após o sucesso dos pré-testes dr, o trabalho de failover irá criar empregos de restauração para cada partilha/volume que existe no seu dispositivo de origem.
    
    ![Screenshot mostrando os detalhes do trabalho de failover, como o estado, o dispositivo e a duração.](./media/storsimple-virtual-array-failover-dr/failover7.png)
13. Depois de concluída a falha, aceda à lâmina **dispositivos.**
    
    1. Selecione e clique no dispositivo StorSimple que foi usado como o dispositivo alvo para o processo de falha.
    2. Aceda a **Definições > Management > Shares** (ou **Volumes** se o servidor iSCSI). Na lâmina **Shares,** pode visualizar todas as ações (volumes) do dispositivo antigo.
        ![Screenshot da lâmina dos dispositivos. O dispositivo alvo está listado com um estado de Online.](./media/storsimple-virtual-array-failover-dr/failover9.png)
14. Será necessário [criar um pseudónimo DNS](https://support.microsoft.com/kb/168322) para que todas as aplicações que estão a tentar ligar possam ser redirecionadas para o novo dispositivo.

## <a name="errors-during-dr"></a>Erros durante o DR

**Interrupção da conectividade na nuvem durante o DR**

Se a conectividade da nuvem for interrompida após o início do DR e antes de o dispositivo restaurar, o DR falhará. Recebe uma notificação de falha. O dispositivo-alvo para DR está marcado como *inutilizável.* Não é possível utilizar o mesmo dispositivo-alvo para futuras DRs.

**Sem dispositivos-alvo compatíveis**

Se os dispositivos-alvo disponíveis não tiverem espaço suficiente, verá um erro no sentido de não existirem dispositivos-alvo compatíveis.

**Falhas de pré-verificação**

Se um dos pré-verificações não estiver satisfeito, então vê falhas de pré-verificação.

## <a name="business-continuity-disaster-recovery-bcdr"></a>Recuperação de desastres de continuidade do negócio (BCDR)

Um cenário de recuperação de desastres de continuidade do negócio (BCDR) ocorre quando todo o datacenter Azure deixa de funcionar. Isto pode afetar o seu serviço StorSimple Device Manager e os dispositivos StorSimple associados.

Se existirem dispositivos StorSimple que foram registados pouco antes de ocorrer uma catástrofe, então estes dispositivos StorSimple podem ter de ser eliminados. Depois do desastre, podes recriar e configurar esses dispositivos.

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre como [administrar o seu StorSimple Virtual Array utilizando a UI web local.](storsimple-ova-web-ui-admin.md)