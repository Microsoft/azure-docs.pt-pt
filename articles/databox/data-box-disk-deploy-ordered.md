---
title: Tutorial para encomendar o Microsoft Azure Data Box Disk | Microsoft Docs
description: Utilize este tutorial para saber como inscrever-se e encomendar um disco do Azure Data Box Disk para importar dados para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 02/27/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 67f4eb5383452a81ba288f5fe611242259217951
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404902"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Tutorial: Solicite um disco de caixa de dados do Azure

O Azure Data Box Disk é uma solução na cloud híbrida que permite importar os dados no local para o Azure, de forma rápida, fácil e fiável. Transfira os seus dados para discos de estado sólido (SSDs) fornecidos pela Microsoft e reenvie os discos. Estes dados são então carregados para o Azure.

Este tutorial descreve como pode encomendar um disco do Azure Data Box Disk. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * Encomendar um disco do Data Box Disk
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

Antes de implementar, conclua os seguintes pré-requisitos de configuração para o serviço do Data Box e disco Data Box.

### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
    - Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Certifique-se de que tem acesso de proprietário ou de contribuinte à subscrição para criar uma encomenda do Data Box.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:
- Possui um computador de cliente disponível do qual pode copiar os dados. O computador cliente tem de:
    - Executar um [sistema operativo suportado](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ter outro [software necessário](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado se for um cliente Windows.  

## <a name="order-data-box-disk"></a>Encomendar o Data Box Disk

Execute os seguintes passos no [portal do Azure](https://aka.ms/azuredataboxfromdiskdocs) para encomendar o Data Box Disk.

1. No canto superior esquerdo do portal, clique em **+ Criar um recurso**e procure *Azure Data Box*. Clique em **Azure Data Box**.
    
   ![Pesquisar o Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Clique em **Criar**.

3. Verifique se o serviço Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e clique em **Aplicar**.

    ![Selecionar a opção Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Definição|Valor|
    |---|---|
    |Subscrição|Selecione a subscrição para o qual o serviço Data Box está ativado.<br> A subscrição está ligada à sua conta de faturação. |
    |Tipo de transferência| Importar para o Azure|
    |País de origem | Selecione o país onde os dados residem atualmente.|
    |Região do Azure de destino|Selecione a região do Azure para onde pretende transferir os dados.|

  
5.  Selecione **Data Box Disk**. A capacidade máxima da solução para uma encomenda única de 5 discos é 35 TB. Pode criar várias encomendas para tamanhos de dados maiores.

     ![Selecionar a opção Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  Em **Encomenda**, especifique os **Detalhes da encomenda**. Introduza ou selecione as seguintes informações.

    |Definição|Valor|
    |---|---|
    |Name|Forneça um nome amigável para controlar a encomenda.<br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número. |
    |Grupo de recursos| Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. |
    |Região do Azure de destino| Selecione uma região para a sua conta de armazenamento.<br> Atualmente, são suportadas contas de armazenamento em todas as regiões nos EUA, na Europa Ocidental e Europa do Norte, no Canadá e na Austrália. |
    |Tamanho de dados estimado em TB| Introduza uma estimativa em TB. <br>Com base no tamanho dos dados, a Microsoft envia um número adequado de SSDs de 8 TB (capacidade utilizável de 7 TB). <br>A capacidade máxima utilizável de 5 discos é 35 TB. |
    |Chave de acesso de disco| Indique a chave de acesso do disco, se assinalar **Utilizar a chave personalizada em vez da chave de acesso gerada pelo Azure**. <br> Forneça uma chave de alfanuméricos de 12 a 32 carateres que tenha pelo menos numérico e um caráter especial. Os carateres especiais permitidos são `@?_+`. <br> Pode optar por ignorar esta opção e utilizar a chave de acesso gerada pelo Azure para desbloquear os discos.|
    |Destino de armazenamento     | Escolha entre a conta de armazenamento ou discos geridos ou ambos. <br> Com base na região do Azure especificada, selecione uma conta de armazenamento na lista filtrada de uma conta de armazenamento existente. O Data Box pode ser associado a um máximo de 10 contas de armazenamento. <br> Também pode criar uma nova **para fins gerais v1**, **para fins gerais v2**, ou **conta de armazenamento de BLOBs**. <br>Não é possível utilizar contas de armazenamento que têm regras configuradas. O armazenamento de contas deve **permitir o acesso de todas as redes** as firewalls e a secção de redes virtuais.|

    Se utilizar a conta de armazenamento como o destino de armazenamento, verá a seguinte captura de ecrã:

    ![Encomenda do disco de caixa de dados para a conta de armazenamento](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Se utilizar o disco Data Box para criar discos geridos a partir dos VHD no local, terá também de fornecer as seguintes informações:

    |Definição  |Value  |
    |---------|---------|
    |Grupo de recursos     | Crie um novo grupo de recursos se pretende criar discos geridos a partir de VHDs no local. Utilize um grupo de recursos existente apenas se ele foi criado para encomenda do disco de caixa de dados para o disco gerido pelo serviço do Data Box. <br> Apenas um grupo de recursos é suportado.|

    ![Encomenda do disco de caixa de dados para o disco gerido](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para discos geridos é utilizada como uma conta de armazenamento de teste. O serviço do Data Box carrega os VHDs para a conta de armazenamento temporário e, em seguida, converte-las em discos geridos e move para os grupos de recursos. Para obter mais informações, consulte [verificar dados carregar para o Azure](data-box-disk-deploy-picked-up.md#verify-data-upload-to-azure).

13. Clique em **Seguinte**.

    ![Fornecer os detalhes da encomenda](media/data-box-disk-deploy-ordered/data-box-order-details.png)

14. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito. 

    ![Fornecer o endereço de envio](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
15. Em **Detalhes de notificação**, especifique os endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados. 

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

16. Reveja o **Resumo** das informações relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

17. Clique em **Encomendar**. A encomenda demora alguns minutos a ser criada.

 
## <a name="track-the-order"></a>Controlar a encomenda

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá para a sua encomenda e, em seguida, aceda a **Descrição Geral** para ver o estado. O portal mostra a tarefa no estado **Encomendado**.

![Estado do disco do Data Box Disk encomendado](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png) 

Se os discos não estiverem disponíveis, receberá uma notificação. Se os discos estiverem disponíveis, a Microsoft identifica os discos para envio e prepara o pacote de discos. Durante a preparação dos discos, as ações seguintes ocorrem:

- Os discos são encriptados com a encriptação AES-128 BitLocker.  
- Os discos são bloqueados para impedir qualquer acesso não autorizado aos mesmos.
- A chave de acesso que desbloqueia os discos é gerada durante este processo.

Quando a preparação dos discos estiver concluída, o portal mostra a encomenda no estado **Processado**.

A Microsoft prepara e expede então os seus discos através de uma operadora regional. Receberá um número de controlo assim que os discos forem enviados. O portal mostra a encomenda no estado **Expedido**.

## <a name="cancel-the-order"></a>Cancelar a encomenda

Para cancelar esta encomenda, no portal do Azure, aceda a **Descrição Geral** e clique em **Cancelar** na barra de comando.

Só pode cancelar quando os discos tiverem sido encomendados e a encomenda estiver a ser processada para envio. Assim que a encomenda for processada, já não pode cancelar a encomenda.

![Cancelar encomenda](media/data-box-disk-deploy-ordered/cancel-order1.png)

Para eliminar uma encomenda cancelada, aceda a **Descrição Geral** e clique em **Eliminar** na barra de comandos.


## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Encomendar o Data Box Disk
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o próximo tutorial para saber como configurar o Data Box Disk.

> [!div class="nextstepaction"]
> [Configurar o seu disco do Azure Data Box Disk](./data-box-disk-deploy-set-up.md)
