---
title: Sincronizar vários dispositivos DK Azure Kinect
description: Este artigo explora os benefícios da sincronização de vários dispositivos, bem como como configurar os dispositivos para sincronizar.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 02/20/2020
ms.topic: article
keywords: azul, cinect, especificações, hardware, DK, capacidades, profundidade, cor, RGB, IMU, array, profundidade, multi, sincronização
ms.openlocfilehash: eabf77896777f39efcfd61adb3040bca8642716e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102039959"
---
# <a name="synchronize-multiple-azure-kinect-dk-devices"></a>Sincronizar vários dispositivos DK Azure Kinect

Cada dispositivo DK Azure Kinect inclui portas de sincronização de 3,5 mm **(Sync in** e **Sync out)** que pode utilizar para ligar vários dispositivos em conjunto. Depois de ligar os dispositivos, o seu software pode coordenar o tempo de disparo entre eles. 

Este artigo como ligar e sincronizar os dispositivos.

## <a name="benefits-of-using-multiple-azure-kinect-dk-devices"></a>Benefícios da utilização de vários dispositivos Azure Kinect DK

Existem muitas razões para usar vários dispositivos Azure Kinect DK, incluindo os seguintes:

- Preencha as oclusões. Embora as transformações de dados do Azure Kinect DK produzam uma única imagem, as duas câmaras (profundidade e RGB) estão na verdade a uma pequena distância. A compensação torna possíveis as oclusões. Uma oclusão ocorre quando um objeto de primeiro plano bloqueia a vista de parte de um objeto de fundo para uma das duas câmaras de um dispositivo. Na imagem de cor resultante, o objeto de primeiro plano parece lançar uma sombra no objeto de fundo.  
   Por exemplo, no diagrama seguinte, a câmara do lado esquerdo vê o pixel cinzento "P2". No entanto, o objeto de primeiro plano branco bloqueia o feixe IR da câmara do lado direito. A câmara do lado direito não tem dados para "P2".  
   ![O diagrama mostra duas câmaras direcionadas para o mesmo ponto com uma delas bloqueada.](./media/occlusion.png)  
   Dispositivos sincronizados adicionais podem fornecer os dados obstruídos.
- Digitalize objetos em três dimensões.
- Aumente a taxa de fotogramas efetiva para um valor superior a 30 fotogramas por segundo (FPS).
- Capture várias imagens a cores 4K da mesma cena, todas alinhadas dentro de 100 microsegundos &mu; (s) do centro de exposição.
- Aumente a cobertura da câmara dentro do espaço.

## <a name="plan-your-multi-device-configuration"></a>Planeie a sua configuração multi-dispositivo

Antes de começar, certifique-se de que revê [as especificações de hardware DK da Azure Kinect](hardware-specification.md) e [da câmara de profundidade Azure Kinect DK](depth-camera.md).

> [!NOTE]  
> Retire a tampa de plástico exterior para expor as tomadas Sync In e Sync out.

### <a name="select-a-device-configuration"></a>Selecione uma configuração do dispositivo

Pode utilizar qualquer uma das seguintes abordagens para a configuração do seu dispositivo:

- **Configuração da cadeia margarida**. Sincronizar um dispositivo principal e até oito dispositivos subordinados.  
   ![Diagrama que mostra como ligar os dispositivos DK do Azure Kinect numa configuração em cadeia de margaridas.](./media/multicam-sync-daisychain.png)
- **Configuração de estrelas**. Sincronizar um dispositivo principal e até dois dispositivos subordinados.  
   ![Diagrama que mostra como configurar vários dispositivos Azure DK numa configuração estelar.](./media/multicam-sync-star.png)

#### <a name="using-an-external-sync-trigger"></a>Usando um gatilho de sincronização externa

Em ambas as configurações, o dispositivo principal fornece o sinal de disparo para os dispositivos subordinados. No entanto, pode utilizar uma fonte externa personalizada para o gatilho de sincronização. Por exemplo, pode utilizar esta opção para sincronizar capturas de imagem com outros equipamentos. Quer na configuração da corrente da margarida, quer na configuração da estrela, a fonte de gatilho externa liga-se ao dispositivo principal.

A fonte de gatilho externa deve funcionar da mesma forma que o dispositivo principal. Deve fornecer um sinal de sincronização com as seguintes características:

