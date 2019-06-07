---
title: Início rápido para pesado de caixa de dados do Microsoft Azure | Documentos da Microsoft
description: Saiba como implementar rapidamente o Azure dados caixa pesadas no portal do Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: quickstart
ms.date: 06/04/2019
ms.author: alkohli
ms.openlocfilehash: 930da7367b3f5d7e20617afedef007efea97c51a
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66518583"
---
# <a name="quickstart-deploy-azure-data-box-heavy-using-the-azure-portal"></a>Início rápido: Implementar no portal do Azure do Azure dados caixa pesadas

Este início rápido descreve como implementar o Azure dados caixa pesadas no portal do Azure. Os passos incluem como instalar, configurar e copiar dados para dados de caixa pesada, de modo a que carrega para o Azure. O início rápido é efetuado no portal do Azure e na IU da Web local do dispositivo.

Para implementação passo a passo detalhada e instruções de controlo, aceda a [Tutorial: Pesado de caixa de dados do Azure de ordem](data-box-heavy-deploy-ordered.md)

## <a name="prerequisites"></a>Pré-requisitos

Conclua os seguintes pré-requisitos de configuração para o site de instalação, o serviço do Data Box e o dispositivo antes de implementar o dispositivo.

### <a name="for-installation-site"></a>Para o site de instalação

Antes de começar, certifique-se de que:

- O dispositivo pode se encaixar através de todos os seus entryways. Dimensões de dispositivo são: largura: 26" comprimento: 48" altura: 28”.
- Tem acesso para o dispositivo através de um elevador ou de uma plataforma de lançamento se planear instalar num chão que não seja o piso de zero.
- Tem duas pessoas para lidar com o dispositivo. O dispositivo examina aproximadamente ~ 500 lbs. e é fornecida no rodas.
- Tem um site simples no Centro de dados com a proximidade para uma ligação de rede disponíveis que pode acomodar um dispositivo com esta presença.

### <a name="for-service"></a>Para o serviço

Antes de começar, certifique-se de que:

