---
title: Tutorial para ordenar Disco do Azure Data Box | Microsoft Docs
description: Utilize este tutorial para saber como inscrever-se e encomendar um disco do Azure Data Box Disk para importar dados para o Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: c1626542aba71f971ad96fe9f187ce9f1c1a99f3
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012931"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>Tutorial: Ordenar um Disco do Azure Data Box

O Azure Data Box Disk é uma solução na cloud híbrida que permite importar os dados no local para o Azure, de forma rápida, fácil e fiável. Transfira os seus dados para discos de estado sólido (SSDs) fornecidos pela Microsoft e reenvie os discos. Estes dados são então carregados para o Azure.

Este tutorial descreve como pode encomendar um disco do Azure Data Box Disk. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * Encomendar um disco do Data Box Disk
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

Antes de implantar o, conclua os seguintes pré-requisitos de configuração para Data Box serviço e Disco do Data Box.

### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
    - Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
- Certifique-se de que tem acesso de proprietário ou de contribuinte à subscrição para criar uma encomenda do Data Box.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:
- Você tem um computador cliente disponível do qual você pode copiar os dados. O computador cliente tem de:
    - Executar um [sistema operativo suportado](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ter outro [software necessário](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) instalado se for um cliente Windows.  

## <a name="order-data-box-disk"></a>Encomendar o Data Box Disk

Entrar em:

- O portal do Azure nesta URL: https://portal.azure.com para solicitar disco do data box.
- Ou o portal do Azure governamental nesta URL: https://portal.azure.us. Para obter mais detalhes, acesse [conectar-se ao Azure governamental usando o portal](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

Execute as etapas a seguir para ordenar Disco do Data Box.

1. No canto superior esquerdo do portal, clique em **+ Criar um recurso**e procure *Azure Data Box*. Clique em **Azure Data Box**.
    
   ![Pesquisar o Azure Data Box 1](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. Clique em **Criar**.

3. Verifique se o serviço Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e clique em **Aplicar**.

    ![Selecionar a opção Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |Definição|Valor|
    |---|---|
    |Subscription|Selecione a subscrição para o qual o serviço Data Box está ativado.<br> A subscrição está ligada à sua conta de faturação. |
    |Tipo de transferência| Importar para o Azure|
    |País de origem | Selecione o país/região onde os dados residem no momento.|
    |Região do Azure de destino|Selecione a região do Azure para onde pretende transferir os dados.|

  
5.  Selecione **Data Box Disk**. A capacidade máxima da solução para uma encomenda única de 5 discos é 35 TB. Pode criar várias encomendas para tamanhos de dados maiores.

     ![Selecionar a opção Data Box Disk](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

6.  Em **Encomenda**, especifique os **Detalhes da encomenda**. Introduza ou selecione as seguintes informações.

    |Definição|Valor|
    |---|---|
    |Name|Forneça um nome amigável para controlar a encomenda.<br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número. |
    |Resource group| Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto. |
    |Região do Azure de destino| Selecione uma região para a sua conta de armazenamento.<br> Atualmente, são suportadas contas de armazenamento em todas as regiões nos EUA, na Europa Ocidental e Europa do Norte, no Canadá e na Austrália. |
    |Tamanho de dados estimado em TB| Introduza uma estimativa em TB. <br>Com base no tamanho dos dados, a Microsoft envia um número adequado de SSDs de 8 TB (capacidade utilizável de 7 TB). <br>A capacidade máxima utilizável de 5 discos é 35 TB. |
    |Chave de acesso de disco| Indique a chave de acesso do disco, se assinalar **Utilizar a chave personalizada em vez da chave de acesso gerada pelo Azure**. <br> Forneça uma chave alfanumérica de 12 a 32 caracteres que tenha pelo menos um caractere numérico e um especial. Os carateres especiais permitidos são `@?_+`. <br> Pode optar por ignorar esta opção e utilizar a chave de acesso gerada pelo Azure para desbloquear os discos.|
    |Destino de armazenamento     | Escolha entre a conta de armazenamento ou os discos gerenciados ou ambos. <br> Com base na região do Azure especificada, selecione uma conta de armazenamento na lista filtrada de uma conta de armazenamento existente. Disco do Data Box pode ser vinculado somente a uma conta de armazenamento. <br> Você também pode criar uma nova **conta de armazenamento de blob** **v1**, de **uso geral v2**ou de uso geral. <br>Há suporte para contas de armazenamento com redes virtuais. Para permitir que Data Box serviço funcione com contas de armazenamento protegidas, habilite os serviços confiáveis nas configurações de firewall de rede da conta de armazenamento. Para obter mais informações, consulte como [adicionar Azure data box como um serviço confiável](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).|

    Se estiver usando a conta de armazenamento como o destino de armazenamento, você verá a seguinte captura de tela:

    ![Ordem de Disco do Data Box para a conta de armazenamento](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Se estiver usando Disco do Data Box para criar discos gerenciados a partir dos VHDs locais, você também precisará fornecer as seguintes informações:

    |Definição  |Value  |
    |---------|---------|
    |Resource group     | Crie um novo grupo de recursos se você pretende criar discos gerenciados de VHDs locais. Use um grupo de recursos existente somente se ele tiver sido criado para Disco do Data Box ordem para o disco gerenciado pelo serviço Data Box. <br> Há suporte para apenas um grupo de recursos.|

    ![Ordem de Disco do Data Box do disco gerenciado](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço de Data Box carrega os VHDs para a conta de armazenamento de preparo e, em seguida, converte-os em discos gerenciados e move para os grupos de recursos. Para obter mais informações, consulte [verificar o carregamento de dados no Azure](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure).

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
