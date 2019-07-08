---
title: Tutorial para ordem pesadas de caixa de dados do Azure | Documentos da Microsoft
description: Aprenda os pré-requisitos de implementação e como encomendar uma pesada de caixa de dados do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: c7fbd37ff8d40f27e0ca18a6f9816d3d96422ab9
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592400"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutorial: Pesado de caixa de dados do Azure de ordem


Pesadas de caixa de dados do Azure é uma solução híbrida que permite-lhe importar os dados no local para o Azure numa rápida, fácil e de forma fiável. Transfira os seus dados para um dispositivo de armazenamento fornecida pelo Microsoft 770 TB (capacidade utilizável de aproximado) e, em seguida, envie o dispositivo novamente. Estes dados são então carregados para o Azure.

Este tutorial descreve como é possível pedir uma pesada de caixa de dados do Azure. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * Pré-requisitos para pesado de caixa de dados
> * Solicite uma sobrecarga de caixa de dados
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

Conclua os pré-requisitos de configuração seguintes para o serviço e dispositivo do Data Box antes de implementar o dispositivo.

### <a name="for-installation-site"></a>Para o site de instalação

Antes de começar, certifique-se de que:

- O dispositivo se encaixa por meio de doorways padrão e entryways. No entanto, certifique-se de que o dispositivo pode se encaixar através de todos os seus entryways. Dimensões de dispositivo são: largura: 26" comprimento: 48" altura: 28”.
- Se instalado num chão que não seja o piso de zero, precisa de acesso para o dispositivo através de um elevador ou de uma plataforma de lançamento. O dispositivo examina aproximadamente ~ 500 lbs.
- Certifique-se de que tem um site simples no Centro de dados com a proximidade para uma ligação de rede disponíveis que pode acomodar um dispositivo com esta presença.


### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
    - Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Certifique-se de que tem acesso de proprietário ou contribuinte à subscrição para criar uma ordem de dados de caixa pesada.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:
- O dispositivo está descompactado.
- Deve ter um computador anfitrião ligado à rede do datacenter. Dados de caixa pesada irá copiar os dados deste computador. O computador anfitrião tem de executar um sistema operativo suportado conforme descrito em [requisitos de sistema pesadas de caixa de dados do Azure](data-box-system-requirements.md).
- Tem de ter um laptop com o cabo de RJ-45 para ligar à interface do usuário local e configurar o dispositivo. Utilize o laptop para configurar uma vez a cada nó do dispositivo.
- O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE.
- Precisa de um 40 Gbps ou por nó de dispositivo de cabo de 10 Gbps. Escolha cabos que são compatíveis com o [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) interface de rede:

    - Para o cabo de 40 Gbps, a fim de dispositivo do cabo tem de ser QSFP +.
    - Para o cabo de 10 Gbps, terá de um cabo de SFP + que se conecta um comutador de 10 G numa extremidade, com um QSFP + SFP + adaptador (ou o adaptador de qsa independente) para o end que se conecta o dispositivo.
    - Os cabos de energia são incluídos com o dispositivo.

## <a name="order-data-box-heavy"></a>Pesado de caixa de dados de ordem

