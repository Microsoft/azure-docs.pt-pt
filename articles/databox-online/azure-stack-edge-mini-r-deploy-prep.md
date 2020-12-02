---
title: Tutorial para preparar portal Azure, ambiente do datacenter para implantar o dispositivo Azure Stack Edge Mini R Microsoft Docs
description: O primeiro tutorial sobre a implementação do dispositivo Azure Stack Edge Mini R envolve a preparação do portal Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/22/2020
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to prepare the portal to deploy Azure Stack Edge Mini R device so I can use it to transfer data to Azure.
ms.openlocfilehash: 54ac683dcc2b124c4a6410b2e8449fa7e969ce8c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468885"
---
# <a name="tutorial-prepare-to-deploy-azure-stack-edge-mini-r"></a>Tutorial: Prepare-se para implantar O Azure Stack Edge Mini R

Este é o primeiro tutorial da série de tutoriais de implementação que são necessários para implantar completamente o dispositivo Azure Stack Edge Mini R. Este tutorial descreve como preparar o portal Azure para implementar um recurso Azure Stack Edge.

Necessita de privilégios de administrador para concluir o processo de instalação e configuração. A preparação do portal demora menos de 10 minutos.

Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

### <a name="get-started"></a>Introdução

Para implementar O Azure Stack Edge Mini R, consulte os seguintes tutoriais na sequência prescrita.

