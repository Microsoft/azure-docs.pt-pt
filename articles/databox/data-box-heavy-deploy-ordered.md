---
title: Tutorial para ordenar Azure Data Box Heavy | Microsoft Docs
description: Conheça os pré-requisitos de implantação e como solicitar um Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: dce3549bde4c62245e1d2b1f8ac0c88c0b70260c
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241395"
---
# <a name="tutorial-order-azure-data-box-heavy"></a>Tutorial: Ordem Azure Data Box Heavy


Azure Data Box Heavy é uma solução híbrida que permite que você importe seus dados locais para o Azure de maneira rápida, fácil e confiável. Você transfere seus dados para um dispositivo de armazenamento 770 TB fornecido pela Microsoft (capacidade utilizável aproximada) e, em seguida, envia o dispositivo de volta. Estes dados são então carregados para o Azure.

Este tutorial descreve como você pode ordenar um Azure Data Box Heavy. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * Pré-requisitos para Data Box Heavy
> * Ordenar um Data Box Heavy
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

Conclua os pré-requisitos de configuração seguintes para o serviço e dispositivo do Data Box antes de implementar o dispositivo.

### <a name="for-installation-site"></a>Para o site de instalação

Antes de começar, certifique-se de que:

- O dispositivo se ajusta por meio de doorways e entryways padrão. No entanto, certifique-se de que o dispositivo possa se ajustar a todos os seus entryways. As dimensões do dispositivo são: largura: 26 "comprimento: 48 "altura: 28 ".
- Se estiver instalado em um andar que não seja o chão, você precisará de acesso ao dispositivo por meio de um elevador ou de uma rampa. O dispositivo pesa aproximadamente cerca de 500 kg.
- Verifique se você tem um site simples no datacenter com proximidade com uma conexão de rede disponível que possa acomodar um dispositivo com essa superfície.


### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
    - Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).

- Verifique se você tem acesso de proprietário ou colaborador à assinatura para criar uma ordem de Data Box Heavy.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:
- Seu dispositivo está desempacotado.
- Deve ter um computador anfitrião ligado à rede do datacenter. Data Box Heavy copiará os dados deste computador. O computador host deve executar um sistema operacional com suporte, conforme descrito em [Azure data Box Heavy requisitos do sistema](data-box-system-requirements.md).
- Você precisa ter um laptop com cabo RJ-45 para se conectar à interface do usuário local e configurar o dispositivo. Use o laptop para configurar cada nó do dispositivo uma vez.
- O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE.
- Você precisa de 1 40 Gbps ou 10 Gbps de cabo por nó de dispositivo. Escolha os cabos que são compatíveis com a interface de rede [MELLANOX MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html) :

    - Para o cabo de 40 Gbps, a extremidade do dispositivo do cabo precisa ser QSFP +.
    - Para o cabo de 10 Gbps, você precisa de um cabo SFP + que se conecte a um comutador de 10 G em uma extremidade, com um adaptador QSFP + para SFP + (ou o adaptador QSA) para o final que se conecta ao dispositivo.
    - Os cabos de alimentação estão incluídos no dispositivo.

## <a name="order-data-box-heavy"></a>Ordem Data Box Heavy

