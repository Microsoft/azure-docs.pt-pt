---
title: Substitua um controlador de dispositivos da série StorSimple 8000 Microsoft Docs
description: Explica como remover e substituir um ou ambos os módulos controladores no seu dispositivo da série StorSimple 8000.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/05/2017
ms.author: alkohli
ms.openlocfilehash: 9d8b75c48da2bb13d843258ead378d3e849da951
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514075"
---
# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substitua um módulo controlador no seu dispositivo StorSimple
## <a name="overview"></a>Descrição geral
Este tutorial explica como remover e substituir um ou ambos os módulos controladores num dispositivo StorSimple. Também discute a lógica subjacente aos cenários de substituição de um controlador único e duplo.

> [!NOTE]
> Antes de efetuar uma substituição do controlador, recomendamos que atualize sempre o firmware do controlador para a versão mais recente.
> 
> Para evitar danos no seu dispositivo StorSimple, não ejeta o controlador até que os LEDs apareçam como um dos seguintes:
> 
> * Todas as luzes estão apagadas.
> * LED 3, ![ ícone de verificação verde ](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png) e ícone da Cruz Vermelha ![ ](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) estão a piscar e LED 0 e LED 7 estão **ligados**.


A tabela seguinte mostra os cenários de substituição do controlador suportado.