- Ativo alto
- Largura do pulso: Maior que 8 &mu; s
- 5V TTL/CMOS
- Capacidade máxima de condução: Não inferior a 8 miliamps (mA)
- Suporte de frequência: Exatamente 30 FPS, 15 FPS e 5 FPS (a frequência do sinal vsyNC mestre da câmara de cor)

A fonte do gatilho deve entregar o sinal ao dispositivo principal **Sync na** porta utilizando um cabo de áudio de 3,5 mm. Pode utilizar um cabo aparelhado ou mono. O Azure Kinect DK calça todas as mangas e anéis do conector do cabo de áudio e os coloca em segurança. Tal como mostrado no diagrama seguinte, o dispositivo recebe apenas o sinal de sincronização da ponta do conector.

![Configurações do cabo para um sinal de gatilho externo](./media/resources/camera-trigger-signal.jpg)

Para obter mais informações sobre como trabalhar com equipamentos [externos, consulte o gravador Azure Kinect com dispositivos sincronizados externos](record-external-synchronized-units.md)

> [!NOTE]  
> Sync out é o VSync para a câmara RGB. Os tempos de fixação de todos os dispositivos estão definidos para zero e contam. A Microsoft não caracterizou a largura min e máxima do pulso de sincronização e recomenda a imitação do pulso gerado por Sync out de um Azure Kinect DK.

### <a name="plan-your-camera-settings-and-software-configuration"></a>Planeie as definições da câmara e a configuração do software

Para obter informações sobre como configurar o seu software para controlar as câmaras e utilizar os dados de imagem, consulte o [Sensor Azure Kinect SDK](about-sensor-sdk.md).

Esta secção discute vários fatores que afetam dispositivos sincronizados (mas não dispositivos únicos). O seu software deve considerar estes fatores.

#### <a name="exposure-considerations"></a>Considerações de exposição
Se pretender controlar o tempo exato de cada dispositivo, recomendamos que utilize uma regulação de exposição manual. Sob a definição de exposição automática, cada câmara de cor pode alterar dinamicamente a exposição real. Como a exposição afeta o tempo, tais mudanças rapidamente empurram as câmaras para fora de sincronização.

No ciclo de captura de imagens, evite definir repetidamente a mesma regulação de exposição. Ligue para a API apenas uma vez quando for necessário.

#### <a name="avoiding-interference-between-multiple-depth-cameras"></a>Evitar interferências entre câmaras de múltiplas profundidades

Quando várias câmaras de profundidade estão a imaginar campos de visão sobrepostos, cada câmara deve imagem do seu próprio laser associado. Para evitar que os lasers interfiram entre si, as capturas da câmara devem ser compensadas umas das outras por 160μs ou mais.

Para cada captura de câmara de profundidade, o laser liga-se nove vezes e está ativo por apenas 125 &mu; s de cada vez. O laser é então inativo para 14505 &mu; s ou 23905 &mu; s, dependendo do modo de funcionamento. Este comportamento significa que o ponto de partida para o cálculo da compensação é de 125 &mu; s.