- Tem a conta do Storage do Microsoft Azure com credenciais de acesso.
- A subscrição a utilizar para o serviço do Data Box está [Microsoft Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/), [fornecedor de soluções Cloud (CSP)](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-overview), ou [Microsoft Azure Sponsorship](https://azure.microsoft.com/offers/ms-azr-0036p/).
- Tem acesso de proprietário ou contribuinte à subscrição para criar uma ordem de dados de caixa pesada.

### <a name="for-device"></a>Para o dispositivo

Antes de começar, certifique-se de que:

- Reviu os [diretrizes de segurança para seu pesada dados de caixa](data-box-safety.md).
- Possui um computador de anfitrião ligado à rede do datacenter. Dados de caixa pesada irá copiar os dados deste computador. O computador anfitrião tem de executar uma [sistema operativo suportado](data-box-heavy-system-requirements.md).
- Tem um laptop com o cabo de RJ-45 para ligar à interface do usuário local e configurar o dispositivo. Utilize o laptop para configurar uma vez a cada nó do dispositivo.
- Seu centro de dados tem a rede de alta velocidade e tiver, pelo menos, uma ligação de 10 GbE.
- Precisa de um cabo de 40 Gbps ou cabo de 10 Gbps por nó de dispositivo. Escolha cabos que são compatíveis com o Mellanox MCX314A-BCCT interface de rede:
    - Para o cabo de 40 Gbps, a fim de dispositivo do cabo tem de ser QSFP +.
    - Para o cabo de 10 Gbps, terá de um cabo de SFP + que se conecta um comutador de 10 G numa extremidade, com um QSFP + SFP + adaptador (ou o adaptador de qsa independente) para o end que se conecta o dispositivo.
- Os cabos de energia são incluídos numa Bandeja parte traseira desse dispositivo.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no Portal do Azure em [http://portal.azure.com](http://portal.azure.com).

## <a name="order"></a>Encomenda

Este passo demora cerca de 5 minutos.

1. Crie um novo recurso do Azure Data Box no portal do Azure.
2. Selecione uma subscrição existente ativada para este serviço e escolha o tipo de transferência, como **Importar**. Indique o **País de origem** onde os dados residem e a **Região de destino do Azure** para a transferência de dados.
3. Selecione **dados de caixa pesados**. A capacidade máxima de utilizável 770 TB e pode criar vários pedidos para tamanhos de dados maiores.
4. Introduza os detalhes da encomenda e as informações de envio. Se o serviço estiver disponível na sua região, indique os endereços de e-mail de notificação, reveja o resumo e, em seguida, crie a encomenda.

Assim que a encomenda for criada, o dispositivo é preparado para envio.

## <a name="cable-for-power"></a>Cabo de energia

Este passo demora cerca de 5 minutos.

Quando receber a pesadas de caixa de dados, siga os passos abaixo para instalar os cabos do dispositivo para poder e ativar o dispositivo.

1. Se existirem quaisquer evidências de que o dispositivo foi adulterado ou está danificado, não prossiga. Contacte o Suporte da Microsoft para enviar-lhe um dispositivo de substituição.
2. Mover o dispositivo para o site de instalação e a bloquear as rodas rear.
3. Ligue todos os cabos de quatro power para as fontes de alimentação parte traseira desse dispositivo.
4. Use os botões de energia no painel de front-para ativar os nós do dispositivo.

## <a name="cable-first-node-for-network"></a>Primeiro nó de cabo de rede

Este passo demora cerca de 10 a 15 minutos a concluir.

1. Utilize o cabo de rede RJ-45 CAT 6 para ligar o seu computador anfitrião à porta de gestão (MGMT) no dispositivo.
2. Utilize o cabo Twinax QSFP + cobre ligar, pelo menos, um 40 Gbps (preferível face a 1 Gbps) interface de rede, dados de 1 ou 2 de dados para dados. Se utilizar um comutador de 10 Gbps, utilize um cabo de cobre Twinax SFP + com um QSFP + SFP + adaptador (adaptador qsa independente) para ligar a interface de rede de 40 Gbps para dados.
3. Instale os cabos do dispositivo conforme mostrado abaixo.  

    ![Instalou os cabos do pesadas de caixa de dados](media/data-box-heavy-quickstart-portal/data-box-heavy-ports-cabled.png)  

## <a name="configure-first-node"></a>Configurar o primeiro nó

Este passo demora entre 5 a 7 minutos.

1. Para obter a palavra-passe do dispositivo, aceda a **Geral > Detalhes do dispositivo** no [portal do Azure](http://portal.azure.com). Mesma palavra-passe é utilizada para ambos os nós do dispositivo.
2. Atribua um endereço IP estático de 192.168.100.5 e sub-rede 255.255.255.0 ao adaptador Ethernet no computador que está a utilizar para ligar a dados de caixa pesada. Aceda à IU da Web local do dispositivo em `https://192.168.100.10`. A ligação pode demorar até 5 minutos depois de ligar o dispositivo.
3. Inicie sessão com a palavra-passe do portal do Azure. Verá um erro que indica um problema com o certificado de segurança do site. Siga as instruções específicas do browser para avançar para a página Web.
4. Por predefinição, as definições de rede para as interfaces (excluindo a MGMT) estão configuradas como DHCP. Se for necessário, pode configurar estas interfaces como estática e forneça um endereço IP.

## <a name="cable-and-configure-the-second-node"></a>Ligar e configurar o segundo nó

Este passo demora cerca de 15 a 20 minutos a concluir.

Siga os passos utilizados para o primeiro nó para ligar e configurar o segundo nó no dispositivo.  

## <a name="copy-data"></a>Copiar dados

O tempo para concluir esta operação depende do tamanho dos dados e a velocidade da rede durante o qual os dados são copiados.
 
1. Copie dados para ambos os nós de dispositivo usando a ambas as interfaces de dados de 40 Gbps em paralelo.

    - Se utilizar um anfitrião do Windows, utilize uma ferramenta de cópia de ficheiros compatível SMB, como [Robocopy](https://technet.microsoft.com/library/ee851678.aspx).
    - Para o anfitrião NFS, utilize o comando `cp` ou `rsync` para copiar os dados.
2. Ligue-se para as partilhas no dispositivo com o caminho:`\\<IP address of your device>\ShareName`. Para obter as credenciais de acesso de partilha, vá para o **Connect & cópia** página da web local da interface do Usuário dos dados de caixa pesada.
3. Certifique-se de que a partilha e nomes de pastas e os dados seguem diretrizes descritas a [limites de serviço de armazenamento do Azure e dados de caixa pesada](data-box-heavy-limits.md).

## <a name="prepare-to-ship"></a>Preparar para enviar

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Depois da cópia de dados estiver concluída sem erros, aceda ao **preparação para envio** página na IU da web local e iniciar a preparação do envio.
2. Depois do **preparação para envio** foi concluída com êxito em ambos os nós, desligue o dispositivo da IU da web local.

## <a name="ship-to-azure"></a>Enviar para o Azure

Esta operação demora cerca de 15 a 20 minutos a concluir.

1. Remova os cabos e retorná-los para a Bandeja parte traseira desse dispositivo.
2. Agende uma recolha com a sua operadora regional.
3. Entrar em contacto com [operações de caixa de dados](mailto:DataBoxOps@microsoft.com) para informar sobre a recolha e obter a etiqueta de envio de devolução.
4. A etiqueta de envio de devolução deve estar visível no painel frontal claro do dispositivo.

## <a name="verify-data"></a>Verificar os dados

O tempo de conclusão desta operação depende do tamanho dos dados.

1. Quando o dispositivo pesadas de caixa de dados está ligado à rede do datacenter do Azure, os dados carrega automaticamente para o Azure.
2. Serviço do Data Box notifica-o de que a cópia de dados está concluída através do portal do Azure.

    1. Consulte os registos de erros para verificar a existência de quaisquer falhas e executar as ações apropriadas.
    2. Certifique-se de que os dados estão na(s) conta(s) de armazenamento antes de eliminá-los da origem.

## <a name="clean-up-resources"></a>Limpar recursos

Este passo demora entre 2 a 3 minutos.

- Pode cancelar a ordem pesadas de caixa de dados no portal do Azure antes do pedido é processado. Assim que a encomenda estiver processada, não é possível cancelá-la. A encomenda avança até atingir a fase de conclusão. Para cancelar a encomenda, aceda a **Descrição geral** e clique em **Cancelar** na barra de comandos.

- Pode eliminar a encomenda assim que o estado for apresentado como **Concluído** ou **Cancelado** no portal do Azure. Para eliminar a encomenda, aceda a **Descrição geral** e clique em **Eliminar** na barra de comandos.

## <a name="next-steps"></a>Passos Seguintes

Neste início rápido, implementou uma pesada de caixa de dados para o ajudar a importar os dados para o Azure. Para saber mais sobre o gerenciamento pesado de caixa de dados do Azure, avance para o tutorial seguinte:

> [!div class="nextstepaction"]
> [Utilizar o portal do Azure para administrar pesadas de caixa de dados](data-box-portal-admin.md)