| Caso | Cenário de substituição | Procedimento aplicável |
|:--- |:--- |:--- |
| 1 |Um controlador está num estado falhado, o outro controlador está saudável e ativo. |[Substituição de um único controlador](#replace-a-single-controller), que descreve a lógica por trás de uma única [substituição do controlador,](#single-controller-replacement-logic)bem como as [etapas de substituição](#single-controller-replacement-steps). |
| 2 |Ambos os controladores falharam e requerem substituição. O chassis, discos e o invólucro do disco são saudáveis. |[Substituição dupla do controlador](#replace-both-controllers), que descreve a [lógica por trás de uma substituição dupla do controlador,](#dual-controller-replacement-logic)bem como as [etapas de substituição](#dual-controller-replacement-steps). |
| 3 |Os controladores do mesmo dispositivo ou de diferentes dispositivos são trocados. O chassis, discos e dissi de disco são saudáveis. |Aparecerá uma mensagem de alerta incompatível com a ranhura. |
| 4 |Falta um controlador e o outro controlador falha. |[Substituição dupla do controlador](#replace-both-controllers), que descreve a [lógica por trás de uma substituição dupla do controlador,](#dual-controller-replacement-logic)bem como as [etapas de substituição](#dual-controller-replacement-steps). |
| 5 |Um ou ambos os controladores falharam. Não é possível aceder ao dispositivo através da consola em série ou da remoting do Windows PowerShell. |[Contacte o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para obter um procedimento de substituição do controlador manual. |
| 6 |Os controladores têm uma versão de construção diferente, que pode ser devido a:<ul><li>Os controladores têm uma versão de software diferente.</li><li>Os controladores têm uma versão de firmware diferente.</li></ul> |Se as versões de software do controlador forem diferentes, a lógica de substituição deteta isso e atualiza a versão do software no controlador de substituição.<br><br>Se as versões do firmware do controlador forem diferentes e a versão antiga do firmware **não** for automaticamente atualizável, aparecerá uma mensagem de alerta no portal Azure. Deve procurar atualizações e instalar as atualizações do firmware.</br></br>Se as versões do firmware do controlador forem diferentes e a versão antiga do firmware for automaticamente atualizável, a lógica de substituição do controlador irá detetar esta situação e, após o início do controlador, o firmware será automaticamente atualizado. |

Tem de remover um módulo controlador se este tiver falhado. Um ou ambos os módulos do controlador podem falhar, o que pode resultar numa substituição de um único controlador ou na substituição do controlador duplo. Para os procedimentos de substituição e a lógica por trás deles, consulte o seguinte:

* [Replace a single controller](#replace-a-single-controller) (Substituir um controlador individual)
* [Substitua ambos os controladores](#replace-both-controllers)
* [Remover um controlador](#remove-a-controller)
* [Inserir um controlador](#insert-a-controller)
* [Identifique o controlador ativo no seu dispositivo](#identify-the-active-controller-on-your-device)

> [!IMPORTANT]
> Antes de remover e substituir um controlador, reveja as informações de segurança na [substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).
> 
> 

## <a name="replace-a-single-controller"></a>Replace a single controller (Substituir um controlador individual)
Quando um dos dois comandos do dispositivo Microsoft Azure StorSimple falhou, está avariado ou está em falta, é necessário substituir um único controlador.

### <a name="single-controller-replacement-logic"></a>Lógica de substituição de controlador único
Numa única substituição do controlador, deve primeiro remover o controlador falhado. (O controlador restante no dispositivo é o controlador ativo.) Quando se insere o controlador de substituição, ocorrem as seguintes ações:

1. O controlador de substituição começa imediatamente a comunicar com o dispositivo StorSimple.
2. Uma imagem do disco rígido virtual (VHD) para o controlador ativo é copiada no controlador de substituição.
3. O instantâneo é modificado de modo a que, quando o controlador de substituição começar a partir deste VHD, seja reconhecido como controlador de espera.
4. Quando as modificações estiverem completas, o controlador de substituição iniciar-se-á como controlador de espera.
5. Quando ambos os controladores estão em funcionamento, o cluster fica on-line.

### <a name="single-controller-replacement-steps"></a>Etapas de substituição de controlador único
Complete os seguintes passos se um dos controladores do seu dispositivo Microsoft Azure StorSimple falhar. (O outro controlador deve estar ativo e a funcionar. Se ambos os controladores falharem ou avariarem, aceda às [duas etapas de substituição do controlador](#dual-controller-replacement-steps).)

> [!NOTE]
> Pode levar 30 a 45 minutos para o controlador reiniciar e recuperar completamente do procedimento de substituição do controlador único. O tempo total para todo o procedimento, incluindo a fixação dos cabos, é de aproximadamente 2 horas.


#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover um único módulo de controlador falhado
1. No portal Azure, vá ao serviço StorSimple Device Manager, clique em **Dispositivos**e, em seguida, clique no nome do dispositivo que pretende monitorizar.
2. Vá ao **Monitor > saúde do hardware**. O estado do Controlador 0 ou do Controlador 1 deve ser vermelho, o que indica uma falha.
   
   > [!NOTE]
   > O controlador falhado numa única substituição do controlador é sempre um controlador de espera.
   
3. Utilize a Figura 1 e a seguinte tabela para localizar o módulo controlador falhado.
   
    ![Backplane dos módulos de recinto primário do dispositivo](./media/storsimple-controller-replacement/IC740994.png)
   
    **Figura 1** Back of StorSimple dispositivo
   
   | Etiqueta | Descrição |
   |:--- |:--- |
   | 1 |PCM 0 |
   | 2 |PCM 1 |
   | 3 |Controlador 0 |
   | 4 |Controlador 1 |
4. No controlador falhado, remova todos os cabos de rede ligados das portas de dados. Se estiver a utilizar um modelo 8600, retire também os cabos SAS que ligam o controlador ao controlador EBOD.
5. Siga os passos para [remover um controlador](#remove-a-controller) para remover o controlador falhado.
6. Instale a substituição da fábrica na mesma ranhura a partir da qual o controlador falhado foi removido. Isto despoleta a lógica de substituição de um único controlador. Para obter mais informações, consulte [a lógica de substituição de um único controlador.](#single-controller-replacement-logic)
7. Enquanto a lógica de substituição do controlador único progride em segundo plano, reconecte os cabos. Tenha o cuidado de ligar todos os cabos exatamente da mesma forma que foram ligados antes da substituição.
8. Após o reinício do controlador, verifique o **estado do Controlador** e o estado do **Cluster** no portal Azure para verificar se o controlador está de volta a um estado saudável e encontra-se em modo de espera.

> [!NOTE]
> Se estiver a monitorizar o dispositivo através da consola em série, poderá ver vários recomeços enquanto o controlador estiver a recuperar do procedimento de substituição. Quando o menu da consola em série é apresentado, então sabe que a substituição está completa. Se o menu não aparecer no prazo de duas horas após o início da substituição do controlador, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md).
>
> A partir do Início da Atualização 4, também pode utilizar o cmdlet `Get-HCSControllerReplacementStatus` na interface Windows PowerShell do dispositivo para monitorizar o estado do processo de substituição do controlador.
> 

## <a name="replace-both-controllers"></a>Substitua ambos os controladores
Quando ambos os controladores do dispositivo Microsoft Azure StorSimple falharam, estão avariados ou estão em falta, é necessário substituir ambos os comandos. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição de controlador duplo
Numa substituição dupla do controlador, primeiro remove ambos os controladores falhados e depois insere substituições. Quando os dois controladores de substituição são inseridos, ocorrem as seguintes ações:

1. O controlador de substituição na ranhura 0 verifica o seguinte:
   
   1. Está a utilizar as versões atuais do firmware e software?
   2. Faz parte do aglomerado?
   3. O controlador de pares está a funcionar e está agrupado?
      
      Se nenhuma destas condições for verdadeira, o controlador procura a mais recente cópia de segurança diária (localizada na **não ODM na** unidade S). O controlador copia a última imagem do VHD a partir da cópia de segurança.
2. O controlador na ranhura 0 utiliza o instantâneo para a própria imagem.
3. Enquanto isso, o controlador na ranhura 1 espera que o controlador 0 complete a imagem e comece.
4. Após o início do controlador 0, o controlador 1 deteta o cluster criado pelo controlador 0, o que aciona a lógica de substituição do controlador único. Para obter mais informações, consulte [a lógica de substituição de um único controlador.](#single-controller-replacement-logic)
5. Depois, ambos os controladores estarão em funcionamento e o cluster ficará online.

> [!IMPORTANT]
> Após uma substituição dupla do controlador, após a configuração do dispositivo StorSimple, é essencial que se faça uma cópia de segurança manual do dispositivo. As cópias de segurança diárias de configuração do dispositivo só são ativadas depois de decorridos 24 horas. Trabalhe com [o Microsoft Support](storsimple-8000-contact-microsoft-support.md) para fazer uma cópia de segurança manual do seu dispositivo.


### <a name="dual-controller-replacement-steps"></a>Passos de substituição de controlador duplo
Este fluxo de trabalho é necessário quando ambos os controladores do seu dispositivo Microsoft Azure StorSimple falharam. Isto pode acontecer num datacenter em que o sistema de arrefecimento deixa de funcionar e, como resultado, ambos os controladores falham num curto espaço de tempo. Dependendo se o dispositivo StorSimple está desligado ou ligado, e se está a utilizar um modelo 8600 ou 8100, é necessário um conjunto diferente de passos.

> [!IMPORTANT]
> Pode levar 45 minutos a 1 hora para o controlador reiniciar e recuperar completamente de um procedimento de substituição do controlador duplo. O tempo total para todo o procedimento, incluindo a fixação dos cabos, é de aproximadamente 2,5 horas.

#### <a name="to-replace-both-controller-modules"></a>Para substituir ambos os módulos controladores
1. Se o dispositivo estiver desligado, ignore este passo e siga para o passo seguinte. Se o aparelho estiver ligado, desligue o aparelho.
   
   1. Se estiver a utilizar um modelo 8600, desligue primeiro o recinto primário e, em seguida, desligue o recinto EBOD.
   2. Aguarde até que o dispositivo se desligue completamente. Todos os LEDs na parte de trás do dispositivo estarão desligados.
2. Remova todos os cabos de rede que estão ligados às portas de dados. Se estiver a utilizar um modelo 8600, retire também os cabos SAS que ligam o recinto primário ao recinto EBOD.
3. Retire ambos os controladores do dispositivo StorSimple. Para obter mais informações, consulte [a remoção de um controlador](#remove-a-controller).
4. Insira primeiro a substituição da fábrica para o Controlador 0 e, em seguida, insira o Controlador 1. Para mais informações, consulte [um controlador.](#insert-a-controller) Isto despoleta a lógica de substituição do controlador duplo. Para obter mais informações, consulte a [lógica de substituição do controlador duplo.](#dual-controller-replacement-logic)
5. Enquanto a lógica de substituição do controlador progride em segundo plano, reconecte os cabos. Tenha o cuidado de ligar todos os cabos exatamente da mesma forma que foram ligados antes da substituição. Consulte as instruções detalhadas para o seu modelo no cabo da secção do seu dispositivo de instalação do [seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instale o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).
6. Ligue o dispositivo StorSimple. Se estiver a utilizar um modelo 8600:
   
   1. Certifique-se de que o recinto EBOD é ligado primeiro.
   2. Espere até que o recinto da EBOD esteja funcionando.
   3. Ligue o recinto primário.
   4. Após o primeiro controlador recomeçar e estiver em estado saudável, o sistema estará em funcionamento.
      
      > [!NOTE]
      > Se estiver a monitorizar o dispositivo através da consola em série, poderá ver vários recomeços enquanto o controlador estiver a recuperar do procedimento de substituição. Quando o menu da consola em série aparecer, então sabe que a substituição está completa. Se o menu não aparecer no prazo de 2,5 horas após o início da substituição do controlador, contacte o [Microsoft Support](storsimple-8000-contact-microsoft-support.md).
     
## <a name="remove-a-controller"></a>Remover um controlador
Utilize o seguinte procedimento para remover um módulo controlador defeituoso do seu dispositivo StorSimple.

> [!NOTE]
> As seguintes ilustrações são para o controlador 0. Para o controlador 1, estes seriam invertidos.


#### <a name="to-remove-a-controller-module"></a>Para remover um módulo de controlador
1. Segure o fecho do módulo entre o polegar e o indicador.
2. Aperte suavemente o polegar e o dedo indicador juntos para soltar o trinco do controlador.
   
    ![Trinco controlador de libertação](./media/storsimple-controller-replacement/IC741047.png)
   
    **Figura 2** Trinco controlador de libertação
3. Utilize o trinco como pega para deslizar o controlador para fora do chassis.
   
    ![Controlador deslizante fora do chassis](./media/storsimple-controller-replacement/IC741048.png)
   
    **Figura 3** Deslizando o controlador para fora do chassis

## <a name="insert-a-controller"></a>Inserir um controlador
Utilize o seguinte procedimento para instalar um módulo controlador fornecido pela fábrica depois de remover um módulo defeituoso do seu dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo de controlador
1. Verifique se há danos nos conectores da interface. Não instale o módulo se algum dos pinos do conector estiver danificado ou dobrado.
2. Deslize o módulo do controlador para dentro do chassis enquanto o trinco estiver totalmente solto.
   
    ![Controlador deslizante para o chassis](./media/storsimple-controller-replacement/IC741053.png)
   
    **Figura 4** Controlador deslizante para o chassis
3. Com o módulo do controlador inserido, comece a fechar o trinco enquanto continua a empurrar o módulo do controlador para dentro do chassis. O trinco irá engatar para guiar o controlador no lugar.
   
    ![Fecho do controlador de encerramento](./media/storsimple-controller-replacement/IC741054.png)
   
    **Figura 5** Fechar o trinco do controlador
4. Acabou-se quando o trinco se encaixar. O LED **OK** deve estar ligado.
   
   > [!NOTE]
   > Pode levar até 5 minutos para o controlador e o LED ativarem.
  
5. Para verificar se a substituição é bem sucedida, no portal Azure, vá ao seu dispositivo e, em seguida, navegue para **monitorizar**a  >  **saúde do Hardware**, e certifique-se de que tanto o controlador 0 como o controlador 1 estão saudáveis (o estado é verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identifique o controlador ativo no seu dispositivo
Existem muitas situações, como o registo do dispositivo pela primeira vez ou a substituição do controlador, que obrigam a localizar o controlador ativo num dispositivo StorSimple. O controlador ativo processa todas as operações de firmware e networking de discos. Pode utilizar qualquer um dos seguintes métodos para identificar o controlador ativo:

* [Utilize o portal Azure para identificar o controlador ativo](#use-the-azure-portal-to-identify-the-active-controller)
* [Utilize o Windows PowerShell para o StorSimple identificar o controlador ativo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)
* [Verifique o dispositivo físico para identificar o controlador ativo](#check-the-physical-device-to-identify-the-active-controller)

Cada um destes procedimentos é descrito a seguir.

### <a name="use-the-azure-portal-to-identify-the-active-controller"></a>Utilize o portal Azure para identificar o controlador ativo
No portal Azure, navegue para o seu dispositivo e, em seguida, **monitorize**a  >  **saúde**do Hardware , e percorra para a secção **Controladores.** Aqui pode verificar qual o controlador ativo.

![Identificar controlador ativo no portal Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** Portal Azure mostrando o controlador ativo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utilize o Windows PowerShell para o StorSimple identificar o controlador ativo
Quando acede ao seu dispositivo através da consola em série, é apresentada uma mensagem de banner. A mensagem banner contém informações básicas do dispositivo, tais como o modelo, nome, versão de software instalado e o estado do controlador a que está a aceder. A imagem a seguir mostra um exemplo de uma mensagem de banner:

![Mensagem de banner em série](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Mensagem de banner mostrando o controlador 0 como Ativo

Pode utilizar a mensagem de banner para determinar se o controlador a que está ligado está ativo ou passivo.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verifique o dispositivo físico para identificar o controlador ativo
Para identificar o controlador ativo no seu dispositivo, localize o LED azul acima da porta DATA 5 na parte de trás do recinto primário.

Se este LED estiver a piscar, o controlador está ativo e o outro controlador está em modo de espera. Utilize o seguinte diagrama e tabela como uma ajuda.

![Plano de apoio do recinto primário do dispositivo com dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Parte de trás do recinto primário com portas de dados e LEDs de monitorização

| Etiqueta | Descrição |
|:--- |:--- |
| 1-6 |DADOS 0 – 5 portas de rede |
| 7 |LED azul |

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre [a substituição do componente de hardware StorSimple](storsimple-8000-hardware-component-replacement.md).