Execute os passos seguintes no Portal do Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [ https://portal.azure.com ](https://portal.azure.com).
2. Selecione **+ criar um recurso** e procure *do Azure Data Box*. Selecione **do Azure Data Box**.
    
   [![Procurar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selecione **Criar**.

4. Verifique se o serviço do Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e selecione **aplicar**.

    |Definição  |Valor  |
    |---------|---------|
    |Subscrição     | Selecione uma subscrição de patrocínio EA, CSP ou do Azure para o serviço do Data Box. <br> A subscrição está ligada à sua conta de faturação.       |
    |Tipo de transferência     | Selecione **Importar para o Azure**.        |
    |País de origem     | Selecione o país/região onde residem atualmente seus dados.         |
    |Região do Azure de destino     | Selecione a região do Azure para onde pretende transferir os dados.        |

    [![Selecionar família de disponibilidade do Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selecione **dados de caixa pesados**. A capacidade máxima utilizável para uma única encomenda é 770 TB.

    [![Selecione pesado de caixa de dados](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. Em **Encomenda**, especifique os **Detalhes da encomenda**. Introduza ou selecione as seguintes informações e selecione **seguinte**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Name     | Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.      |
    |Grupo de recursos     | Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto.         |
    |Região do Azure de destino     | Selecione uma região para a sua conta de armazenamento. <br> Para obter mais informações, aceda a [Disponibilidade de região](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Destino de armazenamento     | Escolha entre a conta de armazenamento ou discos geridos ou ambos. <br> Com base na região do Azure especificada, selecione uma ou mais contas de armazenamento na lista filtrada de uma conta de armazenamento existente. <br>Dados de caixa pesadas podem ser associadas com até 10 contas de armazenamento. <br> Também pode criar uma nova **para fins gerais v1**, **para fins gerais v2**, ou **conta de armazenamento de BLOBs**. <br> Contas de armazenamento Gen 2 do Azure Data Lake não são suportadas. Consulte a [contas de armazenamento suportadas com o seu dispositivo](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Contas de armazenamento com redes virtuais são suportadas. Para permitir que o serviço do Data Box trabalhar com contas de armazenamento seguro, ative os serviços confiáveis dentro de definições de firewall de rede da conta de armazenamento. Para obter mais informações, consulte como [serviço de adicionar o Azure Data Box como um serviço de confiança](../storage/common/storage-network-security.md#exceptions).|

    Se utilizar a conta de armazenamento como o destino de armazenamento, verá a seguinte captura de ecrã:

    ![Ordem de caixa pesadas de dados para a conta de armazenamento](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Se para além da conta de armazenamento como o destino de armazenamento, também estiver a utilizar dados de caixa pesadas para criar discos geridos a partir dos VHD no local, terá de fornecer as seguintes informações:

    |Definição  |Value  |
    |---------|---------|
    |Grupos de recursos     | Crie novos grupos de recursos se pretende criar discos geridos a partir de VHDs no local. Pode utilizar um grupo de recursos existente apenas se o grupo de recursos foi criado anteriormente durante a criação de uma ordem pesadas de caixa de dados para o disco gerido pelo serviço do Data Box. <br> Especificar vários grupos de recursos separados por ponto e vírgula. Um máximo de 10 grupos de recursos são suportadas.|

    ![Ordem de caixa pesadas de dados para o disco gerido](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para discos geridos é utilizada como uma conta de armazenamento de teste. Os carregamentos de serviço do Data Box os VHDs como página de blobs para a conta de armazenamento de teste antes de converter este para discos geridos e movê-lo para os grupos de recursos. Para obter mais informações, consulte [verificar dados carregar para o Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Selecione **validar morada**. 

    O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito. Selecione **Seguinte**.

8. Em **Detalhes de notificação**, especifique os endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

9. Reveja o **Resumo** das informações relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

10. Selecione **ordem**. A encomenda demora alguns minutos a ser criada.


## <a name="track-the-order"></a>Controlar a encomenda

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá para a sua encomenda pesadas de caixa de dados e, em seguida, aceda a **descrição geral** para ver o estado. O portal mostra a encomenda no estado **Encomendado**.

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as ações seguintes:

- São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo.
- Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
- A palavra-passe do dispositivo, que ajuda a desbloqueá-lo, também é gerada.
- A pesadas de caixa de dados está bloqueada para impedir o acesso não autorizado ao dispositivo, a qualquer momento.

Quando a preparação do dispositivo estiver concluída, o portal mostra a encomenda no estado **Processado**.

![Ordem de caixa pesadas de dados processados](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft prepara e expede os seus dispositivos através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Ordem caixa pesada dos dados distribuído](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar a encomenda

Para cancelar esta encomenda, no portal do Azure, aceda a **Descrição Geral** e clique em **Cancelar** na barra de comando.

Depois de efetuar uma encomenda, pode cancelá-la a qualquer momento antes de o estado desta ser marcado como processado.
 
Para eliminar uma encomenda cancelada, aceda a **Descrição Geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, aprendeu sobre tópicos pesadas de caixa de dados do Azure, tais como:

> [!div class="checklist"]
> * Pré-requisitos
> * Pesado de caixa de dados de ordem
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o próximo tutorial para saber como configurar a sua pesadas de caixa de dados.

> [!div class="nextstepaction"]
> [Configurar a sua pesadas de caixa de dados do Azure](./data-box-heavy-deploy-set-up.md)
