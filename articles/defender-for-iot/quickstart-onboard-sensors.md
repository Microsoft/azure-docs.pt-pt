---
title: 'Quickstart: Sensores a bordo no Azure Defender para IoT'
description: Saiba como embarcar sensores para a utilização de IoT no Azure Defender.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2020
ms.author: rkarlin
ms.openlocfilehash: 28d64f5d9d845c2a14f0277799213501e1d68010
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421769"
---
# <a name="quickstart-deploy-and-onboard-a-sensor"></a>Quickstart: Implementar e embarcar um sensor

Este artigo fornece uma visão geral do processo de implantação do sensor. Os sensores devem estar a bordo do Azure Defender para o portal IoT.

Este processo requer a aquisição de um sensor pré-configurado ou a aquisição de um aparelho de sensor certificado e a instalação do software do sensor.

Se estiver a trabalhar com um aparelho de sensor certificado, é aconselhável rever o guia de especificações de [hardware Azure Defender para IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) antes de começar.


> [!NOTE]
> O Azure Defender para a consola de gestão IoT no local não precisa de ser a bordo.


Os sensores de bordo permitem:

|||
|------ | ----------- |
| **Definir um nome de sensor** | Nomeie o sensor que está a bordo e associe-o a um Hub IoT ou subscrição.<br /><br />Consulte **os sensores a bordo** para obter mais informações.|
|**Escolha uma subscrição e número de dispositivos comprometidos**|Selecione uma subscrição e o número de dispositivos abrangidos pela subscrição. Introduza o número em incrementos de 1000.|
| **Defina um modo de gestão de sensores** | Defina onde o ativo, alerta e outras informações detetadas pelo sensor são apresentados. Isto é determinado com base no **modo de gestão do sensor** que define.<br /><br />**Modo gerido localmente** : A informação detetada pelo sensor é apresentada na consola do sensor. As informações de deteção também são partilhadas com a consola de gestão no local se o sensor estiver ligado à sua.<br /><br />**Modo gerido pela nuvem** : A informação detetada pelo sensor é apresentada na consola do sensor. Além disso, as informações de alerta são entregues através de um Hub IoT e podem ser partilhadas com outros serviços Azure, por exemplo, Azure Sentinel.<br />Consulte **o sensor a bordo** para obter mais informações. |
| **Adquira um ficheiro de ativação de sensores** | Para sensores **geridos localmente,** é utilizado um ficheiro de ativação para gerir períodos de ativação de sensores autorizados.<br /><br />Para os sensores **geridos pela Cloud,** um ficheiro de ativação é utilizado como uma ligação entre o sensor e um Hub IoT. Para mais informações, consulte **os sensores a bordo.** |
| **Faça o upload de um ficheiro de ativação para o seu sensor** | Os ficheiros de ativação devem ser enviados para os sensores da empresa. A monitorização da rede e o acesso às funcionalidades da consola de sensores não estarão disponíveis até que o ficheiro de ativação seja carregado. Para obter mais informações, consulte **os ficheiros de ativação do sensor upload**. |
| **Atualizar os parâmetros da rede de sensores antes da ativação** | Atualizar parâmetros definidos durante a instalação do sensor. Para mais informações, consulte **Error! Fonte de referência não encontrada.**|


**Para implantar um sensor:**

1. Vá ao Azure Defender para IoT a partir do portal Azure.

2. **Selecione Começar.**

3. Na secção Descobrir a **sua rede,** selecione **Configurar**.

   ![Azure Defender para IoT descubra a sua visão de rede](media/updates/image5.png)

