---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8b827a80059cecadd7b76263f13dd4a0d141fc7a
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559442"
---
#### <a name="to-create-a-cloud-appliance"></a>Para criar uma aplicação da cloud

1. No portal do Azure, aceda ao serviço **Gestor de Dispositivos do StorSimple**.
2. Aceda ao painel **Dispositivos**. A partir da barra de comando no painel de resumo de serviço, clique em **Criar aplicação da cloud**.
    ![StorSimple cria aplicação da cloud](./media/storsimple-8000-create-cloud-appliance-u2/sca-create1.png)
3. No painel **Criar aplicação da cloud**, especifique os seguintes detalhes.
   
    ![StorSimple criar aparelho em nuvem 2](./media/storsimple-8000-create-cloud-appliance-u2/sca-create2m.png)
   
   1. **Nome** – um nome exclusivo para a sua aplicação da cloud.
   2. **Modelo** – escolha o modelo da aplicação da cloud. Um dispositivo 8010 oferece 30 TB de armazenamento Standard ao passo que o 8020 tem 64 TB de armazenamento Premium. Especifique o 8010 para implementar cenários de obtenção ao nível de itens a partir de cópias de segurança. Selecione 8020 para implementar cargas de trabalho de elevado desempenho e de baixa latência ou para utilizar como um dispositivo secundário para recuperação após desastre.
   3. **Versão** – escolha a versão da aplicação da cloud. A versão corresponde à versão da imagem de disco virtual que é utilizada para criar a aplicação da cloud. Uma vez que a versão da aplicação da cloud irá determinar que dispositivo físico poderá utilizar para a ativação pós-falha ou para a clonagem, é importante que crie uma versão adequada da aplicação da cloud.
   4. **Rede virtual** – especifique uma rede virtual que pretende utilizar com esta aplicação da cloud. Se utilizar o Armazenamento Premium, tem de selecionar uma rede virtual que seja suportada com a Conta do Armazenamento Premium. As redes virtuais não suportadas estão indisponíveis na lista pendente. É avisado se selecionar uma rede virtual não suportada.
   5. **Sub-rede** - com base na rede virtual selecionada, a lista pendente apresenta as sub-redes associadas. Atribua uma sub-rede à sua aplicação da cloud.
   6. **Conta de armazenamento** – selecione uma conta de armazenamento para armazenar a imagem da aplicação da cloud durante o aprovisionamento. Esta conta de armazenamento deve estar na mesma região que a aplicação da cloud e a rede virtual. Não deverá ser utilizada para armazenamento de dados da aplicação da cloud ou física. Por predefinição, é criada uma nova conta de armazenamento para esta finalidade. No entanto, se souber que já tem uma conta de armazenamento adequada para esta utilização, poderá selecioná-la na lista. Se criar uma aplicação da cloud premium, a lista pendente apresenta apenas contas de Armazenamento Premium.
      
      > [!NOTE]
      > A aplicação da cloud só pode ser utilizada com as contas de armazenamento do Azure.
    
   7. Selecione a caixa de verificação para indicar que entende que os dados armazenados na aplicação da cloud são alojados num datacenter da Microsoft.
       * Se utilizar apenas um dispositivo físico, a sua chave de encriptação é mantida com o dispositivo. Por conseguinte, a Microsoft não a poderá desencriptar.

       * Se utilizar uma aplicação da cloud, a chave de encriptação e a chave de desencriptação são armazenadas no Microsoft Azure. Para obter mais informações, veja [security considerations for using a cloud appliance](../articles/storsimple/storsimple-8000-security.md) (considerações de segurança para utilizar uma aplicação da cloud).
   8. Clique em **Criar** para aprovisionar a aplicação da cloud. O dispositivo poderá demorar cerca de 30 minutos a ser aprovisionado. Será notificado quando a aplicação da cloud for criada com êxito. Aceda ao painel Dispositivos e a lista de dispositivos atualiza para apresentar a aplicação da cloud. O estado da aplicação é **Pronta para configurar**.
      
      ![StorSimple Cloud Appliance pronta para configurar](./media/storsimple-8000-create-cloud-appliance-u2/sca-create3.png)