Além disso, as diferenças entre o relógio da câmara e o relógio de firmware do dispositivo aumentam o mínimo para 160 &mu; s. Para calcular uma compensação mais precisa para a sua configuração, note o modo de profundidade que está a utilizar e consulte a [tabela de cronometragem bruta do sensor de profundidade](hardware-specification.md#depth-sensor-raw-timing). Utilizando os dados desta tabela, pode calcular o mínimo de compensação (o tempo de exposição de cada câmara) utilizando a seguinte equação:

> *Tempo de exposição* =*(Largura* do pulso de pulso &times; de IV) + (Tempo *inativo do* &times; *inativo)*

Quando se usa uma offset de 160 &mu; s, pode configurar até nove câmaras de profundidade adicionais para que cada laser se atrase enquanto os outros lasers estão inativos.

No seu software, utilize ```depth_delay_off_color_usec``` ou ```subordinate_delay_off_master_usec``` certifique-se de que cada laser IR dispara na sua própria janela de 160 &mu; s ou tem um campo de visão diferente.

> [!NOTE]  
> A largura do pulso real é de 125us no entanto, nós afirmamos 160us para fornecer alguma margem de manobra. Tomando NFOV UNBINNED como um exemplo, cada pulso de 125us é seguido por 1450us inativo. Totalizando estes - (9 x 125) + (8 x 1450) - rende o tempo de exposição de 12,8ms. O armário que pode intercalar a exposição de 2 dispositivos é ter o primeiro pulso da segunda câmara a cair no primeiro período inativo da primeira câmara. O atraso entre a primeira e a segunda câmaras pode ser de apenas 125us (a largura de um pulso) no entanto recomendamos alguma margem de manobra, daí o 160us. Dado o 160us pode intercalar os períodos de exposição de um máximo de 10 câmaras.

## <a name="prepare-your-devices-and-other-hardware"></a>Prepare os seus dispositivos e outros equipamentos

Além de vários dispositivos Azure Kinect DK, poderá ter de obter computadores anfitriões adicionais e outros hardware para suportar a configuração que pretende construir. Utilize as informações nesta secção para se certificar de que todos os dispositivos e hardware estão prontos antes de começar a configurar.

### <a name="azure-kinect-dk-devices"></a>Dispositivos Azure Kinect DK

Para cada dispositivo Azure Kinect DK que pretende sincronizar, faça o seguinte:

- Certifique-se de que o firmware mais recente está instalado no dispositivo. Para obter mais informações sobre como atualizar os seus dispositivos, aceda ao [firmware Azure Kinect DK](update-device-firmware.md). 
- Retire a tampa do dispositivo para revelar as portas de sincronização.
- Note o número de série de cada dispositivo. Utilizará este número mais tarde no processo de configuração.

### <a name="host-computers"></a>Computadores anfitriões

Normalmente, cada Azure Kinect DK utiliza o seu próprio computador anfitrião. Pode utilizar um controlador de anfitrião dedicado, dependendo da forma como utiliza o dispositivo e da quantidade de dados que é transferido sobre a ligação USB. 

Certifique-se de que o Sensor Kinect Azure SDK está instalado em cada computador anfitrião. Para obter mais informações sobre como instalar o Sensor SDK, vá ao [Quickstart: Confisque o seu DK Azure Kinect](set-up-azure-kinect-dk.md). 

#### <a name="linux-computers-usb-memory-on-ubuntu"></a>Computadores Linux: memória USB em Ubuntu

Por predefinição, os computadores anfitriões baseados no Linux atribuem o controlador USB apenas 16 MB de memória de kernel para lidar com transferências USB. Este montante é geralmente suficiente para suportar um único Azure Kinect DK. No entanto, para suportar vários dispositivos, o controlador USB tem de ter mais memória. Para aumentar a memória, siga estes passos:

1. Editar /**etc/predefinição/grub**.
1. Localize a seguinte linha:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash"
   ```
   Substitua-a utilizando esta linha:
   ```cmd
   GRUB_CMDLINE_LINUX_DEFAULT="quiet splash usbcore.usbfs_memory_mb=32"
   ```
   > [!NOTE]  
   > Estes comandos definem a memória USB em 32 MB. Este é um exemplo que define em duas vezes o valor predefinido. Pode definir um valor muito maior, conforme apropriado para a sua solução.
1. Executar **sudo actualizar-grub**.
1. Reinicie o computador.

### <a name="cables"></a>Cabos

Para ligar os dispositivos uns aos outros e aos computadores hospedeiros, tem de utilizar cabos masculinos para homens de 3,5 mm (também conhecidos como cabo de áudio de 3,5 mm). Os cabos devem ter menos de 10 metros de comprimento e podem ser estéreo ou mono.

O número de cabos que deve ter depende do número de dispositivos que utiliza e também da configuração específica do dispositivo. A caixa DK Azure Kinect não inclui cabos. Deve comprá-los separadamente.

Se ligar os dispositivos na configuração da estrela, também deve ter um separador de auscultadores.

## <a name="connect-your-devices"></a>Ligar os dispositivos

**Para ligar dispositivos DK Azure Kinect numa configuração de corrente de margarida**

1. Ligue cada Azure Kinect DK à alimentação.
1. Ligue cada dispositivo ao seu PC anfitrião. 
1. Selecione um dispositivo para ser o dispositivo principal e ligue um cabo de áudio de 3,5 mm à sua porta **Sync out.**
1. Ligue a outra extremidade do cabo ao **Sync na** porta do primeiro dispositivo subordinado.
1. Para ligar outro dispositivo, ligue outro cabo à porta **Sync out** do primeiro dispositivo subordinado e ao **Sync na** porta do próximo dispositivo.
1. Repita o passo anterior até que todos os dispositivos estejam ligados. O último dispositivo deve ter apenas uma ligação por cabo. A porta **Sync out** deve estar vazia.

**Para ligar dispositivos DK Azure Kinect numa configuração estelar**

1. Ligue cada Azure Kinect DK à alimentação.
1. Ligue cada dispositivo ao seu PC anfitrião. 
1. Selecione um dispositivo para ser o dispositivo principal e ligue a extremidade única do separador dos auscultadores na sua porta **Sync out.**
1. Ligue os cabos de áudio de 3,5 mm às extremidades "split" do separador dos auscultadores.
1. Ligue a outra extremidade de cada cabo ao **Sync na** porta de um dos dispositivos subordinados.

## <a name="verify-that-the-devices-are-connected-and-communicating"></a>Verifique se os dispositivos estão ligados e comunicam

Para verificar se os dispositivos estão ligados corretamente, utilize [o Observador Kinect Azure](azure-kinect-viewer.md). Repita este procedimento se necessário para testar cada dispositivo subordinado em combinação com o dispositivo principal

> [!IMPORTANT]  
> Para este procedimento, deve conhecer o número de série de cada Azure Kinect DK.

1. Abra duas instâncias do Azure Kinect Viewer.
1. Em **Dispositivo Aberto**, selecione o número de série do dispositivo subordinado que pretende testar.  
   ![Dispositivo aberto](./media/open-devices.png)
   > [!IMPORTANT]  
   > Para obter um alinhamento preciso de captura de imagem entre todos os dispositivos, tem de iniciar o dispositivo principal por último.  
1. Em **Sincronização Externa**, selecione **Sub**.  
   ![Início da câmera subordinada](./media/sub-device-start.png)
1.  Selecione **Iniciar**.  
    > [!NOTE]  
    > Por se trata de um dispositivo subordinado, o Azure Kinect Viewer não exibe uma imagem após o início do dispositivo. Nenhuma imagem é apresentada até que o dispositivo subordinado receba um sinal de sincronização do dispositivo principal.
1. Depois de o dispositivo subordinado ter começado, utilize a outra instância do Azure Kinect Viewer para abrir o dispositivo principal.
1. Em **Sincronização Externa,** selecione **Master**.
1. Selecione **Iniciar**.

Quando o mestre Azure Kinect Device começar, ambas as instâncias do Azure Kinect Viewer devem exibir imagens.

## <a name="calibrate-the-devices-as-a-synchronized-set"></a>Calibrar os dispositivos como um conjunto sincronizado

Depois de verificar se os dispositivos estão a comunicar corretamente, está pronto a calibrar para produzir imagens num único domínio.

Num único dispositivo, a profundidade e as câmaras RGB são calibradas para trabalhar em conjunto. No entanto, quando vários dispositivos têm de trabalhar em conjunto, têm de ser calibrados para determinar como transformar uma imagem do domínio da câmara que a capturou para o domínio da câmara que pretende utilizar para processar imagens.

Existem múltiplas opções para dispositivos de calibração cruzada. A Microsoft fornece a amostra de [código de ecrã verde GitHub](https://github.com/microsoft/Azure-Kinect-Sensor-SDK/tree/develop/examples/green_screen), que utiliza o método OpenCV. O ficheiro Readme para esta amostra de código fornece mais detalhes e instruções para calibrar os dispositivos.

Para obter mais informações sobre a calibração, consulte [as funções de calibração do Azure Kinect](use-calibration-functions.md).

## <a name="next-steps"></a>Passos seguintes

Depois de configurar dispositivos sincronizados, também pode aprender a usar o
> [!div class="nextstepaction"]
> [Azure Kinect sensor SDK grava e reproduz a API](record-playback-api.md)

## <a name="related-topics"></a>Tópicos relacionados

- [Sobre a Azure Kinect Sensor SDK](about-sensor-sdk.md)
- [Especificações de hardware Azure Kinect DK](hardware-specification.md) 
- [Quickstart: Configurar o seu Azure Kinect DK](set-up-azure-kinect-dk.md) 
- [Atualizar firmware Azure Kinect DK](update-device-firmware.md) 
- [Repor o Azure Kinect DK](reset-azure-kinect-dk.md) 
- [Espectador Azure Kinect](azure-kinect-viewer.md) 