4. Selecione uma opção para adquirir um sensor.

   ![Azure Defender para a visão do sensor de rede IoT](media/updates/image6.png)

  - **Compre um sensor pré-configurado** : A Microsoft e a Arrow têm uma parceria para fornecer sensores pré-configurados. Para adquirir um sensor pré-configurado, contacte a Seta em: <hardware.sales@arrow.com> . O sensor será entregue nas suas instalações. A versão mais atual está instalada.

  - **Traga o seu próprio aparelho (INSTALAÇÃO ISO)** : A solução funciona em aparelhos certificados. Utilize o Guia de Especificações de [Hardware IoT do Azure Defender](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) como referência na compra do seu aparelho certificado.

    - Selecione uma versão do menu **de versão Select.**

    - Selecione **Baixar** e guardar o ficheiro. Consulte o **Azure Defender para obter** informações sobre o descarregamento da imagem ISO e a instalação do software do sensor.

5. Depois de o software ser instalado no seu sensor ou de ter recebido um sensor pré-configurado, efetuar tarefas de configuração da rede. Consulte o [guia de configuração da Rede](https://aka.ms/AzureDefenderForIoTNetworkSetup) para obter mais detalhes.

## <a name="onboard-a-sensor"></a>A bordo de um sensor

Os sensores precisam de ser acedidos ao Azure Defender para o portal IoT. O embarque é realizado em duas fases:

1. Registe o sensor com o Azure Defender para o portal IoT.

2. Descarregue um ficheiro de ativação para o sensor. O ficheiro será posteriormente enviado para o seu sensor.

**Para aceder à página de bordo:**

1. Navegue para o Microsoft Azure Defender para ioT **Começar a** página.

2. Selecione **2) A bordo** do Azure Defender para IoT.

   ![Screenshot da vista da página do mar](media/updates/image7.png)

3. A página **do sensor de bordo** abre. Fornece opções para registar o sensor e descarregar o ficheiro de ativação.

   ![Screenshot da vista da página do sensor overboard](media/quickstart/onboard-sensors.png)

**Para registar o sensor:**

1. Escolha um nome de sensor. Para os sensores Cloud Managed, o nome aqui definido é aplicado o nome que aparece na consola do sensor: este nome não pode ser alterado a partir da consola. Para sensores geridos localmente, o nome aqui aplicado será armazenado no Azure, mas pode ser atualizado na consola de sensores. Recomenda-se incluir o endereço IP do sensor que instalou como parte do nome ou utilizar um nome facilmente identificável. Isto irá garantir um rastreio mais fácil e um nome consistente entre o nome de registo no portal Azure Defender para IoT e o IP do sensor implantado exibido na consola sensor.

2. Selecione uma subscrição a partir do dropdown de subscrição.
3. No campo **dispositivos Comprometidos,** insira o número de dispositivos abrangidos pela subscrição. Deverá reintrodutar este número para cada sensor que estiver a bordo que esteja associado à mesma subscrição.  Por exemplo, se a subscrição "A" estiver associada a 6000 dispositivos, introduza 6000 para cada sensor associado à subscrição "A".   
4. Escolha um modo de gestão de sensores utilizando o toggle **de ligação Cloud.** Se o toggle estiver ligado, o sensor é **gerido pela Cloud**. Se o toggle estiver desligado, o sensor é **gerido localmente**.


| Modo de Gestão de Sensores | Description                                                |
| ---------------------- | ---------------------------------------------------------  |
| **Cloud Gerida**          | As informações detetadas pelo sensor são apresentadas na consola do sensor. Além disso, as informações de alerta são entregues através de um Hub IoT e podem ser partilhadas com outros serviços Azure, por exemplo, Azure Sentinel.<br /><br />Escolha um Hub IoT para associar a este sensor.<br /><br />Terá de carregar um ficheiro de ativação Cloud Managed para sensores Cloud Managed.<br /><br />Consulte **os ficheiros de ativação do sensor upload** para obter mais detalhes. |
| **Gerido localmente**        | A informação detetada pelos sensores que são Geridos Localmente é apresentada na consola do sensor. Se estiver a trabalhar numa rede com lacunas de ar e quiser uma visão unificada de toda a informação detetada por vários sensores geridos localmente, trabalhe com a consola de gestão no local.<br /><br />Os sensores que são *geridos localmente* estão associados a uma Subscrição Azure e contêm instruções sobre o sensor do período de validade da ativação.<br /><br />Escolha uma subscrição para associar a este sensor.<br /><br />Terá de carregar um ficheiro de ativação gerido localmente para cada sensor. Consulte **os ficheiros de ativação do sensor upload** para obter mais detalhes. |