Execute os passos seguintes no Portal do Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [ https://portal.azure.com ](https://portal.azure.com).
2. Selecione **+ criar um recurso** e pesquise *Azure data Box*. Selecione **Azure data Box**.
    
   [![Procurar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Selecione **Criar**.

4. Verifique se o serviço de Data Box está disponível em sua região. Insira ou selecione as informações a seguir e selecione **aplicar**.

    |Definição  |Valor  |
    |---------|---------|
    |Subscription     | Selecione um EA, CSP ou assinatura de patrocínio do Azure para Data Box serviço. <br> A subscrição está ligada à sua conta de faturação.       |
    |Tipo de transferência     | Selecione **Importar para o Azure**.        |
    |País de origem     | Selecione o país/região onde os dados residem no momento.         |
    |Região do Azure de destino     | Selecione a região do Azure para onde pretende transferir os dados.        |

    [![Selecione Data Box disponibilidade da família](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

5. Selecione **Data Box Heavy**. A capacidade máxima utilizável para uma única ordem é de 770 TB.

    [![Selecionar Data Box Heavy](media/data-box-heavy-deploy-ordered/select-data-box-heavy.png)

6. Em **Encomenda**, especifique os **Detalhes da encomenda**. Insira ou selecione as informações a seguir e selecione **Avançar**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Name     | Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.      |
    |Resource group     | Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto.         |
    |Região do Azure de destino     | Selecione uma região para a sua conta de armazenamento. <br> Para obter mais informações, aceda a [Disponibilidade de região](https://azure.microsoft.com/global-infrastructure/services/?products=databox).        |
    |Destino de armazenamento     | Escolha entre a conta de armazenamento ou os discos gerenciados ou ambos. <br> Com base na região do Azure especificada, selecione uma ou mais contas de armazenamento na lista filtrada de uma conta de armazenamento existente. <br>Data Box Heavy pode ser vinculado com até 10 contas de armazenamento. <br> Você também pode criar uma nova **conta de armazenamento de blob** **v1**, de **uso geral v2**ou de uso geral. <br> Não há suporte para contas de Gen 2 Azure Data Lake Storage. Consulte as [contas de armazenamento com suporte no seu dispositivo](data-box-heavy-system-requirements.md#supported-storage-accounts). <br>Há suporte para contas de armazenamento com redes virtuais. Para permitir que Data Box serviço funcione com contas de armazenamento protegidas, habilite os serviços confiáveis nas configurações de firewall de rede da conta de armazenamento. Para obter mais informações, consulte como [Adicionar o serviço de Azure data box como um serviço confiável](../storage/common/storage-network-security.md#exceptions).|

    Se estiver usando a conta de armazenamento como o destino de armazenamento, você verá a seguinte captura de tela:

    ![Ordem de Data Box Heavy para a conta de armazenamento](media/data-box-heavy-deploy-ordered/order-storage-account.png)

    Se, além da conta de armazenamento como o destino de armazenamento, você também estiver usando Data Box Heavy para criar discos gerenciados a partir dos VHDs locais, será necessário fornecer as seguintes informações:

    |Definição  |Value  |
    |---------|---------|
    |Grupos de recursos     | Crie novos grupos de recursos se você pretende criar discos gerenciados de VHDs locais. Você poderá usar um grupo de recursos existente somente se o grupo de recursos tiver sido criado anteriormente ao criar um Data Box Heavy ordem para o disco gerenciado pelo serviço Data Box. <br> Especifique vários grupos de recursos separados por ponto e vírgula. Há suporte para um máximo de 10 grupos de recursos.|

    ![Ordem de Data Box Heavy do disco gerenciado](media/data-box-heavy-deploy-ordered/order-managed-disks.png)

    A conta de armazenamento especificada para discos gerenciados é usada como uma conta de armazenamento de preparo. O serviço de Data Box carrega os VHDs como BLOBs de páginas para a conta de armazenamento de preparo antes de convertê-los em Managed disks e movê-los para os grupos de recursos. Para obter mais informações, consulte [verificar o carregamento de dados no Azure](data-box-deploy-picked-up.md#verify-data-upload-to-azure).

7. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Selecione **validar endereço**. 

    O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito. Selecione **Seguinte**.

8. Em **Detalhes de notificação**, especifique os endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

9. Reveja o **Resumo** das informações relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

10. Selecione **ordem**. A encomenda demora alguns minutos a ser criada.


## <a name="track-the-order"></a>Controlar a encomenda

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá para sua ordem de Data Box Heavy e vá para **visão geral** para exibir o status. O portal mostra a encomenda no estado **Encomendado**.

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as ações seguintes:

- São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo.
- Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
- A palavra-passe do dispositivo, que ajuda a desbloqueá-lo, também é gerada.
- A Data Box Heavy está bloqueada para impedir o acesso não autorizado ao dispositivo em qualquer ponto.

Quando a preparação do dispositivo estiver concluída, o portal mostra a encomenda no estado **Processado**.

![Ordem de Data Box Heavy processada](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft prepara e expede os seus dispositivos através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Ordem de Data Box Heavy despachada](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar a encomenda

Para cancelar esta encomenda, no portal do Azure, aceda a **Descrição Geral** e clique em **Cancelar** na barra de comando.

Depois de efetuar uma encomenda, pode cancelá-la a qualquer momento antes de o estado desta ser marcado como processado.
 
Para eliminar uma encomenda cancelada, aceda a **Descrição Geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Data Box Heavy como:

> [!div class="checklist"]
> * Pré-requisitos
> * Ordem Data Box Heavy
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o próximo tutorial para saber como configurar seu Data Box Heavy.

> [!div class="nextstepaction"]
> [Configurar seu Azure Data Box Heavy](./data-box-heavy-deploy-set-up.md)
