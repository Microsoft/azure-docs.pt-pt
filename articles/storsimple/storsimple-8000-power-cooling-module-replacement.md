---
title: Substituir um PCM no seu dispositivo da série StorSimple 8000 | Documentos da Microsoft
description: Explica como remover e substituir a energia e refrigeração módulo PCM () no dispositivo StorSimple
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 42561570e24aec5edd33248ef1738e53175e480e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60632510"
---
# <a name="replace-a-power-and-cooling-module-on-your-storsimple-device"></a>Substituir um energia e refrigeração módulo no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
A energia e refrigeração módulo (PCM) no seu dispositivo do Microsoft Azure StorSimple é composta por uma fonte de alimentação e refrigeração fãs que são controlados através do principal e as inclusões EBOD. Há apenas um modelo do PCM está certificado para cada bastidor. O bastidor principal está certificado para um PCM W 764 e a inclusão EBOD está certificada para um PCM W 580. Embora os PCMs para o bastidor principal e a inclusão EBOD sejam diferentes, o procedimento de substituição é idêntico.

Este tutorial explica como:

* Remover um PCM
* Instalar uma substituição PCM

> [!IMPORTANT]
> Antes de remover e substituir um PCM, reveja as informações de segurança na [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).


## <a name="before-you-replace-a-pcm"></a>Antes de substituir o um PCM
Tenha em atenção os seguintes problemas importantes antes de substituir o PCM:

* Se a fonte de alimentação do PCM falhar, deixe o módulo com falhas instalado, mas a remover o cabo de alimentação. A ventoinha irá continuar a receber power da inclusão e continuar a fornecer a refrigeração adequada. Se falhar a ventoinha, o PCM tem de ser substituído imediatamente.
* Antes de remover o PCM, desligue o poder do PCM, desativando o comutador principal (onde exista) ou removendo fisicamente o cabo de alimentação. Isso fornecerá um aviso ao seu sistema que um encerramento de energia é iminente.
* Certifique-se de que os outro PCM funcional para a operação de sistema contínuo antes de substituir o PCM com falhas. Um PCM com falhas têm de ser substituída por um PCM totalmente operacional logo que possível.
* Substituição do módulo PCM demora apenas alguns minutos a concluir, mas tem de ser concluído em 10 minutos da remoção do PCM com falha para impedir o sobreaquecimento.
* Tenha em atenção que os módulos de W PCM 764 substituição enviados a partir da fábrica não contêm o módulo de bateria de segurança. Terá de remover a bateria da sua PCM com falha e, em seguida, insira-o para o módulo de substituição antes de efetuar a substituição. Para obter mais informações, consulte como [remova e insira um módulo de bateria de segurança](storsimple-8000-battery-replacement.md).

## <a name="remove-a-pcm"></a>Remover um PCM
Siga estas instruções quando estiver pronto para remover um energia e refrigeração módulo PCM () a partir do seu dispositivo do Microsoft Azure StorSimple.

> [!NOTE]
> Antes de remover o PCM, certifique-se de que tem uma substituição correta (764 W para o bastidor principal) ou 580 W para a inclusão EBOD.

#### <a name="to-remove-a-pcm"></a>Para remover um PCM
1. No portal clássico do Azure, clique em **definições > monitorização > Estado de funcionamento do Hardware**. Verificar o estado dos componentes do PCM sob **componentes partilhados** para identificar qual PCM falhou:
   
   * Se uma fonte de alimentação no PCM 0 não, o estado dos **fonte de alimentação no PCM 0** ficará vermelho.
   * Se uma fonte de alimentação no PCM 1 falhou, o estado dos **fonte de alimentação no PCM 1** ficará vermelho.
   * Se a ventoinha no PCM 1 falhou, o estado de qualquer um **refrigeração 0 para PCM 0** ou **refrigeração 1 para PCM 0** ficará vermelho.
2. Localize o PCM com falha no verso do bastidor principal. Se estiver a executar um modelo 8600, identifique o bastidor principal ao observar o número de identificação de unidade de sistema mostrado no painel frontal LED de exibição. A predefinição é de ID de unidade apresentado na caixa primária **00**, ao passo que a predefinição é de ID de unidade apresentado na caixa EBOD **01**. O diagrama e tabela seguintes explicam o painel frontal da exibição LED.
   
    ![ID de sistema no painel frontal do OPS](./media/storsimple-power-cooling-module-replacement/IC740991.png)
   
     **Figura 1** painel de frente do dispositivo  
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |Botão de sem som |
   | 2 |Energia do sistema |
   | 3 |Falha do módulo |
   | 4 |Falhas de lógica |
   | 5 |Exibição de ID de unidade |