5. Selecione **Registar**.

6. Na página de ficheiro de ativação de descarregamento **selecione Ficheiro de ativação de descarregamento.**

   ![Screenshot da vista do sensor a bordo](media/updates/image9.png)

7. Guarde o ficheiro. O nome do ficheiro é formato: `<hub_name>_<sensor_name>` . O nome do sensor refere-se ao nome que definiu acima.

8. Selecione **Concluir**.

9. Para completar o processo de embarque do sensor, faça o upload do ficheiro para o seu sensor a partir do Azure Defender para a consola IoT.
 
## <a name="upload-a-sensor-activation-file"></a>Faça upload de um ficheiro de ativação de sensores

Este artigo descreve como carregar um ficheiro de ativação para o sensor. Devia ter recebido o ficheiro ao embarcar no sensor.

**Ficheiros de ativação geridos localmente**

Os sensores geridos localmente estão associados a uma subscrição do Azure.  O ficheiro de ativação dos sensores geridos localmente contém uma data de validade. Um mês antes desta data, uma mensagem de aviso aparece no topo da consola do sensor. O aviso permanece até que tenha atualizado o ficheiro de ativação.

Pode continuar a trabalhar com o Azure Defender para funcionalidades IoT após o fim do ficheiro de ativação.

**Ficheiros de ativação geridos em nuvem**

Os sensores geridos pela nuvem estão associados a um Hub IoT Azure. Estes sensores não são limitados por períodos de tempo do ficheiro de ativação. O ficheiro de ativação de sensores geridos na nuvem é utilizado para garantir a ligação ao Hub IoT.

Para mais detalhes sobre ioT Hubs, consulte [About IoT Hub](../iot-hub/about-iot-hub.md).

**Para carregar o ficheiro de ativação:**

1. Verifique se tem o seguinte:

   - Endereço IP para o sensor - definido durante a instalação

   - Credenciais de login do utilizador necessárias para o sensor

2. Aceda ao Azure Defender para consola IoT a partir do seu navegador utilizando o endereço IP para este sensor.

3. Faça login no Azure Defender para consola de sensores IoT.

   ![Azure Defender para registo de consola IoT à vista](media/updates/image11.png)

4. Após o login bem sucedido, o ecrã de Ativação abre-se. Selecione Upload e escolha o ficheiro de ativação que guardou.

   ![Azure Defender para a vista de ativação IoT](media/updates/image12.png)

5. Aprove os termos e condições.

6. **Selecione Ativar**. Os parâmetros de configuração da rede do sensor foram definidos durante a instalação do software ou quando um sensor pré-configurado foi adquirido. Foram definidos os seguintes parâmetros:
   - Endereço IP
   - DNS  
   - Gateway predefinido
   - Máscara de sub-rede
   - Nome do anfitrião
 
   Pode querer atualizar estas informações antes de ativar o sensor porque:
   - É necessário alterar os parâmetros pré-configurados definidos  
   - Pretende reconfigurar os parâmetros de rede após a instalação, pode também definir definições de procuração antes de ativar o seu sensor.

7. Selecione a **ligação de configuração da rede de sensores** formar a caixa de diálogo de ativação.  

   ![Screenshot da visualização da configuração da rede de edição](media/updates/image13.png)

8. Os parâmetros definidos durante a instalação são apresentados. Uma opção também está disponível para definir proxy. Atualizar conforme necessário e selecionar **Guardar**.
 
 
## <a name="next-steps"></a>Passos seguintes

Neste artigo, aprendeu a implantar e a bordo de sensores. Para saber mais sobre o início, consulte o seguinte artigo:

- [Introdução](getting-started.md)
