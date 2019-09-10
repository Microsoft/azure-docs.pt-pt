---
title: Tutorial para encomendar o Azure Data Box Heavy | Microsoft Docs
description: Conheça os pré-requisitos da implementação e como encomendar um Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70241395"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutorial: Encomendar o Azure Data Box Heavy


O Azure Data Box Heavy é uma solução híbrida que permite importar os dados no local para o Azure, de forma rápida, fácil e fiável. Transfira os dados para um dispositivo de armazenamento de 770 TB (capacidade aproximada utilizável) fornecido pela Microsoft e, em seguida, envie novamente o dispositivo. Estes dados são então carregados para o Azure.

Este tutorial descreve como pode encomendar um Azure Data Box Heavy. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * Pré-requisitos do Data Box Heavy
> * Encomendar um Data Box Heavy
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

Conclua os pré-requisitos de configuração seguintes para o serviço e dispositivo do Data Box antes de implementar o dispositivo.

### <a name="for-installation-site"></a>Para instalação no local

Antes de começar, certifique-se de que:

- O dispositivo passa nas entradas e portas padrão. Verifique também se o dispositivo passa em todas as portas. As dimensões do dispositivo são: largura: 66 cm; comprimento: 122 cm; altura: 71 cm.
- Se pretender instalar o dispositivo num piso que não seja o rés-do-chão, necessitará de um elevador ou de uma rampa. O dispositivo pesa aproximadamente 226 kg.
- Confirme que possui um local plano no datacenter próximo de uma ligação de rede disponível que possa acomodar um dispositivo com estas dimensões.


### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
    - Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Confirme que tem acesso de proprietário ou de contribuinte à subscrição para criar uma encomenda do Data Box Heavy.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:
- Retira o dispositivo da embalagem.
- Deve ter um computador anfitrião ligado à rede do datacenter. O Data Box Heavy irá copiar os dados desse computador. O computador anfitrião tem de ser executado num sistema operativo suportado, conforme descrito em [Requisitos de sistema do Azure Data Box Heavy](data-box-system-requirements.md).
- Precisa de um portátil com cabo RJ-45 para se ligar à IU local e configurar o dispositivo. Utilize o portátil para configurar cada nó do dispositivo uma vez.
- O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE.
- É necessário um cabo de 40 Gbps ou 10 Gbps por nó de dispositivo. Escolha cabos que sejam compatíveis com a interface de rede [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html):

    - Para o cabo de 40 Gbps, a extremidade do cabo que liga ao dispositivo tem de ser QSFP+.
    - Para o cabo de 10 Gbps, precisa de um cabo SFP+ que se ligue a um comutador de 10 G numa extremidade, com um adaptador QSFP+ para SFP+ (ou o adaptador QSA) para a extremidade que liga ao dispositivo.
    - Os cabos de alimentação estão incluídos no dispositivo.

## <a name="order-data-box-heavy"></a>Encomendar o Data Box Heavy