3. Os indicadores LED de monitorização na minha o bastidor principal também podem ser utilizado para identificar o PCM com falhas. Consulte o seguinte diagrama e tabela para compreender como utilizar os LEDs para localizar o PCM com falhas. Por exemplo, se o LED correspondente para o **ventoinha falhar** é ativada, a ventoinha falhou. Da mesma forma, se o LED correspondente **AC falhar** é ativada, a fonte de alimentação falhou. 
   
    ![Backplane de dispositivo PCM indicadores LED de monitorização](./media/storsimple-power-cooling-module-replacement/IC740992.png)
   
     **Figura 2** Back do PCM com indicadores LED
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |Falha de energia de AC |
   | 2 |Falha na ventoinha |
   | 3 |Falhas de bateria |
   | 4 |PCM OK |
   | 5 |Falha de energia do controlador de domínio |
   | 6 |Bom estado de funcionamento de bateria |
4. Consulte o diagrama seguinte de fundo do dispositivo StorSimple para localizar o módulo PCM com falha. PCM 0 é à esquerda e PCM 1 está no lado direito. A seguinte tabela explica os módulos.
   
     ![Backplane dos módulos de bastidor principal do dispositivo](./media/storsimple-power-cooling-module-replacement/IC740994.png)
   
     **Figura 3** inverso de dispositivo com módulos de plug-ins 
   
   | Label | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
5. Desativar o PCM com falhas e desligue o cabo de alimentação de energia. Agora pode remover o PCM.
6. Entender o bloqueio temporário e o lado do identificador PCM entre o thumb e forefinger e compactá-las em conjunto para abrir o identificador.
   
    ![Identificador do PCM de abertura](./media/storsimple-power-cooling-module-replacement/IC740995.png)
   
    **Figura 4** abrindo o identificador do PCM
7. Grip o identificador e remova o PCM.
   
    ![Remover dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740996.png)
   
    **Figura 5** remover o PCM

## <a name="install-a-replacement-pcm"></a>Instalar uma substituição PCM
Siga estas instruções para instalar um PCM no dispositivo StorSimple. Certifique-se de que introduziu o módulo de bateria de segurança antes de instalar a substituição PCM (aplica-se a 764 PCMs W apenas). Para obter mais informações, consulte como [remova e insira um módulo de bateria de segurança](storsimple-8000-battery-replacement.md).

#### <a name="to-install-a-pcm"></a>Para instalar um PCM
1. Certifique-se de que tem a substituição correta PCM para este bastidor. O bastidor principal tem um PCM W 764 e a inclusão EBOD tem um PCM W 580. Não deve tentar utilizar o PCM W 580 no bastidor principal ou o PCM W 764 no bastidor EBOD. A imagem seguinte mostra onde identificar estas informações sobre a etiqueta que é afixação de PCM.
   
    ![Etiqueta do PCM de dispositivo](./media/storsimple-power-cooling-module-replacement/IC740973.png)
   
    **Figura 6** etiqueta PCM
2. Verificar a existência de danos ao bastidor, prestando especial atenção aos conectores. 
   
   > [!NOTE]
   > **Não instale o módulo se, estão dobrados qualquer pins de conector.**
   > 
   > 
3. Com o identificador do PCM na posição abrir, deslize o módulo para o bastidor.
   
    ![Instalação de dispositivo PCM](./media/storsimple-power-cooling-module-replacement/IC740975.png)
   
    **Figura 7** instalar o PCM
4. Feche manualmente o identificador PCM. Deve ouvir um clique como trava o identificador é ativado.
   
   > [!NOTE]
   > Para garantir que os pins de conector tem envolvidos, pode moderadamente tug no identificador sem a liberação do bloqueio temporário. Se o PCM surge, isso implica que o bloqueio temporário foi fechado antes dos conectores envolvidos.
   
5. Ligue os cabos de energia para a fonte de energia e o PCM.
6. Proteja a sobrecarga bales alívio.
7. Ative o PCM.
8. Certifique-se de que a substituição foi concluída com êxito: no portal do Azure do seu serviço StorSimple Device Manager, navegue para o seu dispositivo e, em seguida, para **definições > monitorização > Estado de funcionamento do Hardware**. Sob o **componentes partilhados**, o estado do PCM deve ser verde.
   
   > [!NOTE]
   > Pode demorar alguns minutos para que a substituição PCM completamente inicializar.

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

