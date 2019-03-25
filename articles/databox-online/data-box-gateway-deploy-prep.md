---
title: Tutorial sobre a preparação do portal do Azure para implementar o Data Box Gateway | Microsoft Docs
description: O primeiro tutorial para implementar o Azure Data Box Gateway envolve a preparação do portal do Azure.
services: databox-edge-gateway
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: 78008357e3ea8fbfe707a7dbead19e3fce83b578
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58403702"
---
# <a name="tutorial-prepare-to-deploy-azure-data-box-gateway"></a>Tutorial: Preparar a implementação de Gateway de caixa de dados do Azure


Este é o primeiro tutorial da série de tutoriais de implementação necessários para implementar completamente o Azure Data Box Gateway. Este tutorial descreve como preparar o portal do Azure para implementar o recurso do Data Box Gateway. 

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Transferir a imagem do dispositivo virtual
> * Obter a chave de ativação

## <a name="get-started"></a>Introdução

Para implementar o Data Box Gateway, consulte os seguintes tutoriais na sequência indicada.

| **#** | **Neste passo** | **Utilizar estes documentos** |
| --- | --- | --- | 
| 1. |**[Preparar o portal do Azure para o Data Box Gateway](data-box-gateway-deploy-prep.md)** |Crie e configure o seu recurso do Data Box Gateway antes de aprovisionar um dispositivo virtual Data Box Gateway. |
| 2. |**[Aprovisionar o Data Box Gateway em Hyper-V](data-box-gateway-deploy-provision-hyperv.md)** <br><br><br>**[Aprovisionar o Data Box Gateway em VMware](data-box-gateway-deploy-provision-vmware.md)**|Para Hyper-V, aprovisione e ligue a um dispositivo virtual Data Box Gateway num sistema anfitrião com Hyper-V no Windows Server 2016 ou Windows Server 2012 R2. <br><br><br> Para o VMware, aprovisionar e ligar a um dispositivo virtual de Gateway de caixa de dados num sistema anfitrião com o VMware ESXi 6.0, 6.5 ou 6.7.<br></br> |
| 3. |**[Ligar, configurar, ativar o Data Box Gateway](data-box-gateway-deploy-connect-setup-activate.md)** |Ligue IU Web local, conclua a configuração do dispositivo e ative o dispositivo. Em seguida, pode aprovisionar partilhas SMB.  |
| 4. |**[Transferir dados com o Data Box Gateway](data-box-gateway-deploy-add-shares.md)** |Adicione partilhas, ligue-se a partilhas através de SMB ou NFS. |

Agora, pode começar a configurar o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Aqui, vai encontrar os pré-requisitos de configuração para o recurso do Data Box Gateway, o dispositivo Data Box Gateway e a rede do datacenter.

### <a name="for-the-data-box-gateway-resource"></a>Para o recurso do Data Box Gateway

Antes de começar, certifique-se de que:

- Subscrição do Microsoft Azure deve ser suportada para o recurso de Gateway de caixa de dados. Subscrições pay as you go não são suportadas.
- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.

### <a name="for-the-data-box-gateway-device"></a>Para o dispositivo Data Box Gateway

Antes de implementar um dispositivo virtual, certifique-se de que:

- Tem acesso a um sistema anfitrião com Hyper-V no Windows Server 2012 R2 ou posterior ou o VMware (ESXi 6.0, 6.5 ou 6.7) que pode ser utilizado para um aprovisionar um dispositivo.
- O sistema anfitrião tem capacidade para dedicar os recursos seguintes para aprovisionar o seu dispositivo virtual Data Box:
  
  - Um mínimo de 4 processadores virtuais.
  - Pelos menos 8 GB de RAM.
  - Uma interface de rede.
  - Um disco de SO de 250 GB.
  - Um disco virtual de 2 TB para dados do sistema.

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Data Box Gateway. Para obter mais informações, consulte a [requisitos de sistema do Gateway de caixa de dados](data-box-gateway-system-requirements.md).

