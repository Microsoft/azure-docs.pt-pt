---
title: Substituir um controlador de dispositivo do StorSimple 8000 series | Documentos da Microsoft
description: Explica como remover e substituir um ou ambos os módulos de controlador no seu dispositivo da série StorSimple 8000.
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
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: dd2f6fcc9b2f5d716566e91e89487969613d1005
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61482875"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substituir um módulo de controlador do seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um ou ambos os módulos de controlador num dispositivo StorSimple. Ele também aborda a lógica subjacente para os cenários de substituição de controlador único e duplo.

> [!NOTE]
> Antes de realizar uma substituição de controlador, recomendamos que sempre atualizar o firmware de controlador para a versão mais recente.
> 
> Para evitar danos em seu dispositivo StorSimple, não ejete o controlador até que o LEDs estão a aparecer como um dos seguintes:
> 
> * Todas as luzes são OFF.
> * LED 3, ![ícone de marca de verificação verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), e ![ícone cruz vermelha](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) são piscar, e LED 0 e LED 7 são **ON**.


A tabela seguinte mostra os cenários de substituição do controlador suportada.

| Caso | Cenário de substituição | Procedimento aplicável |
|:--- |:--- |:--- |
| 1 |Um controlador esteja em estado de falha, outro controlador é bom estado de funcionamento e o Active Directory. |[Única substituição de controlador](#replace-a-single-controller), que descreve o [lógica por trás de uma substituição de único controlador](#single-controller-replacement-logic), bem como a [passos de substituição](#single-controller-replacement-steps). |
| 2 |Ambos os controladores falharam e requerem substituição. O chassis, discos e bastidor de disco estão em bom Estados. |[Substituição de controlador dupla](#replace-both-controllers), que descreve o [lógica por trás de uma substituição de controlador dupla](#dual-controller-replacement-logic), bem como a [passos de substituição](#dual-controller-replacement-steps). |
| 3 |Controladores do mesmo dispositivo ou de diferentes dispositivos são trocadas. O chassis, discos e as inclusões de disco estão em bom Estados. |Será apresentada uma mensagem de alerta de erro de correspondência de ranhura. |
| 4 |Um controlador está em falta e não de outro controlador. |[Substituição de controlador dupla](#replace-both-controllers), que descreve o [lógica por trás de uma substituição de controlador dupla](#dual-controller-replacement-logic), bem como a [passos de substituição](#dual-controller-replacement-steps). |
| 5 |Um ou ambos os controladores de tem falhado. Não é possível aceder ao dispositivo através da consola de série ou a comunicação remota do Windows PowerShell. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para um procedimento de substituição manual de controlador. |
| 6 |Os controladores de tem uma versão de compilação diferentes, que pode ser devido a:<ul><li>Controladores de tem uma versão de software diferentes.</li><li>Controladores de tem uma versão de firmware diferentes.</li></ul> |Se as versões de software do controlador forem diferentes, a lógica de substituição Deteta que e atualiza a versão de software no controlador de substituição.<br><br>Se as versões de firmware do controlador são diferentes e é a versão de firmware antigo **não** automaticamente é atualizável, uma mensagem de alerta será apresentado no portal do Azure. Deve procurar as atualizações e instalar as atualizações de firmware.</br></br>Se as versões de firmware do controlador são diferentes e a versão de firmware antiga é automaticamente é atualizável, a lógica de substituição de controlador irá detetá-lo e, depois do controlador é iniciado, o firmware será atualizado automaticamente. |

Tem de remover um módulo de controlador, se tiver falhado. Um ou ambos os módulos controlador podem falhar, o que pode resultar numa substituição de controlador único ou uma substituição de controlador dupla. Para os procedimentos de substituição e a lógica por trás delas, consulte o seguinte:

* [Replace a single controller](#replace-a-single-controller) (Substituir um controlador individual)
* [Substitua os dois controladores](#replace-both-controllers)
* [Remover um controlador](#remove-a-controller)
* [Inserir um controlador](#insert-a-controller)
* [Identificar o controlador ativo no seu dispositivo](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Antes de remover e substituir um controlador, reveja as informações de segurança na [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Substituir um único controlador
Quando um dos dois controladores do dispositivo do Microsoft Azure StorSimple falhou, está a funcionar incorretamente ou está em falta, terá de substituir o controlador de um único.

### <a name="single-controller-replacement-logic"></a>Lógica de substituição de controlador único
Na substituição de um único controlador, primeiro deve remover o controlador com falha. (O controlador restante no dispositivo é o controlador ativo.) Ao inserir o controlador de substituição, ocorrem as seguintes ações:

1. O controlador de substituição começa imediatamente a comunicar com o dispositivo StorSimple.
2. Um instantâneo do disco rígido virtual (VHD) para o controlador ativo é copiado no controlador de substituição.
3. O instantâneo é modificado para que quando o controlador de substituição é iniciado a partir deste VHD, ela será reconhecida como um controlador em modo de espera.
4. Quando as modificações estiverem concluídas, o controlador de substituição será iniciado como o controlador em modo de espera.
5. Quando os dois controladores estiverem a executar, o cluster estiver online.

### <a name="single-controller-replacement-steps"></a>Passos de substituição de controlador único
Conclua os seguintes passos, se um dos controladores no seu dispositivo do Microsoft Azure StorSimple falhar. (Outro controlador tem de ser ativa e em execução. Se os dois controladores não ou funcione incorretamente, aceda a [passos de substituição de controlador dupla](#dual-controller-replacement-steps).)

> [!NOTE]
> Pode demorar 30 – 45 minutos para o controlador reiniciar e recuperar completamente o procedimento de substituição de controlador único. O tempo total para o procedimento inteiro, incluindo a anexar os cabos, é cerca de 2 horas.


#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover um módulo de único controlador com falha
1. No portal do Azure, vá para o serviço StorSimple Device Manager, clique em **dispositivos**e, em seguida, clique no nome do dispositivo que pretende monitorizar.
2. Aceda a **Monitor > Estado de funcionamento do Hardware**. O estado do controlador 0 ou 1 do controlador deve ser vermelho, que indica uma falha.
   
   > [!NOTE]
   > O controlador com falha numa substituição de controlador única é sempre um controlador em modo de espera.
   
3. Utilize figura 1 e a tabela seguinte para localizar o módulo de controlador com falha.
   
    ![Backplane dos módulos de bastidor principal do dispositivo](./media/storsimple-controller-replacement/IC740994.png)
   
    **Figura 1** dispositivo Back do StorSimple
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
4. No controlador com falha, remova as portas de dados a todos os cabos de rede ligada. Se estiver a utilizar um modelo 8600, também remova os cabos SAS que ligar o controlador ao controlador de EBOD.
5. Siga os passos em [remover um controlador de](#remove-a-controller) para remover o controlador com falha.
6. Instale a substituição de fábrica a mesma ranhura a partir do qual foi removido do controlador com falha. Isto aciona a lógica de substituição de controlador único. Para obter mais informações, consulte [único lógica de substituição de controlador](#single-controller-replacement-logic).
7. Enquanto a lógica de substituição de controlador único progride em segundo plano, voltar a ligar os cabos. Tenha cuidado para ligar todos os cabos exatamente da mesma forma que eles foram conectados antes da substituição.
8. Depois de reinicia o controlador, verifique os **estado do controlador** e o **estado do Cluster** no portal do Azure para verificar se o controlador está de volta para um bom estado de funcionamento e está no modo de espera.

> [!NOTE]
> Se estiver a monitorizar o dispositivo através da consola de série, poderá ver vários reinícios enquanto está a recuperar o controlador do procedimento de substituição. Quando é apresentado o menu da consola de série, em seguida, sabe que a substituição está concluída. Se o menu não aparecer nas duas horas de iniciar a substituição de controlador, inicie [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> A iniciar a atualização 4, também pode utilizar o cmdlet `Get-HCSControllerReplacementStatus` na interface do Windows PowerShell do dispositivo para monitorizar o estado do processo de substituição de controlador.
> 

## <a name="replace-both-controllers"></a>Substitua os dois controladores
Quando os dois controladores do dispositivo do Microsoft Azure StorSimple falharam, estão a funcionar incorretamente ou estão em falta, terá de substituir os dois controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição de controlador dupla
Numa substituição de controlador dupla, remova primeiro os dois controladores com falha e, em seguida, inserir substituições. Quando os controladores de dois substituição são inseridos, ocorrem as seguintes ações:

1. O controlador de substituição na ranhura 0 verifica o seguinte:
   
   1. Ele está usando as versões atuais do firmware e software?
   2. É uma parte do cluster?
   3. É o controlador de mesmo nível em execução e está em cluster?
      
      Se nenhuma dessas condições for verdadeira, o controlador de procura para a mais recente cópia de segurança diária (localizado na **nonDOMstorage** na unidade S). O controlador copia o instantâneo mais recente do VHD da cópia de segurança.
2. O controlador na ranhura 0 utiliza o instantâneo para a imagem propriamente dita.
3. Enquanto isso, o controlador na ranhura 1 aguarda controlador 0 para concluir a criação de imagens e iniciar.
4. Depois de iniciado o controlador 0, o controlador 1 Deteta o cluster criado pelo controlador 0, que dispara a lógica de substituição de controlador único. Para obter mais informações, consulte [único lógica de substituição de controlador](#single-controller-replacement-logic).
5. Depois disso, os dois controladores serão executado e o cluster será colocada online.

> [!IMPORTANT]
> Após uma substituição de controlador dupla, após a configuração do dispositivo StorSimple, é essencial que obter um manual cópia de segurança do dispositivo. Cópias de segurança de configuração do dispositivo diárias não são acionadas até depois de ter decorrido 24 horas. Trabalhar com [Support da Microsoft](storsimple-8000-contact-microsoft-support.md) para tornar um manual cópia de segurança do seu dispositivo.


### <a name="dual-controller-replacement-steps"></a>Passos de substituição de controlador dupla
Este fluxo de trabalho é necessário quando ambos os controladores de dispositivo do Microsoft Azure StorSimple falharam. Isto pode ocorrer num datacenter no qual o sistema de refrigeração deixa de funcionar e, como resultado, os dois controladores falharem num curto período de tempo. Dependendo se o dispositivo StorSimple está ativado ou desativar e, se estiver a utilizar um 8600 ou um modelo 8100, um conjunto diferente de passos é necessário.

> [!IMPORTANT]
> Pode demorar 45 minutos para 1 hora para o controlador reiniciar e completamente recuperar a partir de um procedimento de substituição de controlador dupla. O tempo total para o procedimento inteiro, incluindo a anexar os cabos, é aproximadamente 2,5 horas.

#### <a name="to-replace-both-controller-modules"></a>Para substituir os dois módulos de controlador
1. Se o dispositivo estiver desativado, ignore este passo e continue para o passo seguinte. Se o dispositivo estiver ativado, desligue o dispositivo.
   
   1. Se estiver a utilizar um modelo 8600, desative o bastidor principal primeiro e, em seguida, desative o bastidor EBOD.
   2. Aguarde até que o dispositivo foi encerrado completamente. Todos os LEDs na minha o dispositivo será desativado.
2. Remova todos os cabos de rede que estão ligados às portas de dados. Se estiver a utilizar um modelo 8600, também remova os cabos SAS que ligam o bastidor principal para a inclusão EBOD.
3. Remova os dois controladores do dispositivo StorSimple. Para obter mais informações, consulte [remover um controlador de](#remove-a-controller).
4. Insira a substituição de fábrica para o controlador 0 primeiro e, em seguida, insira o controlador 1. Para obter mais informações, consulte [inserir um controlador](#insert-a-controller). Isto aciona a lógica de substituição de controlador dupla. Para obter mais informações, consulte [lógica de substituição de controlador dupla](#dual-controller-replacement-logic).
5. Enquanto a lógica de substituição de controlador progride em segundo plano, voltar a ligar os cabos. Tenha cuidado para ligar todos os cabos exatamente da mesma forma que eles foram conectados antes da substituição. Consulte as instruções detalhadas para o seu modelo, o cabo seu dispositivo seção [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Ative o dispositivo StorSimple. Se estiver a utilizar um modelo 8600:
   
   1. Certifique-se de que o bastidor EBOD está ativado primeiro.
   2. Aguarde até que o bastidor EBOD está em execução.
   3. Ative o bastidor principal.
   4. Após o primeiro controlador de reinício e está em bom estado de funcionamento, o sistema será executado.
      
      > [!NOTE]
      > Se estiver a monitorizar o dispositivo através da consola de série, poderá ver vários reinícios enquanto está a recuperar o controlador do procedimento de substituição. Quando for apresentado o menu da consola de série, em seguida, sabe que a substituição está concluída. Se o menu não aparecer dentro de 2,5 horas de iniciar a substituição de controlador, inicie [contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Remover um controlador
Utilize o procedimento seguinte para remover um módulo com falhas de controlador de dispositivo StorSimple.

> [!NOTE]
> As ilustrações seguintes destinam-se de controlador 0. Para o controlador 1, eles seriam ser revertidos.


#### <a name="to-remove-a-controller-module"></a>Para remover um módulo de controlador
1. Entender o travamento do módulo entre o thumb e forefinger.
2. Moderadamente compactar o thumb e forefinger em conjunto para liberar o bloqueio temporário de controlador.
   
    ![Liberar o bloqueio temporário de controlador](./media/storsimple-controller-replacement/IC741047.png)
   
    **Figura 2** bloqueio temporário de controlador de libertação
3. Utilize o bloqueio temporário como um identificador para o controlador de chassi, faça deslizar.
   
    ![Controlador deslizante fora de chassis](./media/storsimple-controller-replacement/IC741048.png)
   
    **Figura 3** deslizante o controlador de fora os chassis

## <a name="insert-a-controller"></a>Inserir um controlador
Utilize o procedimento seguinte para instalar um módulo de controlador fornecido pelo factory depois de remover um módulo com falhas do dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo de controlador
1. Verifique se existe qualquer dano para os conectores de interface. Não instale o módulo se qualquer um dos pins conector estão danificados ou vinculada.
2. Deslize o módulo de controlador para os chassis, enquanto o bloqueio temporário totalmente for lançado.
   
    ![Controlador deslizante em chassis](./media/storsimple-controller-replacement/IC741053.png)
   
    **Figura 4** deslizantes controlador para os chassis
3. Com o módulo de controlador inserido, comece a fechar o bloqueio temporário e continuar a enviar por push o módulo de controlador para os chassis. Bloqueio temporário irá envolver-se orientar o controlador no lugar certo.
   
    ![Fechar o bloqueio temporário de controlador](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figura 5** fechar o bloqueio temporário de controlador
4. Já está quando o bloqueio temporário ajusta-se no lugar certo. O **OK** LED deverá estar agora no.
   
   > [!NOTE]
   > Pode demorar até 5 minutos para que o controlador e o LED para ativar.
  
5. Para verificar que a substituição é efetuada com êxito, no portal do Azure, vá para o seu dispositivo e, em seguida, navegue para **Monitor** > **estado de funcionamento do Hardware**e certifique-se de que o controlador 0 e controlador 1 estão em bom estado (o estado está verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identificar o controlador ativo no seu dispositivo
Há muitas situações, como substituição de registo ou controlador de dispositivo de iniciantes, que requerem a localizar o controlador ativo num dispositivo StorSimple. O controlador ativo processa todo os firmware e funcionamento em rede operações de disco. Pode utilizar qualquer um dos seguintes métodos para identificar o controlador ativo:

* [Utilizar o portal do Azure para identificar o controlador ativo](#use-the-azure-portal-to-identify-the-active-controller)
* [Utilizar o Windows PowerShell para StorSimple para identificar o controlador ativo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Verificar o dispositivo físico para identificar o controlador ativo](#check-the-physical-device-to-identify-the-active-controller)

Cada um desses procedimentos é descrita a seguir.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Utilizar o portal do Azure para identificar o controlador ativo
No portal do Azure, navegue para o seu dispositivo e clique em **Monitor** > **estado de funcionamento do Hardware**e desloque-se para o **controladores** secção. Aqui pode verificar qual controlador está ativo.

![Identificar o controlador ativo no portal do Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** portal do Azure, com o controlador ativo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utilizar o Windows PowerShell para StorSimple para identificar o controlador ativo
Quando acessa o seu dispositivo através da consola de série, é apresentada uma mensagem de faixa. A mensagem de faixa contém informações básicas do dispositivo, como o modelo, o nome, a versão do software instalado e o estado do controlador que está a aceder. A imagem seguinte mostra um exemplo de uma mensagem de faixa:

![Mensagem de faixa Serial](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** faixa mensagem mostrando controlador 0 como ativo

Pode utilizar a mensagem de faixa para determinar se o controlador que está ligado à é ativas ou passivas.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verificar o dispositivo físico para identificar o controlador ativo
Para identificar o controlador ativo no seu dispositivo, localize o LED azul acima a porta de dados 5 atrás do bastidor principal.

Se este LED é intermitente, o controlador está ativo e outro controlador está no modo de espera. Utilize o diagrama e a tabela seguinte como um auxílio.

![Dispositivo primário do bastidor backplane com dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** inverso de bastidor principal com portas de dados e LEDs de monitorização

| Etiqueta | Descrição |
|:--- |:--- |
| 1-6 |DADOS 0 – 5 portas de rede |
| 7 |Azul LED |

## <a name="next-steps"></a>Passos Seguintes
Saiba mais sobre [substituição de componente de hardware do StorSimple](storsimple-8000-hardware-component-replacement.md).