Execute os passos seguintes no Portal do Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [ https://portal.azure.com ](https://portal.azure.com).
2. Selecione **+ Criar um recurso** e procure *Azure Data Box*. Selecione **Azure Data Box**.
    
   [![Procurar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selecione **Criar**.

4. Verifique se o serviço Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e selecione **Aplicar**.

    |Definição  |Valor  |
    |---------|---------|
    |Subscrição     | Selecione uma subscrição EA, CSP ou Azure Sponsorship para o serviço Data Box. <br> A subscrição está ligada à sua conta de faturação.       |
    |Tipo de transferência     | Selecione **Importar para o Azure**.        |
    |País de origem     | Selecione o país/região onde os dados residem atualmente.         |
    |Região do Azure de destino     | Selecione a região do Azure para onde pretende transferir os dados.        |

    [![Selecionar a disponibilidade da família do Data Box](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selecione **Data Box Heavy**. A capacidade máxima utilizável para uma única encomenda é de 770 TB.

    [![Selecionar o Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. Em **Encomenda**, especifique os **Detalhes da encomenda**. Introduza ou selecione as seguintes informações e selecione **Seguinte**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Nome     | Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.      |
    |Grupo de recursos     | Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto.         |
    |Região do Azure de destino     | Selecione uma região para a sua conta de armazenamento. <br> Para obter mais informações, aceda a [Disponibilidade de região](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Destino de armazenamento     | Escolha entre a conta de armazenamento ou os discos geridos ou ambos. <br> Com base na região do Azure especificada, selecione uma ou mais contas de armazenamento na lista filtrada de uma conta de armazenamento existente. <br>O Data Box Heavy pode ser associado a um máximo de 10 contas de armazenamento. <br> Também pode criar uma nova conta de **Fins gerais v1**, **Fins gerais v2** ou **Conta de armazenamento de blobs**. <br> As contas do Azure Data Lake Storage Gen2 não são suportadas. Veja as [contas de armazenamento suportados pelo dispositivo](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>As contas de armazenamento com redes virtuais são suportadas. Para permitir que o serviço Data Box funcione com contas de armazenamento protegidas, ative os serviços fidedignos nas definições de firewall de rede da conta de armazenamento. Para obter mais informações, veja como [Adicionar o serviço Azure Data Box como um serviço fidedigno](../storage/common/storage-network-security.md#exceptions).|

    Se estiver a utilizar a conta de armazenamento como o destino de armazenamento, verá a seguinte captura de ecrã:

    ![Encomendar o Data Box Heavy para a conta de armazenamento](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Se, além da conta de armazenamento como destino de armazenamento, também estiver a utilizar o Data Box Heavy para criar discos geridos a partir dos VHDs no local, será necessário fornecer as seguintes informações:

    |Definição  |Valor  |
    |---------|---------|
    |Grupos de recursos     | Crie novos grupos de recursos se quiser criar discos geridos a partir dos VHDs no local. Só poderá utilizar um grupo de recursos existente se o grupo de recursos tiver sido criado anteriormente durante a criação da encomenda do Data Box Heavy para o disco gerido pelo serviço Data Box. <br> Especifique vários grupos de recursos separados por ponto e vírgula. É suportado um máximo de 10 grupos de recursos.|

    ![Encomendar o Data Box Heavy para o disco gerido](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para os discos geridos é utilizada como uma conta de armazenamento de teste. O serviço Data Box carrega os VHDs como blobs de páginas na conta de armazenamento de teste antes de os converter em discos geridos e de os mover para os grupos de recursos. Para obter mais informações, veja [Verificar o carregamento de dados no Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Selecione **Validar endereço**. 

    O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito. Selecione **Seguinte**.

8. Em **Detalhes de notificação**, especifique os endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

9. Reveja o **Resumo** das informações relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

10. Selecione **Encomendar**. A encomenda demora alguns minutos a ser criada.


## <a name="track-the-order"></a>Controlar a encomenda

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Aceda à encomenda do Data Box Heavy e, em seguida, aceda a **Descrição geral** para ver o estado. O portal mostra a encomenda no estado **Encomendado**.

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as ações seguintes:

- São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo.
- Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
- A palavra-passe do dispositivo, que ajuda a desbloqueá-lo, também é gerada.
- O Data Box Heavy está bloqueado para impedir o acesso não autorizado ao dispositivo a qualquer momento.

Quando a preparação do dispositivo estiver concluída, o portal mostra a encomenda no estado **Processado**.

![Encomenda do Data Box Heavy processada](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft prepara e expede os seus dispositivos através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Encomenda do Data Box Heavy enviada](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar a encomenda

Para cancelar esta encomenda, no portal do Azure, aceda a **Descrição Geral** e clique em **Cancelar** na barra de comando.

Depois de efetuar uma encomenda, pode cancelá-la a qualquer momento antes de o estado desta ser marcado como processado.
 
Para eliminar uma encomenda cancelada, aceda a **Descrição Geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box Heavy, como:

> [!div class="checklist"]
> * Pré-requisitos
> * Encomendar o Data Box Heavy
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o próximo tutorial para saber como configurar o Data Box Heavy.

> [!div class="nextstepaction"]
> [Configurar o Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
