---
title: Substitua um controlador de dispositivos da série StorSimple 8000 / Microsoft Docs
description: Explica como remover e substituir um ou ambos os módulos controladores no seu dispositivo da série StorSimple 8000.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78365853"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substitua um módulo de controlador no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um ou ambos os módulos controladores num dispositivo StorSimple. Também discute a lógica subjacente para os cenários de substituição de controlador único e duplo.

> [!NOTE]
> Antes de realizar uma substituição do controlador, recomendamos que atualize sempre o firmware do controlador para a versão mais recente.
> 
> Para evitar danos no seu dispositivo StorSimple, não ejete o controlador até que os LEDs apareçam como um dos seguintes:
> 
> * Todas as luzes estão apagadas.
> * LED 3, ![ícone de verificação verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), e ![ícone de cruz vermelha](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) estão a piscar, e led 0 e LED 7 estão **ligados**.


A tabela seguinte mostra os cenários de substituição do controlador suportado.

| Caso | Cenário de substituição | Procedimento aplicável |
|:--- |:--- |:--- |
| 1 |Um controlador está em estado falhado, o outro controlador é saudável e ativo. |[Substituição](#replace-a-single-controller)do controlador único, que descreve a lógica por trás de [uma única substituição](#single-controller-replacement-logic)do controlador, bem como os [passos de substituição](#single-controller-replacement-steps). |
| 2 |Ambos os controladores falharam e requerem substituição. O chassis, discos e o recinto do disco são saudáveis. |[Substituição](#replace-both-controllers)dupla do controlador , que descreve a lógica por trás de [uma substituição dupla](#dual-controller-replacement-logic)do controlador, bem como os [passos de substituição](#dual-controller-replacement-steps). |
| 3 |Os controladores do mesmo dispositivo ou de diferentes dispositivos são trocados. Os chassis, discos e os compartimentos do disco são saudáveis. |Aparecerá uma mensagem de alerta de desajustada. |
| 4 |Falta um controlador e o outro controlador falha. |[Substituição](#replace-both-controllers)dupla do controlador , que descreve a lógica por trás de [uma substituição dupla](#dual-controller-replacement-logic)do controlador, bem como os [passos de substituição](#dual-controller-replacement-steps). |
| 5 |Um ou ambos os controladores falharam. Não é possível aceder ao dispositivo através da consola em série ou do Remo PowerShell do Windows. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter um procedimento de substituição do controlador manual. |
| 6 |Os controladores têm uma versão de construção diferente, que pode ser devido a:<ul><li>Os controladores têm uma versão de software diferente.</li><li>Os controladores têm uma versão de firmware diferente.</li></ul> |Se as versões de software do controlador forem diferentes, a lógica de substituição deteta isso e atualiza a versão do software no controlador de substituição.<br><br>Se as versões de firmware do controlador forem diferentes e a versão antiga do firmware **não** for automaticamente atualizável, uma mensagem de alerta aparecerá no portal Azure. Deverá procurar atualizações e instalar as atualizações do firmware.</br></br>Se as versões de firmware do controlador forem diferentes e a versão antiga do firmware for automaticamente atualizável, a lógica de substituição do controlador irá detetar isso e, após o arranque do controlador, o firmware será automaticamente atualizado. |

Tem de remover um módulo de controlador se tiver falhado. Um ou ambos os módulos do controlador podem falhar, o que pode resultar numa substituição do controlador único ou numa substituição dupla do controlador. Para os procedimentos de substituição e a lógica por trás deles, consulte o seguinte:

* [Replace a single controller](#replace-a-single-controller) (Substituir um controlador individual)
* [Substitua ambos os controladores](#replace-both-controllers)
* [Remova um controlador](#remove-a-controller)
* [Insira um controlador](#insert-a-controller)
* [Identifique o controlador ativo no seu dispositivo](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Antes de remover e substituir um controlador, reveja as informações de segurança na substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Substitua um único controlador
Quando um dos dois controladores do dispositivo Microsoft Azure StorSimple falhou, está avariado ou está em falta, tem de substituir um único controlador.

### <a name="single-controller-replacement-logic"></a>Lógica de substituição do controlador único
Numa única substituição do controlador, deve primeiro remover o controlador falhado. (O controlador restante no dispositivo é o controlador ativo.) Quando insere o controlador de substituição, ocorrem as seguintes ações:

1. O controlador de substituição começa imediatamente a comunicar com o dispositivo StorSimple.
2. Uma imagem do disco rígido virtual (VHD) para o controlador ativo é copiada no controlador de substituição.
3. O instantâneo é modificado de modo a que, quando o controlador de substituição começar a partir deste VHD, seja reconhecido como um controlador de espera.
4. Quando as modificações estiverem completas, o controlador de substituição começará como controlador de espera.
5. Quando ambos os controladores estão em funcionamento, o cluster fica on-line.

### <a name="single-controller-replacement-steps"></a>Passos de substituição do controlador único
Complete os seguintes passos se um dos controladores do seu dispositivo Microsoft Azure StorSimple falhar. (O outro controlador deve estar ativo e em funcionamento. Se ambos os controladores falharem ou falharem, vá a [passos de substituição do controlador duplo](#dual-controller-replacement-steps).)

> [!NOTE]
> Pode levar 30 a 45 minutos para o controlador reiniciar e recuperar completamente do procedimento de substituição do controlador único. O tempo total de todo o procedimento, incluindo a fixação dos cabos, é de aproximadamente 2 horas.


#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover um único módulo de controlador falhado
1. No portal Azure, vá ao serviço StorSimple Device Manager, clique em **Dispositivos**e clique no nome do dispositivo que pretende monitorizar.
2. Vá ao **Monitor > Saúde de Hardware.** O estado do controlador 0 ou do controlador 1 deve ser vermelho, o que indica uma falha.
   
   > [!NOTE]
   > O controlador falhado numa única substituição do controlador é sempre um controlador de espera.
   
3. Utilize a Figura 1 e a tabela seguinte para localizar o módulo do controlador falhado.
   
    ![Backplane dos módulos de recinto primário do dispositivo](./media/storsimple-controller-replacement/IC740994.png)
   
    **Figura 1** Parte de trás do dispositivo StorSimple
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
4. No controlador falhado, retire todos os cabos de rede ligados das portas de dados. Se estiver a utilizar um modelo 8600, retire também os cabos SAS que ligam o controlador ao controlador EBOD.
5. Siga os passos de remoção de [um controlador](#remove-a-controller) para remover o controlador falhado.
6. Instale a substituição da fábrica na mesma ranhura a partir da qual o controlador falhado foi removido. Isto despoleta a lógica de substituição do controlador único. Para mais informações, consulte a lógica de [substituição do controlador único](#single-controller-replacement-logic).
7. Enquanto a lógica de substituição do controlador único progride em segundo plano, religue os cabos. Tome cuidado para ligar todos os cabos exatamente da mesma forma que foram ligados antes da substituição.
8. Depois de o controlador reiniciar, verifique o estado do **Controlador** e o **estado do Cluster** no portal Azure para verificar se o controlador está de volta a um estado saudável e está em modo de espera.

> [!NOTE]
> Se estiver a monitorizar o dispositivo através da consola em série, poderá ver vários reinícios enquanto o controlador está a recuperar do procedimento de substituição. Quando o menu da consola em série for apresentado, então sabe que a substituição está completa. Se o menu não aparecer dentro de duas horas após o início da substituição do controlador, [contacte](storsimple-8000-contact-microsoft-support.md)o Microsoft Support .
>
> Iniciar o Update 4, também pode utilizar o cmdlet `Get-HCSControllerReplacementStatus` na interface Windows PowerShell do dispositivo para monitorizar o estado do processo de substituição do controlador.
> 

## <a name="replace-both-controllers"></a>Substitua ambos os controladores
Quando ambos os controladores do dispositivo Microsoft Azure StorSimple falharam, estão avariados ou estão em falta, é necessário substituir ambos os controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição do controlador duplo
Numa substituição dupla do controlador, remove primeiro os dois controladores falhados e, em seguida, insere substituições. Quando os dois controladores de substituição são inseridos, ocorrem as seguintes ações:

1. O controlador de substituição na ranhura 0 verifica o seguinte:
   
   1. Está a usar as versões atuais do firmware e do software?
   2. Faz parte do agrupamento?
   3. O controlador de pares está a funcionar e está agrupado?
      
      Se nenhuma destas condições for verdadeira, o controlador procura a última cópia de segurança diária (localizada no **não DOMstorage** na unidade S). O controlador copia a última foto do VHD da cópia de segurança.
2. O controlador na ranhura 0 utiliza o instantâneo para a própria imagem.
3. Entretanto, o controlador na ranhura 1 aguarda que o controlador 0 complete a imagem e comece.
4. Após o início do controlador 0, o controlador 1 deteta o cluster criado pelo controlador 0, o que desencadeia a lógica de substituição do controlador único. Para mais informações, consulte a lógica de [substituição do controlador único](#single-controller-replacement-logic).
5. Depois, ambos os controladores estarão em execução e o cluster estará online.

> [!IMPORTANT]
> Após uma substituição dupla do controlador, depois de configurado o dispositivo StorSimple, é essencial que seja possível obter uma cópia de segurança manual do dispositivo. As cópias de segurança diárias de configuração do dispositivo só são acionadas depois de decorridos 24 horas. Trabalhe com o [Suporte](storsimple-8000-contact-microsoft-support.md) microsoft para fazer uma cópia de segurança manual do seu dispositivo.


### <a name="dual-controller-replacement-steps"></a>Passos de substituição do controlador duplo
Este fluxo de trabalho é necessário quando ambos os controladores do seu dispositivo Microsoft Azure StorSimple falharam. Isto pode acontecer num datacenter em que o sistema de arrefecimento deixa de funcionar e, como resultado, ambos os controladores falham num curto espaço de tempo. Dependendo se o dispositivo StorSimple está desligado ou ligado, e se está a utilizar um modelo 8600 ou 8100, é necessário um conjunto diferente de passos.

> [!IMPORTANT]
> Pode levar 45 minutos a 1 hora para o controlador reiniciar e recuperar completamente de um procedimento de substituição do controlador duplo. O tempo total de todo o procedimento, incluindo a fixação dos cabos, é de aproximadamente 2,5 horas.

#### <a name="to-replace-both-controller-modules"></a>Para substituir ambos os módulos controladores
1. Se o dispositivo estiver desligado, salte este passo e siga para o próximo passo. Se o aparelho estiver ligado, desligue o aparelho.
   
   1. Se estiver a utilizar um modelo 8600, desligue primeiro o recinto primário e desligue primeiro o recinto EBOD.
   2. Aguarde até que o dispositivo desligue completamente. Todos os LEDs na parte de trás do dispositivo estarão desligados.
2. Remova todos os cabos de rede ligados às portas de dados. Se estiver a utilizar um modelo 8600, retire também os cabos SAS que ligam o recinto primário ao recinto EBOD.
3. Retire ambos os controladores do dispositivo StorSimple. Para mais informações, consulte [a remoção de um controlador](#remove-a-controller).
4. Insira primeiro a substituição da fábrica para o controlador 0 e, em seguida, insira o Controlador 1. Para mais informações, consulte [a inserção de um controlador](#insert-a-controller). Isto despoleta a lógica de substituição do controlador duplo. Para mais informações, consulte a lógica de [substituição do controlador duplo](#dual-controller-replacement-logic).
5. Enquanto a lógica de substituição do controlador progride em segundo plano, religue os cabos. Tome cuidado para ligar todos os cabos exatamente da mesma forma que foram ligados antes da substituição. Consulte as instruções detalhadas para o seu modelo no Cabo a sua secção de dispositivos de [instalação do seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Ligue o dispositivo StorSimple. Se estiver a utilizar um modelo 8600:
   
   1. Certifique-se de que o recinto ebod está ligado primeiro.
   2. Espere até que o recinto do EBOD esteja em funcionamento.
   3. Ligue o recinto primário.
   4. Após o primeiro controlador reiniciar e estiver em estado saudável, o sistema estará em funcionamento.
      
      > [!NOTE]
      > Se estiver a monitorizar o dispositivo através da consola em série, poderá ver vários reinícios enquanto o controlador está a recuperar do procedimento de substituição. Quando o menu da consola em série aparecer, então sabe que a substituição está completa. Se o menu não aparecer dentro de 2,5 horas após o início da substituição do controlador, [contacte](storsimple-8000-contact-microsoft-support.md)o Suporte da Microsoft .
     
## <a name="remove-a-controller"></a>Remova um controlador
Utilize o seguinte procedimento para remover um módulo de controlador defeituoso do seu dispositivo StorSimple.

> [!NOTE]
> As seguintes ilustrações são para o controlador 0. Para o controlador 1, estes seriam invertidos.


#### <a name="to-remove-a-controller-module"></a>Para remover um módulo controlador
1. Segure o trinco do módulo entre o polegar e o dedo indicador.
2. Aperte suavemente o polegar e o dedo indicador para soltar o trinco do controlador.
   
    ![Liberação do trinco do controlador](./media/storsimple-controller-replacement/IC741047.png)
   
    **Figura 2** Liberação do trinco do controlador
3. Utilize o trinco como pega para deslizar o controlador para fora do chassis.
   
    ![Controlador deslizante fora do chassis](./media/storsimple-controller-replacement/IC741048.png)
   
    **Figura 3** Deslizando o controlador para fora do chassis

## <a name="insert-a-controller"></a>Insira um controlador
Utilize o seguinte procedimento para instalar um módulo de controlador fornecido pela fábrica depois de remover um módulo defeituoso do seu dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo de controlador
1. Verifique se há algum dano nos conectores da interface. Não instale o módulo se algum dos pinos do conector estiver danificado ou dobrado.
2. Deslize o módulo do controlador para dentro do chassis enquanto o trinco estiver completamente libertado.
   
    ![Controlador deslizante em chassis](./media/storsimple-controller-replacement/IC741053.png)
   
    **Figura 4** Controlador deslizante no chassis
3. Com o módulo do controlador inserido, comece a fechar o trinco enquanto continua a empurrar o módulo do controlador para o chassis. O trinco irá engatar-se para guiar o controlador no lugar.
   
    ![Fecho do controlador](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figura 5** Fechando o trinco do controlador
4. Acaba-se quando o trinco se encaixa no lugar. O LED **OK** deve estar ligado.
   
   > [!NOTE]
   > Pode levar até 5 minutos para o controlador e o LED ativarem.
  
5. Para verificar se a substituição é bem sucedida, no portal Azure, vá ao seu dispositivo e navegue para **monitorizar** > saúde de **hardware,** e certifique-se de que tanto o controlador 0 como o controlador 1 são saudáveis (o estado é verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identifique o controlador ativo no seu dispositivo
Existem muitas situações, como o registo do dispositivo pela primeira vez ou a substituição do controlador, que exigem que você localize o controlador ativo num dispositivo StorSimple. O controlador ativo processa todas as operações de firmware e networking do disco. Pode utilizar qualquer um dos seguintes métodos para identificar o controlador ativo:

* [Utilize o portal Azure para identificar o controlador ativo](#use-the-azure-portal-to-identify-the-active-controller)
* [Utilize o Windows PowerShell para o StorSimple para identificar o controlador ativo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Verifique o dispositivo físico para identificar o controlador ativo](#check-the-physical-device-to-identify-the-active-controller)

Cada um destes procedimentos é descrito em seguida.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Utilize o portal Azure para identificar o controlador ativo
No portal Azure, navegue até ao seu dispositivo e, em seguida, para **monitorizar** a **saúde**do hardware > , e percorra a secção **controladores.** Aqui pode verificar qual o controlador ativo.

![Identificar controlador ativo no portal Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** Portal Azure mostrando o controlador ativo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utilize o Windows PowerShell para o StorSimple para identificar o controlador ativo
Quando acedeao ao seu dispositivo através da consola em série, é apresentada uma mensagem de banner. A mensagem de banner contém informações básicas do dispositivo, tais como o modelo, nome, versão de software instalada e estado do controlador a que acede. A imagem que se segue mostra um exemplo de uma mensagem de banner:

![Mensagem de banner em série](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Mensagem de banner mostrando controlador 0 como Ativo

Pode utilizar a mensagem de banner para determinar se o controlador a que está ligado está ativo ou passivo.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verifique o dispositivo físico para identificar o controlador ativo
Para identificar o controlador ativo no seu dispositivo, localize o LED azul acima da porta DATA 5 na parte de trás do recinto primário.

Se este LED estiver a piscar, o controlador está ativo e o outro controlador está em modo de espera. Utilize o seguinte diagrama e tabela como um auxílio.

![Plano de backplane do recinto primário do dispositivo com dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Parte de trás do recinto primário com portas de dados e LEDs de monitorização

| Etiqueta | Descrição |
|:--- |:--- |
| 1-6 |DATA 0 – 5 portas de rede |
| 7 |LED azul |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre a substituição do componente de [hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

