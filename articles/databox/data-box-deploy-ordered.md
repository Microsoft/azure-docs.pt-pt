---
title: Encomendar o Microsoft Azure Data Box | Microsoft Docs
description: Aprenda os pré-requisitos de implementação e como encomendar um Azure Data Box
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 01/09/2019
ms.author: alkohli
ms.openlocfilehash: 00c001b5c0c2c3dab86477fca798ec651f7b6c93
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54189971"
---
# <a name="tutorial-order-azure-data-box"></a>Tutorial: Pedido do Azure Data Box

O Azure Data Box é uma solução híbrida que permite importar os dados no local para o Azure, de forma rápida, fácil e fiável. Transfira os seus dados para um dispositivo de armazenamento de 80 TB fornecido pela Microsoft (capacidade utilizável) e, em seguida, envie novamente o dispositivo. Estes dados são então carregados para o Azure.

Este tutorial descreve como pode encomendar um Azure Data Box. Neste tutorial, ficará a saber mais sobre:

> [!div class="checklist"]
> * Inscrever-se para um Data Box
> * Encomendar um Data Box
> * Controlar a encomenda
> * Cancelar a encomenda

## <a name="prerequisites"></a>Pré-requisitos

Conclua os pré-requisitos de configuração seguintes para o serviço e dispositivo do Data Box antes de implementar o dispositivo.

### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- Certifique-se de que a subscrição que utiliza para o serviço Data Box é de um dos tipos seguintes:
    - Microsoft Enterprise Agreement (EA). Leia mais sobre as [Subscrições EA](https://azure.microsoft.com/pricing/enterprise-agreement/).
    - Fornecedor de Soluções Cloud (CSP). Saiba mais sobre o [Programa CSP do Azure](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview).
    - Microsoft Azure Sponsorship. Saiba mais sobre o [programa Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/). 

- Certifique-se de que tem acesso de proprietário ou de contribuinte à subscrição para criar uma encomenda do Data Box.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:
- Deve ter um computador anfitrião ligado à rede do datacenter. O Data Box irá copiar os dados desse computador. O computador anfitrião tem de executar um sistema operativo suportado, conforme descrito em [Requisitos de sistema do Azure Data Box](data-box-system-requirements.md).
- O seu datacenter tem de ter uma rede de alta velocidade. Recomendamos vivamente que tenha, pelo menos, uma ligação de 10 GbE. Se não estiver disponível uma ligação de 10 GbE, pode ser utilizada uma ligação de dados de 1 GbE, mas as velocidades de cópia serão afetadas.


## <a name="order-data-box"></a>Encomendar o Data Box

Execute os passos seguintes no Portal do Azure para encomendar um dispositivo.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão neste URL: [ https://portal.azure.com ](https://portal.azure.com).
2. Clique em **+ Criar um recurso** e procure *Azure Data Box*. Clique em **Azure Data Box**.
    
   [![Procurar Azure Data Box 1](media/data-box-deploy-ordered/search-azure-data-box1.png)](media/data-box-deploy-ordered/search-azure-data-box1.png#lightbox)

3. Clique em **Criar**.

4. Verifique se o serviço Data Box está disponível na sua região. Introduza ou selecione as seguintes informações e clique em **Aplicar**. 
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição     | Selecione uma subscrição EA, CSP ou Pay as you go para o serviço do Data Box. <br> A subscrição está ligada à sua conta de faturação.       |
    |Tipo de transferência     | Selecione **Importar para o Azure**.        |
    |País de origem     |   Selecione o país onde os dados residem atualmente.         |
    |Região do Azure de destino     |     Selecione a região do Azure para onde pretende transferir os dados.        |

5. Selecione o Data **Box**. A capacidade máxima da solução para uma única encomenda é 80 TB. Pode criar várias encomendas para tamanhos de dados maiores.

      [![Selecione Data Box opção 1](media/data-box-deploy-ordered/select-data-box-option1.png)](media/data-box-deploy-ordered/select-data-box-option1.png#lightbox)

6. Em **Encomenda**, especifique os **Detalhes da encomenda**. Introduza ou selecione as informações seguintes e clique em **Seguinte**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Nome     |  Forneça um nome amigável para controlar a encomenda. <br> O nome pode ter entre 3 e 24 carateres que podem ser letras, números e hífenes. <br> O nome tem de começar e terminar com uma letra ou um número.      |
    |Grupo de recursos     |   Utilize um grupo de recursos existente ou crie um novo. <br> Um grupo de recursos é um contentor lógico para os recursos que podem ser geridos ou implementados em conjunto.         |
    |Região do Azure de destino     | Selecione uma região para a sua conta de armazenamento. <br> Para obter mais informações, aceda a [Disponibilidade de região](data-box-overview.md#region-availability).        |
    |Contas de armazenamento     | Com base na região do Azure especificada, selecione uma ou mais contas de armazenamento na lista filtrada de uma conta de armazenamento existente. O Data Box pode ser associado a um máximo de 10 contas de armazenamento. <br> Também pode criar uma nova conta para **Fins gerais v1**, **Fins gerais v2** ou de **Armazenamento de Blobs**.        |
    
7. No separador **Endereço para envio**, forneça o seu nome próprio e apelido, o nome e o endereço postal da empresa e um número de telefone válido. Clique em **Validar endereço**. O serviço valida o endereço de envio relativamente à disponibilidade do serviço. Se o serviço estiver disponível para o endereço de envio especificado, receberá uma notificação para o efeito. Clique em **Seguinte**.

8. Em **Detalhes de notificação**, especifique os endereços de e-mail. O serviço envia notificações por e-mail relativamente a todas as atualizações do estado da encomenda para os endereços de e-mail especificados.

    Recomendamos que utilize um e-mail de grupo para continuar a receber notificações se um administrador sair do grupo.

9. Reveja o **Resumo** das informações relacionadas com os termos de encomenda, contacto, notificação e privacidade. Selecione a caixa correspondente ao contrato de termos de privacidade.

10. Clique em **Encomendar**. A encomenda demora alguns minutos a ser criada. 


## <a name="track-the-order"></a>Controlar a encomenda

Depois de fazer a encomenda, pode controlar o estado da encomenda a partir do portal do Azure. Vá para a sua encomenda e, em seguida, aceda a **Descrição Geral** para ver o estado. O portal mostra a encomenda no estado **Encomendado**.

Se o dispositivo não estiver disponível, receberá uma notificação. Se o dispositivo estiver disponível, a Microsoft identifica-o para envio e prepara o envio. Durante a preparação do dispositivo, ocorrem as ações seguintes:

- São criadas Partilhas SMB para cada conta de armazenamento associada ao dispositivo. 
- Para cada partilha, são geradas credenciais de acesso, como nome de utilizador e palavra-passe.
- A palavra-passe do dispositivo, que ajuda a desbloqueá-lo, também é gerada. 
- O Data Box está bloqueado para impedir o acesso não autorizado ao dispositivo a qualquer momento.

Quando a preparação do dispositivo estiver concluída, o portal mostra a encomenda no estado **Processado**.

![Encomenda do Data Box processada](media/data-box-overview/data-box-order-status-processed.png)

Em seguida, a Microsoft prepara e expede os seus dispositivos através de uma transportadora regional. Receberá um número de encomenda assim que o dispositivo for enviado. O portal mostra a encomenda no estado **Expedido**.

![Encomenda do Data Box expedida](media/data-box-overview/data-box-order-status-dispatched.png)

## <a name="cancel-the-order"></a>Cancelar a encomenda

Para cancelar esta encomenda, no portal do Azure, aceda a **Descrição Geral** e clique em **Cancelar** na barra de comando.

Depois de efetuar uma encomenda, pode cancelá-la a qualquer momento antes de o estado desta ser marcado como processado.
 
Para eliminar uma encomenda cancelada, aceda a **Descrição Geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Azure Data Box, como:

> [!div class="checklist"]
> * Pré-requisitos para implementar o Data Box
> * Encomendar o Data Box
> * Controlar a encomenda
> * Cancelar a encomenda

Avance para o tutorial seguinte para saber como configurar o Data Box.

> [!div class="nextstepaction"]
> [Configurar o Azure Data Box](./data-box-deploy-set-up.md)