- Para condições de operação normais sua caixa do Gateway de dados, é necessário ter o r:

    - Um mínimo de 10 Mbps transferir largura de banda para garantir que o dispositivo se mantém atualizado.
    - Um mínimo de 20 Mbps dedicado carregar e transferir a largura de banda para transferência de ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso existente do Data Box Gateway para gerir os dispositivos virtuais, ignore este passo e vá para o passo [Obter a chave de ativação](#get-the-activation-key).

Para criar um recurso de Gateway de caixa de dados, siga os passos seguintes no portal do Azure.

1. Utilize as suas credenciais do Microsoft Azure para iniciar sessão em:

    - O portal do Azure neste URL: [ https://portal.azure.com ](http://portal.azure.com).
    - Ou, no portal do Azure Government neste URL: [ https://portal.azure.us ](https://portal.azure.us). Para obter mais detalhes, aceda a [ligue-se ao utilizar o portal do Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-portal).

2. No painel esquerdo, selecione **+ criar um recurso**. Procure **dados de caixa Edge / dados caixa Gateway**. Selecionar um contorno de caixa de dados / dados de caixa de Gateway. Selecione **Criar**.
3. Escolha a subscrição que pretende utilizar para o dispositivo de Gateway de caixa de dados. Selecione a região onde pretende implementar o recurso de Gateway de caixa de dados. Nesta versão, E.U.A. leste, Sudeste asiático e Europa estão disponíveis. Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. Na **dados de caixa de Gateway** opção, selecione **criar**.

    ![Pesquisar o serviço Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-edge-sku.png)

4. Sobre o **Noções básicas** separador, introduza ou selecione as seguintes **detalhes do projeto**.
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isso é preenchido automaticamente com base na seleção da anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/resource-group-overview.md).     |

5. Introduza ou selecione as seguintes **detalhes de instância**.
   | Nome | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |   
    | Região | Nesta versão, E.U.A. leste, Sudeste asiático e Europa estão disponíveis para implementar o seu recurso. Para o Azure Government, todas as regiões do Governo listados na [regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/) estão disponíveis. <br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo. |
    
    ![Criar o recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource.png)
    
6. Selecione **Rever + criar**.
 
7. Na **rever + criar** separador, reveja o **os detalhes dos preços**, **termos de utilização**e os detalhes do seu recurso. Selecione **Criar**.

    ![Rever os detalhes do recurso de Gateway de caixa de dados](media/data-box-gateway-deploy-prep/data-box-gateway-resource1.png)

A criação do recurso demora alguns minutos. Depois do recurso é criado e implementado com êxito, receberá uma notificação. Selecione **Ir para recurso**.

![Rever os detalhes do recurso de Gateway de caixa de dados](media/data-box-gateway-deploy-prep/data-box-gateway-resource2.png)

## <a name="download-the-virtual-device-image"></a>Transferir a imagem do dispositivo virtual

Depois de criar o recurso do Data Box Gateway, transfira a imagem do dispositivo virtual adequado para aprovisionar um dispositivo virtual no seu sistema anfitrião. As imagens do dispositivo virtual são específicas para um sistema operativo.

> [!IMPORTANT]
> O software em execução no Data Box Gateway só pode ser utilizado com o recurso do Data Box Gateway.

Siga estes passos a [portal do Azure](https://portal.azure.com/) para transferir uma imagem do dispositivo virtual.

1. No recurso que criou e, em seguida, selecione **descrição geral**. Se tiver um recurso de Gateway de caixa de dados do Azure existente, selecione o recurso e aceda a **descrição geral**. Selecione **configuração do dispositivo**.

    ![Novo recurso do Data Box Gateway](media/data-box-gateway-deploy-prep/data-box-gateway-resource-created.png)

2. Sobre o **imagem de Download** mosaico, selecione a imagem de dispositivo virtual correspondente ao sistema operativo no servidor de anfitrião utilizado para aprovisionar a VM. Os arquivos de imagem são aproximadamente 5.6 GB.
   
   * [VHDX para Hyper-V no Windows Server 2012 R2 e versões posteriores](https://aka.ms/dbe-vhdx-2012).
   * [VMDK para VMWare ESXi 6.0, 6.5 ou 6.7](https://aka.ms/dbe-vmdk).

    ![Transferir a imagem do dispositivo virtual de Gateway de caixa de dados](media/data-box-gateway-deploy-prep/data-box-gateway-download-image.png)

5. Transfira e deszipe o ficheiro numa unidade local, tomando nota da localização onde o ficheiro deszipado se encontra.


## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois do recurso de Gateway de caixa de dados está em execução, terá de obter a chave de ativação. Esta chave serve para ativar e ligar o seu dispositivo Data Box Gateway ao recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou e, em seguida, selecione **descrição geral**. Na **configuração do dispositivo**, aceda ao **configurar e ativar** mosaico.

    ![Configurar e ativar o mosaico](media/data-box-gateway-deploy-prep/data-box-gateway-configure-activate.png)

2. Selecione **gerar chave** para criar uma chave de ativação. Selecione o ícone de cópia para copiar a chave e guarde-o para utilização posterior.

    ![Obter a chave de ativação](media/data-box-gateway-deploy-prep/get-activation-key.png)

> [!IMPORTANT]
> - A chave de ativação expira três dias após ser gerado.
> - Se a chave expirou, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a conhecer tópicos do Data Box Gateway, como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Transferir a imagem do dispositivo virtual
> * Obter a chave de ativação

Avance para o próximo tutorial para saber como aprovisionar uma máquina virtual para o Data Box Gateway. Consoante o sistema operativo anfitrião, veja as instruções detalhadas em:

> [!div class="nextstepaction"]
> [Aprovisionar um Data Box Gateway em Hyper-V](./data-box-gateway-deploy-provision-hyperv.md)

OU

> [!div class="nextstepaction"]
> [Aprovisionar um Data Box Gateway em VMware](./data-box-gateway-deploy-provision-vmware.md)