| Passo | Descrição |
| --- | --- |
| **Preparação** |Estes passos devem ser concluídos em preparação para a próxima implantação. |
| **[Lista de verificação de configuração de implementação](#deployment-configuration-checklist)** |Utilize esta lista de verificação para recolher e registar informações antes e durante a implementação. |
| **[Pré-requisitos de implantação](#prerequisites)** |Estes validam o ambiente como estando preparado para a implementação. |
|  | |
|**Tutoriais de implantação** |Estes tutoriais são necessários para implementar o seu dispositivo Azure Stack Edge Mini R em produção. |
|**[1. Prepare o portal Azure para o dispositivo](azure-stack-edge-mini-r-deploy-prep.md)** |Crie e configuure o seu recurso Azure Stack Edge antes de instalar o dispositivo físico. |
|**[2. Instale o dispositivo](azure-stack-edge-mini-r-deploy-install.md)**|Desfaça as malas, a cremalheira e o cabo do seu dispositivo físico.  |
|**[3. Ligar ao dispositivo](azure-stack-edge-mini-r-deploy-connect.md)** |Assim que o dispositivo estiver instalado, ligue-se ao dispositivo web UI local.  |
|**[4. Configurar as definições da rede](azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy.md)** |Configure a rede, incluindo a rede de cálculo e as definições de procuração web para o seu dispositivo.   |
|**[5. Configurações do dispositivo de configuração](azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)** |Atribua um nome do dispositivo e domínio DNS, configuure o servidor de atualização e o tempo do dispositivo. |
|**[6. Configurar as definições de segurança](azure-stack-edge-mini-r-deploy-configure-certificates-vpn-encryption.md)** |Configure certificados usando os seus próprios certificados, crie VPN e configuure encriptação em repouso para o seu dispositivo.   |
|**[7. Ativar o dispositivo](azure-stack-edge-mini-r-deploy-activate.md)** |Utilize a chave de ativação do serviço para ativar o dispositivo. O dispositivo está pronto para configurar ações SMB ou NFS ou ligar via REST. |
|**[8. Cálculo de configuração](azure-stack-edge-gpu-deploy-configure-compute.md)** |Configure o papel de computação no seu dispositivo. Isto também criará um cluster Kubernetes. |

Agora, pode começar a configurar o portal do Azure.

## <a name="deployment-configuration-checklist"></a>Lista de verificação das configurações de implementação

Antes de implementar o seu dispositivo, tem de recolher informações para configurar o software no seu dispositivo Azure Stack Edge Mini R. Preparar algumas destas informações com antecedência ajuda a agilizar o processo de implantação do dispositivo no seu ambiente. Utilize a [lista de verificação de configuração de configuração Azure Stack Edge Mini R](azure-stack-edge-mini-r-deploy-checklist.md) para anotar os detalhes de configuração à medida que implementa o seu dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

Seguem-se os pré-requisitos de configuração para o seu recurso Azure Stack Edge, o seu dispositivo Azure Stack Edge e a rede de datacenter.

### <a name="for-the-azure-stack-edge-resource"></a>Para o recurso Azure Stack Edge

[!INCLUDE [Azure Stack Edge resource prerequisites](../../includes/azure-stack-edge-gateway-resource-prerequisites.md)]

### <a name="for-the-azure-stack-edge-device"></a>Para o dispositivo Azure Stack Edge

Antes de implementar um dispositivo físico, certifique-se de que:

- Reviu as informações de segurança deste dispositivo nas [diretrizes de segurança para o seu dispositivo Azure Stack Edge](azure-stack-edge-mini-r-safety.md).
[!INCLUDE [Azure Stack Edge device prerequisites](../../includes/azure-stack-edge-gateway-device-prerequisites.md)] 

### <a name="for-the-datacenter-network"></a>Para a rede do datacenter

Antes de começar, certifique-se de que:

- A rede no seu datacenter está configurada de acordo com os requisitos de rede para o seu dispositivo Azure Stack Edge. Para obter mais informações, consulte [os requisitos do sistema Azure Stack Edge Mini R](azure-stack-edge-mini-r-system-requirements.md).

- Para condições normais de funcionamento do seu Azure Stack Edge, tem:

    - Um mínimo de 10-Mbps descarrega largura de banda para garantir que o dispositivo permanece atualizado.
    - Um mínimo de 20-Mbps dedicado upload e download largura de banda para transferir ficheiros.

## <a name="create-a-new-resource"></a>Criar um novo recurso

Se tiver um recurso Azure Stack Edge existente para gerir o seu dispositivo físico, ignore este passo e vá para [obter a chave de ativação](#get-the-activation-key).

Para criar um recurso Azure Stack Edge, tome os seguintes passos no portal Azure.

1. Utilize as suas credenciais Microsoft Azure para iniciar súb9 no portal Azure neste URL: [https://portal.azure.com](https://portal.azure.com) .


2. No painel esquerdo, selecione **+ Crie um recurso**. Procure e selecione **Azure Stack Edge / Data Box Gateway**. Selecione **Criar**. 

3. Escolha a subscrição que pretende utilizar para o dispositivo Azure Stack Edge Pro. Selecione o país para onde pretende enviar este dispositivo físico. Selecione **dispositivos Show**.

    ![Criar um recurso 1](media/azure-stack-edge-mini-r-deploy-prep/create-resource-1.png)


4. Selecione o tipo de dispositivo. Em **Azure Stack Edge**, escolha **Azure Stack Edge Mini R** e, em seguida, escolha **Select**. Se vir algum problema ou não conseguir selecionar o tipo de dispositivo, aceda a [problemas de ordem de resolução de problemas](azure-stack-edge-troubleshoot-ordering.md).

    [![Criar um recurso 2](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png)](media/azure-stack-edge-mini-r-deploy-prep/create-resource-2.png#lightbox)


6. No **separador Básicos, insira** ou selecione os seguintes detalhes do **Projeto.**
    
    |Definição  |Valor  |
    |---------|---------|
    |Subscrição    |Isto é automaticamente povoado com base na seleção anterior. A subscrição está associada à sua conta de faturação. |
    |Grupo de recursos  |Selecione um grupo existente ou crie um novo grupo.<br>Saiba mais sobre os [Grupos de Recursos do Azure](../azure-resource-manager/management/overview.md).     |


7. Introduza ou selecione os **seguintes detalhes da Instância**.

    |Definição  |Valor  |
    |---------|---------|
    |Nome   | Um nome amigável para identificar o recurso.<br>O nome tem entre 2 e 50 carateres que contêm letras, números e hífenes.<br> O nome começa e termina com uma letra ou um número.        |
    |Região     |Para obter uma lista de todas as regiões onde o recurso Azure Stack Edge está disponível, consulte [os produtos Azure disponíveis por região.](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all) Se utilizar o Governo de Azure, todas as regiões governamentais estão disponíveis, como mostra as regiões de [Azure.](https://azure.microsoft.com/global-infrastructure/regions/)<br> Escolha uma localização mais próxima da região geográfica onde pretende implementar o dispositivo.|

    ![Criar um recurso 4](media/azure-stack-edge-mini-r-deploy-prep/create-resource-4.png)


8. Selecione **Seguinte: Endereço de envio**.

    - Se já tiver um dispositivo, selecione a caixa de combinação para **eu ter um dispositivo Azure Stack Edge Pro R**.

        ![Criar um recurso 5](media/azure-stack-edge-mini-r-deploy-prep/create-resource-5.png)

    - Se este for o novo dispositivo que está a encomendar, insira o nome de contacto, a empresa, o endereço para enviar o dispositivo e as informações de contacto.

        ![Criar um recurso 6](media/azure-stack-edge-mini-r-deploy-prep/create-resource-6.png)

9. Selecione **Seguinte: Tags**. Opcionalmente fornecer etiquetas para categorizar recursos e consolidar a faturação. Selecione **Seguinte: Rever + criar**.

10. No **separador 'Rever +' criar,** rever os **detalhes de Preços,** **Termos de utilização** e os detalhes do seu recurso. Selecione a caixa de combinação para **eu ter revisto os termos de privacidade**.

    ![Criar um recurso 7](media/azure-stack-edge-mini-r-deploy-prep/create-resource-7.png) 

    É também notificado que durante a criação de recursos está ativado um Identidade de Serviço Gerido (MSI) que lhe permite autenticar os serviços na nuvem. Esta identidade existe enquanto o recurso existir.

8. Selecione **Criar**.

    A criação do recurso demora alguns minutos. Também é criado um MSI que permite que o dispositivo Azure Stack Edge comunique com o fornecedor de recursos em Azure.
    
    Depois de o recurso ser criado e implementado com sucesso, é notificado. Selecione **Ir para recurso**.
    
    ![Vá ao recurso Azure Stack Edge Pro](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-1.png)
    
    Após a encomenda ser feita, a Microsoft revê a encomenda e contacta-o (via e-mail) com detalhes de envio.

   Se encontrar problemas durante o processo de encomenda, consulte [problemas de ordem de resolução de problemas](azure-stack-edge-troubleshoot-ordering.md).

## <a name="get-the-activation-key"></a>Obter a chave de ativação

Depois de o recurso Azure Stack Edge estar a funcionar, terás de obter a chave de ativação. Esta chave é utilizada para ativar e ligar o seu dispositivo Azure Stack Edge Mini R com o recurso. Pode obter esta chave agora enquanto está no portal do Azure.

1. Selecione o recurso que criou. Selecione **Overview** e, em seguida, selecione **a configuração do dispositivo**.

    ![Selecione configuração do dispositivo](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-2.png)

2. No azulejo **Ativar,** forneça um nome para o Cofre da Chave Azure ou aceite o nome predefinido. O nome do cofre pode estar entre 3 e 24 caracteres. 

    É criado um cofre-chave para cada recurso Azure Stack Edge que é ativado com o seu dispositivo. O cofre permite armazenar e aceder a segredos, por exemplo, a Chave de Integridade do Canal (CIK) para o serviço está armazenada no cofre da chave. 

    Uma vez especificado um nome de cofre de chave, **selecione Gerar a tecla** para criar uma chave de ativação. 

    [![Obter a chave de ativação](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png)](media/azure-stack-edge-mini-r-deploy-prep/azure-stack-edge-resource-3.png#lightbox)

    Aguarde alguns minutos à medida que a chave do cofre e da chave de ativação são criadas. Selecione o ícone de cópia para copiar a chave e guarde-a para posterior utilização.

> [!IMPORTANT]
> - A chave de ativação expira três dias após a sua geração.
> - Se a chave tiver expirado, gere uma nova chave. A chave mais antiga não é válida.

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você aprendeu sobre tópicos Azure Stack Edge tais como:

> [!div class="checklist"]
> * Criar um novo recurso
> * Obter a chave de ativação

Avance para o próximo tutorial para aprender a instalar o Azure Stack Edge.

> [!div class="nextstepaction"]
> [Instalar borda de pilha de Azure](./azure-stack-edge-mini-r-deploy-install.md)